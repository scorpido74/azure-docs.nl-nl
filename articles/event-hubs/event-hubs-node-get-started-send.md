---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van Java script (verouderd)
description: Dit artikel bevat een overzicht van het maken van een Java script-toepassing waarmee gebeurtenissen naar/van Azure Event Hubs worden verzonden/ontvangen met behulp van het oude Azure/Event-hubs versie 2-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162613"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Quick Start: gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van Java script (@azure/event-hubs versie 2)
Deze Quick Start laat zien hoe u Java script-toepassingen maakt voor het verzenden van gebeurtenissen naar en het ontvangen van gebeurtenissen van een Event Hub met behulp van het Azure/Event-hubs versie 2 java script-pakket. 

> [!WARNING]
> Deze Snelstartgids maakt gebruik van het oude Azure/Event-hubs versie 2-pakket. Zie [gebeurtenissen verzenden en ontvangen met Azure/Event hubs versie 5](get-started-node-send-v2.md)voor een Snelstartgids die gebruikmaakt van de meest recente **versie 5** van het pakket. Zie de [hand leiding voor het migreren van Azure/Event hubs versie 1 naar versie 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)als u uw toepassing wilt verplaatsen van het gebruik van het oude pakket naar een nieuwe. 


## <a name="prerequisites"></a>Vereisten

Als u niet bekend bent met Azure Event Hubs, raadpleegt u [Event hubs Overview](event-hubs-about.md) voordat u deze Snelstartgids. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure abonnement**. Als u Azure-Services, met inbegrip van Azure Event Hubs, wilt gebruiken, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/free/) of de voor delen van uw MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Node.js versie 8.x en hoger. Down load de nieuwste versie van LTS van [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (aanbevolen) of een andere IDE
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Als u een naam ruimte en een Event Hub wilt maken, volgt u de procedure in [dit artikel](event-hubs-create.md)en gaat u verder met de volgende stappen in deze zelf studie. Vervolgens haalt u de connection string voor de Event Hub naam ruimte door de volgende instructies uit het artikel: [get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze zelfstudie.


### <a name="install-npm-package"></a>NPM-pakket installeren
Als u het [NPM-pakket voor Event hubs](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)wilt installeren, opent u een opdracht prompt met `npm` in het bijbehorende pad. Wijzig de map in de map waar u de voor beelden wilt maken en voer vervolgens deze opdracht uit

```shell
npm install @azure/event-hubs@2
```

Als u het [NPM-pakket voor Event processor host](https://www.npmjs.com/package/@azure/event-processor-host)wilt installeren, voert u de onderstaande opdracht uit.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie wordt beschreven hoe u een Java script-toepassing maakt waarmee gebeurtenissen naar een Event Hub worden verzonden. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com). 
2. Maak een bestand met de naam `send.js` en plak de onderstaande code hierin. Haal de connection string voor de Event Hub naam ruimte door de instructies in het artikel: [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Voer de connection string en de naam van uw event hub in de bovenstaande code in
4. Voer vervolgens de opdracht `node send.js` in een opdracht prompt uit om dit bestand uit te voeren. Hiermee worden 100 gebeurtenissen naar uw event hub verzonden

Gefeliciteerd. U hebt nu gebeurtenissen verzonden naar een Event Hub.


## <a name="receive-events"></a>Gebeurtenissen ontvangen

In deze sectie wordt beschreven hoe u een Java script-toepassing maakt die gebeurtenissen ontvangt van één partitie van de standaard Consumer-groep in een Event Hub. 

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com). 
2. Maak een bestand met de naam `receive.js` en plak de onderstaande code hierin.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Voer de connection string en de naam van uw event hub in de bovenstaande code in.
4. Voer vervolgens de opdracht `node receive.js` in een opdracht prompt uit om dit bestand uit te voeren. Hiermee ontvangt u gebeurtenissen van een van de partities van de standaard Consumer-groep in uw event hub

Gefeliciteerd. U hebt nu gebeurtenissen ontvangen van Event Hub.

## <a name="receive-events-using-event-processor-host"></a>Gebeurtenissen ontvangen met Event Processor Host

In deze sectie wordt beschreven hoe u gebeurtenissen van een Event Hub ontvangt met behulp van Azure [EventProcessorHost](event-hubs-event-processor-host.md) in een Java script-toepassing. De EventProcessorHost (EPH) kunt u efficiënt gebeurtenissen ontvangen van een event hub door het maken van ontvangers voor alle partities in de consumentengroep van een event hub. Deze controlepunten metagegevens op de ontvangen berichten met regelmatige tussenpozen in een Azure Storage-Blob. Deze aanpak maakt het eenvoudig om door te gaan met het ontvangen van berichten van waar u gestopt op een later tijdstip bent.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com). 
2. Maak een bestand met de naam `receiveAll.js` en plak de onderstaande code hierin.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Voer de connection string en de naam van uw event hub in de bovenstaande code in, samen met de connection string voor een Azure-Blob Storage
4. Voer vervolgens de opdracht `node receiveAll.js` in een opdracht prompt uit om dit bestand uit te voeren.

Gefeliciteerd. U hebt nu gebeurtenissen ontvangen van Event Hub met behulp van Event processor host. Hiermee ontvangt u gebeurtenissen van alle partities van de standaard Consumer-groep in uw event hub

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
- Bekijk andere Java script-voor beelden voor [Event hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) en [Event processor host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) op github
