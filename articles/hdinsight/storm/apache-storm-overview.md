---
title: Wat is Apache Storm - Azure HDInsight
description: Met Apache Storm kunt u gegevensstromen in realtime verwerken. Met Azure HDInsight kunt u eenvoudig Storm-clusters maken in de Azure-cloud. Met Visual Studio kunt u Storm-oplossingen schrijven in C# en deze vervolgens implementeren in uw HDInsight Storm-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 97b1466e6ac1f2c2dfb931655b64b0f9937ba21d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82183259"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Wat is Apache Storm in Azure HDInsight?

[Apache Storm](https://storm.apache.org/) is een gedistribueerd, fouttolerant en open-source computingsysteem. U kunt Storm gebruiken om gegevens stromen in realtime te verwerken met [Apache Hadoop](../hadoop/apache-hadoop-introduction.md). Storm-oplossingen kunnen ook een gegarandeerde verwerking van gegevens bieden, met de mogelijkheid om gegevens die de eerste keer niet zijn verwerkt, opnieuw af te spelen.

## <a name="why-use-apache-storm-on-hdinsight"></a>Waarom Apache Storm op HDInsight gebruiken?

Storm op HDInsight biedt de volgende functies:

* __99% Service Level Agreement (Sla) op Storm-uptime__: Storm op HDInsight wordt geleverd met volledige continue ondersteuning. Storm op HDInsight beschikt eveneens over een SLA van 99,9 procent. Dit betekent dat Microsoft garandeert dat een Storm-cluster minimaal 99,9 procent van de tijd externe verbinding heeft. Zie [Ondersteuning van Azure](https://azure.microsoft.com/support/options/) voor meer informatie. Zie ook [Sla-gegevens voor HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) -document.

* Ondersteunt de mogelijkheid om eenvoudig aanpassingen te implementeren door tijdens of na het maken van een Storm-cluster scripts voor dat cluster uit te voeren. Zie [HDInsight-clusters aanpassen met scriptacties](../hdinsight-hadoop-customize-cluster-linux.md) voor meer informatie.

* **Oplossingen maken in meerdere talen**: u kunt Storm-onderdelen schrijven in de taal van uw keuze, bijvoorbeeld in Java, C# of Python.

    * Integreert Visual Studio met HDInsight voor het ontwikkelen, beheren en controleren van C#-topologieën. Zie [C# Storm-topologieën met de hulpprogramma's van HDInsight voor Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.

    * Biedt ondersteuning voor de Trident Java-interface. U kunt Storm-topologieën maken die ondersteuning bieden voor een eenmalige verwerking van berichten, transactionele DataStore-persistentie en een aantal algemene Stream Analytics-bewerkingen.

* **Dynamische schaalbaarheid**: u kunt werkknooppunten toevoegen of verwijderen zonder de actieve Storm-topologieën te beïnvloeden. U kunt actieve topologieën deactiveren en opnieuw activeren om te profiteren van nieuwe knoop punten die worden toegevoegd via schaal bewerkingen.

* **Streaming-pijp lijnen maken met meerdere Azure-Services**: Storm op HDInsight kan worden geïntegreerd met andere Azure-Services. Zoals Event Hubs, SQL Database, Azure Storage en Azure Data Lake Storage. Zie [gebeurtenissen van Event hubs verwerken met Apache Storm op HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)voor een voorbeeld oplossing die kan worden geïntegreerd met Azure-Services.

Zie [deze Engelstalige site](https://storm.apache.org/Powered-By.html) voor een lijst met bedrijven die Apache Storm gebruiken voor hun oplossingen voor realtime analyse.

Zie [een Apache Storm topologie maken en bewaken in azure HDInsight](apache-storm-quickstart.md)om aan de slag te gaan met storm.

## <a name="how-does-apache-storm-work"></a>Hoe werkt Apache Storm

Storm voert topologieën uit in plaats van de [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) -taken die u mogelijk kent. Storm-topologieën bestaan uit meerdere onderdelen die zijn gerangschikt in een Directed Acyclic Graph (DAG). Gegevens stromen tussen de onderdelen in de grafiek. Elk onderdeel verbruikt een of meer gegevensstromen en kan eventueel een of meer stromen genereren. Het volgende diagram toont hoe de gegevens stromen tussen de onderdelen van een eenvoudige topologie voor het tellen van woorden:

![Voorbeeld van hoe onderdelen zijn gerangschikt in een Storm-topologie](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Via Spout-onderdelen worden gegevens overgebracht naar een topologie. Deze onderdelen introduceren een of meer stromen in de topologie.

* Bolt-onderdelen verwerken stromen die afkomstig zijn van spouts of andere bolts. Bolts kunnen eventueel nieuwe stromen in de topologie introduceren. Bolts zijn ook verantwoordelijk voor het wegschrijven van gegevens naar externe services of opslag, zoals HDFS, Kafka of HBase.

## <a name="reliability"></a>Betrouwbaarheid

Apache Storm zorgt ervoor dat elk binnenkomend bericht altijd volledig wordt verwerkt, zelfs wanneer de gegevensanalyse is verspreid over honderden knooppunten.

Het Nimbus-knoop punt biedt vergelijk bare functionaliteit als Apache Hadoop JobTracker. Nimbus wijst taken toe aan andere knoop punten in een cluster via Apache ZooKeeper. Zookeeper-knoop punten bieden coördinatie voor een cluster en helpen communicatie tussen Nimbus en het supervisor proces op de worker-knoop punten. Als een verwerkingsknooppunt wordt uitgeschakeld, wordt het Nimbus-knooppunt hiervan op de hoogte gesteld en worden de taak en de bijbehorende gegevens toegewezen aan een ander knooppunt.

Apache Storm-clusters worden standaard geconfigureerd met slechts één Nimbus-knooppunt. Storm in HDInsight ondersteunt twee Nimbus-knooppunten. Als het primaire knooppunt uitvalt, schakelt het Storm-cluster over naar het secundaire knooppunt en wordt het primaire knooppunt hersteld. Het volgende diagram illustreert de taakstroomconfiguratie voor Storm op HDInsight:

![Diagram van nimbus, zookeeper en supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-use"></a>Gebruiksgemak

|Gebruiken |Beschrijving |
|---|---|
|SSH-connectiviteit (Secure Shell)|Met SSH kunt u via internet toegang krijgen tot de hoofd knooppunten van uw Storm-cluster. U kunt opdrachten rechtstreeks op het cluster uitvoeren met behulp van SSH. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.|
|Webconnectiviteit|Alle HDInsight-clusters bieden de Ambari-webgebruikersinterface. Met de Ambare-webgebruikersinterface kunt u eenvoudig services op het cluster controleren, configureren en beheren. Storm-clusters bieden ook de Storm-gebruikersinterface. Met behulp van de Storm-gebruikersinterface kunt u actieve Storm-topologieën controleren en beheren vanuit de browser. Zie [HDInsight beheren met de Web-UI van Apache Ambari](../hdinsight-hadoop-manage-ambari.md) en [controleren en beheren met behulp van de Apache Storm UI](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) -documenten voor meer informatie.|
|Azure PowerShell en Azure CLI|Power shell en Azure CLI bieden beide opdracht regel Programma's die u vanuit uw client systeem kunt gebruiken om te werken met HDInsight en andere Azure-Services.|
|Integratie met Visual Studio|Azure Data Lake-Hulpprogram Ma's voor Visual Studio bevatten project sjablonen voor het maken van C# Storm-topologieën met behulp van het SCP.NET-Framework. Data Lake Tools biedt ook hulpprogramma's voor het implementeren, bewaken en beheren van oplossingen met Storm op HDInsight. Zie [C# Storm-topologieën met de hulpprogramma's van HDInsight voor Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.|

## <a name="integration-with-other-azure-services"></a>Integratie met andere Azure-services

* __Azure data Lake Storage__: Zie [Azure Data Lake Storage gebruiken met Apache Storm op HDInsight](apache-storm-write-data-lake-store.md).

* __Azure Event Hubs__: raadpleeg de volgende voorbeelden voor een voorbeeld van het gebruik van Event Hubs met een Storm-cluster:

    * [Gebeurtenissen verwerken vanuit Azure Event Hubs met Apache Storm op HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Gebeurtenissen verwerken vanuit Azure Event Hubs met Apache Storm op HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL-Database__, __Cosmos DB__, __Event Hubs__ en __HBase__: in de Data Lake-hulpmiddelen voor Visual Studio zijn sjabloonvoorbeelden opgenomen. Zie [een C#-topologie ontwikkelen voor Apache Storm op HDInsight](apache-storm-develop-csharp-visual-studio-topology.md)voor meer informatie.

## <a name="apache-storm-use-cases"></a>Use cases van Apache Storm

Hier volgen enkele veelvoorkomende scenario's waarvoor u Storm in HDInsight zou kunnen gebruiken:

* Internet der dingen (IoT)
* Fraudedetectie
* Sociale analyses
* Extractie, transformatie en laden (ETL)
* Netwerkbewaking
* Search
* Mobile Engagement

Zie [hoe bedrijven Apache Storm document gebruiken](https://storm.apache.org/Powered-By.html) voor informatie over praktijk scenario's.

## <a name="development"></a>Ontwikkeling

Met hulp van Data Lake-tools voor Visual Studio kunnen .NET-ontwikkelaars topologieën ontwerpen en implementeren voor Visual Studio. U kunt ook hybride topologieën maken die gebruikmaken van Java- en C#-onderdelen. Zie [C#-topologieën met Visual Studio ontwikkelen voor Apache Storm op HDInsight](apache-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.

U kunt ook Java-oplossingen ontwikkelen met behulp van de IDE van uw keuze. Zie [Java-topologieën ontwikkelen voor Apache Storm op HDInsight](apache-storm-develop-java-topology.md)voor meer informatie.

Python kan ook worden gebruikt voor het ontwikkelen van Storm-onderdelen. Zie [Apache Storm topologieën ontwikkelen met behulp van python op HDInsight](apache-storm-develop-python-topology.md)voor meer informatie.

## <a name="common-development-patterns"></a>Algemene ontwikkelingspatronen

### <a name="guaranteed-message-processing"></a>Gegarandeerde berichtverwerking

Apache Storm kan verschillende niveaus van gegarandeerde berichtverwerking bieden. Een eenvoudige Storm-toepassing garandeert bijvoorbeeld ten minste één keer dat de verwerking is gegarandeerd en Trident kan gegarandeerd precies één keer worden verwerkt. Zie [garanties voor gegevens verwerking](https://storm.apache.org/about/guarantees-data-processing.html) op Apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Het patroon voor het lezen van een invoer-tuple, het verzenden van nul of meer Tuples en het bevestigen van de invoer tuple onmiddellijk aan het einde van de methode Execute is gebruikelijk. Storm biedt de [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html)-interface voor het automatiseren van dit patroon.

### <a name="joins"></a>Samenvoegingen

Hoe gegevensstromen worden gekoppeld, varieert per toepassing. U kunt bijvoorbeeld elke tuple uit meerdere streams samen voegen in één nieuwe stream, of alleen batches van Tuples voor een specifiek venster samen voegen. In beide gevallen kunt u hiervoor [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) gebruiken. Veldgroepering is een manier om te definiëren hoe tuples worden gerouteerd naar bolts.

In het volgende Java-voorbeeld wordt fieldsGrouping gebruikt om tuples die afkomstig zijn uit de onderdelen 1, 2 en 3, te routeren naar de MyJoiner-bolt:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Batches

Apache Storm biedt een intern timingmechanisme dat bekend staat als een 'tick tuple'. U kunt instellen hoe vaak in uw topologie een tick tuple wordt verzonden.

Zie [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java) voor een voorbeeld van het gebruik van een tick tuple uit een C#-onderdeel.

### <a name="caches"></a>Caches

In-memory caching wordt vaak gebruikt als mechanisme om de verwerking te versnellen, omdat de veelgebruikte assets in het geheugen blijven staan. Omdat een topologie wordt verdeeld over meerdere knooppunten en meerdere processen binnen elk knooppunt, is het goed om het gebruik van [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) te overwegen. Gebruik `fieldsGrouping` om ervoor te zorgen dat tuples met velden die worden gebruikt voor het opzoeken van de cache, altijd naar hetzelfde proces worden gerouteerd. Met deze groeperingsfunctionaliteit voorkomt u dubbele cachevermeldingen voor verschillende processen.

### <a name="stream-top-n"></a>'Top N' van stromen

Wanneer uw topologie afhankelijk is van de berekening van de waarde van een 'top N', moet u die waarde in parallel berekenen. Vervolgens moet u de uitvoer van die berekeningen samenvoegen tot een globale waarde. Dit kan door met [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) op veld te routeren voor parallelle verwerking. Vervolgens kunt u de uitvoer doorsturen naar een bolt die globaal de top N-waarde bepaalt.

Zie het [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java)-voorbeeld voor een voorbeeld van een berekening van een top N-waarde.

## <a name="logging"></a>Logboekregistratie

Storm gebruikt Apache Log4j 2 om informatie te registreren. Standaard wordt een grote hoeveelheid gegevens geregistreerd en kan het lastig zijn om de informatie te doorzoeken. U kunt een configuratiebestand voor logboekregistratie opnemen als onderdeel van uw Storm-topologie om de werking van de logboekregistratie te bepalen.

Zie het voorbeeld van een [op Java gebaseerde woordentelling](apache-storm-develop-java-topology.md) voor Storm op HDInsight, voor een voorbeeldtopologie die aantoont hoe u de logboekregistratie moet configureren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over real-time analyse oplossingen met Apache Storm op HDInsight:

* [Een Apache Storm topologie in azure HDInsight maken en bewaken](apache-storm-quickstart.md)
* [Example Storm toplogies and components for Apache Storm on HDInsight](apache-storm-example-topology.md) (Voorbeelden van Storm-topologieën en -onderdelen in HDInsight)
