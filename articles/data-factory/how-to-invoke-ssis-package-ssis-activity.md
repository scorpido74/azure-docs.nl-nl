---
title: Voer een SSIS-pakket uit met de activiteit voor het uitvoeren van SSIS-pakketten-Azure
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijp lijn met behulp van de activiteit voor het uitvoeren van SSIS-pakketten.
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
ms.openlocfilehash: b8ed0a04d2d13556f38873ef5f346d49ba4d1845
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673734"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Voer een SSIS-pakket uit met de activiteit voor het uitvoeren van SSIS-pakketten in Azure Data Factory
In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijp lijn met behulp van de activiteit voor het uitvoeren van SSIS-pakketten. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Een Azure SSIS Integration runtime (IR) maken als u er nog geen hebt, volgt u de stapsgewijze instructies in de [zelf studie: inrichting van Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Een pakket uitvoeren in de Azure Portal
In deze sectie gebruikt u de Data Factory gebruikers interface (UI) of de app om een Data Factory pijp lijn te maken met een activiteit voor het uitvoeren van SSIS-pakketten die uw SSIS-pakket uitvoert.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijp lijn maken met een activiteit voor het uitvoeren van SSIS-pakketten
In deze stap gebruikt u de Data Factory-gebruikers interface of-app om een pijp lijn te maken. U voegt een activiteit voor het uitvoeren van SSIS-pakketten toe aan de pijp lijn en configureert deze voor het uitvoeren van uw SSIS-pakket. 

1. Selecteer op uw Data Factory overzicht of start pagina in de Azure Portal de tegel **auteur & monitor** om de Data Factory-gebruikers interface of-app op een afzonderlijk tabblad te starten. 

   ![Start pagina van Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**. 

   ![Pagina Aan de slag](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Vouw in de werkset **activities** de optie **Algemeen**uit. Sleep vervolgens een activiteit voor het **uitvoeren van SSIS-pakketten** naar het ontwerp oppervlak voor pijp lijnen. 

   ![Sleep een uitvoeren SSIS-pakket activiteit naar het ontwerp oppervlak](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Geef op het tabblad **Algemeen** voor de activiteit SSIS-pakket uitvoeren een naam en een beschrijving op voor de activiteit. Optionele **time-outwaarden** instellen en waarden **opnieuw proberen** .

   ![Eigenschappen instellen op het tabblad Algemeen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Selecteer op het tabblad **instellingen** voor de activiteit SSIS-pakket uitvoeren een Azure-SSIS IR waar u het pakket wilt uitvoeren. Als uw pakket gebruikmaakt van Windows-verificatie voor toegang tot gegevens archieven (bijvoorbeeld SQL-servers of bestands shares on-premises of Azure Files), schakelt u het selectie vakje **Windows-verificatie** in. Voer de waarden in voor de referenties voor het uitvoeren van het pakket in de vakken **domein**, **gebruikers naam**en **wacht woord** . 

    U kunt ook geheimen die zijn opgeslagen in uw Azure-sleutel kluis, gebruiken als waarden. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast de relevante referentie. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de geheime naam of versie voor de referentie waarde.

    Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheimen ook rechtstreeks invoeren in de volgende indeling: `<Key vault linked service name>/<secret name>/<secret version>`. Als uw pakket 32 bits-runtime nodig heeft om uit te voeren, schakelt u het selectie vakje **32-bits runtime** in.

   Voor de **pakket locatie**selecteert u **SSISDB**, **Bestands systeem (pakket)** of **Bestands systeem (project)** . Als u **SSISDB** als uw pakket locatie selecteert, die automatisch wordt geselecteerd als uw Azure-SSIS IR is ingericht met de SSIS-catalogus (SSISDB) die wordt gehost door een Azure SQL database server of beheerde instantie, geeft u op welk pakket moet worden uitgevoerd dat is geïmplementeerd in SSISDB. 

    Als uw Azure-SSIS IR actief is en het selectie vakje **hand matige invoer** is uitgeschakeld, bladert u naar uw bestaande mappen, projecten, pakketten of omgevingen en selecteert u deze in SSISDB. Selecteer **vernieuwen** om de zojuist toegevoegde mappen, projecten, pakketten of omgevingen van SSISDB op te halen, zodat deze beschikbaar zijn voor bladeren en selectie. Als u de omgevingen voor uw pakket uitvoeringen wilt zoeken of selecteren, moet u uw projecten vooraf configureren om deze omgevingen toe te voegen als verwijzingen van dezelfde mappen onder SSISDB. Zie [SSIS-omgevingen maken en toewijzen](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)voor meer informatie.

   Voor het **niveau van logboek registratie**selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. 

   ![Eigenschappen instellen op het tabblad Instellingen-automatisch](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Als uw Azure-SSIS IR niet wordt uitgevoerd of het selectie vakje **hand matige invoer** is ingeschakeld, voert u de paden voor het pakket en de omgeving van SSISDB rechtstreeks in de volgende indelingen in: `<folder name>/<project name>/<package name>.dtsx` en `<folder name>/<environment name>`.

   ![Eigenschappen instellen op het tabblad instellingen-hand matig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Als u **Bestands systeem (pakket)** als uw pakket locatie selecteert, dat automatisch wordt geselecteerd als uw Azure-SSIS IR is ingericht zonder SSISDB, geeft u het pakket op dat moet worden uitgevoerd door een UNC-pad (Universal Naming Convention) naar uw pakket bestand op te geven (@no __t_1_) in het vak **pakket pad** .`.dtsx` Als u bijvoorbeeld uw pakket opslaat in Azure Files, wordt het pad van het pakket `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Als u uw pakket in een afzonderlijk bestand configureert, moet u ook een UNC-pad naar uw configuratie bestand (`.dtsConfig`) in het vak **configuratiepad** opgeven. Als u bijvoorbeeld uw configuratie in Azure Files opslaat, wordt het configuratiepad `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Eigenschappen instellen op het tabblad instellingen-hand matig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Als u **Bestands systeem (project)** als uw pakket locatie selecteert, geeft u het pakket op dat moet worden uitgevoerd door een UNC-pad naar uw project bestand (`.ispac`) op te geven in het vak **pad naar project** en een pakket bestand (`.dtsx`) van uw project in de naam van het **pakket** keuzelijst. Als u bijvoorbeeld het project in Azure Files opslaat, wordt het pad van het project `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Eigenschappen instellen op het tabblad instellingen-hand matig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Geef vervolgens de referenties op voor toegang tot uw project-, pakket-of configuratie bestanden. Als u de waarden voor de referenties voor het uitvoeren van het pakket hebt opgegeven (zie vorige), kunt u ze opnieuw gebruiken door hetzelfde selectie vakje voor het uitvoeren van de **pakket referenties** in te scha kelen. Voer anders de waarden in voor uw pakket toegangs referenties in de vakken **domein**, **gebruikers naam**en **wacht woord** . Als u bijvoorbeeld uw project, pakket of configuratie in Azure Files opslaat, wordt het domein `Azure`, is de gebruikers naam `<storage account name>`en wordt het wacht woord `<storage account key>`. 

   U kunt ook geheimen die zijn opgeslagen in uw sleutel kluis gebruiken als waarden (zie vorige). Deze referenties worden gebruikt om toegang te krijgen tot uw pakket en onderliggende pakketten in de taak pakket uitvoeren, allemaal vanaf hun eigen pad of hetzelfde project, evenals configuraties, die zijn opgegeven in uw pakketten. 
   
   Als u het beveiligings niveau **EncryptAllWithPassword** of **EncryptSensitiveWithPassword** hebt gebruikt toen u het pakket hebt gemaakt via SQL Server Data tools, voert u de waarde voor uw wacht woord in het vak **versleutelings wachtwoord** in. U kunt ook een geheim dat is opgeslagen in uw sleutel kluis gebruiken als waarde (zie vorige). Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt, voert u uw gevoelige waarden opnieuw in bij configuratie bestanden of op de **SSIS-para meters**, **verbindings beheer**functies of **Eigenschappen onderdrukkingen** tabbladen (zie later). 

   Als u het beveiligings niveau **EncryptAllWithUserKey** hebt gebruikt, wordt dit niet ondersteund. U moet het pakket opnieuw configureren om een ander beveiligings niveau te gebruiken via SQL Server Data Tools of het `dtutil` opdracht regel programma. 
   
   Voor het **niveau van logboek registratie**selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. Als u de pakket uitvoeringen wilt vastleggen naast het gebruik van de standaard logboek providers die in uw pakket kunnen worden opgegeven, geeft u uw logboekmap op door het UNC-pad op te geven in het vak **pad naar logboek** bestand. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, wordt het pad voor logboek registratie `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Er wordt in dit pad een submap gemaakt voor elk afzonderlijk pakket dat wordt uitgevoerd en de naam krijgt na de run-ID van de uitvoering van SSIS-pakket activiteit, waarbij de logboek bestanden om de vijf minuten worden gegenereerd. 
   
   Geef ten slotte de referenties voor toegang tot uw logboekmap op. Als u de waarden voor uw pakket toegangs referenties (zie vorige) eerder hebt opgegeven, kunt u ze opnieuw gebruiken door **hetzelfde selectie vakje als toegangs referenties** voor het pakket in te scha kelen. Voer anders de waarden in voor uw toegangs referenties voor logboek registratie in de vakken **domein**, **gebruikers naam**en **wacht woord** . Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het domein `Azure`, de gebruikers naam `<storage account name>`en het wacht woord `<storage account key>`. 

    U kunt ook geheimen die zijn opgeslagen in uw sleutel kluis gebruiken als waarden (zie vorige). Deze referenties worden gebruikt om uw logboeken op te slaan. 
   
   Voor alle eerder genoemde UNC-paden moet de volledige naam van het bestand kleiner zijn dan 260 tekens. De mapnaam mag niet langer zijn dan 248 tekens.

1. Als uw Azure-SSIS IR wordt uitgevoerd op het tabblad **SSIS para meters** voor de activiteit EXECUTe SSIS-pakket, wordt **SSISDB** geselecteerd als uw pakket locatie en wordt het selectie vakje **hand matige vermeldingen** op het tabblad **instellingen** uitgeschakeld, de bestaande SSIS de para meters in het geselecteerde project of pakket van SSISDB worden weer gegeven, zodat u waarden aan hen kunt toewijzen. Als dat niet het geval is, kunt u ze één voor één invoeren om waarden hand matig toe te wijzen. Zorg ervoor dat deze bestaan en correct zijn ingevoerd voor het slagen van de pakket uitvoering. 
   
   Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt toen u het pakket hebt gemaakt via SQL Server Data tools en het **Bestands systeem (pakket)** of het **Bestands systeem (project)** is geselecteerd als uw pakket locatie, moet u ook opnieuw invoeren uw gevoelige para meters om waarden toe te wijzen in configuratie bestanden of op dit tabblad. 
   
   Wanneer u waarden toewijst aan uw para meters, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, Data Factory systeem variabelen en Data Factory pijplijn parameters of-variabelen. U kunt ook geheimen die zijn opgeslagen in uw sleutel kluis gebruiken als waarden (zie vorige).

   ![Eigenschappen instellen op het tabblad SSIS-para meters](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Als uw Azure-SSIS IR wordt uitgevoerd op het tabblad **verbindings beheer** van de activiteit uitvoeren SSIS-pakket, wordt **SSISDB** geselecteerd als uw pakket locatie en wordt het selectie vakje **hand matige vermeldingen** op het tabblad **instellingen** uitgeschakeld, de bestaande verbindings beheer in uw geselecteerde project of pakket van SSISDB wordt weer gegeven om waarden aan hun eigenschappen toe te wijzen. Als dat niet het geval is, kunt u ze één voor één invoeren om waarden hand matig toe te wijzen aan hun eigenschappen. Zorg ervoor dat deze bestaan en correct zijn ingevoerd voor het slagen van de pakket uitvoering. 
   
   Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt toen u het pakket hebt gemaakt via SQL Server Data tools en het **Bestands systeem (pakket)** of het **Bestands systeem (project)** is geselecteerd als uw pakket locatie, moet u ook opnieuw invoeren de eigenschappen van uw gevoelige verbindings beheer om waarden toe te wijzen in configuratie bestanden of op dit tabblad. 
   
   Wanneer u waarden toewijst aan de eigenschappen van verbindings beheer, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, Data Factory systeem variabelen en Data Factory pijplijn parameters of-variabelen. U kunt ook geheimen die zijn opgeslagen in uw sleutel kluis gebruiken als waarden (zie vorige).

   ![Eigenschappen instellen op het tabblad verbindings beheer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Voer op het tabblad **Eigenschappen onderdrukkingen** voor de activiteit uitvoeren SSIS-pakket de paden in van bestaande eigenschappen in het geselecteerde pakket één voor één om hand matig waarden toe te wijzen. Zorg ervoor dat deze bestaan en correct zijn ingevoerd voor het slagen van de pakket uitvoering. Als u bijvoorbeeld de waarde van uw gebruikers variabele wilt overschrijven, voert u het pad in de volgende indeling in: `\Package.Variables[User::<variable name>].Value`. 
   
   Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt toen u het pakket hebt gemaakt via SQL Server Data tools en het **Bestands systeem (pakket)** of het **Bestands systeem (project)** is geselecteerd als uw pakket locatie, moet u ook opnieuw invoeren uw gevoelige eigenschappen om waarden toe te wijzen in configuratie bestanden of op dit tabblad. 
   
   Wanneer u waarden toewijst aan uw eigenschappen, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, Data Factory systeem variabelen en Data Factory pijplijn parameters of-variabelen.

   ![Eigenschappen instellen op het tabblad Eigenschappen onderdrukkingen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   De waarden die zijn toegewezen in configuratie bestanden en op het tabblad **SSIS-para meters** kunnen worden overschreven met behulp van de tabbladen **verbindings beheer** of **eigenschaps onderdrukkingen** . De waarden die zijn toegewezen op het tabblad **verbindings beheer** kunnen ook worden overschreven met behulp van het tabblad **Eigenschappen onderdrukkingen** .

1. Selecteer **valideren** op de werk balk om de configuratie van de pijp lijn te valideren. Selecteer **>>** om het **rapport pijplijn validatie**te sluiten.

1. Als u de pijp lijn naar Data Factory wilt publiceren, selecteert u **Alles publiceren**. 

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren
In deze stap activeert u een pijplijn uitvoering. 

1. Als u een pijplijn uitvoering wilt activeren, selecteert u **activeren** op de werk balk en selecteert u **nu activeren**. 

   ![Nu activeren](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de pijplijn uitvoering en de status ervan samen met andere informatie, zoals de start tijd van de **uitvoering** . Selecteer **Vernieuwen** om de weergave te vernieuwen.

   ![Pijplijnuitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecteer de koppeling **Uitvoeringen van activiteit weergeven** in de kolom **Acties**. U ziet dat er slechts één activiteit wordt uitgevoerd omdat de pijp lijn maar één activiteit heeft. Het is de activiteit voor het uitvoeren van SSIS-pakketten.

   ![Uitvoering van activiteiten](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Voer de volgende query uit op de SSISDB-data base in uw SQL-Server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

   ![Pakket uitvoeringen controleren](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. U kunt ook de SSISDB-uitvoerings-ID ophalen uit de uitvoer van de uitvoering van de pijplijn activiteit en de ID gebruiken om uitgebreidere uitvoerings logboeken en fout berichten in SQL Server Management Studio te controleren.

   ![De uitvoerings-ID ophalen.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>De pijp lijn plannen met een trigger

U kunt ook een geplande trigger voor uw pijp lijn maken zodat de pijp lijn op een schema wordt uitgevoerd, zoals elk uur of dagelijks. Zie [een Data Factory-Data Factory gebruikers interface maken](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)voor een voor beeld.

## <a name="run-a-package-with-powershell"></a>Een pakket uitvoeren met Power shell
In deze sectie gebruikt u Azure PowerShell om een Data Factory-pijp lijn te maken met een activiteit voor het uitvoeren van SSIS-pakketten die uw SSIS-pakket uitvoert. 

Installeer de nieuwste Azure PowerShell-modules door de stapsgewijze instructies te volgen in [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Een data factory met Azure-SSIS IR maken
U kunt een bestaand data factory gebruiken dat al Azure-SSIS IR ingericht is of een nieuw data factory maakt met Azure-SSIS IR. Volg de stapsgewijze instructies in de [zelf studie: implementeer SSIS-pakketten naar Azure via Power shell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijp lijn maken met een activiteit voor het uitvoeren van SSIS-pakketten 
In deze stap maakt u een pijp lijn met een activiteit voor het uitvoeren van SSIS-pakketten. De activiteit voert uw SSIS-pakket uit. 

1. Maak een JSON-bestand met de naam *RunSSISPackagePipeline. json* in de map *C:\ADF\RunSSISPackage* met inhoud die lijkt op het volgende voor beeld.

   > [!IMPORTANT]
   > Vervang object namen, beschrijvingen en paden, eigenschaps-of parameter waarden, wacht woorden en andere variabelen waarden voordat u het bestand opslaat. 
    
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

   Als u pakketten wilt uitvoeren die zijn opgeslagen in bestands systemen, bestands shares of Azure Files, voert u de waarden voor uw pakket-of logboek locatie-eigenschappen als volgt in:

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

   Als u pakketten wilt uitvoeren in projecten die zijn opgeslagen in bestands systemen, bestands shares of Azure Files, voert u de waarden voor de eigenschap pakket locatie als volgt in:

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

2. Ga in Azure PowerShell naar de map *C:\ADF\RunSSISPackage* .

3. Als u de pijp lijn **RunSSISPackagePipeline**wilt maken, voert u de cmdlet **set-AzDataFactoryV2Pipeline** uit.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Hier volgt een voor beeld van de uitvoer:

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

Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer of plak het volgende script in het Power shell-venster en selecteer ENTER. 

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
In de vorige stap hebt u de pijp lijn op aanvraag uitgevoerd. U kunt ook een schema trigger maken om de pijp lijn op een schema uit te voeren, zoals elk uur of dagelijks.

1. Maak een JSON-bestand met de naam *MyTrigger. json* in de map *C:\ADF\RunSSISPackage* met de volgende inhoud: 
        
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
    
1. Ga in Azure PowerShell naar de map *C:\ADF\RunSSISPackage* .
1. Voer de cmdlet **set-AzDataFactoryV2Trigger** uit om de trigger te maken. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. De trigger heeft standaard de status gestopt. Start de trigger door de cmdlet **Start-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Controleer of de trigger is gestart door de cmdlet **Get-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Voer de volgende opdracht na het volgende uur uit. Als de huidige tijd bijvoorbeeld 3:25 uur UTC is, voert u de opdracht uit op 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Voer de volgende query uit op de SSISDB-data base in uw SQL-Server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende blog bericht:
- [Uw ETL/ELT-werk stromen moderniseren en uitbreiden met SSIS-activiteiten in Azure Data Factory pijp lijnen](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
