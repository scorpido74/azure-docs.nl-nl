---
title: 'Quick Start: gebeurtenissen ontvangen met Apache Storm-Azure Event Hubs'
description: 'Snelstartgids: in dit artikel vindt u informatie over het ontvangen van gebeurtenissen van Azure Event Hubs met behulp van Apache Storm.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 90293da07d3a7ef1c32e5f82d35198d4ffa536b1
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717607"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Quick Start: gebeurtenissen ontvangen van Event Hubs met behulp van Apache Storm

[Apache Storm](https://storm.incubator.apache.org) is een gedistribueerd real-time berekenings systeem dat betrouw bare verwerking van niet-gebonden gegevens stromen vereenvoudigt. In deze sectie wordt beschreven hoe u een Azure Event Hubs Storm Spout gebruikt voor het ontvangen van gebeurtenissen van Event Hubs. Met Apache Storm kunt u gebeurtenissen splitsen over meerdere processen die worden gehost in verschillende knoop punten. De Event Hubs-integratie met Storm vereenvoudigt het verbruik van gebeurtenissen door de voortgang van de Zookeeper-installatie van Storm te verbeteren, zodat u permanente controle punten en parallelle ontvangst van Event Hubs kunt beheren.

Zie [Event hubs Overview (overzicht][Event Hubs overview]van Event hubs) voor meer informatie over het ontvangen van patronen.

## <a name="prerequisites"></a>Vereisten
Voordat u met Quick Start begint, moet u **een event hubs naam ruimte en een event hub maken**. Gebruik de [Azure Portal](https://portal.azure.com) om een naam ruimte van het type Event hubs te maken en de beheer referenties te verkrijgen die uw toepassing nodig heeft om met de Event hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. 

## <a name="create-project-and-add-code"></a>Project maken en code toevoegen

In deze zelf studie wordt gebruikgemaakt van een [HDInsight Storm][HDInsight Storm] -installatie, die wordt geleverd met de Event hubs Spout al beschikbaar.

1. Volg de procedure [Hdinsight Storm-](../hdinsight/storm/apache-storm-overview.md) aan de slag om een nieuw HDInsight-cluster te maken en maak er verbinding mee via Extern bureaublad.
2. Kopieer het `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` bestand naar uw lokale ontwikkel omgeving. Dit bevat de gebeurtenissen-Storm-Spout.
3. Gebruik de volgende opdracht om het pakket te installeren in het lokale archief maven. Hierdoor kunt u deze in een latere stap als referentie toevoegen aan het Storm-project.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Maak in eclips een nieuw Maven-project (Klik op **bestand**, vervolgens op **Nieuw**en vervolgens op **project**).
   
    ![Bestand-> Nieuw-> project][12]
5. Selecteer **standaard locatie van werk ruimte gebruiken**en klik vervolgens op **volgende**
6. Selecteer de archetype **maven-archetype-Quick** start en klik vervolgens op **Next (volgende** ).
7. Voer een **GroupId** en **ArtifactId**in en klik vervolgens op **volt ooien**
8. Voeg in **pom. XML**de volgende afhankelijkheden toe aan het knoop punt `<dependency>`.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. Maak in de map **src** een bestand met de naam **config. Properties** en kopieer de volgende inhoud, waarbij u de `receive rule key` en `event hub name` waarden vervangt:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    De waarde voor **eventhub. receive. credits** is afhankelijk van het aantal gebeurtenissen dat in een batch wordt vastgelegd voordat ze aan de Storm pijp lijn worden vrijgegeven. In dit voor beeld wordt deze waarde ingesteld op 10. In productie moet deze doorgaans worden ingesteld op hogere waarden. bijvoorbeeld 1024.
10. Maak een nieuwe klasse met de naam **LoggerBolt** met de volgende code:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Deze storm Schicht registreert de inhoud van de ontvangen gebeurtenissen. Dit kan eenvoudig worden uitgebreid om Tuples op te slaan in een opslag service. In het [HDInsight Storm met voor beeld van Event hub] wordt dezelfde benadering gebruikt om gegevens op te slaan in Azure Storage en Power bi.
11. Maak een klasse met de naam **LogTopology** met de volgende code:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Met deze klasse wordt een nieuw Event Hubs Spout gemaakt, waarbij de eigenschappen in het configuratie bestand worden gebruikt om het object te instantiëren. Het is belang rijk te weten dat in dit voor beeld zoveel spouts-taken worden gemaakt als het aantal partities in de Event Hub, om de maximale parallellisme te gebruiken die door die Event Hub is toegestaan.

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht][Event Hubs overview]
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm met voor beeld van Event hub]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
