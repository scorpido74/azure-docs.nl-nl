---
title: 'Snelstartgids: Azure Service Bus wacht rijen gebruiken met python'
description: In dit artikel wordt beschreven hoe u python gebruikt om berichten te maken, te verzenden naar en te ontvangen van Azure Service Bus-wacht rijen.
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
ms.openlocfilehash: acb0b0e84804ecf6025e05590133dee9b0d54c48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80478650"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Snelstartgids: Azure Service Bus wacht rijen gebruiken met python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In dit artikel wordt beschreven hoe u python gebruikt om berichten te maken, te verzenden naar en te ontvangen van Azure Service Bus-wacht rijen. 

Zie [Service Bus libraries voor python](/python/api/overview/azure/servicebus?view=azure-python)voor meer informatie over de python Azure service bus-bibliotheken.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U kunt de [voor delen van uw Visual Studio-of MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Een Service Bus naam ruimte, gemaakt door de stappen te volgen op [Quick Start: gebruik de Azure Portal om een service bus onderwerp en abonnementen te maken](service-bus-quickstart-topics-subscriptions-portal.md). Kopieer de primaire connection string uit het scherm voor het **beleid voor gedeelde toegang** om later in dit artikel te gebruiken. 
- Python 3.4 x of hoger, met het [Python Azure service bus][Python Azure Service Bus package] -pakket geïnstalleerd. Raadpleeg de [installatie handleiding voor python](/azure/developer/python/azure-sdk-install)voor meer informatie. 

## <a name="create-a-queue"></a>Een wachtrij maken

Met een **ServiceBusClient** -object kunt u met wacht rijen werken. Als u toegang wilt krijgen tot Service Bus, voegt u de volgende regel toe aan de bovenkant van uw python-bestand:

```python
from azure.servicebus import ServiceBusClient
```

Voeg de volgende code toe om een **ServiceBusClient** -object te maken. Vervang `<connectionstring>` door uw Service Bus primaire Connection String waarde. U kunt deze waarde vinden onder **beleid voor gedeelde toegang** in uw service bus naam ruimte in de [Azure Portal][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

De volgende code gebruikt de `create_queue` methode van de **ServiceBusClient** om een wachtrij te maken `taskqueue` met de naam standaard instellingen:

```python
sb_client.create_queue("taskqueue")
```

U kunt opties gebruiken om standaard instellingen voor de wachtrij te overschrijven, zoals de TTL (time to Live) van het bericht of de maximum grootte van het onderwerp. `taskqueue` Met de volgende code wordt een wachtrij gemaakt met een maximale wachtrij grootte van 5 GB en een TTL-waarde van 1 minuut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij

Een toepassing roept de `send` methode aan in het **ServiceBusClient** -object om een bericht naar een service bus wachtrij te verzenden. In het volgende code voorbeeld wordt een wachtrij-client gemaakt en wordt een test `taskqueue` bericht naar de wachtrij verzonden. Vervang `<connectionstring>` door uw Service Bus primaire Connection String waarde. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limieten en quota voor de bericht grootte

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten dat een wachtrij kan bevatten, maar er is een kapje op de totale grootte van de berichten in de wachtrij. U kunt de grootte van de wachtrij tijdens het maken definiëren, met een bovengrens van 5 GB. 

Zie [Service Bus quota's][Service Bus quotas]voor meer informatie over quota's.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

De wachtrij-client ontvangt berichten van een wachtrij met behulp van de `get_receiver` methode voor het **ServiceBusClient** -object. In het volgende code voorbeeld wordt een wachtrij-client gemaakt en wordt een `taskqueue` bericht uit de wachtrij ontvangen. Vervang `<connectionstring>` door uw Service Bus primaire Connection String waarde. 

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

### <a name="use-the-peek_lock-parameter"></a>De para meter peek_lock gebruiken

De optionele `peek_lock` para meter `get_receiver` van bepaalt of Service Bus berichten uit de wachtrij verwijdert wanneer ze worden gelezen. De standaard modus voor het ontvangen van *PeekLock*berichten is PeekLock `peek_lock` of is ingesteld op **True**, waarmee berichten worden gelezen en vergrendeld zonder dat ze uit de wachtrij worden verwijderd. Elk bericht moet vervolgens expliciet worden voltooid om het uit de wachtrij te verwijderen.

Als u berichten uit de wachtrij wilt verwijderen als ze zijn gelezen, kunt u `peek_lock` de para `get_receiver` meter van op **Onwaar**instellen. Berichten verwijderen als onderdeel van de receive-bewerking is het eenvoudigste model, maar werkt alleen als de toepassing ontbrekende berichten kan verdragen als er een fout optreedt. Om dit gedrag te begrijpen, moet u rekening houden met een scenario waarin de consumer een aanvraag verzendt en vervolgens vastloopt voordat het proces wordt verwerkt. Als het bericht is verwijderd op het moment dat de toepassing opnieuw wordt gestart en opnieuw bezig is met het uitvoeren van berichten, heeft het bericht gemist dat het is ontvangen voordat het vastloopt.

Als uw toepassing gemiste berichten niet kan verdragen, ontvangt u een bewerking met twee fasen. PeekLock vindt het volgende bericht dat moet worden gebruikt, vergrendelt het om te voor komen dat andere gebruikers het ontvangen en retourneert het naar de toepassing. Nadat het bericht is verwerkt of opgeslagen, voltooit de toepassing de tweede fase van het ontvangst proces door de `complete` methode aan te roepen voor het **bericht** object.  Met `complete` de-methode wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Vastlopen van toepassingen en onleesbare berichten verwerken

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvanger een bericht om de een of andere reden niet kan verwerken, kan `unlock` de toepassing de-methode aanroepen in het **bericht** object. Service Bus ontgrendelt het bericht in de wachtrij en maakt het beschikbaar om het opnieuw te ontvangen, hetzij door dezelfde of een andere verbruiks toepassing.

Er is ook een time-out voor berichten die in de wachtrij zijn vergrendeld. Als een toepassing een bericht niet kan verwerken voordat de time-out voor de vergren deling is verlopen, bijvoorbeeld als de toepassing vastloopt, Service Bus het bericht automatisch ontgrendelen en wordt het beschikbaar om opnieuw te worden ontvangen.

Als een toepassing vastloopt na het verwerken van een bericht, `complete` maar voordat de methode wordt aangeroepen, wordt het bericht opnieuw aan de toepassing bezorgd wanneer het opnieuw wordt gestart. Dit gedrag wordt vaak een eenmalige *verwerking*genoemd. Elk bericht wordt ten minste één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als uw scenario dubbele verwerking niet kan verdragen, kunt u de eigenschap **MessageId** van het bericht gebruiken, dat constant blijft tijdens bezorgings pogingen, om dubbele bericht bezorging af te handelen. 

> [!TIP]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunt u verbinding maken met een Service Bus naam ruimte en eenvoudig bericht entiteiten beheren. Het hulp programma biedt geavanceerde functies, zoals de functionaliteit voor importeren/exporteren, en de mogelijkheid om onderwerpen, wacht rijen, abonnementen, relay-Services, Notification hubs en Event hubs te testen.

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Service Bus wacht rijen hebt geleerd, raadpleegt u [wacht rijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] voor meer informatie.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
