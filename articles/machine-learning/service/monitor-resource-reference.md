---
title: Naslag informatie over bewakings gegevens | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Meer informatie over de gegevens en resources die worden verzameld voor Azure Machine Learning en die beschikbaar zijn in Azure Monitor. Azure Monitor verzamelt en bewaart gegevens over uw Azure Machine Learning-werk ruimte en biedt u de mogelijkheid om meet waarden weer te geven, waarschuwingen in te stellen en geregistreerde gegevens te analyseren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/06/2019
ms.openlocfilehash: 743007d3636516a39dd36caf3d44d3edb650c030
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929529"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Naslag informatie voor Azure machine learning monitoring-gegevens

Meer informatie over de gegevens en resources die worden verzameld door Azure Monitor van uw Azure Machine Learning-werk ruimte. Zie [bewaking Azure machine learning](monitor-azure-machine-learning.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens.

## <a name="resource-logs"></a>Resourcelogboeken

De volgende tabel geeft een lijst van de eigenschappen voor Azure Machine Learning bron Logboeken wanneer ze worden verzameld in Azure Monitor Logboeken of Azure Storage.

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents-tabel

| Eigenschap | Beschrijving |
|:--- |:---|
| TimeGenerated | Tijdstip waarop de logboek vermelding is gegenereerd |
| OperationName | De naam van de bewerking die is gekoppeld aan de logboek gebeurtenis |
| Categorie | De naam van de logboek gebeurtenis, AmlComputeClusterNodeEvent |
| JobId | ID van de verzonden taak |
| ExperimentId | ID van het experiment |
| experimentnaam | De naam van het experiment |
| customerSubscriptionId | SubscriptionId waar experiment en job worden verzonden |
| workspaceName | De naam van de machine learning-werk ruimte |
| ClusterName | De naam van het cluster |
| ProvisioningState | Status van het verzenden van taken |
| ResourceGroupName | Naam van de resource groep |
| JobName | De naam van de taak |
| ClusterId | ID van het cluster |
| Type | Type taak gebeurtenis, bijvoorbeeld JobSubmitted, JobRunning, JobFailed, JobSucceeded, enzovoort. |
| ExecutionState | De status van de taak (de uitvoering), bijvoorbeeld in de wachtrij, actief, geslaagd, mislukt |
| ErrorDetails | Details van taak fout |
| CreationApiVersion | API-versie die wordt gebruikt om de taak te maken |
| ClusterResourceGroupName | De naam van de resource groep van het cluster |
| TFWorkerCount | Aantal TF-werk rollen |
| TFParameterServerCount | Aantal TF para meter server |
| ToolType | Type programma dat wordt gebruikt |
| RunInContainer | Vlag waarmee wordt beschreven of de taak moet worden uitgevoerd in een container |
| JobErrorMessage | gedetailleerd bericht van taak fout |
| NodeId | De ID van het knoop punt dat is gemaakt waarbij de taak wordt uitgevoerd |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents-tabel

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated | Tijdstip waarop de logboek vermelding is gegenereerd |
| OperationName | De naam van de bewerking die is gekoppeld aan de logboek gebeurtenis |
| Categorie | De naam van de logboek gebeurtenis, AmlComputeClusterNodeEvent |
| ProvisioningState | De inrichtings status van het cluster |
| ClusterName | De naam van het cluster |
| ClusterType | Type van het cluster |
| Type CreatedBy | Gebruiker die het cluster heeft gemaakt |
| CoreCount | Aantal kernen in het cluster |
| VmSize | VM-grootte van het cluster |
| VmPriority | Prioriteit van de knoop punten die zijn gemaakt in een cluster toegewezen-LowPriority |
| ScalingType | Type hand matig/automatisch schalen cluster |
| InitialNodeCount | Oorspronkelijk aantal knoop punten van het cluster |
| MinimumNodeCount | Minimum aantal knoop punten van het cluster |
| MaximumNodeCount | Maximum aantal knoop punten van het cluster |
| NodeDeallocationOption | Hoe de toewijzing van het knoop punt moet worden opgeheven |
| Uitgever | Uitgever van het cluster type |
| Aanbieding | Aanbod waarmee het cluster is gemaakt |
| Sku | SKU van het knoop punt/de virtuele machine die in het cluster is gemaakt |
| Versie | Versie van de installatie kopie die wordt gebruikt tijdens het maken van het knoop punt/VM |
| SubnetId | SubnetId van het cluster |
| AllocationState | Cluster toewijzings status |
| CurrentNodeCount | Huidig aantal knoop punten van het cluster |
| TargetNodeCount | Aantal doel knooppunten van het cluster tijdens het omhoog/omlaag schalen |
| Type | Type gebeurtenis tijdens het maken van het cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Niet-actieve tijd in seconden voordat het cluster omlaag wordt geschaald |
| PreemptedNodeCount | Telling van het aantal knoop punten van het cluster |
| IsResizeGrow | Vlag waarmee wordt aangegeven dat het cluster omhoog wordt geschaald |
| VmFamilyName | De naam van de VM-serie van de knoop punten die in het cluster kunnen worden gemaakt |
| LeavingNodeCount | Het aantal knoop punten van het cluster verlaten |
| UnusableNodeCount | Onbruikbaar aantal knoop punten van het cluster |
| IdleNodeCount | Aantal niet-actieve knoop punten van het cluster |
| RunningNodeCount | Aantal actieve knoop punten van het cluster |
| PreparingNodeCount | Het aantal knoop punten van het cluster voorbereiden |
| QuotaAllocated | Toegewezen quotum aan het cluster |
| QuotaUtilized | Gebruikt quotum van het cluster |
| AllocationStateTransitionTime | Overgangs tijd van de ene status naar de andere |
| ClusterErrorCodes | Fout code die wordt ontvangen tijdens het maken of schalen van het cluster |
| CreationApiVersion | API-versie die wordt gebruikt tijdens het maken van het cluster |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents-tabel

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated | Tijdstip waarop de logboek vermelding is gegenereerd |
| OperationName | De naam van de bewerking die is gekoppeld aan de logboek gebeurtenis |
| Categorie | De naam van de logboek gebeurtenis, AmlComputeClusterNodeEvent |
| ClusterName | De naam van het cluster |
| NodeId | ID van het cluster knooppunt dat is gemaakt |
| VmSize | VM-grootte van het knoop punt |
| VmFamilyName | De VM-familie waartoe het knoop punt behoort |
| VmPriority | Prioriteit van het knoop punt dat speciaal is gemaakt/LowPriority |
| Uitgever | Uitgever van de VM-installatie kopie, bijvoorbeeld micro soft-dsvm |
| Aanbieding | Aanbieding gekoppeld aan het maken van een virtuele machine |
| Sku | SKU van het knoop punt/VM gemaakt |
| Versie | Versie van de installatie kopie die wordt gebruikt tijdens het maken van het knoop punt/VM |
| ClusterCreationTime | Tijdstip waarop het cluster is gemaakt |
| ResizeStartTime | Tijdstip waarop het omhoog/omlaag schalen van het cluster is gestart |
| ResizeEndTime | Tijd waarop het omhoog/omlaag schalen van het cluster is beëindigd |
| NodeAllocationTime | Tijdstip waarop het knoop punt is toegewezen |
| NodeBootTime | Tijdstip waarop het knoop punt is opgestart |
| StartTaskStartTime | Tijd waarop de taak is toegewezen aan een knoop punt en is gestart |
| StartTaskEndTime | Tijd waarop de taak die aan een knoop punt is toegewezen, is beëindigd |
| TotalE2ETimeInSeconds | Het totale tijds knooppunt is actief |

### <a name="metrics"></a>Metrische gegevens

In de volgende tabellen worden de platform gegevens weer gegeven die zijn verzameld voor Azure Machine Learning alle metrische gegevens worden opgeslagen in de naam ruimte **Azure machine learning de service werkruimte**.

**Activiteitsmodel**

| Gegevens | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Model implementeren is mislukt | Count | Het aantal model implementaties dat is mislukt. |
| Model implementeren gestart | Count | Het aantal model implementaties is gestart. |
| Model implementeren is voltooid | Count | Het aantal model implementaties dat is geslaagd. |
| Model register mislukt | Count | Het aantal model registraties dat is mislukt. |
| Model registratie is voltooid | Count | Het aantal model registraties dat is geslaagd. |

**Quotum**

Quota gegevens zijn alleen voor het berekenen van Azure Machine Learning.

| Gegevens | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Actieve kernen | Count | Het aantal actieve reken kernen. |
| Actieve knoop punten | Count | Het aantal actieve knoop punten. |
| Niet-actieve kernen | Count | Het aantal niet-actieve Compute-kernen. |
| Niet-actieve knoop punten | Count | Het aantal niet-actieve reken knooppunten. |
| Kernen verlaten | Count | Het aantal Leave-kernen. |
| Knoop punten verlaten | Count | Het aantal knoop punten dat de poort verlaat. |
| Afgebroken kernen | Count | Het aantal afgebroken kernen. |
| Knoop punten die zijn afgebroken | Count | Het aantal knoop punten dat is afgebroken. |
| Percentage quotum gebruik | Procent | Het percentage van de gebruikte quota. |
| Totaal aantal kernen | Count | Het totale aantal kernen. |
| Totaal aantal knoop punten | Count | Het totale aantal knoop punten. |
| Onbruikbaar aantal kern geheugens | Count | Het aantal onbruikbaar kern geheugens. |
| Niet-bruikbare knoop punten | Count | Het aantal niet-bruikbare knoop punten. |

Hieronder ziet u dimensies die kunnen worden gebruikt voor het filteren van quotum metrieken:

| Dimensie | Metriek (en) beschikbaar met | Beschrijving |
| ---- | ---- | ---- |
| Clusternaam | Alle quota gegevens | De naam van het reken exemplaar. |
| Naam van VM-familie | Percentage quotum gebruik | De naam van de VM-familie die door het cluster wordt gebruikt. |
| VM-prioriteit | Percentage quotum gebruik | De prioriteit van de virtuele machine.

**Uitvoeringsrun**

Informatie over trainings uitvoeringen.

| Gegevens | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Voltooide uitvoeringen | Count | Het aantal voltooide uitvoeringen. |
| Mislukte uitvoeringen | Count | Het aantal mislukte uitvoeringen. |
| Gestart uitvoeringen | Count | Het aantal gestarte uitvoeringen. |

Hieronder ziet u dimensies die kunnen worden gebruikt voor het filteren van metrische gegevens voor uitvoering:

| Dimensie | Beschrijving |
| ---- | ---- |
| ComputeType | Het reken type dat wordt gebruikt voor de uitvoering. |
| PipelineStepType | Het type [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) dat wordt gebruikt in de uitvoering. |
| PublishedPipelineId | De ID van de gepubliceerde pijp lijn die wordt gebruikt in de uitvoering. |
| RunType | Het type uitvoering. |

De geldige waarden voor de dimensie RunType zijn:

| Waarde | Beschrijving |
| ----- | ----- |
| Probeer | Niet-pijplijn uitvoeringen. |
| PipelineRun | Een pijplijn uitvoering, het bovenliggende element van een StepRun. |
| StepRun | Een uitvoering voor een pijplijn stap. |
| ReusedStepRun | Een uitvoering van een pijplijn stap die een eerdere uitvoering opnieuw gebruikt. |

## <a name="see-also"></a>Zie ook

- Zie [bewaking Azure machine learning](monitor-azure-machine-learning.md) voor een beschrijving van de bewakings Azure machine learning.
- Zie [Azure-resources bewaken met Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) voor meer informatie over het bewaken van Azure-resources.
