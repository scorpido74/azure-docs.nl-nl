---
title: Spark-instellingen configureren-Azure HDInsight
description: Apache Spark-instellingen voor een Azure HDInsight-cluster weer geven en configureren
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397104"
---
# <a name="configure-apache-spark-settings"></a>Apache Spark-instellingen configureren

An HDInsight Spark-cluster bevat een installatie van de [Apache Spark](https://spark.apache.org/) -bibliotheek.  Elk HDInsight-cluster bevat standaard configuratie parameters voor alle geïnstalleerde services, waaronder Spark.  Een belang rijk aspect van het beheren van een HDInsight-Apache Hadoop cluster is bewakings werk belasting, met inbegrip van Spark-taken, om ervoor te zorgen dat de taken op een voorspel bare manier worden uitgevoerd. Als u Spark-taken best wilt uitvoeren, moet u rekening houden met de fysieke cluster configuratie bij het bepalen van het optimaliseren van de logische configuratie van het cluster.

Het standaard HDInsight-Apache Spark cluster bevat de volgende knoop punten: drie [Apache ZooKeeper](https://zookeeper.apache.org/) knoop punten, twee hoofd knooppunten en een of meer worker-knoop punten:

![Spark HDInsight-architectuur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Het aantal Vm's en de VM-grootten voor de knoop punten in uw HDInsight-cluster kunnen ook van invloed zijn op uw Spark-configuratie. Niet-standaard-HDInsight-configuratie waarden vereisen vaak niet-standaard Spark-configuratie waarden. Wanneer u een HDInsight Spark-cluster maakt, worden de voorgestelde VM-grootten voor elk van de onderdelen weer gegeven. Momenteel zijn de door het [geheugen geoptimaliseerde Linux VM-grootten](../../virtual-machines/linux/sizes-memory.md) voor Azure D12 v2 of hoger.

## <a name="apache-spark-versions"></a>Apache Spark versies

Gebruik de aanbevolen Spark-versie voor uw cluster.  De HDInsight-service bevat verschillende versies van zowel Spark als HDInsight.  Elke versie van Spark bevat een set standaard cluster instellingen.  

Wanneer u een nieuw cluster maakt, zijn er meerdere Spark-versies waaruit u kunt kiezen. Voor een overzicht van de volledige lijst, [HDInsight-onderdelen en-versies](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> De standaard versie van Apache Spark in de HDInsight-service kan zonder kennisgeving worden gewijzigd. Als u een versie afhankelijkheid hebt, raadt micro soft u aan die bepaalde versie op te geven wanneer u clusters maakt met behulp van .NET SDK, Azure PowerShell en klassieke CLI van Azure.

Apache Spark heeft drie systeem configuratie locaties:

* Spark-eigenschappen bepalen de meeste toepassings parameters en kunnen worden ingesteld met behulp van een `SparkConf`-object of via Java-systeem eigenschappen.
* Omgevings variabelen kunnen worden gebruikt om instellingen per computer, zoals het IP-adres, in te stellen via het `conf/spark-env.sh` script op elk knoop punt.
* Logboek registratie kan worden geconfigureerd via `log4j.properties`.

Wanneer u een bepaalde versie van Spark selecteert, bevat uw cluster de standaard configuratie-instellingen.  U kunt de standaard Spark-configuratie waarden wijzigen met behulp van een aangepast Spark-configuratie bestand.  Hieronder ziet u een voor beeld.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

In het bovenstaande voor beeld wordt een aantal standaard waarden overschreven voor vijf Spark-configuratie parameters.  Dit zijn de compressie-codec, Apache Hadoop MapReduce de minimale grootte en Parquet-blok grootte te splitsen en ook de standaard waarden voor de SQL-partitie voor spaarzaam en open file sizes.  Deze configuratie wijzigingen worden gekozen, omdat de bijbehorende gegevens en taken (in dit voor beeld genoom gegevens) bepaalde kenmerken hebben, waardoor deze aangepaste configuratie-instellingen beter kunnen worden gebruikt.

---

## <a name="view-cluster-configuration-settings"></a>Cluster configuratie-instellingen weer geven

Controleer de huidige configuratie-instellingen voor het HDInsight-cluster voordat u prestatie optimalisatie op het cluster uitvoert. Start het HDInsight-dash board vanuit de Azure Portal door te klikken op de **Dashboard** koppeling in het deel venster Spark-cluster. Meld u aan met de gebruikers naam en het wacht woord van de Cluster beheerder.

De Apache Ambari-webgebruikersinterface wordt weer gegeven met een dashboard weergave met metrische gegevens over het resource gebruik van het belangrijkste cluster.  Het Ambari-dash board toont u de Apache Spark configuratie en andere services die u hebt geïnstalleerd. Het dash board bevat een tabblad **configuratie geschiedenis** , waar u configuratie-informatie kunt weer geven voor alle geïnstalleerde services, waaronder Spark.

Als u de configuratie waarden voor Apache Spark wilt bekijken, selecteert u **configuratie geschiedenis**en selecteert u **Spark2**.  Selecteer het tabblad **configuraties** en selecteer vervolgens de koppeling `Spark` (of `Spark2`, afhankelijk van uw versie) in de lijst met Services.  U ziet een lijst met configuratie waarden voor het cluster:

![Spark-configuraties](./media/apache-spark-settings/spark-configurations.png)

Als u afzonderlijke Spark-configuratie waarden wilt bekijken en wijzigen, selecteert u een koppeling met het woord "Spark" in de titel van de koppeling.  Configuraties voor Spark bevatten zowel aangepaste als geavanceerde configuratie waarden in deze categorieën:

* Aangepaste Spark2: standaard waarden
* Aangepaste Spark2-metrische gegevens-eigenschappen
* Geavanceerde Spark2-standaard instellingen
* Geavanceerde Spark2-env
* Geavanceerde spark2-Hive-site-opheffen

Als u een niet-standaard set configuratie waarden maakt, kunt u ook de geschiedenis van uw configuratie-updates bekijken.  Deze configuratie geschiedenis kan handig zijn om te zien welke niet-standaard configuratie optimale prestaties heeft.

> [!NOTE]  
> Als u de algemene configuratie-instellingen van het Spark-cluster wilt zien, maar niet wijzigen, selecteert u het tabblad **omgeving** op de interface op het hoogste niveau in de **gebruikers interface van Spark-taken** .

## <a name="configuring-spark-executors"></a>Spark-uitvoerendeers configureren

Het volgende diagram toont de belangrijkste Spark-objecten: het stuur programma en de bijbehorende Spark-context, en de Cluster beheer-en de *n* worker-knoop punten.  Elk werk knooppunt bevat een uitvoerder, een cache en *n* taak exemplaren.

![Cluster objecten](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark-taken gebruiken werk resources, met name geheugen, zodat het gebruikelijk is om Spark-configuratie waarden aan te passen voor uitvoerder van worker-knoop punten.

Drie belang rijke para meters die vaak worden aangepast aan het afstemmen van Spark-configuraties om de toepassings vereisten te verbeteren, zijn `spark.executor.instances`, `spark.executor.cores`en `spark.executor.memory`. Een uitvoerder is een proces dat wordt gestart voor een Spark-toepassing. Een uitvoerder wordt uitgevoerd op het worker-knoop punt en is verantwoordelijk voor de taken voor de toepassing. Voor elk cluster wordt het standaard aantal uitvoerende agents en de omvang van de uitvoerder berekend op basis van het aantal worker-knoop punten en de grootte van het worker-knoop punt. Deze worden opgeslagen in `spark-defaults.conf` op de hoofd knooppunten van het cluster.  U kunt deze waarden bewerken in een cluster dat wordt uitgevoerd door de **aangepaste Spark-standaard** koppeling in de Ambari-webgebruikersinterface te selecteren.  Nadat u wijzigingen hebt aangebracht, wordt u door de gebruikers interface gevraagd om alle betrokken services opnieuw op te **starten** .

> [!NOTE]  
> Deze drie configuratie parameters kunnen worden geconfigureerd op cluster niveau (voor alle toepassingen die in het cluster worden uitgevoerd) en ook zijn opgegeven voor elke afzonderlijke toepassing.

Een andere informatiebron over de resources die worden gebruikt door de Spark-uitvoerders, is de gebruikersinterface van de Spark-toepassing.  Selecteer in de Spark-gebruikers interface het tabblad **uitvoerende** resources om samen vatting-en detail weergaven weer te geven van de configuratie en resources die worden verbruikt door de uitvoerender.  Met deze weergaven kunt u bepalen of u de standaardwaarden voor Spark-uitvoerders voor het hele cluster wilt wijzigen, of alleen voor een bepaalde reeks taakuitvoeringen.

![Spark-Uitvoerendeers](./media/apache-spark-settings/apache-spark-executors.png)

U kunt ook de Ambari-REST API gebruiken om de configuratie-instellingen voor HDInsight-en Spark-clusters programmatisch te controleren.  Meer informatie vindt u op de [AMBARI API-referentie van Apache op github](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Afhankelijk van de Spark-workload kunt u bepalen dat een Spark-configuratie die niet-standaard is, meer geoptimaliseerde uitvoeringen van Spark-taken biedt.  U moet benchmarktests uitvoeren met voorbeeldworkloads om niet-standaard-clusterconfiguraties te valideren.  Suggesties voor algemene parameters die u kunt aanpassen:

* `--num-executors` stelt het aantal uitvoerendeers in.
* `--executor-cores` stelt het aantal kernen in voor elke uitvoerder. We raden u aan om middelste uitvoerders uit te voeren, omdat andere processen een deel van het beschik bare geheugen verbruiken.
* `--executor-memory` regelt de geheugen grootte (Heap-grootte) van elke uitvoerder op [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). u moet een geheugen voor de overhead van de uitvoering van de opslag ruimte laten.

Hier volgt een voor beeld van twee worker-knoop punten met verschillende configuratie waarden:

![Configuraties met twee knoop punten](./media/apache-spark-settings/executor-configuration.png)

In de volgende lijst ziet u de para meters voor Key Spark-bewerkings geheugen.

* `spark.executor.memory` definieert de totale hoeveelheid geheugen die beschikbaar is voor een uitvoerder.
* `spark.storage.memoryFraction` (standaard ~ 60%) Hiermee definieert u de hoeveelheid geheugen die beschikbaar is voor het opslaan van persistente Rdd's.
* `spark.shuffle.memoryFraction` (standaard ~ 20%) Hiermee definieert u de hoeveelheid geheugen die is gereserveerd voor wille keurige volg orde.
* `spark.storage.unrollFraction` en `spark.storage.safetyFraction` (in totaal ongeveer 30% van het totale geheugen): deze waarden worden intern gebruikt door Spark en mogen niet worden gewijzigd.

GAREN beheert de maximale hoeveelheid geheugen die wordt gebruikt door de containers op elk Spark-knoop punt. In het volgende diagram ziet u de relaties per knoop punt tussen garen configuratie objecten en Spark-objecten.

![Vonk geheugen beheer van garen](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Para meters wijzigen voor een toepassing die wordt uitgevoerd in Jupyter notebook

Spark-clusters in HDInsight bevatten standaard een aantal onderdelen. Elk van deze onderdelen bevat standaard configuratie waarden, die als nodig kunnen worden overschreven.

* Spark-kern-Spark-kern, Spark SQL, Spark streaming Api's, Graphx en Apache Spark MLlib.
* Anaconda-een python-pakket beheer.
* [Apache livy](https://livy.incubator.apache.org/) : de Apache Spark rest API, die wordt gebruikt voor het verzenden van externe taken naar een HDInsight Spark-cluster.
* [Jupyter](https://jupyter.org/) -en [Apache Zeppelin](https://zeppelin.apache.org/) -NOTEBOOKS-interactieve gebruikers interface voor interactie met uw Spark-cluster.
* ODBC-stuur programma: verbindt Spark-clusters in HDInsight-naar-business intelligence-hulpprogram ma's (BI), zoals micro soft Power BI en tableau.

Voor toepassingen die worden uitgevoerd in de Jupyter-notebook, gebruikt u de opdracht `%%configure` om configuratie wijzigingen van binnen het notitie blok zelf aan te brengen. Deze configuratie wijzigingen worden toegepast op de Spark-taken die worden uitgevoerd vanuit uw notebook-exemplaar. U moet deze wijzigingen aan het begin van de toepassing aanbrengen voordat u uw eerste code-cel uitvoert. De gewijzigde configuratie wordt toegepast op de livy-sessie wanneer deze wordt gemaakt.

> [!NOTE]  
> Als u de configuratie in een later stadium in de toepassing wilt wijzigen, gebruikt u de para meter `-f` (Force). De voortgang van de toepassing gaat echter verloren.

De volgende code laat zien hoe u de configuratie wijzigt voor een toepassing die wordt uitgevoerd in een Jupyter-notebook.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusie

Er zijn een aantal kern configuratie-instellingen die u moet controleren en aanpassen om ervoor te zorgen dat uw Spark-taken op een voorspel bare en krachtige manier worden uitgevoerd. Deze instellingen helpen bij het bepalen van de aanbevolen Spark-cluster configuratie voor uw specifieke workloads.  U moet ook de uitvoering bewaken van langlopende en/of resource-verbruikte Spark-taak uitvoeringen.  De meest voorkomende uitdagingen rond geheugen druk door onjuiste configuraties (met name onjuist uitgevoerde uitvoeringen), langlopende bewerkingen en taken, wat resulteert in Cartesisch bewerkingen.

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop onderdelen en versies die beschikbaar zijn met HDInsight?](../hdinsight-component-versioning.md)
* [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark configuratie](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Spark op Apache Hadoop GARENs uitvoeren](https://spark.apache.org/docs/latest/running-on-yarn.html)
