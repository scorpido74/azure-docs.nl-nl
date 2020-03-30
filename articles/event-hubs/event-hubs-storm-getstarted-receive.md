---
title: 'Snelstart: gebeurtenissen ontvangen met Apache Storm - Azure Event Hubs'
description: 'Snelstart: in dit artikel vindt u informatie over het ontvangen van gebeurtenissen van Azure Event Hubs met Apache Storm.'
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73717607"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Snelstart: ontvang gebeurtenissen van eventhubs met Apache Storm

[Apache Storm](https://storm.incubator.apache.org) is een gedistribueerd real-time berekeningssysteem dat betrouwbare verwerking van niet-begrensde gegevensstromen vereenvoudigt. In dit gedeelte ziet u hoe u een storm van Azure Event Hubs gebruiken om gebeurtenissen van gebeurtenishubs te ontvangen. Met Apache Storm u gebeurtenissen splitsen over meerdere processen die in verschillende knooppunten worden gehost. De integratie van Event Hubs met Storm vereenvoudigt het evenementverbruik door de voortgang transparant te controleren met de installatie van Storm's Zookeeper, het beheren van permanente controlepunten en parallelle ontvangsts van Event Hubs.

Zie het [overzicht van gebeurtenishubs][Event Hubs overview]voor meer informatie over gebeurtenishubs.

## <a name="prerequisites"></a>Vereisten
Maak **eerst een naamruimte voor gebeurtenishubs en een gebeurtenishub**voordat u met de quickstart begint. Gebruik de [Azure-portal](https://portal.azure.com) om een naamruimte van het type Gebeurtenishubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. 

## <a name="create-project-and-add-code"></a>Project maken en code toevoegen

Deze tutorial maakt gebruik van een [HDInsight Storm][HDInsight Storm] installatie, die wordt geleverd met de Event Hubs tuit al beschikbaar.

1. Volg de [PROCEDURE HDInsight Storm - Get Started](../hdinsight/storm/apache-storm-overview.md) om een nieuw HDInsight-cluster te maken en maak er verbinding mee via Extern bureaublad.
2. Kopieer `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` het bestand naar uw lokale ontwikkelomgeving. Dit bevat de gebeurtenissen-storm-tuit.
3. Gebruik de volgende opdracht om het pakket in de lokale Maven-winkel te installeren. Hiermee u het in een latere stap als referentie toevoegen aan het Storm-project.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Maak in Eclipse een nieuw Maven-project (klik op **Bestand**, dan **nieuw**, dan **project).**
   
    ![Bestand -> Nieuw -> Project][12]
5. Selecteer **Standaardlocatie werkruimte gebruiken**en klik op **Volgende**
6. Selecteer het **archetype maven-archetype-quickstart** en klik op **Volgende**
7. Een **groupid** en **artefactid**invoegen en vervolgens op **Voltooien** klikken
8. Voeg in **pom.xml**de volgende `<dependency>` afhankelijkheden toe aan het knooppunt.

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

9. Maak in de map **src** een bestand met de naam **Config.properties** en kopieer de volgende inhoud en `receive rule key` `event hub name` de volgende waarden:

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
    De waarde voor **eventhub.receiver.credits** bepaalt hoeveel gebeurtenissen worden gebatched voordat ze worden vrijgegeven aan de Storm-pijplijn. Omwille van de eenvoud stelt dit voorbeeld deze waarde in op 10. In de productie moet het meestal worden ingesteld op hogere waarden; bijvoorbeeld 1024.
10. Maak een nieuwe klasse genaamd **LoggerBolt** met de volgende code:
    
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
    
    Deze Storm bout registreert de inhoud van de ontvangen gebeurtenissen. Dit kan eenvoudig worden uitgebreid om tuples op te slaan in een opslagservice. Het [voorbeeld HDInsight Storm with Event Hub] gebruikt dezelfde aanpak om gegevens op te slaan in Azure Storage en Power BI.
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

    Met deze klasse wordt een nieuwe uitloop van gebeurtenishubs gemaakt, waarbij de eigenschappen in het configuratiebestand worden gebruikt om deze te instantiëren. Het is belangrijk op te merken dat dit voorbeeld evenveel uitlooptaken maakt als het aantal partities in de gebeurtenishub, om de maximale parallellisme te gebruiken die door die gebeurtenishub is toegestaan.

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van gebeurtenishubs][Event Hubs overview]
* [Een gebeurtenishub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[VOORBEELD HDInsight Storm met Event Hub]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
