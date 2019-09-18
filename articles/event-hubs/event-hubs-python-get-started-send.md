---
title: Gebeurtenissen verzenden of ontvangen met python-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht van het maken van een python-toepassing die gebeurtenissen naar Azure Event Hubs verzendt.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/16/2019
ms.author: shvija
ms.openlocfilehash: 5162c6359c4b6e6bdd53d2778ca247704e2f16be
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059147"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Gebeurtenissen verzenden naar of ontvangen van Event Hubs met behulp van python

Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelf studie wordt beschreven hoe u python-toepassingen maakt voor het verzenden van gebeurtenissen naar of het ontvangen van gebeurtenissen van een Event Hub. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Python 3.4 of hoger.
- Gebruik de [Azure Portal](https://portal.azure.com) om een naam ruimte van het type Event hubs te maken en de beheer referenties te verkrijgen die uw toepassing nodig heeft om met de Event hub te communiceren. Als u wilt een naamruimte en een event hub maken, volgt u de procedure in [in dit artikel](event-hubs-create.md). Vervolgens haalt u de waarde van de toegangs sleutel voor de Event Hub door de volgende instructies uit het artikel: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangssleutel in de code die u verderop in deze zelfstudie schrijft. De naam van de standaard sleutel is: **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Python-pakket installeren

Open een opdrachtprompt met Python in het pad voor het installeren van het Python-pakket voor Event Hubs, en voer vervolgens deze opdracht uit: 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Gebeurtenissen verzenden

> [!NOTE]
> Deze code in deze sectie is voor de huidige stabiele versie (1.3.1) van Event Hubs SDK. Als u zoekt naar de voorbeeld code die gebruikmaakt van de preview-versie van de SDK, raadpleegt u [Deze pagina](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

### <a name="create-a-python-script-to-send-events"></a>Maken van een Python-script voor het verzenden van gebeurtenissen

Maak vervolgens een Python-toepassing die gebeurtenissen naar een event hub verzendt:

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maken van een script met de naam **send.py**. Met dit script verzonden 100 gebeurtenissen naar uw event hub.
3. Plak de volgende code in send.py, vervang de waarden van de adres-, gebruikers- en -sleutel door de waarden die u hebt verkregen via de Azure portal in de vorige sectie: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://<EVENTHUBS NAMESPACE NAME>.servicebus.windows.net/<EVENTHUB NAME>"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "<SHARED ACCESS KEY FOR THE EVENT HUBS NAMESPACE>"

try:
    if not ADDRESS:
        raise ValueError("No EventHubs URL supplied.")

    # Create Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    sender = client.add_sender(partition="0")
    client.run()
    try:
        start_time = time.time()
        for i in range(100):
            print("Sending message: {}".format(i))
            message = "Message {}".format(i)
            sender.send(EventData(message))
    except:
        raise
    finally:
        end_time = time.time()
        client.stop()
        run_time = end_time - start_time
        logger.info("Runtime: {} seconds".format(run_time))

except KeyboardInterrupt:
    pass
```

### <a name="run-application-to-send-events"></a>Voer de toepassing voor het verzenden van gebeurtenissen

Voer het script uit, open een opdrachtprompt met Python in het pad en voer vervolgens deze opdracht uit:

```bash
start python send.py
```

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="receive-events"></a>Gebeurtenissen ontvangen

### <a name="create-a-python-script-to-receive-events"></a>Maken van een Python-script voor het ontvangen van gebeurtenissen

Maak vervolgens een Python-toepassing die gebeurtenissen vanaf een event hub ontvangt:

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maken van een script met de naam **recv.py**.
3. Plak de volgende code in recv.py, vervang de waarden van de adres-, gebruikers- en -sleutel door de waarden die u hebt verkregen via de Azure portal in de vorige sectie: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://<EVENTHUBS NAMESPACE NAME>.servicebus.windows.net/<EVENTHUB NAME>"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "<SHARED ACCESS KEY FOR THE EVENT HUBS NAMESPACE>"

CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(
        CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
        total += 1

    end_time = time.time()
    client.stop()
    run_time = end_time - start_time
    print("Received {} messages in {} seconds".format(total, run_time))

except KeyboardInterrupt:
    pass
finally:
    client.stop()
```

### <a name="receive-events"></a>Gebeurtenissen ontvangen

Voer het script uit, open een opdrachtprompt met Python in het pad en voer vervolgens deze opdracht uit:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

