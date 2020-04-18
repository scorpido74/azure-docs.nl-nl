---
title: SSIS-pakket uitvoeren met opgeslagen procedureactiviteit - Azure
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijplijn met behulp van de activiteit Opgeslagen procedure.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 7a935fa4c4e91cf8adcd6df467ac56eeecaf46c9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605942"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Een SSIS-pakket uitvoeren met de activiteit Opgeslagen procedure in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u een SSIS-pakket uitvoert in een Azure Data Factory-pijplijn met behulp van een activiteit Opgeslagen procedure. 

## <a name="prerequisites"></a>Vereisten

### <a name="azure-sql-database"></a>Azure SQL Database 
De walkthrough in dit artikel maakt gebruik van een Azure SQL-database die de SSIS-catalogus host. U ook een Azure SQL Database Managed Instance gebruiken.

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken
Maak een runtime voor Azure-SSIS-integratie als u die niet hebt door de stapsgewijze instructie in de [zelfstudie: SSIS-pakketten implementeren](tutorial-create-azure-ssis-runtime-portal.md)te volgen.

## <a name="data-factory-ui-azure-portal"></a>Gebruikersinterface voor gegevensfabriek (Azure-portal)
In deze sectie gebruikt u de gebruikersinterface van Data Factory om een Data Factory-pijplijn te maken met een opgeslagen procedureactiviteit die een SSIS-pakket aanroept.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
De eerste stap is het maken van een gegevensfabriek met behulp van de Azure-portal. 

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Navigeer naar de [Azure-portal](https://portal.azure.com). 
3. Klik op **Nieuw** in het linkermenu en klik vervolgens op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Voer op de blade **New data factory****ADFTutorialDataFactory** in bij **Name**. 
      
     ![Pagina Nieuwe gegevensfactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   De naam van de Azure-gegevensfabriek moet **wereldwijd uniek**zijn. Als het volgende foutbericht wordt weergegeven voor het naamveld, wijzigt u de naam van de data factory (bijvoorbeeld uwnaamADFTutorialDataFactory). Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
     ![Naam niet beschikbaar - fout](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
4. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **Nieuw maken**en voer de naam van een resourcegroep in.   
         
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
4. Selecteer **V2** als de **versie**.
5. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst ziet u alleen locaties die worden ondersteund in Data Factory. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich op andere locaties bevinden.
6. Selecteer **Vastmaken aan dashboard**.     
7. Klik **op Maken**.
8. Op het dashboard ziet u de volgende tegel met status: **Gegevensfabriek implementeren**. 

     ![tegel met de status 'gegevensfactory implementeren'](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Wanneer het maken is voltooid, ziet u de pagina **Data Factory** zoals in de afbeelding wordt weergegeven.
   
     ![Startpagina van de gegevensfactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klik op de tegel **Maken en controleren** om de gebruikersinterface (UI) van Azure Data Factory te openen op een afzonderlijk tabblad. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Een pijplijn maken met opgeslagen procedureactiviteit
In deze stap gebruikt u de gebruikersinterface van De Gegevensfabriek om een pijplijn te maken. U voegt een opgeslagen procedureactiviteit toe aan de pijplijn en configureert deze om het SSIS-pakket uit te voeren met behulp van de sp_executesql opgeslagen procedure. 

1. Klik op de pagina Aan de slag op **Pijplijn maken:** 

    ![Pagina Aan de slag](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Vouw in de **gereedschapsset Activiteiten** **algemene**en slepen-vervolgsetactiviteit **Opgeslagen procedure** uit naar het oppervlak van de pijplijnontwerper. 

    ![Opgeslagen procedureactiviteit slepen en neerzetten](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Schakel in het eigenschappenvenster voor de opgeslagen procedureactiviteit over naar het tabblad **SQL-account** en klik op **+ Nieuw**. U maakt een verbinding met de Azure SQL-database die de SSIS-catalogus (SSIDB-database) host. 
   
    ![Knop Nieuwe gekoppelde service](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit: 

    1. Selecteer **Azure SQL Database** voor **Type**.
    2. Selecteer de **standaardruntijd voor** Azure-integratie om verbinding `SSISDB` te maken met de Azure SQL-database die de database host.
    3. Selecteer de Azure SQL-database met de SSISDB-database voor het veld **Servernaam.**
    4. Selecteer **SSISDB** voor **databasenaam**.
    5. Voer **voor gebruikersnaam**de naam in van de gebruiker die toegang heeft tot de database.
    6. Voer **voor Wachtwoord**het wachtwoord van de gebruiker in. 
    7. Test de verbinding met de database door op de knop **Verbindingsknop Testen** te klikken.
    8. Sla de gekoppelde service op door op de knop **Opslaan te** klikken. 

        ![Een gekoppelde Azure SQL Database-service](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Schakel in het eigenschappenvenster over naar het tabblad **Opgeslagen procedure** op het tabblad **SQL-account** en ga de volgende stappen uitvoeren: 

    1. Selecteer **Bewerken**. 
    2. Voer voor het veld `sp_executesql`Naam van de procedure **opgeslagen** in. 
    3. Klik **+ Nieuw** in de sectie Opgeslagen **procedureparameters.** 
    4. Voer **stmt**voor **de naam** van de parameter in . 
    5. Voer **Tekenreeks**voor **het type** van de parameter in . 
    6. Voer voor **de waarde** van de parameter de volgende SQL-query in:

        Geef in de SQL-query de juiste waarden op voor de **folder_name,** **project_name**en **package_name** parameters. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Een gekoppelde Azure SQL Database-service](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Als u de pijplijnconfiguratie wilt valideren, klikt u op **Valideren** op de werkbalk. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

    ![Pijplijn valideren](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publiceer de pijplijn naar Gegevensfabriek door op De knop **Alles publiceren** te klikken. 

    ![Publiceren](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>De pijplijn uitvoeren en bewaken
In deze sectie activeert u een pijplijnrun en controleert u deze vervolgens. 

1. Als u een pijplijnrun wilt activeren, klikt u op **Trigger** op de werkbalk en klikt u nu op **Activeren**. 

    ![Nu activeren](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 
3. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de pijplijnrun en de status ervan samen met andere informatie (zoals Begintijd uitvoeren). Als u de lijst wilt vernieuwen, klikt u op **Vernieuwen**.

    ![Pijplijnuitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klik op de koppeling **Uitvoeringen van activiteiten weergeven** in de kolom **Acties**. U ziet slechts één activiteit worden uitgevoerd omdat de pijplijn slechts één activiteit heeft (opgeslagen procedureactiviteit).

    ![Uitvoering van activiteiten](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. U de volgende **query** uitvoeren tegen de SSISDB-database in uw Azure SQL-server om te controleren of het pakket is uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```

    ![Pakketuitvoeringen verifiëren](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> U ook een geplande trigger voor uw pijplijn maken, zodat de pijplijn volgens een planning wordt uitgevoerd (per uur, per dag, enz.). Zie Een gegevensfabriek maken voor een voorbeeld [: Gegevensfabriek UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze sectie gebruikt u Azure PowerShell om een Data Factory-pijplijn te maken met een opgeslagen procedureactiviteit die een SSIS-pakket aanroept. 

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
U dezelfde gegevensfabriek gebruiken met de Azure-SSIS IR of een aparte gegevensfabriek maken. De volgende procedure bevat stappen om een gegevensfabriek te maken. U maakt een pijplijn met een opgeslagen procedureactiviteit in deze gegevensfabriek. De opgeslagen procedureactiviteit voert een opgeslagen procedure uit in de SSISDB-database om uw SSIS-pakket uit te voeren. 

1. Definieer een variabele voor de naam van de resourcegroep die u later gaat gebruiken in PowerShell-opdrachten. Kopieer de tekst van de volgende opdracht naar PowerShell, geef tussen dubbele aanhalingstekens een naam op voor de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) en voer de opdracht uit. Bijvoorbeeld: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit.
2. Voer de volgende opdracht uit om de resourcegroep te maken: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit. 
3. Definieer een variabele voor de naam van de data factory. 

    > [!IMPORTANT]
    >  Werk de naam van de data factory zodanig bij dat deze uniek is. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Als u de gegevensfabriek wilt maken, voert u de volgende cmdlet **Set-AzDataFactoryV2** uit met de eigenschap Locatie en ResourceGroupName van de variabele $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rollen **Inzender** of **Eigenaar**, of moet dit een **beheerder** van het Azure-abonnement zijn.
* Voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio’s waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken
Maak een gekoppelde service om uw Azure SQL-database te koppelen die de SSIS-catalogus host met uw gegevensfabriek. Data Factory gebruikt informatie in deze gekoppelde service om verbinding te maken met de SSISDB-database en voert een opgeslagen procedure uit om een SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **AzureSqlDatabaseLinkedService.json** in **C:\ADF\RunSSISPackage-map** met de volgende inhoud: 

    > [!IMPORTANT]
    > Vervang &lt;&gt;servernaam,&gt; &lt;gebruikersnaam &lt;&gt; en wachtwoord door waarden van uw Azure SQL Database voordat u het bestand opslaat.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Schakel in **Azure PowerShell**over naar de map **C:\ADF\RunSSISPackage.**

3. Voer de cmdlet **Set-AzDataFactoryV2LinkedService** uit om de gekoppelde service te maken: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Een pijplijn maken met opgeslagen procedureactiviteit 
In deze stap maakt u een pijplijn met een opgeslagen procedureactiviteit. De activiteit roept de sp_executesql opgeslagen procedure in om uw SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **RunSSISPackagePipeline.json** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud:

    > [!IMPORTANT]
    > MapNAAM &lt;&gt;, &lt;&gt;PROJECTNAAM &lt;,&gt; PAKKETNAAM vervangen door namen van map, project en pakket in de SSIS-catalogus voordat u het bestand opslaat. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. De pijplijn maken: **RunSSISPackagePipeline**, Voert de cmdlet **Set-AzDataFactoryV2Pipeline** uit.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken
Gebruik de cmdlet **Invoke-AzDataFactoryV2Pipeline** om de pijplijn uit te voeren. De cmdlet retourneert de id voor de pijplijnuitvoering voor toekomstige controle.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer/plak het volgende script in het PowerShell-venster en druk op ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

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

### <a name="create-a-trigger"></a>Een trigger maken
In de vorige stap hebt u de pijplijn on-demand ingeschakeld. U ook een schematrigger maken om de pijplijn volgens een schema uit te voeren (per uur, per dag, enz.).

1. Maak een JSON-bestand met de naam **MyTrigger.json** in **C:\ADF\RunSSISPackage** map met de volgende inhoud: 

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
                }
            ]
        }
    }    
    ```
2. Schakel in **Azure PowerShell**over naar de map **C:\ADF\RunSSISPackage.**
3. Voer de **cmdlet Set-AzDataFactoryV2Trigger** uit, waarmee de trigger wordt geactiveerd. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Standaard is de trigger in de gestopte status. Start de trigger door de **cmdlet Start-AzDataFactoryV2Trigger** uit te voeren. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Controleer of de trigger is gestart door de **cmdlet Get-AzDataFactoryV2Trigger** uit te voeren. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Voer de volgende opdracht na het volgende uur uit. Als de huidige tijd bijvoorbeeld 15:25 UTC is, voert u de opdracht uit op UTC van 16.00 uur. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    U de volgende query uitvoeren tegen de SSISDB-database in uw Azure SQL-server om te controleren of het pakket is uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Volgende stappen
U de pijplijn ook controleren met behulp van de Azure-portal. Zie [De pijplijn controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)voor stapsgewijze instructies.
