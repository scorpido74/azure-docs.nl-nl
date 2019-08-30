---
title: Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)
description: In dit onderwerp voert u een query uit op gegevens in Azure Monitor door een Azure Data Explorer proxy te maken voor query's op meerdere producten met Application Insights en Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e61f52282bcbc62a3eb069272cd7c1f3e329d3b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172691"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Query's uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview-versie)

Het Azure Data Explorer-proxy cluster (ADX-proxy) is een entiteit waarmee u query's voor meerdere producten kunt uitvoeren tussen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)en [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) in de [Azure monitor](/azure/azure-monitor/) -service. U kunt Azure Monitor Log Analytics-werk ruimten of Application Insights-apps toewijzen als een proxy cluster. U kunt vervolgens een query uitvoeren op het proxy cluster met behulp van Azure Data Explorer-hulpprogram ma's en hiernaar verwijzen in een cross-cluster query. In dit artikel wordt beschreven hoe u verbinding maakt met een proxy cluster, een proxy cluster toevoegt aan Azure Data Explorer web-UI en query's uitvoert op uw AI-apps of vanuit Azure Data Explorer.

De Azure Data Explorer-proxy stroom: 

![ADX-proxy stroom](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> De ADX-proxy bevindt zich in de preview-modus. Neem contact op met het [ADXProxy](mailto:adxproxy@microsoft.com) -team om deze functie in te scha kelen.

## <a name="connect-to-the-proxy"></a>Verbinding maken met de proxy

1. Controleer of uw Azure Data Explorer systeem eigen cluster (zoals *Help* -cluster) wordt weer gegeven in het menu links voordat u verbinding maakt met uw Log Analytics of Application Insights cluster.

    ![ADX systeem eigen cluster](media/adx-proxy/web-ui-help-cluster.png)

1. Selecteer **cluster toevoegen**in de gebruikers https://dataexplorer.azure.com/clusters) interface van Azure Data Explorer (.

1. In het venster **cluster toevoegen** :

    * Voeg de URL toe aan het LA-of AI-cluster. Bijvoorbeeld: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Selecteer **Toevoegen**.

    ![Cluster toevoegen](media/adx-proxy/add-cluster.png)

    Als u een verbinding aan meer dan één proxy cluster toevoegt, geeft u een andere naam op. Anders hebben alle dezelfde naam in het linkerdeel venster.

1. Nadat de verbinding tot stand is gebracht, wordt uw LA-of AI-cluster weer gegeven in het linkerdeel venster met uw systeem eigen ADX-cluster. 

    ![Log Analytics-en Azure Data Explorer-clusters](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Query's uitvoeren

U kunt Kusto Explorer, ADX Web Explorer, Jupyter Kqlmagic of REST API gebruiken om de proxy clusters op te vragen. 

> [!TIP]
> * De naam van de data base moet dezelfde naam hebben als de resource die is opgegeven in het proxy cluster. Namen zijn hoofdletter gevoelig.
> * In query's voor meerdere clusters moet u ervoor zorgen dat de naamgeving van Application Insights-apps en Log Analytics-werk ruimten juist is.
>     * Als namen speciale tekens bevatten, worden deze vervangen door URL-code ring in de naam van het proxy cluster. 
>     * Als namen tekens bevatten die niet voldoen aan de [KQL-id-naam regels](/azure/kusto/query/schema-entities/entity-names), worden deze **-** vervangen door het koppel teken.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Query uitvoeren op de systeem eigen Azure Data Explorer-cluster 

Query's uitvoeren op uw Azure Data Explorer-cluster (zoals *StormEvents* -tabel in *Help* -cluster). Wanneer u de query uitvoert, controleert u of uw systeem eigen Azure Data Explorer-cluster is geselecteerd in het linkerdeel venster.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Query StormEvents-tabel](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Query's uitvoeren op uw LA-of AI-cluster

Als u query's uitvoert op uw LA of AL-cluster, controleert u of uw LA-of AI-cluster is geselecteerd in het linkerdeel venster. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Werk ruimte query LA](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Een query uitvoeren op uw LA of AI-cluster vanuit de ADX-proxy  

Wanneer u query's uitvoert op uw LA-of AI-cluster vanuit de proxy, controleert u of uw ADX native-cluster is geselecteerd in het linkerdeel venster. In het volgende voor beeld wordt een query van de werk ruimte LA met het systeem eigen ADX-cluster gedemonstreerd

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Query vanuit Azure Data Explorer proxy](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Kruis query van LA of AI-cluster en het ADX-cluster van de ADX-proxy 

Wanneer u cross-cluster query's uitvoert vanuit de proxy, controleert u of uw systeem eigen ADX-cluster is geselecteerd in het linkerdeel venster. In de volgende voor beelden ziet u hoe u ADX `union`-cluster tabellen (met) combineert met de werk ruimte van La.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Kruis query van de Azure Data Explorer-proxy](media/adx-proxy/cross-query-adx-proxy.png)

Het gebruik van de [ `join` operator](/azure/kusto/query/joinoperator)in plaats van Union vereist mogelijk een hint om deze uit te voeren op een native Azure Data Explorer-cluster (en niet op de proxy). 

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