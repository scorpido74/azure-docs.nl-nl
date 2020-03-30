---
title: 'Snelstart: Azure Queue storage library v12 - JavaScript'
description: Meer informatie over het gebruik van de JavaScript-v12-bibliotheek in Azure Queue om een wachtrij te maken en berichten toe te voegen aan de wachtrij. Vervolgens leert u hoe u berichten uit de wachtrij lezen en verwijderen. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 59a5308d2c0a1fa2e1f38f2fe3da3a2cc29448be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199781"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Snelstart: Azure Queue storage client library v12 voor JavaScript

Aan de slag met de Azure Queue-opslagclientbibliotheekversie 12 voor JavaScript. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten voor later ophalen en verwerken. Volg deze stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken.

Gebruik de Azure Queue-opslagclientbibliotheek v12 voor JavaScript om:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten ontvangen vanuit een wachtrij
* Berichten uit een wachtrij verwijderen
* Een wachtrij verwijderen

[API-naslagdocumentatie](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [(Node Package Manager)](https://www.npmjs.com/package/@azure/storage-queue) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* Azure-opslagaccount - [een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidige [Node.js](https://nodejs.org/en/download/) voor uw besturingssysteem.

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Queue-opslagclientbibliotheek v12 voor JavaScript.

### <a name="create-the-project"></a>Het project maken

Maak een Node.js-toepassing met de naam *queues-quickstart-v12*.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Schakel over naar de nieuw gemaakte *map snelstart-v12 voor wachtrijen.*

    ```console
    cd queues-quickstart-v12
    ```

1. Een nieuw tekstbestand maken met de naam *package.json*. In dit bestand wordt het node.js-project gedefinieerd. Sla dit bestand op in de map *snelstart-v12 wachtrijen.* Hier is de inhoud van het bestand:

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

    Je je eigen naam `author` in het veld zetten, als je wilt.

### <a name="install-the-package"></a>Het pakket installeren

Installeer de azure queue storage-clientbibliotheek voor JavaScript-pakket met behulp van de `npm install` opdracht terwijl u nog steeds in de map *wachtrijen-quickstart-v12* staat.

```console
npm install
```

 Met deze opdracht wordt het *bestand package.json* gelezen en wordt het Azure Queue-opslagclientbibliotheek v12 voor JavaScript-pakket en alle bibliotheken waarvan het afhankelijk is, geïnstalleerd.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Een ander nieuw tekstbestand openen in uw codeeditor
1. Oproepen `require` toevoegen om Azure- en Node.js-modules te laden
1. Maak de structuur voor het programma, inclusief zeer eenvoudige uitzonderingsafhandeling

    Hier is de code:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Sla het nieuwe bestand op als *queues-quickstart-v12.js* in de map *wachtrijen-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een werkachterstand te maken om asynchroon te verwerken. Wachtrijopslag biedt drie soorten resources:

* Het opslagaccount
* Een wachtrij in het opslagaccount
* Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram met wachtrijopslagarchitectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende JavaScript-klassen om met deze bronnen te communiceren:

* [QueueServiceClient:](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient) `QueueServiceClient` Hiermee u alle wachtrijen in uw opslagaccount beheren.
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient): `QueueClient` Met de klasse u een afzonderlijke wachtrij en de bijbehorende berichten beheren en manipuleren.
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` De klasse vertegenwoordigt de afzonderlijke objecten die zijn geretourneerd bij het aanroepen [van ontvangenBerichten](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) in een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u de volgende acties uitvoeren met de Azure Queue-opslagclientbibliotheek voor JavaScript:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten ontvangen vanuit een wachtrij](#receive-messages-from-a-queue)
* [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op uit de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `main` toe in de functie:

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

Bepaal een naam voor de nieuwe wachtrij. De onderstaande code voegt een UUID-waarde toe aan de wachtrijnaam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en koppeltekens bevatten en moeten beginnen met een letter of een getal. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie [Wachtrijen en metagegevens voor](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)meer informatie over het benoemen van wachtrijen.

Maak een instantie van de klasse [QueueClient.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) Roep vervolgens de [methode maken](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) aan om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe `main` aan het einde van de functie:

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

In het volgende codefragment worden berichten toegevoegd aan de wachtrij door de [verzendmethode aan te](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) roepen. Het slaat ook de [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) terug van het derde `sendMessage` gesprek. De `sendMessageResponse` geretourneerde wordt gebruikt om de inhoud van het bericht later in het programma bij te werken.

Voeg deze code toe `main` aan het einde van de functie:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de [methode peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) aan te roepen. De `peekMessages` methode haalt een of meer berichten op aan de voorkant van de wachtrij, maar verandert niets aan de zichtbaarheid van het bericht.

Voeg deze code toe `main` aan het einde van de functie:

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

Werk de inhoud van een bericht bij door de [update-methode van Message](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) aan te roepen. De `updateMessage` methode kan de zichtbaarheidstime-out en inhoud van een bericht wijzigen. De inhoud van het bericht moet een utf-8 gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Samen met de nieuwe `messageId` inhoud, pass in en `popReceipt` van de reactie die eerder is opgeslagen in de code. De `sendMessageResponse` eigenschappen bepalen welk bericht moet worden bijgewerkt.

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

### <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij

Download eerder toegevoegde berichten door de methode [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) aan te roepen.  Geef `numberOfMessages` in het veld het maximum aantal berichten door dat u voor deze oproep wilt ontvangen.

Voeg deze code toe `main` aan het einde van de functie:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Verwijder berichten uit de wachtrij nadat ze zijn ontvangen en verwerkt. In dit geval wordt het bericht alleen weergegeven op de console.

Berichten verwijderen door de [methode deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) aan te roepen. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij voor een nieuwe kans om ze te verwerken.

Voeg deze code toe `main` aan het einde van de functie:

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

Met de volgende code worden de resources die de app heeft gemaakt door de wachtrij te verwijderen met behulp van de [verwijdermethode,](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) opgeschoond.

Voeg deze code toe `main` aan het einde van de functie en sla het bestand op:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>De code uitvoeren

Deze app maakt en voegt drie berichten toe aan een Azure-wachtrij. De code geeft een lijst van de berichten in de wachtrij en haalt ze op en verwijdert ze, voordat u de wachtrij uiteindelijk verwijdert.

Navigeer in het consolevenster naar de map met het bestand *queues-quickstart-v12.js* en voer de volgende `node` opdracht uit om de app uit te voeren.

```console
node queues-quickstart-v12.js
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

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

Stap door de code in uw foutopsporing en controleer uw [Azure-portal](https://portal.azure.com) gedurende het hele proces. Controleer uw opslagaccount om berichten in de wachtrij te verifiëren en worden gemaakt en verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een wachtrij maken en er berichten aan toevoegen met JavaScript-code. Vervolgens heb je geleerd om te gluren, op te halen en berichten te verwijderen. Ten slotte hebt u geleerd hoe u een berichtenwachtrij verwijderen.

Voor tutorials, voorbeelden, snelle starts en andere documentatie, bezoek:

> [!div class="nextstepaction"]
> [Azure voor JavaScript-documentatie](https://docs.microsoft.com/azure/javascript/)

* Zie de azure [storage queue-clientbibliotheek voor JavaScript voor](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)meer informatie.
* Ga verder naar [Azure Queue storage client library v12 JavaScript samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
