---
title: 'Zelf studie: Azure Service Bus wacht rijen gebruiken met python'
description: Meer informatie over het gebruik van Azure Service Bus wachtrijen van python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: 3561d7e3569b31698fbbc5f8cf2518d9fe1fc398
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331128"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Service Bus wachtrijen gebruiken met python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In deze zelf studie leert u hoe u python-toepassingen kunt maken om berichten te verzenden naar en berichten van een Service Bus wachtrij te ontvangen. 

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) van uw MSDN-abonnee activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in de [Azure Portal gebruiken om een service bus wachtrij artikel te maken](service-bus-quickstart-portal.md) .
    1. Lees het kort **overzicht** van service bus- **wacht rijen**. 
    2. Maak een Service Bus **naam ruimte**. 
    3. Haal de **Connection String**op. 

        > [!NOTE]
        > In deze zelf studie maakt u een **wachtrij** in de service bus naam ruimte met behulp van python. 
1. Python of het [python Azure service bus-pakket][Python Azure Service Bus package]installeren, raadpleegt u de [installatie handleiding voor python](/azure/python/python-sdk-azure-install). Bekijk [hier](/python/api/overview/azure/servicebus?view=azure-python)de volledige documentatie over service bus python SDK.

## <a name="create-a-queue"></a>Een wachtrij maken
Met het **ServiceBusClient** -object kunt u met wacht rijen werken. Voeg de volgende code toe aan de bovenkant van een python-bestand waarin u programmatisch toegang wilt krijgen Service Bus:

```python
from azure.servicebus import ServiceBusClient
```

Met de volgende code wordt een **ServiceBusClient** -object gemaakt. Vervang `<CONNECTION STRING>` door uw servicebus Connections Tring.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

De waarden voor de SAS-sleutel naam en-waarde zijn te vinden in de [Azure Portal][Azure portal] verbindings gegevens, of in het deel venster Visual Studio- **Eigenschappen** bij het selecteren van de Service Bus naam ruimte in Server Explorer (zoals weer gegeven in de vorige sectie).

```python
sb_client.create_queue("taskqueue")
```

De methode `create_queue` biedt ook ondersteuning voor extra opties, waarmee u standaard instellingen voor de wachtrij, zoals de TTL (time to Live) of de maximale wachtrij grootte, kunt overschrijven. In het volgende voor beeld wordt de maximale wachtrij grootte ingesteld op 5 GB en de TTL-waarde op 1 minuut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Zie [Azure service bus python-documentatie](/python/api/overview/azure/servicebus?view=azure-python)voor meer informatie.

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u een bericht wilt verzenden naar een Service Bus wachtrij, roept uw toepassing de `send`-methode aan voor het object `ServiceBusClient`.

In het volgende voor beeld ziet u hoe u een test bericht naar de wachtrij met de naam `taskqueue` verzendt met behulp van `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Zie [Service Bus quota's][Service Bus quotas]voor meer informatie over quota's.

Zie [Azure service bus python-documentatie](/python/api/overview/azure/servicebus?view=azure-python)voor meer informatie.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Er worden berichten van een wachtrij ontvangen met behulp van de `get_receiver`-methode voor het `ServiceBusService`-object:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Zie [Azure service bus python-documentatie](/python/api/overview/azure/servicebus?view=azure-python)voor meer informatie.


Berichten worden uit de wachtrij verwijderd wanneer ze worden gelezen wanneer de para meter `peek_lock` is ingesteld op **Onwaar**. U kunt het bericht lezen (bekijken) en dit vergren delen zonder het uit de wachtrij te verwijderen door de para meter `peek_lock` in te stellen op **True**.

Het gedrag van het lezen en verwijderen van het bericht als onderdeel van de receive-bewerking is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing een bericht niet verwerkt in het geval van een fout. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, wordt het bericht dat voor het vastlopen is verbruikt, overgeslagen wanneer de toepassing opnieuw wordt gestart en opnieuw bezig is met het uitvoeren van berichten.

Als de para meter `peek_lock` is ingesteld op **True**, wordt de ontvangst een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of op betrouw bare wijze is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door de methode **Delete** aan te roepen voor het **bericht** object. Met de methode **Delete** wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een receiver-toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode voor het **ontgrendelen** van het **bericht object worden** aangeroepen. Dit leidt ertoe dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), wordt het bericht automatisch door Service Bus ontgrendeld en wordt het beschikbaar om opnieuw te worden ontvangen.

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de methode **Delete** is aangeroepen, wordt het bericht opnieuw aan de toepassing geleverd wanneer het opnieuw wordt gestart. Dit wordt vaak **ten minste eenmaal verwerken**genoemd, dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de **MessageId**-eigenschap van het bericht dat gelijk blijft bij meerdere bezorgingspogingen.

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Service Bus wacht rijen hebt geleerd, raadpleegt u de volgende artikelen voor meer informatie.

* [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

