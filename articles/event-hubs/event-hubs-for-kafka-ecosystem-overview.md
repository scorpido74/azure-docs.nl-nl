---
title: Event Hub van Apache Kafka app gebruiken-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over Apache Kafka ondersteuning door Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 733623895176bb3b573c2efcbda8a40b9e2d87c0
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85320525"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Azure Event Hubs van Apache Kafka toepassingen gebruiken
Event Hubs biedt een Kafka-eind punt dat door uw bestaande op Kafka gebaseerde toepassingen kan worden gebruikt als alternatief voor het uitvoeren van uw eigen Kafka-cluster. Event Hubs ondersteunt [Apache Kafka protocol 1,0 en hoger](https://kafka.apache.org/documentation/)en werkt met uw bestaande Kafka-toepassingen, waaronder MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Wat biedt Event Hubs voor Kafka?

De functie Event Hubs voor Kafka voorziet in een Protocolsubstatus boven op de Azure-Event Hubs die binair compatibel is met Kafka versie 1,0 en hoger voor zowel lees-als schrijf bewerkingen naar Kafka-onderwerpen. U kunt beginnen met het gebruik van het Kafka-eind punt vanuit uw toepassingen zonder code wijziging, maar een minimale configuratie wijziging. U werkt de connection string in configuraties bij zodat deze verwijzen naar het Kafka-eind punt dat door uw Event Hub wordt weer gegeven in plaats van naar uw Kafka-cluster te wijzen. Vervolgens kunt u gebeurtenissen vanuit uw toepassingen die gebruikmaken van het Kafka-protocol in Event Hubs starten. Deze integratie biedt ook ondersteuning voor frameworks zoals [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), dat momenteel als preview-versie beschikbaar is. 

Conceptueel Kafka en Event Hubs bijna identiek zijn: ze zijn beide gepartitioneerde logboeken die zijn gemaakt voor het streamen van gegevens. In de volgende tabel worden concepten tussen Kafka en Event Hubs toegewezen.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Conceptuele toewijzing van Kafka en Event hub

| Kafka-concept | Event Hubs concept|
| --- | --- |
| Cluster | Naamruimte |
| Onderwerp | Event Hub |
| Partitie | Partitie|
| Consumenten groep | Consumenten groep |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>De belangrijkste verschillen tussen Kafka en Event Hubs

Hoewel [Apache Kafka](https://kafka.apache.org/) software is, die u overal kunt uitvoeren, is Event hubs een Cloud service die vergelijkbaar is met Azure Blob Storage. Er zijn geen servers of netwerken om te beheren en er kunnen geen makelaars worden geconfigureerd. U maakt een naam ruimte, een FQDN waarin uw onderwerpen wonen en vervolgens Event Hubs of onderwerpen maken binnen die naam ruimte. Zie [Event hubs-functies](event-hubs-features.md#namespace)voor meer informatie over Event hubs en naam ruimten. Als Cloud service gebruikt Event Hubs één stabiel virtueel IP-adres als het eind punt, zodat clients niet hoeven te weten over de Brokers of computers binnen een cluster. 

De schaal van Event Hubs wordt bepaald door het aantal doorvoer eenheden dat u aanschaft, waarbij elke doorvoer eenheid u een recht geeft op 1 MB per seconde, of 1000 gebeurtenissen per seconde van ingang. Event Hubs worden standaard doorvoer eenheden geschaald wanneer u de limiet bereikt met de functie voor [automatisch verg Roten](event-hubs-auto-inflate.md) . deze functie werkt ook met de Event Hubs voor de functie Kafka. 

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
Voor een **zelf studie** met stapsgewijze instructies voor het maken van een event hub en het openen met behulp van SAS of OAuth, raadpleegt u [Quick Start: streamen met Event hubs met behulp van het Kafka-protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Voor meer voor **beelden** die laten zien hoe u OAuth gebruikt met Event hubs voor Kafka, raadpleegt u voor [beelden op github](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andere Event Hubs-functies die beschikbaar zijn voor Kafka

Met de functie Event Hubs voor Kafka kunt u met één protocol schrijven en met een andere en lezen, zodat uw huidige Kafka-producenten kunnen blijven publiceren via Kafka, en u kunt lezers toevoegen met Event Hubs, zoals Azure Stream Analytics of Azure Functions. Daarnaast werkt Event Hubs functies, zoals [vastleggen](event-hubs-capture-overview.md) en [geo-herstel na nood gevallen](event-hubs-geo-dr.md) , ook met de Event hubs voor de functie Kafka.

## <a name="features-that-are-not-yet-supported"></a>Functies die nog niet worden ondersteund 

Hier volgt een lijst met Kafka-functies die nog niet worden ondersteund:

*   Idempotent producer
*   Transactie
*   Compressie
*   Bewaar periode op basis van grootte
*   Logboek compressie
*   Partities toevoegen aan een bestaand onderwerp
*   HTTP Kafka API-ondersteuning
*   Kafka stromen

## <a name="next-steps"></a>Volgende stappen
In dit artikel is een inleiding tot Event Hubs voor Kafka beschreven. Zie [Apache Kafka ontwikkelaars handleiding voor Azure Event hubs](apache-kafka-developer-guide.md)voor meer informatie.


