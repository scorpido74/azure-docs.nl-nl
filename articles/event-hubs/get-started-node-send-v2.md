---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van Java script (nieuwste)
description: Dit artikel bevat een overzicht van het maken van een Java script-toepassing waarmee gebeurtenissen naar/van Azure Event Hubs worden verzonden/ontvangen met behulp van het nieuwste Azure/Event-hubs versie 5-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: e296ae36eeeb816d8704ab03824f8cbb80082ea6
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163004"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Gebeurtenissen verzenden naar of ontvangen gebeurtenissen van Event hubs met behulp van Java script (Azure/Event-hubs versie 5)
Deze Quick Start laat zien hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het **Azure/Event-hubs versie 5** java script-pakket. 

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van het nieuwste Azure/Event-hubs versie 5-pakket. Zie [gebeurtenissen verzenden en ontvangen met Azure/Event-hubs versie 2](event-hubs-node-get-started-send.md)voor een snelle start die gebruikmaakt van het oude Azure/Event-hubs versie 2-pakket. 

## <a name="prerequisites"></a>Vereisten
Als u niet bekend bent met Azure Event Hubs, raadpleegt u [Event hubs Overview](event-hubs-about.md) voordat u deze Snelstartgids. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure abonnement**. Als u Azure-Services, met inbegrip van Azure Event Hubs, wilt gebruiken, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/free/) of de voor delen van uw MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Node. js versie 8. x of hoger. Down load de meest recente [LTS-versie (lange termijn ondersteuning)](https://nodejs.org).  
- Visual Studio code (aanbevolen) of andere Integrated Development Environment (IDE).  
- Een actieve Event Hubs naam ruimte en Event Hub. Voer de volgende stappen uit om ze te maken: 

   1. Maak in de [Azure Portal](https://portal.azure.com)een naam ruimte van het type *Event hubs*en verkrijg vervolgens de beheer referenties die uw toepassing nodig heeft om met de Event hub te communiceren. 
   1. Volg de instructies op Quick Start om de naam ruimte en het Event Hub te maken [: Maak een event hub met behulp van de Azure Portal](event-hubs-create.md).
   1. Volg de instructies in deze Snelstartgids om door te gaan. 
   1. Volg de instructies in [get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)om de Connection String voor uw event hub-naam ruimte op te halen. Neem de connection string op die later in deze Snelstartgids moeten worden gebruikt.
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Vervolgens haalt u de **Connection String voor de Event hubs naam ruimte** door de volgende instructies uit het artikel: [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de connection string verderop in deze Quick Start.

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

In deze sectie maakt u een Java script-toepassing die gebeurtenissen naar een Event Hub verzendt.

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
In deze sectie ontvangt u gebeurtenissen van een Event Hub met behulp van een Azure Blob Storage-controlepunt archief in een Java script-toepassing. Het voert de controle punten voor meta gegevens uit op ontvangen berichten met regel matige intervallen in een Azure Storage blob. Met deze aanpak kunt u later eenvoudig berichten blijven ontvangen vanaf de plek waar u was gebleven.

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
