---
title: Kernels voor Jupyter notebook in Spark-clusters in azure HDInsight
description: Meer informatie over de PySpark-, PySpark3-en Spark-kernels voor Jupyter notebook beschikbaar met Spark-clusters in azure HDInsight.
keywords: jupyter notebook op Spark, jupyter Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 44089ea4b997e06cb7654fc6665a1a9a59ae2658
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389700"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels voor Jupyter notebook in Apache Spark-clusters in azure HDInsight

HDInsight Spark-clusters bieden kernels die u kunt gebruiken met de Jupyter notebook van [Apache Spark](https://spark.apache.org/) voor het testen van uw toepassingen. Een kernel is een programma dat uw code uitvoert en interpreteert. De drie kernels zijn:

- **PySpark** -voor toepassingen die zijn geschreven in Python2.
- **PySpark3** -voor toepassingen die zijn geschreven in Python3.
- **Spark** -voor toepassingen die zijn geschreven in scala.

In dit artikel leert u hoe u deze kernels en de voor delen van het gebruik ervan kunt gebruiken.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Een Jupyter-notebook maken op Spark HDInsight

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw Spark-cluster.  Zie de [lijst en clusters weer geven](../hdinsight-administer-use-portal-linux.md#showClusters) voor de instructies. De **overzichts** weergave wordt geopend.

2. Selecteer in de weer gave **overzicht** in het vak **cluster dashboards** de optie **Jupyter notebook**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    ![Jupyter notebook op Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter notebook in Spark")
  
   > [!NOTE]  
   > U kunt ook het Jupyter-notebook in het Spark-cluster bereiken door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selecteer **Nieuw**en selecteer vervolgens **Pyspark**, **PySpark3**of **Spark** om een notitie blok te maken. Gebruik de Spark-kernel voor scala-toepassingen, PySpark-kernel voor Python2-toepassingen en PySpark3 kernel voor Python3-toepassingen.

    ![Kernels voor Jupyter notebook in Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernels voor Jupyter notebook in Spark")

4. Er wordt een notitie blok geopend met de kernel die u hebt geselecteerd.

## <a name="benefits-of-using-the-kernels"></a>Voor delen van het gebruik van de kernels

Hier volgen enkele voor delen van het gebruik van de nieuwe kernels met Jupyter notebook in Spark HDInsight-clusters.

- **Vooraf ingestelde contexten**. Met **PySpark**, **PySpark3**of **Spark** -kernels hoeft u de Spark-of Hive-contexten niet expliciet in te stellen voordat u met uw toepassingen begint te werken. Deze zijn standaard beschikbaar. Deze context zijn:

  - **sc** -voor Spark-context
  - **sqlContext** -voor Hive-context

    U hoeft dus geen instructies uit te voeren zoals de volgende om de contexten in te stellen:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    In plaats daarvan kunt u rechtstreeks de vooraf ingestelde contexten in uw toepassing gebruiken.

- **Cel magics**. De PySpark-kernel biedt enkele vooraf gedefinieerde ' magics '. Dit zijn speciale opdrachten die u kunt aanroepen met `%%` (bijvoorbeeld `%%MAGIC` `<args>`). De Magic-opdracht moet het eerste woord in een code-cel zijn en meerdere regels inhoud toestaan. Het Magic-woord moet het eerste woord in de cel zijn. Het toevoegen van iets vóór het magische, zelfs opmerkingen, veroorzaakt een fout.     Zie [hier](https://ipython.readthedocs.org/en/stable/interactive/magics.html)voor meer informatie over magics.

    De volgende tabel geeft een lijst van de verschillende magics die beschikbaar zijn via de kernels.

   | Magische | Voorbeeld | Beschrijving |
   | --- | --- | --- |
   | Help |`%%help` |Hiermee wordt een tabel met alle beschik bare magics gegenereerd met voor beeld en beschrijving |
   | info |`%%info` |Hiermee worden sessie gegevens voor het huidige livy-eind punt uitgevoerd |
   | Configureren |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Hiermee configureert u de para meters voor het maken van een sessie. De geforceerde vlag (-f) is verplicht als er al een sessie is gemaakt. Hiermee zorgt u ervoor dat de sessie wordt verwijderd en opnieuw wordt gemaakt. Bekijk [de hoofd tekst van de LIVY post/Sessions](https://github.com/cloudera/livy#request-body) voor een lijst met geldige para meters. Para meters moeten worden door gegeven als een JSON-teken reeks en moeten zich op de volgende regel na de Magic bevallen, zoals wordt weer gegeven in de kolom voor beeld. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |Voert een Hive-query op de sqlContext. Als de para meter `-o` wordt door gegeven, wordt het resultaat van de query persistent gemaakt in de lokale python-context%% als een [Panda](https://pandas.pydata.org/) data frame. |
   | lokale |`%%local`<br>`a=1` |Alle code in de volgende regels wordt lokaal uitgevoerd. De code moet een geldige Python2-code zijn, zelfs als de kernel die u gebruikt. , Zelfs als u **PySpark3** of **Spark** -kernels hebt geselecteerd tijdens het maken van het notitie blok en u de `%%local` Magic in een cel gebruikt, mag die cel alleen een geldige Python2-code hebben. |
   | logboeken |`%%logs` |Hiermee worden de logboeken voor de huidige livy-sessie uitgevoerd. |
   | delete |`%%delete -f -s <session number>` |Hiermee verwijdert u een specifieke sessie van het huidige livy-eind punt. U kunt de sessie die voor de kernel zelf wordt gestart, niet verwijderen. |
   | opruimen |`%%cleanup -f` |Hiermee verwijdert u alle sessies voor het huidige livy-eind punt, inclusief de sessie van dit notitie blok. De Force Flag-f is verplicht. |

   > [!NOTE]  
   > Naast de magics die door de PySpark-kernel worden toegevoegd, kunt u ook de [ingebouwde IPython magics](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)gebruiken, met inbegrip van `%%sh`. U kunt de `%%sh` Magic gebruiken om scripts en blok code uit te voeren op het cluster hoofd knooppunt.

- **Automatische visualisatie**. De Pyspark-kernel visualiseert automatisch de uitvoer van Hive-en SQL-query's. U kunt kiezen uit verschillende soorten visualisaties, waaronder tabel, cirkel, lijn, vlak, staaf.

## <a name="parameters-supported-with-the-sql-magic"></a>Para meters die worden ondersteund met%% SQL Magic

De `%%sql` Magic ondersteunt verschillende para meters die u kunt gebruiken om het soort uitvoer te bepalen dat u ontvangt wanneer u query's uitvoert. De volgende tabel bevat de uitvoer.

| Parameter | Voorbeeld | Beschrijving |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Gebruik deze para meter om het resultaat van de query op te slaan in de context%% Local python als een [Panda](https://pandas.pydata.org/) data frame. De naam van de variabele data frame is de naam van de variabele die u opgeeft. |
| -q |`-q` |Gebruik deze optie om visualisaties voor de cel uit te scha kelen. Als u de inhoud van een cel niet wilt visualiseren en deze alleen wilt vastleggen als een data frame, gebruikt u `-q -o <VARIABLE>`. Als u visualisaties wilt uitschakelen zonder de resultaten vast te leggen (bijvoorbeeld voor het uitvoeren van een SQL-query, zoals een `CREATE TABLE`-instructie), gebruikt u `-q` zonder een `-o` argument op te geven. |
| -m |`-m <METHOD>` |Waarbij de **methode** ofwel **nemen** of voor **beeld** is (de standaard instelling **is).** Als de methode wordt **toegepast**, worden de elementen van de top van de resultatenset OPGEHAALD door MaxRows (verderop in deze tabel beschreven). Als de methode voor **beeld**is, wordt de kernel wille keurig voor beelden van elementen van de gegevensset op basis van `-r` para meter, zoals wordt beschreven in de volgende tabel. |
| -r |`-r <FRACTION>` |Hier is een **fractie** een getal met drijvende komma tussen 0,0 en 1,0. Als de voorbeeld methode voor de SQL-query `sample`is, wordt in de kernel wille keurig de opgegeven Fractie van de onderdelen van de resultatenset voor u weer gegeven. Als u bijvoorbeeld een SQL-query uitvoert met de argumenten `-m sample -r 0.01`, worden 1% van de resultaat rijen wille keurig van een steek proef genomen. |
| -n |`-n <MAXROWS>` |**MaxRows** is een geheel getal. De kernel beperkt het aantal uitvoer rijen tot **MaxRows**. Als **MaxRows** een negatief getal is, zoals **-1**, is het aantal rijen in de resultatenset niet beperkt. |

**Voorbeeld:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

De bovenstaande instructie doet het volgende:

- Hiermee worden alle records uit **hivesampletable**geselecteerd.
- Omdat we-q gebruiken, wordt de functie voor autovisualisatie uitgeschakeld.
- Omdat we `-m sample -r 0.1 -n 500` wille keurig 10% van de rijen in het hivesampletable gebruiken en de grootte van de resultatenset beperkt tot 500 rijen.
- Ten slotte, omdat we `-o query2`, wordt de uitvoer ook opgeslagen in een data frame met de naam **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Overwegingen bij het gebruik van de nieuwe kernels

Welke kernel u gebruikt, waardoor de notitie blokken die worden uitgevoerd, verbruikt de cluster resources.  Met deze kernels, omdat de contexten vooraf worden ingesteld, wordt door het afsluiten van de notitie blokken de context niet afgebroken en worden de cluster bronnen in gebruik genomen. Het is een goed idee om de optie **sluiten en stoppen** te gebruiken in het menu **bestand** van het notitie blok wanneer u klaar bent met het notitie blok, waardoor de context wordt beëindigd en vervolgens het notitie blok wordt gesloten.

## <a name="where-are-the-notebooks-stored"></a>Waar worden de notitie blokken opgeslagen?

Als uw cluster Azure Storage als standaard-opslag account gebruikt, worden Jupyter-notebooks opgeslagen in het opslag account in de map **/HdiNotebooks** .  Notebooks, tekst bestanden en mappen die u maakt vanuit Jupyter zijn toegankelijk vanuit het opslag account.  Als u bijvoorbeeld Jupyter gebruikt om een map **MyFolder** en een notebook **MyFolder/mynotebook. ipynb**te maken, hebt u toegang tot dat notebook op `/HdiNotebooks/myfolder/mynotebook.ipynb` binnen het opslag account.  Het omgekeerde is ook waar, dat wil zeggen, als u een notitie blok rechtstreeks naar uw opslag account uploadt op `/HdiNotebooks/mynotebook1.ipynb`, het notitie blok ook zichtbaar is vanuit Jupyter.  Notebooks blijven aanwezig in het opslag account, zelfs nadat het cluster is verwijderd.

> [!NOTE]  
> HDInsight-clusters met Azure Data Lake Storage als standaard opslag slaan geen notitie blokken op in gekoppelde opslag.

De manier waarop notitie blokken worden opgeslagen in het opslag account, is compatibel met [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Als u dus SSH in het cluster, kunt u de opdrachten voor bestands beheer gebruiken, zoals wordt weer gegeven in het volgende code fragment:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Ongeacht of het cluster Azure Storage of Azure Data Lake Storage gebruikt als het standaard opslag account, worden de notitie blokken ook opgeslagen op het cluster hoofd knooppunt op `/var/lib/jupyter`.

## <a name="supported-browser"></a>Ondersteunde browser

Jupyter-notebooks op Spark HDInsight-clusters worden alleen ondersteund op Google Chrome.

## <a name="feedback"></a>Feedback

De nieuwe kernels bevinden zich in de ontwikkelings fase en zullen gedurende een periode vervallen. Dit kan ook betekenen dat Api's kunnen veranderen als deze kernels rijp zijn. We waarderen de feedback die u hebt bij het gebruik van deze nieuwe kernels. Dit is handig bij het vorm geven van de definitieve versie van deze kernels. U kunt uw opmerkingen/feedback in het gedeelte **feedback** onder aan dit artikel laten staan.

## <a name="seealso"></a>Zie ook

- [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario 's

- [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
- [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
- [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

- [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
- [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

- [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
- [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
- [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
- [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

- [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
- [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
