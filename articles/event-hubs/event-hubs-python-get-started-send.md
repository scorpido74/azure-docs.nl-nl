---
title: Gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van Python (verouderd)
description: In dit overzicht ziet u hoe u Python-scripts kunt maken en uitvoeren waarmee gebeurtenissen worden verzonden naar of ontvangen van Azure Event Hubs met behulp van het oude pakket azure-eventhub versie 1.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 4ce53d2d82a00f98dbbd538bd7a61da9ba44e832
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314469"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Quickstart: Gebeurtenissen verzenden en ontvangen met Event Hubs met behulp van Python (azure-eventhub versie 1)
In deze quickstart ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het Python-pakket **azure-eventhub versie 1**. 

> [!WARNING]
> In deze quickstart wordt het oude pakket azure-eventhub versie 1 gebruikt. Zie [Gebeurtenissen verzenden en ontvangen met behulp van azure-eventhub versie 5](get-started-python-send-v2.md) voor een quickstart waarin het nieuwste pakket azure-eventhub **versie 5** wordt gebruikt. Als u uw toepassing van het oude naar een nieuw pakket wilt verplaatsen, raadpleegt u de [Handleiding voor het migreren van azure-eventhub versie 1 naar versie 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Python 3.4 of hoger, waarbij `pip` is geïnstalleerd en bijgewerkt.
- Het Python-pakket voor Event Hubs. Als u het pakket wilt installeren, voert u deze opdracht uit in een opdrachtprompt met Python in het pad: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Een Event Hubs-naamruimte en een Event Hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Haal vervolgens de waarde van de toegangssleutel voor de Event Hub op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangssleutel in de code die u verderop in deze quickstart schrijft. De standaardsleutelnaam is: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Gebeurtenissen verzenden

Een Python-toepassing maken die gebeurtenissen naar een Event Hub verzendt:

> [!NOTE]
> In plaats van de quickstart te gebruiken, kunt u de [voorbeeld-apps](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) downloaden en uitvoeren vanuit GitHub. Vervang de tekenreeksen `EventHubConnectionString` en `EventHubName` door uw Event Hub-waarden.

1. Open uw favoriete Python-editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een nieuw bestand met de naam *send.py*. Met dit script worden 100 gebeurtenissen naar uw Event Hub verzonden.
3. Plak de volgende code in *send.py*, waarbij u de Event Hubs \<namespace>, \<eventhub>, \<AccessKeyName> en \<primary key value> vervangt door uw waarden: 
   
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

Als u het script wilt uitvoeren, voert u de volgende opdracht uit in de map waar u *sender.py* hebt opgeslagen:

```cmd
start python send.py
```

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="receive-events"></a>Gebeurtenissen ontvangen

Een Python-toepassing maken die gebeurtenissen ontvangt van een Event Hub:

1. Maak in uw Python-editor een bestand met de naam *recv.py*.
2. Plak de volgende code in *recv.py*, waarbij u de Event Hubs \<namespace>, \<eventhub>, \<AccessKeyName> en \<primary key value> vervangt door uw waarden: 
   
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

Als u het script wilt uitvoeren, voert u de volgende opdracht uit in de map waar u *recv.py* hebt opgeslagen:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

