---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met python (recentste)
description: Dit artikel bevat een overzicht van het maken van een python-toepassing die gebeurtenissen naar/van Azure Event Hubs verzendt/ontvangt met behulp van het meest recente pakket met Azure-eventhub versie 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 6b16398c7c1fd53562df7e4ac8e801a8c97162f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82159434"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Gebeurtenissen verzenden naar of ontvangen gebeurtenissen van Event hubs met behulp van python (Azure-eventhub versie 5)
In deze Quick start ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het python-pakket van **Azure-eventhub versie 5** .

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van het meest recente pakket voor Azure-eventhub versie 5. Zie [gebeurtenissen verzenden en ontvangen met Azure-eventhub versie 1](event-hubs-python-get-started-send.md)voor een snelle start die gebruikmaakt van het oude pakket met Azure-eventhub versie 1. 

## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u [Event hubs Overview](event-hubs-about.md) voordat u deze Snelstartgids uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure abonnement**. Als u Azure-Services, met inbegrip van Azure Event Hubs, wilt gebruiken, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/free/) of de voor delen van uw MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
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
- **Een event hubs naam ruimte en een event hub maken**. De eerste stap is het gebruik van de [Azure Portal](https://portal.azure.com) om een naam ruimte van het type Event hubs te maken en de beheer referenties te verkrijgen die uw toepassing nodig heeft om met de Event hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Vervolgens haalt u de **Connection String voor de Event hubs naam ruimte** door de volgende instructies uit het artikel: [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de connection string verderop in deze Quick Start.

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie maakt u een python-script voor het verzenden van gebeurtenissen naar de Event Hub die u eerder hebt gemaakt.

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/).
2. Maak een script met de naam *Send.py*. Met dit script wordt een batch gebeurtenissen verzonden naar de Event Hub die u eerder hebt gemaakt.
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

> [!NOTE]
> Als u gebruikmaakt van Azure Stack hub, ondersteunt dat platform mogelijk een andere versie van de opslag-BLOB-SDK dan die doorgaans beschikbaar is op Azure. Als u bijvoorbeeld werkt met [Azure stack hub versie 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), is de hoogste beschik bare versie van de opslag service versie 2017-11-09. In dit geval moet u, naast de volgende stappen in deze sectie, ook code toevoegen om de API-versie 2017-11-09 van de Storage-service te richten. Zie [synchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) en [asynchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) voor beelden op github voor een voor beeld van het richten op een specifieke opslag-API-versie. Raadpleeg [Azure stack hub Storage: verschillen en overwegingen](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)voor meer informatie over de versies van de Azure Storage-service die op Azure stack hub worden ondersteund.


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Een Azure-opslag account en een BLOB-container maken
Maak een Azure Storage-account en een BLOB-container door de volgende stappen uit te voeren:

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Een blob-container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De connection string ophalen voor het opslag account](../storage/common/storage-configure-connection-string.md)

Zorg ervoor dat u de connection string en de container naam vastlegt voor later gebruik in de receive-code.


### <a name="create-a-python-script-to-receive-events"></a>Een python-script maken om gebeurtenissen te ontvangen

In deze sectie maakt u een python-script om gebeurtenissen van uw Event Hub te ontvangen:

1. Open uw favoriete python-editor, zoals [Visual Studio code](https://code.visualstudio.com/).
2. Maak een script met de naam *recv.py*.
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

Als u het script wilt uitvoeren, opent u een opdracht prompt met python in het pad en voert u de volgende opdracht uit:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>De Sender-app uitvoeren

Als u het script wilt uitvoeren, opent u een opdracht prompt met python in het pad en voert u de volgende opdracht uit:

```bash
python send.py
```

In het venster ontvanger moeten de berichten worden weer gegeven die naar het Event Hub zijn verzonden.


## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u gebeurtenissen asynchroon verzonden en ontvangen. Als u wilt weten hoe u gebeurtenissen synchroon kunt verzenden en ontvangen, gaat u naar de [pagina GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Voor alle voor beelden (zowel synchroon als asynchroon) op GitHub gaat u naar de [client bibliotheek van Azure Event hubs voor python-voor beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
