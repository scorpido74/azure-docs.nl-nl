---
title: Poorten die worden gebruikt door Hadoop-services op HDInsight - Azure
description: In dit artikel vindt u een lijst met poorten die worden gebruikt door Apache Hadoop-services die worden uitgevoerd in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: fe2cb04f36026740dc54f4668d3c3188592bd8ae
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754223"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Poorten gebruikt door Apache Hadoop diensten op HDInsight

Dit document bevat een lijst van de poorten die worden gebruikt door Apache Hadoop-services die op HDInsight-clusters worden uitgevoerd. Het biedt ook informatie over poorten die worden gebruikt om verbinding te maken met het cluster met behulp van SSH.

## <a name="public-ports-vs-non-public-ports"></a>Openbare poorten versus niet-openbare havens

Linux-gebaseerde HDInsight clusters alleen bloot drie poorten openbaar op het internet: 22, 23, en 443. Deze poorten beveiligen clustertoegang met SSH en services die worden blootgesteld via het beveiligde HTTPS-protocol.

HDInsight wordt geïmplementeerd door verschillende Azure Virtual Machines (clusterknooppunten) die worden uitgevoerd op een Azure Virtual Network. Vanuit het virtuele netwerk hebt u toegang tot poorten die niet via internet worden blootgesteld. Als u via SSH verbinding maakt met het hoofdknooppunt, hebt u rechtstreeks toegang tot services die op de clusterknooppunten worden uitgevoerd.

> [!IMPORTANT]  
> Als u een Azure Virtual Network niet opgeeft als configuratieoptie voor HDInsight, wordt er automatisch een gemaakt. U echter geen andere machines (zoals andere Azure Virtual Machines of uw clientontwikkelingsmachine) aansluiten bij dit virtuele netwerk.

Als u extra machines wilt aansluiten bij het virtuele netwerk, moet u eerst het virtuele netwerk maken en vervolgens opgeven bij het maken van uw HDInsight-cluster. Zie [Een virtueel netwerk voor HDInsight plannen voor](hdinsight-plan-virtual-network-deployment.md)meer informatie.

## <a name="public-ports"></a>Openbare havens

Alle knooppunten in een HDInsight-cluster bevinden zich in een Azure Virtual Network. De knooppunten zijn niet rechtstreeks toegankelijk via het internet. Een openbare gateway biedt internettoegang tot de volgende poorten, die gebruikelijk zijn voor alle HDInsight-clustertypen.

| Service | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- |
| Sshd |22 |SSH |Verbindt clients met sshd op de primaire headnode. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie. |
| Sshd |22 |SSH |Verbindt clients met sshd op het randknooppunt. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie. |
| Sshd |23 |SSH |Verbindt klanten met sshd op de secundaire headnode. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie. |
| Ambari |443 |HTTPS |Ambari web Gebruikersinterface. Zie [HDInsight beheren met de Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Zie [HDInsight beheren met behulp van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Zie [Map gebruiken Verminderen met krul](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Maakt verbinding met Hive via ODBC. Zie [Excel verbinden met HDInsight met het Microsoft ODBC-stuurprogramma](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Maakt verbinding met ApacheHive via JDBC. Zie [Verbinding maken met Apache Hive op HDInsight met behulp van de Hive JDBC-driver](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Voor specifieke clustertypen zijn de volgende opties beschikbaar:

| Service | Poort | Protocol | Clustertype | Beschrijving |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |HBase |HBase REST API. Zie [Aan de slag met Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Zie [Apache Spark-taken op afstand verzenden met Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift-server |443 |HTTPS |Spark |Spark Thrift-server wordt gebruikt om Hive-query's in te dienen. Zie [Beeline gebruiken met Apache Hive op HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm web UI. Bekijk [De topologieën van Apache Storm](storm/apache-storm-deploy-monitor-topology-linux.md) implementeren en beheren op HDInsight |
| Kafka Rest proxy |443 |HTTPS |Kafka |Kafka REST API. Zie [Interactie met Apache Kafka-clusters in Azure HDInsight met behulp van een REST-proxy](kafka/rest-proxy.md) |

### <a name="authentication"></a>Verificatie

Alle diensten die openbaar zijn blootgesteld op het internet moeten worden geverifieerd:

| Poort | Referenties |
| --- | --- |
| 22 of 23 |De SSH-gebruikersreferenties die zijn opgegeven tijdens het maken van het cluster |
| 443 |De inlognaam (standaard: beheerder) en wachtwoord die zijn ingesteld tijdens het maken van het cluster |

## <a name="non-public-ports"></a>Niet-openbare havens

> [!NOTE]  
> Sommige services zijn alleen beschikbaar op specifieke clustertypen. HBase is bijvoorbeeld alleen beschikbaar op HBase-clustertypen.

> [!IMPORTANT]  
> Sommige diensten draaien slechts op één headnode tegelijk. Als u probeert verbinding te maken met de service op de primaire headnode en een fout ontvangt, probeert u de secundaire headnode opnieuw te gebruiken.

### <a name="ambari"></a>Ambari

| Service | Knooppunten | Poort | URL-pad | Protocol |
| --- | --- | --- | --- | --- |
| Ambari web gebruikersinterface | Hoofdknooppunten | 8080 | / | HTTP |
| Ambari REST API | Hoofdknooppunten | 8080 | /api/v1 | HTTP |

Voorbeelden:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-poorten

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| Gebruikersinterface namenode |Hoofdknooppunten |30070 |HTTPS |Web-gebruikersinterface om de status weer te geven |
| Metagegevensservice NameNode |hoofdknooppunten |8020 |Ipc |Metagegevens van bestandssystemen |
| DataNode |Alle werkknooppunten |30075 |HTTPS |Web-gebruikersinterface om status, logboeken enzovoort weer te geven. |
| DataNode |Alle werkknooppunten |30010 |&nbsp; |Gegevensoverdracht |
| DataNode |Alle werkknooppunten |30020 |Ipc |Metagegevensbewerkingen |
| Secundaire naamknooppunt |Hoofdknooppunten |50090 |HTTP |Controlepunt voor metagegevens namenode |

### <a name="yarn-ports"></a>GARENpoorten

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| Gebruikersinterface van Resource Manager |Hoofdknooppunten |8088 |HTTP |Web-gebruikersinterface voor resourcebeheer |
| Gebruikersinterface van Resource Manager |Hoofdknooppunten |8090 |HTTPS |Web-gebruikersinterface voor resourcebeheer |
| Beheerinterface voor resourcebeheer |hoofdknooppunten |8141 |Ipc |Voor inzendingen voor toepassingen (Hive, Hive-server, pig, enzovoort.) |
| Resource Manager-planner |hoofdknooppunten |8030 |HTTP |Administratieve interface |
| Resource Manager-toepassingsinterface |hoofdknooppunten |8050 |HTTP |Adres van de interface voor toepassingenbeheer |
| NodeManager |Alle werkknooppunten |30050 |&nbsp; |Het adres van de containerbeheerder |
| Gebruikersinterface van het NodeManager-web |Alle werkknooppunten |30060 |HTTP |Resourcemanager-interface |
| Tijdlijnadres |Hoofdknooppunten |10200 |RPC |De RPC-service Timeline-service. |
| Gebruikersinterface van tijdlijnweb |Hoofdknooppunten |8188 |HTTP |De webgebruikers van de tijdlijnservice |

### <a name="hive-ports"></a>Hive-poorten

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| HiveServer2 |Hoofdknooppunten |10001 |Spaarzaamheid |Service voor verbinding met Hive (Thrift/JDBC) |
| Hive-metastore |Hoofdknooppunten |9083 |Spaarzaamheid |Service voor het verbinden met Hive-metagegevens (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat-poorten

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| WebHCat-server |Hoofdknooppunten |30111 |HTTP |Web API bovenop HCatalog en andere Hadoop-services |

### <a name="mapreduce-ports"></a>MapPoorten verkleinen

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| JobGeschiedenis |Hoofdknooppunten |19888 |HTTP |MapReduce JobHistory web Gebruikersinterface |
| JobGeschiedenis |Hoofdknooppunten |10020 |&nbsp; |MapJobGeschiedenis-server verkleinen |
| ShuffleHandler (ShuffleHandler) |&nbsp; |13562 |&nbsp; |Tussentijdse kaartuitvoer overdraagt aan aanvragen reducers |

### <a name="oozie"></a>Oozie

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| Oozie-server |Hoofdknooppunten |11000 |HTTP |URL voor Oozie-service |
| Oozie-server |Hoofdknooppunten |11001 |HTTP |Poort voor Oozie-beheerder |

### <a name="ambari-metrics"></a>Metrische gegevens van Ambari

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| TimeLine (toepassingsgeschiedenis) |Hoofdknooppunten |6188 |HTTP |De gebruikersinterface van de TimeLine-service |
| TimeLine (toepassingsgeschiedenis) |Hoofdknooppunten |30200 |RPC |De gebruikersinterface van de TimeLine-service |

### <a name="hbase-ports"></a>HBase-poorten

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| HMaster (HMaster) |Hoofdknooppunten |16000 |&nbsp; |&nbsp; |
| HMaster-info Web-gebruikersinterface |Hoofdknooppunten |16010 |HTTP |De poort voor de HBase Master web gebruikersinterface |
| Regioserver |Alle werkknooppunten |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |De poort die klanten gebruiken om verbinding te maken met ZooKeeper |

### <a name="kafka-ports"></a>Kafka-poorten

| Service | Knooppunten | Poort | Protocol | Beschrijving |
| --- | --- | --- | --- | --- |
| Makelaar |Werkknooppunten |9092 |[Kafka Wire Protocol](https://kafka.apache.org/protocol.html) |Gebruikt voor klantcommunicatie |
| &nbsp; |Dierenverzorgerknooppunten |2181 |&nbsp; |De poort die klanten gebruiken om verbinding te maken met Zookeeper |
| REST-proxy | Kafka-beheerknooppunten |9400 |HTTPS |[Kafka REST specificatie](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark-poorten

| Service | Knooppunten | Poort | Protocol | URL-pad | Beschrijving |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift-servers |Hoofdknooppunten |10002 |Spaarzaamheid | &nbsp; | Service voor verbinding maken met Spark SQL (Thrift/JDBC) |
| Livy-server | Hoofdknooppunten | 8998 | HTTP | &nbsp; | Service voor het uitvoeren van instructies, taken en toepassingen |
| Jupyter-notebook | Hoofdknooppunten | 8001 | HTTP | &nbsp; | Website van Jupyter-notitieblok |

Voorbeelden:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. In dit `10.0.0.11` voorbeeld is het IP-adres van de headnode dat de Livy-service host.
