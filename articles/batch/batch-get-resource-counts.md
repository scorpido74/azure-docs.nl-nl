---
title: Statussen tellen voor taken en knooppunten - Azure Batch | Microsoft Documenten
description: Tel de status van Azure Batch-taken en rekenknooppunten om batchoplossingen te beheren en te bewaken.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a7b58e96918d26851812aa96c18043121c081e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023919"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Batch-oplossingen bewaken door taken en knooppunten op status te tellen

Als u grootschalige Azure Batch-oplossingen wilt bewaken en beheren, hebt u nauwkeurige tellingen van resources in verschillende statussen nodig. Azure Batch biedt efficiënte bewerkingen om deze tellingen voor *batchtaken* en *compute-knooppunten*op te halen. Gebruik deze bewerkingen in plaats van mogelijk tijdrovende lijstquery's die gedetailleerde informatie over grote verzamelingen taken of knooppunten retourneren.

* [Als u taaktellingen ophaalt,][rest_get_task_counts] wordt een geaggregeerd aantal actieve, actieve en voltooide taken in een taak en van taken die zijn geslaagd of mislukt. 

  Door taken in elke status te tellen, u de voortgang van de taak gemakkelijker weergeven aan een gebruiker of onverwachte vertragingen of fouten detecteren die van invloed kunnen zijn op de taak. Get Task Counts is beschikbaar vanaf Batch Service API versie 2017-06-01.5.1 en gerelateerde SDKs en tools.

* [Lijst pool knooppunt telt][rest_get_node_counts] krijgt het aantal speciale en lage prioriteit compute nodes in elke groep die zich in verschillende staten: maken, inactief, offline, vooruitlopen, rebooten, reimaging, starten, en anderen. 

  Door knooppunten in elke status te tellen, u bepalen wanneer u over voldoende rekenbronnen beschikt om uw taken uit te voeren en potentiële problemen met uw pools identificeren. Lijstpool knooppunttellingen is beschikbaar vanaf Batch Service API-versie 2018-03-01.6.1 en gerelateerde SDK's en -hulpprogramma's.

Als u een versie van de service gebruikt die geen ondersteuning biedt voor het aantal taken voor taaktelling of knooppunttelling, gebruikt u in plaats daarvan een lijstquery om deze resources te tellen. Gebruik ook een lijstquery om informatie te krijgen over andere Batchbronnen, zoals toepassingen, bestanden en taken. Zie [Query's maken om batchbronnen efficiënt weer te geven](batch-efficient-list-queries.md)voor meer informatie over het toepassen van filters op lijstquery's.

## <a name="task-state-counts"></a>Tellingen van taakstatus

De bewerking Taaktellingen opvoeren telt taken op de volgende statussen:

- **Actief** - een taak die in de wachtrij staat en kan worden uitgevoerd, maar momenteel niet is toegewezen aan een compute-knooppunt. Een taak `active` is ook afhankelijk [van een bovenliggende taak](batch-task-dependencies.md) die nog niet is voltooid. 
- **Uitvoeren** - Een taak die is toegewezen aan een compute-knooppunt, maar nog niet is voltooid. Een taak wordt `running` geteld als wanneer `preparing` `running`de status is of , zoals aangegeven door de [informatie over een taakbewerking opbrengen.][rest_get_task]
- **Voltooid** - Een taak die niet langer in aanmerking komt om uit te voeren, omdat deze is voltooid of zonder succes is voltooid en ook de limiet voor opnieuw proberen heeft uitgeput. 
- **Geslaagd** - Een taak waarvan het `success`resultaat van taakuitvoering is. Batch bepaalt of een taak is geslaagd `TaskExecutionResult` of mislukt door de eigenschap [executionInfo te][rest_get_exec_info] controleren.
- **Mislukt** Een taak waarvan het `failure`resultaat van taakuitvoering is.

In het volgende voorbeeld van .NET-code ziet u hoe u taaktellingen op de status ophalen: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

U een vergelijkbaar patroon voor REST en andere ondersteunde talen gebruiken om taaktellingen voor een taak te krijgen. 

> [!NOTE]
> Batch Service API-versies vóór 2018-08-01.7.0 retourneren ook een `validationStatus` eigenschap in het antwoord Taaktellingen ontvangen. Deze eigenschap geeft aan of Batch de statustellingen heeft gecontroleerd op consistentie met de statussen die worden gerapporteerd in de API Lijsttaken. Een waarde `validated` van geeft alleen aan dat Batch ten minste één keer op consistentie is gecontroleerd voor de taak. De waarde `validationStatus` van de eigenschap geeft niet aan of de tellingen die taaktellingen ophalen momenteel up-to-date zijn.
>

## <a name="node-state-counts"></a>Knooppuntstatustelt

De bewerking Aantal tellingen van lijstgroepknooppunten telt rekenknooppunten door de volgende statussen in elke groep. Er worden afzonderlijke geaggregeerde tellingen verstrekt voor speciale knooppunten en knooppunten met een lage prioriteit in elke groep.

- **Maken** - Een vm met Azure-toegewezen die nog niet is begonnen met het deelnemen aan een pool.
- **Niet actief** : een beschikbaar rekenknooppunt dat momenteel geen taak uitvoert.
- **LeavingPool** - Een knooppunt dat de groep verlaat, hetzij omdat de gebruiker het expliciet heeft verwijderd of omdat de groep het formaat of de automatische schaling wordt gewijzigd of automatisch wordt afschalen.
- **Offline** - Een knooppunt dat Batch niet kan gebruiken om nieuwe taken te plannen.
- **Voorkom -** Een knooppunt met lage prioriteit dat uit de groep is verwijderd omdat Azure de VM heeft teruggevorderd. Een `preempted` knooppunt kan opnieuw worden geinitialiseerd wanneer er een vm-capaciteit met lage prioriteit beschikbaar is.
- **Rebooten** - Een knooppunt dat opnieuw wordt opgestart.
- **Reimaging** - Een knooppunt waarop het besturingssysteem opnieuw wordt geïnstalleerd.
- **Uitvoeren** - Een knooppunt waarop een of meer taken worden uitgevoerd (andere dan de starttaak).
- **Start** - Een knooppunt waarop de batchservice wordt gestart. 
- **StartTaskFailed** - Een knooppunt waarop de [starttaak][rest_start_task] is mislukt en `waitForSuccess` alle nieuwe pogingen heeft uitgeput en waarop is ingesteld op de starttaak. Het knooppunt is niet bruikbaar voor het uitvoeren van taken.
- **Onbekend** - Een knooppunt dat het contact met de Batch-service heeft verloren en waarvan de staat niet bekend is.
- **Onbruikbaar** - Een knooppunt dat niet kan worden gebruikt voor taakuitvoering vanwege fouten.
- **WaitingForStartTask** - Een knooppunt waarop de starttaak `waitForSuccess` is gestart, maar is ingesteld en de starttaak is nog niet voltooid.

In het volgende C#-fragment ziet u hoe u knooppunttellingen weergeeft voor alle groepen in de lopende rekening:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
In het volgende C#-fragment ziet u hoe u knooppunttellingen voor een bepaalde groep in de lopende rekening weergeeft.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
U een vergelijkbaar patroon voor REST en andere ondersteunde talen gebruiken om knooppunttellingen voor groepen te krijgen.
 
## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van Batch-functies](batch-api-basics.md) voor meer informatie over de concepten en functies van de Batch-service. Het artikel bespreekt de primaire batchbronnen, zoals pools, rekenknooppunten, taken en taken, en biedt een overzicht van de functies van de service.

* Zie [Query's maken om batchbronnen efficiënt weer te geven](batch-efficient-list-queries.md)voor informatie over het toepassen van filters op query's met batchbronnen.


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

