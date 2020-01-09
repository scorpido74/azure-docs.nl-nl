---
title: 'Quick Start: Azure Queue-opslag bibliotheek V12-java script'
description: Meer informatie over het gebruik van de Azure Queue java script V12-bibliotheek voor het maken van een wachtrij en het toevoegen van berichten aan de wachtrij. Vervolgens leert u hoe u berichten leest en verwijdert uit de wachtrij. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 9f666376020d36bd57b2cc9e78a0704a8ec47b4e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473120"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Quick Start: Azure Queue Storage-client bibliotheek V12 voor Java script

Aan de slag met de Azure Queue Storage-client bibliotheek versie 12 voor Java script. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten die later worden opgehaald en verwerkt. Volg deze stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Gebruik de Azure Queue Storage-client bibliotheek V12 voor Java script voor het volgende:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten van een wachtrij ontvangen
* Berichten uit een wachtrij verwijderen
* Een wachtrij verwijderen

[API-referentie documentatie](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [pakket (knooppunt pakket beheer)](https://www.npmjs.com/package/@azure/storage-queue) | voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidig [knoop punt. js](https://nodejs.org/en/download/) voor uw besturings systeem.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-client bibliotheek V12 voor Java script.

### <a name="create-the-project"></a>Het project maken

Maak een node. js-toepassing *met de naam queues-Quick Start-V12*.

1. Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor het project.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Schakel over naar de nieuwe map *wacht rijen-Quick Start-V12* .

    ```console
    cd queues-quickstart-v12
    ```

1. Maak een nieuw tekst bestand met de naam *package. json*. Dit bestand definieert het node. js-project. Sla dit bestand op in de map *wacht rijen-Quick Start-V12* . Dit is de inhoud van het bestand:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Als u wilt, kunt u uw eigen naam in het veld `author` opnemen.

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u zich nog steeds in de map *wacht rijen-Quick Start-V12* bevindt, installeert u de Azure Queue Storage-client bibliotheek voor Java script-pakket met behulp van de `npm install` opdracht.

```console
npm install
```

 Met deze opdracht wordt het bestand *package. json* gelezen en wordt de Azure Queue Storage-client bibliotheek V12 voor Java script-pakket en alle bibliotheken waarvan het afhankelijk is, geïnstalleerd.

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Een ander nieuw tekst bestand openen in de code-editor
1. `require`-aanroepen toevoegen om Azure-en node. js-modules te laden
1. De structuur voor het programma maken, inclusief zeer eenvoudige uitzonde ringen verwerken

    Hier volgt de code:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Sla het nieuwe bestand op als *Queues-QuickStart-V12. js* in de map *Queues-Quick Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Object model

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrij bericht kan Maxi maal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteits limiet van een opslag account. Wacht rijen worden vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk. Queue Storage biedt drie typen resources:

* Het opslag account
* Een wachtrij in het opslag account
* Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de architectuur van de wachtrij opslag](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende Java script-klassen om te communiceren met deze resources:

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient): met de `QueueServiceClient` kunt u de alle wacht rijen in uw opslag account beheren.
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient): de klasse `QueueClient` biedt u de mogelijkheid om een afzonderlijke wachtrij en de bijbehorende berichten te beheren en te bewerken.
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage): de klasse `QueueMessage` vertegenwoordigt de afzonderlijke objecten die worden geretourneerd bij het aanroepen van [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) voor een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeld code fragmenten ziet u hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-client bibliotheek voor Java script:

* [De connection string ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten van een wachtrij ontvangen](#receive-messages-from-a-queue)
* [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op uit de omgevings variabele die in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) is gemaakt.

Voeg deze code toe in de `main` functie:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Een wachtrij maken

Kies een naam voor de nieuwe wachtrij. De onderstaande code voegt een UUID-waarde toe aan de naam van de wachtrij om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> De naam van een wachtrij mag alleen kleine letters, cijfers en afbreek streepjes bevatten en moet beginnen met een letter of een cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie de [naamgeving van wacht rijen en meta gegevens](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)voor meer informatie over de naamgeving van wacht rijen.

Maak een instantie van de klasse [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) . Roep vervolgens de [Create](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) -methode aan om de wachtrij in uw opslag account te maken.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Berichten toevoegen aan een wachtrij

Met het volgende code fragment worden berichten toegevoegd aan de wachtrij door de [SendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) -methode aan te roepen. Ook wordt de [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) die wordt geretourneerd door de derde `sendMessage`-aanroep opgeslagen. De geretourneerde `sendMessageResponse` wordt gebruikt om de inhoud van het bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de methode [peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) aan te roepen. Met de methode `peekMessages` worden een of meer berichten van het begin van de wachtrij opgehaald, maar wordt de zicht baarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

De inhoud van een bericht bijwerken door de methode [updateMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) aan te roepen. Met de methode `updateMessage` kan de time-out en inhoud van de zicht baarheid van een bericht worden gewijzigd. De inhoud van het bericht moet een teken reeks met UTF-8-code ring zijn met een grootte van Maxi maal 64 KB. Naast de nieuwe inhoud, geeft u `messageId` en `popReceipt` uit het antwoord dat eerder in de code is opgeslagen. De `sendMessageResponse` eigenschappen bepalen welk bericht moet worden bijgewerkt.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

Down load eerder toegevoegde berichten door de [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) -methode aan te roepen.  Geef in het veld `numberOfMessages` het maximum aantal berichten dat voor deze aanroep moet worden ontvangen.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Berichten uit de wachtrij verwijderen nadat ze zijn ontvangen en verwerkt. In dit geval wordt de verwerking gewoon het bericht op de console weer gegeven.

Berichten verwijderen door de methode [deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) aan te roepen. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij om ze te kunnen verwerken.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de wachtrij te verwijderen met de methode [Delete](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) .

Voeg deze code toe aan het einde van de functie `main` en sla het bestand op:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en toegevoegd aan een Azure-wachtrij. De code vermeldt de berichten in de wachtrij, haalt deze vervolgens op en verwijdert deze voordat u de wachtrij definitief verwijdert.

Navigeer in uw console venster naar de map met het bestand *Queues-QuickStart-V12. js* en voer de volgende `node` opdracht uit om de app uit te voeren.

```console
node queues-quickstart-v12.js
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Door loop de code in uw debugger en controleer de [Azure Portal](https://portal.azure.com) gedurende het proces. Controleer uw opslag account om te controleren of de berichten in de wachtrij zijn gemaakt en verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een wachtrij maakt en er berichten aan toevoegt met behulp van Java script-code. Vervolgens hebt u geleerd hoe u berichten kunt bekijken, ophalen en verwijderen. Ten slotte hebt u geleerd hoe u een berichten wachtrij verwijdert.

Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Documentatie voor Azure voor Java script](https://docs.microsoft.com/azure/javascript/)

* Zie voor meer informatie de [Azure Storage Queue-client bibliotheek voor Java script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
* Als u meer voor beelden wilt zien van Azure Queue Storage-voor beeld-apps, gaat u naar [Azure Queue Storage-client bibliotheek V12 java script samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
