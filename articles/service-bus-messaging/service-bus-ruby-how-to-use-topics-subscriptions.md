---
title: 'Quickstart: Service Bus-onderwerpen gebruiken (Ruby)'
description: 'Snelstart: meer informatie over het gebruik van servicebus-onderwerpen en -abonnementen in Azure. Code samples zijn geschreven voor Ruby toepassingen.'
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73718930"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Snelstart: Service Bus-onderwerpen en abonnementen gebruiken met Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u servicebus-onderwerpen en abonnementen van Ruby-toepassingen gebruiken. De behandelde scenario's omvatten:

- Onderwerpen en abonnementen maken 
- Abonnementsfilters maken 
- Berichten naar een onderwerp verzenden 
- Berichten ontvangen van een abonnement
- Onderwerpen en abonnementen verwijderen


## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [Visual Studio- of MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Volg stappen in de [Quickstart: gebruik de Azure-portal om een servicebusonderwerp en abonnementen op het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om een **naamruimte** voor servicebus te maken en de **verbindingstekenreeks op te**halen. 

    > [!NOTE]
    > Je maakt een **topic** en een **abonnement op** het onderwerp door **Ruby** in deze quickstart te gebruiken. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Een onderwerp maken
Met het object **Azure::ServiceBusService** u met onderwerpen werken. Met de volgende code wordt een **Azure::ServiceBusService-object** gemaakt. Als u een onderwerp `create_topic()` wilt maken, gebruikt u de methode. In het volgende voorbeeld wordt een onderwerp gemaakt of worden eventuele fouten afgedrukt.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

U ook een **Azure::ServiceBus::Object::Topic** met extra opties doorgeven, waarmee u standaardonderwerpinstellingen zoals berichttijd overschrijven om te leven of de maximale wachtrijgrootte. In het volgende voorbeeld wordt de maximale wachtrijgrootte ingesteld op 5 GB en de tijd om te leven tot 1 minuut:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Abonnementen maken
Onderwerpabonnementen worden ook gemaakt met het object **Azure::ServiceBusService.** Abonnementen hebben de naam en kunnen een optioneel filter hebben dat de set berichten beperkt die worden geleverd in de virtuele wachtrij van het abonnement.

Standaard zijn abonnementen blijvend. Ze blijven bestaan totdat ze, of het onderwerp waaraan ze zijn gekoppeld, worden verwijderd. Als uw toepassing logica bevat om een abonnement te maken, moet deze eerst controleren of het abonnement al bestaat met behulp van de methode getSubscription.

U de abonnementen automatisch laten verwijderen door de [eigenschap AutoDeleteOnIdle in te](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)stellen.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Als er geen filter is opgegeven wanneer een nieuw abonnement wordt gemaakt, wordt het **Filter MatchAll** (standaard) gebruikt. Wanneer het filter **MatchAll** wordt gebruikt, worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. In het volgende voorbeeld wordt een abonnement gemaakt met de naam 'alle berichten' en wordt het standaard **filter MatchAll** gebruikt.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U ook filters definiëren waarmee u opgeven welke berichten die naar een onderwerp worden verzonden, binnen een specifiek abonnement moeten worden weergegeven.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is de **Azure::ServiceBus:SqlFilter**, die een subset van SQL92 implementeert. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Controleer de syntaxis [van SqlFilter](service-bus-messaging-sql-filter.md) voor meer informatie over de expressies die met een SQL-filter kunnen worden gebruikt.

U filters aan een `create_rule()` abonnement toevoegen met behulp van de methode van het object **Azure::ServiceBusService.** Met deze methode u nieuwe filters toevoegen aan een bestaand abonnement.

Aangezien het standaardfilter automatisch wordt toegepast op alle nieuwe abonnementen, moet u eerst het standaardfilter verwijderen of overschrijft de **MatchAll** alle andere filters die u opgeven. U de standaardregel `delete_rule()` verwijderen met behulp van de methode op het object **Azure::ServiceBusService.**

In het volgende voorbeeld wordt een abonnement gemaakt met de naam 'hoge berichten' met een **Azure::ServiceBus::SqlFilter** dat alleen berichten selecteert met een aangepaste `message_number` eigenschap groter dan 3:

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

In het volgende voorbeeld wordt `low-messages` ook een abonnement gemaakt met de naam **Azure::ServiceBus::SqlFilter** dat alleen berichten selecteert met een `message_number` eigenschap die kleiner is dan of gelijk is aan 3:

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

Wanneer een bericht nu `test-topic`wordt verzonden naar , wordt `all-messages` het altijd geleverd aan ontvangers die `high-messages` `low-messages` zijn geabonneerd op het onderwerpabonnement en selectief worden geleverd aan ontvangers die zijn geabonneerd op de en onderwerpabonnementen (afhankelijk van de inhoud van het bericht).

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Als u een bericht wilt verzenden naar een `send_topic_message()` servicebusonderwerp, moet uw toepassing de methode gebruiken op het object **Azure::ServiceBusService.** Berichten die naar servicebus-onderwerpen worden verzonden, zijn instanties van de **azure::ServiceBus::BrokeredMessage-objecten.** **Azure::ServiceBus:::BrokeredMessage-objecten** hebben een set `label` standaardeigenschappen (zoals en `time_to_live`), een woordenboek dat wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bevatten en een reeks tekenreeksgegevens. Een toepassing kan de hoofdtekst van het bericht `send_topic_message()` instellen door een tekenreekswaarde door te geven aan de methode en alle vereiste standaardeigenschappen worden standaardwaarden ingevuld.

In het volgende voorbeeld wordt uitgelegd hoe `test-topic`u vijf testberichten verzenden naar. De `message_number` aangepaste eigenschapswaarde van elk bericht is afhankelijk van de iteratie van de lus (het bepaalt welk abonnement het ontvangt):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
Berichten worden ontvangen van een `receive_subscription_message()` abonnement met behulp van de methode op het **azure::ServiceBusService-object.** Standaard worden berichten gelezen(piek) en vergrendeld zonder het abonnement te verwijderen. U het bericht uit het abonnement `peek_lock` lezen en verwijderen door de optie in te stellen op **false.**

Het standaardgedrag maakt het lezen en verwijderen van een tweetrapsbewerking, waardoor het ook mogelijk is om toepassingen te ondersteunen die ontbrekende berichten niet kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing is voltooid verwerking van het bericht (of slaat het betrouwbaar voor toekomstige `delete_subscription_message()` verwerking), het voltooit de tweede fase van het ontvangstproces door aanroepen methode en het verstrekken van het bericht te worden verwijderd als een parameter. De `delete_subscription_message()` methode markeert het bericht als wordt verbruikt en verwijder het uit het abonnement.

Als `:peek_lock` de parameter is ingesteld op **false,** wordt het lezen en verwijderen van het bericht het eenvoudigste model en werkt het het beste voor scenario's waarin een toepassing kan tolereren dat een bericht niet wordt verwerkt wanneer er een fout optreedt. Overweeg een scenario waarin de consument het ontvangen verzoek uitgeeft en vervolgens crasht voordat het wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als wordt verbruikt, heeft de toepassing, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, het bericht gemist dat voorafgaand aan de crash is verbruikt.

In het volgende voorbeeld wordt uitgelegd hoe `receive_subscription_message()`berichten kunnen worden ontvangen en verwerkt met behulp van . Het voorbeeld ontvangt en verwijdert eerst `low-messages` een `:peek_lock` bericht uit het abonnement met behulp `high-messages` van ingesteld op `delete_subscription_message()` **false,** dan ontvangt het een ander bericht van de en vervolgens verwijdert het bericht met behulp van:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangertoepassing het bericht om de een of `unlock_subscription_message()` andere reden niet kan verwerken, kan het de methode aanroepen op het **azure::ServiceBusService-object.** Het zorgt ervoor dat Service Bus het bericht binnen het abonnement ontgrendelt en beschikbaar maakt om opnieuw te worden ontvangen, hetzij door dezelfde verbruikende toepassing of door een andere verbruikende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat is vergrendeld in het abonnement, en als de toepassing het bericht niet verwerkt voordat de vergrendelingstime-out verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en beschikbaar te maken om opnieuw te worden ontvangen.

In het geval dat de toepassing crasht `delete_subscription_message()` na het verwerken van het bericht, maar voordat de methode wordt aangeroepen, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Het wordt vaak *ten minste eenmaal verwerkt*genoemd; dat wil zeggen, elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Deze logica wordt vaak `message_id` bereikt met behulp van de eigenschap van het bericht, dat constant blijft over leveringspogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Onderwerpen en abonnementen zijn persistent, tenzij de [eigenschap AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) is ingesteld. Ze kunnen worden verwijderd via de [Azure-portal][Azure portal] of programmatisch. In het volgende voorbeeld wordt uitgelegd `test-topic`hoe u het onderwerp met de naam verwijdert.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. In de volgende code wordt uitgelegd `high-messages` hoe `test-topic` u het abonnement met de naam van het onderwerp verwijderen:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu je de basisprincipes van Service Bus-onderwerpen hebt geleerd, volg je deze links om meer te weten te komen.

* Zie [Wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md).
* API-naslaginformatie voor [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Ga naar de [Azure SDK for Ruby-repository](https://github.com/Azure/azure-sdk-for-ruby) op GitHub.

[Azure portal]: https://portal.azure.com
