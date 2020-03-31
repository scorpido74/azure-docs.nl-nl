---
title: Clustergroottes schalen - Azure HDInsight
description: Een Apache Hadoop-cluster elastisch schalen naar uw werkbelasting in Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234739"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight-clusters schalen

HDInsight biedt elasticiteit door u de mogelijkheid te bieden om het aantal werknemersknooppunten in uw clusters op te schalen en af te schalen. Met deze elasticiteit u een cluster na uren of in het weekend verkleinen en uitbreiden tijdens piekzakelijke eisen.

Als u periodieke batchverwerking hebt, kan het HDInsight-cluster enkele minuten voor die bewerking worden opgeschaald, zodat uw cluster voldoende geheugen en CPU-kracht heeft. Later, nadat de verwerking is uitgevoerd en het gebruik opnieuw naar beneden gaat, u het HDInsight-cluster naar minder werknemersknooppunten schalen.

U een cluster handmatig schalen met een van de onderstaande methoden of [automatische schaalopties](hdinsight-autoscale-clusters.md) gebruiken om het systeem automatisch op en neer te laten schalen als reactie op CPU, geheugen en andere statistieken.

> [!NOTE]  
> Alleen clusters met HDInsight-versie 3.1.3 of hoger worden ondersteund. Als u niet zeker bent van de versie van uw cluster, u de pagina Eigenschappen controleren.

## <a name="utilities-to-scale-clusters"></a>Hulpprogramma's om clusters te schalen

Microsoft biedt de volgende hulpprogramma's om clusters te schalen:

|Utility | Beschrijving|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-azHDInsightClustersize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -Clustername \<clusternaam> \<-TargetInstanceCount NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzurermHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -Clustername \<clusternaam> \<-TargetInstanceCount NewSize>|
|[Azure-CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight wijzigt formaat](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) \<--resourcegroep Resourcegroep> --naam \<Clusternaam \<> --workernode-count NewSize>|
|[Azure Classic CLI](hdinsight-administer-use-command-line.md)|azure hdinsight-cluster \<wijzigt \<clusterNaam> Aantal doelinstantie> |
|[Azure-portal](https://portal.azure.com)|Open het clustervenster HDInsight, selecteer **Clustergrootte** in het linkermenu, typ vervolgens in het deelvenster Clustergrootte, typ het aantal werknemersknooppunten en selecteer Opslaan.|  

![Clusteroptie azure-portalschaal](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Met een van deze methoden u uw HDInsight-cluster binnen enkele minuten omhoog of omlaag schalen.

> [!IMPORTANT]  
> * De Azure-klassieke CLI is afgeschaft en mag alleen worden gebruikt met het klassieke implementatiemodel. Voor alle andere implementaties gebruikt u de [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * De PowerShell AzureRM-module is afgeschaft.  Gebruik de [Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) waar mogelijk.

## <a name="impact-of-scaling-operations"></a>Impact van schaalbewerkingen

Wanneer u knooppunten **toevoegt** aan het hdinsight-cluster (opschalen), worden in behandeling komende of lopende taken niet beïnvloed. Nieuwe taken kunnen veilig worden ingediend terwijl het schaalproces wordt uitgevoerd. Als de schalingbewerking om welke reden dan ook mislukt, wordt de fout verwerkt om uw cluster in een functionele status te laten.

Als u knooppunten **verwijdert** (omlaag schalen), mislukken in behandeling en uitvoeren van taken wanneer de schaalbewerking is voltooid. Deze fout is te wijten aan het opnieuw opstarten van sommige services tijdens het schaalproces. Er bestaat ook een risico dat uw cluster tijdens een handmatige schalingvast raakt in de veilige modus.

De impact van het wijzigen van het aantal gegevensknooppunten varieert voor elk type cluster dat wordt ondersteund door HDInsight:

* Apache Hadoop

    U het aantal werknemersknooppunten in een Hadoop-cluster dat wordt uitgevoerd naadloos verhogen zonder dat dit gevolgen heeft voor lopende of lopende taken. Nieuwe taken kunnen ook worden ingediend terwijl de bewerking in volle gang is. Fouten in een schaalbewerking worden op een elegante manier verwerkt, zodat het cluster altijd in een functionele status wordt achtergelaten.

    Wanneer een Hadoop-cluster wordt verkleind door het aantal gegevensknooppunten te verminderen, worden sommige services in het cluster opnieuw gestart. Dit gedrag zorgt ervoor dat alle lopende en lopende taken mislukken bij het voltooien van de schaalbewerking. U de taken echter opnieuw indienen zodra de bewerking is voltooid.

* Apache HBase

    U knooppunten naadloos toevoegen of verwijderen aan uw HBase-cluster terwijl deze wordt uitgevoerd. Regionale servers worden automatisch in evenwicht gebracht binnen een paar minuten na het voltooien van de schalingsbewerking. U de regionale servers echter ook handmatig in evenwicht brengen door u aan te melden bij de headnode van het cluster en de volgende opdrachten uit te voeren vanuit een opdrachtpromptvenster:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Zie [Aan de slag met een Apache HBase-voorbeeld in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)voor meer informatie over het gebruik van de HBase-shell.

* Apache Storm

    U gegevensknooppunten naadloos toevoegen of verwijderen aan uw Storm-cluster terwijl deze wordt uitgevoerd. Na een succesvolle voltooiing van de schaalbewerking moet u de topologie echter opnieuw in evenwicht brengen.

    Het opnieuw in evenwicht brengen kan op twee manieren worden bereikt:

  * Gebruikersinterface van stormweb
  * Cli (Command Line Interface)

    Raadpleeg de [Apache Storm documentatie](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) voor meer details.

    De gebruikersinterface van het Storm-web is beschikbaar op het HDInsight-cluster:

    ![HDInsight Storm schaal herbalans](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Hier is een voorbeeld van CLI-opdracht om de stormtopologie opnieuw in evenwicht te brengen:

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Een cluster veilig schalen

### <a name="scale-down-a-cluster-with-running-jobs"></a>Een cluster schalen met lopende taken

Om te voorkomen dat uw lopende taken mislukken tijdens een scale down-bewerking, u drie dingen proberen:

1. Wacht tot de taken zijn voltooid voordat u uw cluster hebt geschaald.
1. Maak handmatig een einde aan de taken.
1. De taken opnieuw indienen nadat de schaalbewerking is beëindigd.

Als u een lijst met lopende en lopende taken wilt bekijken, u de **gebruikersinterface**van YARN Resource Manager gebruiken, nadat u de volgende stappen wilt volgen:

1. Selecteer uw cluster in de [Azure-portal.](https://portal.azure.com/)  Zie [Clusters weergeven en weergeven](./hdinsight-administer-use-portal-linux.md#showClusters) voor de instructies. Het cluster wordt geopend in een nieuwe portalpagina.
2. Navigeer vanuit de hoofdweergave naar **Clusterdashboards** > **Ambari home**. Voer uw clusterreferenties in.
3. Selecteer **GAREN** in de lijst met services in het linkermenu in de Gebruikersinterface van Ambari.  
4. Selecteer op de pagina GAREN **snelle koppelingen** en plaats de plaats boven het actieve hoofdknooppunt en selecteer **vervolgens ResourceManager-gebruikersinterface**.

    ![Apache Ambari koppelt resourcemanager-gebruikersinterface snel](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

U rechtstreeks toegang krijgen `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`tot de Gebruikersinterface van ResourceManager met.

U ziet een lijst met vacatures, samen met hun huidige status. In de schermafbeelding wordt één taak uitgevoerd:

![Gebruikersinterfacetoepassingen voor ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Als u die lopende toepassing handmatig wilt doden, voert u de volgende opdracht uit vanuit de SSH-shell:

```bash
yarn application -kill <application_id>
```

Bijvoorbeeld:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Vast komen te zitten in de veilige modus

Wanneer u een cluster schaalt, gebruikt HDInsight Apache Ambari-beheerinterfaces om eerst de extra werknemersknooppunten te ontmantelen, die hun HDFS-blokken repliceren naar andere online werknemersknooppunten. Daarna schaalt HDInsight het cluster veilig op. HDFS gaat in de veilige modus tijdens de schaling operatie, en wordt verondersteld om uit te komen zodra de schaling is voltooid. In sommige gevallen komt HDFS echter vast te zitten in de veilige modus tijdens een schalingbewerking vanwege bestandsblok onderreplicatie.

HDFS is standaard geconfigureerd met `dfs.replication` een instelling van 1, waarmee wordt aangenomen hoeveel kopieën van elk bestandsblok beschikbaar zijn. Elke kopie van een bestandsblok wordt opgeslagen op een ander knooppunt van het cluster.

Wanneer HDFS detecteert dat het verwachte aantal blokkopieën niet beschikbaar is, treedt HDFS in de veilige modus en genereert Ambari waarschuwingen. Als HDFS de veilige modus invoert voor een schalingbewerking, maar vervolgens de veilige modus niet kan afsluiten omdat het vereiste aantal knooppunten niet wordt gedetecteerd voor replicatie, kan het cluster vast komen te zitten in de veilige modus.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Voorbeeldfouten wanneer de veilige modus is ingeschakeld

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

U de naamknooppuntlogboeken `/var/log/hadoop/hdfs/` uit de map bekijken, in de buurt van het tijdstip waarop het cluster werd geschaald, om te zien wanneer het de veilige modus ingaat. De logbestanden `Hadoop-hdfs-namenode-<active-headnode-name>.*`hebben de naam .

De hoofdoorzaak van de vorige fouten is dat Hive afhankelijk is van tijdelijke bestanden in HDFS tijdens het uitvoeren van query's. Wanneer HDFS de veilige modus invoert, kan Hive geen query's uitvoeren omdat deze niet naar HDFS kan schrijven. De tijdelijke bestanden in HDFS bevinden zich in het lokale station dat is gemonteerd op de VM's van het afzonderlijke werknemersknooppunt en worden gerepliceerd tussen andere werknemersknooppunten op minimaal drie replica's.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Zo voorkom je dat HDInsight vast komt te zitten in de veilige modus

Er zijn verschillende manieren om te voorkomen dat HDInsight in de veilige modus wordt achtergelaten:

* Stop alle Hive-taken voordat hdinsight wordt afgebroken. Plan afwisselend het afschaalproces om te voorkomen dat er conflicteert met lopende Hive-taken.
* De krasmapbestanden `tmp` van Hive handmatig opschonen in HDFS voordat u wordt opgeschaald.
* Schaal HDInsight alleen naar drie werkknooppunten, minimaal. Vermijd zo laag als een werknemer knooppunt.
* Voer de opdracht uit om de veilige modus te verlaten, indien nodig.

In de volgende secties worden deze opties beschreven.

#### <a name="stop-all-hive-jobs"></a>Stop alle Hive-taken

Stop alle Hive-taken voordat u wordt geschaald naar één werkknooppunt. Als uw werkbelasting is gepland, voert u uw scale-down uit nadat Hive-werk is uitgevoerd.

Als u de Hive-taken stopt voordat u ze schaalt, wordt het aantal krasbestanden in de tmp-map (indien aanwezig) geminimaliseerd.

#### <a name="manually-clean-up-hives-scratch-files"></a>De krasbestanden van Hive handmatig opruimen

Als Hive tijdelijke bestanden heeft achtergelaten, u deze bestanden handmatig opschonen voordat u deze opschaalt om de veilige modus te voorkomen.

1. Controleer welke locatie wordt gebruikt voor tijdelijke `hive.exec.scratchdir` bestanden van Hive door naar de configuratieeigenschap te kijken. Deze parameter is `/etc/hive/conf/hive-site.xml`ingesteld in :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Stop Hive-services en zorg ervoor dat alle query's en taken zijn voltooid.
2. Vermeld de inhoud van de `hdfs://mycluster/tmp/hive/` krasmap hierboven, om te zien of deze bestanden bevat:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Hier is een voorbeelduitvoer wanneer er bestanden bestaan:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Als u weet dat Hive klaar is met deze bestanden, u ze verwijderen. Zorg ervoor dat Hive geen query's heeft die worden uitgevoerd door te kijken op de pagina Yarn ResourceManager UI.

    Voorbeeldopdrachtregel om bestanden van HDFS te verwijderen:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HdInsight schalen naar drie of meer werkknooppunten

Als uw clusters vaak vast komen te zitten in de veilige modus wanneer ze worden opgeschaald naar minder dan drie werknemersknooppunten en de vorige stappen niet werken, u voorkomen dat uw cluster helemaal in de veilige modus gaat door ten minste drie werknemersknooppunten te behouden.

Het behouden van drie werknemersknooppunten is duurder dan verkleinen tot slechts één werkknooppunt, maar het voorkomt dat uw cluster vast komt te zitten in de veilige modus.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Schaal HDInsight tot één werkknooppunt

Zelfs wanneer het cluster is teruggebracht tot 1 knooppunt, zal worker node 0 nog steeds overleven. Worker node 0 kan nooit worden ontmanteld.

#### <a name="run-the-command-to-leave-safe-mode"></a>De opdracht uitvoeren om de veilige modus te verlaten

De laatste optie is het uitvoeren van de opdracht Veilige modus verlaten. Als u weet dat de reden voor het invoeren van de veilige modus van HDFS is vanwege Hive-bestand onder replicatie, u de volgende opdracht uitvoeren om de veilige modus te verlaten:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Een Apache HBase-cluster schalen

Regioservers worden binnen enkele minuten na het voltooien van een schalingbewerking automatisch in evenwicht gebracht. Voer de volgende stappen uit om regioservers handmatig in evenwicht te brengen:

1. Maak verbinding met het HDInsight-cluster via SSH. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

2. Start de HBase-shell:

    ```bash
    hbase shell
    ```

3. Gebruik de volgende opdracht om de regioservers handmatig in evenwicht te brengen:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight-clusters automatisch schalen](hdinsight-autoscale-clusters.md)
* [Inleiding tot Azure HDInsight](hadoop/apache-hadoop-introduction.md)
