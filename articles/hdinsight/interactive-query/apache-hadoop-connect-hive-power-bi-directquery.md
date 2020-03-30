---
title: Zie Interactive Query Hive-gegevens met Power BI in Azure HDInsight
description: Microsoft Power BI gebruiken om interactieve queryhive-gegevens van Azure HDInsight te visualiseren
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367989"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Interactieve Query Apache Hive-gegevens visualiseren met Microsoft Power BI met behulp van directe query in HDInsight

In dit artikel wordt beschreven hoe u Microsoft Power BI verbinden met Azure HDInsight Interactive Query-clusters en Apache Hive-gegevens visualiseren met behulp van directe query. In het voorbeeld worden `hivesampletable` de gegevens van een Hive-tabel geladen naar Power BI. De `hivesampletable` Hive-tabel bevat enkele gegevens over het gebruik van mobiele telefoons. Vervolgens zet u de gebruiksgegevens op een wereldkaart in kaart:

![HDInsight Power BI het kaartrapport](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

U de [Apache Hive ODBC-driver](../hadoop/apache-hadoop-connect-hive-power-bi.md) gebruiken om te importeren via de generieke ODBC-connector in Power BI Desktop. Het wordt echter niet aanbevolen voor BI-workloads die niet-interactief zijn van de Hive-queryengine. [HDInsight Interactive Query-connector](./apache-hadoop-connect-hive-power-bi-directquery.md) en [HDInsight Apache Spark-connector](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) zijn betere keuzes voor hun prestaties.

## <a name="prerequisites"></a>Vereisten
Voordat je door dit artikel gaat, moet je de volgende items hebben:

* **HDInsight-cluster**. Het cluster kan een HDInsight-cluster zijn met Apache Hive of een nieuw uitgebracht cluster voor interactieve query's. Zie [Cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md)voor het maken van clusters.
* **[Microsoft Power BI-bureaublad](https://powerbi.microsoft.com/desktop/)**. U een kopie downloaden van het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=45331)

## <a name="load-data-from-hdinsight"></a>Gegevens van HDInsight laden

De `hivesampletable` Hive-tafel wordt geleverd met alle HDInsight-clusters.

1. Start Power BI Desktop.

2. Navigeer via de menubalk naar **Home** > **Get Data** > **More...**.

    ![HDInsight Power BI Meer gegevens](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Voer in het venster **Gegevens ophalen** **hdinsight** in in het zoekvak.  

4. Selecteer **hdinsight interactieve query**in de zoekresultaten en selecteer **Verbinding maken**.  Als u **HDInsight Interactive Query**niet ziet, moet u uw Power BI-bureaublad bijwerken naar de nieuwste versie.

5. Selecteer **Doorgaan** om het dialoogvenster **Verbinding maken met een service van derden te** sluiten.

6. Voer in het venster **HDInsight Interactive Query** de volgende informatie in en selecteer **OK:**

    |Eigenschap | Waarde |
    |---|---|
    |server |Voer de clusternaam in, bijvoorbeeld *myiqcluster.azurehdinsight.net*.|
    |Database |Voer **de standaardinstelling** in voor dit artikel.|
    |Gegevensverbindingsmodus |Selecteer **DirectQuery** voor dit artikel.|

    ![HDInsight interactieve query Power BI DirectQuery connect](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Voer de HTTP-referenties in en selecteer **Verbinding maken**. De standaardgebruikersnaam is **beheerder**.

8. Selecteer in het **navigatorvenster** in het linkerdeelvenster de optie **hivesampletale**.

9. Selecteer **Laden** in het hoofdvenster.

    ![HDInsight interactieve query Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Gegevens op een kaart visualiseren

Vervolg vanaf de laatste procedure.

1. Selecteer In het deelvenster Visualisaties de optie **Kaart**, het wereldbolpictogram. Een generieke kaart verschijnt dan in het hoofdvenster.

    ![HDInsight Power BI past rapport aan](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Selecteer **land** en **apparaatmake**in het deelvenster Velden. Een wereldkaart met de gegevenspunten verschijnt in het hoofdvenster na een paar ogenblikken.

3. Vouw de kaart uit.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u gegevens van HDInsight visualiseren met Behulp van Microsoft Power BI.  Zie de volgende artikelen voor meer informatie over gegevensvisualisatie:

* [Visualiseer Apache Hive-gegevens met Microsoft Power BI met ODBC in Azure HDInsight.](../hadoop/apache-hadoop-connect-hive-power-bi.md) 
* [Gebruik Apache Zeppelin om Apache Hive-query's uit te voeren in Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Verbind Excel met HDInsight met de Microsoft Hive ODBC-driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Sluit Excel aan op Apache Hadoop met Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Maak verbinding met Azure HDInsight en voer Apache Hive-query's uit met Data Lake Tools voor Visual Studio.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Gebruik Azure HDInsight Tool voor Visual Studio Code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight.](./../hdinsight-upload-data.md)
