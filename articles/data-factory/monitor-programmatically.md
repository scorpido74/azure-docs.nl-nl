---
title: Een Azure-gegevensfabriek programmatisch controleren
description: Meer informatie over het bewaken van een pijplijn in een gegevensfabriek met behulp van verschillende softwaredevelopmentkits (SDK's).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 43a31d588ff6616d7200d9773883ce5da570b100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927364"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Een Azure-gegevensfabriek programmatisch controleren
In dit artikel wordt beschreven hoe u een pijplijn in een gegevensfabriek controleren met behulp van verschillende softwaredevelopmentkits (SDK's). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Gegevensbereik

Data Factory slaat alleen pijplijngegevens gedurende 45 dagen op. Wanneer u programmatisch naar gegevens over datafactory-pijplijnuitvoeringen zoekt `Get-AzDataFactoryV2PipelineRun` - bijvoorbeeld met de opdracht `LastUpdatedAfter` `LastUpdatedBefore` PowerShell - zijn er geen maximumdatums voor de optionele en parameters. Maar als u bijvoorbeeld gegevens van het afgelopen jaar opvraagt, geeft de query geen fout weer, maar retourneert de pijplijnalleen gegevens van de afgelopen 45 dagen.

Als u gegevens wilt blijven uitvoeren van pijplijns gedurende meer dan 45 dagen, stelt u uw eigen diagnostische logboekregistratie in met [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Zie [Een gegevensfabriek en pijplijn maken met .NET](quickstart-create-data-factory-dot-net.md)voor een volledige walkthrough van het maken en bewaken van een pijplijn met .NET .

1. Voeg de volgende code toe om continu de status van de pijplijn te controleren totdat de gegevens zijn gekopieerd.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Voeg de volgende code toe aan die kopieactiviteit smeergegevens ophaalt, bijvoorbeeld de grootte van de gelezen/geschreven gegevens.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Zie [Data Factory .NET SDK-referentie](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet)voor volledige documentatie over .NET SDK.

## <a name="python"></a>Python
Zie [Een gegevensfabriek en pijplijn maken met Python](quickstart-create-data-factory-python.md)voor een volledige walkthrough van het maken en bewaken van een pijplijn met Python.

Als u de pijplijnrun wilt controleren, voegt u de volgende code toe:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Zie [Gegevensfabriek Python SDK-verwijzing](/python/api/overview/azure/datafactory?view=azure-python)voor volledige documentatie over Python SDK.

## <a name="rest-api"></a>REST API
Zie [Een gegevensfabriek en -pijplijn maken met REST API](quickstart-create-data-factory-rest-api.md)voor een volledige walkthrough van het maken en bewaken van een pijplijn met REST API.
 
1. Voer het volgende script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Voer het volgende script uit om uitvoeringsdetails van de kopieeractiviteit op te halen, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Zie Data Factory REST [API-referentie](/rest/api/datafactory/)voor volledige documentatie over REST API.

## <a name="powershell"></a>PowerShell
Zie [Een gegevensfabriek en -pijplijn maken met PowerShell](quickstart-create-data-factory-powershell.md)voor een volledige walkthrough van het maken en bewaken van een pijplijn met PowerShell.

1. Voer het volgende script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Voer het volgende script uit om uitvoeringsdetails van de kopieeractiviteit op te halen, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Zie [Gegevensfabriek PowerShell-cmdletreferentie](/powershell/module/az.datafactory)voor volledige documentatie over PowerShell-cmdlets.

## <a name="next-steps"></a>Volgende stappen
Zie [Pijplijnen controleren met behulp van het azure-monitor-artikel](monitor-using-azure-monitor.md) voor meer informatie over het gebruik van Azure Monitor om pijplijnen voor gegevensfabrieken te controleren. 

