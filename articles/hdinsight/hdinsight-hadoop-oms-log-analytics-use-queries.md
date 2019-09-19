---
title: Query's uitvoeren op Azure Monitor logboeken voor het bewaken van Azure HDInsight-clusters
description: Meer informatie over het uitvoeren van query's in Azure Monitor logboeken voor het bewaken van taken die worden uitgevoerd in een HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103509"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor logboeken voor het controleren van HDInsight-clusters doorzoeken

Meer informatie over de basis scenario's voor het gebruik van Azure Monitor logboeken voor het bewaken van Azure HDInsight-clusters:

* [Metrische gegevens van HDInsight-cluster analyseren](#analyze-hdinsight-cluster-metrics)
* [Specifieke logboek berichten zoeken](#search-for-specific-log-messages)
* [Gebeurtenis waarschuwingen maken](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

U moet een HDInsight-cluster hebben geconfigureerd om Azure Monitor-logboeken te gebruiken en u hebt de HDInsight-cluster-specifieke Azure Monitor logboeken voor het controleren van oplossingen toegevoegd aan de werk ruimte. Zie [Azure monitor-Logboeken gebruiken met HDInsight-clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md)voor instructies.

## <a name="analyze-hdinsight-cluster-metrics"></a>Metrische gegevens van HDInsight-cluster analyseren

Meer informatie over het zoeken naar specifieke metrische gegevens voor uw HDInsight-cluster.

1. Open de Log Analytics-werk ruimte die is gekoppeld aan uw HDInsight-cluster via de Azure Portal.
1. Selecteer de tegel **Zoeken in Logboeken** .
1. Typ de volgende query in het zoekvak om te zoeken naar alle metrische gegevens voor alle beschik bare gegevens voor alle HDInsight-clusters die zijn geconfigureerd voor het gebruik van Azure Monitor logboeken en selecteer vervolgens **uitvoeren**.

        search *

    ![Apache Ambari Analytics alle metrische gegevens doorzoeken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Alle metrische gegevens zoeken")

    De uitvoer ziet er als volgt uit:

    ![log Analytics doorzoeken alle metrische gegevens](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Zoeken in alle metrische gegevens uitvoer")

1. Selecteer in het linkerdeel venster onder **type**de waarde die u wilt dieper in en selecteer vervolgens **Toep assen**. Op de volgende scherm afbeelding `metrics_resourcemanager_queue_root_default_CL` ziet u het type geselecteerd.

    > [!NOTE]  
    > Mogelijk moet u de knop **[+] meer** selecteren om de metrische gegevens te vinden die u zoekt. De knop **Toep assen** bevindt zich ook onder aan de lijst, dus u moet omlaag schuiven om deze weer te geven.

    U ziet dat de query in het tekstvak verandert in een van de weer gegeven in het gemarkeerde vak in de volgende scherm afbeelding:

    ![log Analytics-specifieke metrische gegevens zoeken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Specifieke metrische gegevens zoeken")

1. Om te dieper in deze specifieke metriek. U kunt bijvoorbeeld de bestaande uitvoer verfijnen op basis van het gemiddelde van de resources die worden gebruikt in een interval van 10 minuten, gecategoriseerd op cluster naam met behulp van de volgende query:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. In plaats van te verfijnen op basis van het gemiddelde van gebruikte resources, kunt u de volgende query gebruiken om de resultaten te verfijnen op basis van de gebruikte maximum bronnen (evenals negen tigste en 95e percentiel) in een venster van 10 minuten:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Specifieke logboek berichten zoeken

Meer informatie over het opzoeken van fout berichten tijdens een bepaald tijd venster. Hier volgen enkele voor beelden van hoe u kunt aankomen bij het fout bericht dat u geïnteresseerd bent. U kunt elke eigenschap gebruiken die beschikbaar is om te zoeken naar de fouten die u probeert te vinden.

1. Open de Log Analytics-werk ruimte die is gekoppeld aan uw HDInsight-cluster via de Azure Portal.
2. Selecteer de tegel **Zoeken in Logboeken** .
3. Typ de volgende query om te zoeken naar alle fout berichten voor alle HDInsight-clusters die zijn geconfigureerd voor het gebruik van Azure Monitor logboeken en selecteer vervolgens **uitvoeren**.

         search "Error"

    U ziet een uitvoer zoals de volgende uitvoer:

    ![Azure Portal fouten in logboek zoeken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Uitvoer van alle fouten zoeken")

4. Selecteer in het linkerdeel venster onder **type** -categorie een fout type dat u wilt dieper in en selecteer vervolgens **Toep assen**.  U ziet dat de resultaten worden verfijnd om alleen de fout van het geselecteerde type weer te geven.

5. U kunt met behulp van de beschik bare opties in het linkerdeel venster dieper in deze specifieke fout lijst gaan. Bijvoorbeeld:

    - Fout berichten van een specifiek worker-knoop punt weer geven:

        ![Zoeken naar specifieke fouten output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Zoeken naar specifieke fouten output1")

    - Er is een fout opgetreden op een bepaald moment:

        ![Zoeken naar specifieke fouten output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Zoeken naar specifieke fouten output2")

6. Om de specifieke fout weer te geven. U kunt **[+] meer weer geven** selecteren om het werkelijke fout bericht te bekijken.

    ![Zoeken naar specifieke fouten output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Zoeken naar specifieke fouten output3")

## <a name="create-alerts-for-tracking-events"></a>Waarschuwingen voor het bijhouden van gebeurtenissen maken

De eerste stap bij het maken van een waarschuwing is het aanroepen van een query op basis waarvan de waarschuwing wordt geactiveerd. U kunt elke query gebruiken waarvoor u een waarschuwing wilt maken.

1. Open de Log Analytics-werk ruimte die is gekoppeld aan uw HDInsight-cluster via de Azure Portal.
2. Selecteer de tegel **Zoeken in Logboeken** .
3. Voer de volgende query uit waarop u een waarschuwing wilt maken en selecteer vervolgens **uitvoeren**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    De query bevat een lijst met mislukte toepassingen die worden uitgevoerd op HDInsight-clusters.

4. Selecteer **nieuwe waarschuwings regel** boven aan de pagina.

    ![Voer een query in om een alert1 te maken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Voer een query in om een alert1 te maken")

5. Voer in het venster **regel maken** de query en andere details in om een waarschuwing te maken en selecteer vervolgens **waarschuwings regel maken**.

    ![Voer een query in om een alert2 te maken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Voer een query in om een alert2 te maken")

Een bestaande waarschuwing bewerken of verwijderen:

1. Open de werk ruimte Log Analytics vanuit de Azure Portal.
2. Selecteer in het menu links de optie **waarschuwing**.
3. Selecteer de waarschuwing die u wilt bewerken of verwijderen.
4. U hebt de volgende opties: **Opslaan**, **verwijderen**, **uitschakelen**en **verwijderen**.

    ![Waarschuwing voor verwijderen van HDInsight-Azure Monitor logboeken](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/platform/alerts-metric.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

* [Aangepaste weer gaven maken met behulp van de weer gave designer in Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Metrische waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/platform/alerts-metric.md)
