---
title: 'Zelfstudie: Werken met de Streams-API van Apache Kafka - Azure HDInsight '
description: 'Zelf studie: informatie over het gebruik van de Apache Kafka streams API met Kafka in HDInsight. Met deze API kunt u gegevensstromen tussen onderwerpen in Kafka verwerken.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/20/2020
ms.openlocfilehash: 2885fccd95d09149ae496b80a658f34e5b697d0b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80064481"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Zelf studie: Apache Kafka streams-API in azure HDInsight gebruiken

Leer hoe u een toepassing maakt die gebruikmaakt van de Streams-API van Apache Kafka en hoe u deze uitvoert met Kafka in HDInsight.

De voorbeeldtoepassing die wordt gebruikt in deze zelfstudie, is een app voor het tellen van woorden die via een stream worden aangeboden. Eerst worden er tekstgegevens gelezen uit een onderwerp van Kafka, vervolgens worden afzonderlijke woorden uitgepakt en ten slotte worden de woorden en het aantal woorden opgeslagen in een ander Kafka-onderwerp.

De verwerking van Kafka-gegevensstromen gebeurt vaak met Apache Spark of Apache Storm. Kafka-versie 1.1.0 (in HDInsight 3,5 en 3,6) heeft de Kafka streams API geïntroduceerd. Met deze API kunt u gegevensstromen transformeren tussen invoer- en uitvoeronderwerpen. In sommige gevallen kan dit een alternatief zijn voor het maken van een streamingoplossing op basis van Spark of Storm.

Meer informatie over de Streams-API van Kafka vindt u in het Engelstalige artikel [Intro to Streams](https://kafka.apache.org/10/documentation/streams/) op Apache.org.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De code begrijpen
> * De toepassing compileren en implementeren
> * Kafka onderwerpen configureren
> * De code uitvoeren

## <a name="prerequisites"></a>Vereisten

* Een Kafka in HDInsight 3.6-cluster. Zie [Aan de slag met Apache Kafka in HDInsight](apache-kafka-get-started.md) voor informatie over het maken van een Kafka-cluster in HDInsight.

* Voer de stappen in het document [Consumer- en Producer-API's van Apache Kafka](apache-kafka-producer-consumer-api.md) uit. In de stappen in dit document worden de voorbeeldtoepassing en onderwerpen gebruikt die in deze zelfstudie zijn gemaakt.

* [Jdk-versie 8 (Java Developer Kit)](https://aka.ms/azure-jdks) of een equivalent, zoals openjdk.

* [Apache Maven](https://maven.apache.org/download.cgi) is op de juiste wijze [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project voor het maken van een systeem voor Java-projecten.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>De code begrijpen

De voorbeeld toepassing bevindt [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)zich in de `Streaming` submap. De toepassing bestaat uit twee bestanden:

* `pom.xml`: dit bestand definieert de projectafhankelijkheden, de Java-versie en de pakketmethoden.
* `Stream.java`: dit bestand implementeert de streaming-logica.

### <a name="pomxml"></a>Pom.xml

Belangrijke aandachtspunten voor het bestand `pom.xml`:

* Afhankelijkheden: dit project is afhankelijk van de Kafka-API Streams, die wordt geleverd door het pakket `kafka-clients`. Deze afhankelijkheid wordt gedefinieerd met de volgende XML-code:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    De vermelding `${kafka.version}` wordt gedeclareerd in de sectie `<properties>..</properties>` van `pom.xml`, en wordt geconfigureerd voor de Kafka-versie van het HDInsight-cluster.

* Plugins: de Maven-plugins bieden diverse mogelijkheden. In dit project worden de volgende plugins of invoegtoepassingen gebruikt:

    * `maven-compiler-plugin`: wordt gebruikt om de Java-versie die wordt gebruikt door het project in te stellen op 8. Java 8 is vereist voor HDInsight 3.6.
    * `maven-shade-plugin`: Wordt gebruikt voor het genereren van een uber jar dat deze toepassing bevat, en eventuele afhankelijkheden. Het wordt ook gebruikt om het ingangs punt van de toepassing in te stellen, zodat u het jar-bestand rechtstreeks kunt uitvoeren zonder dat u de hoofd klasse hoeft op te geven.

### <a name="streamjava"></a>Stream.java

Het bestand [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) gebruikt de Streams-API voor het implementeren van een toepassing voor het tellen van woorden. De toepassing leest gegevens uit een Kafka-onderwerp met de naam `test` en schrijft het gelezen aantal woorden naar een onderwerp met de naam `wordcounts`.

Met de volgende code wordt de toepassing gedefinieerd:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>Het voorbeeld compileren en implementeren

Als u het project wilt implementeren in het Kafka-cluster in HDInsight, voert u de volgende stappen uit:

1. Stel de huidige map in op de locatie van `hdinsight-kafka-java-get-started-master\Streaming` de Directory en gebruik vervolgens de volgende opdracht om een jar-pakket te maken:

    ```cmd
    mvn clean package
    ```

    Met deze opdracht maakt u het pakket op `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `clustername` door de naam van het cluster. Gebruik de volgende opdracht om het `kafka-streaming-1.0-SNAPSHOT.jar` bestand te kopiëren naar uw HDInsight-cluster. Voer het wachtwoord voor het SSH-gebruikersaccount in wanneer hierom wordt gevraagd.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Apache Kafka-onderwerpen maken

1. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `CLUSTERNAME` door de naam van het cluster. Open een SSH-verbinding met het cluster door de volgende opdracht in te voeren. Voer het wachtwoord voor het SSH-gebruikersaccount in wanneer hierom wordt gevraagd.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Installeer [JQ](https://stedolan.github.io/jq/), een JSON-processor op de opdracht regel. Voer bij de open SSH-verbinding de volgende opdracht in `jq`die u wilt installeren:

    ```bash
    sudo apt -y install jq
    ```

3. Wachtwoord variabele instellen. Vervang `PASSWORD` door het wacht woord voor cluster aanmelding en voer de volgende opdracht in:

    ```bash
    export password='PASSWORD'
    ```

4. Haal de juiste cluster naam op. De daad werkelijke behuizing van de cluster naam kan anders zijn dan verwacht, afhankelijk van hoe het cluster is gemaakt. Met deze opdracht wordt de daad werkelijke behuizing opgehaald en opgeslagen in een variabele. Voer de volgende opdracht in:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Als u dit proces van buiten het cluster uitvoert, is er een andere procedure voor het opslaan van de cluster naam. Haal de cluster naam op in kleine letters van de Azure Portal. Vervang vervolgens de cluster naam door `<clustername>` in de volgende opdracht en voer deze uit: `export clusterName='<clustername>'`.  

5. Gebruik de volgende opdrachten als u de Kafka-brokerhosts en de Apache Zookeeper-hosts wilt opvragen. Voer desgevraagd het wachtwoord voor het account voor clusteraanmelding (admin).

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Voor deze opdrachten is toegang tot Ambari vereist. Als uw cluster zich achter een NSG bevindt, voert u deze opdrachten uit vanaf een computer die toegang heeft tot Ambari.

6. Gebruik de volgende opdrachten om de onderwerpen te maken die worden gebruikt door de streaming-bewerking:

    > [!NOTE]  
    > Er kan een foutbericht worden weergegeven dat het onderwerp `test` al bestaat. Dit is geen probleem omdat het onderwerp mogelijk in de zelfstudie over de Producer- en Consumer-API's van Apache Kafka is gemaakt.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    De onderwerpen worden gebruikt voor de volgende doeleinden:

   * `test`: dit is het onderwerp waarin records worden ontvangen. De streaming-toepassing leest uit dit onderwerp.
   * `wordcounts`: dit is het onderwerp waarin de uitvoer van de streaming-toepassing wordt opgeslagen.
   * `RekeyedIntermediateTopic`: dit onderwerp wordt gebruikt voor het opnieuw partitioneren van gegevens wanneer het aantal woorden wordt bijgewerkt door de operator `countByKey`.
   * `wordcount-example-Counts-changelog`: in dit onderwerp worden statuswaarden opgeslagen die worden gebruikt door de bewerking `countByKey`.

    Kafka in HDInsight kan ook worden geconfigureerd voor het automatisch maken van onderwerpen. Zie [How to configure Apache Kafka on HDInsight to automatically create topics](apache-kafka-auto-create-topics.md) (Apache Kafka in HDInsight configureren voor het automatisch maken van onderwerpen) voor meer informatie.

## <a name="run-the-code"></a>De code uitvoeren

1. Gebruik de volgende opdracht om de streaming-toepassing te starten als een achtergrondproces:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Er kan een waarschuwing over Apache log4j worden weergegeven. Deze kunt u negeren.

2. Als u records wilt verzenden naar het onderwerp `test`, gebruikt u de volgende opdracht gebruiken om de Producer-toepassing te starten:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Zodra de toepassing is voltooid, gebruikt u de volgende opdracht om de informatie weer te geven die is opgeslagen in het onderwerp `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    De parameters `--property` geven de Consumer-API opdracht om de key (het woord) samen met de count (waarde) weer te geven. Deze parameter configureert ook welke deserializer moet worden gebruikt bij het lezen van deze waarden uit Kafka.

    De uitvoer lijkt op het volgende:

        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    De parameter `--from-beginning` configureert de Consumer om te beginnen bij het begin van de records die zijn opgeslagen in het onderwerp. Het aantal wordt met elk ontvangen woord opgehoogd, zodat het onderwerp meerdere vermeldingen voor elk woord bevat, met een oplopend aantal.

4. Gebruik __Ctrl+C__ om de Producer af te sluiten. Blijf op __Ctrl+C__ drukken om de toepassing en de Consumer af te sluiten.

5. Gebruik de volgende opdrachten om de onderwerpen te verwijderen die door de streaming-bewerking worden gebruikt:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, kunt u de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende HDInsight-cluster en eventuele andere resources die aan de resourcegroep zijn gekoppeld, verwijderd.

Ga als volgt te werk om de resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies __Resourcegroepen__ om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen en klik met de rechtermuisknop op de knop __Meer__ (... ) aan de rechterkant van de vermelding.
3. Selecteer __Resourcegroep verwijderen__ en bevestig dit.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Streams-API van Apache Kafka gebruikt met Kafka in HDInsight. Gebruik de volgende informatie om meer te weten te komen over het werken met Kafka.

> [!div class="nextstepaction"]
> [Apache Kafka-logboeken analyseren](apache-kafka-log-analytics-operations-management.md)
