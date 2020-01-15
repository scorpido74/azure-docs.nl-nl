---
title: 'Quick Start: gebeurtenissen verzenden en ontvangen met python-Azure Event Hubs'
description: 'Snelstartgids: in dit overzicht ziet u hoe u python-scripts maakt en uitvoert waarmee gebeurtenissen worden verzonden naar of ontvangen van Azure Event Hubs.'
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/08/2020
ms.author: shvija
ms.openlocfilehash: c4fa9e6038f4007246552610f537825f9def92a8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75939958"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>Snelstartgids: gebeurtenissen verzenden en ontvangen met Event Hubs met behulp van python

Azure Event Hubs is een Big data streaming-platform en Event opname-service waarmee miljoenen gebeurtenissen per seconde kunnen worden ontvangen en verwerkt. Event Hubs kunt gebeurtenissen, gegevens of telemetrie van gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden getransformeerd en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [azure Event hubs](event-hubs-about.md) en [functies en terminologie in azure Event hubs](event-hubs-features.md)voor meer informatie over Event hubs.

Deze Quick Start laat zien hoe u python-toepassingen maakt waarmee gebeurtenissen worden verzonden naar en ontvangen van een Event Hub. 

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van versie 1 van de Azure Event Hubs python SDK. Als u geen ervaring hebt met Azure Event Hubs, gebruikt u versie 5 van de python-SDK. Zie [dit artikel](get-started-python-send-v2.md)voor een Snelstartgids die versie 5 van de PYTHON-SDK gebruikt. Zie de [migratie handleiding](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)voor informatie over het migreren van bestaande code van versie 1 naar versie 5.


> [!NOTE]
> In plaats van de Snelstartgids te gebruiken, kunt u de voor [beeld-apps](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) downloaden en uitvoeren vanuit github. Vervang de `EventHubConnectionString` en `EventHubName` teken reeksen door uw Event Hub waarden. 

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Een actieve Event Hubs naam ruimte en Event Hub, gemaakt door de instructies te volgen op [Quick Start: een event hub maken met Azure Portal](event-hubs-create.md). Noteer de naam ruimte en de namen van de Event Hub die u later in dit overzicht kunt gebruiken. 
- De naam van de gedeelde toegangs sleutel en de waarde van de primaire sleutel voor uw Event Hubs naam ruimte. Haal de naam en waarde van de toegangs sleutel op door de instructies te volgen op [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). De standaard naam van de toegangs sleutel is **RootManageSharedAccessKey**. Kopieer de sleutel naam en de primaire-sleutel waarde die u later in dit overzicht wilt gebruiken. 
- Python 3,4 of hoger, met `pip` geïnstalleerd en bijgewerkt.
- Het python-pakket voor Event Hubs. Als u het pakket wilt installeren, voert u deze opdracht uit in een opdracht prompt met python in het pad: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > De code in deze Quick Start maakt gebruik van de huidige stabiele versie 1.3.1 van de Event Hubs SDK. Zie [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples)voor voorbeeld code die gebruikmaakt van de preview-versie van de SDK.

## <a name="send-events"></a>Gebeurtenissen verzenden

Een python-toepassing maken die gebeurtenissen naar een Event Hub verzendt:

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maak een nieuw bestand met de naam *Send.py*. Met dit script verzonden 100 gebeurtenissen naar uw event hub.
3. Plak de volgende code in *Send.py*en vervang de Event hubs \<naam ruimte >, \<eventhub >, \<accesskeynaam > en \<primaire sleutel waarde > met uw waarden: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
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
   
4. Sla het bestand op. 

Als u het script wilt uitvoeren, voert u de volgende opdracht uit in de map waarin u *Send.py*hebt opgeslagen:

```cmd
start python send.py
```

Gefeliciteerd. U hebt nu berichten verzonden naar een Event Hub.

## <a name="receive-events"></a>Gebeurtenissen ontvangen

Een python-toepassing maken die gebeurtenissen ontvangt van een Event Hub:

1. Maak een bestand met de naam *recv.py*in uw python-editor.
2. Plak de volgende code in *recv.py*en vervang de Event hubs \<naam ruimte >, \<eventhub >, \<accesskeynaam > en \<primaire sleutel waarde > met uw waarden: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
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
   
4. Sla het bestand op.

Als u het script wilt uitvoeren, voert u de volgende opdracht uit in de map waarin u *recv.py*hebt opgeslagen:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

