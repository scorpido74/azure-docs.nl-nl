---
title: Apache Kafka MirrorMaker gebruiken - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u informatie over het gebruik van Kafka MirrorMaker om een Kafka-cluster in AzureEvent Hubs te spiegelen.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: fb041ec0d3cd474cca12d5ad55b733337566b9cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632785"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Gebruik Kafka MirrorMaker met Event Hubs voor Apache Kafka

In deze zelfstudie laat u zien hoe u een Kafka-makelaar in een gebeurtenishub spiegelen met Kafka MirrorMaker.

   ![Kafka MirrorMaker met Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Een Kafka-cluster instellen
> * Kafka MirrorMaker configureren
> * Voer Kafka MirrorMaker uit

## <a name="introduction"></a>Inleiding
Een belangrijke overweging voor moderne cloudschaal-apps is de mogelijkheid om infrastructuur bij te werken, te verbeteren en te wijzigen zonder de service te onderbreken. In deze zelfstudie ziet u hoe een gebeurtenishub en Kafka MirrorMaker een bestaande Kafka-pijplijn in Azure kunnen integreren door de Kafka-invoerstream in de gebeurtenishubs-service te 'spiegelen'. 

Met een Azure Event Hubs Kafka-eindpunt u verbinding maken met Azure Event Hubs met behulp van het Kafka-protocol (dat wil zeggen Kafka-clients). Door minimale wijzigingen aan te brengen in een Kafka-toepassing, u verbinding maken met Azure Event Hubs en genieten van de voordelen van het Azure-ecosysteem. Event Hubs ondersteunt momenteel Kafka-versies 1.0 en hoger.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Er is een Event Hubs-naamruimte vereist om gegevens te verzenden naar en te ontvangen van Event Hubs-services. Zie [Een gebeurtenishub maken](event-hubs-create.md) voor instructies voor het maken van een naamruimte en een gebeurtenishub. Zorg ervoor dat u de verbindingstekenreeks Voor gebeurtenishubs kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u een verbindingstekenreeks voor gebeurtenishubs hebt, kloont u de `mirror-maker` Azure Event Hubs voor kafka-opslagplaats en navigeert u naar de submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Een Kafka-cluster instellen

Gebruik de [quickstartgids Kafka](https://kafka.apache.org/quickstart) om een cluster in te stellen met de gewenste instellingen (of gebruik een bestaand Kafka-cluster).

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker configureren

Kafka MirrorMaker maakt het "spiegelen" van een stroom mogelijk. Gezien de bron en bestemming Kafka clusters, MirrorMaker zorgt ervoor dat alle berichten die naar het broncluster worden verzonden, worden ontvangen door zowel de bron- als de doelclusters. In dit voorbeeld ziet u hoe u een bron-Kafka-cluster spiegelen met een gebeurtenishub voor bestemming. Dit scenario kan worden gebruikt om gegevens van een bestaande Kafka-pijplijn naar gebeurtenishubs te verzenden zonder de gegevensstroom te onderbreken. 

Voor meer gedetailleerde informatie over Kafka MirrorMaker, zie de [Kafka Mirroring / MirrorMaker gids](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Als u Kafka MirrorMaker wilt configureren, geeft u het een Kafka-cluster als consument/bron en een gebeurtenishub als producent/bestemming.

#### <a name="consumer-configuration"></a>Consumentenconfiguratie

Werk het configuratiebestand van de consument bij `source-kafka.config`, dat MirrorMaker de eigenschappen van het bron-Kafka-cluster vertelt.

##### <a name="source-kafkaconfig"></a>bron-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producentenconfiguratie

Werk nu het `mirror-eventhub.config`configuratiebestand van de producent bij, waarmee MirrorMaker de dubbele (of "gespiegelde" gegevens naar de Service Event Hubs moet verzenden. Wijzig specifiek `bootstrap.servers` `sasl.jaas.config` het eindpunt van uw gebeurtenishubs Kafka en wijst u aan. De Event Hubs-service vereist veilige (SASL)-communicatie, wat wordt bereikt door de laatste drie eigenschappen in de volgende configuratie in te stellen: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Voer Kafka MirrorMaker uit

Voer het Kafka MirrorMaker-script uit de map root-Kafka uit met behulp van de onlangs bijgewerkte configuratiebestanden. Zorg ervoor dat u de config-bestanden naar de map met de hoofdkafka-map kopieert of hun paden bijwerkt in de volgende opdracht.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Als u wilt controleren of gebeurtenissen de gebeurtenishub bereiken, raadpleegt u de binnenkomende statistieken in de [Azure-portal](https://azure.microsoft.com/features/azure-portal/)of voert u een consument uit tegen de gebeurtenishub.

Als MirrorMaker actief is, worden alle gebeurtenissen die naar het cluster van de bron Kafka worden verzonden, ontvangen door zowel het Kafka-cluster als de gespiegelde gebeurtenishub. Door MirrorMaker en een Kafka-eindpunt voor gebeurtenishubs te gebruiken, u een bestaande Kafka-pijplijn migreren naar de beheerde Azure Event Hubs-service zonder het bestaande cluster te wijzigen of een doorlopende gegevensstroom te onderbreken.

## <a name="samples"></a>Voorbeelden
Bekijk de volgende voorbeelden op GitHub:

- [Voorbeeldcode voor deze zelfstudie op GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker uitgevoerd op een Azure Container Instance](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over gebeurtenishubs voor Kafka:  

- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md)
- [Integreer Kafka Connect met een gebeurtenishub](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
- [Akka Streams verbinden met een gebeurtenishub](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka-ontwikkelaarshandleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)