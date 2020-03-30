---
title: Gebeurtenissen verzenden of ontvangen vanuit Azure Event Hubs met Python (oud)
description: Deze walkthrough laat zien hoe u Python-scripts maken en uitvoeren die gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van het oude azure-eventhub versie 1-pakket.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162596"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Snelstart: gebeurtenissen verzenden en ontvangen met Gebeurtenishubs met Python (azure-eventhub versie 1)
Deze quickstart laat zien hoe u gebeurtenissen verzendt naar en ontvangt vanuit een gebeurtenishub met het **Azure-Eventhub-versie 1 Python-pakket.** 

> [!WARNING]
> Deze quickstart maakt gebruik van het oude azure-eventhub versie 1-pakket. Zie [Gebeurtenissen verzenden en ontvangen met azure-eventhub versie 5](get-started-python-send-v2.md)voor een snelle start die de nieuwste versie **5** van het pakket gebruikt. Als u uw toepassing wilt verplaatsen van het oude pakket naar het nieuwe pakket, raadpleegt u de [handleiding om te migreren van azure-eventhub versie 1 naar versie 5.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
 

## <a name="prerequisites"></a>Vereisten
Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- Python 3.4 of `pip` hoger, met geïnstalleerd en bijgewerkt.
- Het Python-pakket voor gebeurtenishubs. Als u het pakket wilt installeren, voert u deze opdracht uit in een opdrachtprompt met Python op zijn pad: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download vervolgens de waarde van de toegangssleutel voor de gebeurtenishub door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangssleutel in de code die u later in deze quickstart schrijft. De standaardsleutelnaam is: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Gebeurtenissen verzenden

Ga als lid van het werk om een Python-toepassing te maken die gebeurtenissen naar een gebeurtenishub verzendt:

> [!NOTE]
> In plaats van de quickstart te doorlopen, u de [voorbeeld-apps](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) downloaden en uitvoeren van GitHub. Vervang `EventHubConnectionString` de `EventHubName` tekenreeksen door de waarden van de gebeurtenishub.

1. Open je favoriete Python-editor, zoals [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een nieuw bestand met de naam *send.py*. Dit script stuurt 100 gebeurtenissen naar uw gebeurtenishub.
3. Plak de volgende code in *send.py,* vervang de \<naamruimte \<van gebeurtenishubs \<>, eventhub>, AccessKeyName> en \<primaire sleutelwaarde> met uw waarden: 
   
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

Voer de opdracht uit om het script uit te voeren in de map waar u *send.py*hebt opgeslagen:

```cmd
start python send.py
```

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="receive-events"></a>Gebeurtenissen ontvangen

Ga als lid van het werk om een Python-toepassing te maken die gebeurtenissen ontvangt van een gebeurtenishub:

1. Maak in uw Python-editor een bestand met de naam *recv.py*.
2. Plak de volgende code in *recv.py,* vervang de \<naamruimte \<van gebeurtenishubs \<>, eventhub>, AccessKeyName> en \<primaire waarde> met uw waarden: 
   
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

Voer de opdracht uit om het script uit te voeren in de map waar u *recv.py*hebt opgeslagen:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over gebeurtenishubs:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

