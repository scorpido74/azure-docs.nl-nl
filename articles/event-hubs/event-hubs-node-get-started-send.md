---
title: Gebeurtenissen verzenden of ontvangen vanuit Azure Event Hubs met JavaScript (legacy)
description: In dit artikel vindt u een walkthrough voor het maken van een JavaScript-toepassing die gebeurtenissen verzendt/ontvangt van/naar Azure Event Hubs met behulp van het oude versie 2-pakket azure/event-hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162613"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Snelstart: gebeurtenissen verzenden naar of ontvangen vanuit Azure@azure/event-hubs Event Hubs met JavaScript (versie 2)
Met deze quickstart u JavaScript-toepassingen maken om gebeurtenissen naar gebeurtenissen te verzenden en te ontvangen vanuit een gebeurtenishub met behulp van het JavaScript-pakket azure/event-hubs. 

> [!WARNING]
> Deze quickstart maakt gebruik van het oude azure/event-hubs versie 2-pakket. Zie [Gebeurtenissen verzenden en ontvangen met azure/eventhubs versie 5](get-started-node-send-v2.md)voor een snelle start die de nieuwste versie **5** van het pakket gebruikt. Als u uw toepassing wilt verplaatsen van het oude pakket naar het oude pakket, raadpleegt u de [handleiding om te migreren van azure/eventhubs versie 1 naar versie 5.](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md) 


## <a name="prerequisites"></a>Vereisten

Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- Node.js versie 8.x en hoger. Download de nieuwste LTS-versie van [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (aanbevolen) of een andere IDE
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Als u een naamruimte en een gebeurtenishub wilt maken, volgt u de procedure in [dit artikel](event-hubs-create.md)en gaat u verder met de volgende stappen in deze zelfstudie. Download vervolgens de verbindingstekenreeks voor de naamruimte van de gebeurtenishub door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze zelfstudie.


### <a name="install-npm-package"></a>NPM-pakket installeren
Als u het [npm-pakket voor gebeurtenishubs](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0) `npm` wilt installeren, opent u een opdrachtprompt die op zijn pad is, wijzigt u de map in de map waar u uw voorbeelden wilt hebben en voert u deze opdracht uit

```shell
npm install @azure/event-hubs@2
```

Als u het [npm-pakket voor Event Processor Host wilt](https://www.npmjs.com/package/@azure/event-processor-host)installeren, voert u in plaats daarvan de onderstaande opdracht uit

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie ziet u hoe u een JavaScript-toepassing maakt die gebeurtenissen naar een gebeurtenishub verzendt. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

1. Open uw favoriete editor, zoals [Visual Studio Code](https://code.visualstudio.com). 
2. Maak een `send.js` bestand aangeroepen en plak de onderstaande code erin. Download de verbindingstekenreeks voor de naamruimte van de gebeurtenishub door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
3. Voer de verbindingstekenreeks en de naam van uw gebeurtenishub in de bovenstaande code in
4. Voer vervolgens `node send.js` de opdracht uit in een opdrachtprompt om dit bestand uit te voeren. Dit stuurt 100 evenementen naar je Event Hub

Gefeliciteerd! Je hebt nu evenementen naar een gebeurtenishub gestuurd.


## <a name="receive-events"></a>Gebeurtenissen ontvangen

In deze sectie ziet u hoe u een JavaScript-toepassing maakt die gebeurtenissen ontvangt van één partitie van de standaardgroep voor consumenten in een gebeurtenishub. 

1. Open uw favoriete editor, zoals [Visual Studio Code](https://code.visualstudio.com). 
2. Maak een `receive.js` bestand aangeroepen en plak de onderstaande code erin.
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
3. Voer de verbindingstekenreeks en de naam van uw gebeurtenishub in de bovenstaande code in.
4. Voer vervolgens `node receive.js` de opdracht uit in een opdrachtprompt om dit bestand uit te voeren. Dit ontvangt gebeurtenissen van een van de partities van de standaardconsumentengroep in uw Event Hub

Gefeliciteerd! Je hebt nu evenementen ontvangen van de evenementenhub.

## <a name="receive-events-using-event-processor-host"></a>Gebeurtenissen ontvangen met Event Processor Host

In deze sectie ziet u hoe u gebeurtenissen ontvangt van een gebeurtenishub met Azure [EventProcessorHost](event-hubs-event-processor-host.md) in een JavaScript-toepassing. De EventProcessorHost (EPH) helpt u om gebeurtenissen efficiënt te ontvangen van een gebeurtenishub door ontvangers te maken voor alle partities in de consumentengroep van een gebeurtenishub. Het controleert metagegevens op ontvangen berichten op regelmatige tijdstippen in een Azure Storage Blob. Deze aanpak maakt het gemakkelijk om berichten te blijven ontvangen van waar u op een later tijdstip was gebleven.

1. Open uw favoriete editor, zoals [Visual Studio Code](https://code.visualstudio.com). 
2. Maak een `receiveAll.js` bestand aangeroepen en plak de onderstaande code erin.
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
3. Voer de verbindingstekenreeks en de naam van uw gebeurtenishub in de bovenstaande code in, samen met verbindingstekenreeks voor een Azure Blob Storage
4. Voer vervolgens `node receiveAll.js` de opdracht uit in een opdrachtprompt om dit bestand uit te voeren.

Gefeliciteerd! Je hebt nu evenementen ontvangen van event hub met Event Processor Host. Dit ontvangt gebeurtenissen van alle partities van de standaardconsumentengroep in uw Event Hub

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
- Bekijk andere JavaScript-voorbeelden voor [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) en [Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) op GitHub
