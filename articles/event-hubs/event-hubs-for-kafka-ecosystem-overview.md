---
title: Gebeurtenishub gebruiken vanuit de Apache Kafka-app - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u informatie over apache Kafka-ondersteuning door Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 91e2d70bab8c1be4b3e5b400ce21122eccb1e9eb
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811373"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Azure-gebeurtenishubs gebruiken vanuit Apache Kafka-toepassingen
Event Hubs biedt een Kafka-eindpunt dat door uw bestaande Kafka-gebaseerde toepassingen kan worden gebruikt als alternatief voor het uitvoeren van uw eigen Kafka-cluster. Event Hubs ondersteunt [Apache Kafka protocol 1.0 en hoger](https://kafka.apache.org/documentation/)en werkt met uw bestaande Kafka-toepassingen, waaronder MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Wat biedt Event Hubs voor Kafka?

De functie Gebeurtenishubs voor Kafka biedt een protocolkop bovenop Azure Event Hubs dat binair compatibel is met Kafka-versies 1.0 en hoger voor zowel het lezen van als het schrijven naar Kafka-onderwerpen. U het Kafka-eindpunt van uw toepassingen gaan gebruiken zonder codewijziging, maar een minimale configuratiewijziging. U werkt de verbindingstekenreeks in configuraties bij om naar het Kafka-eindpunt te verwijzen dat wordt blootgesteld door uw gebeurtenishub in plaats van naar uw Kafka-cluster te wijzen. Vervolgens u beginnen met het streamen van gebeurtenissen vanuit uw toepassingen die het Kafka-protocol gebruiken in Gebeurtenishubs. Deze integratie ondersteunt ook frameworks zoals [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), die momenteel in preview. 

Conceptueel zijn Kafka en Event Hubs bijna identiek: het zijn beide partitielogboeken die zijn gebouwd voor streaminggegevens. In de volgende tabel worden concepten tussen Kafka en Gebeurtenishubs in kaart gebracht.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Conceptmapping van Kafka en Event Hub

| Kafka Concept | Event Hubs Concept|
| --- | --- |
| Cluster | Naamruimte |
| Onderwerp | Event Hub |
| Partitie | Partitie|
| Consumentengroep | Consumentengroep |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Belangrijkste verschillen tussen Kafka en Event Hubs

Hoewel [Apache Kafka](https://kafka.apache.org/) software is, die u uitvoeren waar u maar wilt, is Event Hubs een cloudservice die vergelijkbaar is met Azure Blob Storage. Er zijn geen servers of netwerken te beheren en geen makelaars te configureren. U maakt een naamruimte, een FQDN waarin uw onderwerpen leven, en maakt vervolgens Gebeurtenishubs of onderwerpen binnen die naamruimte. Zie [Functies van gebeurtenishubs](event-hubs-features.md#namespace)voor meer informatie over gebeurtenishubs en naamruimten. Als cloudservice gebruikt Event Hubs één stabiel virtueel IP-adres als eindpunt, zodat clients niets hoeven te weten over de brokers of machines binnen een cluster. 

Schaal in gebeurtenishubs wordt aangestuurd door het aantal doorvoereenheden dat u koopt, waarbij elke doorvoereenheid u recht krijgt op 1 MB per seconde of 1000 gebeurtenissen per seconde van binnendringen. Gebeurtenishubs schalen standaard doorvoereenheden op wanneer u uw limiet bereikt met de functie [Automatisch opblazen.](event-hubs-auto-inflate.md) deze functie werkt ook met de functie Gebeurtenishubs voor Kafka. 

### <a name="security-and-authentication"></a>Verificatie en beveiliging
Elke keer dat u gebeurtenissen publiceert of gebruikt vanuit een gebeurtenishubs voor Kafka, probeert uw client toegang te krijgen tot de bronnen van Event Hubs. U wilt ervoor zorgen dat de resources worden geopend met behulp van een geautoriseerde entiteit. Wanneer u het Apache Kafka-protocol met uw clients gebruikt, u uw configuratie instellen voor verificatie en versleuteling met behulp van de SASL-mechanismen. Bij het gebruik van Event Hubs voor Kafka is de TLS-encryptie vereist (omdat alle gegevens die worden verzonden met Event Hubs TLS-versleuteld zijn). Dit kan worden gedaan door de SASL_SSL optie in uw configuratiebestand op te geven. 

Azure Event Hubs biedt meerdere opties om toegang tot uw beveiligde bronnen te autoriseren. 

- Oauth
- Shared Access Signature (SAS)

#### <a name="oauth"></a>Oauth
Event Hubs integreert met Azure Active Directory (Azure AD), dat een **OAuth** 2.0-compatibele gecentraliseerde autorisatieserver biedt. Met Azure AD u RBAC (Role-based access control) gebruiken om fijnmazige machtigingen toe te kennen aan uw clientidentiteiten. U deze functie gebruiken met uw Kafka-clients door **SASL_SSL** op te geven voor het protocol en **OAUTHBEARER** voor het mechanisme. Zie [Toegang met Azure AD autoriseren voor](authorize-access-azure-active-directory.md)meer informatie over RBAC-rollen en -niveaus voor scopingtoegang.

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Event Hubs biedt ook de **Shared Access Signatures (SAS)** voor gedelegeerde toegang tot Event Hubs voor Kafka-bronnen. Het toestaan van toegang met oauth 2.0-tokenmechanisme biedt superieure beveiliging en gebruiksgemak via SAS. De ingebouwde rollen kunnen ook de noodzaak van ACL-gebaseerde autorisatie elimineren, die moet worden onderhouden en beheerd door de gebruiker. U deze functie gebruiken met uw Kafka-clients door **SASL_SSL** op te geven voor het protocol en **PLAIN** voor het mechanisme. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Voorbeelden 
Zie [Quickstart: Gegevensstreamen met gebeurtenishubs met behulp van het Kafka-protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md)voor een **zelfstudie** met stapsgewijze instructies om een gebeurtenishub te maken en deze te openen met SAS of OAuth.

Zie [voorbeelden op GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)voor meer **voorbeelden** die laten zien hoe u OAuth gebruiken met gebeurtenishubs voor Kafka.

## <a name="other-event-hubs-features-available-for-kafka"></a>Andere Functies van Event Hubs beschikbaar voor Kafka

Met de functie Gebeurtenishubs voor Kafka u met het ene protocol schrijven en met het andere lezen, zodat uw huidige Kafka-producenten kunnen blijven publiceren via Kafka en u lezers toevoegen met Event Hubs, zoals Azure Stream Analytics of Azure Functions. Daarnaast werken event hubs-functies zoals [Capture](event-hubs-capture-overview.md) en [Geo Disaster-Recovery](event-hubs-geo-dr.md) ook met de functie Event Hubs for Kafka.

## <a name="features-that-are-not-yet-supported"></a>Functies die nog niet worden ondersteund 

Hier is de lijst met Kafka-functies die nog niet worden ondersteund:

*   Idempotente producent
*   Transactie
*   Compressie
*   Behoud op basis van grootte
*   Logboekverdichting
*   Partities toevoegen aan een bestaand onderwerp
*   HTTP Kafka API-ondersteuning
*   Kafka Streams

## <a name="next-steps"></a>Volgende stappen
Dit artikel gaf een inleiding tot Event Hubs voor Kafka. Zie [de ontwikkelaarshandleiding van Apache Kafka voor Azure Event Hubs voor](apache-kafka-developer-guide.md)meer informatie.


