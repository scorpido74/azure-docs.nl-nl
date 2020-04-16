---
title: Spark-instellingen configureren - Azure HDInsight
description: Apache Spark-instellingen weergeven en configureren voor een Azure HDInsight-cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: e13390067f8767e8c07b9c189264444e6d999a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411306"
---
# <a name="configure-apache-spark-settings"></a>Apache Spark-instellingen configureren

Een HDInsight Spark-cluster bevat een installatie van de [Apache Spark-bibliotheek.](https://spark.apache.org/)  Elk HDInsight-cluster bevat standaardconfiguratieparameters voor al zijn geïnstalleerde services, inclusief Spark.  Een belangrijk aspect van het beheren van een HDInsight Apache Hadoop-cluster is het bewaken van de werkbelasting, waaronder Spark Jobs. Als u Spark-taken het beste wilt uitvoeren, moet u rekening houden met de fysieke clusterconfiguratie bij het bepalen van de logische configuratie van het cluster.

Het standaard CLUSTER HDInsight Apache Spark bevat de volgende knooppunten: drie [Apache ZooKeeper-knooppunten,](https://zookeeper.apache.org/) twee hoofdknooppunten en een of meer werknemersknooppunten:

![Spark HDInsight-architectuur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Het aantal VM's en VM-formaten voor de knooppunten in uw HDInsight-cluster kan van invloed zijn op uw Spark-configuratie. Voor niet-standaard HDInsight-configuratiewaarden zijn vaak niet-standaard Spark-configuratiewaarden vereist. Wanneer u een HDInsight Spark-cluster maakt, worden voorgestelde VM-formaten voor elk van de componenten weergegeven. Momenteel zijn de [voor geheugen geoptimaliseerde Linux VM-formaten](../../virtual-machines/linux/sizes-memory.md) voor Azure D12 v2 of hoger.

## <a name="apache-spark-versions"></a>Apache Spark-versies

Gebruik de beste Spark-versie voor uw cluster.  De HDInsight-service bevat verschillende versies van zowel Spark als HDInsight zelf.  Elke versie van Spark bevat een set standaardclusterinstellingen.  

Wanneer u een nieuw cluster maakt, zijn er meerdere Spark-versies om uit te kiezen. [HdInsight-componenten en -versies](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)om de volledige lijst te bekijken.

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

In het bovenstaande voorbeeld worden verschillende standaardwaarden voor vijf Spark-configuratieparameters overschreven.  Deze waarden zijn de compressie codec, Apache Hadoop MapReduce split minimum grootte en parket blok maten. Ook de Spar SQL-partitie en de standaardwaarden voor open bestandsformaten.  Deze configuratiewijzigingen worden gekozen omdat de bijbehorende gegevens en taken (in dit voorbeeld genomische gegevens) bepaalde kenmerken hebben. Deze kenmerken zullen het beter doen met behulp van deze aangepaste configuratie-instellingen.

---

## <a name="view-cluster-configuration-settings"></a>Clusterconfiguratie-instellingen weergeven

Controleer de huidige HDInsight-clusterconfiguratie-instellingen voordat u prestatieoptimalisatie op het cluster doet. Start het HDInsight-dashboard vanuit de Azure-portal door op de **koppeling Dashboard** in het spark-clustervenster te klikken. Meld u aan met de gebruikersnaam en het wachtwoord van de clusterbeheerder.

De Apache Ambari Web UI wordt weergegeven met een dashboard met belangrijke clusterresourcegebruiksstatistieken.  Het Ambari-dashboard toont u de Apache Spark-configuratie en andere geïnstalleerde services. Het dashboard bevat een tabblad **Config-geschiedenis,** waarin u informatie weergeeft voor geïnstalleerde services, waaronder Spark.

Als u configuratiewaarden voor Apache Spark wilt weergeven, selecteert u **Config-geschiedenis**en selecteert u **Vervolgens Spark2**.  Selecteer het tabblad **Configs** `Spark` en `Spark2`selecteer vervolgens de koppeling (of , afhankelijk van uw versie), in de servicelijst.  U ziet een lijst met configuratiewaarden voor uw cluster:

![Spark-configuraties](./media/apache-spark-settings/spark-configurations.png)

Als u afzonderlijke Spark-configuratiewaarden wilt zien en wijzigen, selecteert u een koppeling met 'vonk' in de titel.  Configuraties voor Spark bevatten zowel aangepaste als geavanceerde configuratiewaarden in deze categorieën:

* Aangepaste Spark2-standaardinstellingen
* Aangepaste eigenschappen spark2-metrics
* Geavanceerde Spark2-standaardinstellingen
* Geavanceerde Spark2-env
* Geavanceerde spark2-hive-site-override

Als u een niet-standaardset met configuratiewaarden maakt, is uw updategeschiedenis zichtbaar.  Deze configuratiegeschiedenis kan handig zijn om te zien welke niet-standaardconfiguratie optimale prestaties heeft.

> [!NOTE]  
> Als u de algemene spark-clusterconfiguratie-instellingen wilt bekijken, maar niet wilt wijzigen, selecteert u het tabblad **Omgeving** op de **interface van spark-taak op** het hoogste niveau.

## <a name="configuring-spark-executors"></a>Spark-uitvoerders configureren

In het volgende diagram worden belangrijke Spark-objecten weergegeven: het stuurprogrammaprogramma *n* en de bijbehorende Spark-context en de clusterbeheerder en de n-werkknooppunten.  Elk werkknooppunt bevat een executeur, *n* een cache en n-taakinstanties.

![Clusterobjecten](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark-taken gebruiken werknemersbronnen, met name geheugen, dus het is gebruikelijk om de spark-configuratiewaarden aan te passen voor werknemersnodeuitvoerders.

Drie belangrijke parameters die vaak worden aangepast om Spark-configuraties af te stemmen om de toepassingsvereisten te verbeteren, zijn `spark.executor.instances`, `spark.executor.cores`en `spark.executor.memory`. Een executor is een proces dat is gestart voor een Spark-toepassing. Een executeur draait op het werkknooppunt en is verantwoordelijk voor de taken voor de toepassing. Het aantal werknemersknooppunten en de grootte van het werknemersknooppunt bepaalt het aantal uitvoerders en de grootte van de uitvoerder. Deze waarden worden `spark-defaults.conf` opgeslagen in de clusterkopknooppunten.  U deze waarden bewerken in een lopend cluster door **Aangepaste spark-defaults** te selecteren in de gebruikersinterface van het Web Ambari.  Nadat u wijzigingen hebt aangebracht, wordt u door de gebruikersinterface gevraagd om alle betrokken services opnieuw op te **starten.**

> [!NOTE]  
> Deze drie configuratieparameters kunnen worden geconfigureerd op clusterniveau (voor alle toepassingen die op het cluster worden uitgevoerd) en ook worden opgegeven voor elke afzonderlijke toepassing.

Een andere bron van informatie over resources die worden gebruikt door Spark Executors is de Spark Application UI.  In de gebruikersinterface geeft **Executors overzichts-** en detailweergaven weer van de configuratie en verbruikte resources.  Bepaal of uitvoerderswaarden voor het hele cluster of bepaalde set taakuitvoeringen moeten wijzigen.

![Spark-uitvoerders](./media/apache-spark-settings/apache-spark-executors.png)

U de Ambari REST API ook gebruiken om de configuratie-instellingen van HDInsight en Spark-cluster programmatisch te verifiëren.  Meer informatie is beschikbaar op de [Apache Ambari API referentie op GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Afhankelijk van de Spark-workload kunt u bepalen dat een Spark-configuratie die niet-standaard is, meer geoptimaliseerde uitvoeringen van Spark-taken biedt.  Doe benchmarktests met voorbeeldworkloads om niet-standaard clusterconfiguraties te valideren.  Suggesties voor algemene parameters die u kunt aanpassen:

|Parameter |Beschrijving|
|---|---|
|--num-executors|Hiermee stelt u het aantal uitvoerders in.|
|--executeur-cores|Hiermee stelt u het aantal kernen in voor elke uitvoerder. We raden u aan middelgrote uitvoerders te gebruiken, omdat andere processen ook een deel van het beschikbare geheugen verbruiken.|
|--executeur-geheugen|Hiermee bepaalt u de geheugengrootte (heapgrootte) van elke executeur op [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)en moet u wat geheugen achterlaten voor de uitvoering overhead.|

Hier is een voorbeeld van twee werknemersknooppunten met verschillende configuratiewaarden:

![Twee knooppuntconfiguraties](./media/apache-spark-settings/executor-configuration.png)

In de volgende lijst worden belangrijke geheugenparameters voor spark-uitvoerders weergegeven.

|Parameter |Beschrijving|
|---|---|
|spark.executor.memory|Hiermee definieert u de totale hoeveelheid geheugen die beschikbaar is voor een executeur.|
|spark.storage.memoryFractie|(standaard ~60%) definieert de hoeveelheid geheugen die beschikbaar is voor het opslaan van aanhoudende RDD's.|
|spark.shuffle.memoryFractie|(standaard ~20%) definieert de hoeveelheid geheugen gereserveerd voor shuffle.|
|spark.storage.unrollFractie en spark.storage.safetyFraction|(totaal ~ 30% van het totale geheugen) - deze waarden worden intern gebruikt door Spark en mogen niet worden gewijzigd.|

YARN regelt de maximale som geheugen die door de containers op elk Spark-knooppunt wordt gebruikt. In het volgende diagram worden de relaties per knooppunt tussen YARN-configuratieobjecten en Spark-objecten weergegeven.

![YARN Spark-geheugenbeheer](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Parameters wijzigen voor een toepassing die wordt uitgevoerd in Jupyter-notitieblok

Spark clusters in HDInsight bevatten standaard een aantal componenten. Elk van deze componenten bevat standaardconfiguratiewaarden, die naar behoefte kunnen worden overschreven.

|Onderdeel |Beschrijving|
|---|---|
|Vonkkern|Spark Core, Spark SQL, Spark streaming API's, GraphX en Apache Spark MLlib.|
|Anaconda|Een python package manager.|
|[Apache Livy](https://livy.incubator.apache.org/)|De Apache Spark REST API, gebruikt om externe taken in te dienen bij een HDInsight Spark-cluster.|
|[Jupyter](https://jupyter.org/) en [Apache Zeppelin](https://zeppelin.apache.org/) notebooks|Interactieve browsergebaseerde gebruikersinterface voor interactie met uw Spark-cluster.|
|ODBC-stuurprogramma|Verbindt Spark-clusters in HDInsight met bi-tools (business intelligence), zoals Microsoft Power BI en Tableau.|

Voor toepassingen die in het Jupyter-notitieblok worden uitgevoerd, gebruikt u de `%%configure` opdracht om configuratiewijzigingen aan te brengen vanuit het notitieblok zelf. Deze configuratiewijzigingen worden toegepast op de Spark-taken die worden uitgevoerd vanuit uw notitieblokinstantie. Breng dergelijke wijzigingen aan het begin van de toepassing, voordat u uw eerste code cel. De gewijzigde configuratie wordt toegepast op de Livy-sessie wanneer deze wordt gemaakt.

> [!NOTE]  
> Als u de configuratie in een later `-f` stadium van de toepassing wilt wijzigen, gebruikt u de parameter (kracht). Alle voortgang in de toepassing gaat echter verloren.

In de onderstaande code ziet u hoe u de configuratie voor een toepassing die wordt uitgevoerd in een Jupyter-notitieblok wijzigen.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusie

Monitor de belangrijkste configuratie-instellingen om ervoor te zorgen dat uw Spark-taken op een voorspelbare en performante manier worden uitgevoerd. Met deze instellingen u bepalen wat de beste Spark-clusterconfiguratie is voor uw specifieke workloads.  U moet ook de uitvoering van langlopende en of resource-verbruikende Spark-taakuitvoeringen controleren.  De meest voorkomende uitdagingen zijn het centrum rond geheugendruk van onjuiste configuraties, zoals foutieve uitvoerders. Ook langlopende bewerkingen en taken, die resulteren in Cartesiaanse bewerkingen.

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop componenten en versies beschikbaar met HDInsight?](../hdinsight-component-versioning.md)
* [Resources beheren voor een Apache Spark-cluster op HDInsight](apache-spark-resource-manager.md)
* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark-configuratie](https://spark.apache.org/docs/latest/configuration.html)
* [Het lopen Apache Spark op Apache Hadoop GAREN](https://spark.apache.org/docs/latest/running-on-yarn.html)
