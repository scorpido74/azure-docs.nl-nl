---
title: Apache Kafka MirrorMaker gebruiken-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het gebruik van Kafka MirrorMaker om een Kafka-cluster in evenementen hubs te spie gelen.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6d1596cf0a50ed5dcb896896282178b6fc12c1a1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555104"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Gebruik Kafka MirrorMaker met Event Hubs voor Apache Kafka

Deze zelf studie laat zien hoe u een Kafka-Broker spiegelt in een Kafka die is ingeschakeld Event Hub met Kafka MirrorMaker.

   ![Kafka MirrorMaker met Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

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
Een belang rijke overweging voor moderne Cloud Scale-apps is de mogelijkheid om de infra structuur bij te werken, te verbeteren en te wijzigen zonder dat de service wordt onderbroken. In deze zelf studie ziet u hoe een Kafka-Event Hub en Kafka MirrorMaker een bestaande Kafka-pijp lijn kan integreren in azure door de Kafka-invoer stroom te spie gelen in de Event Hubs-service. 

Met een Azure Event Hubs Kafka-eind punt kunt u verbinding maken met Azure Event Hubs met behulp van het Kafka-Protocol (dat wil zeggen Kafka-clients). Door minimale wijzigingen aan te brengen in een Kafka-toepassing, kunt u verbinding maken met Azure Event Hubs en profiteren van de voor delen van het Azure-ecosysteem. Kafka ingeschakeld Event Hubs ondersteunt momenteel Kafka-versies 1,0 en hoger.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* Een binair archief van Maven [downloaden](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html)
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [Een Event Hub maken waarvoor Kafka is ingeschakeld](event-hubs-create.md) voor instructies voor het ophalen van een Event Hubs Kafka-eindpunt. Zorg ervoor dat u de Event Hubs-connection string kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u een Kafka Event Hubs connection string hebt ingeschakeld, kloont u de Azure Event Hubs voor Kafka-opslag plaats en navigeert u naar de submap `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Een Kafka-cluster instellen

Gebruik de [Kafka Snelstartgids](https://kafka.apache.org/quickstart) om een cluster in te stellen met de gewenste instellingen (of gebruik een bestaand Kafka-cluster).

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker configureren

Kafka MirrorMaker maakt het spie gelen van een stroom mogelijk. Op basis van de bron-en doel-Kafka-clusters zorgt MirrorMaker ervoor dat berichten die naar het bron cluster worden verzonden, zowel door de bron-als de doel clusters worden ontvangen. In dit voor beeld ziet u hoe u een bron Kafka-cluster spiegelt met een Event Hub voor doel-Kafka. Dit scenario kan worden gebruikt om gegevens te verzenden van een bestaande Kafka-pijp lijn naar Event Hubs zonder de stroom van gegevens te onderbreken. 

Zie de [hand leiding Kafka mirroring/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)voor meer gedetailleerde informatie over Kafka MirrorMaker.

Als u Kafka MirrorMaker wilt configureren, geeft u het een Kafka-cluster als consument/bron en een Kafka Event Hub als producent/bestemming.

#### <a name="consumer-configuration"></a>Consumenten configuratie

Werk het configuratie bestand van de consument bij `source-kafka.config`, waarmee MirrorMaker de eigenschappen van het Kafka cluster worden aangegeven.

##### <a name="source-kafkaconfig"></a>Bron-Kafka. config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producer-configuratie

Werk nu het configuratie bestand van de producent bij `mirror-eventhub.config`, waarmee wordt aangegeven MirrorMaker de gedupliceerde (of gespiegelde) gegevens naar de Event Hubs-service te verzenden. Wijzig `bootstrap.servers` en `sasl.jaas.config` om naar het eind punt van Event Hubs Kafka te verwijzen. De Event Hubs-service vereist beveiligde (SASL) communicatie. dit wordt bereikt door de laatste drie eigenschappen in de volgende configuratie in te stellen: 

##### <a name="mirror-eventhubconfig"></a>spiegel-eventhub. config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker uitvoeren

Voer het script Kafka MirrorMaker uit vanuit de hoofdmap van Kafka met de zojuist bijgewerkte configuratie bestanden. Kopieer de configuratie bestanden naar de hoofdmap Kafka of werk hun paden bij in de volgende opdracht.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Raadpleeg de ingangs statistieken in de [Azure Portal](https://azure.microsoft.com/features/azure-portal/)of voer een consument uit op basis van de Event hub om te controleren of de Kafka-Event hub worden bereikt.

Wanneer MirrorMaker wordt uitgevoerd, worden alle gebeurtenissen die worden verzonden naar de bron-Kafka-cluster ontvangen door zowel het Kafka-cluster als de gespiegelde Kafka Event Hub-service ingeschakeld. Door MirrorMaker en een Event Hubs Kafka-eind punt te gebruiken, kunt u een bestaande Kafka-pijp lijn migreren naar de beheerde Azure Event Hubs-service zonder dat u het bestaande cluster wijzigt of doorlopende gegevens stroom onderbreekt.

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
