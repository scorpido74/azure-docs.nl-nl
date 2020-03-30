---
title: Apache Hive-gegevens visualiseren met Power BI - Azure HDInsight
description: Meer informatie over het gebruik van Microsoft Power BI om Hive-gegevens te visualiseren die door Azure HDInsight zijn verwerkt.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/02/2020
ms.openlocfilehash: d9b64785dbd82842479eb3f313b8394f9f25b40b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369995"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Apache Hive-gegevens visualiseren met Microsoft Power BI met behulp van ODBC in Azure HDInsight

Meer informatie over het verbinden van Microsoft Power BI Desktop met Azure HDInsight met ODBC en het visualiseren van Apache Hive-gegevens.

> [!IMPORTANT]
> U de Hive ODBC-driver gebruiken om te importeren via de generieke ODBC-connector in Power BI Desktop. Het wordt echter niet aanbevolen voor BI-workloads die niet-interactief zijn van de Hive-queryengine. [HDInsight Interactive Query-connector](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) en [HDInsight Spark-connector](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) zijn betere keuzes voor hun prestaties.

In dit artikel laadt u `hivesampletable` de gegevens uit een Hive-tabel naar Power BI. De Hive-tabel bevat enkele gegevens over het gebruik van mobiele telefoons. Vervolgens zet u de gebruiksgegevens op een wereldkaart in kaart:

![HDInsight Power BI het kaartrapport](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

De informatie is ook van toepassing op het nieuwe [clustertype Interactieve query.](../interactive-query/apache-interactive-query-get-started.md) Zie [Interactieve Queryhivegegevens visualiseren met Microsoft Power BI met directe query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)voor het maken van verbinding met hdInsight Interactive Query.

## <a name="prerequisites"></a>Vereisten

Voordat je door dit artikel gaat, moet je de volgende items hebben:

* HDInsight-cluster. Het cluster kan een HDInsight-cluster zijn met Hive of een nieuw uitgebracht cluster voor interactieve query's. Zie [Cluster maken](apache-hadoop-linux-tutorial-get-started.md)voor het maken van clusters.

* [Microsoft Power BI-bureaublad](https://powerbi.microsoft.com/desktop/). U een kopie downloaden van het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=45331)

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-gegevensbron maken

Zie [Hive ODBC-gegevensbron maken](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Gegevens van HDInsight laden

De **hivesampletable** Hive-tabel wordt geleverd met alle HDInsight-clusters.

1. Start Power BI Desktop.

1. Navigeer in het bovenste menu naar **Home** > **Get Data** > **More...**.

    ![HDInsight Excel Power BI open data](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Selecteer **in** het dialoogvenster Gegevens ophalen de optie **Andere** van links, selecteer **ODBC** rechts en selecteer **Verbinding** onderin.

1. Selecteer in het dialoogvenster **Van ODBC** de gegevensbronnaam die u in de laatste sectie hebt gemaakt in de vervolgkeuzelijst. Selecteer vervolgens **OK**.

1. Voor het eerste gebruik wordt een **ODBC-stuurprogrammadialoogvenster** geopend. Selecteer **Standaard of Aangepast** in het linkermenu. Selecteer vervolgens **Verbinding maken** om Navigator te **openen**.

1. Vouw in het dialoogvenster **Navigator** **ODBC-> HIVE uit > standaard,** selecteer **hivesampletabel**en selecteer **Laden**.

## <a name="visualize-data"></a>Gegevens visualiseren

Vervolg vanaf de laatste procedure.

1. Selecteer In het deelvenster Visualisaties de optie **Kaart**, het is een wereldbolpictogram.

    ![HDInsight Power BI past rapport aan](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. Selecteer **land** en **apparaatmake**in het deelvenster **Velden.** U de gegevens op de kaart zien.

1. Vouw de kaart uit.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u gegevens van HDInsight visualiseren met Power BI.  Zie voor meer informatie de volgende artikelen:

* [Gebruik Apache Zeppelin om Apache Hive-query's uit te voeren in Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Verbind Excel met HDInsight met de Microsoft Hive ODBC-driver](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Sluit Excel aan op Apache Hadoop met Power Query](apache-hadoop-connect-excel-power-query.md).
* [Maak verbinding met Azure HDInsight en voer Apache Hive-query's uit met Data Lake Tools voor Visual Studio.](apache-hadoop-visual-studio-tools-get-started.md)
* [Gebruik Azure HDInsight Tool voor Visual Studio Code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight.](./../hdinsight-upload-data.md)
