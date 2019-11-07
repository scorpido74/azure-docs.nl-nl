---
title: 'Snelstartgids: Azure Service Bus wachtrijen gebruiken met ruby'
description: 'Quick Start: informatie over het gebruik van Service Bus wachtrijen in Azure. Code voorbeelden geschreven in Ruby.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 09fdc58254d260b6ffeff958b6bbda50332adfac
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718771"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Snelstartgids: Service Bus wachtrijen gebruiken met ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In deze zelf studie leert u hoe u ruby-toepassingen kunt maken om berichten te verzenden naar en berichten van een Service Bus wachtrij te ontvangen. De voor beelden zijn geschreven in Ruby en gebruiken de Azure Gem.

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) van uw MSDN-abonnee activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in de [Azure Portal gebruiken om een service bus wachtrij artikel te maken](service-bus-quickstart-portal.md) .
    1. Lees het kort **overzicht** van service bus- **wacht rijen**. 
    2. Maak een Service Bus **naam ruimte**. 
    3. Haal de **Connection String**op. 

        > [!NOTE]
        > In deze zelf studie maakt u een **wachtrij** in de service bus naam ruimte met behulp van Ruby. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Een wachtrij maken
Met het object **Azure:: ServiceBusService** kunt u met wacht rijen werken. Als u een wachtrij wilt maken, gebruikt u de methode `create_queue()`. In het volgende voor beeld wordt een wachtrij gemaakt of worden eventuele fouten afgedrukt.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

U kunt ook een **Azure:: ServiceBus:: queue** -object door geven met aanvullende opties. Hiermee kunt u de standaard instellingen voor de wachtrij negeren, zoals de bericht tijd in een live of maximale wachtrij grootte. In het volgende voor beeld ziet u hoe u de maximale wachtrij grootte instelt op 5 GB en de TTL-waarde op 1 minuut:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u een bericht wilt verzenden naar een Service Bus wachtrij, wordt de methode `send_queue_message()` door de toepassing aangeroepen in het object **Azure:: ServiceBusService** . Berichten die worden verzonden naar (en ontvangen van) Service Bus wacht rijen zijn **Azure:: ServiceBus:: BrokeredMessage** -objecten en hebben een aantal standaard eigenschappen (zoals `label` en `time_to_live`), een woorden lijst die wordt gebruikt voor het opslaan van aangepaste toepassingsspecifieke eigenschappen en een hoofd tekst van wille keurige toepassings gegevens. Een toepassing kan de hoofd tekst van het bericht instellen door een teken reeks waarde door te geven als het bericht en eventuele vereiste standaard eigenschappen worden gevuld met standaard waarden.

In het volgende voor beeld ziet u hoe u een test bericht naar de wachtrij met de naam `test-queue` verzendt met behulp van `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Er worden berichten van een wachtrij ontvangen met behulp van de methode `receive_queue_message()` in het object **Azure:: ServiceBusService** . Standaard worden berichten gelezen en vergrendeld zonder dat ze uit de wachtrij worden verwijderd. U kunt echter berichten uit de wachtrij verwijderen wanneer ze worden gelezen door de `:peek_lock` optie in te stellen op **Onwaar**.

De standaard instelling is het lezen en verwijderen van een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of op betrouw bare wijze is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door het aanroepen van de methode `delete_queue_message()` en het bericht te verwijderen als een para meter. Met de methode `delete_queue_message()` wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

Als de para meter `:peek_lock` is ingesteld op **False**, lezen en verwijderen van het bericht het eenvoudigste model is, en werkt het beste voor scenario's waarin een toepassing een bericht niet verwerkt in het geval van een fout. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, wordt het bericht dat voor het vastlopen is verbruikt, gemist wanneer de toepassing opnieuw wordt gestart en er opnieuw wordt verbruikt.

In het volgende voor beeld ziet u hoe u berichten ontvangt en verwerkt met behulp van `receive_queue_message()`. In het voor beeld wordt eerst een bericht ontvangen en verwijderd met behulp van `:peek_lock` ingesteld op **Onwaar**. vervolgens wordt er een ander bericht ontvangen en wordt het bericht vervolgens verwijderd met behulp van `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode `unlock_queue_message()` worden aangeroepen in het object **Azure:: ServiceBusService** . Deze aanroep veroorzaakt Service Bus om het bericht in de wachtrij te ontgrendelen en het beschikbaar te maken om opnieuw te worden ontvangen, ofwel door dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), wordt Service Bus het bericht automatisch ontgrendeld en wordt het beschikbaar om opnieuw te worden ontvangen.

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de `delete_queue_message()` methode wordt aangeroepen, wordt het bericht opnieuw aan de toepassing bezorgd wanneer het opnieuw wordt gestart. Dit proces wordt vaak *minstens eenmaal verwerkt*. dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met behulp van de eigenschap `message_id` van het bericht, dat constant blijft tijdens bezorgings pogingen.

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basisprincipes van Service Bus-wachtrijen hebt geleerd, kunt u deze koppelingen volgen voor meer informatie.

* Overzicht van [wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md).
* Ga naar de [Azure SDK voor ruby](https://github.com/Azure/azure-sdk-for-ruby) -opslag plaats op github.

Voor een vergelijking tussen de Azure Service Bus wacht rijen die in dit artikel worden besproken en Azure-wacht rijen die worden beschreven in het artikel [wachtrij opslag gebruiken van Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) , raadpleegt u [Azure queues and Azure service bus queues-vergeleken en daarentegen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

