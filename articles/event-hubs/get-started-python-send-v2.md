---
title: Gebeurtenissen verzenden of ontvangen vanuit Azure Event Hubs met Python (laatste)
description: Dit artikel biedt een walkthrough voor het maken van een Python-toepassing die gebeurtenissen verzendt/ontvangt van/naar Azure Event Hubs met behulp van het nieuwste azure-eventhub versie 5-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 352ff91bf26c7ff4f6945431fe6e1357f030e1db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477529"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Gebeurtenissen verzenden naar of ontvangen van gebeurtenishubs met Python (azure-eventhub versie 5)
Deze quickstart laat zien hoe u gebeurtenissen verzendt naar en ontvangt vanuit een gebeurtenishub met het **Azure-Eventhub-versie 5 Python-pakket.**

> [!IMPORTANT]
> Deze quickstart maakt gebruik van het nieuwste azure-eventhub versie 5-pakket. Zie [Gebeurtenissen verzenden en ontvangen met Azure-Eventhub versie 1](event-hubs-python-get-started-send.md)voor een snelle start waarbij gebruik wordt gemaakt van het oude azure-eventhub-versie 1. 

## <a name="prerequisites"></a>Vereisten
Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- Python 2.7 of 3.5 of hoger, met PIP geïnstalleerd en bijgewerkt.
- Het Python-pakket voor gebeurtenishubs. 

    Als u het pakket wilt installeren, voert u deze opdracht uit in een opdrachtprompt met Python op zijn pad:

    ```cmd
    pip install azure-eventhub
    ```

    Installeer het volgende pakket voor het ontvangen van de gebeurtenissen met Azure Blob-opslag als controlepuntarchief:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download vervolgens de **verbindingstekenreeks voor de naamruimte van gebeurtenishubs** door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingstekenreeks later in deze snelstart.

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie maakt u een Python-script om gebeurtenissen naar de gebeurtenishub te verzenden die u eerder hebt gemaakt.

1. Open uw favoriete Python-editor, zoals [Visual Studio Code](https://code.visualstudio.com/).
2. Maak een script *met*de send.py . Dit script stuurt een batch gebeurtenissen naar de gebeurtenishub die u eerder hebt gemaakt.
3. Plak de volgende code in *send.py:*

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
    > Ga voor de volledige broncode, inclusief informatieve opmerkingen, naar de [pagina GitHub send_async.py.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)
    

## <a name="receive-events"></a>Gebeurtenissen ontvangen
Deze quickstart gebruikt Azure Blob-opslag als controlepuntopslag. Het controlepuntarchief wordt gebruikt om controlepunten (dat wil zeggen de laatst gelezen posities) voort te houden.  

> [!NOTE]
> Als u op Azure Stack Hub werkt, kan dat platform een andere versie van Storage Blob SDK ondersteunen dan die welke doorgaans beschikbaar zijn op Azure. Als u bijvoorbeeld op [Azure Stack Hub-versie 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)draait, is de hoogst beschikbare versie voor de opslagservice versie 2017-11-09. In dit geval moet u naast de volgende stappen in deze sectie ook code toevoegen om de API-versie 2017-11-09 van de opslagservice te targeten. Zie de [synchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) en [asynchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) voorbeelden op GitHub voor een voorbeeld over het targeten van een specifieke Storage API-versie. Voor meer informatie over de Azure Storage-serviceversies die worden ondersteund op Azure Stack Hub, raadpleegt u [Azure Stack Hub-opslag: verschillen en overwegingen](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Een Azure-opslagaccount en een blobcontainer maken
Maak een Azure-opslagaccount en een blobcontainer in het account door de volgende stappen uit te voeren:

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Een blob-container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De verbindingstekenreeks naar het opslagaccount weerleggen](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Zorg ervoor dat u de verbindingstekenreeks en de containernaam registreert voor later gebruik in de ontvangstcode.


### <a name="create-a-python-script-to-receive-events"></a>Een Python-script maken om gebeurtenissen te ontvangen

In deze sectie maakt u een Python-script om gebeurtenissen van uw gebeurtenishub te ontvangen:

1. Open uw favoriete Python-editor, zoals [Visual Studio Code](https://code.visualstudio.com/).
2. Een script maken dat *recv.py*wordt genoemd .
3. Plak de volgende code in *recv.py:*

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
    > Ga voor de volledige broncode, inclusief aanvullende informatieve opmerkingen, naar de [pagina GitHub recv_with_checkpoint_store_async.py.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


### <a name="run-the-receiver-app"></a>De ontvanger-app uitvoeren

Als u het script wilt uitvoeren, opent u een opdrachtprompt met Python op het pad en voert u deze opdracht uit:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>De afzender-app uitvoeren

Als u het script wilt uitvoeren, opent u een opdrachtprompt met Python op het pad en voert u deze opdracht uit:

```bash
python send.py
```

In het venster van de ontvanger moeten de berichten worden weergegeven die naar de gebeurtenishub zijn verzonden.


## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u gebeurtenissen asynchroon verzonden en ontvangen. Ga naar de [pagina GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)voor meer informatie over het synchroon verzenden en ontvangen van gebeurtenissen.

Ga voor alle voorbeelden (zowel synchroon als asynchroon) op GitHub naar [azure event hubs-clientbibliotheek voor Python-voorbeelden.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
