---
title: Querygegevens in Azure Monitor met Azure Data Explorer (voorbeeld)
description: In dit onderwerp querygegevens in Azure Monitor door een Azure Data Explorer-proxy te maken voor kruisproductquery's met Toepassingsinzichten en Logboekanalyse
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560419"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Querygegevens in Azure Monitor met Azure Data Explorer (Voorbeeld)

Het Azure Data Explorer-proxycluster (ADX-proxy) is een entiteit waarmee u kruisproductquery's uitvoeren tussen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)en [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) in de [Azure Monitor-service.](/azure/azure-monitor/) U Azure Monitor Log Analytics-werkruimten of Application Insights-apps toewijzen als proxyclusters. U vervolgens het proxycluster opvragen met azure data explorer-hulpprogramma's en ernaar verwijzen in een clusterquery voor verschillende bestanden. In het artikel ziet u hoe u verbinding maken met een proxycluster, een proxycluster toevoegen aan azure data explorer webgebruikers en query's uitvoeren tegen uw AI-apps of LA-werkruimten vanuit Azure Data Explorer.

De proxystroom van Azure Data Explorer: 

![ADX-proxystroom](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> De ADX Proxy bevindt zich in de preview-modus. [Maak verbinding met de proxy](#connect-to-the-proxy) om de ADX-proxyfunctie voor uw clusters in te schakelen. Neem contact op met het [ADXProxy-team](mailto:adxproxy@microsoft.com) met vragen.

## <a name="connect-to-the-proxy"></a>Verbinding maken met de proxy

1. Controleer of uw azure data explorer-cluster (zoals *helpcluster)* in het linkermenu wordt weergegeven voordat u verbinding maakt met uw cluster Log Analytics of Application Insights.

    ![ADX-native cluster](media/adx-proxy/web-ui-help-cluster.png)

1. Selecteer Cluster toevoegen inhttps://dataexplorer.azure.com/clusters)de gebruikersinterface van Azure Data Explorer ( Selecteer **Cluster toevoegen**.

1. Voeg **in** het venster Cluster toevoegen de URL toe aan het LA- of AI-cluster. 
    
    * Voor LA:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Voor AI:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecteer **Toevoegen**.

    ![Cluster toevoegen](media/adx-proxy/add-cluster.png)

    Als u een verbinding toevoegt aan meer dan één proxycluster, geeft u elk een andere naam. Anders hebben ze allemaal dezelfde naam in het linkerdeelvenster.

1. Nadat de verbinding is gemaakt, wordt uw LA- of AI-cluster weergegeven in het linkerdeelvenster met uw oorspronkelijke ADX-cluster. 

    ![Clusters van Logboekanalyse en Azure Data Explorer](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Query's uitvoeren

U de query's uitvoeren met behulp van clienthulpprogramma's die Kusto-query's ondersteunen, zoals: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * De naam van de database moet dezelfde naam hebben als de resource die is opgegeven in het proxycluster. Namen zijn hoofdlettergevoelig.
> * Controleer in crossclusterquery's of de naamgeving van Application Insights-apps en Log Analytics-werkruimten juist is.
>     * Als namen speciale tekens bevatten, worden ze vervangen door URL-codering in de naam van het proxycluster. 
>     * Als namen tekens bevatten die niet voldoen aan de naamregels **-** van [de KQL-id,](/azure/kusto/query/schema-entities/entity-names)worden ze vervangen door het streepjesteken.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Directe query van uw LA- of AI ADX-proxycluster

Query's uitvoeren op uw LA- of AI-cluster. Controleer of uw cluster is geselecteerd in het linkerdeelvenster. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Query LA-werkruimte](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Kruisquery van uw LA- of AI ADX-proxycluster en het Native cluster van ADX 

Wanneer u crossclusterquery's uitvoert vanuit de proxy, controleert u of uw ADX-native cluster is geselecteerd in het linkerdeelvenster. In de volgende voorbeelden wordt aangetoond dat `union`ADX-clustertabellen (met behulp van) worden gecombineerd met DE-werkruimte.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Query's doorkruisen vanaf de proxy azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Als [ `join` ](/azure/kusto/query/joinoperator)u de operator gebruikt , [`hint`](/azure/kusto/query/joinoperator#join-hints) in plaats van unie, moet u deze mogelijk uitvoeren op een native cluster van Azure Data Explorer (en niet op de proxy). 

## <a name="additional-syntax-examples"></a>Aanvullende syntaxisvoorbeelden

De volgende syntaxisopties zijn beschikbaar bij het aanroepen van de clusters Application Insights (AI) of Log Analytics (LA):

|Syntaxisbeschrijving  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Database binnen een cluster die alleen de gedefinieerde bron in dit abonnement bevat **(aanbevolen voor crossclusterquery's)** |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster dat alle apps/werkruimten in dit abonnement bevat    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster dat alle apps/werkruimten in het abonnement bevat en lid is van deze brongroep    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster dat alleen de gedefinieerde bron in dit abonnement bevat      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Volgende stappen

[Query's schrijven](write-queries.md)
