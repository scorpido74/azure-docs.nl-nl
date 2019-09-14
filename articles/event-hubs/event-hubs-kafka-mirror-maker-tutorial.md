---
title: Gebruik Apache Kafka MirrorMaker - Azure Eventhubs | Microsoft Docs
description: In dit artikel bevat informatie over het gebruik van Kafka MirrorMaker voor het spiegelen van een Kafka-cluster in AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 43a32177280361bb0c2a433af0cb5dd3cfc6b9d3
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967590"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Kafka MirrorMaker gebruiken met Eventhubs voor Apache Kafka

In deze zelfstudie laat zien hoe een Kafka-broker in een Kafka-ingeschakelde event hub met behulp van Kafka MirrorMaker spiegelen.

   ![Kafka MirrorMaker met Eventhubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Een Kafka-cluster instellen
> * Kafka MirrorMaker configureren
> * Kafka MirrorMaker uitvoeren

## <a name="introduction"></a>Inleiding
Een van de belangrijkste overweging voor moderne cloud schaal apps is de mogelijkheid om te werken, te verbeteren en infrastructuur wijzigen zonder dat service wordt onderbroken. Deze zelfstudie laat zien hoe een Kafka-functionaliteit event hub en Kafka MirrorMaker een bestaande Kafka-pijplijn kunt integreren in Azure door "spiegelen" van de invoerstroom van Kafka in de Event Hubs-service. 

Een Azure Event Hubs Kafka-eindpunt kunt u verbinding maken met Azure Event Hubs met behulp van het Kafka-protocol (dat wil zeggen, Kafka-clients). Met minimale wijzigingen aanbrengt in een Kafka-toepassing, kunt u verbinding maken met Azure Event Hubs en profiteren van de voordelen van het Azure-ecosysteem. Event Hubs waarvoor Kafka is ingeschakeld op dit moment ondersteunt Kafka versies 1.0 en hoger.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* [Download](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html) een binaire Maven-archief
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [Een Event Hub maken waarvoor Kafka is ingeschakeld](event-hubs-create.md) voor instructies voor het ophalen van een Event Hubs Kafka-eindpunt. Zorg ervoor dat u het kopiëren van de Event Hubs-verbindingsreeks voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu dat u hebt een Kafka Event Hubs-verbindingsreeks ingeschakeld, wordt de Azure Event Hubs voor Kafka-opslagplaats te klonen en navigeer naar de `mirror-maker` submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Een Kafka-cluster instellen

Gebruik de [Kafka-snelstartgids](https://kafka.apache.org/quickstart) naar een cluster met de gewenste instellingen instellen (of gebruik een bestaande Kafka-cluster).

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker configureren

Kafka MirrorMaker kunt u de 'mirroring' van een stroom. Opgegeven bron- en Kafka-clusters, MirrorMaker zorgt ervoor dat alle berichten verzonden naar het broncluster worden ontvangen door de bron- en doel-clusters. In dit voorbeeld laat zien hoe om een bron Kafka-cluster met een doel waarvoor Kafka is ingeschakeld event hub te spiegelen. In dit scenario kan worden gebruikt voor het verzenden van gegevens uit een bestaande pijplijn van Kafka naar Event Hubs zonder dat de stroom van gegevens wordt onderbroken. 

Zie voor meer informatie over Kafka MirrorMaker, de [Kafka spiegelen/MirrorMaker handleiding](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Voor het configureren van Kafka MirrorMaker, geeft u deze een Kafka-cluster als de consument/bron- en een Kafka-functionaliteit event hub als de producent/bestemming.

#### <a name="consumer-configuration"></a>Configuratie van consumenten

Het configuratiebestand van de consument bijwerken `source-kafka.config`, waarin de eigenschappen van de bron van Kafka-cluster MirrorMaker staat.

##### <a name="source-kafkaconfig"></a>bron-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producent-configuratie

Nu de producent-configuratiebestand bijwerken `mirror-eventhub.config`, waarin staat dat MirrorMaker de gedupliceerde (of 'gespiegelde') om gegevens te verzenden naar de Event Hubs-service. Met name wijzigen `bootstrap.servers` en `sasl.jaas.config` om te verwijzen naar uw Event Hubs Kafka-eindpunt. De Event Hubs-service vereist een beveiligde communicatie voor (SASL), die wordt bereikt door het instellen van de laatste drie eigenschappen in de volgende configuratie: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker uitvoeren

Voer het script Kafka MirrorMaker vanuit de hoofdmap van Kafka-directory met behulp van de nieuwe, bijgewerkte configuratiebestanden. Zorg ervoor dat de configuratiebestanden kopiëren naar de hoofdmap Kafka-directory, of de paden in de volgende opdracht kunt bijwerken.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Om te controleren dat gebeurtenissen de Kafka-functionaliteit event hub worden bereikt, Zie de statistieken voor de inkomende gegevens in de [Azure-portal](https://azure.microsoft.com/features/azure-portal/), of een gebruiker worden uitgevoerd op basis van de event hub.

Alle gebeurtenissen verzonden naar de bron van Kafka-cluster worden ontvangen door zowel de Kafka-cluster met MirrorMaker uitgevoerd, en de gespiegelde Kafka event hub-service ingeschakeld. Met behulp van MirrorMaker en een Event Hubs Kafka-eindpunt, kunt u een bestaande Kafka-pijplijn met de beheerde service van Azure Event Hubs migreren zonder het bestaande cluster wijzigen of onderbreken van een doorlopende gegevensstroom.

## <a name="samples"></a>Voorbeelden
Bekijk de volgende voor beelden op GitHub:

- [Voorbeeld code voor deze zelf studie op GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker uitgevoerd op een Azure-container exemplaar](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Een Kafka-cluster instellen
> * Kafka MirrorMaker configureren
> * Kafka MirrorMaker uitvoeren

Zie het volgende onderwerp voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Event Hubs maken waarvoor Kafka is ingeschakeld](event-hubs-create-kafka-enabled.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Apache Spark verbinden met een Event Hub waarvoor Kafka is ingeschakeld)
- [Apache Flink verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-flink-tutorial.md)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Kafka Connect integreren met een Event Hub waarvoor Kafka is ingeschakeld)
- [Akka Streams verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
