---
title: Integreren met Apache Kafka Connect - Azure Event Hubs | Microsoft Docs
description: In dit artikel vindt u informatie over het gebruik van Apache Spark met Azure Event Hubs voor Kafka.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 84220d5dda26c25f40138629e2be1f10d57fe3c4
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555124"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Apache Kafka Connect-ondersteuning integreren in Azure Event Hubs (preview)
Naarmate de gegevensopname voor bedrijven toeneemt, neemt tevens de vereiste toe om diverse externe bronnen en sinks op te nemen. [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) biedt een framework om gegevens te verbinden met en te exporteren/importeren uit/in externe systemen als MySQL, HDFS, en een bestandssysteem via een Kafka-cluster. Deze zelfstudie begeleidt u door het gebruik van het Kafka Connect-kader met Event Hubs waarvoor Kafka is ingeschakeld.

Deze zelfstudie begeleidt u bij het integreren van Kafka Connect met een Event Hub waarvoor Kafka is ingeschakeld en bij het implementeren van eenvoudige FileStreamSource- en FileStreamSink-connectors. Deze functie is momenteel beschikbaar als preview-product. Hoewel deze connectors niet bedoeld zijn voor gebruik in productieomgevingen, laten ze een end-to-end Kafka Connect-scenario zien waarin Azure Event Hubs als een Kafka-broker fungeert.

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Kafka Connect configureren voor Event Hubs
> * Kafka Connect uitvoeren
> * Connectors maken

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u aan de volgende vereisten voldoet om deze stappen uit te voeren:

- Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/Mac OS
- Kafka-release (versie 1.1.1, Scala-versie 2.11), beschikbaar op [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Lees het inleidende artikel [Event Hubs voor Apache Kafka](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview) door

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [Een Event Hub maken waarvoor Kafka is ingeschakeld](event-hubs-create.md) voor instructies voor het ophalen van een Event Hubs Kafka-eindpunt. Haal de Event Hubs-verbindingsreeks en de Fully Qualified Domain Name (FQDN) op voor later gebruik. Zie [Get an Event Hubs connection string](event-hubs-get-connection-string.md). (Een Event Hubs-verbindingsreeks ophalen). 

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen
Kloon de Azure Event Hubs-opslagplaats en ga naar de submap tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Kafka Connect configureren voor Event Hubs
Er is een minimale herconfiguratie vereist als u doorvoer van Kafka Connect wilt omleiden van Kafka naar Event Hubs.  In het volgende `connect-distributed.properties`-voorbeeld wordt getoond hoe u Connect kunt configureren om op Event Hubs het Kafka-eindpunt te verifiëren en hoe ermee te communiceren:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Kafka Connect uitvoeren

In deze stap wordt een Kafka Connect-werkrol lokaal wordt gestart in gedistribueerde modus, waarbij Event Hubs wordt gebruikt om de clusterstatus te handhaven.

1. Sla het bovenstaande `connect-distributed.properties`-bestand lokaal op.  Vervang alle waarden tussen accolades.
2. Ga naar de locatie van de Kafka-release op uw computer.
4. Voer `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` uit.  De REST API van de Connect-werkrol is klaar voor interactie als u `'INFO Finished starting connectors and tasks'` ziet. 

> [!NOTE]
> Event Hubs biedt ondersteuning voor Kafka-clients die automatisch onderwerpen maken. Een snelle controle van de naamruimte in de Azure-portal laat zien dat de interne onderwerpen van de Connect-werkrol automatisch zijn gemaakt.

### <a name="create-connectors"></a>Connectors maken
In deze sectie wordt u begeleidt bij het starten van de FileStreamSource- en FileStreamSink-connectors. 

1. Maak een map voor gegevensbestanden voor in- en uitvoer.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Maak twee bestanden: een met seedgegevens die door de FileStreamSource-connector worden gelezen en een waarnaar de FileStreamSink-connector schrijft.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Maak een FileStreamSource-connector.  Vervang de accolades door het pad naar uw basismap.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    na het uitvoeren van de bovenstaande opdracht, ziet u de Event Hub `connect-quickstart` in uw Event Hubs-instantie.
4. Controleer de status van de bronconnector.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    U kunt eventueel [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) gebruiken om te verifiëren of er gebeurtenissen in het `connect-quickstart`-onderwerp zijn aangekomen.

5. Maak een FileStreamSink-connector.  Vervang ook hier de accolades door het pad naar uw basismap.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Controleer de status van de sinkconnector.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Verifieer of de gegevens van de bestanden zijn gerepliceerd en of de gegevens in beide bestanden identiek zijn.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Opschonen
Kafka Connect maakt Event Hub-onderwerpen om configuraties, verschuivingen en status op te slaan die ook behouden blijven nadat het Connect-cluster is uitgeschakeld. Tenzij persistentie gewenst is, wordt het aanbevolen deze onderwerpen te verwijderen. U kunt ook Event Hub `connect-quickstart` verwijderen, die tijdens deze stappen is gemaakt.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende onderwerp voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Event Hubs maken waarvoor Kafka is ingeschakeld](event-hubs-create-kafka-enabled.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een event hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Apache Spark verbinden met een Event Hub waarvoor Kafka is ingeschakeld)
- [Apache Flink verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-flink-tutorial.md)
- [Akka Streams verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
