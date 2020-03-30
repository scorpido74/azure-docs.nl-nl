---
title: Taken parallel uitvoeren om rekenresources te optimaliseren - Azure Batch
description: Verhoog de efficiëntie en verlaag de kosten door minder rekenknooppunten te gebruiken en gelijktijdige taken uit te voeren op elk knooppunt in een Azure Batch-groep
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465771cb97ef9d8d5c451a6bafc61c4621d3c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023630"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Taken gelijktijdig uitvoeren om het gebruik van batchcomputenodes te maximaliseren 

Door meer dan één taak tegelijk uit te voeren op elk compute-knooppunt in uw Azure Batch-groep, u het resourcegebruik maximaliseren op een kleiner aantal knooppunten in de groep. Voor sommige workloads kan dit leiden tot kortere werktijden en lagere kosten.

Hoewel sommige scenario's profiteren van het wijden van alle resources van een knooppunt aan één taak, profiteren verschillende situaties van het toestaan van meerdere taken om deze resources te delen:

* **Gegevensoverdracht minimaliseren** wanneer taken gegevens kunnen delen. In dit scenario u de kosten voor gegevensoverdracht drastisch verlagen door gedeelde gegevens naar een kleiner aantal knooppunten te kopiëren en taken parallel op elk knooppunt uit te voeren. Dit geldt met name als de gegevens die naar elk knooppunt moeten worden gekopieerd, moeten worden overgedragen tussen geografische regio's.
* **Het maximaliseren van het geheugengebruik** wanneer taken een grote hoeveelheid geheugen vereisen, maar alleen tijdens korte perioden en op variabele tijden tijdens de uitvoering. U minder, maar grotere rekenknooppunten met meer geheugen gebruiken om dergelijke pieken efficiënt te verwerken. Deze knooppunten zouden meerdere taken parallel op elk knooppunt hebben, maar elke taak zou profiteren van het overvloedige geheugen van de knooppunten op verschillende tijdstippen.
* **Beperkende nodenummerlimieten** wanneer communicatie tussen knooppunten vereist is binnen een pool. Momenteel zijn pools die zijn geconfigureerd voor internodecommunicatie beperkt tot 50 compute nodes. Als elk knooppunt in een dergelijke groep taken parallel kan uitvoeren, kan een groter aantal taken tegelijkertijd worden uitgevoerd.
* **Een on-premises compute cluster repliceren,** bijvoorbeeld wanneer u een compute-omgeving voor het eerst naar Azure verplaatst. Als uw huidige on-premises oplossing meerdere taken per compute-knooppunt uitvoert, u het maximum aantal knooppunttaken verhogen om die configuratie beter te weerspiegelen.

## <a name="example-scenario"></a>Voorbeeldscenario
Als voorbeeld om de voordelen van parallelle taakuitvoering te illustreren, laten we zeggen dat uw taaktoepassing CPU- en geheugenvereisten heeft, zodat [standaard\_D1-knooppunten](../cloud-services/cloud-services-sizes-specs.md) voldoende zijn. Maar om de taak in de vereiste tijd te voltooien, zijn 1.000 van deze knooppunten nodig.

In plaats\_van standaard D1-knooppunten te gebruiken met een CPU-kern van 1 CPU, u [standaard\_D14-knooppunten](../cloud-services/cloud-services-sizes-specs.md) gebruiken die elk 16 cores hebben en parallelle taakuitvoering inschakelen. Daarom kunnen *16 keer minder knooppunten* worden gebruikt - in plaats van 1.000 knooppunten, slechts 63 nodig zou zijn. Bovendien, als grote toepassingsbestanden of referentiegegevens nodig zijn voor elk knooppunt, worden de taakduur en de efficiëntie opnieuw verbeterd, omdat de gegevens worden gekopieerd naar slechts 63 knooppunten.

## <a name="enable-parallel-task-execution"></a>Parallelle taakuitvoering inschakelen
U configureert rekenknooppunten voor parallelle taakuitvoering op poolniveau. Stel met de batch .NET-bibliotheek de eigenschap [CloudPool.MaxTasksPerComputeNode][maxtasks_net] in wanneer u een groep maakt. Als u de Batch REST API gebruikt, stelt u het element [maxTasksPerNode][rest_addpool] in de aanvraaginstantie in tijdens het maken van de groep.

Met Azure Batch u taken per knooppunt instellen tot (4x) het aantal kernknooppunten. Als de groep bijvoorbeeld is geconfigureerd met knooppunten van grootte 'Groot' (vier kernen), kan deze `maxTasksPerNode` worden ingesteld op 16. Ongeacht het aantal cores dat het knooppunt heeft, u echter niet meer dan 256 taken per knooppunt hebben. Zie [Grootte voor Cloud Services voor](../cloud-services/cloud-services-sizes-specs.md)meer informatie over het aantal cores voor elk van de nodeformaten. Zie Quota en limieten [voor de Azure Batch-service voor](batch-quota-limit.md)meer informatie over servicelimieten.

> [!TIP]
> Zorg ervoor dat u `maxTasksPerNode` rekening houdt met de waarde wanneer u een [autoscale-formule][enable_autoscaling] voor uw groep maakt. Een formule die evalueert, `$RunningTasks` kan bijvoorbeeld drastisch worden beïnvloed door een toename van taken per knooppunt. Zie [Compute-knooppunten automatisch schalen in een Azure Batch-groep](batch-automatic-scaling.md) voor meer informatie.
>
>

## <a name="distribution-of-tasks"></a>Verdeling van taken
Wanneer de compute nodes in een groep taken gelijktijdig kunnen uitvoeren, is het belangrijk om op te geven hoe u de taken wilt verdelen over de knooppunten in de groep.

Door de eigenschap [CloudPool.TaskSchedulingPolicy te][task_schedule] gebruiken, u opgeven dat taken gelijkmatig moeten worden toegewezen aan alle knooppunten in de groep ('verspreiden'). U ook opgeven dat aan elk knooppunt zoveel mogelijk taken moeten worden toegewezen voordat taken worden toegewezen aan een ander knooppunt in de groep ('verpakking').

Als voorbeeld van hoe deze functie waardevol is, u rekening houden met de pool van [Standaard\_D14-knooppunten](../cloud-services/cloud-services-sizes-specs.md) (in het bovenstaande voorbeeld) die is geconfigureerd met een [CloudPool.MaxTasksPerComputeNode-waarde][maxtasks_net] van 16. Als het [CloudPool.TaskSchedulingPolicy][task_schedule] is geconfigureerd met een [ComputeNodeFillType][fill_type] van *Pack,* zou dit het gebruik van alle 16 kernen van elk knooppunt maximaliseren en een [automatisch schalende groep](batch-automatic-scaling.md) toestaan om ongebruikte knooppunten uit de groep te snoeien (knooppunten zonder toegewezen taken). Dit minimaliseert het gebruik van resources en bespaart geld.

## <a name="batch-net-example"></a>Batch .NET, voorbeeld
In [dit batchfragment van de NET][api_net] API-code wordt een aanvraag weergegeven om een groep te maken die vier knooppunten bevat met maximaal vier taken per knooppunt. Hiermee wordt een taakplanningsbeleid opgegeven dat elk knooppunt vult met taken voordat taken worden toewijzen aan een ander knooppunt in de groep. Zie [BatchClient.PoolOperations.CreatePool][poolcreate_net]voor meer informatie over het toevoegen van pools met behulp van de Batch .NET API.

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Voorbeeld batchREST
Dit [API-fragment Batch REST][api_rest] toont een aanvraag om een groep te maken die twee grote knooppunten bevat met maximaal vier taken per knooppunt. Zie Een groep toevoegen [aan een account voor][rest_addpool]meer informatie over het toevoegen van pools met behulp van de API REST.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> U `maxTasksPerNode` het element en de eigenschap [MaxTasksPerComputeNode][maxtasks_net] alleen instellen op tijd voor het maken van de groep. Ze kunnen niet worden gewijzigd nadat er al een pool is gemaakt.
>
>

## <a name="code-sample"></a>Codevoorbeeld
Het [ParallelNodeTasks-project][parallel_tasks_sample] op GitHub illustreert het gebruik van de eigenschap [CloudPool.MaxTasksPerComputeNode.][maxtasks_net]

Deze C#-consoletoepassing gebruikt de [Batch .NET-bibliotheek][api_net] om een groep te maken met een of meer rekenknooppunten. Het voert een configureerbaar aantal taken op die knooppunten uit om variabele belasting te simuleren. Uitvoer van de toepassing geeft aan welke knooppunten elke taak hebben uitgevoerd. De toepassing geeft ook een overzicht van de taakparameters en duur. Het overzichtsgedeelte van de uitvoer van twee verschillende uitvoeringen van de voorbeeldtoepassing wordt hieronder weergegeven.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

De eerste uitvoering van de voorbeeldtoepassing toont aan dat met één knooppunt in de groep en de standaardinstelling van één taak per knooppunt de taakduur meer dan 30 minuten bedraagt.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

De tweede run van de steekproef toont een aanzienlijke daling van de duur van de taak. Dit komt omdat de groep is geconfigureerd met vier taken per knooppunt, waardoor parallelle taakuitvoering de taak in bijna een kwart van de tijd kan voltooien.

> [!NOTE]
> De taakduur in de bovenstaande samenvattingen omvat geen tijd voor het maken van de groep. Elk van de bovenstaande taken is ingediend bij eerder gemaakte groepen waarvan de rekenknooppunten in de status *Niet-actief* waren tijdens het indienen.
>
>

## <a name="next-steps"></a>Volgende stappen
### <a name="batch-explorer-heat-map"></a>Batch Explorer-heatkaart
[Batch Explorer][batch_labs] is een gratis, rijk uitgeruste, zelfstandige clienttool om Azure Batch-toepassingen te maken, te debuggen en te controleren. Batch Explorer bevat een *Heat Map-functie* die de uitvoering van taken biedt. Wanneer u de voorbeeldtoepassing [ParallelTasks][parallel_tasks_sample] uitvoert, u de functie Heat Map gebruiken om de uitvoering van parallelle taken op elk knooppunt eenvoudig te visualiseren.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

