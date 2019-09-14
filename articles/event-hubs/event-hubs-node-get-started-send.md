---
title: Gebeurtenissen verzenden en ontvangen met behulp van node. js-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht voor het maken van een Node.js-toepassing die gebeurtenissen uit Azure Event Hubs verzendt.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 3bb222d3197ef37d56767300d71cc350d25a37bd
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984485"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van node. js

Azure Event Hubs is een Big data streaming-platform en Event opname-service waarmee miljoenen gebeurtenissen per seconde kunnen worden ontvangen en verwerkt. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelf studie wordt beschreven hoe u node. js-toepassingen maakt voor het verzenden van gebeurtenissen naar of het ontvangen van gebeurtenissen van een Event Hub.

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.
- Node.js versie 8.x en hoger. Download de nieuwste versie van de TNS van [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (aanbevolen) of een andere IDE
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Als u een naam ruimte en een Event Hub wilt maken, volgt u de procedure in [dit artikel](event-hubs-create.md)en gaat u verder met de volgende stappen in deze zelf studie. Vervolgens haalt u de connection string voor de Event Hub naam ruimte door de volgende instructies uit het artikel: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U de verbindingsreeks later in deze zelfstudie.


### <a name="install-npm-package"></a>NPM-pakket installeren
Als u het [NPM-pakket voor Event hubs](https://www.npmjs.com/package/@azure/event-hubs)wilt installeren, opent u een `npm` opdracht prompt met in het pad naar de map waarin u de voor beelden wilt maken en voert u deze opdracht uit

```shell
npm install @azure/event-hubs
```

Als u het [NPM-pakket voor Event processor host](https://www.npmjs.com/package/@azure/event-processor-host)wilt installeren, voert u de onderstaande opdracht uit.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie wordt beschreven hoe u een node. js-toepassing maakt die gebeurtenissen naar een Event Hub verzendt. 

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com). 
2. Maak een bestand met `send.js` de naam en plak de onderstaande code hierin. Haal de verbindingsreeks voor de naamruimte van de Event Hub op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

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
4. Voer vervolgens de opdracht `node send.js` uit in een opdracht prompt om dit bestand uit te voeren. Hiermee worden 100 gebeurtenissen naar uw event hub verzonden

Gefeliciteerd! U hebt nu gebeurtenissen verzonden naar een Event Hub.


## <a name="receive-events"></a>Gebeurtenissen ontvangen

In deze sectie wordt beschreven hoe u een node. js-toepassing maakt die gebeurtenissen ontvangt van één partitie van de standaard Consumer groep in een Event Hub. 

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com). 
2. Maak een bestand met `receive.js` de naam en plak de onderstaande code hierin.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

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
4. Voer vervolgens de opdracht `node receive.js` uit in een opdracht prompt om dit bestand uit te voeren. Hiermee ontvangt u gebeurtenissen van een van de partities van de standaard Consumer-groep in uw event hub

Gefeliciteerd! U hebt nu gebeurtenissen ontvangen van Event Hub.

## <a name="receive-events-using-event-processor-host"></a>Gebeurtenissen ontvangen met Event Processor Host

In deze sectie wordt beschreven hoe u gebeurtenissen van een Event Hub ontvangt met behulp van Azure [EventProcessorHost](event-hubs-event-processor-host.md) in een node. js-toepassing. De EventProcessorHost (EPH) kunt u efficiënt gebeurtenissen ontvangen van een event hub door het maken van ontvangers voor alle partities in de consumentengroep van een event hub. Deze controlepunten metagegevens op de ontvangen berichten met regelmatige tussenpozen in een Azure Storage-Blob. Deze aanpak maakt het eenvoudig om door te gaan met het ontvangen van berichten van waar u gestopt op een later tijdstip bent.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com). 
2. Maak een bestand met `receiveAll.js` de naam en plak de onderstaande code hierin.
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
4. Voer vervolgens de opdracht `node receiveAll.js` uit in een opdracht prompt om dit bestand uit te voeren.

Gefeliciteerd! U hebt nu gebeurtenissen ontvangen van Event Hub met behulp van Event processor host. Hiermee ontvangt u gebeurtenissen van alle partities van de standaard Consumer-groep in uw event hub

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
- Bekijk andere node. js-voor beelden voor [Event hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) en [Event processor host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) op github
