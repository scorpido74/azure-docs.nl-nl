---
title: Bewakings Azure Machine Learning | Microsoft Docs
description: Meer informatie over het gebruik van Azure Monitor voor het weer geven, analyseren en maken van waarschuwingen over metrische gegevens van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/04/2019
ms.openlocfilehash: c35afe6e3d6ec74e8fc7cd134f533d1e618c820e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845738"
---
# <a name="monitoring-azure-machine-learning"></a>Bewakings Azure Machine Learning

In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure Machine Learning. Ook wordt beschreven hoe u de Azure Monitor kunt gebruiken om uw gegevens te analyseren en waarschuwingen te definiëren.

> [!TIP]
> De informatie in dit document is hoofd zakelijk bedoeld voor beheerders, zoals het de bewaking voor de Azure Machine Learning beschrijft. Als u een Data-wetenschapper of-ontwikkelaar bent en informatie wilt bewaken die specifiek is voor uw model training-uitvoeringen, raadpleegt u de volgende documenten:
>
> * [Trainings uitvoeringen starten, controleren en annuleren](how-to-manage-runs.md)
> * [Metrische logboek gegevens voor trainings uitvoeringen](how-to-track-experiments.md)
> * [Experimenten met MLflow bijhouden](how-to-use-mlflow.md)
> * [Visualiseren wordt uitgevoerd met TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning registreert de bewakings gegevens met behulp van Azure Monitor. Dit is een volledige stack monitoring-service in Azure. Azure Monitor biedt een volledige set functies voor het bewaken van uw Azure-resources. Het kan ook resources in andere Clouds en on-premises bewaken.

Begin met het artikel [Azure Monitor overzicht](/azure/azure-monitor/overview). Dit biedt een overzicht van de mogelijkheden van bewaking. In de volgende secties vindt u informatie over het gebruik van Azure Monitor met Azure Machine Learning.

Zie [verbruik en geschatte kosten](/azure/azure-monitor/platform/usage-estimated-costs)voor meer informatie over de kosten die zijn gekoppeld aan Azure monitor. Zie [gegevens opname tijd vastleggen](/azure/azure-monitor/platform/data-ingestion-time)voor meer informatie over de tijd die nodig is om uw gegevens weer te geven in azure monitor.

## <a name="monitoring-data-from-azure-machine-learning"></a>Gegevens van Azure Machine Learning bewaken

Azure Machine Learning worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources, die worden beschreven in [gegevens van Azure-resources bewaken](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Zie [Azure machine learning monitoring data Naslag informatie](monitor-resource-reference.md) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens die door Azure machine learning zijn gemaakt.

## <a name="analyzing-metric-data"></a>Metrische gegevens analyseren

U kunt metrische gegevens voor Azure Machine Learning analyseren door **metrische gegevens** te openen in het menu **Azure monitor** . Zie [aan de slag met Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) voor meer informatie over het gebruik van dit hulp programma.

Alle metrische gegevens voor Azure Machine Learning bevinden zich in de naam ruimte **machine learning service werkruimte**.

![Metrics Explorer met Machine Learning service werkruimte geselecteerd](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filteren en splitsen

Voor metrische gegevens die dimensies ondersteunen, kunt u filters toep assen met behulp van een dimensie waarde. U kunt bijvoorbeeld **actieve kernen** filteren op een **Cluster naam** van `cpu-cluster`. 

U kunt ook een metriek op dimensie splitsen om te visualiseren hoe verschillende segmenten van de metrische gegevens met elkaar worden vergeleken. U kunt bijvoorbeeld het **stap type voor de pijp lijn** opsplitsen om een telling te zien van de typen stappen die worden gebruikt in de pijp lijn.

Zie [geavanceerde functies van Azure monitor](/azure/azure-monitor/platform/metrics-charts)voor meer informatie over filteren en splitsen.

## <a name="alerts"></a>Waarschuwingen

U krijgt toegang tot waarschuwingen voor Azure Machine Learning door **waarschuwingen** te openen in het menu **Azure monitor** . Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](/azure/azure-monitor/platform/alerts-metric) voor meer informatie over het maken van waarschuwingen.

De volgende tabel bevat algemene en aanbevolen waarschuwings regels voor metrische gegevens voor Azure Machine Learning:

| Waarschuwingstype | Voorwaarde | Beschrijving |
|:---|:---|:---|
| Modelimplementatie is mislukt | Aggregatie type: Total, operator: groter dan, drempel waarde: 0 | Wanneer een of meer model implementaties zijn mislukt |
| Percentage quotum gebruik | Aggregatie type: Average, operator: groter dan, drempel waarde: 90| Wanneer het percentage van het quota gebruik groter is dan 90% |
| Niet-bruikbare knoop punten | Aggregatie type: Total, operator: groter dan, drempel waarde: 0 | Wanneer er een of meer niet-bruikbare knoop punten zijn |

## <a name="configuration"></a>Configuratie

> [!IMPORTANT]
> __Metrische gegevens voor Azure machine learning hoeven niet te worden geconfigureerd__, ze worden automatisch verzameld en zijn beschikbaar in de metrics Explorer voor bewaking en waarschuwingen.

U kunt een diagnostische instelling toevoegen om de volgende functionaliteit te configureren:

* Logboek-en metrische gegevens archiveren naar een Azure-opslag account.
* Informatie over het streamen van Logboeken en metrische gegevens naar een Azure Event hub.
* Informatie over logboek-en metrische gegevens verzenden naar Azure Monitor Log Analytics.

Het inschakelen van deze instellingen vereist extra Azure-Services (opslag account, Event Hub of Log Analytics), waardoor uw kosten kunnen worden verhoogd. Ga naar de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator)om een geschatte kosten te berekenen.

Zie voor meer informatie over het maken van een diagnostische instelling [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](/azure/azure-monitor/platform/diagnostic-settings).

U kunt de volgende logboeken configureren voor Azure Machine Learning:

| Category | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen van Azure Machine Learning Reken clusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knoop punten binnen een Azure Machine Learning Compute-Cluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!NOTE]
> Wanneer u metrische gegevens in een diagnostische instelling inschakelt, wordt er momenteel geen dimensie-informatie opgenomen als onderdeel van de gegevens die worden verzonden naar een opslag account, Event Hub of log Analytics.

## <a name="analyzing-log-data"></a>Logboek gegevens analyseren

Als u Azure Monitor Log Analytics wilt gebruiken, moet u een diagnostische configuratie maken en __gegevens verzenden naar log Analytics__inschakelen. Zie de sectie [configuratie](#configuration) voor meer informatie.

Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen, waarbij elke tabel een eigen set unieke eigenschappen heeft. Azure Machine Learning slaat gegevens op in de volgende tabellen:

| Tabel | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen van Azure Machine Learning Reken clusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knoop punten binnen een Azure Machine Learning Compute-Cluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!IMPORTANT]
> Wanneer u **Logboeken** in het menu Azure machine learning selecteert, wordt log Analytics geopend met het query bereik dat is ingesteld op de huidige werk ruimte. Dit betekent dat logboek query's alleen gegevens van die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere data bases of gegevens uit andere Azure-Services bevat, selecteert u **Logboeken** in het **Azure monitor** menu. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) voor meer informatie.

Zie [Azure machine learning monitoring data Reference](monitor-resource-reference.md)(Engelstalig) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens.

### <a name="sample-queries"></a>Voorbeeldquery's

Hieronder vindt u query's die u kunt gebruiken om uw Azure Machine Learning-resources te bewaken: 

+ Mislukte taken in de afgelopen vijf dagen ophalen:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Records ophalen voor een specifieke taak naam:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ In de afgelopen vijf dagen cluster gebeurtenissen ophalen voor clusters waarin de VM-grootte is Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Knoop punten ophalen die zijn toegewezen in de afgelopen acht dagen:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure machine learning monitoring data Reference](monitor-resource-reference.md)voor een verwijzing naar de logboeken en metrische gegevens.
- Zie voor meer informatie over het werken met quota's die betrekking hebben op Azure Machine Learning, [Quota's beheren en aanvragen voor Azure-resources](how-to-manage-quotas.md).
- Zie [Azure-resources bewaken met Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource)voor meer informatie over het bewaken van Azure-resources.
