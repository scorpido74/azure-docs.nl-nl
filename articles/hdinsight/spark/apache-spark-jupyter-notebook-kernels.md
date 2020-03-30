---
title: Kernels voor Jupyter-notitieblok op Spark-clusters in Azure HDInsight
description: Meer informatie over de PySpark-, PySpark3- en Spark-kernels voor Jupyter-laptop die beschikbaar zijn met Spark-clusters op Azure HDInsight.
keywords: jupyter notebook op vonk, jupyter vonk
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/20/2020
ms.openlocfilehash: a04b8fee31ffa5280bc8ad0fca35495bb87e0e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064469"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels voor Jupyter-laptop op Apache Spark-clusters in Azure HDInsight

HDInsight Spark-clusters bieden kernels die u gebruiken met de Jupyter-notebook op [Apache Spark](./apache-spark-overview.md) voor het testen van uw toepassingen. Een kernel is een programma dat uw code uitvoert en interpreteert. De drie kernels zijn:

- **PySpark** - voor toepassingen geschreven in Python2.
- **PySpark3** - voor toepassingen geschreven in Python3.
- **Spark** - voor toepassingen geschreven in Scala.

In dit artikel leert u hoe u deze kernels gebruiken en de voordelen van het gebruik ervan.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Maak een Jupyter-laptop op Spark HDInsight

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw Spark-cluster.  Zie [Clusters weergeven en weergeven](../hdinsight-administer-use-portal-linux.md#showClusters) voor de instructies. De **overzichtsweergave** wordt geopend.

2. Selecteer in de **weergave Overzicht** in het vak **Clusterdashboards** de optie **Jupyter-notitieblok**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    ![Jupyter notebook op Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter-laptop op Spark")
  
   > [!NOTE]  
   > U ook het Jupyter-notitieblok op het Spark-cluster bereiken door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selecteer **Nieuw**en selecteer **vervolgens Pyspark,** **PySpark3**of **Spark** om een notitieblok te maken. Gebruik de Spark-kernel voor Scala-toepassingen, PySpark-kernel voor Python2-toepassingen en PySpark3-kernel voor Python3-toepassingen.

    ![Kernels voor Jupyter-laptop op Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernels voor Jupyter-laptop op Spark")

4. Er wordt een notitieblok geopend met de kernel die u hebt geselecteerd.

## <a name="benefits-of-using-the-kernels"></a>Voordelen van het gebruik van de kernels

Hier zijn een paar voordelen van het gebruik van de nieuwe kernels met Jupyter notebook op Spark HDInsight clusters.

- **Vooraf ingestelde contexten**. Met **PySpark,** **PySpark3**of de **Spark-kernels** hoeft u de Spark- of Hive-contexten niet expliciet in te stellen voordat u met uw toepassingen gaat werken. Deze zijn standaard beschikbaar. Deze contexten zijn:

  - **sc** - voor Spark-context
  - **sqlContext** - voor Hive-context

    U hoeft dus **geen** instructies als de volgende uit te voeren om de contexten in te stellen:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    In plaats daarvan u de vooraf ingestelde contexten in uw toepassing direct gebruiken.

- **Cel magie.** De PySpark-kernel biedt een aantal vooraf gedefinieerde magics, die `%%` speciale opdrachten `%%MAGIC` `<args>`zijn waarmee u bellen (bijvoorbeeld). De magische opdracht moet het eerste woord in een codecel zijn en meerdere regels inhoud mogelijk maken. Het toverwoord moet het eerste woord in de cel zijn. Het toevoegen van iets voor de magie, zelfs opmerkingen, veroorzaakt een fout.     Voor meer informatie over magie, zie [hier](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    In de volgende tabel worden de verschillende magics weergegeven die beschikbaar zijn via de kernels.

   | Magic | Voorbeeld | Beschrijving |
   | --- | --- | --- |
   | Help |`%%help` |Genereert een tabel met alle beschikbare magie met voorbeeld en beschrijving |
   | Info |`%%info` |Sessiegegevens voor het huidige Livy-eindpunt uitvoergeven |
   | configureren |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Hiermee configureert u de parameters voor het maken van een sessie. De krachtvlag (-f) is verplicht als er al een sessie is gemaakt, waardoor de sessie wordt verwijderd en opnieuw wordt gemaakt. Kijk naar [Livy's POST / sessies Request Body](https://github.com/cloudera/livy#request-body) voor een lijst van geldige parameters. Parameters moeten worden doorgegeven als een JSON-tekenreeks en moeten na de magie op de volgende regel staan, zoals in de voorbeeldkolom wordt weergegeven. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Hiermee wordt een Hive-query uitgevoerd tegen de sqlContext. Als `-o` de parameter wordt doorgegeven, blijft het resultaat van de query bestaan in de context %%local Python als een [Pandas-gegevensframe.](https://pandas.pydata.org/) |
   | lokaal |`%%local`<br>`a=1` |Alle code in volgende regels wordt lokaal uitgevoerd. Code moet geldig zijn Python2 code, zelfs ongeacht de kernel die u gebruikt. Dus zelfs als u **PySpark3-** of **Spark-kernels** hebt `%%local` geselecteerd tijdens het maken van het notitieblok, moet die cel alleen geldige Python2-code hebben als u de magie in een cel gebruikt. |
   | logboeken |`%%logs` |De logboeken voor de huidige Livy-sessie worden uitgevoerd. |
   | delete |`%%delete -f -s <session number>` |Hiermee verwijdert u een specifieke sessie van het huidige Livy-eindpunt. U de sessie die voor de kernel zelf is gestart, niet verwijderen. |
   | opruimen |`%%cleanup -f` |Hiermee verwijdert u alle sessies voor het huidige Livy-eindpunt, inclusief de sessie van dit notitieblok. De krachtvlag -f is verplicht. |

   > [!NOTE]  
   > Naast de magie die door de PySpark-kernel wordt toegevoegd, u ook `%%sh`de [ingebouwde IPython-magie](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)gebruiken, waaronder. U `%%sh` de magie gebruiken om scripts en codeteblokkeren op de clusterheadnode uit te voeren.

- **Automatische visualisatie**. De Pyspark-kernel visualiseert automatisch de uitvoer van Hive- en SQL-query's. U kiezen uit verschillende typen visualisaties, waaronder Tabel, Cirkel, Lijn, Gebied, Balk.

## <a name="parameters-supported-with-the-sql-magic"></a>Parameters ondersteund met de %%sql-magie

De `%%sql` magie ondersteunt verschillende parameters die u gebruiken om het soort uitvoer te bepalen dat u ontvangt wanneer u query's uitvoert. In de volgende tabel worden de uitvoer weergegeven.

| Parameter | Voorbeeld | Beschrijving |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Gebruik deze parameter om het resultaat van de query in de context %%local Python als een [Pandas-gegevensframe](https://pandas.pydata.org/) voort te houden. De naam van de variabele dataframe is de variabele naam die u opgeeft. |
| -q |`-q` |Gebruik dit om visualisaties voor de cel uit te schakelen. Als u de inhoud van een cel niet automatisch wilt visualiseren en deze gewoon `-q -o <VARIABLE>`als gegevensframe wilt vastleggen, gebruikt u . Als u visualisaties wilt uitschakelen zonder de resultaten vast te leggen (bijvoorbeeld `CREATE TABLE` voor `-q` het uitvoeren `-o` van een SQL-query, zoals een instructie), gebruikt u deze zonder een argument op te geven. |
| -m |`-m <METHOD>` |Wanneer **METHODE** is ofwel **nemen** of **monster** (standaard is **nemen).** Als de methode wordt **gebruikt,** haalt de kernel elementen uit de bovenkant van de resultaatgegevensset die is opgegeven door MAXROWS (later beschreven in deze tabel). Als de methode **een monster**is, monsters de kernel `-r` willekeurig elementen van de gegevensset op basis van parameter, die vervolgens in deze tabel wordt beschreven. |
| -r |`-r <FRACTION>` |Hier **is FRACTION** een floating-point getal tussen 0,0 en 1,0. Als de voorbeeldmethode voor `sample`de SQL-query is, wordt in de kernel willekeurig de opgegeven fractie van de elementen van de resultaatset voor u gesampled. Als u bijvoorbeeld een SQL-query `-m sample -r 0.01`uitvoert met de argumenten, wordt 1% van de resultaatrijen willekeurig bemonsterd. |
| -n |`-n <MAXROWS>` |**MAXROWS** is een gehele waarde. De kernel beperkt het aantal uitvoerrijen tot **MAXROWS**. Als **MAXROWS** een negatief getal is, zoals **-1,** is het aantal rijen in de resultaatset niet beperkt. |

**Voorbeeld:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

De bovenstaande verklaring doet het volgende:

- Hiermee selecteert u alle records uit **de hivesampletable**.
- Omdat we -q gebruiken, schakelt het autovisualisatie uit.
- Omdat we `-m sample -r 0.1 -n 500` het willekeurig gebruiken monsters 10% van de rijen in de hivesampletable en beperkt de grootte van het resultaat ingesteld tot 500 rijen.
- Ten slotte, `-o query2` omdat we het gebruikt slaat ook de uitvoer in een dataframe genaamd **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Overwegingen tijdens het gebruik van de nieuwe kernels

Welke kernel u ook gebruikt, het verlaten van de notitieblokken verbruikt de clusterbronnen.  Met deze kernels, omdat de contexten vooraf zijn ingesteld, gewoon het verlaten van de notebooks niet doden van de context en dus de cluster bronnen blijven in gebruik. Een goede gewoonte is om de optie **Sluiten en Stoppen** te gebruiken in het menu **Bestand** van het notitieblok wanneer u klaar bent met het notitieblok, waardoor de context wordt gedood en het notitieblok wordt afgesloten.

## <a name="where-are-the-notebooks-stored"></a>Waar worden de notitieblokken opgeslagen?

Als uw cluster Azure Storage als standaardopslagaccount gebruikt, worden Jupyter-notitieblokken opgeslagen in een opslagaccount onder de map **/HdiNotebooks.**  Notitieblokken, tekstbestanden en mappen die u vanuit Jupyter maakt, zijn toegankelijk vanuit het opslagaccount.  Als u bijvoorbeeld Jupyter gebruikt om een map **myfolder** en een notebook **myfolder/mynotebook.ipynb**te maken, hebt u toegang tot dat notitieblok in `/HdiNotebooks/myfolder/mynotebook.ipynb` het opslagaccount.  Het omgekeerde is ook waar, dat wil zeggen, als `/HdiNotebooks/mynotebook1.ipynb`je een notebook direct uploaden naar uw opslag account op , het notitieblok is zichtbaar van Jupyter ook.  Notitieblokken blijven in het opslagaccount staan, zelfs nadat het cluster is verwijderd.

> [!NOTE]  
> HDInsight-clusters met Azure Data Lake Storage als standaardopslag slaan notitieblokken niet op in bijbehorende opslag.

De manier waarop notitieblokken worden opgeslagen in het opslagaccount is compatibel met [Apache Hadoop HDFS.](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) Als u Dus SSH in het cluster gebruikt, u bestandsbeheeropdrachten gebruiken zoals weergegeven in het volgende fragment:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it's visible from Jupyter

Ongeacht of het cluster Azure Storage of Azure Data Lake Storage gebruikt als standaardopslagaccount, `/var/lib/jupyter`de notitieblokken worden ook opgeslagen op de clusterheadnode op .

## <a name="supported-browser"></a>Ondersteunde browser

Jupyter-laptops op Spark HDInsight-clusters worden alleen ondersteund op Google Chrome.

## <a name="feedback"></a>Feedback

De nieuwe kernels zijn in een evoluerende fase en zullen na verloop van tijd volwassen worden. Dit kan ook betekenen dat API's kunnen veranderen als deze kernels volwassen worden. We stellen het zeer op prijs als u ons daarover een nieuwe kernel gebruikt. Dit is handig bij het vormgeven van de definitieve release van deze kernels. Je je opmerkingen/feedback achterlaten onder in de sectie **Feedback** onderaan dit artikel.

## <a name="see-also"></a>Zie ook

- [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

- [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
- [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
- [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

- [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
- [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

- [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
- [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
- [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
- [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

- [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
- [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
