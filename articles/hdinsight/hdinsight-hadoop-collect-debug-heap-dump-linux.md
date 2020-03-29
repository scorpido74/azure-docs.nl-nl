---
title: Heapdumps inschakelen voor Apache Hadoop-services op HDInsight - Azure
description: Heap dumps inschakelen voor Apache Hadoop-services van HdInsight-clusters op basis van Linux voor foutopsporing en analyse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75658794"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Heap dumps inschakelen voor Apache Hadoop services op Linux-gebaseerde HDInsight

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heapdumps bevatten een momentopname van het geheugen van de toepassing, inclusief de waarden van variabelen op het moment dat de dump is gemaakt. Ze zijn dus handig voor het diagnosticeren van problemen die zich voordoen tijdens de run-time.

## <a name="services"></a>Services

U heapdumps inschakelen voor de volgende services:

* **Apache hcatalog** - tempelton
* **Apache hive** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **Apache garen** - resourcemanager, nodemanager, timelineserver
* **Apache hdfs** - datanode, secondarynamenode, namenode

U ook heapdumps voor de kaart inschakelen en processen verminderen die door HDInsight worden uitgevoerd.

## <a name="understanding-heap-dump-configuration"></a>De heapdumpconfiguratie begrijpen

Heapdumps worden ingeschakeld door opties (ook wel opts of parameters genoemd) door te geven aan de JVM wanneer een service wordt gestart. Voor de meeste [Apache Hadoop-services](https://hadoop.apache.org/) u het shellscript wijzigen dat wordt gebruikt om de service te starten om deze opties door te geven.

In elk script is er een export voor ** \* \_OPTS**, die de opties bevat die zijn doorgegeven aan de JVM. In het **hadoop-env.sh** script `export HADOOP_NAMENODE_OPTS=` bevat de regel die begint met bijvoorbeeld de opties voor de NameNode-service.

Kaart- en reduceerprocessen zijn iets anders, omdat deze bewerkingen een onderliggend proces van de MapReduce-service zijn. Elke kaart of proces wordt uitgevoerd in een onderliggende container en er zijn twee vermeldingen die de JVM-opties bevatten. Beide opgenomen in **mapred-site.xml:**

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> We raden u aan [Apache Ambari](https://ambari.apache.org/) te gebruiken om zowel de scripts als de instellingen van mapred-site.xml te wijzigen, omdat Ambari replicareringswijzigingen tussen knooppunten in het cluster verwerkt. Zie de sectie [Apache Ambari gebruiken](#using-apache-ambari) voor specifieke stappen.

### <a name="enable-heap-dumps"></a>Heapdumps inschakelen

Met de volgende optie worden heapdumps inschakelt wanneer een OutOfMemoryError optreedt:

    -XX:+HeapDumpOnOutOfMemoryError

Hiermee **+** geeft u aan dat deze optie is ingeschakeld. Uitgeschakeld is de standaardinstelling.

> [!WARNING]  
> Heap dumps zijn standaard niet ingeschakeld voor Hadoop-services op HDInsight, omdat de dumpbestanden groot kunnen zijn. Als u ze inschakelt voor het oplossen van problemen, vergeet dan niet om ze uit te schakelen zodra u het probleem hebt gereproduceerd en de dumpbestanden hebt verzameld.

### <a name="dump-location"></a>Dumplocatie

De standaardlocatie voor het dumpbestand is de huidige werkmap. U met de volgende optie bepalen waar het bestand is opgeslagen:

    -XX:HeapDumpPath=/path

Met behulp `-XX:HeapDumpPath=/tmp` van de dumps worden bijvoorbeeld de dumps opgeslagen in de /tmp-map.

### <a name="scripts"></a>Scripts

U ook een script activeren wanneer er een **OutOfMemoryError** optreedt. Bijvoorbeeld het activeren van een melding zodat u weet dat de fout is opgetreden. Gebruik de volgende optie om een script te activeren op een __OutOfMemoryError:__

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Aangezien Apache Hadoop een gedistribueerd systeem is, moet elk gebruikt script op alle knooppunten in het cluster waarop de service wordt uitgevoerd, worden geplaatst.
> 
> Het script moet zich ook op een locatie bevinden die toegankelijk is voor het account dat de service als wordt uitgevoerd en moet uitvoermachtigingen bieden. U bijvoorbeeld scripts opslaan `/usr/local/bin` en `chmod go+rx /usr/local/bin/filename.sh` gebruiken om lees- en uitvoermachtigingen toe te staan.

## <a name="using-apache-ambari"></a>Apache Ambari gebruiken

Als u de configuratie voor een service wilt wijzigen, gebruikt u de volgende stappen:

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

2. Selecteer met de lijst aan de linkerkant het servicegebied dat u wilt wijzigen. **HDFS**bijvoorbeeld . Selecteer in het middelste gebied het tabblad **Configs.**

    ![Afbeelding van het Ambari-web met tabblad HDFS Configs geselecteerd](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Voer **opts**in met de vermelding **Filter...** . Alleen items met deze tekst worden weergegeven.

    ![Apache Ambari config gefilterde lijst](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Zoek de ** \* \_OPTS-vermelding** voor de service waarvoor u heapdumps wilt inschakelen en voeg de opties toe die u wilt inschakelen. In de volgende afbeelding heb `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` ik toegevoegd aan de **HADOOP\_NAMENODE\_OPTS** vermelding:

    ![Apache Ambari hadoop-namenode-opts](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Wanneer u heapdumps inschakelt voor de kaart of het onderliggende proces vermindert, zoekt u naar de velden met de naam **mapreduce.admin.map.child.java.opts** en **mapreduce.admin.reduce.child.java.opts**.

    Gebruik de knop **Opslaan** om de wijzigingen op te slaan. U een korte notitie invoeren waarin de wijzigingen worden beschreven.

5. Zodra de wijzigingen zijn toegepast, wordt het **pictogram Opnieuw starten** weergegeven naast een of meer services.

    ![vereiste pictogram opnieuw starten en knop opnieuw starten](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Selecteer elke service die opnieuw moet worden opgestart en gebruik de knop **Serviceacties** om **de onderhoudsmodus in**te schakelen. De onderhoudsmodus voorkomt dat er waarschuwingen worden gegenereerd vanuit de service wanneer u deze opnieuw start.

    ![Menu hdi-onderhoudsmodus inschakelen](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Zodra u de onderhoudsmodus hebt ingeschakeld, gebruikt u de knop **Opnieuw starten** voor de service om **Alle effected opnieuw op** te starten

    ![Apache Ambari herstart alle getroffen invoer](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > De vermeldingen voor de knop **Opnieuw starten** kunnen verschillen voor andere services.

8. Zodra de services opnieuw zijn opgestart, gebruikt u de knop **Serviceacties** om **de onderhoudsmodus uit**te schakelen. Deze Ambari om de bewaking voor waarschuwingen voor de service te hervatten.