---
title: SSIS-pakket aanroepen met Azure Data Factory - Activiteit opgeslagen procedure
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services-pakket (SSIS)-pakket aanroept vanuit een Azure Data Factory-pijplijn met behulp van de activiteit Opgeslagen procedure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: ea86c4670a8eb6dc5e2133ed01045e8aada0f707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438790"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Een SSIS-pakket aanroepen met opgeslagen procedureactiviteit in Azure Data Factory
In dit artikel wordt beschreven hoe u een SSIS-pakket uit een Azure Data Factory-pijplijn aanroepen met behulp van een opgeslagen procedureactiviteit. 

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [SSIS-pakketten aanroepen met opgeslagen procedureactiviteit in](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Vereisten

### <a name="azure-sql-database"></a>Azure SQL Database 
De walkthrough in dit artikel maakt gebruik van een Azure SQL-database die de SSIS-catalogus host. U ook een Azure SQL Database Managed Instance gebruiken.

### <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken
Maak een runtime voor Azure-SSIS-integratie als u die niet hebt door de stapsgewijze instructie in de [zelfstudie: SSIS-pakketten implementeren](../tutorial-create-azure-ssis-runtime-portal.md)te volgen. U Data Factory-versie 1 niet gebruiken om een runtime van Azure-SSIS-integratie te maken. 

## <a name="azure-powershell"></a>Azure PowerShell
In deze sectie gebruikt u Azure PowerShell om een Data Factory-pijplijn te maken met een opgeslagen procedureactiviteit die een SSIS-pakket aanroept.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
De volgende procedure bevat stappen om een gegevensfabriek te maken. U maakt een pijplijn met een opgeslagen procedureactiviteit in deze gegevensfabriek. De opgeslagen procedureactiviteit voert een opgeslagen procedure uit in de SSISDB-database om uw SSIS-pakket uit te voeren.

1. Definieer een variabele voor de naam van de resourcegroep die u later gaat gebruiken in PowerShell-opdrachten. Kopieer de tekst van de volgende opdracht naar PowerShell, geef tussen dubbele aanhalingstekens een naam op voor de [Azure-resourcegroep](../../azure-resource-manager/management/overview.md) en voer de opdracht uit. Bijvoorbeeld: `"adfrg"`. 
   
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

5. Als u de gegevensfabriek wilt maken, voert u de volgende cmdlet **Nieuw-AzDataFactory** uit met de eigenschap Locatie en ResourceGroupName van de variabele $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rollen **Inzender** of **Eigenaar**, of moet dit een **beheerder** van het Azure-abonnement zijn.

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken
Maak een gekoppelde service om uw Azure SQL-database te koppelen die de SSIS-catalogus host met uw gegevensfabriek. Data Factory gebruikt informatie in deze gekoppelde service om verbinding te maken met de SSISDB-database en voert een opgeslagen procedure uit om een SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **AzureSqlDatabaseLinkedService.json** in **C:\ADF\RunSSISPackage-map** met de volgende inhoud: 

    > [!IMPORTANT]
    > Vervang &lt;&gt;servernaam,&gt;@&lt;&gt; &lt;gebruikersnaamservernaam&gt; en wachtwoord door waarden van uw Azure SQL Database voordat u het bestand opslaat. &lt;

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
3. Voer de cmdlet **New-AzDataFactoryLinkedService** uit om de gekoppelde service te maken: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Een uitvoergegevensset maken
Deze uitvoergegevensset is een dummy-gegevensset die de planning van de pijplijn aanstuurt. Merk op dat de frequentie is ingesteld op Uur en interval is ingesteld op 1. Daarom wordt de pijplijn eenmaal per uur uitgevoerd binnen de begin- en eindtijden van de pijplijn. 

1. Maak een Bestand OutputDataset.json met de volgende inhoud: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Voer de cmdlet **Nieuw-AzDataFactoryDataset** uit om een gegevensset te maken. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Een pijplijn maken met opgeslagen procedureactiviteit 
In deze stap maakt u een pijplijn met een opgeslagen procedureactiviteit. De activiteit roept de sp_executesql opgeslagen procedure in om uw SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **MyPipeline.json** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud:

    > [!IMPORTANT]
    > Vervang &lt;de&gt; &lt;naam&gt;van &lt;de&gt; map, de projectnaam, de pakketnaam, de naam van de verpakking door namen van de map, het project en het pakket in de SSIS-catalogus voordat u het bestand opslaat.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Om de pijplijn te maken: **RunSSISPackagePipeline**, voert u de cmdlet **Nieuw-AzDataFactoryPipeline** uit.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Voer **Get-AzDataFactorySlice uit** om meer informatie te krijgen over alle segmenten van de uitvoergegevensset**, de uitvoertabel van de pijplijn.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    De StartDateTime die u hier opgeeft, is dezelfde begintijd die u hebt opgegeven in de JSON van de pijplijn. 
1. Voer **Get-AzDataFactoryRun** uit om de details van activiteitsuitvoeringen voor een specifiek segment te krijgen.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    U kunt deze cmdlet blijven uitvoeren tot u ziet dat het segment de status **Gereed** of **Mislukt** heeft gekregen. 

    U de volgende query uitvoeren tegen de SSISDB-database in uw Azure SQL-server om te controleren of het pakket is uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md) voor meer informatie over de opgeslagen procedureactiviteit.

