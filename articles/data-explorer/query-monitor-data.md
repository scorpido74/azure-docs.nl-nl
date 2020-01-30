---
title: Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)
description: In dit onderwerp voert u een query uit op gegevens in Azure Monitor door een Azure Data Explorer proxy te maken voor query's op meerdere producten met Application Insights en Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: d39ffa05448600fe3bd09baf6080aa1565ae19ba
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843570"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)

Het Azure Data Explorer-proxy cluster (ADX-proxy) is een entiteit waarmee u query's voor meerdere producten kunt uitvoeren tussen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)en [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) in de [Azure monitor](/azure/azure-monitor/) -service. U kunt Azure Monitor Log Analytics-werk ruimten of Application Insights-apps als proxy clusters toewijzen. U kunt vervolgens een query uitvoeren op het proxy cluster met behulp van Azure Data Explorer-hulpprogram ma's en hiernaar verwijzen in een cross-cluster query. In dit artikel wordt beschreven hoe u verbinding maakt met een proxy cluster, een proxy cluster toevoegt aan Azure Data Explorer web-UI en query's uitvoert op uw AI-apps of vanuit Azure Data Explorer.

De Azure Data Explorer-proxy stroom: 

![ADX-proxy stroom](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> De ADX-proxy bevindt zich in de preview-modus. [Maak verbinding met de proxy](#connect-to-the-proxy) om de ADX-proxy functie voor uw clusters in te scha kelen. Neem contact op met het [ADXProxy](mailto:adxproxy@microsoft.com) -team.

## <a name="connect-to-the-proxy"></a>Verbinding maken met de proxy

1. Controleer of uw Azure Data Explorer systeem eigen cluster (zoals *Help* -cluster) wordt weer gegeven in het menu links voordat u verbinding maakt met uw Log Analytics of Application Insights cluster.

    ![ADX systeem eigen cluster](media/adx-proxy/web-ui-help-cluster.png)

1. Selecteer **cluster toevoegen**in de gebruikers interface van Azure Data Explorer (https://dataexplorer.azure.com/clusters).

1. Voeg in het venster **cluster toevoegen** de URL toe aan het La-of AI-cluster. 
    
    * Voor LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Voor AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecteer **Toevoegen**.

    ![Cluster toevoegen](media/adx-proxy/add-cluster.png)

    Als u een verbinding aan meer dan één proxy cluster toevoegt, geeft u een andere naam op. Anders hebben alle dezelfde naam in het linkerdeel venster.

1. Nadat de verbinding tot stand is gebracht, wordt uw LA-of AI-cluster weer gegeven in het linkerdeel venster met uw systeem eigen ADX-cluster. 

    ![Log Analytics-en Azure Data Explorer-clusters](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Query's uitvoeren

U kunt de query's uitvoeren met client hulpprogramma's die ondersteuning bieden voor Kusto-query's, zoals: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, flow, PowerQuery, Power shell, Jarvis, lens REST API.

> [!TIP]
> * De naam van de data base moet dezelfde naam hebben als de resource die is opgegeven in het proxy cluster. Namen zijn hoofdletter gevoelig.
> * In query's voor meerdere clusters moet u ervoor zorgen dat de naamgeving van Application Insights-apps en Log Analytics-werk ruimten juist is.
>     * Als namen speciale tekens bevatten, worden deze vervangen door URL-code ring in de naam van het proxy cluster. 
>     * Als namen tekens bevatten die niet voldoen aan de [KQL-id-naam regels](/azure/kusto/query/schema-entities/entity-names), worden deze vervangen door het streepje **-** teken.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Directe query vanuit uw LA of AI ADX-proxy cluster

Query's uitvoeren op uw LA-of AI-cluster. Controleer of uw cluster is geselecteerd in het linkerdeel venster. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Werk ruimte query LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Kruis query van uw ADX-of AI-cluster en het ADX systeem eigen cluster 

Wanneer u cross-cluster query's uitvoert vanuit de proxy, controleert u of uw systeem eigen ADX-cluster is geselecteerd in het linkerdeel venster. De volgende voor beelden demonstreren het combi neren van ADX-cluster tabellen (met behulp van `union`) met de werk ruimte LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Kruis query van de Azure Data Explorer proxy](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Het gebruik van de [`join`-operator](/azure/kusto/query/joinoperator)in plaats van Union vereist mogelijk een [`hint`](/azure/kusto/query/joinoperator#join-hints) om het uit te voeren op een Azure Data Explorer systeem eigen cluster (en niet op de proxy). 

## <a name="additional-syntax-examples"></a>Aanvullende voor beelden van syntaxis

De volgende syntaxis opties zijn beschikbaar bij het aanroepen van de Application Insights (AI) of Log Analytics (LA)-clusters:

|Syntaxis beschrijving  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Data base binnen een cluster dat alleen de gedefinieerde resource bevat in dit abonnement (**Aanbevolen voor query's in meerdere clusters**) |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster dat alle apps/werk ruimten in dit abonnement bevat    |     cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Een cluster dat alle apps/werk ruimten in het abonnement bevat en lid is van deze resource groep    |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Een cluster dat alleen de gedefinieerde resource bevat in dit abonnement      |    cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Volgende stappen

[Query's schrijven](write-queries.md)
