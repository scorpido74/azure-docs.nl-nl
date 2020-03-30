---
title: Een groot aantal taken verzenden - Azure Batch | Microsoft Documenten
description: Een zeer groot aantal taken efficiënt indienen in één Azure Batch-taak
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: c3857e512da5fe4fceefa5f735ddc65f73e11623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026044"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Een groot aantal taken verzenden naar een batchtaak

Wanneer u grootschalige Azure Batch-workloads uitvoert, u tienduizenden, honderdduizenden of zelfs meer taken naar één taak verzenden. 

Dit artikel geeft richtlijnen en enkele codevoorbeelden om grote aantallen taken in te dienen met een aanzienlijk verhoogde doorvoer naar één batchtaak. Nadat taken zijn verzonden, worden de batchwachtrij ingevoerd voor verwerking in de groep die u voor de taak opgeeft.

## <a name="use-task-collections"></a>Taakverzamelingen gebruiken

De Batch API's bieden methoden om taken efficiënt toe te voegen aan een taak als *verzameling,* naast één tegelijk. Wanneer u een groot aantal taken toevoegt, moet u de juiste methoden of overbelasting gebruiken om taken toe te voegen als verzameling. Over het algemeen maakt u een taakverzameling door taken te definiëren terwijl u een reeks invoerbestanden of parameters voor uw taak overneemt.

De maximale grootte van de taakverzameling die u in één gesprek toevoegen, is afhankelijk van de Batch-API die u gebruikt:

* De volgende Batch-API's beperken de verzameling tot **100 taken**. De limiet kan kleiner zijn, afhankelijk van de grootte van de taken, bijvoorbeeld als de taken een groot aantal resourcebestanden of omgevingsvariabelen hebben.

    * [REST-API](/rest/api/batchservice/task/addcollection)
    * [Python-API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Wanneer u deze API's gebruikt, moet u logica bieden om het aantal taken te verdelen om aan de verzamellimiet te voldoen en fouten en nieuwe pogingen te verwerken in het geval de toevoeging van taken mislukt. Als een taakverzameling te groot is om toe te voegen, genereert de aanvraag een fout en moet deze opnieuw worden geprobeerd met minder taken.

* De volgende API's ondersteunen veel grotere taakverzamelingen - alleen beperkt door de beschikbaarheid van RAM op de verzendende client. Deze API's verwerken op transparante wijze het verdelen van de taakverzameling in 'segmenten' voor de API's en nieuwe vermeldingen op een lager niveau als de toevoeging van taken mislukt.

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java-API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Azure Batch CLI-extensie](batch-cli-templates.md) met Batch CLI-sjablonen
    * [Python SDK-extensie](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Doorvoer van taakindiening verhogen

Het kan enige tijd duren om een grote verzameling taken toe te voegen aan een taak - bijvoorbeeld tot 1 minuut om 20.000 taken toe te voegen via de .NET API. Afhankelijk van de Batch API en uw workload u de taakdoorvoer verbeteren door een of meer van de volgende opties te wijzigen:

* **Taakgrootte** - Het toevoegen van grote taken duurt langer dan het toevoegen van kleinere taken. Als u de grootte van elke taak in een verzameling wilt verkleinen, u de taakopdrachtregel vereenvoudigen, het aantal omgevingsvariabelen verminderen of de vereisten voor taakuitvoering efficiënter afhandelen. In plaats van bijvoorbeeld een groot aantal bronbestanden te gebruiken, installeert u taakafhankelijkheden met behulp van een [starttaak](batch-api-basics.md#start-task) op de groep of gebruikt u een [toepassingspakket](batch-application-packages.md) of [Docker-container.](batch-docker-container-workloads.md)

* **Aantal parallelle bewerkingen** - Afhankelijk van de Batch API verhoogt u de doorvoer door het maximum aantal gelijktijdige bewerkingen door de batchclient te verhogen. Configureer deze instelling met de eigenschap [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) `threads` in de .NET API of de parameter van methoden zoals [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) in de Batch Python SDK-extensie. (Deze eigenschap is niet beschikbaar in de native Batch Python SDK.) Standaard is deze eigenschap ingesteld op 1, maar stelt deze hoger in om de doorvoer van bewerkingen te verbeteren. U ruilt verhoogde doorvoer af door netwerkbandbreedte en cpu-prestaties te verbruiken. Taakdoorvoer neemt toe met maximaal `MaxDegreeOfParallelism` 100 keer de of `threads`. In de praktijk moet u het aantal gelijktijdige bewerkingen onder de 100 instellen. 
 
  De Azure Batch CLI-extensie met batchsjablonen verhoogt het aantal gelijktijdige bewerkingen automatisch op basis van het aantal beschikbare kernen, maar deze eigenschap is niet configureerbaar in de CLI. 

* **HTTP-verbindingslimieten** - Het aantal gelijktijdige HTTP-verbindingen kan de prestaties van de Batch-client beperken wanneer deze grote aantallen taken toevoegt. Het aantal HTTP-verbindingen is beperkt met bepaalde API's. Bij het ontwikkelen met de .NET API is de eigenschap [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) standaard ingesteld op 2. We raden u aan de waarde te verhogen tot een getal dat dicht bij of groter is dan het aantal parallelle bewerkingen.

## <a name="example-batch-net"></a>Voorbeeld: Batch .NET

In de volgende C#-fragmenten worden instellingen weergegeven die u wilt configureren bij het toevoegen van een groot aantal taken met de Batch .NET API.

Als u de taakdoorvoer wilt verhogen, verhoogt u de waarde van de eigenschap [MaxDegreeOfParallelisme](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) van de [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Bijvoorbeeld:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Voeg een taakverzameling toe aan de taak met de juiste overbelasting van de methode [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) of [AddTask.](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) Bijvoorbeeld:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Voorbeeld: Batch CLI-extensie

Met de Azure Batch CLI-extensies met [Batch CLI-sjablonen](batch-cli-templates.md)maakt u een JSON-bestand voor taaksjablonen met een [taakfabriek](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). De taakfabriek configureert een verzameling gerelateerde taken voor een taak vanuit één taakdefinitie.  

Het volgende is een voorbeeldtaaksjabloon voor een eendimensionale parametrische veegtaak met een groot aantal taken - in dit geval 250.000. De opdrachtregel voor `echo` taken is een eenvoudige opdracht.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Zie [Azure Batch CLI-sjablonen en bestandsoverdracht gebruiken](batch-cli-templates.md)als u een taak met de sjabloon wilt uitvoeren.

## <a name="example-batch-python-sdk-extension"></a>Voorbeeld: Batch Python SDK-extensie

Als u de Azure Batch Python SDK-extensie wilt gebruiken, installeert u eerst de Python SDK en de extensie:

```
pip install azure-batch
pip install azure-batch-extensions
```

Een set `BatchExtensionsClient` instellen die de SDK-extensie gebruikt:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Maak een verzameling taken die aan een taak moet worden toegevoegd. Bijvoorbeeld:


```python
tasks = list()
# Populate the list with your tasks
...
```

Voeg de taakverzameling toe met [taak.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Stel `threads` de parameter in om het aantal gelijktijdige bewerkingen te verhogen:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

De Batch Python SDK-extensie ondersteunt ook het toevoegen van taakparameters aan taken met behulp van een JSON-specificatie voor een taakfabriek. Configureer bijvoorbeeld taakparameters voor een parametrische veegverbinding die vergelijkbaar is met die in het voorgaande sjabloonvoorbeeld batch CLI:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Voeg de taakparameters toe aan de taak. Stel `threads` de parameter in om het aantal gelijktijdige bewerkingen te verhogen:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van de Azure Batch CLI-extensie met [Batch CLI-sjablonen](batch-cli-templates.md).
* Meer informatie over de [Batch Python SDK-extensie](https://pypi.org/project/azure-batch-extensions/).
