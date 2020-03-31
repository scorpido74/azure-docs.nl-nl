---
title: Azure Service Bus-wachtrijen gebruiken met Ruby
description: In deze zelfstudie leert u hoe u Ruby-toepassingen maakt om berichten naar berichten te verzenden en te ontvangen vanuit een wachtrij voor servicebussen.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a699543bb442e7c57d57e72acb2cdf6ac40159c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760586"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Snelstart: Wachtrijen voor servicebussen gebruiken met Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In deze zelfstudie leert u hoe u Ruby-toepassingen maakt om berichten naar berichten te verzenden en te ontvangen vanuit een wachtrij voor servicebussen. De voorbeelden zijn geschreven in Ruby en gebruik maken van de Azure-parel.

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Volg stappen in de [Azure-portal gebruiken om een wachtrijartikel voor servicebus te maken.](service-bus-quickstart-portal.md)
    1. Lees het snelle **overzicht** van wachtrijen voor **servicebussen.** 
    2. Een **naamruimte**voor servicebus maken . 
    3. Haal de **verbindingstekenreeks**. 

        > [!NOTE]
        > U maakt een **wachtrij** in de naamruimte van de servicebus met Ruby in deze zelfstudie. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Een wachtrij maken
Met het object **Azure::ServiceBusService** u met wachtrijen werken. Als u een wachtrij `create_queue()` wilt maken, gebruikt u de methode. In het volgende voorbeeld wordt een wachtrij gemaakt of worden eventuele fouten afgedrukt.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

U ook een **Azure::ServiceBus::Wachtrijobject** met extra opties doorgeven, waarmee u de standaardwachtrijinstellingen overschrijven, zoals berichttijd om te wonen of de maximale wachtrijgrootte. In het volgende voorbeeld ziet u hoe u de maximale wachtrijgrootte instelt op 5 GB en de tijd om te leven op 1 minuut:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Berichten naar een wachtrij verzenden
Als u een bericht naar een wachtrij `send_queue_message()` van een servicebus wilt verzenden, roept uw toepassing de methode aan op het object **Azure::ServiceBusService.** Berichten die naar **azure::ServiceBus::BrokeredMessage-objecten** worden verzonden en ontvangen) en hebben een `label` set `time_to_live`standaardeigenschappen (zoals en ), een woordenboek dat wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bevatten en een verzameling willekeurige toepassingsgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door een tekenreekswaarde door te geven omdat het bericht en de vereiste standaardeigenschappen worden gevuld met standaardwaarden.

In het volgende voorbeeld wordt uitgelegd hoe u `test-queue` `send_queue_message()`een testbericht verzendt naar de wachtrij met de naam :

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Berichten worden ontvangen vanuit een `receive_queue_message()` wachtrij met behulp van de methode op het **azure::ServiceBusService-object.** Standaard worden berichten gelezen en vergrendeld zonder uit de wachtrij te worden verwijderd. U echter berichten uit de wachtrij verwijderen terwijl `:peek_lock` ze worden gelezen door de optie in te stellen op **false.**

Het standaardgedrag maakt het lezen en verwijderen van een tweetrapsbewerking, waardoor het ook mogelijk is om toepassingen te ondersteunen die ontbrekende berichten niet kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing is voltooid verwerking van het bericht (of slaat het betrouwbaar voor toekomstige `delete_queue_message()` verwerking), het voltooit de tweede fase van het ontvangstproces door aanroepen methode en het verstrekken van het bericht te worden verwijderd als een parameter. De `delete_queue_message()` methode markeert het bericht als verbruikt en verwijdert het uit de wachtrij.

Als `:peek_lock` de parameter is ingesteld op **false,** wordt het lezen en verwijderen van het bericht het eenvoudigste model en werkt het het beste voor scenario's waarin een toepassing kan tolereren dat een bericht niet wordt verwerkt in het geval van een storing. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als wordt verbruikt, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, heeft het bericht gemist dat voorafgaand aan de crash is verbruikt.

In het volgende voorbeeld wordt uitgelegd hoe `receive_queue_message()`u berichten ontvangen en verwerken met behulp van. Het voorbeeld ontvangt en verwijdert eerst `:peek_lock` een bericht met behulp van ingesteld op **false,** vervolgens ontvangt het een ander bericht en verwijdert vervolgens het bericht met `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangertoepassing het bericht om de een of `unlock_queue_message()` andere reden niet kan verwerken, kan het de methode aanroepen op het **azure::ServiceBusService-object.** Deze oproep zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en beschikbaar maakt om opnieuw te worden ontvangen, hetzij door dezelfde verbruikende toepassing, hetzij door een andere verbruikende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat is vergrendeld in de wachtrij, en als de toepassing het bericht niet verwerkt voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te ontvangen.

In het geval dat de toepassing crasht `delete_queue_message()` na het verwerken van het bericht, maar voordat de methode wordt aangeroepen, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Dit proces wordt vaak *ten minste eenmaal verwerken*genoemd ; dat wil zeggen, elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak `message_id` bereikt met behulp van de eigenschap van het bericht, dat constant blijft over leveringspogingen.

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basisprincipes van Service Bus-wachtrijen hebt geleerd, kunt u deze koppelingen volgen voor meer informatie.

* Overzicht van [wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md).
* Ga naar de [Azure SDK for Ruby-repository](https://github.com/Azure/azure-sdk-for-ruby) op GitHub.

Zie Azure Queues en Azure Service Bus Queues - Vergeleken [en Contrasterend](service-bus-azure-and-service-bus-queues-compared-contrasted.md) voor een vergelijking tussen de azure servicebuswachtrijen die in dit artikel worden besproken in het artikel [Wachtrijopslag gebruiken uit Ruby.](../storage/queues/storage-ruby-how-to-use-queue-storage.md)

