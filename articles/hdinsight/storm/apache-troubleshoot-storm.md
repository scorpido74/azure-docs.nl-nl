---
title: Problemen met Storm oplossen met Azure HDInsight
description: Antwoorden op veelgestelde vragen over het gebruik van Apache Storm met Azure HDInsight.
keywords: Azure HDInsight, Storm, FAQ, handleiding voor probleemoplossing, veelvoorkomende problemen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271926"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Problemen met Apache Storm oplossen met Azure HDInsight

Meer informatie over de belangrijkste problemen en hun resoluties voor het werken met [Apache Storm](https://storm.apache.org/) payloads in [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hoe krijg ik toegang tot de gebruikersinterface van Storm op een cluster?

U hebt twee opties voor toegang tot de gebruikersinterface van Storm vanuit een browser:

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. Ga naar het Ambari dashboard.
2. Selecteer **Storm**in de lijst met services .
3. Selecteer in het menu **Snelle koppelingen** de optie **Gebruikersinterface van Storm**.

### <a name="direct-link"></a>Directe koppeling

U hebt toegang tot de gebruikersinterface van Storm op de volgende URL:

`https://<cluster DNS name>/stormui`

Voorbeeld: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hoe kan ik de informatie over de controlepuntcontrolevan storm-gebeurtenishub van de ene topologie naar de andere overbrengen?

Wanneer u topologieën ontwikkelt die lezen vanuit Azure Event Hubs met behulp van het HDInsight Storm-gebeurtenishub-uitloopbestand .jar, moet u een topologie implementeren met dezelfde naam op een nieuw cluster. U moet echter de controlecontrolegegevens die zijn vastgelegd voor [Apache ZooKeeper](https://zookeeper.apache.org/) op het oude cluster behouden.

### <a name="where-checkpoint-data-is-stored"></a>Waar controlepuntgegevens worden opgeslagen

Controlepuntgegevens voor verschuivingen worden door de gebeurtenishub in ZooKeeper opgeslagen in twee hoofdpaden:

- Niet-transactionele uitlooppunten worden `/eventhubspout`opgeslagen in .

- Transactionele tuitcontrolecontrolepuntenworden `/transactional`opgeslagen in .

### <a name="how-to-restore"></a>Hoe te herstellen

Zie [hdinsight stormvoorbeelden](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)om de scripts en bibliotheken die u gebruikt om gegevens uit ZooKeeper te exporteren en vervolgens de gegevens terug te importeren naar ZooKeeper met een nieuwe naam.

De libmap bevat .jar-bestanden die de implementatie voor de export-/importbewerking bevatten. De bashmap heeft een voorbeeldscript dat laat zien hoe u gegevens exporteert van de ZooKeeper-server op het oude cluster en deze vervolgens importeert naar de ZooKeeper-server op het nieuwe cluster.

Voer het [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) script uit de ZooKeeper-knooppunten om gegevens te exporteren en vervolgens te importeren. Werk het script bij naar de juiste HDP-versie (Hortonworks Data Platform). (We werken aan het maken van deze scripts generiek in HDInsight. Algemene scripts kunnen worden uitgevoerd vanaf elk knooppunt op het cluster zonder wijzigingen door de gebruiker.)

De exportopdracht schrijft de metagegevens naar een HDFS-pad (Apache Hadoop Distributed File System) (in Azure Blob Storage of Azure Data Lake Storage) op een locatie die u instelt.

### <a name="examples"></a>Voorbeelden

#### <a name="export-offset-metadata"></a>Metagegevens voor verschuiving exporteren

1. Gebruik SSH om naar het cluster ZooKeeper te gaan op het cluster van waaruit de controlepuntverschuiving moet worden geëxporteerd.
2. Voer de volgende opdracht uit (nadat u de HDP-versietekenreeks `/stormmetadta/zkdata` hebt bijgewerkt) om zookeeper-verschuivingsgegevens naar het HDFS-pad te exporteren:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Metagegevens voor verschuiving importeren

1. Gebruik SSH om naar het cluster ZooKeeper te gaan op het cluster waaruit de controlepuntverschuiving moet worden geïmporteerd.
2. Voer de volgende opdracht uit (nadat u de HDP-versietekenreeks hebt `/stormmetadata/zkdata` bijgewerkt) om ZooKeeper-offsetgegevens van het HDFS-pad te importeren naar de ZooKeeper-server op het doelcluster:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Offsetmetagegevens verwijderen, zodat topologieën vanaf het begin kunnen beginnen met het verwerken van gegevens of vanaf een tijdstempel die de gebruiker kiest

1. Gebruik SSH om naar het zookeeper-cluster te gaan op het cluster waaruit de controlepuntverschuiving moet worden verwijderd.
2. Voer de volgende opdracht uit (nadat u de HDP-versietekenreeks hebt bijgewerkt) om alle verschuivingsgegevens van ZooKeeper in het huidige cluster te verwijderen:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hoe vind ik Stormbinaries op een cluster?

Storm binaries voor de huidige `/usr/hdp/current/storm-client`HDP stack zijn in . De locatie is hetzelfde, zowel voor hoofdknooppunten als voor werknemersknooppunten.

Er kunnen meerdere binaire bestanden voor specifieke HDP-versies in / `/usr/hdp/2.5.0.1233/storm`usr / hdp (bijvoorbeeld), De `/usr/hdp/current/storm-client` map is gekoppeld aan de nieuwste versie die op het cluster wordt uitgevoerd.

Zie [Verbinding maken met een HDInsight-cluster met SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) en Apache [Storm](https://storm.apache.org/)voor meer informatie.

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hoe bepaal ik de implementatietopologie van een stormcluster?

Identificeer eerst alle componenten die zijn geïnstalleerd met HDInsight Storm. Een cluster Storm bestaat uit vier knooppuntcategorieën:

* Gatewayknooppunten
* Hoofdknooppunten
* ZooKeeper knooppunten
* Werkknooppunten

### <a name="gateway-nodes"></a>Gatewayknooppunten

Een gatewayknooppunt is een gateway- en reverse proxy-service waarmee het publiek toegang heeft tot een actieve Ambari-beheerservice. Het behandelt ook Ambari leiderverkiezing.

### <a name="head-nodes"></a>Hoofdknooppunten

Stormhoofdknooppunten voeren de volgende services uit:
* Nimbus
* Ambari-server
* Ambari Metrics-server
* Ambari Metrics Collector
 
### <a name="zookeeper-nodes"></a>ZooKeeper knooppunten

HDInsight wordt geleverd met een ZooKeeper-quorum met drie node. De quorumgrootte is vast en kan niet opnieuw worden geconfigureerd.

Stormservices in het cluster zijn geconfigureerd om automatisch het ZooKeeper-quorum te gebruiken.

### <a name="worker-nodes"></a>Werkknooppunten

Met knooppunten stormwerkers worden de volgende services uitgevoerd:
* Supervisor
* Worker Java virtuele machines (JVMs), voor het uitvoeren van topologieën
* Ambari agent

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hoe vind ik Storm event hub tuit binaries voor ontwikkeling?

Zie de volgende bronnen voor meer informatie over het gebruik van Storm-gebeurtenishub.jar-bestanden met uw topologie.

### <a name="java-based-topology"></a>Op Java gebaseerde topologie

[Gebeurtenissen uit Azure Event Hubs verwerken met Apache Storm op HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-gebaseerde topologie (Mono op HDInsight 3.4+ Linux Storm clusters)

[Gebeurtenissen uit Azure-gebeurtenishubs verwerken met Apache Storm op HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Nieuwste Apache Storm event hub tuit binaries voor HDInsight 3.5+ Linux Storm clusters

Zie het [mvn-repo-leesme-bestand](https://github.com/hdinsight/mvn-repo/blob/master/README.md)voor meer informatie over het gebruik van de nieuwste Storm-eventhub die werkt met HDInsight 3.5+ Linux Storm-clusters.

### <a name="source-code-examples"></a>Voorbeelden van broncodes

Bekijk [voorbeelden](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) van lezen en schrijven vanuit Azure Event Hub met behulp van een Apache Storm-topologie (geschreven in Java) op een Azure HDInsight-cluster.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hoe vind ik Storm Log4J 2-configuratiebestanden op clusters?

Apache [Log4j](https://logging.apache.org/log4j/2.x/) 2-configuratiebestanden voor Storm-services te identificeren.

### <a name="on-head-nodes"></a>Op hoofdknooppunten

De Nimbus Log4J configuratie `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`wordt gelezen van .

### <a name="on-worker-nodes"></a>Op werkknooppunten

De configuratie van de supervisor `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`Log4J wordt voorgelezen van .

Het configuratiebestand van de werknemer `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`Log4J wordt gelezen van .

Voorbeelden:`/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Geen leader uitzondering

Bij het indienen van een topologie kan de `Topology submission exception, cause not a leader, the current leader is NimbusInfo`gebruiker een foutmelding ontvangen die vergelijkbaar is met: .

Om dit op te lossen, moet de gebruiker mogelijk een ticket indienen om de knooppunten opnieuw te starten/opnieuw opstarten. Zie voor meer [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html)informatie .

---

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

- Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
