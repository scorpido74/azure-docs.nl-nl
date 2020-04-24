---
title: Statussen voor taken en knoop punten tellen
description: Tel de status van Azure Batch taken en reken knooppunten op om batch-oplossingen te beheren en te bewaken.
ms.date: 09/07/2018
ms.topic: article
ms.custom: seodec18
ms.openlocfilehash: 1abff4c1a07ba0c5375228995330646204d33cd7
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116091"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Batch-oplossingen bewaken door taken en knoop punten te tellen per status

Als u grootschalige Azure Batch oplossingen wilt bewaken en beheren, hebt u nauw keurige aantallen resources in verschillende staten nodig. Azure Batch biedt efficiënte bewerkingen voor het ophalen van deze aantallen voor batch *taken* en *reken knooppunten*. Gebruik deze bewerkingen in plaats van mogelijk tijdrovende lijst query's die gedetailleerde informatie over grote verzamelingen taken of knoop punten retour neren.

* [Taak aantallen ophalen][rest_get_task_counts] haalt een totaal aantal actieve, uitgevoerde en voltooide taken in een taak op en van taken die zijn geslaagd of mislukt. 

  Door taken in elke status te tellen, kunt u de taak voortgang gemakkelijker weer geven voor een gebruiker of onverwachte vertragingen of fouten detecteren die van invloed kunnen zijn op de taak. Taak aantallen ophalen is beschikbaar vanaf de API-versie 2017 van de batch-service, -06-01.5.1 en gerelateerde Sdk's en hulpprogram ma's.

* Aantallen van het aantal [weer geven][rest_get_node_counts] van de lijst wordt opgehaald uit een van de bestemde en lage prioriteits knooppunten in elke groep die zich in verschillende statussen bevinden: maken, inactief, offline, afgebroken, opnieuw opstarten, opnieuw maken van installatie kopieën, starten en andere. 

  Door knoop punten te tellen in elke status kunt u bepalen wanneer u beschikt over de juiste reken resources om uw taken uit te voeren en mogelijke problemen met uw Pools te identificeren. Het aantal verzamelingen van de lijst is beschikbaar vanaf de API-versie van de batch-service 2018 -03-01.6.1 en gerelateerde Sdk's en hulpprogram ma's.

Als u een versie van de service gebruikt die geen ondersteuning biedt voor het aantal taken of bewerkingen in het aantal knoop punten, kunt u in plaats daarvan een lijst query gebruiken om deze resources te tellen. Gebruik een lijst query ook om informatie te krijgen over andere batch-resources, zoals toepassingen, bestanden en taken. Zie [Query's maken om batch resources efficiënt weer te geven](batch-efficient-list-queries.md)voor meer informatie over het Toep assen van filters op lijst query's.

## <a name="task-state-counts"></a>Aantal taak status

De bewerking taak aantal ophalen telt taken met de volgende statussen:

- **Actief** : een taak die in de wachtrij is geplaatst en kan worden uitgevoerd, maar die momenteel niet is toegewezen aan een reken knooppunt. Een taak is ook `active` als deze [afhankelijk is van een bovenliggende taak](batch-task-dependencies.md) die nog niet is voltooid. 
- **Uitvoeren** : een taak die is toegewezen aan een reken knooppunt, maar nog niet is voltooid. Een taak wordt meegeteld als `running` de status `preparing` of `running`is, zoals wordt aangegeven door de bewerking [informatie over een taak ophalen][rest_get_task] .
- **Voltooid** : een taak die niet langer in aanmerking komt voor uitvoering, omdat deze is voltooid, of als deze is voltooid en de limiet voor nieuwe pogingen is bereikt. 
- **Geslaagd** : een taak waarvan het resultaat van de uitvoering `success`van de taak is. Batch bepaalt of een taak is geslaagd of mislukt door de `TaskExecutionResult` eigenschap van de eigenschap [executionInfo][rest_get_exec_info] te controleren.
- **Is mislukt** Een taak waarvan het resultaat van de uitvoering `failure`van de taak is.

In het volgende voor beeld van de .NET-code ziet u hoe u taak aantallen kunt ophalen per status: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

U kunt een vergelijkbaar patroon voor REST en andere ondersteunde talen gebruiken om taak aantallen voor een taak te ontvangen. 

> [!NOTE]
> De API-versies van de batch-service vóór 2018 -08 `validationStatus` -01.7.0 retour neren ook een eigenschap in het antwoord taak aantallen ophalen. Deze eigenschap geeft aan of batch de status aantallen heeft gecontroleerd op consistentie met de statussen die zijn gerapporteerd in de API lijst taken. Een waarde van `validated` geeft alleen aan dat de batch ten minste één keer voor de taak is gecontroleerd op consistentie. De waarde van de `validationStatus` eigenschap geeft niet aan of het aantal dat taak tellingen ophalen, op dit moment up-to-date is.
>

## <a name="node-state-counts"></a>Aantal knooppunt statussen

De bewerking aantal knoop punten van een lijst groep telt het aantal reken knooppunten op basis van de volgende statussen in elke groep. Er worden afzonderlijke statistische aantallen gegeven voor toegewezen knoop punten en knoop punten met lage prioriteit in elke groep.

- **Maken** : een door Azure toegewezen virtuele machine die nog niet is gestart om lid te worden van een groep.
- **Inactief** : er is een beschik bare Compute-knoop punt dat momenteel geen taak wordt uitgevoerd.
- **LeavingPool** : een knoop punt dat de pool verlaat, hetzij omdat de gebruiker deze expliciet verwijdert of omdat het formaat van de groep wordt gewijzigd of automatisch wordt geschaald.
- **Offline** : een knoop punt dat batch niet kan gebruiken om nieuwe taken te plannen.
- **Afgebroken** : een knoop punt met lage prioriteit dat uit de groep is verwijderd omdat Azure de virtuele machine heeft teruggewonnen. Een `preempted` knoop punt kan opnieuw worden geïnitialiseerd als er een VM-capaciteit met lage prioriteit beschikbaar is.
- **Opnieuw opstarten** : een knoop punt dat opnieuw wordt opgestart.
- **Reimaging** : een knoop punt waarop het besturings systeem opnieuw wordt geïnstalleerd.
- **Uitvoeren** : een knoop punt waarop een of meer taken worden uitgevoerd (anders dan de begin taak).
- **Starten** : een knoop punt waarop de batch-service wordt gestart. 
- **StartTaskFailed** : een knoop punt waarop de [begin taak][rest_start_task] is mislukt en alle pogingen zijn uitgeput en op de begin `waitForSuccess` taak is ingesteld. Het knoop punt is niet bruikbaar voor het uitvoeren van taken.
- **Onbekend** : een knoop punt dat contact heeft gemaakt met de batch-service en waarvan de status niet bekend is.
- **Onbruikbaar** : een knoop punt dat niet kan worden gebruikt voor het uitvoeren van taken vanwege fouten.
- **WaitingForStartTask** : een knoop punt waarop de begin taak wordt uitgevoerd, maar `waitForSuccess` is ingesteld en de begin taak is niet voltooid.

Het volgende C#-code fragment laat zien hoe u het aantal knoop punten in het huidige account kunt weer geven voor alle Pools:

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
Het volgende C#-code fragment laat zien hoe u het aantal knoop punten voor een bepaalde groep in het huidige account kunt weer geven.

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
U kunt een vergelijkbaar patroon voor REST en andere ondersteunde talen gebruiken om het aantal knoop punten voor Pools te verkrijgen.
 
## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van Batch-functies](batch-api-basics.md) voor meer informatie over de concepten en functies van de Batch-service. In het artikel worden de primaire batch-resources zoals Pools, reken knooppunten, Jobs en taken beschreven en wordt een overzicht gegeven van de functies van de service.

* Zie [Query's maken om batch-resources efficiënt weer te geven](batch-efficient-list-queries.md)voor informatie over het Toep assen van filters op query's met een lijst met batch-resources.


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

