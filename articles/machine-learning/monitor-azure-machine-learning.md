---
title: Azure Machine Learning controleren | Microsoft Documenten
description: Meer informatie over het gebruik van Azure Monitor om waarschuwingen over metrische gegevens van Azure Machine Learning weer te geven, te analyseren en te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399112"
---
# <a name="monitoring-azure-machine-learning"></a>Azure Machine Learning bewaken

In dit artikel worden de bewakingsgegevens beschreven die zijn gegenereerd door Azure Machine Learning. Het beschrijft ook hoe u de Azure Monitor gebruiken om uw gegevens te analyseren en waarschuwingen te definiëren.

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor beheerders, zoals het de bewaking voor azure machine learning beschrijft. Als u een gegevenswetenschapper of ontwikkelaar bent en informatie wilt controleren die specifiek is voor uw modeltrainingsuitvoeringen, raadpleegt u de volgende documenten:
>
> * [Trainingsruns starten, controleren en annuleren](how-to-manage-runs.md)
> * [Metrische gegevens registreren voor trainingsuitvoeringen](how-to-track-experiments.md)
> * [Experimenten bijhouden met MLflow](how-to-use-mlflow.md)
> * [Uitvoeringen visualiseren met TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning registreert bewakingsgegevens met Azure Monitor, een volledige stackmonitoringservice in Azure. Azure Monitor biedt een complete set functies om uw Azure-bronnen te bewaken. Het kan ook resources in andere clouds en on-premises controleren.

Begin met het artikel [Azure Monitor-overzicht](/azure/azure-monitor/overview), dat een overzicht geeft van de bewakingsmogelijkheden. De volgende secties bouwen voort op deze informatie door details van het gebruik van Azure Monitor met Azure Machine Learning.

Zie [Gebruiks- en geschatte kosten](/azure/azure-monitor/platform/usage-estimated-costs)voor meer informatie over de kosten die zijn gekoppeld aan Azure Monitor. Zie Tijd voor het [innemen](/azure/azure-monitor/platform/data-ingestion-time)van gegevens registreren om inzicht te krijgen in de tijd die nodig is om inzicht te krijgen in de tijd die nodig is om inzicht te krijgen in de tijd van het opnemen van gegevens in Azure Monitor.

## <a name="monitoring-data-from-azure-machine-learning"></a>Gegevens van Azure Machine Learning bewaken

Azure Machine Learning verzamelt dezelfde soorten bewakingsgegevens als andere Azure-resources, die worden beschreven in [Monitoringgegevens van Azure-bronnen.](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) Zie [de gegevensverwijzing voor azure machine learning-bewakingsgegevens](monitor-resource-reference.md) voor een gedetailleerde referentie van de logboeken en statistieken die zijn gemaakt door Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Metrische gegevens analyseren

U statistieken voor Azure Machine Learning analyseren door **Statistieken** te openen in het menu **Azure Monitor.** Zie [Aan de slag met Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) voor meer informatie over het gebruik van deze tool.

Alle statistieken voor Azure Machine Learning staan in de naamruimte **Machine Learning Service Workspace**.

![Metrics Explorer met Machine Learning Service Workspace geselecteerd](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filteren en splitsen

Voor statistieken die dimensies ondersteunen, u filters toepassen met een dimensiewaarde. Bijvoorbeeld het filteren van **actieve kernen** voor een **clusternaam** van `cpu-cluster`. 

U ook een statistiek splitsen op dimensie om te visualiseren hoe verschillende segmenten van de statistiek zich met elkaar verhouden. Als u bijvoorbeeld het **type pijplijnstap** uitsplitst om een aantal typen stappen te zien die in de pijplijn worden gebruikt.

Zie Geavanceerde functies van Azure [Monitor](/azure/azure-monitor/platform/metrics-charts)voor meer informatie over filteren en splitsen.

## <a name="alerts"></a>Waarschuwingen

U waarschuwingen voor Azure Machine Learning openen door **Waarschuwingen** te openen in het menu **Azure Monitor.** Zie [Metrische waarschuwingen maken, weergeven en beheren met Azure Monitor](/azure/azure-monitor/platform/alerts-metric) voor meer informatie over het maken van waarschuwingen.

In de volgende tabel worden veelvoorkomende en aanbevolen metrische waarschuwingsregels voor Azure Machine Learning weergegeven:

| Waarschuwingstype | Voorwaarde | Beschrijving |
|:---|:---|:---|
| Modeldeploy mislukt | Aggregatietype: Totaal, Operator: groter dan, Drempelwaarde: 0 | Wanneer een of meer modelimplementaties zijn mislukt |
| Percentage quotumgebruik | Aggregatietype: Gemiddelde, operator: groter dan drempelwaarde: 90| Wanneer het percentage quotumgebruik groter is dan 90% |
| Onbruikbare knooppunten | Aggregatietype: Totaal, Operator: groter dan, Drempelwaarde: 0 | Wanneer er een of meer onbruikbare knooppunten zijn |

## <a name="configuration"></a>Configuratie

> [!IMPORTANT]
> __Statistieken voor Azure Machine Learning hoeven niet te worden geconfigureerd,__ ze worden automatisch verzameld en zijn beschikbaar in de Metrics Explorer voor monitoring en waarschuwingen.

U een diagnostische instelling toevoegen om de volgende functionaliteit te configureren:

* Archiefgegevens en metrische gegevens naar een Azure-opslagaccount.
* Log- en metrische gegevens streamen naar een Azure Event Hub.
* Log- en metrische gegevens verzenden naar Azure Monitor Log Analytics.

Voor het inschakelen van deze instellingen is extra Azure-services (opslagaccount, gebeurtenishub of Log Analytics) vereist, waardoor uw kosten kunnen stijgen. Ga naar de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator)om een geschatte kosten te berekenen.

Zie Diagnostische instelling maken om [platformlogboeken en statistieken in Azure te verzamelen voor](/azure/azure-monitor/platform/diagnostic-settings)meer informatie over het maken van een diagnostische instelling.

U de volgende logboeken configureren voor Azure Machine Learning:

| Categorie | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen uit Azure Machine Learning-computeclusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knooppunten binnen een Azure Machine Learning-computecluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!NOTE]
> Wanneer u statistieken inschakelt in een diagnostische instelling, wordt dimensiegegevens momenteel niet opgenomen als onderdeel van de informatie die naar een opslagaccount, gebeurtenishub of logboekanalyse wordt verzonden.

## <a name="analyzing-log-data"></a>Loggegevens analyseren

Voor het gebruik van Azure Monitor Log Analytics moet u een diagnostische configuratie maken en __gegevens verzenden naar Logboekanalyse__inschakelen. Zie de sectie [Configuratie](#configuration) voor meer informatie.

Gegevens in Azure Monitor-logboeken worden opgeslagen in tabellen, waarbij elke tabel zijn eigen set unieke eigenschappen heeft. Azure Machine Learning slaat gegevens op in de volgende tabellen:

| Tabel | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen uit Azure Machine Learning-computeclusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knooppunten binnen een Azure Machine Learning-computecluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!IMPORTANT]
> Wanneer u **Logboeken** selecteert in het azure machine learning-menu, wordt Log Analytics geopend met de queryscope die is ingesteld op de huidige werkruimte. Dit betekent dat logboekquery's alleen gegevens uit die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere databases of gegevens uit andere Azure-services bevat, selecteert u **Logboeken** in het menu **Azure Monitor.** Zie [Querybereik en -tijdsbereik logboeken in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) voor meer informatie.

Zie [Azure Machine Learning](monitor-resource-reference.md)monitoring data reference voor een gedetailleerde referentie van de logboeken en statistieken.

### <a name="sample-queries"></a>Voorbeeldquery's

Hieronder volgen query's die u gebruiken om uw Azure Machine Learning-resources te controleren: 

+ Mislukte taken in de afgelopen vijf dagen:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Records opvragen voor een specifieke taaknaam:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Clustergebeurtenissen in de afgelopen vijf dagen weergeven voor clusters waarbij de VM-grootte wordt Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Ontvang knooppunten toegewezen in de afgelopen acht dagen:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie Azure Machine Learning monitoring [data reference](monitor-resource-reference.md)voor een overzicht van de logboeken en statistieken.
- Zie [Quota beheren en aanvragen voor Azure-resources voor](how-to-manage-quotas.md)informatie over het werken met quota met betrekking tot Azure Machine Learning.
- Zie [Azure-resources bewaken met Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource)voor meer informatie over het bewaken van Azure-resources.
