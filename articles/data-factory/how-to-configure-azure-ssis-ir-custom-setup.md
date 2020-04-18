---
title: De installatie aanpassen voor een runtime voor Azure-SSIS-integratie
description: In dit artikel wordt beschreven hoe u de aangepaste installatie-interface voor een runtime voor Azure-SSIS-integratie gebruiken om extra componenten te installeren of instellingen te wijzigen
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: ab2ba31d6b712bd3399bc8bf5b491337d462dac9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606208"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>De installatie aanpassen voor een runtime voor Azure-SSIS-integratie

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De aangepaste installatie voor een Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) biedt een interface voor het toevoegen van uw eigen stappen tijdens het instellen of opnieuw configureren van uw Azure-SSIS IR. 

Met de aangepaste installatie u de standaardconfiguratie of -omgeving wijzigen om bijvoorbeeld aanvullende Windows-services te starten, toegangsreferenties voor bestandsshares aan te houden of een sterk cryptografie/veiliger netwerkprotocol (TLS 1.2) te gebruiken. Of u extra onderdelen, zoals assemblages, stuurprogramma's of extensies, installeren op elk knooppunt van uw Azure-SSIS IR.

U op twee manieren aangepaste instellingen op uw Azure-SSIS IR doen: 
* **Aangepaste installatie uitdrukken zonder script:** Voer enkele algemene systeemconfiguraties en Windows-opdrachten uit of installeer enkele populaire of aanbevolen extra componenten zonder scripts te gebruiken.
* **Standaard aangepaste installatie met een script:** bereid een script en de bijbehorende bestanden voor en upload ze allemaal samen naar een blobcontainer in uw Azure-opslagaccount. U verstrekt vervolgens een SAS-uniform resource-id (Shared Access Signature) (URI) voor uw container wanneer u uw Azure-SSIS IR instelt of opnieuw configureert. Elk knooppunt van uw Azure-SSIS IR downloadt vervolgens het script en de bijbehorende bestanden uit uw container en voert uw aangepaste installatie uit met verhoogde machtigingen. Wanneer uw aangepaste installatie is voltooid, uploadt elk knooppunt de standaarduitvoer van uitvoering en andere logboeken naar uw container.

U zowel gratis, niet-gelicentieerde componenten als betaalde, gelicentieerde componenten installeren met express en standaard aangepaste opstellingen. Zie Betaalde [of gelicentieerde onderdelen ontwikkelen voor een Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)als u een onafhankelijke softwareleverancier (ISV) bent.

> [!IMPORTANT]
> Om te profiteren van toekomstige verbeteringen, raden we u aan v3 of hoger reeks knooppunten te gebruiken voor uw Azure-SSIS IR met aangepaste installatie.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn alleen van toepassing op standaard aangepaste instellingen:

- Als u *gacutil.exe* in uw script wilt gebruiken om assemblages te installeren in de wereldwijde assemblagecache (GAC), moet u *gacutil.exe* leveren als onderdeel van uw aangepaste installatie. U de kopie gebruiken die is opgenomen in onze *openbare preview-container,* die later in de sectie Instructies wordt besproken.

- Als u wilt verwijzen naar een submap in uw script, `.\` ondersteunt *msiexec.exe* de notatie niet om naar de hoofdmap te verwijzen. Gebruik een opdracht `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` zoals `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`in plaats van .

- Administratieve shares of verborgen netwerkshares die automatisch door Windows worden gemaakt, worden momenteel niet ondersteund op azure-SSIS IR.

- Het IBM iSeries Access ODBC-stuurprogramma wordt niet ondersteund op azure-SSIS IR. Mogelijk ziet u installatiefouten tijdens uw aangepaste installatie. Neem als u dit doet contact op met IBM-ondersteuning voor hulp.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u uw Azure-SSIS IR wilt aanpassen, hebt u de volgende items nodig:

- [Een Azure-abonnement](https://azure.microsoft.com/)

- [Uw Azure-SSIS IR inrichten](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Een Azure-opslagaccount](https://azure.microsoft.com/services/storage/). Niet vereist voor express aangepaste opstellingen. Voor standaard aangepaste instellingen uploadt en bewaart u uw aangepaste installatiescript en de bijbehorende bestanden in een blobcontainer. Het aangepaste installatieproces uploadt ook de uitvoeringslogboeken naar dezelfde blobcontainer.

## <a name="instructions"></a>Instructies

1. Als u uw Azure-SSIS IR wilt instellen of opnieuw configureren met PowerShell, downloadt en installeert u [Azure PowerShell.](/powershell/azure/install-az-ps) Ga naar stap 4 voor aangepaste aangepaste opstellingen.

1. Bereid uw aangepaste installatiescript en de bijbehorende bestanden voor (bijvoorbeeld .bat,.cmd, .exe, .dll,msi of .ps1-bestanden).

   * U moet een scriptbestand met de naam *main.cmd*hebben, dat is het ingangspunt van uw aangepaste setup.  
   * Om ervoor te zorgen dat het script in stilte kan worden uitgevoerd, raden we u aan het eerst op uw lokale machine te testen.  
   * Als u wilt dat extra logboeken die worden gegenereerd door andere hulpprogramma's (bijvoorbeeld *msiexec.exe)* naar uw container worden geüpload, geeft u de vooraf gedefinieerde omgevingsvariabele op, `CUSTOM_SETUP_SCRIPT_LOG_DIR`omdat de logboekmap in uw scripts (bijvoorbeeld *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log).*

1. Azure Storage Explorer downloaden, installeren en [openen.](https://storageexplorer.com/) Dit doet u als volgt:

   a. Klik **onder (Lokaal en bijgevoegd)** met de rechtermuisknop op **Opslagaccounts**en selecteer **Verbinding maken met Azure-opslag**.

      ![Verbinding maken met Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecteer **De naam en sleutel van een opslagaccount**gebruiken en selecteer **Volgende**.

      ![De naam en sleutel van een opslagaccount gebruiken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Voer de naam en sleutel van uw Azure-opslagaccount in, selecteer **Volgende**en selecteer **Verbinding maken**.

      ![Naam en sleutel van het opslagaccount opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Klik onder uw verbonden Azure-opslagaccount met de rechtermuisknop op **Blob-containers**, selecteer **Blobcontainer maken**en geef de nieuwe container een naam.

      ![Een blob-container maken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecteer de nieuwe container en upload uw aangepaste installatiescript en de bijbehorende bestanden. Zorg ervoor dat u *main.cmd uploadt* op het hoogste niveau van uw container, niet in een map. Zorg er ook voor dat uw container alleen de benodigde aangepaste installatiebestanden bevat, zodat het downloaden naar uw Azure-SSIS IR later niet lang zal duren. De maximale duur van een aangepaste installatie is momenteel ingesteld op 45 minuten voordat deze een keer wordt uitgeschakeld. Dit omvat de tijd om alle bestanden uit uw container te downloaden en te installeren op de Azure-SSIS IR. Als de installatie meer tijd vereist, verhoogt u een ondersteuningsticket.

      ![Bestanden uploaden naar de blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klik met de rechtermuisknop op de container en selecteer **Gedeelde toegangshandtekening opbrengen**.

      ![De handtekening voor gedeelde toegang voor de container opvragen](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Maak de SAS URI voor uw container met een voldoende lange vervaldatum en met lees/schrijf/lijst toestemming. U hebt de SAS URI nodig om uw aangepaste installatiescript en de bijbehorende bestanden te downloaden en uit te voeren wanneer een knooppunt van uw Azure-SSIS IR opnieuw wordt weergegeven of opnieuw wordt gestart. U hebt schrijftoestemming nodig om uitvoeringslogboeken voor instellingen te uploaden.

      > [!IMPORTANT]
      > Zorg ervoor dat de SAS URI niet verloopt en dat de aangepaste installatiebronnen altijd beschikbaar zijn gedurende de gehele levenscyclus van uw Azure-SSIS IR, van creatie tot verwijdering, vooral als u uw Azure-SSIS IR regelmatig stopt en start tijdens deze periode.

      ![De gedeelde toegangshandtekening voor de container genereren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopieer en sla de SAS URI van uw container.

      ![De handtekening voor gedeelde toegang kopiëren en opslaan](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Wanneer u uw Azure-SSIS IR instelt of opnieuw configureert met een gebruikersinterface van de gegevensfabriek, u aangepaste instellingen toevoegen of verwijderen door de runtime van **Azure-SSIS-integratie aanpassen met extra systeemconfiguraties/componentinstallaties** in het gedeelte **Geavanceerde instellingen** van het **installatievenster voor runtime van Integratie.** 

   Als u standaard aangepaste instellingen wilt toevoegen, voert u de SAS URI van uw container in het vak SAS URI voor aangepaste installatie.If you want to add standard custom setups, enter the SAS URI of your container in the **Custom setup container SAS URI** box. 
   
   Als u aangepaste expresinstellingen wilt toevoegen, selecteert u **Nieuw** om het **aangepaste instellingsvenster Express toevoegen** te openen en selecteert u een type in de vervolgkeuzelijst Express aangepast **instellingstype:**

   * Als u het **opdrachttype Cmdkey** uitvoeren selecteert, u toegangsreferenties voor uw bestandsshares of Azure Files-shares op Azure-SSIS IR behouden door uw beoogde computernaam of domeinnaam, accountnaam of gebruikersnaam en accountsleutel of wachtwoord in de vakken **/Toevoegen,** **/Gebruiker**en **/Pass** in te voeren. Dit is vergelijkbaar met het uitvoeren van de [Windows cmdkey-opdracht](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) op uw lokale machine.
   
   * Als u het variabele type **Omgeving toevoegen** selecteert, u Windows-omgevingsvariabelen toevoegen die u gebruiken in uw pakketten die worden uitgevoerd op de Azure-SSIS IR door de naam en de waarde van uw omgevingvariabele waarde in te voeren in de vakken **Variabele naam** en **Variabele waarde.** Dit is vergelijkbaar met het uitvoeren van de [windows-setopdracht](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) op uw lokale machine.

   * Als u het type **component met licentie installeren** selecteert, u vervolgens een geïntegreerd onderdeel selecteren van onze ISV-partners in de vervolgkeuzelijst **Componentnaam:**

     * Als u de component Taakfabriek van **De SentryOne** selecteert, u de [suite Van Taakfabriek](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) van onderdelen van SentryOne op uw Azure-SSIS IR installeren door de productlicentiesleutel in te voeren die u bij deze onderdelen hebt gekocht, in het vak **Licentiesleutel.** De huidige geïntegreerde versie is **2019.4.3**.

     * Als u de **oh22's HEDDA selecteert. IO** component, u de [HEDDA installeren. ](https://hedda.io/ssis-component/)IO-gegevenskwaliteit/reinigingscomponent van oh22 op uw Azure-SSIS IR na aankoop van hun service. De huidige geïntegreerde versie is **1.0.13**.

     * Als u de SQLPhonetics.NET component van de **oh22** selecteert, u de [SQLPhonetics.NET-component](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) voor gegevenskwaliteit/matching van oh22 op uw Azure-SSIS IR installeren door de productlicentiesleutel in te voeren die u bij deze component hebt gekocht in het vak **Licentiesleutel.** De huidige geïntegreerde versie is **1.0.43**.

     * Als u de **SSIS Integration Toolkit-component van KingswaySoft** selecteert, u de [SSIS Integration Toolkit-suite](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) met connectors voor CRM/ERP/marketing/samenwerkingsapps installeren, zoals Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud, **License key** enz. De huidige geïntegreerde versie is **2019.2**.

     * Als u de **component SSIS Productivity Pack van KingswaySoft** selecteert, u de [SSIS Productivity Pack-suite](https://www.kingswaysoft.com/products/ssis-productivity-pack) met onderdelen van KingswaySoft op uw Azure-SSIS IR installeren door de productlicentiesleutel in te voeren die u bij hen hebt gekocht in het vak **Licentiesleutel.** De huidige geïntegreerde versie is **10.0**.

     * Als u het **Xtract IS-onderdeel** van de Theobald Software selecteert, u de [Xtract IS-suite](https://theobald-software.com/en/xtract-is/) met connectoren voor SAP-systeem (ERP, S/4HANA, BW) van Theobald Software op uw Azure-SSIS IR installeren door & te slepen die het productlicentiebestand dat u van hen hebt gekocht, in het vak **Licentiebestand** te laten vallen/uploaden. De huidige geïntegreerde versie is **6.1.1.3**.

   Uw aangepaste express-instellingen worden weergegeven in de sectie **Geavanceerde instellingen.** Als u ze wilt verwijderen, schakelt u de selectievakjes in en selecteert u **Verwijderen**.

   ![Geavanceerde instellingen met aangepaste instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Wanneer u uw Azure-SSIS IR instelt of opnieuw configureert met PowerShell, `Set-AzDataFactoryV2IntegrationRuntime` u de aangepaste instellingen toevoegen of verwijderen door de cmdlet uit te voeren voordat u uw Azure-SSIS IR start.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   Nadat uw standaard aangepaste installatie is voltooid en uw Azure-SSIS IR is gestart, u de standaarduitvoer van *main.cmd-* en andere uitvoeringslogboeken vinden in de map *main.cmd.log* van uw opslagcontainer.

1. Als u enkele voorbeelden van standaard aangepaste instellingen wilt bekijken, maakt u verbinding met onze openbare voorvertoningscontainer met Azure Storage Explorer.

   a. Klik **onder (Lokaal en bijgevoegd)** met de rechtermuisknop op **Opslagaccounts,** selecteer **Verbinding maken met Azure-opslag,** selecteer **Een verbindingstekenreeks of een uri met gedeelde toegangshandtekening**gebruiken en selecteer **Volgende**.

      ![Verbinding maken met Azure-opslag met de gedeelde toegangshandtekening](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecteer **Een SAS URI gebruiken** en voer in het vak **URI** de volgende SAS URI in:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![De handtekening voor gedeelde toegang voor de container opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecteer **Volgende**en selecteer **Verbinding maken**.

   d. Selecteer in het linkerdeelvenster de verbonden **publicpreview-container** en dubbelklik op de *map CustomSetupScript.* In deze map zijn de volgende items:

      * Een *voorbeeldmap,* die een aangepaste installatie bevat om een basistaak te installeren op elk knooppunt van uw Azure-SSIS IR. De taak doet niets anders dan slapen voor een paar seconden. De map bevat ook een *gacutil* map, waarvan de volledige inhoud *(gacutil.exe*, *gacutil.exe.config*, en *1033\gacutlrc.dll*) kan worden gekopieerd als is naar uw container.

      * Een map *UserScenarios,* die verschillende aangepaste installatievoorbeelden van echte gebruikersscenario's bevat.

        ![Inhoud van de openbare voorbeeldcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbelklik op de map *Gebruikersscenario's* om de volgende items te zoeken:

      * Een *.NET FRAMEWORK 3.5-map,* die een aangepast setupscript *(main.cmd)* bevat om een eerdere versie van het .NET Framework te installeren die mogelijk nodig is voor aangepaste componenten op elk knooppunt van uw Azure-SSIS IR.

      * Een *BCP-map,* die een aangepast setupscript *(main.cmd)* bevat om SQL Server-commandline-hulpprogramma's *(MsSqlCmdLnUtils.msi)* te installeren, inclusief het bulkcopy-programma *(bcp),* op elk knooppunt van uw Azure-SSIS IR.

      * Een *EXCEL-map,* die een aangepast setupscript *(main.cmd)* bevat om C#-verzamelingen en -bibliotheken te installeren die u in scripttaken gebruiken om Excel-bestanden dynamisch te lezen en te schrijven op elk knooppunt van uw Azure-SSIS IR. 
      
        Download eerst [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) en [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)en upload ze vervolgens allemaal samen met *main.cmd* naar uw container. Als u ook alleen de standaard Excel Connection Manager-, Excel-bron- en Excel-bestemming wilt gebruiken, is de vereiste toegangs-versie al vooraf geïnstalleerd op uw Azure-SSIS IR, zodat u geen aangepaste installatie nodig hebt.
      
      * Een *MYSQL ODBC-map,* die een aangepast setupscript *(main.cmd)* bevat om de MySQL ODBC-stuurprogramma's op elk knooppunt van uw Azure-SSIS IR te installeren. Met deze instelling u odbc-verbindingsbeheer, bron en bestemming gebruiken om verbinding te maken met de MySQL-server. 
     
        Download eerst [de nieuwste 64-bits en 32-bits versies van de MySQL ODBC-driver installers](https://dev.mysql.com/downloads/connector/odbc/) (bijvoorbeeld *mysql-connector-odbc-8.0.13-winx64.msi* en *mysql-connector-odbc-8.0.13-win32.msi)* en upload ze vervolgens allemaal samen met *main.cmd* naar uw container.

      * Een *ORACLE ENTERPRISE-map,* die een aangepast setupscript *(main.cmd)* en een stil installatieconfig-bestand *(client.rsp)* bevat om de Oracle-connectors en oci-stuurprogramma op elk knooppunt van uw Azure-SSIS IR Enterprise Edition te installeren. Met deze instelling u Oracle Connection Manager, bron en bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        Download eerst Microsoft Connectors v5.0 voor Oracle *(AttunitySSISOraAdaptersSetup.msi* en *AttunitySSISOraAdaptersSetup64.msi)* van [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) en de nieuwste Oracle-client (bijvoorbeeld *winx64_12102_client.zip)* van [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)en upload ze vervolgens allemaal samen met *main.cmd* en *client.rsp* naar uw container. Als u TNS gebruikt om verbinding te maken met Oracle, moet u ook *tnsnames.ora*downloaden, bewerken en uploaden naar uw container, zodat deze tijdens de installatie naar de installatiemap van Oracle kan worden gekopieerd.

      * Een *ORACLE STANDARD-ADO.NET* map, die een aangepast setupscript *(main.cmd)* bevat om het Oracle-ODP.NET-stuurprogramma op elk knooppunt van uw Azure-SSIS IR te installeren. Met deze instelling u de ADO.NET Connection Manager, bron en bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        Download eerst [de nieuwste Oracle ODP.NET driver](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (bijvoorbeeld *ODP.NET_Managed_ODAC122cR1.zip)* en upload deze vervolgens samen met *main.cmd* naar uw container.
       
      * Een *ORACLE STANDARD ODBC-map,* die een aangepast setupscript *(main.cmd)* bevat om het Oracle ODBC-stuurprogramma te installeren en de gegevensbronnaam (DSN) op elk knooppunt van uw Azure-SSIS IR te configureren. Met deze instelling u ODBC Connection Manager, bron en bestemming of Power Query Connection Manager en bron gebruiken met het ODBC-gegevensbrontype om verbinding te maken met de Oracle-server. 
      
        Download eerst de nieuwste Oracle Instant Client (Basic Package of Basic Lite Package) en ODBC Package en upload ze vervolgens allemaal samen met *main.cmd* naar uw container:
        * [Download 64-bits pakketten](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basispakket: *instantclient-basic-windows.x64-18.3.0.0.0.0dbru.zip*; Basic Lite-pakket: *instantclient-basiclite-windows.x64-18.3.0.0.0.0dbru.zip*; *ODBC-pakket: instantclient-odbc-windows.x64-18.3.0.0.0.0dbru.zip*) 
        * [Download 32-bits pakketten](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basispakket: *instantclient-basic-nt-18.3.0.0.0.0dbru.zip*; Basic Lite Pakket: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; *ODBC-pakket: instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Een *ORACLE STANDARD OLEDB-map,* die een aangepast setupscript *(main.cmd)* bevat om het Oracle OLEDB-stuurprogramma op elk knooppunt van uw Azure-SSIS IR te installeren. Met deze instelling u de OLEDB Connection Manager, de bron en de bestemming gebruiken om verbinding te maken met de Oracle-server. 
     
        Download eerst [de nieuwste Oracle OLEDB-driver](https://www.oracle.com/partners/campaign/index-090165.html) (bijvoorbeeld *ODAC122010Xcopy_x64.zip)* en upload deze vervolgens samen met *main.cmd* naar uw container.

      * Een *POSTGRESQL ODBC-map,* die een aangepast setupscript *(main.cmd)* bevat om de PostgreSQL ODBC-stuurprogramma's op elk knooppunt van uw Azure-SSIS IR te installeren. Met deze instelling u odbc-verbindingsbeheer, bron en bestemming gebruiken om verbinding te maken met de PostgreSQL-server. 
     
        Download eerst [de nieuwste 64-bits en 32-bits versies van PostgreSQL ODBC-stuurprogrammainstallateurs](https://www.postgresql.org/ftp/odbc/versions/msi/) (bijvoorbeeld *psqlodbc_x64.msi* en *psqlodbc_x86.msi)* en upload ze vervolgens allemaal samen met *main.cmd* naar uw container.

      * Een *SAP BW-map,* die een aangepast setupscript *(main.cmd)* bevat om de SAP .NET-connectorassemblage *(librfc32.dll)* te installeren op elk knooppunt van uw Azure-SSIS IR Enterprise Edition. Met deze instelling u de SAP Business Warehouse (BW) Connection Manager, bron en bestemming gebruiken om verbinding te maken met de SAP BW-server. 
      
        Upload eerst de 64-bits of de 32-bits versie van *librfc32.dll* uit de SAP-installatiemap samen met *main.cmd* naar uw container. Het script kopieert de SAP-assemblage vervolgens naar de map *%windir%\SysWow64* of *%windir%\System32* tijdens de installatie.

      * Een *opslagmap,* die een aangepast setupscript *(main.cmd)* bevat om Azure PowerShell te installeren op elk knooppunt van uw Azure-SSIS IR. Met deze instelling u SSIS-pakketten implementeren en uitvoeren waarmee [PowerShell-scripts worden uitgevoerd om uw Azure-opslagaccount te manipuleren.](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell) 
      
        Kopieer *main.cmd*, een voorbeeld *van AzurePowerShell.msi* (of gebruik de nieuwste versie) en *storage.ps1* naar uw container. Gebruik *PowerShell.dtsx* als sjabloon voor uw pakketten. De pakketsjabloon combineert een [Azure Blob-downloadtaak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), die *storage.ps1* downloadt als een aanpasbaar PowerShell-script en een [procestaak uitvoeren,](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)waarmee het script op elk knooppunt wordt uitgevoerd.

      * Een *TERADATA-map,* die een aangepast setupscript *(main.cmd),* het bijbehorende bestand *(install.cmd)* en installerpackages (*.msi )* bevat. Deze bestanden installeren de Teradata-connectors, de Teradata Parallel Transporter (TPT) API en het ODBC-stuurprogramma op elk knooppunt van uw Azure-SSIS IR Enterprise Edition. Met deze instelling u de Teradata Connection Manager, de bron en de bestemming gebruiken om verbinding te maken met de Teradata-server. 
      
        Download eerst [het Teradata Tools and Utilities 15.x zip-bestand](http://partnerintelligence.teradata.com) (bijvoorbeeld *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip)* en upload het vervolgens samen met de eerder genoemde *.cmd-* en *.msi-bestanden* naar uw container.

      * Een *TLS 1.2-map,* die een aangepast setupscript *(main.cmd)* bevat om een sterk cryptografie/veiliger netwerkprotocol (TLS 1.2) te gebruiken en oudere SSL/TLS-versies uit te schakelen op elk knooppunt van uw Azure-SSIS IR.

      * Een *ZULU OPENJDK-map,* die een aangepast setupscript *(main.cmd)* en PowerShell-bestand *(install_openjdk.ps1*) bevat om de Zulu OpenJDK op elk knooppunt van uw Azure-SSIS IR te installeren. Met deze instelling u Azure Data Lake Store- en Flexible File-connectors gebruiken om ORC- en Parketbestanden te verwerken. Zie [Azure Feature Pack for Integration Services voor](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)meer informatie. 
      
        Download eerst [de nieuwste Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (bijvoorbeeld *zulu8.33.0.1-jdk8.0.192-win_x64.zip)* en upload deze vervolgens samen met *main.cmd* en *install_openjdk.ps1* naar uw container.

        ![Mappen in de map gebruikersscenario's](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Als u deze aangepaste installatievoorbeelden wilt proberen, kopieert u de inhoud uit de geselecteerde map naar uw container.
   
      Wanneer u uw Azure-SSIS IR instelt of opnieuw configureert met de gebruikersinterface van de gegevensfabriek, schakelt u **het selectievakje Runtime voor Azure-SSIS-integratie aanpassen met extra systeemconfiguraties/componentinstallaties** in de sectie **Geavanceerde instellingen** in en voert u de SAS URI van uw container in het selectievakje Aangepaste **installatiecontainer SAS URI** in.
   
      Wanneer u uw Azure-SSIS IR instelt of opnieuw `Set-AzDataFactoryV2IntegrationRuntime` configureert met PowerShell, voert u `SetupScriptContainerSasUri` de cmdlet uit met de SAS URI van uw container als waarde voor parameter.

## <a name="next-steps"></a>Volgende stappen

- [De Enterprise Edition van de runtime Azure-SSIS-integratie instellen](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Betaalde of gelicentieerde aangepaste componenten ontwikkelen voor de runtime azure-SSIS-integratie](how-to-develop-azure-ssis-ir-licensed-components.md)
