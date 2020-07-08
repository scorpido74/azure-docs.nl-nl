---
title: De instellingen voor een Azure-SSIS Integration Runtime aanpassen
description: In dit artikel wordt beschreven hoe u de aangepaste configuratie-interface gebruikt voor een Azure-SSIS Integration Runtime om extra onderdelen te installeren of instellingen te wijzigen
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
ms.date: 06/03/2020
ms.openlocfilehash: 576861265771977f7e13140dd595f47bf556e585
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331896"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>De instellingen voor een Azure-SSIS Integration Runtime aanpassen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De aangepaste installatie voor een Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF) biedt een interface voor het toevoegen van uw eigen stappen tijdens het inrichten of opnieuw configureren van uw Azure-SSIS IR. 

Door de aangepaste installatie te gebruiken, kunt u de standaard configuratie van het besturings systeem of de omgeving wijzigen in, bijvoorbeeld door extra Windows-Services te starten, toegangs referenties voor bestands shares op te slaan of gebruik te maken van sterke crypto grafie/veiliger netwerk protocol (TLS 1,2). U kunt ook aanvullende aangepaste/derde onderdelen, zoals assembly's, stuur Programma's of uitbrei dingen, op elk knoop punt van uw Azure-SSIS IR installeren. Zie [Ingebouwde/vooraf geïnstalleerde onderdelen op Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime) voor meer informatie over ingebouwde en vooraf geïnstalleerde onderdelen.

U kunt op twee manieren aangepaste installatie op uw Azure-SSIS IR uitvoeren: 
* **Aangepaste standaard installatie met een script**: bereid een script en de bijbehorende bestanden voor en upload deze allemaal samen naar een BLOB-container in uw Azure Storage-account. Vervolgens geeft u een Shared Access Signature (SAS) Uniform Resource Identifier (URI) voor uw container op wanneer u uw Azure-SSIS IR instelt of opnieuw configureert. Elk knoop punt van uw Azure-SSIS IR downloadt vervolgens het script en de bijbehorende bestanden uit uw container en voert uw aangepaste installatie uit met verhoogde machtigingen. Wanneer uw aangepaste installatie is voltooid, uploadt elk knoop punt de standaard uitvoer van de uitvoering en andere logboeken naar uw container.
* **Snelle aangepaste installatie zonder script**: Voer enkele algemene systeem configuraties en Windows-opdrachten uit of installeer populaire of aanbevolen extra onderdelen zonder scripts te gebruiken.

U kunt zowel gratis, niet-gelicentieerde onderdelen en betaalde, gelicentieerde onderdelen installeren met standaard en snelle aangepaste installatie. Als u een onafhankelijke software leverancier (ISV) bent, raadpleegt u [betaalde of gelicentieerde onderdelen voor Azure-SSIS IR ontwikkelen](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Om te profiteren van toekomstige verbeteringen raden we u aan om een reeks van v3 of later te gebruiken voor uw Azure-SSIS IR met aangepaste installatie.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen gelden alleen voor standaard aangepaste Setup:

- Als u *gacutil.exe* in uw script wilt gebruiken voor het installeren van assembly's in de Global assembly cache (GAC), moet u *gacutil.exe* opgeven als onderdeel van de aangepaste installatie. U kunt ook gebruikmaken van de kopie die is opgenomen in de *open bare preview* -container, die later wordt beschreven in de sectie ' instructies '.

- Als u wilt verwijzen naar een submap in uw script, ondersteunt *msiexec.exe* de notatie niet `.\` voor verwijzing naar de hoofdmap. Gebruik een opdracht, zoals `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` in plaats van `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Beheerders shares of verborgen netwerk shares die automatisch door Windows worden gemaakt, worden momenteel niet ondersteund op de Azure-SSIS IR.

- Het ODBC-stuur programma voor de IBM iSeries-toegang wordt niet ondersteund op de Azure-SSIS IR. Tijdens uw aangepaste installatie worden mogelijk installatie fouten weer geven. Neem contact op met IBM Support voor hulp.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u uw Azure-SSIS IR wilt aanpassen, hebt u de volgende items nodig:

- [Een Azure-abonnement](https://azure.microsoft.com/)

- [Uw Azure-SSIS IR inrichten](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Een Azure-opslag account](https://azure.microsoft.com/services/storage/). Niet vereist voor snelle aangepaste installatie. Voor aangepaste standaard instellingen uploadt en slaat u uw aangepaste installatie script en de bijbehorende bestanden op in een BLOB-container. Het aangepaste installatie proces uploadt ook de uitvoerings logboeken naar dezelfde BLOB-container.

## <a name="instructions"></a>Instructies

U kunt uw Azure-SSIS IR inrichten of opnieuw configureren met aangepaste Setup op de ADF-gebruikers interface. Als u hetzelfde wilt doen met behulp van Power shell, downloadt en installeert u [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Aangepaste standaard installatie

Voer de volgende stappen uit om uw Azure-SSIS IR in te richten of opnieuw te configureren met standaard aangepaste Setup.

1. Bereid uw aangepaste installatie script en de bijbehorende bestanden voor (bijvoorbeeld. bat,. cmd,. exe,. dll,. msi of. ps1-bestanden).

   * U moet een script bestand met de naam *Main. cmd*hebben. Dit is het toegangs punt van uw aangepaste installatie.  
   * Om ervoor te zorgen dat het script op de achtergrond kan worden uitgevoerd, raden we u aan het eerst op uw lokale machine te testen.  
   * Als u wilt dat extra logboeken die worden gegenereerd door andere hulpprogram ma's (bijvoorbeeld *msiexec.exe*) worden geüpload naar uw container, geeft u de vooraf gedefinieerde omgevings variabele op, `CUSTOM_SETUP_SCRIPT_LOG_DIR` als de logboekmap in uw scripts (bijvoorbeeld *msiexec/i xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Down load, installeer en open [Azure Storage Explorer](https://storageexplorer.com/).

   a. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts**en selecteert **u verbinding maken met Azure Storage**.

      ![Verbinding maken met Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecteer **een opslag accountnaam en-sleutel gebruiken**en selecteer **volgende**.

      ![De naam en sleutel van een opslagaccount gebruiken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Voer de naam en sleutel van uw Azure Storage-account in, selecteer **volgende**en selecteer vervolgens **verbinding maken**.

      ![Naam en sleutel van opslag account opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Klik onder uw verbonden Azure Storage-account met de rechter muisknop op **BLOB-containers**, selecteer **BLOB-container maken**en geef de nieuwe container een naam.

      ![Een blob-container maken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecteer de nieuwe container en upload uw aangepaste installatie script en de bijbehorende bestanden. Zorg ervoor dat u *Main. cmd* op het hoogste niveau van uw container uploadt, niet in een map. Zorg er ook voor dat de container alleen de benodigde aangepaste installatie bestanden bevat, zodat deze niet langer lang worden gedownload naar uw Azure-SSIS IR. De maximale duur van een aangepaste installatie is momenteel ingesteld op 45 minuten voordat er een time-out optreedt. Dit omvat de tijd voor het downloaden van alle bestanden uit uw container en het installeren ervan op het Azure-SSIS IR. Als er meer tijd nodig is, kunt u een ondersteunings ticket genereren.

      ![Bestanden uploaden naar de BLOB-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klik met de rechter muisknop op de container en selecteer vervolgens **Shared Access Signature ophalen**.

      ![De Shared Access Signature voor de container ophalen](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   bijvoorbeeld Maak de SAS-URI voor uw container met een voldoende lange verloop tijd en met de machtiging lezen/schrijven/lijst. U hebt de SAS-URI nodig om uw aangepaste installatie script en de bijbehorende bestanden te downloaden en uit te voeren wanneer een wille keurig knoop punt van uw Azure-SSIS IR wordt geimageeerd of opnieuw wordt gestart. U hebt schrijf machtigingen nodig voor het uploaden van de installatie Logboeken.

      > [!IMPORTANT]
      > Zorg ervoor dat de SAS-URI niet verlopen en dat de aangepaste installatie resources altijd beschikbaar zijn tijdens de hele levens cyclus van uw Azure-SSIS IR, van het maken tot verwijderen, met name als u uw Azure-SSIS IR tijdens deze periode regel matig stopt en start.

      ![De Shared Access Signature voor de container genereren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopieer de SAS-URI van uw container en sla deze op.

      ![De Shared Access Signature kopiëren en opslaan](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert op de ADF-gebruikers interface, schakelt u het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in op de pagina **Geavanceerde instellingen** van het deel venster instellingen voor **Integration runtime** en voert u de SAS-URI van uw container in het vakje **aangepaste installatie container SAS URI** in.

   ![Geavanceerde instellingen met aangepaste installatie](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

### <a name="express-custom-setup"></a>Snelle aangepaste installatie

Voer de volgende stappen uit om uw Azure-SSIS IR in te richten of opnieuw te configureren met snelle aangepaste installatie.

1. Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert op de ADF-gebruikers interface, schakelt u het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in op de pagina **Geavanceerde instellingen** van het deel venster **Setup van Integration runtime** . 

1. Selecteer **Nieuw** om het deel venster **snelle aangepaste installatie toevoegen** te openen en selecteer vervolgens een type in de vervolg keuzelijst **expliciet aangepaste installatie type** :

   * Als u het **opdracht cmdkey uitvoeren** selecteert, kunt u toegangs referenties voor uw bestands shares of Azure files shares op Azure-SSIS IR bewaren door de naam van uw doel computer of de domein naam, de account naam of de gebruikers naam en de account sleutel of het wacht woord in de vakken **/add**, **/User**en **/Pass** op te geven. Dit is vergelijkbaar met het uitvoeren van de Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) -opdracht op uw lokale computer.
   
   * Als u het type **omgevings variabele toevoegen** selecteert, kunt u Windows-omgevings variabelen toevoegen voor gebruik in uw pakketten die worden uitgevoerd op de Azure-SSIS IR door de naam en waarde van de omgevings variabele in te voeren in de vakken **naam** en **variabele waarde** . Dit is vergelijkbaar met het uitvoeren van de Windows-opdracht [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) op uw lokale computer.

   * Als u het **onderdeel type licentie installeren** selecteert, kunt u een geïntegreerd onderdeel selecteren in onze ISV-partners in de vervolg keuzelijst **onderdeel naam** :

     * Als u het onderdeel **taak-Factory van SentryOne** selecteert, kunt u de [taken fabrieks](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) suite van onderdelen van SentryOne op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **2019.4.3**.

     * Als u de **oh22's-HEDDA selecteert. IO** -onderdeel kunt u de [HEDDA installeren. ](https://hedda.io/ssis-component/)Het onderdeel io-gegevens kwaliteit/opschonen van oh22 op uw Azure-SSIS IR na het aanschaffen van de service. De huidige geïntegreerde versie is **1.0.13**.

     * Als u het **oh22's SQLPhonetics.net** -onderdeel selecteert, kunt u de [SQLPhonetics.net](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) -gegevens kwaliteit/het overeenkomende onderdeel van oh22 op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **1.0.43**.

     * Als u het **SSIS Integration Toolkit** -onderdeel van de KingswaySoft selecteert, kunt u het pakket met de [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) van connectors voor CRM/ERP/marketing/samenwerkings-apps installeren, zoals micro soft Dynamics/share point/Project Server, Oracle/sales force marketing Cloud, Azure-SSIS IR enzovoort door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **2019,2**.

     * Als u het **SSIS Productivity Pack** -onderdeel van de KingswaySoft selecteert, kunt u de [SSIS-productiviteits pakket](https://www.kingswaysoft.com/products/ssis-productivity-pack) onderdelen van KingswaySoft op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het vak **licentie code** . De huidige geïntegreerde versie is **10,0**.

     * Als u het onderdeel **Xtract van de Theobald-software** selecteert, kunt u de [Xtract is](https://theobald-software.com/en/xtract-is/) een suite van connectors voor SAP-systeem (ERP, s/4HANA, BW) installeren van de Theobald-software op uw Azure-SSIS IR door te slepen & het product licentie bestand dat u hebt aangeschaft, te verwijderen en te uploaden naar het vak **licentie bestand** . De huidige geïntegreerde versie is **6.1.1.3**.

Uw toegevoegde snelle aangepaste Setup wordt weer gegeven op de pagina **Geavanceerde instellingen** . Als u deze wilt verwijderen, schakelt u de selectie vakjes in en selecteert u **Verwijderen**.

### <a name="azure-powershell"></a>Azure PowerShell

Voer de volgende stappen uit om uw Azure-SSIS IR in te richten of opnieuw te configureren met aangepaste setups met behulp van Azure PowerShell.

1. Als uw Azure-SSIS IR al is gestart of wordt uitgevoerd, stopt u het eerst.

1. U kunt vervolgens aangepaste Setup toevoegen of verwijderen door de cmdlet uit te voeren `Set-AzDataFactoryV2IntegrationRuntime` voordat u begint met het Azure-SSIS IR.

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

1. Nadat de aangepaste standaard installatie is voltooid en uw Azure-SSIS IR wordt gestart, kunt u de standaard uitvoer van *Main. cmd* en andere uitvoerings logboeken vinden in de map *Main. cmd. log* van uw container.

### <a name="standard-custom-setup-samples"></a>Standaard aangepaste installatie voorbeelden

Voer de volgende stappen uit om enkele voor beelden van aangepaste standaard instellingen weer te geven en opnieuw te gebruiken.

1. Maak verbinding met onze open bare preview-container met behulp van Azure Storage Explorer.

   a. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts**, selecteert **u verbinding maken met Azure Storage**, selecteert **u een Connection String of een URI voor de hand tekening voor gedeelde toegang gebruiken**en selecteert u vervolgens **volgende**.

      ![Verbinding maken met Azure Storage met de Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecteer **een SAS-URI gebruiken** en voer vervolgens in het vak **URI** de volgende SAS-URI in:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![De Shared Access Signature voor de container opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecteer **volgende**en selecteer vervolgens **verbinding maken**.

   d. Selecteer in het linkerdeel venster de container Connected **publicpreview** en dubbel klik vervolgens op de map *CustomSetupScript* . In deze map zijn de volgende items:

      * Een voor *beeld* van een map, die een aangepaste installatie bevat voor het installeren van een basis taak op elk knoop punt van uw Azure-SSIS IR. De taak heeft niets maar een paar seconden in de slaap stand. De map bevat ook een map *gacutil* , waarvan de volledige inhoud (*gacutil.exe*, *gacutil.exe.config*en *1033\gacutlrc.dll*) naar uw container kan worden gekopieerd.

      * Een *UserScenarios* -map, die verschillende aangepaste installatie voorbeelden bevat uit echte gebruikers scenario's.

        ![Inhoud van de open bare preview-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbel klik op de map *UserScenarios* om de volgende items te zoeken:

      * Een *.NET FRAMEWORK 3,5* -map die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van een eerdere versie van de .NET Framework die mogelijk vereist is voor aangepaste onderdelen op elk knoop punt van uw Azure-SSIS IR.

      * Een *bcp* -map die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van SQL Server opdracht regel programma's (*MsSqlCmdLnUtils.msi*), met inbegrip van het pakket voor bulksgewijs kopiëren (*bcp*), op elk knoop punt van uw Azure-SSIS IR.

      * Een *Excel* -map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van C#-assembly's en-bibliotheken die u in script taken kunt gebruiken om Excel-bestanden dynamisch te lezen en schrijven op elk knoop punt van uw Azure-SSIS IR. 
      
        Down load eerst [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) en [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)en upload deze allemaal samen met *Main. cmd* naar uw container. Als u alleen de standaard Excel-verbindings beheer, de Excel-bron en de Excel-bestemming wilt gebruiken, is het vereiste toegangs herdistribueersysteem al vooraf geïnstalleerd op uw Azure-SSIS IR, dus hebt u geen aangepaste installatie nodig.
      
      * Een *MySQL ODBC-* map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van de MySQL ODBC-stuur Programma's op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de MySQL-server. 
     
        [Down load eerst de nieuwste 64-bits en 32-bits versies van de MySQL ODBC-stuur Programma's](https://dev.mysql.com/downloads/connector/odbc/) (bijvoorbeeld *mysql-connector-odbc-8.0.13-winx64.msi* en *mysql-connector-odbc-8.0.13-win32.msi*) en upload deze allemaal samen met *Main. cmd* naar uw container.

      * Een *Oracle Enter prise* -map, die een aangepast installatie script (*Main. cmd*) en een installatie configuratie bestand (*client. RSP*) bevat waarmee de Oracle-connectors en het OCI-stuur programma kunnen worden geïnstalleerd op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Oracle-verbindings beheer,-bron en-bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        Down load eerst micro soft-connectors v 5.0 voor Oracle (*AttunitySSISOraAdaptersSetup.msi* en *AttunitySSISOraAdaptersSetup64.msi*) van het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=55179) en de nieuwste Oracle-client (bijvoorbeeld *winx64_12102_client.zip*) van [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)en upload deze allemaal samen met *Main. cmd* en *client. RSP* naar uw container. Als u TNS gebruikt om verbinding te maken met Oracle, moet u ook *bestand Tnsnames. ora*downloaden, bewerken en uploaden naar uw container, zodat deze kan worden gekopieerd naar de map Oracle-installatie tijdens de installatie.

      * Een *Oracle STANDARD ADO.net* -map met een aangepast installatie script (*Main. cmd*) om het Oracle ODP.net-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ADO.NET-verbindings beheer,-bron en-bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        [Down load eerst het meest recente Oracle ODP.net-stuur programma](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (bijvoorbeeld *ODP.NET_Managed_ODAC122cR1.zip*) en upload het vervolgens samen met *Main. cmd* naar uw container.
       
      * Een *ODBC-standaard-* map met een aangepaste installatie script (*Main. cmd*) om het Oracle ODBC-stuur programma te installeren en de naam van de gegevens bron (DSN) op elk knoop punt van uw Azure-SSIS IR te configureren. Met deze installatie kunt u de ODBC-verbindings beheer, de bron, de doel-of Power Query verbindings beheer en de bron met het ODBC-gegevens bron type gebruiken om verbinding te maken met de Oracle-server. 
      
        Down load eerst de nieuwste Oracle Instant-client (Basic package of Basic Lite package) en het ODBC-pakket en upload deze allemaal samen met *Main. cmd* naar uw container:
        * [64-bits pakketten downloaden](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-pakket: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic Lite-pakket: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC-pakket: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32-bits pakketten downloaden](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-pakket: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic Lite-pakket: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC-pakket: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Een *Oracle Standard OLEDB* -map, die een aangepast installatie script (*Main. cmd*) bevat om het Oracle OLEDB-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de OLEDB-verbindings beheer, de bron en de bestemming gebruiken om verbinding te maken met de Oracle-server. 
     
        [Down load eerst het nieuwste Oracle OLEDB-stuur programma](https://www.oracle.com/partners/campaign/index-090165.html) (bijvoorbeeld *ODAC122010Xcopy_x64.zip*) en upload het vervolgens samen met *Main. cmd* naar uw container.

      * Een *POSTGRESQL ODBC* -map, die een aangepast installatie script (*Main. cmd*) bevat om de POSTGRESQL ODBC-stuur Programma's te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de PostgreSQL-server. 
     
        [Down load eerst de nieuwste 64-bits en 32-bits versies van postgresql ODBC-stuur programma-installatie Programma's](https://www.postgresql.org/ftp/odbc/versions/msi/) (bijvoorbeeld *psqlodbc_x64.msi* en *psqlodbc_x86.msi*) en upload deze allemaal samen met *Main. cmd* naar uw container.

      * Een *SAP BW* map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van de SAP .net-connector-Assembly (*librfc32.dll*) op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de SAP Business Warehouse (BW)-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de SAP BW-server. 
      
        Upload eerst de 64-bits of de 32-bits versie van *librfc32.dll* vanuit de SAP-installatiemap samen met *Main. cmd* naar uw container. Het script kopieert vervolgens de SAP-assembly naar de map *%windir%\syswow64* of *%windir%\System32* tijdens de installatie.

      * Een *opslagmap* , die een aangepast installatie script (*Main. cmd*) bevat om Azure PowerShell te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u SSIS-pakketten implementeren en uitvoeren die [Power shell-scripts uitvoeren voor het bewerken van uw Azure Storage-account](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Kopieer *Main. cmd*, een voor beeld *AzurePowerShell.msi* (of gebruik de meest recente versie) en *storage.ps1* naar uw container. Gebruik *Power shell. dtsx* als een sjabloon voor uw pakketten. De pakket sjabloon bevat een combi natie van een [Azure Blob-Download taak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), die *storage.ps1* downloadt als een gewijzigd Power shell-script, en een taak voor het uitvoeren van een [proces](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), waarmee het script wordt uitgevoerd op elk knoop punt.

      * Een *TERADATA* -map die een aangepast installatie script (*Main. cmd*) bevat, het bijbehorende bestand (*install. cmd*) en de installatie pakketten (*. msi*). Met deze bestanden worden de Teradata-connectors, de TPT-API (parallel trans Porter) en het ODBC-stuur programma geïnstalleerd op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Teradata-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de Teradata-server. 
      
        [Down load eerst de Teradata-Hulpprogram ma's en Hulpprogram ma's 15. x zip-bestand](http://partnerintelligence.teradata.com) (bijvoorbeeld *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) en upload het vervolgens samen met de eerder genoemde *. cmd* -en *MSI* -bestanden naar de container.

      * Een *TLS 1,2* -map, die een aangepast installatie script (*Main. cmd*) bevat voor het gebruik van sterke crypto grafie/veiliger netwerk protocol (TLS 1,2) en het uitschakelen van oudere SSL/TLS-versies op elk knoop punt van uw Azure-SSIS IR.

      * Een *ZULU OPENJDK* -map met een aangepast installatie script (*Main. cmd*) en Power shell-bestand (*install_openjdk.ps1*) voor het installeren van de ZULU OPENJDK op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u Azure Data Lake Store en flexibele bestands connectors gebruiken voor het verwerken van ORC-en Parquet-bestanden. Zie [Azure Feature Pack voor integratie Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)voor meer informatie. 
      
        [Down load eerst de nieuwste Zulu-openjdk](https://www.azul.com/downloads/zulu/zulu-windows/) (bijvoorbeeld *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) en upload deze vervolgens samen met *Main. cmd* en *install_openjdk.ps1* naar uw container.

        ![Mappen in de map gebruikers scenario's](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Als u deze standaard instellingen voor aangepaste installatie opnieuw wilt gebruiken, kopieert u de inhoud van de geselecteerde map naar uw container.

1. Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert op de ADF-gebruikers interface, schakelt u het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in op de pagina **Geavanceerde instellingen** van het deel venster instellingen voor **Integration runtime** en voert u de SAS-URI van uw container in het vakje **aangepaste installatie container SAS URI** in.
   
1. Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert met behulp van Azure PowerShell, stopt u het als het al is gestart of wordt uitgevoerd, voert u de `Set-AzDataFactoryV2IntegrationRuntime` cmdlet uit met de SAS-URI van uw container als waarde voor `SetupScriptContainerSasUri` para meter en start u vervolgens uw Azure-SSIS IR.

1. Nadat de aangepaste standaard installatie is voltooid en uw Azure-SSIS IR wordt gestart, kunt u de standaard uitvoer van *Main. cmd* en andere uitvoerings logboeken vinden in de map *Main. cmd. log* van uw container.

## <a name="next-steps"></a>Volgende stappen

- [De Enter prise-editie van Azure-SSIS IR instellen](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Betaalde of gelicentieerde onderdelen ontwikkelen voor Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
