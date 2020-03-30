---
title: Een SSIS-pakket uitvoeren met de activiteit SSIS-pakket uitvoeren
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijplijn met behulp van de activiteit SSIS-pakket uitvoeren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: 6027c2d94535ca2ef5c41e7027fe070c6ccb21a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260629"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Een SSIS-pakket uitvoeren met de activiteit SSIS-pakket uitvoeren in Azure Data Factory
In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijplijn met behulp van de activiteit SSIS-pakket uitvoeren. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Maak een Azure-SSIS-integratieruntime (IR) als u die nog niet hebt door de stapsgewijze instructies in de [zelfstudie: Azure-SSIS IR interichten.](tutorial-create-azure-ssis-runtime-portal.md)

## <a name="run-a-package-in-the-azure-portal"></a>Een pakket uitvoeren in de Azure-portal
In deze sectie gebruikt u de gebruikersinterface (UI) of de app Data Factory om een Data Factory-pijplijn te maken met een SSIS-pakketactiviteit uitvoeren waarmee uw SSIS-pakket wordt uitgevoerd.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijplijn maken met een activiteit SSIS-pakket uitvoeren
In deze stap gebruikt u de gebruikersinterface of app Gegevensfabriek om een pijplijn te maken. U voegt een Activiteit SSIS-pakket uitvoeren toe aan de pijplijn en configureert deze om uw SSIS-pakket uit te voeren. 

1. Selecteer op uw overzicht van de gegevensfabriek of op de startpagina in de Azure-portal de tegel **Auteur & monitor** om de gebruikersinterface of app gegevensfabriek te starten op een afzonderlijk tabblad. 

   ![Startpagina gegevensfabriek](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**. 

   ![Pagina Aan de slag](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Vouw in de **gereedschapsset Activiteiten** **Algemeen**uit. Sleep vervolgens een activiteit **SSIS-pakket uitvoeren** naar het oppervlak van de pijplijnontwerper. 

   ![Een activiteit ssis-pakket uitvoeren naar het ontwerpoppervlak slepen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Geef op het tabblad **Algemeen** voor de activiteit SSIS-pakket uitvoeren een naam en beschrijving voor de activiteit op. Stel optionele **time-out-** en **probeerwaarden** in.

   ![Eigenschappen instellen op het tabblad Algemeen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Selecteer op het tabblad **Instellingen** voor de activiteit SSIS-pakket uitvoeren een Azure-SSIS IR waar u uw pakket wilt uitvoeren. Als uw pakket Windows-verificatie gebruikt om toegang te krijgen tot gegevensopslag (bijvoorbeeld SQL-servers of bestandsshares on-premises of Azure Files), schakelt u het selectievakje **Windows-verificatie** in. Voer de waarden in voor de invoerreferenties van uw pakket in de vakken **Domein,** **Gebruikersnaam**en **Wachtwoord.** 

    U ook geheimen gebruiken die zijn opgeslagen in uw Azure-sleutelkluis als hun waarden. Schakel hiervoor het selectievakje **AZURE KEY VAULT** in naast de relevante referenties. Selecteer of bewerk uw bestaande key vault linked service of maak een nieuwe. Selecteer vervolgens de geheime naam of versie voor uw referentiewaarde.

    Wanneer u uw key vault linked service maakt of bewerkt, u uw bestaande sleutelkluis selecteren of bewerken of een nieuwe vault maken. Zorg ervoor dat Data Factory beheerde identiteitstoegang verleent tot uw sleutelkluis als u dit nog niet hebt gedaan. U uw geheimen ook rechtstreeks `<Key vault linked service name>/<secret name>/<secret version>`invoeren in de volgende indeling:. Als uw pakket 32-bits runtime nodig heeft om uit te voeren, schakelt u het selectievakje **32-bits runtime** in.

   Selecteer **voor pakketlocatie** **SSISDB**, **Bestandssysteem (pakket),** **bestandssysteem (project)** of **ingesloten pakket**. Als u **SSISDB** selecteert als uw pakketlocatie, die automatisch wordt geselecteerd als uw Azure-SSIS IR is ingericht met de SSIS-catalogus (SSISDB) die wordt gehost door een Azure SQL Database-server of beheerde instantie, geeft u uw pakket op dat is geïmplementeerd in SSISDB. 

    Als uw Azure-SSIS IR wordt uitgevoerd en het selectievakje **Handmatige vermeldingen** is gewist, bladert u door uw bestaande mappen, projecten, pakketten of omgevingen van SSISDB en selecteert u deze. Selecteer **Vernieuwen** om uw nieuw toegevoegde mappen, projecten, pakketten of omgevingen op te halen bij SSISDB, zodat ze beschikbaar zijn voor browsen en selectie. Als u wilt bladeren of de omgevingen voor uw pakketuitvoeringen wilt selecteren, moet u uw projecten vooraf configureren om deze omgevingen toe te voegen als verwijzingen uit dezelfde mappen onder SSISDB. Zie [SSIS-omgevingen maken en toewijzen](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)voor meer informatie.

   Selecteer **voor logboekregistratieniveau**een vooraf gedefinieerd bereik van logboekregistratie voor de uitvoering van uw pakket. Schakel het selectievakje **Aangepast** in als u in plaats daarvan de aangepaste logboekregistratienaam wilt invoeren. 

   ![Eigenschappen instellen op het tabblad Instellingen - Automatisch](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Als uw Azure-SSIS IR niet wordt uitgevoerd of als het selectievakje **Handmatige vermeldingen** is ingeschakeld, voert u `<folder name>/<project name>/<package name>.dtsx` `<folder name>/<environment name>`uw pakket- en omgevingspaden van SSISDB rechtstreeks in de volgende indelingen in: en .

   ![Eigenschappen instellen op het tabblad Instellingen - Handmatig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Als u **Bestandssysteem (pakket)** selecteert als uw pakketlocatie, die automatisch wordt geselecteerd als uw Azure-SSIS IR is ingericht zonder SSISDB, geeft`.dtsx`u uw pakket op dat moet worden uitgevoerd door een UNC-pad (Universal Naming Convention) op te geven in het **pakketpadvak.** Als u uw pakket bijvoorbeeld opslaat in Azure `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`Files, is het pakketpad . 
   
   Als u uw pakket in een afzonderlijk bestand configureert, moet`.dtsConfig`u ook een UNC-pad naar uw configuratiebestand () in het vak **Configuratiepad** () verstrekken. Als u bijvoorbeeld uw configuratie opslaat in Azure `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`Files, is het configuratiepad .

   ![Eigenschappen instellen op het tabblad Instellingen - Handmatig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Als u **Bestandssysteem (Project)** als locatie van uw pakket selecteert, geeft u`.ispac`het pakket op dat moet worden`.dtsx`uitgevoerd door een UNC-pad op te geven voor uw projectbestand ( ) in het vak **Projectpad** en een pakketbestand ( ) vanuit uw project in het vak **Pakketnaam.** Als u uw project bijvoorbeeld opslaat in Azure `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`Files, is het projectpad .

   ![Eigenschappen instellen op het tabblad Instellingen - Handmatig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Geef vervolgens de referenties op om toegang te krijgen tot uw project-, pakket- of configuratiebestanden. Als u eerder de waarden voor de uitvoeringreferenties van uw pakket hebt ingevoerd (zie vorige), u deze opnieuw gebruiken door het selectievakje **Hetzelfde als pakketuitvoeringsreferenties** in te schakelen. Voer anders de waarden in voor de vakken **Domeintoegangsgegevens**voor uw pakket, **Gebruikersnaam**en **Wachtwoord.** Als u bijvoorbeeld uw project, pakket of configuratie opslaat `Azure`in Azure-bestanden, is het domein de gebruikersnaam `<storage account name>`en is `<storage account key>`het wachtwoord . 

   U ook geheimen gebruiken die in uw sleutelkluis zijn opgeslagen als hun waarden (zie vorige). Deze referenties worden gebruikt om toegang te krijgen tot uw pakket- en onderliggende pakketten in Pakkettaak uitvoeren, allemaal vanuit hun eigen pad of hetzelfde project, evenals configuraties, die zijn opgegeven in uw pakketten. 

   Als u **Ingesloten pakket** als locatie van uw pakket selecteert, sleept u uw pakket naar uitvoeren of **Uploadt u** het pakket vanuit een bestandsmap naar het meegeleverde vak. Uw pakket wordt automatisch gecomprimeerd en ingebed in de activiteit payload. Eenmaal ingesloten, u uw pakket later **downloaden** voor bewerking. U uw ingesloten pakket ook **parameteriseren** door het toe te wijst aan een pijplijnparameter die in meerdere activiteiten kan worden gebruikt, waardoor de grootte van uw pijplijnpayload wordt geoptimaliseerd. Als uw ingesloten pakket niet allemaal versleuteld is en we het gebruik van Pakkettaak uitvoeren erin detecteren, wordt het selectievakje **Pakkettaak uitvoeren** automatisch ingeschakeld en worden de relevante onderliggende pakketten met hun bestandssysteemreferenties automatisch toegevoegd om ze ook in te sluiten. Als we het gebruik van Taken uitvoeren niet kunnen detecteren, moet u handmatig het selectievakje **Pakkettaak uitvoeren** handmatig inschakelen en de relevante onderliggende pakketten toevoegen met hun bestandssysteemverwijzingen één voor één, om ze ook in te sluiten. Als de onderliggende pakketten SQL Server-verwijzingen gebruiken, moet u ervoor zorgen dat de SQL Server toegankelijk is voor uw Azure-SSIS IR.  Het gebruik van projectverwijzingen voor onderliggende pakketten wordt momenteel niet ondersteund.
   
   ![Eigenschappen instellen op het tabblad Instellingen - Handmatig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Als u het beveiligingsniveau **EncryptAllWithPassword** of **EncryptSensitiveWithPassword** hebt gebruikt wanneer u uw pakket hebt gemaakt via SQL Server Data Tools, voert u de waarde voor uw wachtwoord in het vak **Versleutelingswachtwoord** in. U ook een geheim gebruiken dat is opgeslagen in uw sleutelkluis als de waarde ervan (zie vorige). Als u het beveiligingsniveau **EncryptSensitiveWithUserKey** hebt gebruikt, voert u uw gevoelige waarden opnieuw in configuratiebestanden of op de tabbladen **SSIS-parameters,** **Verbindingsbeheer**of **Eigenschappenoverschrijvingen** (zie later). 

   Als u het **EncryptAllWithUserKey-beveiligingsniveau** hebt gebruikt, wordt dit niet ondersteund. U moet uw pakket opnieuw configureren om een ander beveiligingsniveau te gebruiken via SQL Server Data Tools of het `dtutil` command-line hulpprogramma. 
   
   Selecteer **voor logboekregistratieniveau**een vooraf gedefinieerd bereik van logboekregistratie voor de uitvoering van uw pakket. Schakel het selectievakje **Aangepast** in als u in plaats daarvan de aangepaste logboekregistratienaam wilt invoeren. Als u uw pakketuitvoeringen verder wilt registreren dan de standaardlogboekproviders die in uw pakket kunnen worden opgegeven, geeft u uw logboekmap op door het UNC-pad op te geven in het vak **Logboekpad.** Als u bijvoorbeeld uw logboeken opslaat in `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`Azure Files, is uw logboekpad . Op dit pad wordt een submap gemaakt voor elke afzonderlijke pakketuitvoering en vernoemd naar de run-ID van het SSIS-pakket uitvoeren, waarin logboekbestanden elke vijf minuten worden gegenereerd. 
   
   Geef ten slotte de referenties op om toegang te krijgen tot uw logboekmap. Als u eerder de waarden voor de referenties voor pakkettoegangsinvoer hebt ingevoerd (zie vorige), u deze opnieuw gebruiken door het selectievakje **Hetzelfde als pakkettoegangsreferenties in** te schakelen. Voer anders de waarden in voor de vakken Toegang tot logboekregistratie in de vakken **Domein,** **Gebruikersnaam**en **Wachtwoord.** Als u bijvoorbeeld uw logboeken opslaat in `Azure`Azure Files, is het domein , de gebruikersnaam is `<storage account name>`en het wachtwoord is `<storage account key>`. 

    U ook geheimen gebruiken die in uw sleutelkluis zijn opgeslagen als hun waarden (zie vorige). Deze referenties worden gebruikt om uw logboeken op te slaan. 
   
   Voor alle eerder genoemde UNC-paden moet de volledig gekwalificeerde bestandsnaam minder dan 260 tekens bevatten. De naam van de map moet minder dan 248 tekens bevatten.

1. Op het tabblad **SSIS-parameters** voor de activiteit SSIS-pakket uitvoeren wordt, als uw Azure-SSIS IR wordt uitgevoerd, **SSISDB** als locatie voor uw pakket geselecteerd en wordt het selectievakje **Handmatige vermeldingen** op het tabblad **Instellingen** gewist, worden de bestaande SSIS-parameters in uw geselecteerde project of pakket van SSISDB weergegeven om u aan hen toe te wijzen. Anders u ze één voor één invoeren om er handmatig waarden aan toe te wijzen. Zorg ervoor dat ze bestaan en correct zijn ingevoerd voor uw pakket uitvoering te slagen. 
   
   Als u het **encryptsensitiveWithUserKey-beveiligingsniveau** hebt gebruikt wanneer u uw pakket hebt gemaakt via SQL Server Data Tools en **Bestandssysteem (Project)** of **Bestandssysteem (Project)** is geselecteerd als uw pakketlocatie, moet u ook uw gevoelige parameters opnieuw invoeren om waarden toe te wijzen aan hen in configuratiebestanden of op dit tabblad. 
   
   Wanneer u waarden aan uw parameters toewijst, u dynamische inhoud toevoegen met behulp van expressies, functies, gegevensfabriekssysteemvariabelen en pijplijnparameters of variabelen voor gegevensfabrieken. U ook geheimen gebruiken die in uw sleutelkluis zijn opgeslagen als hun waarden (zie vorige).

   ![Eigenschappen instellen op het tabblad SSIS-parameters](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Op het tabblad **Verbindingsbeheer** voor de activiteit SSIS-pakket uitvoeren wordt, als uw Azure-SSIS IR wordt uitgevoerd, **SSISDB** als locatie voor uw pakket geselecteerd en wordt het selectievakje **Handmatige vermeldingen** op het tabblad **Instellingen** gewist, worden de bestaande verbindingsmanagers in uw geselecteerde project of pakket van SSISDB weergegeven waarmee u waarden aan hun eigenschappen toewijzen. Anders u ze één voor één invoeren om waarden handmatig aan hun eigenschappen toe te wijzen. Zorg ervoor dat ze bestaan en correct zijn ingevoerd voor uw pakket uitvoering te slagen. 
   
   Als u het **encryptsensitiveWithUserKey-beveiligingsniveau** hebt gebruikt wanneer u uw pakket hebt gemaakt via SQL Server Data Tools en **Bestandssysteem (Project)** of **Bestandssysteem (Project)** is geselecteerd als uw pakketlocatie, moet u ook uw gevoelige eigenschappen van verbindingsbeheer opnieuw invoeren om waarden aan hen toe te wijzen in configuratiebestanden of op dit tabblad. 
   
   Wanneer u waarden toewijst aan de eigenschappen van uw verbindingsbeheerder, u dynamische inhoud toevoegen met behulp van expressies, functies, gegevensfabriekssysteemvariabelen en pijplijnparameters of variabelen voor gegevensfabrieken. U ook geheimen gebruiken die in uw sleutelkluis zijn opgeslagen als hun waarden (zie vorige).

   ![Eigenschappen instellen op het tabblad Verbindingsbeheer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Voer op het tabblad **Eigenschappenoverschrijvingen** voor de activiteit SSIS-pakket uitvoeren één voor één de paden van bestaande eigenschappen in het geselecteerde pakket in om waarden handmatig toe te wijzen. Zorg ervoor dat ze bestaan en correct zijn ingevoerd voor uw pakket uitvoering te slagen. Als u bijvoorbeeld de waarde van uw gebruikersvariabele wilt overschrijven, voert u het pad in de volgende indeling in: `\Package.Variables[User::<variable name>].Value`. 
   
   Als u het **encryptsensitiveWithUserKey-beveiligingsniveau** hebt gebruikt wanneer u uw pakket hebt gemaakt via SQL Server Data Tools en **Bestandssysteem (Project)** of **Bestandssysteem (Project)** is geselecteerd als uw pakketlocatie, moet u ook uw gevoelige eigenschappen opnieuw invoeren om waarden toe te wijzen aan hen in configuratiebestanden of op dit tabblad. 
   
   Wanneer u waarden aan uw eigenschappen toewijst, u dynamische inhoud toevoegen met behulp van expressies, functies, gegevensfabriekssysteemvariabelen en pijplijnparameters of variabelen voor gegevensfabrieken.

   ![Eigenschappen instellen op het tabblad Overschrijven van eigenschappen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   De waarden die zijn toegewezen in configuratiebestanden en op het tabblad **SSIS-parameters** kunnen worden overschreven met de tabbladen **Verbindingsbeheer** of **Eigenschapoverschrijft.** De waarden die op het tabblad **Verbindingsbeheer** zijn toegewezen, kunnen ook worden overschreven met het tabblad **Eigenschappenoverschrijven.**

1. Als u de pijplijnconfiguratie wilt valideren, selecteert **u Valideren** op de werkbalk. Als u het **pijplijnvalidatierapport wilt**sluiten, selecteert u **>>**.

1. Als u de pijplijn wilt publiceren naar Gegevensfabriek, selecteert u **Alles publiceren**. 

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren
In deze stap activeert u een pijplijnrun. 

1. Als u een pijplijnrun wilt activeren, selecteert u **Trigger** op de werkbalk en selecteert u **Nu activeren**. 

   ![Nu activeren](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de pijplijnrun en de status ervan samen met andere informatie, zoals de **begintijd uitvoeren.** Selecteer **Vernieuwen** om de weergave te vernieuwen.

   ![Pijplijnuitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecteer de koppeling **Uitvoeringen van activiteit weergeven** in de kolom **Acties**. U ziet slechts één activiteit worden uitgevoerd omdat de pijplijn slechts één activiteit heeft. Het is de activiteit SSIS-pakket uitvoeren.

   ![Uitvoering van activiteiten](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Voer de volgende query uit tegen de SSISDB-database in uw SQL-server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

   ![Pakketuitvoeringen verifiëren](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. U de SSISDB-uitvoerings-ID ook ophalen bij de uitvoer van de pijplijnactiviteit uitvoeren en de ID gebruiken om uitgebreidere uitvoeringslogboeken en foutberichten in SQL Server Management Studio te controleren.

   ![Haal de executie-id.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>De pijplijn plannen met een trigger

U ook een geplande trigger voor uw pijplijn maken, zodat de pijplijn volgens een planning wordt uitgevoerd, zoals per uur of per dag. Zie Een gegevensfabriek maken voor een voorbeeld [: Gegevensfabriek UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Een pakket uitvoeren met PowerShell
In deze sectie gebruikt u Azure PowerShell om een Data Factory-pijplijn te maken met een SSIS-pakketactiviteit uitvoeren waarmee uw SSIS-pakket wordt uitgevoerd. 

Installeer de nieuwste Azure PowerShell-modules door de stapsgewijze instructies te volgen in [Azure PowerShell installeren en configureren.](/powershell/azure/install-az-ps)

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Een gegevensfabriek maken met Azure-SSIS IR
U een bestaande gegevensfabriek gebruiken die al azure-SSIS IR heeft ingericht of een nieuwe gegevensfabriek maken met Azure-SSIS IR. Volg de stapsgewijze instructies in de [zelfstudie: SSIS-pakketten implementeren in Azure via PowerShell.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijplijn maken met een activiteit SSIS-pakket uitvoeren 
In deze stap maakt u een pijplijn met een activiteit SSIS-pakket uitvoeren. De activiteit voert uw SSIS-pakket uit. 

1. Maak een JSON-bestand met de naam *RunSSISPackagePipeline.json* in de map *C:\ADF\RunSSISPackage* met inhoud die lijkt op het volgende voorbeeld.

   > [!IMPORTANT]
   > Vervang objectnamen, beschrijvingen en paden, eigenschaps- of parameterwaarden, wachtwoorden en andere variabele waarden voordat u het bestand opslaat. 
    
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

   Als u pakketten wilt uitvoeren die zijn opgeslagen in bestandssystemen, bestandsshares of Azure-bestanden, voert u als volgt de waarden voor uw pakket- en logboeklocatie-eigenschappen uit:

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

   Als u pakketten wilt uitvoeren in projecten die zijn opgeslagen in bestandssystemen, bestandsshares of Azure-bestanden, voert u als volgt de waarden voor de eigenschap pakketlocatie in:

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

   Als u ingesloten pakketten wilt uitvoeren, voert u de waarden voor de eigenschap pakketlocatie als volgt in:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. Schakel in Azure PowerShell over naar de map *C:\ADF\RunSSISPackage.*

3. Als u de pijplijn **RunSSISPackagePipeline wilt maken,** voert u de cmdlet **Set-AzDataFactoryV2Pipeline** uit.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Hier is de voorbeelduitvoer:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren
Gebruik de cmdlet **Invoke-AzDataFactoryV2Pipeline** om de pijplijn uit te voeren. De cmdlet retourneert de id voor de pijplijnuitvoering voor toekomstige controle.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer of plak het volgende script in het PowerShell-venster en selecteer Enter. 

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

U de pijplijn ook controleren met behulp van de Azure-portal. Zie [De pijplijn controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)voor stapsgewijze instructies.

### <a name="schedule-the-pipeline-with-a-trigger"></a>De pijplijn plannen met een trigger
In de vorige stap hebt u de pijplijn op aanvraag uitgevoerd. U ook een planningstrigger maken om de pijplijn volgens een planning uit te voeren, zoals per uur of per dag.

1. Maak een JSON-bestand met de naam *MyTrigger.json* in de map *C:\ADF\RunSSISPackage* met de volgende inhoud: 
        
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
    
1. Schakel in Azure PowerShell over naar de map *C:\ADF\RunSSISPackage.*
1. Voer de **cmdlet Set-AzDataFactoryV2Trigger** uit, waarmee de trigger wordt geactiveerd. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Standaard is de trigger in de gestopte status. Start de trigger door de **cmdlet Start-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Controleer of de trigger is gestart door de **cmdlet Get-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Voer de volgende opdracht na het volgende uur uit. Als de huidige tijd bijvoorbeeld 15:25 UTC is, voert u de opdracht uit op UTC van 16.00 uur. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Voer de volgende query uit tegen de SSISDB-database in uw SQL-server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende blogpost:
- [Uw ETL/ELT-werkstromen moderniseren en uitbreiden met SSIS-activiteiten in Azure Data Factory-pijplijnen](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
