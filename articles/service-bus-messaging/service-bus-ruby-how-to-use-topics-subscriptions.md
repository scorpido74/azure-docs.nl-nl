---
title: 'Snelstartgids: Service Bus-onderwerpen gebruiken (Ruby)'
description: 'Snelstartgids: informatie over het gebruik van Service Bus-onderwerpen en-abonnementen in Azure. Code voorbeelden zijn geschreven voor ruby-toepassingen.'
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b5401eae844ed2113a9fbc07c8b3ad8601709d43
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718930"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Snelstartgids: Service Bus-onderwerpen en-abonnementen gebruiken met ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Service Bus-onderwerpen en-abonnementen van Ruby-toepassingen gebruikt. De volgende scenario's zijn van toepassing:

- Onderwerpen en abonnementen maken 
- Abonnements filters maken 
- Berichten verzenden naar een onderwerp 
- Berichten ontvangen van een abonnement
- Onderwerpen en abonnementen verwijderen


## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voor delen voor Visual Studio of MSDN Subscriber](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in de [Snelstartgids: gebruik de Azure Portal om een service bus onderwerp en abonnementen aan het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om een service bus **naam ruimte** te maken en de **Connection String**op te halen. 

    > [!NOTE]
    > U maakt een **onderwerp** en een **abonnement** op het onderwerp met behulp van **ruby** in deze Quick Start. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Een onderwerp maken
Met het object **Azure:: ServiceBusService** kunt u werken met onderwerpen. Met de volgende code wordt een **Azure:: ServiceBusService** -object gemaakt. Als u een onderwerp wilt maken, gebruikt u de methode `create_topic()`. In het volgende voor beeld wordt een onderwerp gemaakt of worden eventuele fouten afgedrukt.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

U kunt ook een **Azure:: ServiceBus:: Topic** -object door geven aan de hand van extra opties, waarmee u standaard instellingen voor het onderwerp, zoals de bericht tijd, de TTL (time to Live) of de maximale wachtrij grootte. In het volgende voor beeld ziet u het instellen van de maximale wachtrij grootte op 5 GB en de TTL-waarde op 1 minuut:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Abonnementen maken
Er worden ook abonnementen van het onderwerp gemaakt met het object **Azure:: ServiceBusService** . Abonnementen krijgen een naam en kunnen een optioneel filter hebben waarmee de set berichten wordt beperkt die aan de virtuele wachtrij van het abonnement wordt geleverd.

Standaard zijn abonnementen permanent. Ze blijven bestaan totdat ze worden verwijderd, of het onderwerp waaraan ze zijn gekoppeld. Als uw toepassing logica bevat om een abonnement te maken, moet u eerst controleren of het abonnement al bestaat met behulp van de getSubscription-methode.

U kunt de abonnementen automatisch laten verwijderen door de [eigenschap AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)in te stellen.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Als er geen filter is opgegeven bij het maken van een nieuw abonnement, wordt het **MatchAll** -filter (standaard) gebruikt. Wanneer het filter **MatchAll** wordt gebruikt, worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. In het volgende voor beeld wordt een abonnement met de naam ' alle berichten ' gemaakt en wordt het standaard **MatchAll** -filter gebruikt.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U kunt ook filters definiëren om aan te geven welke berichten naar een onderwerp moeten worden weer gegeven binnen een specifiek abonnement.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is **Azure:: ServiceBus:: SqlFilter**, dat een SUBSET van SQL92 implementeert. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Raadpleeg de [SqlFilter](service-bus-messaging-sql-filter.md) -syntaxis voor meer informatie over de expressies die kunnen worden gebruikt met een SQL-filter.

U kunt filters toevoegen aan een abonnement met behulp van de methode `create_rule()` van het object **Azure:: ServiceBusService** . Met deze methode kunt u nieuwe filters toevoegen aan een bestaand abonnement.

Aangezien het standaard filter automatisch wordt toegepast op alle nieuwe abonnementen, moet u eerst het standaard filter verwijderen, of de **MatchAll** overschrijft andere filters die u kunt opgeven. U kunt de standaard regel verwijderen met behulp van de methode `delete_rule()` in het object **Azure:: ServiceBusService** .

In het volgende voor beeld wordt een abonnement met de naam ' hoge berichten ' gemaakt met een **Azure:: ServiceBus:: SqlFilter** , waarmee alleen berichten worden geselecteerd die een aangepaste `message_number` eigenschap hebben die groter is dan 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Op dezelfde manier maakt het volgende voor beeld een abonnement met de naam `low-messages` met een **Azure:: ServiceBus:: SqlFilter** die alleen berichten selecteert die een `message_number` eigenschap hebben die kleiner is dan of gelijk is aan 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Wanneer er nu een bericht naar `test-topic`wordt verzonden, wordt het altijd bezorgd bij ontvangers die zijn geabonneerd op het abonnement van de `all-messages`-onderwerp en worden ze selectief geleverd aan ontvangers die zijn geabonneerd op de abonnementen `high-messages` en `low-messages`-onderwerp (afhankelijk van het bericht inhoud).

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Als u een bericht wilt verzenden naar een Service Bus onderwerp, moet uw toepassing de methode `send_topic_message()` gebruiken in het object **Azure:: ServiceBusService** . Berichten die worden verzonden naar Service Bus onderwerpen zijn exemplaren van de objecten **Azure:: ServiceBus:: BrokeredMessage** . **Azure:: ServiceBus:: BrokeredMessage** -objecten hebben een aantal standaard eigenschappen (zoals `label` en `time_to_live`), een woorden lijst die wordt gebruikt voor het opslaan van aangepaste toepassingsspecifieke eigenschappen en een hoofd tekst van teken reeks gegevens. Een toepassing kan de hoofd tekst van het bericht instellen door een teken reeks waarde door te geven aan de `send_topic_message()` methode en alle vereiste standaard eigenschappen worden gevuld met standaard waarden.

In het volgende voor beeld ziet u hoe u vijf test berichten naar `test-topic`verzendt. De `message_number` aangepaste eigenschaps waarde van elk bericht varieert afhankelijk van de herhaling van de lus (Hiermee wordt bepaald welk abonnement wordt ontvangen):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
Er worden berichten ontvangen van een abonnement met behulp van de methode `receive_subscription_message()` in het object **Azure:: ServiceBusService** . Standaard worden berichten gelezen (pieken) en vergrendeld zonder dat ze worden verwijderd uit het abonnement. U kunt het bericht lezen en verwijderen uit het abonnement door de `peek_lock` optie in te stellen op **Onwaar**.

De standaard instelling is het lezen en verwijderen van een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of op betrouw bare wijze is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door het aanroepen van de methode `delete_subscription_message()` en het bericht te verwijderen als een para meter. Met de methode `delete_subscription_message()` wordt het bericht gemarkeerd als verbruikt en wordt het verwijderd uit het abonnement.

Als de para meter `:peek_lock` is ingesteld op **False**, lezen en verwijderen van het bericht het eenvoudigste model is, en werkt het beste voor scenario's waarin een toepassing niet de verwerking van een bericht kan verdragen wanneer zich een fout voordoet. Overweeg een scenario waarin de consumer de ontvangst aanvraag uitgeeft en vervolgens vastloopt voordat deze wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als verbruikt, wordt het bericht dat voor het vastlopen is verbruikt, gemist wanneer de toepassing opnieuw wordt gestart en er opnieuw wordt verbruikt.

In het volgende voor beeld ziet u hoe berichten kunnen worden ontvangen en verwerkt met behulp van `receive_subscription_message()`. In het voor beeld wordt eerst een bericht ontvangen en verwijderd uit het `low-messages`-abonnement met behulp van `:peek_lock` ingesteld op **False**. Vervolgens ontvangt het een ander bericht van de `high-messages` en wordt het bericht vervolgens verwijderd met `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode `unlock_subscription_message()` worden aangeroepen in het object **Azure:: ServiceBusService** . Het zorgt ervoor dat Service Bus het bericht in het abonnement ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in het abonnement is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), Service Bus het bericht automatisch ontgrendelen en Zorg ervoor dat de app weer beschikbaar is en opnieuw kan worden ontvangen.

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de `delete_subscription_message()` methode wordt aangeroepen, wordt het bericht opnieuw aan de toepassing bezorgd wanneer het opnieuw wordt gestart. Dit wordt vaak *ten minste eenmaal per verwerking*genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Deze logica wordt vaak bereikt met behulp van de eigenschap `message_id` van het bericht, dat constant blijft tijdens bezorgings pogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Onderwerpen en abonnementen zijn permanent, tenzij de [eigenschap AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) is ingesteld. Ze kunnen worden verwijderd via de [Azure Portal][Azure portal] of via een programma. In het volgende voor beeld ziet u hoe u het onderwerp met de naam `test-topic`verwijdert.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. De volgende code laat zien hoe u het abonnement met de naam `high-messages` verwijderen uit het onderwerp `test-topic`:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Service Bus onderwerpen hebt geleerd, volgt u deze koppelingen voor meer informatie.

* Zie [wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md).
* API-naslaginformatie voor [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Ga naar de [Azure SDK voor ruby](https://github.com/Azure/azure-sdk-for-ruby) -opslag plaats op github.

[Azure portal]: https://portal.azure.com
