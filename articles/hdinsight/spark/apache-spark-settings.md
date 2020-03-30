---
title: Spark-instellingen configureren - Azure HDInsight
description: Apache Spark-instellingen weergeven en configureren voor een Azure HDInsight-cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272017"
---
# <a name="configure-apache-spark-settings"></a>Apache Spark-instellingen configureren

Een HDInsight Spark-cluster bevat een installatie van de [Apache Spark-bibliotheek.](https://spark.apache.org/)  Elk HDInsight-cluster bevat standaardconfiguratieparameters voor al zijn geïnstalleerde services, inclusief Spark.  Een belangrijk aspect van het beheren van een HDInsight Apache Hadoop-cluster is het bewaken van de werkbelasting, inclusief Spark Jobs, om ervoor te zorgen dat de taken op een voorspelbare manier worden uitgevoerd. Als u Spark-taken het beste wilt uitvoeren, moet u rekening houden met de fysieke clusterconfiguratie bij het bepalen hoe u de logische configuratie van het cluster optimaliseren.

Het standaard CLUSTER HDInsight Apache Spark bevat de volgende knooppunten: drie [Apache ZooKeeper-knooppunten,](https://zookeeper.apache.org/) twee hoofdknooppunten en een of meer werknemersknooppunten:

![Spark HDInsight-architectuur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Het aantal VM's en de VM-formaten voor de knooppunten in uw HDInsight-cluster kan ook van invloed zijn op uw Spark-configuratie. Voor niet-standaard HDInsight-configuratiewaarden zijn vaak niet-standaard Spark-configuratiewaarden vereist. Wanneer u een HDInsight Spark-cluster maakt, worden voorgestelde VM-formaten voor elk van de componenten weergegeven. Momenteel zijn de [voor geheugen geoptimaliseerde Linux VM-formaten](../../virtual-machines/linux/sizes-memory.md) voor Azure D12 v2 of hoger.

## <a name="apache-spark-versions"></a>Apache Spark-versies

Gebruik de beste Spark-versie voor uw cluster.  De HDInsight-service bevat verschillende versies van zowel Spark als HDInsight zelf.  Elke versie van Spark bevat een set standaardclusterinstellingen.  

Wanneer u een nieuw cluster maakt, zijn er meerdere Spark-versies om uit te kiezen. [HdInsight-componenten en -versies](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) om de volledige lijst te bekijken


> [!NOTE]  
> De standaardversie van Apache Spark in de HDInsight-service kan zonder kennisgeving worden gewijzigd. Als u een afhankelijkheid van de versie hebt, raadt Microsoft u aan die specifieke versie op te geven wanneer u clusters maakt met .NET SDK, Azure PowerShell en Azure Classic CLI.

Apache Spark heeft drie systeemconfiguratielocaties:

* Spark-eigenschappen bepalen de meeste toepassingsparameters `SparkConf` en kunnen worden ingesteld met behulp van een object of via java-systeemeigenschappen.
* Omgevingsvariabelen kunnen worden gebruikt om instellingen per machine in te `conf/spark-env.sh` stellen, zoals het IP-adres, via het script op elk knooppunt.
* Logboekregistratie kan worden `log4j.properties`geconfigureerd via .

Wanneer u een bepaalde versie van Spark selecteert, bevat uw cluster de standaardconfiguratie-instellingen.  U de standaardsparkconfiguratiewaarden wijzigen met behulp van een aangepast Spark-configuratiebestand.  Hieronder kunt u een voorbeeld bekijken.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

In het bovenstaande voorbeeld worden verschillende standaardwaarden voor vijf Spark-configuratieparameters overschreven.  Dit zijn de compressiecodec, Apache Hadoop MapReduce split minimum size and parquet block sizes, and also the Spar SQL partition and open file sizes default values.  Deze configuratiewijzigingen worden gekozen omdat de bijbehorende gegevens en taken (in dit voorbeeld genomische gegevens) bepaalde kenmerken hebben, die beter presteren met behulp van deze aangepaste configuratie-instellingen.

---

## <a name="view-cluster-configuration-settings"></a>Clusterconfiguratie-instellingen weergeven

Controleer de huidige HDInsight-clusterconfiguratie-instellingen voordat u prestatieoptimalisatie uitvoert op het cluster. Start het HDInsight-dashboard vanuit de Azure-portal door op de **koppeling Dashboard** in het spark-clustervenster te klikken. Meld u aan met de gebruikersnaam en het wachtwoord van de clusterbeheerder.

De Apache Ambari Web UI wordt weergegeven met een dashboardweergave van de statistieken voor het gebruik van belangrijke clusterbronnen.  Het Ambari-dashboard toont u de Apache Spark-configuratie en andere services die u hebt geïnstalleerd. Het dashboard bevat een tabblad **Config-geschiedenis,** waar u configuratiegegevens voor alle geïnstalleerde services bekijken, inclusief Spark.

Als u configuratiewaarden voor Apache Spark wilt weergeven, selecteert u **Config-geschiedenis**en selecteert u **Vervolgens Spark2**.  Selecteer het tabblad **Configs** `Spark` en `Spark2`selecteer vervolgens de koppeling (of , afhankelijk van uw versie), in de servicelijst.  U ziet een lijst met configuratiewaarden voor uw cluster:

![Spark-configuraties](./media/apache-spark-settings/spark-configurations.png)

Als u afzonderlijke Spark-configuratiewaarden wilt zien en wijzigen, selecteert u een koppeling met het woord 'vonk' in de koppelingstitel.  Configuraties voor Spark bevatten zowel aangepaste als geavanceerde configuratiewaarden in deze categorieën:

* Aangepaste Spark2-standaardinstellingen
* Aangepaste eigenschappen spark2-metrics
* Geavanceerde Spark2-standaardinstellingen
* Geavanceerde Spark2-env
* Geavanceerde spark2-hive-site-override

Als u een niet-standaardset configuratiewaarden maakt, u ook de geschiedenis van uw configuratie-updates zien.  Deze configuratiegeschiedenis kan handig zijn om te zien welke niet-standaardconfiguratie optimale prestaties heeft.

> [!NOTE]  
> Als u de algemene spark-clusterconfiguratie-instellingen wilt bekijken, maar niet wilt wijzigen, selecteert u het tabblad **Omgeving** op de **interface van spark-taak op** het hoogste niveau.

## <a name="configuring-spark-executors"></a>Spark-uitvoerders configureren

In het volgende diagram worden belangrijke Spark-objecten weergegeven: het stuurprogrammaprogramma *n* en de bijbehorende Spark-context en de clusterbeheerder en de n-werkknooppunten.  Elk werkknooppunt bevat een executeur, *n* een cache en n-taakinstanties.

![Clusterobjecten](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark-taken gebruiken werknemersbronnen, met name geheugen, dus het is gebruikelijk om de spark-configuratiewaarden aan te passen voor werknemersnodeuitvoerders.

Drie belangrijke parameters die vaak worden aangepast om Spark-configuraties af te stemmen om de toepassingsvereisten te verbeteren, zijn `spark.executor.instances`, `spark.executor.cores`en `spark.executor.memory`. Een executor is een proces dat is gestart voor een Spark-toepassing. Een executeur draait op het werkknooppunt en is verantwoordelijk voor de taken voor de toepassing. Voor elk cluster wordt het standaardaantal uitvoerders en de grootte van de uitvoerder berekend op basis van het aantal werknemersknooppunten en de grootte van het werknemersknooppunt. Deze worden `spark-defaults.conf` opgeslagen in op de clusterkopknooppunten.  U deze waarden in een lopend cluster bewerken door de koppeling **Aangepaste spark-defaults** te selecteren in de gebruikersinterface van het Web Ambari.  Nadat u wijzigingen hebt aangebracht, wordt u door de gebruikersinterface gevraagd om alle betrokken services opnieuw op te **starten.**

> [!NOTE]  
> Deze drie configuratieparameters kunnen worden geconfigureerd op clusterniveau (voor alle toepassingen die op het cluster worden uitgevoerd) en ook worden opgegeven voor elke afzonderlijke toepassing.

Een andere informatiebron over de resources die worden gebruikt door de Spark-uitvoerders, is de gebruikersinterface van de Spark-toepassing.  Selecteer in de Spark-gebruikersinterface het tabblad **Executors om overzichts-** en detailweergaven weer te geven van de configuratie en resources die door de uitvoerders worden verbruikt.  Met deze weergaven kunt u bepalen of u de standaardwaarden voor Spark-uitvoerders voor het hele cluster wilt wijzigen, of alleen voor een bepaalde reeks taakuitvoeringen.

![Spark-uitvoerders](./media/apache-spark-settings/apache-spark-executors.png)

U ook de Ambari REST API gebruiken om de configuratie-instellingen van HDInsight en Spark-cluster programmatisch te verifiëren.  Meer informatie is beschikbaar op de [Apache Ambari API referentie op GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Afhankelijk van de Spark-workload kunt u bepalen dat een Spark-configuratie die niet-standaard is, meer geoptimaliseerde uitvoeringen van Spark-taken biedt.  U moet benchmarktests uitvoeren met voorbeeldworkloads om niet-standaard-clusterconfiguraties te valideren.  Suggesties voor algemene parameters die u kunt aanpassen:

* `--num-executors`hiermee wordt het aantal uitvoerders ingesteld.
* `--executor-cores`hiermee wordt het aantal kernen voor elke uitvoerder ingesteld. We raden u aan middelgrote uitvoerders te gebruiken, omdat andere processen ook een deel van het beschikbare geheugen verbruiken.
* `--executor-memory`hiermee bepaalt u de geheugengrootte (heapgrootte) van elke executeur op [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)en moet u wat geheugen achterlaten voor de uitvoering overhead.

Hier is een voorbeeld van twee werknemersknooppunten met verschillende configuratiewaarden:

![Twee knooppuntconfiguraties](./media/apache-spark-settings/executor-configuration.png)

In de volgende lijst worden belangrijke geheugenparameters voor spark-uitvoerders weergegeven.

* `spark.executor.memory`definieert de totale hoeveelheid geheugen die beschikbaar is voor een executeur.
* `spark.storage.memoryFraction`(standaard ~60%) definieert de hoeveelheid geheugen die beschikbaar is voor het opslaan van aanhoudende RDD's.
* `spark.shuffle.memoryFraction`(standaard ~20%) definieert de hoeveelheid geheugen gereserveerd voor shuffle.
* `spark.storage.unrollFraction`en `spark.storage.safetyFraction` (totaal ~ 30% van het totale geheugen) - deze waarden worden intern gebruikt door Spark en mogen niet worden gewijzigd.

YARN regelt de maximale som geheugen die door de containers op elk Spark-knooppunt wordt gebruikt. In het volgende diagram worden de relaties per knooppunt tussen YARN-configuratieobjecten en Spark-objecten weergegeven.

![YARN Spark-geheugenbeheer](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Parameters wijzigen voor een toepassing die wordt uitgevoerd in Jupyter-notitieblok

Spark clusters in HDInsight bevatten standaard een aantal componenten. Elk van deze componenten bevat standaardconfiguratiewaarden, die naar behoefte kunnen worden overschreven.

* Spark Core - Spark Core, Spark SQL, Spark streaming API's, GraphX en Apache Spark MLlib.
* Anaconda - een python package manager.
* [Apache Livy](https://livy.incubator.apache.org/) - de Apache Spark REST API, gebruikt om externe taken in te dienen bij een HDInsight Spark-cluster.
* [Jupyter-](https://jupyter.org/) en [Apache Zeppelin-notebooks](https://zeppelin.apache.org/) - interactieve browsergebaseerde gebruikersinterface voor interactie met uw Spark-cluster.
* ODBC-driver - verbindt Spark-clusters in HDInsight met bi-tools (Business Intelligence), zoals Microsoft Power BI en Tableau.

Voor toepassingen die in het Jupyter-notitieblok worden uitgevoerd, gebruikt u de `%%configure` opdracht om configuratiewijzigingen aan te brengen vanuit het notitieblok zelf. Deze configuratiewijzigingen worden toegepast op de Spark-taken die worden uitgevoerd vanuit uw notitieblokinstantie. U moet dergelijke wijzigingen aanbrengen aan het begin van de toepassing, voordat u uw eerste codecel uitvoert. De gewijzigde configuratie wordt toegepast op de Livy-sessie wanneer deze wordt gemaakt.

> [!NOTE]  
> Als u de configuratie in een later `-f` stadium van de toepassing wilt wijzigen, gebruikt u de parameter (kracht). Alle voortgang in de toepassing gaat echter verloren.

In de onderstaande code ziet u hoe u de configuratie voor een toepassing die wordt uitgevoerd in een Jupyter-notitieblok wijzigen.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusie

Er zijn een aantal kernconfiguratie-instellingen die u moet controleren en aanpassen om ervoor te zorgen dat uw Spark-taken op een voorspelbare en performante manier worden uitgevoerd. Met deze instellingen u bepalen wat de beste Spark-clusterconfiguratie is voor uw specifieke workloads.  U moet ook de uitvoering van langlopende en/of resource-verbruikende Spark-taakuitvoeringen controleren.  De meest voorkomende uitdagingen zijn het centrum rond geheugendruk als gevolg van onjuiste configuraties (met name onjuist formaat uitvoerders), langlopende bewerkingen en taken, die resulteren in Cartesiaanse bewerkingen.

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop componenten en versies beschikbaar met HDInsight?](../hdinsight-component-versioning.md)
* [Resources beheren voor een Apache Spark-cluster op HDInsight](apache-spark-resource-manager.md)
* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark-configuratie](https://spark.apache.org/docs/latest/configuration.html)
* [Het lopen Apache Spark op Apache Hadoop GAREN](https://spark.apache.org/docs/latest/running-on-yarn.html)
