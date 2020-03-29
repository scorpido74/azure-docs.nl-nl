---
title: Azure-monitorlogboeken voor query's om Azure HDInsight-clusters te controleren
description: Meer informatie over het uitvoeren van query's in Azure Monitor-logboeken om taken te controleren die worden uitgevoerd in een HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803788"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure-monitorlogboeken voor query's om HDInsight-clusters te controleren

Meer informatie over het gebruik van Azure Monitor-logboeken om Azure HDInsight-clusters te controleren:

* [HDInsight-clusterstatistieken analyseren](#analyze-hdinsight-cluster-metrics)
* [Gebeurteniswaarschuwingen maken](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

U moet een HDInsight-cluster hebben geconfigureerd om Azure Monitor-logboeken te gebruiken en de HDInsight-clusterspecifieke Azure Monitor-logboekenbewakingsoplossingen aan de werkruimte hebben toegevoegd. Zie Azure [Monitor-logboeken gebruiken met HDInsight-clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md)voor instructies.

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight-clusterstatistieken analyseren

Meer informatie over het zoeken naar specifieke statistieken voor uw HDInsight-cluster.

1. Open de werkruimte Log Analytics die is gekoppeld aan uw HDInsight-cluster vanuit de Azure-portal.
1. Selecteer **Logboeken**onder **Algemeen**.
1. Typ de volgende query in het zoekvak om te zoeken naar alle statistieken voor alle beschikbare statistieken voor alle HDInsight-clusters die zijn geconfigureerd om Azure Monitor-logboeken te gebruiken en selecteer **Uitvoeren**. Bekijk de resultaten.

    ```kusto
    search *
    ```

    ![Apache Ambari analytics doorzoekt alle statistieken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Alle statistieken doorzoeken")

1. Selecteer in het linkermenu het tabblad **Filter.**

1. Selecteer **Heartbeat** **onder Type**. Selecteer vervolgens **Apply & Run**.

    ![log analytics zoeken naar specifieke statistieken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Zoeken naar specifieke statistieken")

1. De query in het tekstvak wordt gewijzigd in:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. U dieper graven met behulp van de opties die beschikbaar zijn in het linkermenu. Bijvoorbeeld:

    - Ga als lid van het werk om logboeken van een specifiek knooppunt te bekijken:

        ![Zoeken naar specifieke fouten uitvoer1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Zoeken naar specifieke fouten uitvoer1")

    - Ga als u op bepaalde tijdstippen logboeken bekijken:

        ![Zoeken naar specifieke fouten uitvoer2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Zoeken naar specifieke fouten uitvoer2")

1. Selecteer **Toepassen & Uitvoeren** en bekijk de resultaten. Houd er ook rekening mee dat de query is bijgewerkt naar:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Aanvullende voorbeeldquery's

Een voorbeeldquery op basis van het gemiddelde van resources die worden gebruikt in een interval van 10 minuten, gecategoriseerd op clusternaam:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

In plaats van te verfijnen op basis van het gemiddelde van de gebruikte resources, u de volgende query gebruiken om de resultaten te verfijnen op basis van het moment waarop de maximale resources zijn gebruikt (evenals 90e en 95e percentiel) in een venster van 10 minuten:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Waarschuwingen maken voor het bijhouden van gebeurtenissen

De eerste stap om een waarschuwing te maken is om te komen tot een query op basis waarvan de waarschuwing wordt geactiveerd. U elke query gebruiken die u wilt maken.

1. Open de werkruimte Log Analytics die is gekoppeld aan uw HDInsight-cluster vanuit de Azure-portal.
1. Selecteer **Logboeken**onder **Algemeen**.
1. Voer de volgende query uit waarop u een waarschuwing wilt maken en selecteer **Uitvoeren.**

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    De query bevat een lijst met mislukte toepassingen die worden uitgevoerd op HDInsight-clusters.

1. Selecteer **Nieuwe waarschuwingsregel** boven aan de pagina.

    ![Query invoeren om een waarschuwing te maken1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Query invoeren om een waarschuwing te maken1")

1. Voer in het venster **Regel maken** de query en andere details in om een waarschuwing te maken en selecteer vervolgens **Waarschuwingsregel maken**.

    ![Query invoeren om een waarschuwing te maken2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Query invoeren om een waarschuwing te maken2")

### <a name="edit-or-delete-an-existing-alert"></a>Een bestaande waarschuwing bewerken of verwijderen

1. Open de werkruimte Log Analytics vanuit de Azure-portal.

1. Selecteer In het linkermenu onder **Controleren**de optie **Waarschuwingen**.

1. Selecteer Naar boven in de optie **Waarschuwingsregels beheren**.

1. Selecteer de waarschuwing die u wilt bewerken of verwijderen.

1. U hebt de volgende opties: **Opslaan**, **Verwijderen**, **Uitschakelen**en **verwijderen**.

    ![HDInsight Azure Monitor registreert waarschuwing verwijderen bewerken](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Zie [Metrische waarschuwingen maken, weergeven en beheren met Azure Monitor](../azure-monitor/platform/alerts-metric.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

* [Aan de slag met logboekquery's in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Aangepaste weergaven maken met View Designer in Azure Monitor](../azure-monitor/platform/view-designer.md)
