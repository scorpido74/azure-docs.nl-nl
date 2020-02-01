---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van node. js (laatste)
description: Dit artikel bevat een overzicht van het maken van een node. js-toepassing waarmee gebeurtenissen naar/van Azure Event Hubs worden verzonden/ontvangen met behulp van het nieuwste Azure/Event-hubs versie 5-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906371"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Gebeurtenissen verzenden naar of ontvangen gebeurtenissen van Event hubs met behulp van node. js (Azure/Event-hubs versie 5)

Azure Event Hubs is een big data streaming platform en een service voor gebeurtenis opname waarmee miljoenen gebeurtenissen per seconde kunnen worden ontvangen en verwerkt. Event hubs kan gebeurtenissen, gegevens of telemetrie verwerken en opslaan die worden geproduceerd door gedistribueerde software en apparaten. Gegevens die naar een Event Hub worden verzonden, kunnen worden getransformeerd en opgeslagen met behulp van een real-time analyse provider of batch-en opslag adapters. Zie [Event hubs-overzicht](event-hubs-about.md) en [Event hubs-functies](event-hubs-features.md)voor meer informatie.

In deze Quick Start wordt beschreven hoe u node. js-toepassingen maakt die gebeurtenissen kunnen verzenden naar of ontvangen van een Event Hub.

> [!IMPORTANT]
> In deze Quick Start wordt versie 5 van de Azure Event Hubs java script SDK gebruikt. Zie [dit artikel](event-hubs-node-get-started-send.md)voor een snelle start die gebruikmaakt van versie 2 van de Java script SDK. 

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.  
- Node. js versie 8. x of hoger. Down load de meest recente [LTS-versie (lange termijn ondersteuning)](https://nodejs.org).  
- Visual Studio code (aanbevolen) of andere Integrated Development Environment (IDE).  
- Een actieve Event Hubs naam ruimte en Event Hub. Voer de volgende stappen uit om ze te maken: 

   1. Maak in de [Azure Portal](https://portal.azure.com)een naam ruimte van het type *Event hubs*en verkrijg vervolgens de beheer referenties die uw toepassing nodig heeft om met de Event hub te communiceren. 
   1. Volg de instructies op Quick Start om de naam ruimte en het Event Hub te maken [: Maak een event hub met behulp van de Azure Portal](event-hubs-create.md).
   1. Volg de instructies in deze Snelstartgids om door te gaan. 
   1. Volg de instructies in [get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)om de Connection String voor uw event hub-naam ruimte op te halen. Neem de connection string op die later in deze Snelstartgids moeten worden gebruikt.

### <a name="install-the-npm-package"></a>Het NPM-pakket installeren
Als u het [NPM-pakket (node Package Manager) voor Event hubs](https://www.npmjs.com/package/@azure/event-hubs)wilt installeren, opent u een opdracht prompt met *NPM* in het pad naar de map waarin u de voor beelden wilt opslaan en voert u de volgende opdracht uit:

```shell
npm install @azure/event-hubs
```

Voor de ontvangende zijde moet u twee meer pakketten installeren. In deze Quick Start gebruikt u Azure Blob Storage om controle punten te behouden zodat het programma de gebeurtenissen die het al heeft gelezen niet kan lezen. Er worden met regel matige intervallen in een BLOB meta gegevens controle punten uitgevoerd op ontvangen berichten. Met deze aanpak kunt u later eenvoudig berichten blijven ontvangen vanaf de plek waar u was gebleven.

Voer de volgende opdrachten uit:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie maakt u een node. js-toepassing die gebeurtenissen naar een Event Hub verzendt.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com).
1. Maak een bestand met de naam *Send. js*en plak de volgende code hierin:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. Gebruik in de code echte waarden om het volgende te vervangen:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Voer `node send.js` uit om dit bestand uit te voeren. Met deze opdracht verzendt u een batch met drie gebeurtenissen naar uw Event Hub.
1. Controleer in de Azure Portal of de Event Hub de berichten heeft ontvangen. Schakel in de sectie **metrische gegevens** over naar de weer gave **berichten** . Vernieuw de pagina om de grafiek bij te werken. Het kan een paar seconden duren voordat wordt weer gegeven dat de berichten zijn ontvangen.

    [![controleren of het Event Hub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Ga voor de volledige bron code, met inbegrip van aanvullende informatieve opmerkingen, naar de [pagina github sendEvents. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Gefeliciteerd. U hebt nu gebeurtenissen verzonden naar een Event Hub.


## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie ontvangt u gebeurtenissen van een Event Hub met behulp van een Azure Blob Storage-controlepunt archief in een node. js-toepassing. Het voert de controle punten voor meta gegevens uit op ontvangen berichten met regel matige intervallen in een Azure Storage blob. Met deze aanpak kunt u later eenvoudig berichten blijven ontvangen vanaf de plek waar u was gebleven.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Een Azure-opslag account en een BLOB-container maken
Voer de volgende acties uit om een Azure-opslag account en een BLOB-container daarin te maken:

1. [Een Azure-opslag account maken](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Een BLOB-container maken in het opslag account](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [De connection string ophalen voor het opslag account](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Zorg ervoor dat u de connection string en de container naam vastlegt voor later gebruik in de receive-code.

### <a name="write-code-to-receive-events"></a>Code schrijven voor het ontvangen van gebeurtenissen

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com).
1. Maak een bestand met de naam *Receive. js*en plak de volgende code hierin:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. Gebruik in de code echte waarden om de volgende waarden te vervangen:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Voer `node receive.js` uit in een opdracht prompt om dit bestand uit te voeren. In het venster moeten berichten over ontvangen gebeurtenissen worden weer gegeven.

    > [!NOTE]
    > Ga voor de volledige bron code, met inbegrip van aanvullende informatieve opmerkingen, naar de [pagina github receiveEventsUsingCheckpointStore. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Gefeliciteerd. U hebt nu gebeurtenissen ontvangen van uw Event Hub. Het Receive-programma ontvangt gebeurtenissen van alle partities van de standaard Consumer-groep in de Event Hub.

## <a name="next-steps"></a>Volgende stappen
Bekijk deze voor beelden op GitHub:

- [Java script-voor beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Type script-voor beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
