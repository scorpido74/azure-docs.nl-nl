---
title: Onderdelen met hoge beschikbaarheid in Azure HDInsight
description: Overzicht van de verschillende high availability componenten die worden gebruikt door HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069632"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Services met hoge beschikbaarheid ondersteund door Azure HDInsight

 Om u een optimale beschikbaarheid te bieden voor uw analytics componenten, is HDInsight ontwikkeld met een unieke architectuur voor het garanderen van een hoge beschikbaarheid (HA) van kritische diensten. Sommige onderdelen van deze architectuur zijn ontwikkeld door Microsoft om automatische failover te bieden. Andere componenten zijn standaard Apache-componenten die worden geïmplementeerd om specifieke services te ondersteunen. In dit artikel wordt de architectuur van het HA-servicemodel in HDInsight uitgelegd, hoe HDInsight failover voor HA-services ondersteunt en best practices om te herstellen van andere serviceonderbrekingen.

## <a name="high-availability-infrastructure"></a>Infrastructuur met hoge beschikbaarheid

HDInsight biedt aangepaste infrastructuur om ervoor te zorgen dat vier primaire services hoog beschikbaar zijn met automatische failovermogelijkheden:

- Apache Ambari-server
- Application Timeline Server voor Apache YARN
- Job history Server voor Hadoop MapReduce
- Apache Livy

Deze infrastructuur bestaat uit een aantal services en softwarecomponenten, waarvan sommige zijn ontworpen door Microsoft. De volgende componenten zijn uniek voor het HDInsight-platform:

- Slave failover controller
- Master failovercontroller
- Slave hoge beschikbaarheid service
- Master high availability service

![infrastructuur met hoge beschikbaarheid](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Er zijn ook andere diensten met hoge beschikbaarheid, die worden ondersteund door open source Apache betrouwbaarheidcomponenten. Deze componenten zijn ook aanwezig op HDInsight clusters:

- Hadoop File System (HDFS) NameNode
- YARN ResourceManager
- HBase-master

In de volgende secties vindt u meer informatie over de manier waarop deze services samenwerken.

## <a name="hdinsight-high-availability-services"></a>HDInsight-services met hoge beschikbaarheid

Microsoft biedt ondersteuning voor de vier Apache-services in de volgende tabel in HDInsight-clusters. Om hen te onderscheiden van services met een hoge beschikbaarheid die worden ondersteund door componenten van Apache, worden ze *HDInsight HA-services*genoemd.

| Service | Clusterknooppunten | Clustertypen | Doel |
|---|---|---|---|
| Apache Ambari-server| Actieve headnode | Alle | Bewaakt en beheert het cluster.|
| Application Timeline Server voor Apache YARN | Actieve headnode | Alles behalve Kafka | Hiermee behoudt u foutopsporingsgegevens over GAREN-taken die op het cluster worden uitgevoerd.|
| Job history Server voor Hadoop MapReduce | Actieve headnode | Alles behalve Kafka | Onderhoudt foutopsporingsgegevens voor mapMinder taken.|
| Apache Livy | Actieve headnode | Spark | Eenvoudige interactie met een Spark-cluster via een REST-interface mogelijk maakt |

>[!Note]
> HDInsight Enterprise Security Package (ESP)-clusters bieden momenteel alleen de Ambari-server hoge beschikbaarheid.

### <a name="architecture"></a>Architectuur

Elk HDInsight-cluster heeft respectievelijk twee headnodes in actieve en stand-bymodi. De HDInsight HA-services draaien alleen op headnodes. Deze services moeten altijd op de actieve headnode worden uitgevoerd en worden gestopt en in de onderhoudsmodus op de stand-by headnode geplaatst.

Om de juiste toestanden van HA-services te behouden en een snelle failover te bieden, maakt HDInsight gebruik van Apache ZooKeeper, een coördinatieservice voor gedistribueerde toepassingen, om actieve headnode-verkiezingen uit te voeren. HDInsight voorziet ook in enkele Java-achtergrondprocessen, die de failoverprocedure voor HDInsight HA-services coördineren. Deze diensten zijn de volgende: de master failover controller, de slave failover controller, de *master-ha-service*, en de *slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper is een krachtige coördinatieservice voor gedistribueerde toepassingen. In productie draait ZooKeeper meestal in de gerepliceerde modus waarin een gerepliceerde groep ZooKeeper-servers een quorum vormen. Elk HDInsight-cluster heeft drie ZooKeeper-knooppunten waarmee drie ZooKeeper-servers een quorum kunnen vormen. HDInsight heeft twee ZooKeeper quorums die parallel lopen met elkaar. Eén quorum bepaalt de actieve headnode in een cluster waarop HDInsight HA-services moeten worden uitgevoerd. Een ander quorum wordt gebruikt om HA-services van Apache te coördineren, zoals in latere secties wordt beschreven.

### <a name="slave-failover-controller"></a>Slave failover controller

De slave failover controller draait op elk knooppunt in een HDInsight cluster. Deze controller is verantwoordelijk voor het starten van de Ambari agent en *slave-ha-service* op elk knooppunt. Het vraagt periodiek het eerste ZooKeeper quorum over de actieve headnode. Wanneer de actieve en stand-by headnodes veranderen, voert de slave failover-controller het volgende uit:

1. Hiermee wordt het hostconfiguratiebestand bijgewerkt.
1. Start Ambari-agent opnieuw.

De *slave-ha-service* is verantwoordelijk voor het stoppen van de HDInsight HA-services (behalve Ambari-server) op de stand-by headnode.

### <a name="master-failover-controller"></a>Master failovercontroller

Een master failover controller draait op beide headnodes. Beide master failover controllers communiceren met het eerste ZooKeeper quorum om de headnode te nomineren waarop ze draaien als de actieve headnode.

Als bijvoorbeeld de master failovercontroller op headnode 0 de verkiezing wint, vinden de volgende wijzigingen plaats:

1. Headnode 0 wordt actief.
1. De master failover controller start Ambari server en de *master-ha-service* op headnode 0.
1. De andere master failover controller stopt Ambari server en de *master-ha-service* op headnode 1.

De master-ha-service draait alleen op de actieve headnode, stopt de HDInsight HA-services (behalve Ambari-server) op standby headnode en start ze op actieve headnode.

### <a name="the-failover-process"></a>Het failoverproces

![failoverproces](./media/hdinsight-high-availability-components/failover-steps.png)

Een gezondheidsmonitor wordt op elke headnode uitgevoerd, samen met de master failover-controller om hearbeat-meldingen naar het Zookeeper-quorum te verzenden. De headnode wordt in dit scenario beschouwd als een HA-service. De gezondheidsmonitor controleert of elke service met hoge beschikbaarheid gezond is en of deze klaar is om deel te nemen aan de leiderschapsverkiezing. Zo ja, dan zal deze headnode deelnemen aan de verkiezingen. Zo niet, dan zal het stoppen met de verkiezingen totdat het weer klaar wordt.

Als de stand-by headnode ooit leiderschap behaalt en actief wordt (zoals in het geval van een storing bij het vorige actieve knooppunt), zal de master failover-controller alle HDInsight HA-services erop starten. De master failover controller zal ook stoppen met deze diensten op de andere headnode.

Voor HDInsight HA-servicefouten, zoals een service die is uitgeschakeld of ongezond is, moet de hoofdfailovercontroller de services automatisch opnieuw starten of stoppen op basis van de headnodestatus. Gebruikers moeten hdinsight HA-services niet handmatig starten op beide hoofdknooppunten. In plaats daarvan u automatische of handmatige failover toestaan om de service te helpen herstellen.

### <a name="inadvertent-manual-intervention"></a>Onbedoelde handmatige interventie

HDInsight HA-services mogen alleen op de actieve headnode worden uitgevoerd en worden automatisch opnieuw gestart wanneer dat nodig is. Aangezien individuele HA-services geen eigen statusmonitor hebben, kan failover niet worden geactiveerd op het niveau van de afzonderlijke service. Failover is verzekerd op knooppuntniveau en niet op serviceniveau.

### <a name="some-known-issues"></a>Enkele bekende problemen

- Wanneer u handmatig een HA-service start op de stand-by headnode, stopt deze niet totdat de volgende failover plaatsvindt. Wanneer HA-services op beide hoofdknooppunten worden uitgevoerd, zijn enkele potentiële problemen: Ambari UI is ontoegankelijk, Ambari gooit fouten, YARN, Spark en Oozie-taken kunnen vast komen te zitten.

- Wanneer een HA-service op de actieve headnode stopt, wordt deze pas opnieuw opgestart als de volgende failover plaatsvindt of de master failovercontroller/master-ha-service opnieuw wordt opgestart. Wanneer een of meer HA-services stoppen op de actieve headnode, vooral wanneer Ambari-server stopt, is ambari-gebruikersinterface ontoegankelijk, andere potentiële problemen zijn YARN, Spark en Oozie-fouten.

## <a name="apache-high-availability-services"></a>Apache hoge beschikbaarheid diensten

Apache biedt een hoge beschikbaarheid voor HDFS NameNode, YARN ResourceManager en HBase Master, die ook beschikbaar zijn in HDInsight-clusters. In tegenstelling tot HDInsight HA-services worden ze ondersteund in ESP-clusters. Apache HA-services communiceren met het tweede ZooKeeper-quorum (beschreven in de bovenstaande sectie) om actieve/stand-by-statussen te kiezen en automatische failover uit te voeren. In de volgende secties wordt beschreven hoe deze services werken.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Naamnode van hadoop distributed bestandssysteem (HDFS)

HDInsight clusters op basis van Apache Hadoop 2.0 of hoger bieden NameNode hoge beschikbaarheid. Er zijn twee NameNodes uitgevoerd op de hoofdnoden, die zijn geconfigureerd voor automatische failover. De NameNodes gebruiken de *ZKFailoverController* om te communiceren met Zookeeper om te kiezen voor actieve/stand-by status. De *ZKFailoverController* draait op beide headnodes en werkt op dezelfde manier als de master failover controller hierboven.

Het tweede Zookeeper quorum is onafhankelijk van het eerste quorum, dus de actieve NameNode mag niet op de actieve headnode draaien. Wanneer de actieve NameNode dood of ongezond is, wint de stand-by NameNode de verkiezing en wordt actief.

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

HDInsight-clusters op basis van Apache Hadoop 2.4 of hoger ondersteunen YARN ResourceManager hoge beschikbaarheid. Er zijn twee ResourceManagers, rm1 en rm2, die respectievelijk op headnode 0 en headnode 1 draaien. Net als NameNode is YARN ResourceManager ook geconfigureerd voor automatische failover. Een andere ResourceManager wordt automatisch gekozen om actief te zijn wanneer de huidige actieve ResourceManager naar beneden gaat of niet reageert.

YARN ResourceManager gebruikt zijn ingebouwde *ActiveStandbyElector* als een faaldetector en leiderkiezer. In tegenstelling tot HDFS NameNode heeft YARN ResourceManager geen aparte ZKFC daemon nodig. De actieve ResourceManager schrijft zijn staten in Apache Zookeeper.

De hoge beschikbaarheid van de YARN ResourceManager is onafhankelijk van NameNode en andere HDInsight HA-services. De actieve ResourceManager mag niet worden uitgevoerd op de actieve headnode of de headnode waar de actieve NameNode wordt uitgevoerd. Zie [ResourceManager hoge beschikbaarheid](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)voor meer informatie over yarn resourcemanager met hoge beschikbaarheid.

### <a name="hbase-master"></a>HBase-master

HDInsight HBase-clusters ondersteunen HBase Master hoge beschikbaarheid. In tegenstelling tot andere HA-services, die op headnodes draaien, draaien HBase Masters op de drie Zookeeper-knooppunten, waar een van hen de actieve meester is en de andere twee stand-by. Net als NameNode coördineert HBase Master met Apache Zookeeper voor de leader verkiezing en doet automatisch failover wanneer de huidige actieve meester problemen heeft. Er is slechts één actieve HBase Master op elk gewenst moment.

## <a name="next-steps"></a>Volgende stappen

- [Beschikbaarheid en betrouwbaarheid van Apache Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md)
- [Azure HDInsight virtuele netwerkarchitectuur](hdinsight-virtual-network-architecture.md)
