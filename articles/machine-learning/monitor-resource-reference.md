---
title: Referentie van monitoringgegevens | Microsoft Documenten
titleSuffix: Azure Machine Learning
description: Meer informatie over de gegevens en bronnen die zijn verzameld voor Azure Machine Learning en beschikbaar zijn in Azure Monitor. Azure Monitor verzamelt en surfaces gegevens over uw Azure Machine Learning-werkruimte en stelt u in staat om statistieken te bekijken, waarschuwingen in te stellen en geregistreerde gegevens te analyseren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927553"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Gegevensverwijzing voor Azure machine learning-controlegegevens

Meer informatie over de gegevens en bronnen die door Azure Monitor zijn verzameld vanuit uw Azure Machine Learning-werkruimte. Zie [Azure Machine Learning controleren](monitor-azure-machine-learning.md) voor meer informatie over het verzamelen en analyseren van bewakingsgegevens.

## <a name="resource-logs"></a>Resourcelogboeken

In de volgende tabel worden de eigenschappen voor Azure Machine Learning-bronlogboeken weergegeven wanneer ze worden verzameld in Azure Monitor-logboeken of Azure Storage.

### <a name="amlcomputejobevents-table"></a>Tabel AmlComputeJobEvents

| Eigenschap | Beschrijving |
|:--- |:---|
| TimeGenerated | Tijd waarop de logboekvermelding is gegenereerd |
| OperationName | Naam van de bewerking die is gekoppeld aan de logboekgebeurtenis |
| Categorie | Naam van de logboekgebeurtenis, AmlComputeClusterNodeEvent |
| JobId | ID van de ingediende taak |
| ExperimentId (ExperimentId) | ID van het experiment |
| ExperimentName | Naam van het experiment |
| KlantAbonnementId | AbonnementId waar Experiment en Job zoals ingediend |
| WorkspaceName | Naam van de werkruimte voor machine learning |
| Clusternaam | Naam van het cluster |
| VoorzieningStaat | Indiening van de stand van de baan |
| ResourceGroupName | Naam van de resourcegroep |
| JobName | Naam van de job |
| Clusterid | ID van het cluster |
| EventType (EventType) | Type van de gebeurtenis Job, bijvoorbeeld JobSubmitted, JobRunning, JobFailed, JobSucceeded, etc. |
| ExecutionState | Status van de taak (de run), bijvoorbeeld Wachtrijen, Uitvoeren, Geslaagd, Mislukt |
| ErrorDetails | Details van taakfout |
| CreationApiVersie | Api-versie die wordt gebruikt om de taak te maken |
| ClusterResourceGroupName | Naam van de brongroep van het cluster |
| TFWorkerCount | Aantal TF-werknemers |
| TFParameterServerCount | Aantal TF-parameterserver |
| ToolType | Gebruikt gereedschap |
| Runincontainer RuninContainer | Vlag die beschrijft of taak in een container moet worden uitgevoerd |
| JobErrorMessage | gedetailleerd bericht van Functiefout |
| NodeId | ID van het knooppunt dat is gemaakt wanneer de taak wordt uitgevoerd |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents, tabel

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated | Tijd waarop de logboekvermelding is gegenereerd |
| OperationName | Naam van de bewerking die is gekoppeld aan de logboekgebeurtenis |
| Categorie | Naam van de logboekgebeurtenis, AmlComputeClusterNodeEvent |
| VoorzieningStaat | Inrichtingsstatus van het cluster |
| Clusternaam | Naam van het cluster |
| Clustertype | Type van het cluster |
| Createdby | Gebruiker die het cluster heeft gemaakt |
| Aantal cores | Aantal kernen in het cluster |
| VmSize (VmSize) | Vm-grootte van het cluster |
| VmPrioriteit | Prioriteit van de knooppunten die zijn gemaakt binnen een cluster Dedicated/LowPriority |
| ScalingType | Type clusterschaling handmatig/automatisch |
| InitialNodeCount InitialNodeCount | Aantal eerste knooppunts van het cluster |
| MinimumAantal Nodede | Minimum aantal node van het cluster |
| MaximumAantal Nodede | Maximaal aantal node s van het cluster |
| Optie nodedetoewijzing | Hoe het knooppunt moet worden omgetrokken |
| Uitgever | Uitgever van het clustertype |
| Aanbieding | Aanbieding waarmee het cluster is gemaakt |
| Sku | Sku van het knooppunt/vm dat binnen het cluster is gemaakt |
| Versie | Versie van de afbeelding die wordt gebruikt terwijl Node/VM wordt gemaakt |
| SubnetId SubnetId | SubnetId van het cluster |
| Toewijzingstaat | Clustertoewijzingsstatus |
| CurrentNodeCount | Huidige knooppunttelling van het cluster |
| TargetNodeCount TargetNodeCount | Aantal doelknooppunten van het cluster tijdens het opschalen/omlaag |
| EventType (EventType) | Type gebeurtenis tijdens het maken van het cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Idle tijd in seconden voordat cluster wordt verkleind |
| VoorrangNodeCount | Vooraf gaande knooppunttelling van het cluster |
| IsResizeGrow IsResizeGrow | Vlag die aangeeft dat cluster wordt opschaling |
| VmFamilyName | Naam van de VM-familie van de knooppunten die binnen het cluster kunnen worden gemaakt |
| LeavingNodeCount | Het aantal knooppunten van het cluster verlaten |
| UnusableNodeCount | Aantal onbruikbare knooppunt's van het cluster |
| IdleNodeCount | Aantal niet-actieve nodevan het cluster |
| RunningNodeCount | Het aantal nodes van het cluster uitvoeren |
| VoorbereidenNodeCount | Knooppunttelling van het cluster voorbereiden |
| Toegewezen quota | Toegewezen quotum aan het cluster |
| QuotaGebruikt | Gebruikt quotum van het cluster |
| ToewijzingStateTransitionTime | Overgangstijd van de ene status naar de andere |
| ClusterFoutcodes | Foutcode ontvangen tijdens het maken of schalen van het cluster |
| CreationApiVersie | Api-versie die wordt gebruikt tijdens het maken van het cluster |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents, tabel

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated | Tijd waarop de logboekvermelding is gegenereerd |
| OperationName | Naam van de bewerking die is gekoppeld aan de logboekgebeurtenis |
| Categorie | Naam van de logboekgebeurtenis, AmlComputeClusterNodeEvent |
| Clusternaam | Naam van het cluster |
| NodeId | ID van het gemaakte clusterknooppunt |
| VmSize (VmSize) | Vm-grootte van het knooppunt |
| VmFamilyName | Vm-familie waartoe het knooppunt behoort |
| VmPrioriteit | Prioriteit van het knooppunt gemaakt Dedicated/LowPriority |
| Uitgever | Uitgever van de vm-afbeelding, bijvoorbeeld microsoft-dsvm |
| Aanbieding | Aanbieding in verband met het maken van vm's |
| Sku | Sku van het knooppunt/vm gemaakt |
| Versie | Versie van de afbeelding die wordt gebruikt terwijl Node/VM wordt gemaakt |
| ClusterCreationTime | Tijd waarop het cluster is gemaakt |
| Het formaat Starttijd wijzigen | Tijd waarop het cluster opschalen/omlaag is gestart |
| Het formaat Van EndTime wijzigen | Tijd waarop clusteropschalen/omlaag is beëindigd |
| NodeAllocationTime | Tijd waarop Node werd toegewezen |
| NodeBootTime | Tijd waarop Node werd opgestart |
| StartTaskStarttijd | Tijd waarop taak aan een knooppunt is toegewezen en is gestart |
| Begintaakendtijd | Tijd waarop taak die is toegewezen aan een knooppunt is beëindigd |
| TotalE2ETimeInSeconden | Totaal tijdknooppunt actief |

### <a name="metrics"></a>Metrische gegevens

In de volgende tabellen worden de platformstatistieken weergegeven die zijn verzameld voor Azure Machine Learning Alle statistieken worden opgeslagen in de naamruimte **Azure Machine Learning Workspace**.

**Model**

| Gegevens | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Modelimplementatie is mislukt | Count | Het aantal modelimplementaties dat is mislukt. |
| Modeldeploy gestart | Count | Het aantal gestarte modelimplementaties. |
| Modeldeploy geslaagd | Count | Het aantal modelimplementaties dat is geslaagd. |
| Modelregister is mislukt | Count | Het aantal modelregistraties dat is mislukt. |
| Modelregister geslaagd | Count | Het aantal modelregistraties dat is geslaagd. |

**Quotum**

Quota-informatie is alleen voor Azure Machine Learning compute.

| Gegevens | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Actieve kernen | Count | Het aantal actieve rekenkernen. |
| Actieve knooppunten | Count | Het aantal actieve knooppunten. |
| Niet-actieve kernen | Count | Het aantal niet-actieve rekenkernen. |
| Niet-actieve knooppunten | Count | Het aantal inactieve compute nodes. |
| Kernen verlaten | Count | Het aantal verlaten kernen. |
| Knooppunten verlaten | Count | Het aantal verlaten knooppunten. |
| Voorrang kernen | Count | Het aantal voorgefinancierde kernen. |
| Voorkomknooppunten | Count | Het aantal voorkoming van knooppunten. |
| Percentage quotumgebruik | Percentage | Het gebruikte percentage quota. |
| Totaal aantal cores | Count | De totale kernen. |
| Totaal aantal knooppunten | Count | De totale knooppunten. |
| Onbruikbare kernen | Count | Het aantal onbruikbare kernen. |
| Onbruikbare knooppunten | Count | Het aantal onbruikbare knooppunten. |

De volgende dimensies zijn dimensies die kunnen worden gebruikt om quotastatistieken te filteren:

| Dimensie | Metrische(s) beschikbaar met | Beschrijving |
| ---- | ---- | ---- |
| Clusternaam | Alle quotastatistieken | De naam van de rekeninstantie. |
| Vm-familienaam | Percentage quotumgebruik | De naam van de VM-familie die door het cluster wordt gebruikt. |
| Vm-prioriteit | Percentage quotumgebruik | De prioriteit van de VM.

**Uitvoeren**

Informatie over trainingsruns.

| Gegevens | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Voltooide uitvoeringen | Count | Het aantal voltooide runs. |
| Mislukte uitvoeringen | Count | Het aantal mislukte runs. |
| Gestarte runs | Count | Het aantal gestarte runs. |

De volgende dimensies zijn dimensies die kunnen worden gebruikt om run metrics te filteren:

| Dimensie | Beschrijving |
| ---- | ---- |
| ComputeType (ComputeType) | Het rekentype dat de run heeft gebruikt. |
| PipelineStepType | Het type [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) dat tijdens de uitvoering wordt gebruikt. |
| GepubliceerdePipelineId | De id van de gepubliceerde pijplijn die tijdens de uitvoering wordt gebruikt. |
| RunType (RunType) | Het type run. |

De geldige waarden voor de dimensie RunType zijn:

| Waarde | Beschrijving |
| ----- | ----- |
| Experiment | Niet-pijplijnuitvoeringen. |
| PipelineRun | Een pijplijnrun, die de bovenliggende is van een StepRun. |
| StepRun (StepRun) | Een run voor een pijplijnstap. |
| Opnieuw gebruiktStepRun | Een run voor een pijplijnstap die een eerdere run opnieuw gebruikt. |

## <a name="see-also"></a>Zie ook

- Zie [Azure Machine Learning controleren](monitor-azure-machine-learning.md) voor een beschrijving van het bewaken van Azure Machine Learning.
- Zie [Azure-resources bewaken met Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) voor meer informatie over het bewaken van Azure-resources.
