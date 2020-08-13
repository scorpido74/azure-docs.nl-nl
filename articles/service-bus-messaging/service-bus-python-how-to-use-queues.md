---
title: 'Quickstart: Azure Service Bus-wachtrijen gebruiken met Python'
description: In dit artikel wordt beschreven hoe u met Python berichten maakt en deze verzendt naar en ontvangt van Azure Service Bus-wachtrijen.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 55ed71c6947c70ac797656f2f18cf71dd8aaae9d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852477"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Quickstart: Azure Service Bus-wachtrijen gebruiken met Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In dit artikel wordt beschreven hoe u met Python berichten maakt en deze verzendt naar en ontvangt van Azure Service Bus-wachtrijen. 

Zie [Service Bus-bibliotheken voor Python](/python/api/overview/azure/servicebus?view=azure-python) voor meer informatie over de Azure Service Bus-bibliotheken voor Python.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Een Service Bus-naamruimte, gemaakt door de stappen in [Quickstart: Met Azure Portal een Service Bus-onderwerp en -abonnementen maken](service-bus-quickstart-topics-subscriptions-portal.md) uit te voeren. Kopieer de primaire verbindingstekenreeks in het scherm **Beleid voor gedeelde toegang**, zodat u deze later in dit artikel kunt gebruiken. 
- Python 3.4x of hoger, met het [Python Azure Service Bus][Python Azure Service Bus package]-pakket geïnstalleerd. Zie de [Python-installatiehandleiding](/azure/developer/python/azure-sdk-install) voor meer informatie. 

## <a name="create-a-queue"></a>Een wachtrij maken

Met een object **ServiceBusClient** kunt u wachtrijen gebruiken. Als u via programmacode toegang wilt krijgen tot Service Bus, voegt u de volgende regel toe bovenaan het Python-bestand:

```python
from azure.servicebus import ServiceBusClient
```

Voeg de volgende code toe om een object **ServiceBusClient** te maken. Vervang `<connectionstring>` door de waarde voor de primaire verbindingstekenreeks voor Service Bus. U vindt deze waarde onder **Beleid voor gedeelde toegang** in uw Service Bus-naamruimte in [Azure Portal][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

De volgende code maakt gebruik van de methode `create_queue` van het object **ServiceBusClient** om een wachtrij met de naam `taskqueue` met standaardinstellingen te maken:

```python
sb_client.create_queue("taskqueue")
```

U kunt opties gebruiken om de standaardinstellingen voor de wachtrij te overschrijven, zoals de levensduur van het bericht (TTL of Time to Live) of de maximumgrootte van het onderwerp. Met de volgende code wordt een wachtrij met de naam `taskqueue` gemaakt met een maximale wachtrijgrootte van 5 GB en TTL-waarde van 1 minuut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij

Om een bericht te verzenden naar een Service Bus-wachtrij, wordt door de app de methode `send` in het object **ServiceBusClient** aangeroepen. In het volgende codevoorbeeld wordt een wachtrijclient gemaakt en wordt een testbericht verzonden naar de wachtrij `taskqueue`. Vervang `<connectionstring>` door de waarde voor de primaire verbindingstekenreeks voor Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limieten en quota voor berichtgrootten

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten dat een wachtrij kan bevatten, maar er is een limiet voor de totale grootte van de berichten die de wachtrij kan bevatten. U kunt tijdens het maken van de wachtrij de wachtrijgrootte definiëren, met een bovengrens van 5 GB. 

Zie [Service Bus-quota][Service Bus quotas] voor meer informatie over quota.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

De wachtrijclient ontvangt berichten van een wachtrij met behulp van de methode `get_receiver` in het object **ServiceBusClient**. Met het volgende codevoorbeeld wordt een wachtrijclient gemaakt en wordt een bericht ontvangen van de wachtrij `taskqueue`. Vervang `<connectionstring>` door de waarde voor de primaire verbindingstekenreeks voor Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>De parameter peek_lock gebruiken

Met de optionele parameter `peek_lock` van `get_receiver` wordt bepaald of Service Bus berichten verwijdert uit de wachtrij wanneer ze worden gelezen. De standaardmodus voor het ontvangen van berichten is *PeekLock*, of `peek_lock` ingesteld op **Waar**. Hierbij worden berichten gelezen (peek) en vergrendeld zonder dat deze uit de wachtrij worden verwijderd. Elk bericht moet vervolgens expliciet worden voltooid om het uit de wachtrij te verwijderen.

Als u berichten uit de wachtrij wilt verwijderen wanneer ze worden gelezen, kunt u de parameter `peek_lock` van `get_receiver` instellen op **Onwaar**. Berichten verwijderen als onderdeel van de bewerking voor ontvangen is het eenvoudigste model, maar werkt alleen als de app toelaat dat berichten ontbreken als er een fout optreedt. Neem bijvoorbeeld een scenario waarin de verwerkende app een ontvangstaanvraag indient en vervolgens vastloopt voordat het bericht wordt verwerkt. Als het bericht bij ontvangst is verwijderd en de app opnieuw wordt gestart en opnieuw berichten gaat verwerken, heeft deze het bericht gemist dat het heeft ontvangen vóór het vastlopen.

Als uw app het niet toelaat dat berichten worden gemist, bestaat het ontvangen uit twee fasen. PeekLock zoekt naar het volgende bericht dat moet worden verwerkt, vergrendelt dit om te voorkomen dat andere verwerkende apps het ontvangen en retourneert het bericht vervolgens naar de app. Nadat het bericht is verwerkt of opgeslagen, voltooit de app de tweede fase van het ontvangstproces door de methode `complete` in het object **Message** aan te roepen.  Met de methode `complete` wordt het bericht gemarkeerd als verwerkt en wordt het uit de wachtrij verwijderd.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de app en onleesbare berichten afhandelen

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende app een bericht om de een of andere reden niet kan verwerken, kan deze de methode `unlock` in het object **Message** aanroepen. Service Bus ontgrendelt het bericht in de wachtrij en maakt het beschikbaar, zodat het opnieuw kan worden ontvangen, ofwel door dezelfde of door een andere verwerkende app.

Er is ook een time-out voor berichten die in de wachtrij zijn vergrendeld. Als een app een bericht niet kan verwerken voordat de time-out voor vergrendeling verloopt, bijvoorbeeld als de app vastloopt, dan ontgrendelt Service Bus automatisch het bericht en maakt het dit beschikbaar, zodat het weer kan worden ontvangen.

Als een app vastloopt na het verwerken van een bericht, maar voordat deze de methode `complete` aanroept, wordt het bericht opnieuw bij de app bezorgd wanneer deze opnieuw wordt gestart. Deze werkwijze wordt ook wel *Ten minste één keer verwerken* genoemd. Elk bericht wordt ten minste één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als uw situatie geen dubbele verwerking toelaat, kunt u de eigenschap **MessageId** van het bericht gebruiken, die constant blijft tijdens de bezorgingspogingen, om dubbele berichtbezorging te verwerken. 

> [!TIP]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunt u verbinding maken met een Service Bus-naamruimte en berichtenentiteiten eenvoudig beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren en de mogelijkheid om onderwerpen, wachtrijen, abonnementen, relayservices, notification hubs en event hubs te testen.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de basisprincipes van Service Bus-wachtrijen, kunt u [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] raadplegen voor meer informatie.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
