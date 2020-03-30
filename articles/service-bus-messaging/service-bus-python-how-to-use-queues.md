---
title: 'Snelstart: Azure Service Bus-wachtrijen gebruiken met Python'
description: In dit artikel ziet u hoe u Python gebruiken om berichten te maken, te verzenden naar en berichten te ontvangen vanuit azure servicebuswachtrijen.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: aa9ffc48d9b1374fa510f450bab2c66641421446
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76773497"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Snelstart: Azure Service Bus-wachtrijen gebruiken met Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In dit artikel ziet u hoe u Python gebruiken om berichten te maken, te verzenden naar en berichten te ontvangen vanuit azure servicebuswachtrijen. 

Zie [ServiceBus-bibliotheken voor Python voor](/python/api/overview/azure/servicebus?view=azure-python)meer informatie over de Python Azure Service Bus-bibliotheken.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U uw [Visual Studio- of MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Een naamruimte voor servicebus die is gemaakt door de stappen te volgen bij [Quickstart: gebruik de Azure-portal om een servicebusonderwerp en -abonnementen te maken.](service-bus-quickstart-topics-subscriptions-portal.md) Kopieer de tekenreeks voor de primaire verbinding vanuit het scherm **Beleid voor gedeelde toegang** om later in dit artikel te gebruiken. 
- Python 3,4x of hoger, met het [Python Azure Service Bus-pakket][Python Azure Service Bus package] geïnstalleerd. Zie de [Python-installatiehandleiding](/azure/python/python-sdk-azure-install)voor meer informatie. 

## <a name="create-a-queue"></a>Een wachtrij maken

Met een **ServiceBusClient-object** u werken met wachtrijen. Als u programmatisch toegang wilt krijgen tot Service Bus, voegt u de volgende regel toe boven aan uw Python-bestand:

```python
from azure.servicebus import ServiceBusClient
```

Voeg de volgende code toe om een **ServiceBusClient-object** te maken. Vervang `<connectionstring>` door de primaire verbindingstekenreekswaarde van de ServiceBus. U deze waarde vinden onder **Beleid voor gedeelde toegang** in de naamruimte van uw ServiceBus in de [Azure-portal.][Azure portal]

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

De volgende code `create_queue` gebruikt de methode van de `taskqueue` **ServiceBusClient** om een wachtrij met de naam standaardinstellingen te maken:

```python
sb_client.create_queue("taskqueue")
```

U opties gebruiken om standaardwachtrijinstellingen te overschrijven, zoals berichttijd om te leven (TTL) of maximale onderwerpgrootte. Met de volgende code `taskqueue` wordt een wachtrij aangeroepen met een maximale wachtrijgrootte van 5 GB en ttl-waarde van 1 minuut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij

Als u een bericht naar een wachtrij `send` voor servicebus wilt verzenden, roept een toepassing de methode aan op het **Object ServiceBusClient.** In het volgende codevoorbeeld wordt een wachtrijclient `taskqueue` gesmaakt en wordt een testbericht naar de wachtrij verzonden. Vervang `<connectionstring>` door de primaire verbindingstekenreekswaarde van de ServiceBus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limieten en quota voor berichtgrootte

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet aan het aantal berichten dat een wachtrij kan bevatten, maar er is een limiet op de totale grootte van de berichten die de wachtrij bevat. U de grootte van de wachtrij definiëren bij het maken van de tijd, met een bovengrens van 5 GB. 

Zie [ServiceBus-quota voor][Service Bus quotas]meer informatie over quota.

## <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij

De wachtrijclient ontvangt berichten uit een `get_receiver` wachtrij met behulp van de methode op het **object ServiceBusClient.** In het volgende codevoorbeeld wordt een wachtrijclient gesmaakt en ontvangt u een bericht uit de `taskqueue` wachtrij. Vervang `<connectionstring>` door de primaire verbindingstekenreekswaarde van de ServiceBus. 

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

De `peek_lock` optionele `get_receiver` parameter bepaalt of Service Bus berichten uit de wachtrij verwijdert terwijl ze worden gelezen. De standaardmodus voor het ontvangen `peek_lock` van berichten is *PeekLock*of ingesteld op **True,** die berichten leest (gluurt) en vergrendelt zonder ze uit de wachtrij te verwijderen. Elk bericht moet vervolgens expliciet worden ingevuld om het uit de wachtrij te verwijderen.

Als u berichten uit de wachtrij wilt verwijderen terwijl `peek_lock` ze `get_receiver` worden gelezen, u de parameter instellen op **False**. Het verwijderen van berichten als onderdeel van de ontvangstbewerking is het eenvoudigste model, maar werkt alleen als de toepassing ontbrekende berichten kan tolereren als er een fout optreedt. Als u dit gedrag wilt begrijpen, moet u een scenario overwegen waarin de consument een ontvangstverzoek uitgeeft en vervolgens vastloopt voordat deze wordt verwerkt. Als het bericht is verwijderd nadat het is ontvangen, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, heeft het het bericht gemist dat het vóór de crash heeft ontvangen.

Als uw toepassing gemiste berichten niet kan tolereren, is ontvangen een bewerking in twee fasen. PeekLock vindt het volgende bericht te worden verbruikt, vergrendelt het om te voorkomen dat andere consumenten het ontvangen en stuurt het terug naar de toepassing. Nadat de toepassing het bericht hebt verwerkt of opgeslagen, voltooit `complete` de toepassing de tweede fase van het ontvangstproces door de methode op het **object Bericht** aan te roepen.  De `complete` methode markeert het bericht als wordt verbruikt en verwijdert het uit de wachtrij.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Toepassingscrashes en onleesbare berichten verwerken

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangertoepassing om de een of andere reden `unlock` een bericht niet kan verwerken, kan deze de methode op het object **Message** aanroepen. Service Bus ontgrendelt het bericht in de wachtrij en maakt het beschikbaar om opnieuw te worden ontvangen, hetzij door dezelfde of een andere verbruikende toepassing.

Er is ook een time-out voor berichten die zijn vergrendeld in de wachtrij. Als een toepassing een bericht niet verwerkt voordat de vergrendelingstime-out verloopt, bijvoorbeeld als de toepassing vastloopt, ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te worden ontvangen.

Als een toepassing vastloopt na het `complete` verwerken van een bericht, maar voordat de methode wordt aangeroepen, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Dit gedrag wordt vaak *At-least-once Processing*genoemd. Elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als uw scenario dubbele verwerking niet kan tolereren, u de eigenschap **MessageId** van het bericht, dat constant blijft bij leveringspogingen, gebruiken om dubbele berichtbezorging af te handelen. 

> [!TIP]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met Service Bus Explorer u verbinding maken met een naamruimte van een ServiceBus en eenvoudig berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/exportfunctionaliteit en de mogelijkheid om onderwerpen, wachtrijen, abonnementen, relayservices, meldingshubs en gebeurtenishubs te testen.

## <a name="next-steps"></a>Volgende stappen

Nu u de basisbeginselen van wachtrijen voor servicebussen hebt geleerd, raadpleegt u [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] voor meer informatie.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
