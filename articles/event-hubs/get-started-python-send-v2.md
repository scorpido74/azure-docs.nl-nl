---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met python (recentste)
description: Dit artikel bevat een overzicht van het maken van een python-toepassing die gebeurtenissen naar/van Azure Event Hubs verzendt/ontvangt met behulp van het meest recente pakket met Azure-eventhub versie 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906352"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Gebeurtenissen verzenden naar of ontvangen gebeurtenissen van Event hubs met behulp van python (Azure-eventhub versie 5)

Azure Event Hubs is een big data streaming platform en een service voor gebeurtenis opname waarmee miljoenen gebeurtenissen per seconde kunnen worden ontvangen en verwerkt. Event hubs kan gebeurtenissen, gegevens of telemetrie verwerken en opslaan die worden geproduceerd door gedistribueerde software en apparaten. Gegevens die naar een Event Hub worden verzonden, kunnen worden getransformeerd en opgeslagen met behulp van een real-time analyse provider of batch-en opslag adapters. Zie [Event hubs-overzicht](event-hubs-about.md) en [Event hubs-functies](event-hubs-features.md)voor meer informatie.

In deze Quick Start wordt beschreven hoe u python-toepassingen maakt die gebeurtenissen kunnen verzenden naar of ontvangen van een Event Hub.

> [!IMPORTANT]
> In deze Quick Start wordt versie 5 van de Azure Event Hubs python-SDK gebruikt. Zie [dit artikel](event-hubs-python-get-started-send.md)voor een snelle start die gebruikmaakt van versie 1 van de PYTHON-SDK. 

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Een actieve Event Hubs naam ruimte en Event Hub. Volg de instructies op [Snelstartgids: een event hub maken met behulp van de Azure Portal](event-hubs-create.md)om ze te maken. Noteer de naam ruimte en Event Hub namen die u later in deze Quick Start wilt gebruiken.
- De naam van de gedeelde toegangs sleutel en de waarde van de primaire sleutel voor uw Event Hubs naam ruimte. Haal de naam en waarde van de toegangs sleutel op door de instructies te volgen in [Get a Event hubs Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). De standaard naam van de toegangs sleutel is *RootManageSharedAccessKey*. Noteer de sleutel naam en de waarde voor de primaire sleutel die u later in deze Snelstartgids wilt gebruiken.
- Python 2,7 of 3,5 of hoger, waarbij PIP is geïnstalleerd en bijgewerkt.
- Het python-pakket voor Event Hubs. 

    Als u het pakket wilt installeren, voert u deze opdracht uit in een opdracht prompt met python in het pad:

    ```cmd
    pip install azure-eventhub
    ```

    Installeer het volgende pakket om de gebeurtenissen te ontvangen met behulp van Azure Blob-opslag als controlepunt archief:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie maakt u een python-script voor het verzenden van gebeurtenissen naar de Event Hub die u eerder hebt gemaakt.

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/).
2. Maken van een script met de naam *send.py*. Met dit script wordt een batch gebeurtenissen verzonden naar de Event Hub die u eerder hebt gemaakt.
3. Plak de volgende code in *Send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Ga voor de volledige bron code, inclusief informatieve opmerkingen, naar de [pagina GitHub send_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Gebeurtenissen ontvangen
Deze Snelstartgids maakt gebruik van Azure Blob Storage als een controlepunt opslag. De controlepunt opslag wordt gebruikt voor het persistent maken van controle punten (dat wil zeggen, de laatste lees posities).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Een Azure-opslag account en een BLOB-container maken
Maak een Azure Storage-account en een BLOB-container door de volgende stappen uit te voeren:

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Een BLOB-container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De connection string ophalen voor het opslag account](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Zorg ervoor dat u de connection string en de container naam vastlegt voor later gebruik in de receive-code.


### <a name="create-a-python-script-to-receive-events"></a>Maken van een Python-script voor het ontvangen van gebeurtenissen

In deze sectie maakt u een python-script om gebeurtenissen van uw Event Hub te ontvangen:

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/).
2. Maken van een script met de naam *recv.py*.
3. Plak de volgende code in *recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Ga voor de volledige bron code, met inbegrip van aanvullende informatieve opmerkingen, naar de [pagina GitHub recv_with_checkpoint_store_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


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

In het venster ontvanger moeten de berichten worden weer gegeven die naar het Event Hub zijn verzonden.


## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u gebeurtenissen asynchroon verzonden en ontvangen. Als u wilt weten hoe u gebeurtenissen synchroon kunt verzenden en ontvangen, gaat u naar de [pagina GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Voor alle voor beelden (zowel synchroon als asynchroon) op GitHub gaat u naar de [client bibliotheek van Azure Event hubs voor python-voor beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
