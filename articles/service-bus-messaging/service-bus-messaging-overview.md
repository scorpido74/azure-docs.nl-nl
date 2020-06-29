---
title: Overzicht van Azure Service Bus-berichtenservice | Microsoft Docs
description: Dit artikel bevat een overzicht op hoog niveau van Azure Service Bus, een volledig beheerde berichtenbroker die binnen ondernemingen kan worden geïntegreerd.
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
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707887"
---
# <a name="what-is-azure-service-bus"></a>Wat is Azure Service Bus?

Microsoft Azure Service Bus is een volledig beheerde berichtenbroker die binnen ondernemingen kan worden geïntegreerd. Service Bus kan toepassingen en services loskoppelen. Service Bus biedt een betrouwbaar en veilig platform voor asynchrone overdracht van gegevens en status.

Gegevens worden uitgewisseld tussen verschillende toepassingen en services met behulp van *berichten*. Een bericht heeft een binaire indeling en kan JSON, XML of alleen tekst bevatten. Zie [Integration Services](https://azure.com/integration) voor meer informatie.

Enkele algemene berichtenscenario's:

* *Berichtenuitwisseling*. Draag bedrijfsgegevens over, zoals verkoop- of inkooporders, dagboeken of voorraadverplaatsingen.
* *Toepassingen loskoppelen*. Verbeter de betrouwbaarheid en schaalbaarheid van toepassingen en services. Client en service hoeven niet tegelijkertijd online te zijn.
* *Onderwerpen en abonnementen*. 1:*n*-relaties mogelijk maken tussen uitgevers en abonnees.
* *Berichtsessies*. Werkstromen implementeren waarvoor het ordenen of uitstellen van berichten vereist is.

## <a name="namespaces"></a>Naamruimten

Een naamruimte is een container voor alle berichtenonderdelen. Er kunnen zich meerdere wachtrijen en onderwerpen in één naamruimte bevinden, en naamruimten fungeren vaak als toepassingscontainers.

## <a name="queues"></a>Wachtrijen

Berichten worden verzonden naar en ontvangen van *wachtrijen*. Wachtrijen slaan berichten op totdat de ontvangende toepassing ze kan ontvangen en verwerken.

![Wachtrij](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Berichten in wachtrijen worden bij ontvangst geordend en voorzien van een tijdstempel. Als een bericht is geaccepteerd, wordt het veilig ondergebracht in redundante opslag. Berichten worden bezorgd in *pull*-modus, waarbij berichten alleen op aanvraag worden geleverd.

## <a name="topics"></a>Onderwerpen

U kunt ook *onderwerpen* gebruiken voor het verzenden en ontvangen van berichten. Daar waar een wachtrij vaak wordt gebruikt voor punt-naar-punt communicatie, zijn onderwerpen handig in scenario's met publiceren/abonneren.

![Onderwerp](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Onderwerpen kunnen meerdere, onafhankelijke abonnementen hebben. Een abonnee van een onderwerp kan een kopie ontvangen van elk bericht dat naar dat onderwerp wordt verzonden. Abonnementen worden entiteiten genoemd. De abonnementen blijven behouden, maar kunnen verlopen of automatisch worden verwijderd.

Mogelijk wilt u niet dat afzonderlijke abonnementen alle berichten ontvangen die naar een onderwerp worden gestuurd. Als dat het geval is, kunt u *regels* en *filters* gebruiken om voorwaarden te definiëren waarmee optionele *acties* worden geactiveerd. U kunt opgegeven berichten filteren en berichteigenschappen instellen of wijzigen. Zie [Onderwerpfilters en acties](topic-filters.md) voor meer informatie.

## <a name="advanced-features"></a>Geavanceerde functies

Service Bus beschikt over geavanceerde functies waarmee u complexere problemen met berichtenuitwisseling kunt oplossen. In de volgende secties worden verschillende van deze functies beschreven.

### <a name="message-sessions"></a>Berichtsessies

Als u er zeker van wilt zijn dat berichten in Service Bus op basis van FIFO (first in, first out) worden verwerkt, moet u sessies gebruiken. Berichtsessies maken gezamenlijke en geordende verwerking van niet-gekoppelde reeksen gerelateerde berichten mogelijk. Zie [Message sessions: first in, first out (FIFO)](message-sessions.md) (Berichtensessies: first in, first out (FIFO)) voor meer informatie.

### <a name="autoforwarding"></a>Automatisch doorsturen

De functie voor automatisch doorsturen koppelt een wachtrij of abonnement aan een andere wachtrij of een ander onderwerp. Ze moeten deel uitmaken van dezelfde naamruimte. Met automatisch doorsturen verwijdert Service Bus berichten uit een wachtrij of abonnement, en verplaatst deze naar een andere wachtrij of een ander onderwerp. Zie [Chaining Service Bus entities with autoforwarding](service-bus-auto-forwarding.md) (Service Bus-entiteiten koppelen met automatisch doorsturen) voor meer informatie.

### <a name="dead-letter-queue"></a>Wachtrij voor onbestelbare berichten

Service Bus ondersteunt een wachtrij voor onbestelbare berichten (DLQ). Een DLQ bevat berichten die niet aan een ontvanger kunnen worden geleverd. De wachtrij bevat berichten die niet kunnen worden verwerkt. Met Service Bus kunt u berichten uit de DLQ verwijderen en deze controleren. Zie [Overview of Service Bus dead-letter queues](service-bus-dead-letter-queues.md) (Overzicht van Service Bus-wachtrijen voor onbestelbare berichten) voor meer informatie.

### <a name="scheduled-delivery"></a>Geplande bezorging

U kunt berichten naar een wachtrij of onderwerp verzenden voor een vertraagde verwerking. U kunt plannen dat een taak op een bepaald moment beschikbaar is voor verwerking in een systeem. Zie [Geplande berichten](message-sequencing.md#scheduled-messages) voor meer informatie.

### <a name="message-deferral"></a>Berichten uitstellen

Een wachtrij- of abonnementsclient kan het ophalen van een bericht tot een later tijdstip uitstellen. Dit uitstel kan worden veroorzaakt door speciale omstandigheden in de toepassing. Het bericht blijft in de wachtrij of het abonnement staan, maar wordt nog niet verwerkt. Zie [Berichten uitstellen](message-deferral.md) voor meer informatie.

### <a name="batching"></a>Batchverwerking

Batchverwerking aan de clientzijde zorgt ervoor dat een wachtrij- of onderwerpclient het verzenden van een bericht voor een bepaalde tijd kan vertragen. Als de client aanvullende berichten verstuurt tijdens deze periode, worden de berichten in één batch verstuurd. Zie [Client-side batching](service-bus-performance-improvements.md#client-side-batching) (Batchverwerking aan de clientzijde) voor meer informatie.

### <a name="transactions"></a>Transacties

Een transactie groepeert twee of meer bewerkingen in een *uitvoeringsbereik*. Service Bus biedt ondersteuning voor het groeperen van bewerkingen voor één berichtentiteit binnen het bereik van één transactie. Een berichtentiteit kan een wachtrij, onderwerp of abonnement zijn. Zie [Overview of Service Bus transaction processing](service-bus-transactions.md) (Overzicht van transactieverwerking in Service Bus) voor meer informatie.

### <a name="filtering-and-actions"></a>Filteren en acties

Abonnees kunnen definiëren welke berichten ze willen ontvangen van een onderwerp. Deze berichten worden opgegeven in de vorm van een of meer benoemde abonnementsregels. Voor elke regelvoorwaarde waaraan wordt voldaan, produceert het abonnement een kopie van het bericht, dat voor elke overeenkomende regel anders kan worden geannoteerd. Zie [Onderwerpfilters en acties](topic-filters.md) voor meer informatie.

### <a name="autodelete-on-idle"></a>Automatisch verwijderen bij inactiviteit

Automatisch verwijderen bij inactiviteit houdt in dat u een interval voor inactiviteit kunt opgeven waarna de wachtrij automatisch wordt verwijderd. De minimale duur is vijf minuten. Zie [QueueDescription.AutoDeleteOnIdle Property](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle) (Eigenschap QueueDescription.AutoDeleteOnIdle) voor meer informatie.

### <a name="duplicate-detection"></a>Detectie van duplicaten

Een fout kan ertoe leiden dat de client twijfelt over het resultaat van een verzendbewerking. Met detectie van duplicaten kan de afzender hetzelfde bericht opnieuw verzenden. Ook kan de wachtrij of het onderwerp dubbele kopieën verwijderen. Zie [Detectie van duplicaten](duplicate-detection.md) voor meer informatie.

### <a name="security-protocols"></a>Beveiligingsprotocollen
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus biedt ondersteuning voor beveiligingsprotocollen zoals [SAS](service-bus-sas.md) (Shared Access Signatures), [RBAC](authenticate-application.md) (op rollen gebaseerd toegangsbeheer) en [MSI](service-bus-managed-service-identity.md) (Managed Service Identity) voor Azure-resources.

### <a name="geo-disaster-recovery"></a>Geo-noodherstel

Wanneer er sprake is van uitval in Azure-regio's of -datacenters, zorgt geo-herstel na noodgeval ervoor dat de gegevensverwerking kan plaatsvinden in een andere regio of in een ander datacenter. Zie [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md) (Geo-herstel na noodgeval in Azure Service Bus) voor meer informatie.

### <a name="security"></a>Beveiliging

Service Bus ondersteunt standaard [AMQP 1.0](service-bus-amqp-overview.md) en [HTTP/REST](/rest/api/servicebus/)-protocollen.

## <a name="client-libraries"></a>Clientbibliotheken

Service Bus ondersteunt clientbibliotheken voor [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master) en [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integratie

Service Bus kan volledig worden geïntegreerd met de volgende Azure-services:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen om aan de slag te gaan met de Service Bus-berichtenservice:

* Zie [Vergelijking van services](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json) als u Azure-berichtenservices wilt vergelijken.
* Probeer de quickstarts voor [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) of [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Zie [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) voor het beheren van Service Bus-resources.
* Zie [Prijzen van Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) voor meer informatie over de Standard- en Premium-categorieën en de bijbehorende tarieven.
* Zie [Premium Messaging](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722) voor meer informatie over de prestaties en latentie voor de Premium-categorie.
