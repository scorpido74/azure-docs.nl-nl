---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van JavaScript (verouderde versie)
description: Dit artikel bevat een overzicht van het maken van een JavaScript-toepassing waarmee gebeurtenissen worden verzonden naar/ontvangen van Azure Event Hubs met behulp van het oude pakket azure/event-hubs versie 2.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 548276b4bcbdd514c37a19375154923ad9f3f9a4
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314509"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Quickstart: Gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van JavaScript (@azure/event-hubs versie 2)
In deze quickstart ziet u hoe u Javascript-toepassingen creëert om gebeurtenissen te verzenden naar en ontvangen van een Event Hub met behulp van het JavaScript-pakket azure/event-hubs versie 2. 

> [!WARNING]
> In deze quickstart wordt het oude pakket azure/eventhub versie 2 gebruikt. Zie [Gebeurtenissen verzenden en ontvangen met behulp van azure-eventhub versie 5](get-started-node-send-v2.md) voor een quickstart waarin het nieuwste pakket azure/eventhub **versie 5** wordt gebruikt. Als u uw toepassing van het oude naar een nieuw pakket wilt verplaatsen, raadpleegt u de [Handleiding voor het migreren van azure/eventhub versie 1 naar versie 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Vereisten

Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Node.js versie 8.x en hoger. De nieuwste LTS-versie vanuit [https://nodejs.org](https://nodejs.org) downloaden.
- Visual Studio Code (aanbevolen) of een andere IDE
- **Een Event Hubs-naamruimte en een Event Hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken en ga daarna verder met de volgende stappen in deze zelfstudie. Haal vervolgens de verbindingsreeks voor de naamruimte van de Event Hub op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze zelfstudie.


### <a name="install-npm-package"></a>Npm-pakket installeren
Als u het [npm-pakket voor Event Hubs](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0) wilt installeren, opent u een opdrachtprompt met `npm` in het bijbehorende pad. Wijzig de directory in de map waar u de voorbeelden wilt maken en voer vervolgens deze opdracht uit

```shell
npm install @azure/event-hubs@2
```

Als u het [npm-pakket voor Event Processor Host](https://www.npmjs.com/package/@azure/event-processor-host) wilt installeren, voert u de onderstaande opdracht uit

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie ziet u hoe u een JavaScript-toepassing creëert waarmee gebeurtenissen naar een Event Hub worden verzonden. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com). 
2. Maak een bestand met de naam `send.js` en plak de onderstaande code hierin. Haal de verbindingsreeks voor de naamruimte van de Event Hub op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
3. Voer in de bovenstaande code de verbindingstekenreeks en de naam van uw Event Hub in
4. Voer vervolgens de opdracht `node send.js` in een opdrachtprompt uit om dit bestand uit te voeren. Hiermee worden 100 gebeurtenissen naar uw eventhub verzonden

Gefeliciteerd! U hebt nu gebeurtenissen verzonden naar een Event Hub.


## <a name="receive-events"></a>Gebeurtenissen ontvangen

In deze sectie wordt beschreven hoe u een JavaScript-toepassing maakt die gebeurtenissen ontvangt van één partitie van de standaardconsumentengroep in een Event Hub. 

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com). 
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
3. Voer in de bovenstaande code de verbindingstekenreeks en de naam van uw Event Hub in.
4. Voer vervolgens de opdracht `node receive.js` in een opdrachtprompt uit om dit bestand uit te voeren. Dit ontvangt gebeurtenissen van een van de partities van de standaardconsumentengroep in de Event Hub

Gefeliciteerd! U hebt nu gebeurtenissen ontvangen van Event Hub.

## <a name="receive-events-using-event-processor-host"></a>Gebeurtenissen ontvangen met Event Processor Host

In deze sectie wordt beschreven hoe u gebeurtenissen van een Event Hub ontvangt met behulp van Azure [EventProcessorHost](event-hubs-event-processor-host.md) in een JavaScript-toepassing. Met de EventProcessorHost (EPH) kunt u op efficiënte wijze gebeurtenissen ontvangen van een Event Hub door ontvangers te maken voor alle partities in de consumentengroep van een Event Hub. Er worden in een Azure Storage-blob regelmatig controlepunten voor metagegevens uitgevoerd voor ontvangen berichten. Met deze aanpak kunt u later eenvoudig berichten blijven ontvangen vanaf de plek waar u was gebleven.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com). 
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
3. Voer de verbindingstekenreeks en de naam van uw Event Hub in de bovenstaande code in, samen met de verbindingstekenreeks voor een Azure-Blob Storage
4. Voer vervolgens de opdracht `node receiveAll.js` in een opdrachtprompt uit om dit bestand uit te voeren.

Gefeliciteerd! U hebt nu met behulp van EventProcessorHost gebeurtenissen ontvangen van Event Hub. Dit ontvangt gebeurtenissen van alle partities van de standaardconsumentengroep in de Event Hub

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
- Bekijk andere JavaScript-voorbeelden voor [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) en [Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) op GitHub
