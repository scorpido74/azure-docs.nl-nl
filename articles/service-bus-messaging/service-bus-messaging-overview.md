---
title: Overzicht van Azure Service Bus-berichtenservice | Microsoft Docs
description: Dit artikel biedt een overzicht op hoog niveau van Azure Service Bus, een volledig beheerde berichtmakelaar voor bedrijfsintegratie.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240630"
---
# <a name="what-is-azure-service-bus"></a>Wat is Azure Service Bus?

Microsoft Azure Service Bus is een volledig beheerde berichtenbroker die binnen ondernemingen kan worden geïntegreerd. Service Bus kan applicaties en services ontkoppelen. Service Bus biedt een betrouwbaar en veilig platform voor asynchrone overdracht van gegevens en status.

Gegevens worden uitgewisseld tussen verschillende toepassingen en services met behulp van *berichten*. Een bericht is in binaire indeling en kan JSON, XML of gewoon tekst bevatten. Zie [Integration Services](https://azure.com/integration)voor meer informatie.

Enkele algemene berichtenscenario's:

* *Berichten*. Zakelijke gegevens overdragen, zoals verkoop- of inkooporders, dagboeken of voorraadbewegingen.
* *Toepassingen ontkoppelen*. Verbeter de betrouwbaarheid en schaalbaarheid van applicaties en services. Klant en service hoeven niet tegelijkertijd online te zijn.
* *Onderwerpen en abonnementen*. Enable 1:*n* relaties tussen uitgevers en abonnees.
* *Berichtsessies*. Werkstromen implementeren waarvoor berichtvolgorde of berichtuitstel vereist is.

## <a name="namespaces"></a>Naamruimten

Een naamruimte is een container voor alle berichtencomponenten. Meerdere wachtrijen en onderwerpen kunnen zich in één naamruimte bevinden en naamruimten dienen vaak als toepassingscontainers.

## <a name="queues"></a>Wachtrijen

Berichten worden verzonden naar en ontvangen van *wachtrijen*. Wachtrijen slaan berichten op totdat de ontvangende toepassing beschikbaar is om ze te ontvangen en te verwerken.

![Wachtrij](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Berichten in wachtrijen worden besteld en bij aankomst getimed. Als een bericht is geaccepteerd, wordt het veilig ondergebracht in redundante opslag. Berichten worden geleverd in *de pull-modus,* alleen het leveren van berichten op verzoek.

## <a name="topics"></a>Onderwerpen

U kunt ook *onderwerpen* gebruiken voor het verzenden en ontvangen van berichten. Daar waar een wachtrij vaak wordt gebruikt voor punt-naar-punt communicatie, zijn onderwerpen handig in scenario's met publiceren/abonneren.

![Onderwerp](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Onderwerpen kunnen meerdere, onafhankelijke abonnementen hebben. Een abonnee van een onderwerp kan een kopie ontvangen van elk bericht dat naar dat onderwerp wordt verzonden. Abonnementen worden entiteiten genoemd. Abonnementen blijven bestaan, maar kunnen verlopen of automatisch worden verwijderd.

U wilt mogelijk niet dat afzonderlijke abonnementen alle berichten ontvangen die naar een onderwerp worden verzonden. Als dat het zo is, u *regels* en *filters* gebruiken om voorwaarden te definiëren die optionele *acties activeren.* U opgegeven berichten filteren en berichteigenschappen instellen of wijzigen. Zie [Onderwerpfilters en -acties voor](topic-filters.md)meer informatie .

## <a name="advanced-features"></a>Geavanceerde functies

Service Bus bevat geavanceerde functies waarmee u complexere berichtenproblemen oplossen. In de volgende secties worden een aantal van deze functies beschreven.

### <a name="message-sessions"></a>Berichtsessies

Gebruik sessies om een first-in first-out,first-out (FIFO) garantie in Service Bus te creëren. Berichtsessies maken gezamenlijke en geordende verwerking van niet-gekoppelde reeksen gerelateerde berichten mogelijk. Zie [Berichtensessies: first in, first out (FIFO)](message-sessions.md)voor meer informatie.

### <a name="autoforwarding"></a>Automatisch doorsturen

De functie autoforwarding maakt een wachtrij of abonnement op een andere wachtrij of onderwerp. Ze moeten deel uitmaken van dezelfde naamruimte. Met automatisch doorsturen verwijdert Service Bus automatisch berichten uit een wachtrij of abonnement en plaatst deze in een andere wachtrij of onderwerp. Zie [Entiteiten van Chaining Service Bus met autoforwarding](service-bus-auto-forwarding.md)voor meer informatie.

### <a name="dead-letter-queue"></a>Dode-letter wachtrij

Service Bus ondersteunt een dode-letter wachtrij (DLQ). Een DLQ bevat berichten die niet aan een ontvanger kunnen worden bezorgd. Er staan berichten op die niet kunnen worden verwerkt. Met Service Bus u berichten uit de DLQ verwijderen en inspecteren. Zie [Overzicht van wachtrijen voor servicebus.For](service-bus-dead-letter-queues.md)more information, see Overview of Service Bus dead-letter queues .

### <a name="scheduled-delivery"></a>Geplande bezorging

U berichten verzenden naar een wachtrij of onderwerp voor vertraagde verwerking. U een taak plannen om op een bepaald moment beschikbaar te komen voor verwerking door een systeem. Zie [Geplande berichten voor](message-sequencing.md#scheduled-messages)meer informatie .

### <a name="message-deferral"></a>Berichten uitstellen

Een wachtrij- of abonnementsclient kan het ophalen van een bericht uitstellen tot een later tijdstip. Dit uitstel kan het gevolg zijn van bijzondere omstandigheden in de aanvraag. Het bericht blijft in de wachtrij of het abonnement staan, maar het is apart gezet. Zie [Uitstel van bericht voor](message-deferral.md)meer informatie .

### <a name="batching"></a>Batchverwerking

Batchverwerking aan de clientzijde zorgt ervoor dat een wachtrij of onderwerpabonnee het verzenden van een bericht voor een bepaalde tijd kan vertragen. Als de client aanvullende berichten verstuurt tijdens deze periode, worden de berichten in één batch verstuurd. Zie [Batching aan clientzijde](service-bus-performance-improvements.md#client-side-batching)voor meer informatie.

### <a name="transactions"></a>Transacties

Een transactie groepeert twee of meer bewerkingen samen in een *uitvoeringsbereik*. Service Bus ondersteunt het groeperen van bewerkingen tegen één berichtenentiteit binnen het bereik van één transactie. Een berichtentiteit kan een wachtrij, onderwerp of abonnement zijn. Zie [Overzicht van de verwerking van servicebustransacties](service-bus-transactions.md)voor meer informatie .

### <a name="filtering-and-actions"></a>Filteren en acties

Abonnees kunnen definiëren welke berichten ze willen ontvangen van een onderwerp. Deze berichten worden opgegeven in de vorm van een of meer benoemde abonnementsregels. Voor elke overeenkomende regelvoorwaarde produceert het abonnement een kopie van het bericht, die voor elke overeenkomende regel anders kan worden geannoteerd. Zie [Onderwerpfilters en -acties voor](topic-filters.md)meer informatie .

### <a name="autodelete-on-idle"></a>Automatisch verwijderen in idle

Met Automatisch verwijderen in idle u een inactief interval opgeven waarna een wachtrij automatisch wordt verwijderd. De minimale duur is vijf minuten. Zie de eigenschap [QueueDescription.AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)voor meer informatie .

### <a name="duplicate-detection"></a>Detectie van duplicaten

Een fout kan ertoe leiden dat de klant twijfelt over de uitkomst van een verzendbewerking. Met dubbele detectie kan de afzender hetzelfde bericht opnieuw verzenden. Een andere optie is dat de wachtrij of het onderwerp dubbele kopieën verwijdert. Zie [Dubbele detectie voor](duplicate-detection.md)meer informatie.

### <a name="security-protocols"></a>Beveiligingsprotocollen
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus biedt ondersteuning voor beveiligingsprotocollen zoals [SAS](service-bus-sas.md) (Shared Access Signatures), [RBAC](authenticate-application.md) (op rollen gebaseerd toegangsbeheer) en [MSI](service-bus-managed-service-identity.md) (Managed Service Identity) voor Azure-resources.

### <a name="geo-disaster-recovery"></a>Geo-noodherstel

Wanneer er sprake is van uitval in Azure-regio's of -datacenters, zorgt Geo-noodherstel ervoor dat de gegevensverwerking kan plaatsvinden in een andere regio of in een ander datacenter. Zie [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md)voor meer informatie.

### <a name="security"></a>Beveiliging

Service Bus ondersteunt standaard [AMQP 1.0](service-bus-amqp-overview.md) en [HTTP/REST](/rest/api/servicebus/)-protocollen.

## <a name="client-libraries"></a>Clientbibliotheken

Service Bus ondersteunt clientbibliotheken voor [.NET,](https://github.com/Azure/azure-service-bus-dotnet/tree/master) [Java](https://github.com/Azure/azure-service-bus-java/tree/master)en [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integratie

Service Bus kan volledig worden geïntegreerd met de volgende Azure-services:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen om aan de slag te gaan met de Service Bus-berichtenservice:

* Zie [Vergelijking van services](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)vergelijken als u Azure-berichtenservices wilt vergelijken.
* Probeer de quickstarts voor [.NET,](service-bus-dotnet-get-started-with-queues.md) [Java](service-bus-java-how-to-use-queues.md)of [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Zie [Service Bus Explorer voor](https://github.com/paolosalvatori/ServiceBusExplorer/releases)het beheren van servicebusbronnen.
* Zie [Service Bus-prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor meer informatie over standaard- en premiumlagen en hun prijzen.
* Zie Premium Messaging voor meer informatie over prestaties en latentie voor de [Premium-laag.](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)
