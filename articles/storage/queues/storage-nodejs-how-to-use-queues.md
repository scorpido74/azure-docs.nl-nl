---
title: Azure-wachtrij opslag gebruiken met behulp van Node.js-Azure Storage
description: Meer informatie over het gebruik van Azure Queue-service voor het maken en verwijderen van wacht rijen. Meer informatie over het invoegen, ophalen en verwijderen van berichten met behulp van Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 77c35ae4b9e845cd3c0f638407c0d71c36fcf9f7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289681"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Azure-wachtrij opslag gebruiken met Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Overzicht

In deze hand leiding wordt beschreven hoe u algemene scenario's kunt uitvoeren met behulp van de Microsoft Azure Queue-service. De voor beelden zijn geschreven met behulp van de Node.js-API. De gedekte scenario's zijn het invoegen, inspecteren, ophalen en verwijderen van wachtrij berichten. Meer informatie over het maken en verwijderen van wacht rijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

Als u een lege Node.js-toepassing wilt maken, raadpleegt u [een Node.js-web-app maken in azure app service][Create a Node.js web app in Azure App Service], [een Node.js-toepassing bouwen en implementeren in een Azure-Cloud service][Build and deploy a Node.js application to an Azure Cloud Service] met behulp van Windows Power shell of [Visual Studio code][Visual Studio Code].

## <a name="configure-your-application-to-access-storage"></a>Uw toepassing configureren voor toegang tot opslag

De [Azure Storage-client bibliotheek voor Java script][Azure Storage client library for JavaScript] bevat een reeks gebruiks vriendelijke bibliotheken die communiceren met de opslag rest-Services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te verkrijgen

1. Gebruik een opdracht regel interface zoals Power shell (Windows), Terminal (Mac) of bash (UNIX), navigeer naar de map waarin u de voorbeeld toepassing hebt gemaakt.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. Typ **NPM \@ Azure/Storage-Queue installeren** in het opdracht venster.

1. Controleer of er een map met **knooppunt \_ modules** is gemaakt. In deze map vindt u het pakket ** \@ Azure/Storage-Queue** , dat de client bibliotheek bevat die u nodig hebt om toegang te krijgen tot de opslag.

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

1. Typ **npm install azure-storage** in het opdrachtvenster.

1. Controleer of er een map met **knooppunt \_ modules** is gemaakt. In die map vindt u het **Azure-opslag** pakket, dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot opslag.

---

### <a name="import-the-package"></a>Het pakket importeren

Gebruik uw code-editor om het volgende toe te voegen aan de bovenkant van het Java script-bestand waar u wacht rijen wilt gebruiken.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Een wachtrij maken

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Met de volgende code wordt de waarde van een omgevings variabele met de naam opgehaald `AZURE_STORAGE_CONNECTION_STRING` en gebruikt voor het maken van een [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient) -object. Het **QueueServiceClient** -object wordt vervolgens gebruikt voor het maken van een [QueueClient](/javascript/api/@azure/storage-queue/queueclient) -object. Met het **QueueClient** -object kunt u werken met een specifieke wachtrij.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Als de wachtrij al bestaat, wordt er een uitzonde ring gegenereerd.

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

De Azure-module leest de omgevings variabelen `AZURE_STORAGE_ACCOUNT` en `AZURE_STORAGE_ACCESS_KEY` , of `AZURE_STORAGE_CONNECTION_STRING` voor de vereiste informatie om verbinding te maken met uw Azure Storage-account. Als deze omgevings variabelen niet zijn ingesteld, moet u de account gegevens opgeven bij het aanroepen van **createQueueService**.

Met de volgende code wordt een **QueueService** -object gemaakt, waarmee u met wacht rijen kunt werken.

```javascript
var queueSvc = azure.createQueueService();
```

Roep de methode **createQueueIfNotExists** aan om een nieuwe wachtrij met de opgegeven naam te maken of de wachtrij te retour neren als deze al bestaat.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Als de wachtrij is gemaakt, `result.created` is waar. Als de wachtrij bestaat, `result.created` is onwaar.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Als u een bericht wilt toevoegen aan een wachtrij, roept u de [SendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) -methode aan.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Als u een bericht in een wachtrij wilt invoegen, roept u de methode **createMessage** aan om een nieuw bericht te maken en toe te voegen aan de wachtrij.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Het volgende bericht bekijken

U kunt de berichten in de wachtrij bekijken zonder ze uit de wachtrij te verwijderen door de methode **peekMessages** aan te roepen.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

[PeekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) bekijkt standaard één bericht. In het volgende voor beeld worden de eerste vijf berichten in de wachtrij weer geven. Als er minder dan vijf berichten zichtbaar zijn, worden alleen de zicht bare berichten weer gegeven.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

**PeekMessages** bekijkt standaard één bericht.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Het `result` bevat het bericht.

---

Het aanroepen van **peekMessages** als de wachtrij geen berichten bevat, wordt er geen fout geretourneerd. Er worden echter geen berichten geretourneerd.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

In het volgende voor beeld wordt de tekst van een bericht bijgewerkt.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Wijzig de inhoud van een bericht dat in de wachtrij wordt geplaatst door [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-)aan te roepen. 

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Wijzig de inhoud van een bericht dat in de wachtrij wordt geplaatst door **updateMessage**aan te roepen. 

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Een bericht uit de wachtrij verwijderen

Een bericht uit de wachtrij verwijderen is een proces in twee fasen:

1. Het bericht ophalen.

1. Verwijder het bericht.

In het volgende voor beeld wordt een bericht opgehaald en vervolgens verwijderd.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Als u een bericht wilt ontvangen, roept u de methode [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) aan. Met deze aanroep worden de berichten onzichtbaar gemaakt in de wachtrij, zodat andere clients deze niet kunnen verwerken. Wanneer uw toepassing een bericht heeft verwerkt, roept u [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) aan om het te verwijderen uit de wachtrij.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Standaard wordt een bericht 30 seconden verborgen. Na 30 seconden is de weer gave van andere clients zichtbaar. U kunt een andere waarde opgeven door [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) in te stellen wanneer u **receiveMessages**aanroept.

Het aanroepen van **receiveMessages** als de wachtrij geen berichten bevat, wordt er geen fout geretourneerd. Er worden echter geen berichten geretourneerd.

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Als u een bericht wilt ontvangen, roept u de methode **getMessages** aan. Met deze aanroep worden de berichten onzichtbaar gemaakt in de wachtrij, zodat andere clients deze niet kunnen verwerken. Wanneer uw toepassing een bericht heeft verwerkt, roept u **deleteMessage** aan om het te verwijderen uit de wachtrij.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Standaard wordt een bericht 30 seconden verborgen. Na 30 seconden is de weer gave van andere clients zichtbaar. U kunt een andere waarde opgeven met `options.visibilityTimeout` with **getMessages**.

Als u **getMessages** gebruikt wanneer er geen berichten in de wachtrij staan, worden er geen fouten geretourneerd. Er worden echter geen berichten geretourneerd.

---

## <a name="additional-options-for-dequeuing-messages"></a>Aanvullende opties voor het dequeuing van berichten

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen:

* [Options. numberOfMessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) : een batch berichten ophalen (maxi maal 32.)
* [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) : Stel een langere of korte time-out voor onzichtbaarheid in.

In het volgende voor beeld wordt de methode **receiveMessages** gebruikt om vijf berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `for` lus verwerkt. Ook wordt de time-out voor ininzicht ingesteld op vijf minuten voor alle berichten die door deze methode worden geretourneerd.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen:

* `options.numOfMessages` -Een batch berichten ophalen (Maxi maal 32.)
* `options.visibilityTimeout` -Stel een langere of korte time-out voor onzichtbaar in.

In het volgende voor beeld wordt de methode **getMessages** gebruikt om 15 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `for` lus verwerkt. Ook wordt de time-out voor ininzicht ingesteld op vijf minuten voor alle berichten die door deze methode worden geretourneerd.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>De lengte van de wachtrij ophalen

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

De methode [getProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) retourneert meta gegevens over de wachtrij, met inbegrip van het geschatte aantal berichten in de wachtrij.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

De **getQueueMetadata** -methode retourneert meta gegevens over de wachtrij, met inbegrip van het geschatte aantal berichten in de wachtrij.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Wacht rijen weer geven

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Roep [QueueServiceClient. list queues]()aan om een lijst met wacht rijen op te halen. Als u een lijst wilt ophalen die is gefilterd op een specifiek voor voegsel, stelt u [Opties. voor voegsel](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) in de aanroep van **List queues**.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Gebruik **listQueuesSegmented**om een lijst met wacht rijen op te halen. Gebruik **listQueuesSegmentedWithPrefix**om een lijst op te halen die wordt gefilterd op een specifiek voor voegsel.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Als niet alle wacht rijen kunnen worden geretourneerd, geeft `result.continuationToken` u de eerste para meter van **listQueuesSegmented** of de tweede para meter van **listQueuesSegmentedWithPrefix** op om meer resultaten op te halen.

---

## <a name="how-to-delete-a-queue"></a>Een wachtrij verwijderen

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de methode [Delete Queue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) aan voor het object **QueueClient** .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Als u alle berichten uit een wachtrij wilt wissen zonder deze te verwijderen, roept u [clearMessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-)aan.

# <a name="javascript-v2"></a>[Java script v2](#tab/javascript2)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de methode **Delete Queue** aan in het wachtrij object.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Als u alle berichten uit een wachtrij wilt wissen zonder deze te verwijderen, roept u **clearMessages**aan.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van wachtrij opslag hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken.

* Ga naar het [Azure Storage-Team blog][Azure Storage Team Blog] voor meer informatie over wat er nieuw is
* Ga naar de [Azure Storage-client bibliotheek voor de Java script][Azure Storage client library for JavaScript] -opslag plaats op github

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
