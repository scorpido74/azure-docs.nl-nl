---
title: Gebeurtenissen verzenden of ontvangen vanuit Azure Event Hubs met JavaScript (laatste)
description: Dit artikel biedt een walkthrough voor het maken van een JavaScript-toepassing die gebeurtenissen verzendt/ontvangt van/naar Azure Event Hubs met behulp van het nieuwste versie 5-pakket azure/event-hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: e296ae36eeeb816d8704ab03824f8cbb80082ea6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77163004"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Gebeurtenissen verzenden naar of ontvangen van gebeurtenishubs met JavaScript (versie 5 van Azure/Event-Hubs)
In deze quickstart ziet u hoe u gebeurtenissen verzendt naar en ontvangt vanuit een gebeurtenishub met het **JavaScript-pakket azure/event-hubs.** 

> [!IMPORTANT]
> Deze quickstart maakt gebruik van het nieuwste azure/event-hubs versie 5-pakket. Zie [Gebeurtenissen verzenden en ontvangen met azure/event-hubs versie 2](event-hubs-node-get-started-send.md)voor een snelle start waarbij gebruik wordt gemaakt van het oude pakket versie 2 van Azure/Event-Hubs. 

## <a name="prerequisites"></a>Vereisten
Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- Node.js versie 8.x of hoger. Download de nieuwste [LTS-versie (Long-Term Support).](https://nodejs.org)  
- Visual Studio Code (aanbevolen) of een andere geïntegreerde ontwikkelomgeving (IDE).  
- Een actieve naamruimte voor Gebeurtenishubs en gebeurtenishub. Ga als volgt te werk om ze te maken: 

   1. Maak in de [Azure-portal](https://portal.azure.com)een naamruimte van het type *Gebeurtenishubs*en verkrijg vervolgens de beheerreferenties die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. 
   1. Als u de naamruimte en gebeurtenishub wilt maken, volgt u de instructies bij [Quickstart: Een gebeurtenishub maken met behulp van de Azure-portal](event-hubs-create.md).
   1. Ga verder door de instructies in deze quickstart te volgen. 
   1. Als u de verbindingstekenreeks voor de naamruimte van de gebeurtenishub wilt krijgen, volgt u de instructies in [Verbindingstekenreeks opraken](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Neem de verbindingstekenreeks op die u later in deze snelstart wilt gebruiken.
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download vervolgens de **verbindingstekenreeks voor de naamruimte van gebeurtenishubs** door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingstekenreeks later in deze snelstart.

### <a name="install-the-npm-package"></a>Het npm-pakket installeren
Als u het [npm-pakket (Node Package Manager) voor gebeurtenishubs wilt](https://www.npmjs.com/package/@azure/event-hubs)installeren, opent u een opdrachtprompt met *npm* op het pad, wijzigt u de map in de map waar u uw voorbeelden wilt bewaren en voert u deze opdracht uit:

```shell
npm install @azure/event-hubs
```

Voor de ontvangende kant moet u nog twee pakketten installeren. In deze snelstart gebruikt u Azure Blob-opslag om controlepunten voort te houden, zodat het programma de gebeurtenissen die het al heeft gelezen, niet leest. Het voert metagegevenscontrolepunten uit op ontvangen berichten met regelmatige tussenpozen in een blob. Deze aanpak maakt het gemakkelijk om berichten later te blijven ontvangen van waar u was gebleven.

Voer de volgende opdrachten uit:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie maakt u een JavaScript-toepassing die gebeurtenissen naar een gebeurtenishub verzendt.

1. Open uw favoriete editor, zoals [Visual Studio Code](https://code.visualstudio.com).
1. Maak een bestand met de naam *send.js*en plak de volgende code erin:

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
1. Voer `node send.js` uit om dit bestand uit te voeren. Met deze opdracht wordt een batch van drie gebeurtenissen naar uw gebeurtenishub verzenden.
1. Controleer in de Azure-portal of de gebeurtenishub de berichten heeft ontvangen. Schakel in de sectie **Statistieken** over naar **de weergave Berichten.** Vernieuw de pagina om de grafiek bij te werken. Het kan enkele seconden duren voordat het is aangetoond dat de berichten zijn ontvangen.

    [![Controleren of de gebeurtenishub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Ga voor de volledige broncode, inclusief aanvullende informatieve opmerkingen, naar de [pagina GitHub sendEvents.js.](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Gefeliciteerd! Je hebt nu evenementen naar een gebeurtenishub gestuurd.


## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie ontvangt u gebeurtenissen van een gebeurtenishub met behulp van een Azure Blob-opslagcontrolearchief in een JavaScript-toepassing. Er worden metagegevenscontrolepunten uitgevoerd voor ontvangen berichten op regelmatige tijdstippen in een Azure Storage-blob. Deze aanpak maakt het gemakkelijk om berichten later te blijven ontvangen van waar u was gebleven.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Een Azure-opslagaccount en een blobcontainer maken
Ga als volgt te werk om een Azure-opslagaccount en een blobcontainer te maken:

1. [Een Azure-opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Een blobcontainer maken in het opslagaccount](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [De verbindingstekenreeks naar het opslagaccount weerleggen](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Zorg ervoor dat u de verbindingstekenreeks en de containernaam registreert voor later gebruik in de ontvangstcode.

### <a name="write-code-to-receive-events"></a>Code schrijven om gebeurtenissen te ontvangen

1. Open uw favoriete editor, zoals [Visual Studio Code](https://code.visualstudio.com).
1. Maak een bestand met de naam *receive.js*en plak de volgende code erin:

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
1. Voer `node receive.js` een opdrachtprompt uit om dit bestand uit te voeren. In het venster moeten berichten over ontvangen gebeurtenissen worden weergegeven.

    > [!NOTE]
    > Ga voor de volledige broncode, inclusief aanvullende informatieve opmerkingen, naar de [pagina GitHub receiveEventsUsingCheckpointStore.js.](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)

Gefeliciteerd! Je hebt nu evenementen ontvangen van je evenementenhub. Het ontvangstprogramma ontvangt gebeurtenissen van alle partities van de standaardconsumentengroep in de gebeurtenishub.

## <a name="next-steps"></a>Volgende stappen
Bekijk deze voorbeelden op GitHub:

- [JavaScript-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
