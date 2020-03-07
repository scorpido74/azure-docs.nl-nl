---
title: Gebruik event hub uit de app in Apache Kafka - Azure Event Hubs | Microsoft Docs
description: In dit artikel bevat informatie over de ondersteuning van Apache Kafka met Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: fc81226e754178ad0edfff96a494dd7522662261
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395450"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Azure Event Hubs gebruiken vanuit toepassingen van Apache Kafka
Eventhubs biedt een Kafka-eindpunt dat kan worden gebruikt door uw bestaande Kafka op basis van toepassingen als alternatief voor het uitvoeren van uw eigen Kafka-cluster. Event Hubs ondersteunt [Apache Kafka protocol 1,0 en hoger](https://kafka.apache.org/documentation/)en werkt met uw bestaande Kafka-toepassingen, waaronder MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Wat biedt Event Hubs voor Kafka

De Event Hubs voor Kafka-functie biedt een protocol hoofd boven op Azure Event Hubs die binaire compatibel is met Kafka-versies 1.0 en hoger voor zowel lezen van en schrijven naar het Kafka-onderwerpen. U kunt beginnen met behulp van het Kafka-eindpunt van uw toepassingen met een minimale configuratie wijzigen, maar er zijn geen codewijzigingen. U kunt de verbindingsreeks in configuraties om te verwijzen naar het Kafka-eindpunt beschikbaar gesteld door uw event hub in plaats van dat verwijst naar het Kafka-cluster bijwerken. Vervolgens kun u streaming-gebeurtenissen uit uw toepassingen die gebruikmaken van het Kafka-protocol in Event Hubs. Deze integratie biedt ook ondersteuning voor frameworks zoals [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), dat momenteel als preview-versie beschikbaar is. 

Conceptueel Kafka en Event Hubs bijna identiek zijn: ze zijn beide gepartitioneerde logboeken die zijn gemaakt voor het streamen van gegevens. De volgende tabel worden de concepten tussen Kafka- en Event Hubs toegewezen.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka en Event Hub conceptuele toewijzing

| Kafka-Concept | Event Hubs Concept|
| --- | --- |
| Cluster | Naamruimte |
| Onderwerp | Event Hub |
| Partitie | Partitie|
| Consumentengroep | Consumentengroep |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Belangrijke verschillen tussen Kafka- en Event Hubs

Hoewel [Apache Kafka](https://kafka.apache.org/) software is, die u overal kunt uitvoeren, is Event hubs een Cloud service die vergelijkbaar is met Azure Blob Storage. Er zijn geen servers of netwerken te beheren en geen brokers te configureren. U maakt een naamruimte een FQDN-naam in die uw onderwerpen is, en maak vervolgens Event Hubs en onderwerpen binnen die naamruimte. Zie [Event hubs-functies](event-hubs-features.md#namespace)voor meer informatie over Event hubs en naam ruimten. Als Cloud service gebruikt Event Hubs één stabiel virtueel IP-adres als het eind punt, zodat clients niet hoeven te weten over de Brokers of computers binnen een cluster. 

Schaal in Event Hubs wordt bepaald door u hoeveel doorvoereenheden u hebt gekocht, met elke throughput unit grond u 1 MB per seconde of 1000 gebeurtenissen per seconde van inkomend verkeer. Event Hubs worden standaard doorvoer eenheden geschaald wanneer u de limiet bereikt met de functie voor [automatisch verg Roten](event-hubs-auto-inflate.md) . deze functie werkt ook met de Event Hubs voor de functie Kafka. 

### <a name="security-and-authentication"></a>Verificatie en beveiliging
Elke keer dat u gebeurtenissen publiceert of gebruikt vanuit een Event Hubs voor Kafka, probeert de client toegang te krijgen tot de Event Hubs resources. U wilt er zeker van zijn dat de resources worden geopend met behulp van een geautoriseerde entiteit. Wanneer u Apache Kafka-protocol met uw clients gebruikt, kunt u uw configuratie voor verificatie en versleuteling instellen met behulp van de SASL-mechanismen. Bij het gebruik van Event Hubs voor Kafka is TLS-code ring vereist (omdat alle gegevens in de overdracht met Event Hubs TLS-versleuteld zijn). U kunt dit doen door de SASL_SSL optie in uw configuratie bestand op te geven. 

Azure Event Hubs biedt meerdere opties voor het machtigen van toegang tot uw beveiligde bronnen. 

- OAuth
- Shared Access Signature (SAS)

#### <a name="oauth"></a>OAuth
Event Hubs integreert met Azure Active Directory (Azure AD), dat een door **OAuth** 2,0 compatibele gecentraliseerde autorisatie server biedt. Met Azure AD kunt u gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC) om verfijnde machtigingen te verlenen aan uw client identiteiten. U kunt deze functie met uw Kafka-clients gebruiken door **SASL_SSL** op te geven voor het protocol en **OAUTHBEARER** voor het mechanisme. Zie [toegang verlenen met Azure AD](authorize-access-azure-active-directory.md)voor meer informatie over RBAC-rollen en-niveaus voor het bereik van de toegang.

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Event Hubs biedt ook de **Shared Access signatures (SAS)** voor gedelegeerde toegang tot Event hubs voor Kafka-resources. Het machtigen van toegang met behulp van een op tokens gebaseerd OAuth 2,0-mechanisme biedt een superieure beveiliging en gebruiks vriendelijk gebruik van SAS. De ingebouwde rollen kunnen ook de nood zaak voor autorisatie op basis van een toegangs beheer lijst elimineren, die door de gebruiker moet worden onderhouden en beheerd. U kunt deze functie met uw Kafka-clients gebruiken door **SASL_SSL** op te geven voor het **protocol en voor** het mechanisme. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Voorbeelden 
Voor een **zelf studie** met stapsgewijze instructies voor het maken van een Kafka-ingeschakelde Event hub en toegang krijgen met behulp van SAS of OAuth, raadpleegt u [Quick Start: streamen met Event hubs met het Kafka-protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Voor meer voor **beelden** die laten zien hoe u OAuth gebruikt met Event hubs voor Kafka, raadpleegt u voor [beelden op github](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andere functies van Event Hubs voor Kafka

De Event Hubs voor Kafka-functie kunt u met één protocol schrijven en lezen met een andere, zodat uw huidige Kafka producenten publiceren via Kafka kunt, en u lezers met Event Hubs, zoals Azure Stream Analytics of Azure Functions kunt toevoegen. Daarnaast werkt Event Hubs functies, zoals [vastleggen](event-hubs-capture-overview.md) en [geo-herstel na nood gevallen](event-hubs-geo-dr.md) , ook met de Event hubs voor de functie Kafka.

## <a name="features-that-are-not-yet-supported"></a>Functies die nog niet ondersteund 

Hier volgt de lijst van Kafka-functies die nog niet ondersteund:

*   Idempotent producent
*   Transactie
*   Compressie
*   Op basis van grootte
*   Meld u compressie
*   Partities toe te voegen aan een bestaand onderwerp
*   Kafka met HTTP-API-ondersteuning
*   Kafka Streams

## <a name="next-steps"></a>Volgende stappen

In dit artikel opgegeven een inleiding tot Event Hubs voor Kafka. Zie de volgende koppelingen voor meer informatie:

- [Event Hubs maken waarvoor Kafka is ingeschakeld](event-hubs-create-kafka-enabled.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een event hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Apache Spark verbinden met een Event Hub waarvoor Kafka is ingeschakeld)
- [Apache Flink verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-flink-tutorial.md)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Kafka Connect integreren met een Event Hub waarvoor Kafka is ingeschakeld)
- [Akka Streams verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)


