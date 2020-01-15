---
title: Gebeurtenissen verzenden of ontvangen met python-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht van het maken van een python-toepassing die gebeurtenissen naar Azure Event Hubs verzendt.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: a137f274744a6acec22d036f9f4c5c4a5174cc14
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942592"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Gebeurtenissen verzenden naar of ontvangen van Event Hubs met behulp van python

Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten, verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden getransformeerd en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelf studie wordt beschreven hoe u python-toepassingen maakt voor het verzenden van gebeurtenissen naar of het ontvangen van gebeurtenissen van een Event Hub. 

> [!IMPORTANT]
> In deze Quick Start wordt versie 5 van de Azure Event Hubs python-SDK gebruikt. Zie [dit artikel](event-hubs-python-get-started-send.md)voor een snelle start die gebruikmaakt van de oude versie 1 van de PYTHON-SDK. Als u versie 1 van de SDK gebruikt, raden we u aan uw code te migreren naar de nieuwste versie. Zie de [migratie handleiding](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)voor meer informatie.


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Een actieve Event Hubs naam ruimte en Event Hub, gemaakt door de instructies te volgen op [Quick Start: een event hub maken met Azure Portal](event-hubs-create.md). Noteer de naam ruimte en de namen van de Event Hub die u later in dit overzicht kunt gebruiken. 
- De naam van de gedeelde toegangs sleutel en de waarde van de primaire sleutel voor uw Event Hubs naam ruimte. Haal de naam en waarde van de toegangs sleutel op door de instructies te volgen op [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). De standaard naam van de toegangs sleutel is **RootManageSharedAccessKey**. Kopieer de sleutel naam en de primaire-sleutel waarde die u later in dit overzicht wilt gebruiken. 
- Python 2,7 en 3,5 of hoger, met `pip` geïnstalleerd en bijgewerkt.
- Het python-pakket voor Event Hubs. Als u het pakket wilt installeren, voert u deze opdracht uit in een opdracht prompt met python in het pad: 
    
    ```cmd
    pip install azure-eventhub
    ```

    Installeer dit pakket voor het ontvangen van de gebeurtenissen met behulp van een Azure Blob-opslag als de opslag plaats van het controle punt. 

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie maakt u een python-script voor het verzenden van gebeurtenissen naar de Event Hub die u eerder hebt gemaakt. 

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maken van een script met de naam **send.py**. Met dit script wordt een batch gebeurtenissen verzonden naar de Event Hub die u eerder hebt gemaakt. 
3. Plak de volgende code in send.py. Zie de opmerkingen bij de code voor meer informatie. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData
    
    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and 
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()
    
            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))
    
            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)
    
    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())
    
    ```

    > [!NOTE]
    > Voor de volledige bron code met zeer nuttige opmerkingen raadpleegt u [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)

## <a name="receive-events"></a>Gebeurtenissen ontvangen
Deze Snelstartgids maakt gebruik van een Azure Blob Storage als een controlepunt opslag. De controlepunt opslag wordt gebruikt voor het persistent maken van controle punten (laatste lees positie).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Een Azure Storage en een BLOB-container maken
Volg deze stappen om een Azure Storage account te maken in een BLOB-container. 

1. [Een Azure Storage-account maken](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [Een BLOB-container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De connection string ophalen voor het opslag account](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Noteer connection string en de naam van de container. U gebruikt deze in de receive-code. 


### <a name="create-python-script-to-receive-events"></a>Python-script maken voor het ontvangen van gebeurtenissen

In deze sectie maakt u een python-script om gebeurtenissen van uw Event Hub te ontvangen:

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maken van een script met de naam **recv.py**.
3. Plak de volgende code in recv.py. Zie de opmerkingen bij de code voor meer informatie. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore
    
    
    async def on_event(partition_context, event):
        # print the event data 
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))
    
        # update the checkpoint so that the program doesn't read the events 
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)
    
    async def main():
        # create an Azure blob checkpoint store to store the checkpoints 
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")
    
        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)
    
    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Voor de volledige bron code met zeer nuttige opmerkingen raadpleegt u [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


### <a name="run-the-receiver-app"></a>De ontvanger-app uitvoeren

Voer het script uit, open een opdrachtprompt met Python in het pad en voer vervolgens deze opdracht uit:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>De Sender-app uitvoeren

Voer het script uit, open een opdrachtprompt met Python in het pad en voer vervolgens deze opdracht uit:

```bash
python send.py
```

Als het goed is, ziet u de berichten die zijn verzonden naar de Event Hub in het Receive-venster. 


## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u gebeurtenissen asynchroon verzonden en ontvangen. Zie voor beelden op [deze locatie](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)voor meer informatie over hoe u gebeurtenissen synchroon kunt verzenden en ontvangen.

U kunt alle voor beelden (zowel synchroniseren als async) vinden op [het github.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) 
