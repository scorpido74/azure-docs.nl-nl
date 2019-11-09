---
title: Overzicht van Azure Service Bus-berichtenservice | Microsoft Docs
description: Beschrijving van Service Bus-berichten
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
ms.openlocfilehash: e2460ab760811a3db39058eac74d519ca09046c6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889818"
---
# <a name="what-is-azure-service-bus"></a>Wat is Azure Service Bus?

Microsoft Azure Service Bus is een volledig beheerde berichtenbroker die binnen ondernemingen kan worden geïntegreerd. Service Bus kunnen toepassingen en services loskoppelen. Service Bus biedt een betrouwbaar en veilig platform voor asynchrone gegevens en overdracht van status.

Gegevens worden uitgewisseld tussen verschillende toepassingen en services met behulp van *berichten*. Een bericht heeft een binaire indeling en kan JSON, XML of alleen tekst bevatten. Zie [Integration Services](https://azure.com/integration)(Engelstalig) voor meer informatie.

Enkele algemene berichtenscenario's:

* *Berichtenuitwisseling*. Bedrijfs gegevens overdragen, zoals verkoop-of inkoop orders, journalen of voorraad verplaatsingen.
* *Toepassingen loskoppelen*. Verbeter de betrouw baarheid en schaal baarheid van toepassingen en services. Client en service hoeven niet tegelijkertijd online te zijn.
* *Onderwerpen en abonnementen*. Schakel 1:*n* relaties in tussen uitgevers en abonnees.
* *Bericht sessies*. Implementeer werk stromen waarvoor bericht volgorde of bericht uitstel vereist is.

## <a name="namespaces"></a>Naamruimten

Een naam ruimte is een container voor alle Messa ging-onderdelen. Meerdere wacht rijen en onderwerpen kunnen zich in één naam ruimte bevinden, en naam ruimten fungeren vaak als toepassings containers.

## <a name="queues"></a>Wachtrijen

Berichten worden verzonden naar en ontvangen van *wachtrijen*. Wacht rijen slaan berichten totdat de ontvangende toepassing beschikbaar is om ze te ontvangen en verwerken.

![Wachtrij](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Berichten in wacht rijen worden besteld en getimed op aankomst. Als een bericht is geaccepteerd, wordt het veilig ondergebracht in redundante opslag. Berichten worden in de *pull* -modus bezorgd en leveren alleen berichten wanneer daarom wordt gevraagd.

## <a name="topics"></a>Onderwerpen

U kunt ook *onderwerpen* gebruiken voor het verzenden en ontvangen van berichten. Daar waar een wachtrij vaak wordt gebruikt voor punt-naar-punt communicatie, zijn onderwerpen handig in scenario's met publiceren/abonneren.

![Onderwerp](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Onderwerpen kunnen meerdere, onafhankelijke abonnementen hebben. Een abonnee van een onderwerp kan een kopie ontvangen van elk bericht dat naar dat onderwerp wordt verzonden. Abonnementen hebben de naam entities. De abonnementen blijven behouden, maar kunnen verlopen of worden verwijderd.

Mogelijk wilt u niet dat afzonderlijke abonnementen alle berichten ontvangen die naar een onderwerp worden verzonden. Als dat het geval is, kunt u *regels* en *filters* gebruiken om voor waarden te definiëren waarmee optionele *acties*worden geactiveerd. U kunt opgegeven berichten filteren en bericht eigenschappen instellen of wijzigen. Zie voor meer informatie [onderwerp filters en acties](topic-filters.md).

## <a name="advanced-features"></a>Geavanceerde functies

Service Bus bevat geavanceerde functies waarmee u complexere bericht problemen kunt oplossen. In de volgende secties worden verschillende van deze functies beschreven.

### <a name="message-sessions"></a>Berichtsessies

Als u een FIFO-garantie (First-in, first-out) in Service Bus wilt maken, gebruikt u sessies. Bericht sessies bieden gezamenlijke en geordende verwerking van niet-gebonden reeksen van gerelateerde berichten. Zie [Message Sessions: First-in, first out (FIFO)](message-sessions.md)voor meer informatie.

### <a name="autoforwarding"></a>Autoforwarding

De functie voor het door sturen van een wachtrij of-abonnement is gekoppeld aan een andere wachtrij of een ander onderwerp. Ze moeten deel uitmaken van dezelfde naam ruimte. Door automatisch door te sturen Service Bus berichten van een wachtrij of abonnement, en worden deze in een andere wachtrij of een ander onderwerp geplaatst. Zie [Service Bus entiteiten koppelen met autoforwarding](service-bus-auto-forwarding.md)voor meer informatie.

### <a name="dead-letter-queue"></a>Wachtrij met onbestelbare berichten

Service Bus ondersteunt een wachtrij met onbestelbare berichten (DLQ). Een DLQ bevat berichten die niet aan een ontvanger kunnen worden geleverd. Het bevat berichten die niet kunnen worden verwerkt. Met Service Bus kunt u berichten uit de DLQ verwijderen en deze controleren. Zie [overzicht van service bus wacht rijen voor onbestelbare berichten](service-bus-dead-letter-queues.md)voor meer informatie.

### <a name="scheduled-delivery"></a>Geplande bezorging

U kunt berichten verzenden naar een wachtrij of onderwerp voor een vertraagde verwerking. U kunt plannen dat een taak op een bepaald moment beschikbaar is voor verwerking. Zie [geplande berichten](message-sequencing.md#scheduled-messages)voor meer informatie.

### <a name="message-deferral"></a>Berichten uitstellen

Een wachtrij-of abonnements-client kan ophalen van een bericht tot een later tijdstip uitstellen. Deze uitstel kan worden veroorzaakt door speciale omstandigheden in de toepassing. Het bericht blijft in de wachtrij of het abonnement, maar wordt wel gereserveerd. Zie voor meer informatie [bericht uitstel](message-deferral.md).

### <a name="batching"></a>Batchverwerking

Met batch verwerking aan client zijde kan een wachtrij of een onderwerp-client een bericht voor een bepaalde periode vertragen. Als de client aanvullende berichten verstuurt tijdens deze periode, worden de berichten in één batch verstuurd. Zie batch verwerking aan de [client zijde](service-bus-performance-improvements.md#client-side-batching)voor meer informatie.

### <a name="transactions"></a>Transacties

Met een trans actie worden twee of meer bewerkingen gegroepeerd in een *uitvoerings bereik*. Service Bus ondersteunt groeperings bewerkingen op één Messa ging-entiteit binnen het bereik van één trans actie. Een bericht entiteit kan een wachtrij, onderwerp of abonnement zijn. Zie [overzicht van de verwerking van service bus transacties](service-bus-transactions.md)voor meer informatie.

### <a name="filtering-and-actions"></a>Filteren en acties

Abonnees kunnen definiëren welke berichten ze willen ontvangen van een onderwerp. Deze berichten worden opgegeven in de vorm van een of meer regels met de naam abonnement. Voor elke vergelijkings regel voorwaarde produceert het abonnement een kopie van het bericht, dat voor elke overeenkomende regel verschillend kan worden gemarkeerd. Zie voor meer informatie [onderwerp filters en acties](topic-filters.md).

### <a name="autodelete-on-idle"></a>Autoverwijderen bij niet-actief

Met automatisch verwijderen bij inactief kunt u een niet-actief interval opgeven waarna een wachtrij automatisch wordt verwijderd. De minimale duur is vijf minuten. Zie de [eigenschap QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)voor meer informatie.

### <a name="duplicate-detection"></a>Detectie van duplicaten

Een fout kan ertoe leiden dat de client twijfelt over het resultaat van een verzend bewerking. Met duplicaten detectie kan de afzender hetzelfde bericht opnieuw verzenden. Een andere optie is voor de wachtrij of het onderwerp om dubbele kopieën te verwijderen. Zie [Duplicaten detectie](duplicate-detection.md)voor meer informatie.

### <a name="security-protocols"></a>Beveiligings protocollen
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus biedt ondersteuning voor beveiligingsprotocollen zoals [SAS](service-bus-sas.md) (Shared Access Signatures), [RBAC](authenticate-application.md) (op rollen gebaseerd toegangsbeheer) en [MSI](service-bus-managed-service-identity.md) (Managed Service Identity) voor Azure-resources.

### <a name="geo-disaster-recovery"></a>Geo-noodherstel

Wanneer Azure-regio's of-Data Centers downtime hebben, kan gegevens verwerking door de geo-nood herstel bewerking worden uitgevoerd in een andere regio of Data Center. Zie [Azure service bus geo-nood herstel](service-bus-geo-dr.md)voor meer informatie.

### <a name="security"></a>Beveiliging

Service Bus ondersteunt standaard [AMQP 1.0](service-bus-amqp-overview.md) en [HTTP/REST](/rest/api/servicebus/)-protocollen.

## <a name="client-libraries"></a>Clientbibliotheken

Service Bus ondersteunt client bibliotheken voor [.net](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master)en [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integratie

Service Bus kan volledig worden geïntegreerd met de volgende Azure-services:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logische apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen om aan de slag te gaan met de Service Bus-berichtenservice:

* Zie [vergelijking van services](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)voor het vergelijken van Azure Messa ging Services.
* Probeer de Quick starts voor [.net](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)of [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Zie [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)als u Service Bus resources wilt beheren.
* Zie [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor meer informatie over de standaard-en Premium-lagen en hun prijzen.
* Zie [Premium Messa ging](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)(Engelstalig) voor meer informatie over prestaties en latentie voor de Premium-laag.
