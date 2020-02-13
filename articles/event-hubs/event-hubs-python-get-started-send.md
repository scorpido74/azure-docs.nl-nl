---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met python (oud)
description: In dit scenario ziet u hoe u python-scripts maakt en uitvoert waarmee gebeurtenissen worden verzonden naar of ontvangen van Azure Event Hubs met behulp van het oude pakket voor Azure-eventhub versie 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162596"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Quick Start: gebeurtenissen verzenden en ontvangen met Event Hubs met behulp van python (Azure-eventhub versie 1)
In deze Quick start ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het python-pakket van **Azure-eventhub versie 1** . 

> [!WARNING]
> Deze Snelstartgids maakt gebruik van het oude pakket voor Azure-eventhub versie 1. Zie [gebeurtenissen verzenden en ontvangen met Azure-eventhub versie 5](get-started-python-send-v2.md)voor een Snelstartgids die gebruikmaakt van de meest recente **versie 5** van het pakket. Raadpleeg de [hand leiding voor het migreren van Azure-eventhub versie 1 naar versie 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)als u uw toepassing wilt verplaatsen van het oude pakket naar een nieuwe.
 

## <a name="prerequisites"></a>Vereisten
Als u niet bekend bent met Azure Event Hubs, raadpleegt u [Event hubs Overview](event-hubs-about.md) voordat u deze Snelstartgids. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure abonnement**. Als u Azure-Services, met inbegrip van Azure Event Hubs, wilt gebruiken, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/free/) of de voor delen van uw MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Python 3,4 of hoger, met `pip` geïnstalleerd en bijgewerkt.
- Het python-pakket voor Event Hubs. Als u het pakket wilt installeren, voert u deze opdracht uit in een opdracht prompt met python in het pad: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Vervolgens haalt u de waarde van de toegangs sleutel voor de Event Hub door de volgende instructies uit het artikel: [get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangs sleutel in de code die u verderop in deze Quick Start schrijft. De naam van de standaard sleutel is: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Gebeurtenissen verzenden

Een python-toepassing maken die gebeurtenissen naar een Event Hub verzendt:

> [!NOTE]
> In plaats van de Snelstartgids te gebruiken, kunt u de voor [beeld-apps](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) downloaden en uitvoeren vanuit github. Vervang de `EventHubConnectionString` en `EventHubName` teken reeksen door uw Event Hub waarden.

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

