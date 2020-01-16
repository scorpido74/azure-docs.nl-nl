---
title: Gebeurtenissen verzenden en ontvangen met behulp van node. js-Azure Event Hubs
description: Dit artikel bevat een overzicht voor het maken van een Node.js-toepassing die gebeurtenissen uit Azure Event Hubs verzendt.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981617"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van node. js

Azure Event Hubs is een Big data streaming-platform en Event opname-service waarmee miljoenen gebeurtenissen per seconde kunnen worden ontvangen en verwerkt. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten, verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden getransformeerd en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelf studie wordt beschreven hoe u node. js-toepassingen maakt voor het verzenden van gebeurtenissen naar of het ontvangen van gebeurtenissen van een Event Hub.

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van versie 5 van de Azure Event Hubs java script SDK. Zie [dit artikel](event-hubs-node-get-started-send.md)voor een snelle start die gebruikmaakt van de oude versie 2 van de Java script SDK. Als u versie 2 van de SDK gebruikt, raden we u aan uw code te migreren naar de nieuwste versie. Zie de [migratie handleiding](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.
- Node.js versie 8.x en hoger. Download de nieuwste versie van de TNS van [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (aanbevolen) of een andere IDE
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Als u een naam ruimte en een Event Hub wilt maken, volgt u de procedure in [dit artikel](event-hubs-create.md)en gaat u verder met de volgende stappen in deze zelf studie. Vervolgens haalt u de connection string voor de Event Hub naam ruimte door de volgende instructies uit het artikel: [get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze zelfstudie.


### <a name="install-npm-packages"></a>NPM-pakketten installeren
Als u het [NPM-pakket voor Event hubs](https://www.npmjs.com/package/@azure/event-hubs)wilt installeren, opent u een opdracht prompt met `npm` in het bijbehorende pad. Wijzig de map in de map waar u de voor beelden wilt maken en voer vervolgens deze opdracht uit

```shell
npm install @azure/event-hubs
```

Voor de ontvangende zijde moet u twee meer pakketten installeren. In deze Quick Start gebruikt u Azure Blob Storage voor het persistent maken van controle punten zodat het programma de gebeurtenissen die het al heeft gelezen niet kan lezen. Er worden met regel matige intervallen in een BLOB meta gegevens over ontvangen berichten in een controle punt geplaatst. Deze aanpak maakt het eenvoudig om door te gaan met het ontvangen van berichten van waar u gestopt op een later tijdstip bent.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie wordt beschreven hoe u een node. js-toepassing maakt die gebeurtenissen naar een Event Hub verzendt.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com).
2. Maak een bestand met de naam `send.js` en plak de volgende code in de map.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Vergeet niet om de **Connection String** en de **Event hub naam** waarden in de code te vervangen.
5. Voer de opdracht uit `node send.js` om dit bestand uit te voeren. Hiermee wordt een batch van drie gebeurtenissen naar uw Event Hub verzonden
6. In de Azure Portal kunt u controleren of de Event Hub de berichten heeft ontvangen. Schakel over naar de weer gave **berichten** in het gedeelte **metrische gegevens** . Vernieuw de pagina om de grafiek bij te werken. Het kan een paar seconden duren voordat wordt weer gegeven dat de berichten zijn ontvangen.

    [![controleren of het Event Hub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Voor de volledige bron code met meer informatieve opmerkingen raadpleegt u [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Gefeliciteerd. U hebt nu gebeurtenissen verzonden naar een Event Hub.


## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie wordt beschreven hoe u gebeurtenissen van een Event Hub ontvangt met behulp van een Azure Blob-opslag punt archief in een node. js-toepassing. Deze controlepunten metagegevens op de ontvangen berichten met regelmatige tussenpozen in een Azure Storage-Blob. Deze aanpak maakt het eenvoudig om door te gaan met het ontvangen van berichten van waar u gestopt op een later tijdstip bent.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Een Azure Storage en een BLOB-container maken
Volg deze stappen om een Azure Storage account te maken in een BLOB-container.

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Een BLOB-container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De connection string ophalen voor het opslag account](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Noteer connection string en de naam van de container. U gebruikt deze in de receive-code.

### <a name="write-code-to-receive-events"></a>Code schrijven voor het ontvangen van gebeurtenissen

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com).
2. Maak een bestand met de naam `receive.js` en plak de volgende code in de map. Zie code opmerkingen voor meer informatie.
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
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
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
3. Vergeet niet om de **volgende waarden** op te geven in de code:
    - Verbindings reeks voor de Event Hubs naam ruimte
    - De naam van de Event Hub
    - Verbindings reeks met het Azure Storage-account
    - De naam van de BLOB-container
5. Voer vervolgens de opdracht `node receive.js` in een opdracht prompt uit om dit bestand uit te voeren. U ziet de berichten over ontvangen gebeurtenissen in het venster.

    > [!NOTE]
    > Zie [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)voor de volledige bron code met meer informatieve opmerkingen.

Gefeliciteerd. U hebt nu gebeurtenissen ontvangen van Event Hub. Het Receive-programma ontvangt gebeurtenissen van alle partities van de standaard Consumer-groep in uw Event Hub

## <a name="next-steps"></a>Volgende stappen
Bekijk deze voor beelden op GitHub:

- [Java script-voor beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Type script-voor beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
