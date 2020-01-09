---
title: Setup aanpassen voor de Azure-SSIS Integration runtime
description: In dit artikel wordt beschreven hoe u de aangepaste installatie-interface voor de Azure-SSIS Integration runtime gebruikt voor het installeren van extra onderdelen of het wijzigen van instellingen
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
ms.date: 12/23/2019
ms.openlocfilehash: ccf7ba2fd27dabdb090be87c5438ad68471996da
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497041"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Setup aanpassen voor de Azure-SSIS Integration runtime

De aangepaste configuratie-interface voor de Azure-SSIS Integration Runtime biedt een interface om uw eigen installatie stappen toe te voegen tijdens het inrichten of opnieuw configureren van uw Azure-SSIS IR. 

Met aangepaste installatie kunt u de standaard configuratie of-omgeving wijzigen (bijvoorbeeld om extra Windows-Services te starten of toegangs referenties voor bestands shares op te slaan) of extra onderdelen (bijvoorbeeld assembly's, stuur Programma's of extensies) installeren op elk knoop punt van uw Azure-SSIS IR.

Er zijn twee manieren om aangepaste installatie op uw Azure-SSIS IR uit te voeren: snelle aangepaste installatie zonder scripts en aangepaste standaard instellingen met scripts.

Met snelle aangepaste installatie kunt u enkele algemene systeem configuraties/Windows-opdrachten uitvoeren of een aantal populaire/aanbevolen extra onderdelen installeren zonder scripts te gebruiken.  

Met standaard aangepaste Setup moet u een script en de bijbehorende bestanden voorbereiden en deze uploaden naar een BLOB-container in uw Azure Storage-account. U geeft een Shared Access Signature (SAS) Uniform Resource Identifier (URI) voor uw container op wanneer u uw Azure-SSIS IR inricht of opnieuw configureert. Elk knoop punt van uw Azure-SSIS IR downloadt vervolgens het script en de bijbehorende bestanden uit uw container en voert uw aangepaste installatie uit met verhoogde bevoegdheden. Wanneer uw aangepaste installatie is voltooid, uploadt elk knoop punt de standaard uitvoer van de uitvoering en andere logboeken naar uw container.

U kunt zowel vrije als niet-gelicentieerde en betaalde/gelicentieerde onderdelen installeren met een aangepaste snelle/standaard instelling. Als u een ISV bent, raadpleegt u onze documentatie over [het ontwikkelen van betaalde of gelicentieerde onderdelen voor Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> De knoop punten van de v2-serie van Azure-SSIS IR zijn niet geschikt voor aangepaste installatie, dus gebruik in plaats daarvan de knoop punten uit de V3-serie.  Als u de knoop punten van de v2-serie al gebruikt, moet u zo snel mogelijk overschakelen op het gebruik van de knoop punten van de V3-serie.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen gelden alleen voor standaard aangepaste Setup:

- Als u `gacutil.exe` in uw script wilt gebruiken voor het installeren van assembly's in de GAC (Global assembly cache), moet u `gacutil.exe` opgeven als onderdeel van uw aangepaste installatie, of u kunt de kopie van de open bare preview-container hieronder gebruiken.

- Als u wilt verwijzen naar een submap in uw script, wordt de `.\` notatie door `msiexec.exe` niet ondersteund om te verwijzen naar de hoofdmap. Gebruik een opdracht als `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` in plaats van `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Beheerders shares, d.w.z. verborgen netwerk shares die automatisch door Windows worden gemaakt, worden momenteel niet ondersteund op de Azure-SSIS IR.

- Het ODBC-stuur programma voor IBM iSeries-toegang wordt niet ondersteund op de Azure-SSIS IR. Tijdens uw aangepaste installatie worden mogelijk installatie fouten weer geven. Neem contact op met IBM ondersteuning voor hulp.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u uw Azure-SSIS IR wilt aanpassen, moet u het volgende doen:

- [Azure-abonnement](https://azure.microsoft.com/)

- [Uw Azure-SSIS IR inrichten](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Een Azure Storage-account](https://azure.microsoft.com/services/storage/). Niet vereist voor snelle aangepaste installatie. Voor aangepaste standaard instellingen uploadt en slaat u uw aangepaste installatie script en de bijbehorende bestanden op in een BLOB-container. Het aangepaste installatie proces uploadt ook de uitvoerings logboeken naar dezelfde BLOB-container.

## <a name="instructions"></a>Instructies

1. Als u uw Azure-SSIS IR wilt inrichten of opnieuw configureren met Power shell, downloadt en installeert u [Azure PowerShell](/powershell/azure/install-az-ps). Ga door naar stap 4 voor snelle aangepaste installatie.

1. Bereid uw aangepaste installatie script en de bijbehorende bestanden voor (bijvoorbeeld. bat,. cmd,. exe,. dll,. msi of. ps1-bestanden).

   1. U moet een script bestand hebben met de naam `main.cmd`, het toegangs punt van uw aangepaste installatie.

   1. U moet ervoor zorgen dat het script op de achtergrond kan worden uitgevoerd. het wordt aanbevolen het script eerst te testen op de lokale computer.

   1. Als u wilt dat extra logboeken die worden gegenereerd door andere hulpprogram ma's (bijvoorbeeld `msiexec.exe`) worden geüpload naar uw container, geeft u de vooraf gedefinieerde omgevings variabele op `CUSTOM_SETUP_SCRIPT_LOG_DIR` als de logboekmap in uw scripts (bijvoorbeeld `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Down load, installeer en start [Azure Storage Explorer](https://storageexplorer.com/).

   1. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts** en selecteert **u verbinding maken met Azure Storage**.

      ![Verbinding maken met Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Selecteer **een opslag accountnaam en-sleutel gebruiken** en selecteer **volgende**.

      ![De naam en sleutel van een opslagaccount gebruiken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Voer de naam en sleutel van uw Azure Storage-account in, selecteer **volgende**en selecteer vervolgens **verbinding maken**.

      ![Account naam en-sleutel voor het archief opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Klik onder uw verbonden Azure Storage-account met de rechter muisknop op **BLOB-containers**, selecteer **BLOB-container maken**en geef de nieuwe container een naam.

      ![Een blob-container maken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Selecteer de nieuwe container en upload uw aangepaste installatie script en de bijbehorende bestanden. Zorg ervoor dat u `main.cmd` op het hoogste niveau van uw container uploadt, niet in een map. Zorg er ook voor dat de container alleen de benodigde aangepaste installatie bestanden bevat. Als u deze vervolgens naar uw Azure-SSIS IR wilt downloaden, duurt het later niet lang. De maximale periode voor aangepaste installatie is momenteel ingesteld op 45 minuten voordat er een time-out optreedt. Dit omvat de tijd om alle bestanden uit de container te downloaden en te installeren op Azure-SSIS IR. Als er een langere periode nodig is, kunt u een ondersteunings ticket genereren.

      ![Bestanden uploaden naar de BLOB-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Klik met de rechter muisknop op de container en selecteer **Shared Access Signature ophalen**.

      ![De Shared Access Signature voor de container ophalen](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Maak de SAS-URI voor uw container met een voldoende lange verloop tijd en met de machtigingen lezen en schrijven + lijst. U hebt de SAS-URI nodig om uw aangepaste installatie script en de bijbehorende bestanden te downloaden en uit te voeren wanneer een wille keurig knoop punt van uw Azure-SSIS IR opnieuw wordt bijgewerkt/opnieuw wordt gestart. U hebt schrijf machtigingen nodig voor het uploaden van de installatie Logboeken.

      > [!IMPORTANT]
      > Zorg ervoor dat de SAS-URI niet verloopt en dat aangepaste configuratie resources altijd beschikbaar zijn tijdens de hele levens cyclus van uw Azure-SSIS IR, van het maken tot verwijderen, met name als u uw Azure-SSIS IR tijdens deze periode regel matig stopt en start.

      ![De Shared Access Signature voor de container genereren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Kopieer de SAS-URI van uw container en sla deze op.

      ![De Shared Access Signature kopiëren en opslaan](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert met Data Factory gebruikers interface, kunt u aangepaste Setup toevoegen/verwijderen door het selectie vakje **uw Azure-SSIS Integration runtime met aanvullende systeem configuraties/onderdeel installaties aanpassen** in te scha kelen in het gedeelte **Geavanceerde instellingen** van het configuratie paneel voor Integration runtime. 

   Als u aangepaste standaard instellingen wilt toevoegen, voert u de SAS-URI van uw container in het veld **SAS URI voor de aangepaste installatie container** in. 
   
   Als u snelle aangepaste installatie wilt toevoegen, selecteert u **Nieuw** om het deel venster **snelle aangepaste installatie toevoegen** te openen en selecteert u vervolgens de gewenste typen onder het vervolg keuzemenu voor het **aangepaste installatie type** :

   1. Als u het **opdracht veld cmdkey uitvoeren** selecteert, kunt u toegangs referenties voor uw bestands shares/Azure Files op Azure-SSIS IR blijven door de doel computer/domein naam, de account naam/gebruikers naam en de account sleutel/het wacht woord in de velden **/add**, **/User**en **/Pass** op te geven. Dit is vergelijkbaar met het uitvoeren van de Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) -opdracht op uw lokale computer.
   
   1. Als u het type **omgevings variabele toevoegen** selecteert, kunt u Windows-omgevings variabelen toevoegen voor gebruik in uw pakketten die worden uitgevoerd op Azure-SSIS IR door respectievelijk de naam en waarde van de omgevings variabele in te voeren in de velden **naam** en **variabele waarde** . Dit is vergelijkbaar met het uitvoeren van de Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) -opdracht op uw lokale computer.

   1. Als u het **onderdeel type licentie installeren** selecteert, kunt u in het vervolg keuzemenu **onderdeel naam** alle geïntegreerde onderdelen selecteren uit onze ISV-partners:

      1. Als u het onderdeel **taak-Factory van SentryOne** selecteert, kunt u de [taken fabrieks](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) suite van onderdelen van SentryOne op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het veld **licentie sleutel** . De huidige geïntegreerde versie is **2019.4.3**.

      1. Als u de **oh22's-HEDDA selecteert. IO** -onderdeel kunt u de [HEDDA installeren. ](https://hedda.io/ssis-component/)Het onderdeel io-gegevens kwaliteit/opschonen van oh22 op uw Azure-SSIS IR na het aanschaffen van de service. De huidige geïntegreerde versie is **1.0.13**.

      1. Als u het **oh22's SQLPhonetics.net** -onderdeel selecteert, kunt u de [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) -gegevens kwaliteit/het overeenkomende onderdeel van oh22 op uw Azure-SSIS IR installeren door de product licentie code in te voeren die u hebt aangeschaft in het veld **licentie sleutel** . De huidige geïntegreerde versie is **1.0.43**.
   
   Uw toegevoegde snelle aangepaste Setup wordt weer gegeven in de sectie **Geavanceerde instellingen** . Als u deze wilt verwijderen, kunt u de bijbehorende selectie vakjes selecteren en vervolgens **verwijderen**selecteren.

   ![Geavanceerde instellingen met aangepaste installatie](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

   Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert met Power shell, kunt u aangepaste Setup toevoegen/verwijderen door de `Set-AzDataFactoryV2IntegrationRuntime`-cmdlet uit te voeren voordat u de Azure-SSIS IR start.
   
   Voor aangepaste standaard instellingen kunt u de SAS-URI van uw container opgeven als waarde voor de para meter `SetupScriptContainerSasUri`. Bijvoorbeeld:

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                         -Name $MyAzureSsisIrName `
                                         -ResourceGroupName $MyResourceGroupName `
                                         -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                           -Name $MyAzureSsisIrName `
                                           -ResourceGroupName $MyResourceGroupName
   ```
   
   Nadat de aangepaste standaard installatie is voltooid en uw Azure-SSIS IR wordt gestart, kunt u de standaard uitvoer van `main.cmd` en andere uitvoerings logboeken vinden in de map `main.cmd.log` van uw opslag container.

1. Als u enkele voor beelden van aangepaste standaard instellingen wilt zien, maakt u verbinding met onze open bare preview-container met Azure Storage Explorer.

   1. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts**, selecteert **u verbinding maken met Azure Storage**, selecteert **u een Connection String of een URI voor de hand tekening voor gedeelde toegang gebruiken**en selecteert u vervolgens **volgende**.

      ![Verbinding maken met Azure Storage met de Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Selecteer **een SAS-URI gebruiken** en voer de volgende SAS-URI in voor de open bare preview-container. Selecteer **volgende**en selecteer vervolgens **verbinding maken**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![De Shared Access Signature voor de container opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Selecteer de gekoppelde open bare preview-container en dubbel klik op de map `CustomSetupScript`. In deze map zijn de volgende items:

      1. Een `Sample` map die een aangepaste installatie bevat voor het installeren van een basis taak op elk knoop punt van uw Azure-SSIS IR. De taak heeft niets maar een paar seconden in de slaap stand. De map bevat ook een `gacutil` map, de volledige inhoud waarvan (`gacutil.exe`, `gacutil.exe.config`en `1033\gacutlrc.dll`) naar de container kan worden gekopieerd.

      1. Een `UserScenarios` map, die verschillende aangepaste installatie voorbeelden bevat uit echte gebruikers scenario's.

      ![Inhoud van de open bare preview-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. Dubbel klik op de map `UserScenarios` om de volgende items te vinden:

      1. Een `.NET FRAMEWORK 3.5` map die een aangepaste installatie bevat voor het installeren van een eerdere versie van de .NET Framework die mogelijk vereist is voor aangepaste onderdelen op elk knoop punt van uw Azure-SSIS IR.

      1. Een `BCP`-map, die een aangepaste installatie bevat voor het installeren van SQL Server opdracht regel programma's (`MsSqlCmdLnUtils.msi`), met inbegrip van het Bulk Copy programma (`bcp`), op elk knoop punt van uw Azure-SSIS IR.

      1. Een `EXCEL` map die een aangepast installatie script (`main.cmd`) bevat voor het installeren C# van assembly's/bibliotheken die u in script taken kunt gebruiken om Excel-bestanden dynamisch te lezen/schrijven op elk knoop punt van uw Azure-SSIS IR. Down load eerst `ExcelDataReader.dll` van [hier](https://www.nuget.org/packages/ExcelDataReader/) en `DocumentFormat.OpenXml.dll` van [hier](https://www.nuget.org/packages/DocumentFormat.OpenXml/)en upload deze allemaal samen met `main.cmd` in uw container. Als u alleen de standaard-micro soft-verbindings beheer/-bron/-bestemming wilt gebruiken, is het vereiste toegangs herdistribueersysteem al vooraf geïnstalleerd op uw Azure-SSIS IR, dus hebt u geen aangepaste installatie nodig.
      
      1. Een `MYSQL ODBC` map, die een aangepast installatie script (`main.cmd`) bevat voor het installeren van de MySQL ODBC-stuur Programma's op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-verbindings beheer/-bron/-bestemming gebruiken om verbinding te maken met de MySQL-server. Down load eerst de nieuwste 64-bits en 32-bits versies van MySQL ODBC-stuur programma-installatie Programma's, bijvoorbeeld `mysql-connector-odbc-8.0.13-winx64.msi` en `mysql-connector-odbc-8.0.13-win32.msi`-uit [MySQL](https://dev.mysql.com/downloads/connector/odbc/)en upload ze allemaal samen met `main.cmd` in uw container.

      1. Een `ORACLE ENTERPRISE`-map, die een aangepast installatie script (`main.cmd`) en een installatie van het configuratie bestand voor de achtergrond (`client.rsp`) bevat voor het installeren van de Oracle-connectors en het OCI-stuur programma op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Oracle-verbindings beheer/-bron/-bestemming gebruiken om verbinding te maken met de Oracle-server. Down load eerst micro soft-connectors v 5.0 voor Oracle (`AttunitySSISOraAdaptersSetup.msi` en `AttunitySSISOraAdaptersSetup64.msi`) van het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=55179) en de nieuwste Oracle-client, bijvoorbeeld `winx64_12102_client.zip`-van [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), en upload ze allemaal samen met `main.cmd` en `client.rsp` naar uw container. Als u TNS gebruikt om verbinding te maken met Oracle, moet u ook `tnsnames.ora`downloaden, bewerken en uploaden naar uw container, zodat deze kan worden gekopieerd naar de map Oracle-installatie tijdens de installatie.

      1. Een `ORACLE STANDARD ADO.NET` map, die een aangepaste installatie script (`main.cmd`) bevat om het Oracle ODP.NET-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ADO.NET-verbindings beheer/-bron/-bestemming gebruiken om verbinding te maken met de Oracle-server. Down load eerst het meest recente Oracle ODP.NET-stuur programma, bijvoorbeeld `ODP.NET_Managed_ODAC122cR1.zip`-van [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), en upload het samen met `main.cmd` naar uw container.
       
      1. Een `ORACLE STANDARD ODBC` map die een aangepast installatie script (`main.cmd`) bevat voor de installatie van het Oracle ODBC-stuur programma en het configureren van de DSN op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u het type ODBC-verbindings beheer/bron/bestemming of Power Query verbindings beheer/bron met ODBC-gegevens bron gebruiken om verbinding te maken met Oracle server. Down load eerst de nieuwste Oracle Instant-client (Basic package of Basic Lite package) en het ODBC-pakket, bijvoorbeeld de 64-bits pakketten van [hier](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-pakket: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, Basic Lite-pakket: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC-pakket: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) of de 32-bits pakketten van [hier](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (basic-pakket: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, Basic Lite-pakket: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC-pakket: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) en upload deze allemaal samen met `main.cmd` naar uw container.

      1. Een `ORACLE STANDARD OLEDB` map die een aangepast installatie script (`main.cmd`) bevat om het Oracle OLEDB-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de OLEDB-verbindings beheer/-bron/-bestemming gebruiken om verbinding te maken met de Oracle-server. Down load eerst het nieuwste Oracle OLEDB-stuur programma, bijvoorbeeld `ODAC122010Xcopy_x64.zip`-van [Oracle](https://www.oracle.com/partners/campaign/index-090165.html), en upload het vervolgens samen met `main.cmd` naar uw container.

      1. Een `POSTGRESQL ODBC` map, die een aangepaste installatie script (`main.cmd`) bevat om de PostgreSQL ODBC-stuur Programma's te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-verbindings beheer/-bron/-bestemming gebruiken om verbinding te maken met de PostgreSQL-server. Down load eerst de nieuwste 64-bits en 32-bits versies van PostgreSQL ODBC-stuur programma-installatie Programma's, bijvoorbeeld `psqlodbc_x64.msi` en `psqlodbc_x86.msi`-van [postgresql](https://www.postgresql.org/ftp/odbc/versions/msi/), en upload deze allemaal samen met `main.cmd` in uw container.

      1. Een `SAP BW` map, die een aangepaste installatie script (`main.cmd`) bevat voor het installeren van de SAP .NET-connector-Assembly (`librfc32.dll`) op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de SAP BW verbindings beheer/bron/bestemming gebruiken om verbinding te maken met SAP BW-server. Upload eerst de 64-bits of de 32-bits versie van `librfc32.dll` vanuit de SAP-installatiemap samen met `main.cmd` naar uw container. Het script kopieert vervolgens de SAP-assembly naar de map `%windir%\SysWow64` of `%windir%\System32` tijdens de installatie.

      1. Een `STORAGE` map, die een aangepaste installatie bevat om Azure PowerShell te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u SSIS-pakketten implementeren en uitvoeren die [Power shell-scripts uitvoeren om uw Azure Storage-account te bewerken](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopieer `main.cmd`, een voor beeld `AzurePowerShell.msi` (of gebruik de nieuwste versie) en `storage.ps1` naar uw container. Gebruik Power shell. dtsx als een sjabloon voor uw pakketten. De pakket sjabloon bevat een combi natie van een [Azure Blob-Download taak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), die `storage.ps1` downloadt als een gewijzigd Power shell-script, en een taak voor het uitvoeren van een [proces](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) waarmee het script wordt uitgevoerd op elk knoop punt.

      1. Een `TERADATA` map met een aangepast installatie script (`main.cmd`), het bijbehorende bestand (`install.cmd`) en de installatie pakketten (`.msi`). Met deze bestanden worden de Teradata-connectors, de TPT-API en het ODBC-stuur programma geïnstalleerd op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Teradata-verbindings beheer/-bron/-bestemming gebruiken om verbinding te maken met de Teradata-server. Down load eerst de Teradata-Hulpprogram Ma's en Hulpprogram Ma's 15. x zip-bestand (bijvoorbeeld `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) van [Teradata](http://partnerintelligence.teradata.com)en upload het vervolgens samen met de bovenstaande `.cmd` en `.msi` bestanden naar uw container.

      1. Een `ZULU OPENJDK` map die een aangepast installatie script (`main.cmd`) en Power shell-bestand (`install_openjdk.ps1`) bevat om Zulu OpenJDK te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u Azure Data Lake Store/flexibele bestands connectors gebruiken voor het verwerken van ORC/Parquet-bestanden. Zie [hier](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) voor meer informatie. Down load eerst de nieuwste Zulu- [openjdk, bijvoorbeeld `zulu8.33.0.1-jdk8.0.192-win_x64.zip`, en](https://www.azul.com/downloads/zulu/zulu-windows/)upload deze vervolgens samen met `main.cmd` en `install_openjdk.ps1` in uw container.

      ![Mappen in de map gebruikers scenario's](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Kopieer en plak de inhoud van de geselecteerde map in uw container om deze aangepaste installatie voorbeelden uit te proberen.
   
      Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert met Data Factory gebruikers interface, schakelt u het selectie vakje **uw Azure-SSIS Integration runtime met aanvullende systeem configuraties/onderdeel installaties aanpassen** in het gedeelte **Geavanceerde instellingen** in en voert u de SAS-URI van uw container in het veld **SAS URI voor de aangepaste installatie container** in.
   
      Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert met Power shell, voert u de `Set-AzDataFactoryV2IntegrationRuntime` cmdlet uit met de SAS-URI van uw container als waarde voor de `SetupScriptContainerSasUri`-para meter.

## <a name="next-steps"></a>Volgende stappen

-   [Enter prise-editie van de Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Betaalde of gelicentieerde aangepaste onderdelen ontwikkelen voor Azure-SSIS Integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
