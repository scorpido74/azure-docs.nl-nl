---
title: SSIS-pakket uitvoeren met activiteit voor het uitvoeren van SSIS-pakketten-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in Azure Data Factory pijp lijn met behulp van de activiteit voor het uitvoeren van SSIS-pakketten.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9057cefa5108924c57dbc85bbb895b31e804a51c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000641"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Voer een SSIS-pakket uit met de activiteit voor het uitvoeren van SSIS-pakketten in Azure Data Factory
In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket in de Azure Data Factory (ADF)-pijp lijn uitvoert met behulp van de activiteit SSIS-pakket uitvoeren. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Maak een Azure-SSIS Integration runtime (IR) als u er nog geen hebt, door de stapsgewijze instructies in de [zelf studie te volgen: Het inrichten van](tutorial-create-azure-ssis-runtime-portal.md)Azure-SSIS IR.

## <a name="run-a-package-in-the-azure-portal"></a>Een pakket uitvoeren in de Azure Portal
In deze sectie gebruikt u de ADF-gebruikers interface (UI)/app om een ADF-pijp lijn te maken met activiteit voor het uitvoeren van SSIS-pakketten die uw SSIS-pakket uitvoert.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijp lijn maken met een activiteit voor het uitvoeren van SSIS-pakketten
In deze stap gebruikt u ADF-gebruikers interface/-app om een pijp lijn te maken. U voegt een activiteit voor het uitvoeren van SSIS-pakketten toe aan de pijp lijn en configureert deze voor het uitvoeren van uw SSIS-pakket. 

1. Klik op de pagina overzicht van ADF/start in Azure Portal op de tegel **& Auteur** om ADF-gebruikers interface/-app te starten op een afzonderlijk tabblad. 

   ![Startpagina van de gegevensfactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Klik op de pagina **aan de slag** op **pijp lijn maken**: 

   ![Pagina Aan de slag](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Vouw in de werkset **activities** de optie **Algemeen**uit en sleep & een activiteit voor het **uitvoeren van SSIS-pakketten** neer te zetten naar het ontwerp oppervlak voor pijp lijnen. 

   ![Sleep een uitvoeren SSIS-pakket activiteit naar het ontwerp oppervlak](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Geef op het tabblad **Algemeen** voor het uitvoeren van SSIS-pakket activiteit een naam en een beschrijving op voor de activiteit. Optionele time-outwaarden instellen en waarden opnieuw proberen.

   ![Eigenschappen instellen op het tabblad Algemeen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Selecteer op het tabblad **instellingen** voor uitvoering van SSIS-pakket activiteit een Azure-SSIS IR waar u het pakket wilt uitvoeren. Als uw pakket gebruikmaakt van Windows-verificatie voor toegang tot gegevens archieven, bijvoorbeeld SQL-servers/bestands shares op locatie, Azure Files, enzovoort, schakelt u het selectie vakje **Windows-verificatie** in en voert u de waarden in voor de referenties voor het uitvoeren van het pakket (**domein** Gebruikers naam**wacht woord**). // U kunt ook geheimen gebruiken die zijn opgeslagen in uw Azure Key Vault (Azure) als waarden. Als u dit wilt doen, klikt u op het selectie vakje **Azure-sleutel kluis** naast de relevante referentie, selecteert u de bestaande gekoppelde Azure-service of maakt u een nieuwe, en selecteert u vervolgens de geheime naam/versie voor uw referentie waarde.  Wanneer u uw gekoppelde Azure-service maakt/bewerkt, kunt u uw bestaande Azure selecteren/bewerken of een nieuwe maken, maar u moet ADF beheerde identiteits toegang tot uw Azure verlenen als u dit nog niet hebt gedaan. U kunt uw geheimen ook rechtstreeks invoeren in de volgende indeling: `<AKV linked service name>/<secret name>/<secret version>`. Als uw pakket 32-bits runtime nodig heeft om uit te voeren, schakelt u het selectie vakje **32-bits runtime** in. 

   Voor de **pakket locatie**selecteert u **SSISDB**, **Bestands systeem (pakket)** of **Bestands systeem (project)** . Als u **SSISDB** als uw pakket locatie selecteert, die automatisch wordt geselecteerd als uw Azure-SSIS IR is ingericht met een SSIS-catalogus (SSISDB) die wordt gehost door Azure SQL database server/beheerd exemplaar, moet u opgeven welk pakket moet worden uitgevoerd. geïmplementeerd in SSISDB. Als uw Azure-SSIS IR actief is en het selectie vakje **hand matige invoer** is uitgeschakeld, kunt u bladeren en uw bestaande mappen/projecten/pakketten/omgevingen van SSISDB selecteren. Klik op de knop **vernieuwen** om de zojuist toegevoegde mappen/projecten/pakketten/omgevingen van SSISDB op te halen, zodat deze beschikbaar zijn voor bladeren en selectie. Als u de omgevingen voor uw pakket uitvoeringen wilt bladeren/selecteren, moet u uw projecten vooraf configureren om deze omgevingen toe te voegen als verwijzingen vanuit dezelfde mappen onder SSISDB. Zie [SSIS-omgevingen maken/toewijzen](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)voor meer informatie.

   Voor het **niveau van logboek registratie**selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. 

   ![Eigenschappen instellen op het tabblad Instellingen-automatisch](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Als uw Azure-SSIS IR niet wordt uitgevoerd of het selectie vakje **hand matige invoer** is ingeschakeld, kunt u uw pakket-en omgevings paden rechtstreeks in de volgende indelingen `<folder name>/<project name>/<package name>.dtsx` invoeren `<folder name>/<environment name>`: en.

   ![Eigenschappen instellen op het tabblad instellingen-hand matig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Als u **Bestands systeem (pakket)** als uw pakket locatie selecteert, die automatisch wordt geselecteerd als uw Azure-SSIS IR is ingericht zonder SSISDB, moet u opgeven welk pakket moet worden uitgevoerd door een UNC-pad (Universal Naming Convention) op te geven voor uw pakket bestand (`.dtsx`) in het **pad**van het pakket. Als u bijvoorbeeld uw pakket opslaat in Azure Files, wordt `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`het pad van het pakket. 
   
   Als u uw pakket in een afzonderlijk bestand configureert, moet u ook een UNC-pad naar uw configuratie bestand (`.dtsConfig`) in het **configuratiepad**opgeven. Als u bijvoorbeeld uw configuratie in Azure Files opslaat, wordt `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`het configuratiepad.

   ![Eigenschappen instellen op het tabblad instellingen-hand matig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Als u **Bestands systeem (project)** als uw pakket locatie selecteert, moet u opgeven welk pakket moet worden uitgevoerd door een UNC-pad op te geven naar uw`.ispac`project bestand () in het **projectmap** en een pakket`.dtsx`bestand () van uw project in de  **Pakket naam**. Als u bijvoorbeeld het project in Azure Files opslaat, wordt `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`het pad van het project.

   ![Eigenschappen instellen op het tabblad instellingen-hand matig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Vervolgens moet u de referenties opgeven om toegang te krijgen tot uw project/pakket/configuratie bestanden. Als u eerder de waarden hebt ingevoerd voor de referenties voor het uitvoeren van het pakket (zie hierboven), kunt u ze opnieuw gebruiken door hetzelfde selectie vakje voor het **uitvoeren van pakket uitvoerings referenties** in te scha kelen. Als dat niet het geval is, moet u de waarden voor uw pakket toegangs referenties opgeven (**wacht woord**voor**domein**/**gebruikers naam**/). Als u bijvoorbeeld het project/pakket/de configuratie opslaat in azure files, is `Azure`het **domein** `<storage account name>`; de **gebruikers naam** is; en het **wacht woord** `<storage account key>`. U kunt ook geheimen die zijn opgeslagen in uw Azure gebruiken als hun waarden (zie hierboven). Deze referenties worden gebruikt om toegang te krijgen tot uw pakket en onderliggende pakketten in de taak pakket uitvoeren, allemaal vanaf hun eigen pad/hetzelfde project, evenals configuraties, inclusief de instellingen die zijn opgegeven in uw pakketten. 
   
   Als u het **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** -beveiligings niveau hebt gebruikt bij het maken van uw pakket via SQL Server Data tools (SSDT), moet u de waarde voor uw wacht woord invoeren in de **versleuteling wacht woord**. U kunt ook een geheim dat is opgeslagen in uw Azure gebruiken als waarde (zie hierboven). Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt, moet u uw gevoelige waarden opnieuw invoeren in configuratie bestanden of op de **SSIS para meters**/**verbindings beheer**/**eigenschap Tabbladen negeren** (zie hieronder). Als u het beveiligings niveau **EncryptAllWithUserKey** hebt gebruikt, wordt dit niet ondersteund. u moet het pakket daarom opnieuw configureren om het andere beveiligings niveau te gebruiken via SSDT of `dtutil` het opdracht regel hulpprogramma. 
   
   Voor het **niveau van logboek registratie**selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. Als u de pakket uitvoeringen wilt vastleggen buiten het gebruik van de standaard logboek providers die in uw pakket kunnen worden opgegeven, moet u uw Logboekmap opgeven door het UNC-pad op te geven in het **pad voor logboek registratie**. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, wordt `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`het pad naar logboek registratie. In dit pad wordt een submap gemaakt voor elk afzonderlijk pakket dat wordt uitgevoerd en de naam na de run-ID van de uitvoering van SSIS-pakket activiteit, waarbij de logboek bestanden om de vijf minuten worden gegenereerd. 
   
   Ten slotte moet u ook de referenties voor toegang tot uw Logboekmap opgeven. Als u eerder de waarden voor uw pakket toegangs referenties hebt ingevoerd (zie hierboven), kunt u ze opnieuw gebruiken door hetzelfde selectie vakje voor **toegangs referenties voor pakket** in te scha kelen. Als dat niet het geval is, moet u de waarden opgeven voor de toegangs referenties voor de logboek registratie (**domein**/**gebruikers naam**/**wacht woord**). Als u bijvoorbeeld uw logboeken opslaat in azure files, is `Azure`het **domein** `<storage account name>`; de **gebruikers naam** is; en het **wacht woord** `<storage account key>`. U kunt ook geheimen die zijn opgeslagen in uw Azure gebruiken als hun waarden (zie hierboven). Deze referenties worden gebruikt om uw logboeken op te slaan. 
   
   Voor alle hierboven genoemde UNC-paden moet de volledige naam van het bestand kleiner zijn dan 260 tekens en moet de mapnaam kleiner zijn dan 248 tekens.

5. Als uw Azure-SSIS IR wordt uitgevoerd op het tabblad **SSIS para meters** voor uitvoeren SSIS-pakket, wordt **SSISDB** geselecteerd als uw pakket locatie en wordt het selectie vakje **hand matige vermeldingen** op het tabblad **instellingen** uitgeschakeld, de bestaande SSIS de para meters in uw geselecteerde project/pakket van SSISDB worden weer gegeven, zodat u er waarden aan kunt toewijzen. Als dat niet het geval is, kunt u ze één voor één invoeren om hand matig waarden toe te wijzen. Zorg ervoor dat deze bestaan en correct worden ingevoerd voor het slagen van de pakket uitvoering. 
   
   Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt bij het maken van uw pakket via SSDT en **Bestands systeem (pakket)** /**File System (project)** is geselecteerd als uw pakket locatie, moet u ook opnieuw invoeren uw gevoelige para meters om waarden toe te wijzen in configuratie bestanden of op dit tabblad. 
   
   Wanneer u waarden toewijst aan uw para meters, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, ADF systeem variabelen en ADF-pijplijn parameters/-variabelen. U kunt ook geheimen die zijn opgeslagen in uw Azure gebruiken als hun waarden (zie hierboven).

   ![Eigenschappen instellen op het tabblad SSIS-para meters](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Als uw Azure-SSIS IR wordt uitgevoerd op het tabblad **verbindings beheer** voor het uitvoeren van SSIS-pakket activiteit, wordt **SSISDB** geselecteerd als uw pakket locatie. het selectie vakje **hand matige vermeldingen** op het tabblad **instellingen** is uitgeschakeld, de bestaande verbindings beheer in uw geselecteerde project/pakket van SSISDB wordt weer gegeven om waarden aan hun eigenschappen toe te wijzen. Als dat niet het geval is, kunt u ze één voor één invoeren om waarden hand matig toe te wijzen. Controleer of deze bestaan en correct zijn ingevoerd voor het slagen van de pakket uitvoering. 
   
   Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt bij het maken van uw pakket via SSDT en **Bestands systeem (pakket)** /**File System (project)** is geselecteerd als uw pakket locatie, moet u ook opnieuw invoeren de eigenschappen van uw gevoelige verbindings beheer om waarden toe te wijzen in configuratie bestanden of op dit tabblad. 
   
   Wanneer u waarden toewijst aan de eigenschappen van verbindings beheer, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, ADF systeem variabelen en ADF-pijplijn parameters/-variabelen. U kunt ook geheimen die zijn opgeslagen in uw Azure gebruiken als hun waarden (zie hierboven).

   ![Eigenschappen instellen op het tabblad verbindings beheer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Op het tabblad **Eigenschappen onderdrukkingen** voor uitvoering van SSIS-pakket activiteit kunt u de paden van bestaande eigenschappen in het geselecteerde pakket één voor één opgeven om hand matig waarden toe te wijzen. Zorg ervoor dat deze bestaan en correct zijn ingevoerd voor uw pakket uitvoering is voltooid, bijvoorbeeld als u de waarde van uw gebruikers variabele wilt overschrijven, voert u het pad in de volgende `\Package.Variables[User::<variable name>].Value`indeling in:. 
   
   Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt bij het maken van uw pakket via SSDT en **Bestands systeem (pakket)** /**File System (project)** is geselecteerd als uw pakket locatie, moet u ook opnieuw invoeren uw gevoelige eigenschappen om waarden toe te wijzen in configuratie bestanden of op dit tabblad. 
   
   Wanneer u waarden toewijst aan uw eigenschappen, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, ADF systeem variabelen en ADF-pijplijn parameters/-variabelen.

   ![Eigenschappen instellen op het tabblad Eigenschappen onderdrukkingen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   De waarden die zijn toegewezen in configuratie bestanden en op het tabblad *SSIS-para meters* kunnen worden overschreven met behulp van de tabbladen van de**eigenschap** **verbindings beheer**/, terwijl deze worden toegewezen op het tabblad **verbindings beheer** ook worden overschreven met behulp van het tabblad **Eigenschappen onderdrukkingen** .

8. Klik op **valideren** op de werk balk om de configuratie van de pijp lijn te valideren. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

9. Publiceer de pijp lijn naar ADF door te klikken op **de knop Alles publiceren** . 

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren
In deze stap activeert u een pijplijn uitvoering. 

1. Als u een pijplijn uitvoering wilt activeren, klikt u op **trigger** op de werk balk en klikt u op **nu activeren**. 

   ![Nu activeren](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de pijplijn uitvoering en de status ervan samen met andere informatie (zoals run-start tijd). Als u de lijst wilt vernieuwen, klikt u op **Vernieuwen**.

   ![Pijplijnuitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klik op de koppeling **Uitvoeringen van activiteiten weergeven** in de kolom **Acties**. U ziet dat er slechts één activiteit wordt uitgevoerd, omdat de pijp lijn maar één activiteit heeft (de activiteit voor het uitvoeren van SSIS-pakketten).

   ![Uitvoeringen van activiteit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. U kunt de volgende **query** uitvoeren op de SSISDB-data base in uw Azure SQL-Server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

   ![Pakket uitvoeringen controleren](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. U kunt ook de SSISDB-uitvoerings-ID ophalen uit de uitvoer van de uitvoering van de pijplijn activiteit en de ID gebruiken om uitgebreidere uitvoerings logboeken en fout berichten in SQL Server Management Studio (SSMS) te controleren.

   ![De uitvoerings-ID ophalen.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>De pijp lijn plannen met een trigger

U kunt ook een geplande trigger voor uw pijp lijn maken zodat de pijp lijn op een schema wordt uitgevoerd (elk uur, dagelijks, enzovoort). Zie [een Data Factory-Data Factory gebruikers interface maken](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)voor een voor beeld.

## <a name="run-a-package-with-powershell"></a>Een pakket uitvoeren met Power shell
In deze sectie gebruikt u Azure PowerShell om een ADF-pijp lijn te maken met activiteit voor het uitvoeren van SSIS-pakketten die uw SSIS-pakket uitvoert. 

Installeer de nieuwste Azure PowerShell-modules door de stapsgewijze instructies te volgen in [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Een ADF maken met Azure-SSIS IR
U kunt een bestaande ADF gebruiken die al Azure-SSIS IR ingericht is of een nieuwe ADF maakt met Azure-SSIS IR na de stapsgewijze instructies in de [zelf studie: Implementeer SSIS-pakketten naar Azure via](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)Power shell.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijp lijn maken met een activiteit voor het uitvoeren van SSIS-pakketten 
In deze stap maakt u een pijp lijn met een activiteit voor het uitvoeren van SSIS-pakketten. De activiteit voert uw SSIS-pakket uit. 

1. Maak een JSON-bestand met de naam **RunSSISPackagePipeline. json** in de map **C:\ADF\RunSSISPackage** met inhoud die lijkt op het volgende voor beeld:

   > [!IMPORTANT]
   > Vervang object namen/beschrijvingen/paden, eigenschap/parameter waarden, wacht woorden en andere variabelen waarden voordat u het bestand opslaat. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Als u pakketten wilt uitvoeren die zijn opgeslagen in bestands systemen/bestands shares/Azure Files, kunt u de waarden voor de eigenschappen van uw pakket/logboek locatie als volgt opgeven.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Als u pakketten wilt uitvoeren in projecten die zijn opgeslagen in bestands systemen/bestands shares/Azure Files, kunt u als volgt de waarden voor uw pakket locatie-eigenschap invoeren.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Ga in azure PowerShell naar de `C:\ADF\RunSSISPackage` map.

3. Als u de pijp lijn **RunSSISPackagePipeline**wilt maken, voert u de cmdlet **set-AzDataFactoryV2Pipeline** uit.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Hier volgt een voorbeeld van uitvoer:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren
Gebruik de cmdlet **invoke-AzDataFactoryV2Pipeline** om de pijp lijn uit te voeren. De cmdlet retourneert de id voor de pijplijnuitvoering voor toekomstige controle.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer/plak het volgende script in het PowerShell-venster en druk op ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

U kunt de pijp lijn ook bewaken met behulp van de Azure Portal. Zie [de pijp lijn controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)voor stapsgewijze instructies.

### <a name="schedule-the-pipeline-with-a-trigger"></a>De pijp lijn plannen met een trigger
In de vorige stap hebt u de pijp lijn op aanvraag uitgevoerd. U kunt ook een schema trigger maken om de pijp lijn op een schema uit te voeren (elk uur, dagelijks, enzovoort).

1. Maak een JSON-bestand met de naam **MyTrigger. json** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud: 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. Ga in **Azure PowerShell**naar de map **C:\ADF\RunSSISPackage** .
3. Voer de cmdlet **set-AzDataFactoryV2Trigger** uit om de trigger te maken. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. De trigger heeft standaard de status gestopt. Start de trigger door de cmdlet **Start-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Controleer of de trigger is gestart door de cmdlet **Get-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Voer de volgende opdracht na het volgende uur uit. Als de huidige tijd bijvoorbeeld 3:25 uur UTC is, voert u de opdracht uit op 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   U kunt de volgende query uitvoeren op de SSISDB-data base in uw Azure SQL-Server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende blog bericht:
-   [Uw ETL/ELT-werk stromen moderniseren en uitbreiden met SSIS-activiteiten in ADF-pijp lijnen](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
