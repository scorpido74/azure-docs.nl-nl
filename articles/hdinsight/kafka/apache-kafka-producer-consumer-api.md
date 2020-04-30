---
title: "Zelf studie: producent-& consumenten-Api's Apache Kafka producer-Azure HDInsight"
description: Leer hoe u de Producer- en Consumer-API's van Apache Kafka gebruikt met Kafka in HDInsight. In deze zelfstudie leert u hoe u deze API's vanuit een Java-toepassing gebruikt met Kafka in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: 5a7d4d1917f65cd3d836db83600937a3e3d89de6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79239537"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Zelfstudie: Werken met de Producer- en Consumer-API's van Apache Kafka

Leer hoe u de Producer- en Consumer-API's van Apache Kafka gebruikt met Kafka in HDInsight.

Met de Producer-API van Kafka kunnen toepassingen gegevensstromen naar het Kafka-cluster verzenden. Met de Consumer-API van Kafka kunnen toepassingen gegevensstromen uit het cluster lezen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten
> * De code begrijpen
> * De toepassing compileren en implementeren
> * De toepassing uitvoeren in het cluster

Meer informatie over de [Producer-API](https://kafka.apache.org/documentation/#producerapi) en de [Consumer-API](https://kafka.apache.org/documentation/#consumerapi) kunt u lezen in de Apache-documentatie (Engelstalig).

## <a name="prerequisites"></a>Vereisten

* Apache Kafka in HDInsight-cluster. Zie [beginnen met Apache Kafka op HDInsight](apache-kafka-get-started.md)voor meer informatie over het maken van het cluster.
* [Jdk-versie 8 (Java Developer Kit)](https://aka.ms/azure-jdks) of een equivalent, zoals openjdk.
* [Apache Maven](https://maven.apache.org/download.cgi) is op de juiste wijze [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project voor het maken van een systeem voor Java-projecten.
* Een SSH-client, zoals putty. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>De code begrijpen

De voorbeeld toepassing bevindt [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)zich in de `Producer-Consumer` submap. Als u **Enterprise Security Package (ESP)** ingeschakelde Kafka-cluster gebruikt, moet u de toepassings versie gebruiken die zich `DomainJoined-Producer-Consumer` in de submap bevindt.

De toepassing bestaat hoofdzakelijk uit vier bestanden:
* `pom.xml`: dit bestand definieert de projectafhankelijkheden, de Java-versie en de pakketmethoden.
* `Producer.java`: dit bestand verstuurt willekeurige zinnen naar Kafka met behulp van de Producer-API.
* `Consumer.java`: dit bestand gebruikt de Consumer-API om gegevens te lezen uit Kafka en deze te verzenden naar STDOUT.
* `AdminClientWrapper.java`: In dit bestand wordt de-beheer-API gebruikt om Kafka-onderwerpen te maken, te beschrijven en te verwijderen.
* `Run.java`: de opdrachtregelinterface ie wordt gebruikt voor het uitvoeren van de Producer- en Consumer-code.

### <a name="pomxml"></a>Pom.xml

Belangrijke aandachtspunten voor het bestand `pom.xml`:

* Afhankelijkheden: dit project is afhankelijk van de Kafka-API's Producer en Consumer, die worden geleverd door het pakket `kafka-clients`. Deze afhankelijkheid wordt gedefinieerd met de volgende XML-code:

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

    * `maven-compiler-plugin`: wordt gebruikt om de Java-versie die wordt gebruikt door het project in te stellen op 8. Dit is de versie van Java die door HDInsight 3.6 wordt gebruikt.
    * `maven-shade-plugin`: wordt gebruikt voor het genereren van een uber jar die deze toepassing bevat, evenals eventuele afhankelijkheden. Dit bestand wordt ook gebruikt om het toegangspunt van de toepassing in te stellen, zodat u het Jar-bestand rechtstreeks kunt uitvoeren, dus zonder de hoofdklasse op te geven.

### <a name="producerjava"></a>Producer.java

De producer communiceert met de Kafka-brokerhosts (werkknooppunten) en verzendt gegevens naar een Kafka-onderwerp. Het volgende code fragment is afkomstig uit het bestand [producer. java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) uit de [github-opslag plaats](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) en laat zien hoe u de eigenschappen van de producer kunt instellen:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

De Consumer-API communiceert met de Kafka-brokerhosts (werkknooppunten) en leest records in een lus. Met het volgende codefragment van het bestand [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) worden de Consumer-eigenschappen ingesteld:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

In deze code is de Consumer-API geconfigureerd voor het lezen vanaf het begin van het onderwerp (`auto.offset.reset` is ingesteld op `earliest`.)

### <a name="runjava"></a>Run.java

Het [Run. java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) -bestand biedt een opdracht regel interface die de producent-of Consumer code uitvoert. U moet de gegevens van de Kafka-brokerhost opgeven als een parameter. U kunt eventueel een groeps-ID-waarde toevoegen die door het Consumer proces wordt gebruikt. Als u meerdere consumenten instanties maakt met behulp van dezelfde groeps-ID, wordt er taak verdeling in het onderwerp.

## <a name="build-and-deploy-the-example"></a>Het voorbeeld compileren en implementeren

Als u deze stap wilt overs Laan, kunnen vooraf ontwikkelde potten worden gedownload uit `Prebuilt-Jars` de submap. Down load het Kafka-producer-Consumer. jar. Als uw cluster **Enterprise Security Package (ESP)** is ingeschakeld, gebruikt u Kafka-producer-Consumer-ESP. jar. Voer stap 3 uit om het jar naar uw HDInsight-cluster te kopiëren.

1. Down load de voor beelden en [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)pak deze uit.

2. Stel de huidige map in op de locatie van `hdinsight-kafka-java-get-started\Producer-Consumer` de map. Als u **Enterprise Security Package (ESP)** ingeschakelde Kafka-cluster gebruikt, moet u de locatie instellen `DomainJoined-Producer-Consumer`op subdirectory. Gebruik de volgende opdracht om de toepassing te bouwen:

    ```cmd
    mvn clean package
    ```

    Met deze opdracht maakt u een directory met de naam `target`, die een bestand met de naam `kafka-producer-consumer-1.0-SNAPSHOT.jar` bevat.

3. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `CLUSTERNAME` door de naam van het cluster. Voer de volgende opdracht in om het `kafka-producer-consumer-1.0-SNAPSHOT.jar` bestand naar uw HDInsight-cluster te kopiëren. Voer het wachtwoord van de SSH-gebruiker in wanneer hierom wordt gevraagd.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a>Het voor beeld uitvoeren

1. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `CLUSTERNAME` door de naam van het cluster. Open een SSH-verbinding met het cluster door de volgende opdracht in te voeren. Voer het wachtwoord voor het SSH-gebruikersaccount in wanneer hierom wordt gevraagd.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Als u de Kafka Broker-hosts wilt ophalen, vervangt `<clustername>` u `<password>` de waarden voor en voert u de volgende opdracht uit. Gebruik dezelfde behuizing voor `<clustername>` zoals weer gegeven in de Azure Portal. Vervang `<password>` door het wacht woord voor cluster aanmelding en voer de volgende handelingen uit:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Voor deze opdracht is toegang tot Ambari vereist. Als uw cluster zich achter een NSG bevindt, voert u deze opdracht uit vanaf een computer die toegang heeft tot Ambari.

1. Maak een Kafka- `myTest`onderwerp door de volgende opdracht in te voeren:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Gebruik de volgende opdracht om de Producer-API uit te voeren en gegevens te schrijven naar het onderwerp:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Als dit proces is voltooid, gebruikt u de volgende opdracht om gegevens uit het onderwerp te lezen:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    De gelezen records worden weergegeven, samen met een telling van de records.

1. Gebruik __Ctrl + C__ om de consument af te sluiten.

### <a name="multiple-consumers"></a>Meerdere consumenten

Kafka-consumenten gebruiken een consumentengroep bij het lezen van records. Door dezelfde groep voor meerdere consumenten te gebruiken, worden leestaken voor onderwerpen gelijk verdeeld. Elke consument in de groep ontvangt een deel van de records.

De Consumer-toepassing accepteert een parameter die wordt gebruikt als de groeps-id. Met de volgende opdracht start u bijvoorbeeld een Consumer met behulp van de groeps-id `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Gebruik __Ctrl + C__ om de consument af te sluiten.

Om dit proces in actie te zien, gebruikt u de volgende opdracht:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Deze opdracht gebruikt `tmux` om de terminal op te splitsen in twee kolommen. In elke kolom wordt een Consumer gestart, met dezelfde waarde voor de groeps-id. Als de Consumers klaar zijn met lezen, ziet u dat ieder Consumer slechts een deel van de records heeft gelezen. Gebruik __CTRL + C__ twee keer om `tmux`af te sluiten.

Gebruik door clients binnen dezelfde groep wordt verwerkt door de partities voor het onderwerp. Het eerder gemaakte onderwerp `test` uit dit codevoorbeeld heeft acht partities. Als u acht Consumers start, leest elke Consumer records uit één partitie van het onderwerp.

> [!IMPORTANT]  
> Een consumentengroep kan niet meer consumentexemplaren dan partities bevatten. In dit voorbeeld kan één consumentengroep maximaal acht consumenten bevatten, omdat het onderwerp dit aantal partities heeft. U kunt ook meerdere consumentengroepen hebben, waarvan elke groep niet meer dan acht consumenten bevat.

Records die zijn opgeslagen in Kafka, worden opgeslagen in de volg orde waarin ze worden ontvangen in een partitie. Als u records *binnen een partitie* op volgorde wilt leveren, maakt u een consumentengroep waarvan het aantal consumentexemplaren gelijk is aan het aantal partities. Als u records *binnen het onderwerp* op volgorde wilt leveren, maakt u een consumentengroep met slechts één consumentexemplaar.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, kunt u de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende HDInsight-cluster en eventuele andere resources die aan de resourcegroep zijn gekoppeld, verwijderd.

Ga als volgt te werk om de resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies __Resourcegroepen__ om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen en klik met de rechtermuisknop op de knop __Meer__ (... ) aan de rechterkant van de vermelding.
3. Selecteer __Resourcegroep verwijderen__ en bevestig dit.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Producer- en Consumer-API's van Apache Kafka gebruikt met Kafka in HDInsight. Gebruik de volgende documenten voor meer informatie over het werken met Kafka:

* [Kafka REST-proxy gebruiken](rest-proxy.md)
* [Apache Kafka-logboeken analyseren](apache-kafka-log-analytics-operations-management.md)
