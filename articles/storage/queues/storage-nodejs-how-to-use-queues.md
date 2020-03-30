---
title: Azure Queue-opslag gebruiken vanuit Node.js - Azure Storage
description: Meer informatie over het gebruik van de Azure Queue-service om wachtrijen te maken en te verwijderen en berichten in te voegen, op te halen en te verwijderen. Monsters geschreven in Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7abcad03678131668700f5d2c64b9c971081cb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060937"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Azure Queue Service gebruiken om wachtrijen te maken en te verwijderen vanuit Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Overzicht
In deze handleiding ziet u hoe u veelvoorkomende scenario's uitvoert met de Microsoft Azure Queue-service. De voorbeelden worden geschreven met behulp van de Node.js API. De behandelde scenario's omvatten **het invoegen,** **gluren,** **krijgen**en **verwijderen van** wachtrijberichten, evenals het maken en verwijderen **van wachtrijen**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Een node.js-toepassing maken
Maak een lege Node.js-toepassing. Zie [Een Node.js-webapp maken in Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [Een Node.js-toepassing maken en implementeren voor](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) instructies voor het maken van een Node.js-toepassing met Windows PowerShell of Visual Studio [Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Uw toepassing configureren voor toegang tot opslag
Als u Azure-opslag wilt gebruiken, hebt u de Azure Storage SDK voor Node.js nodig, die een reeks gemaksbibliotheken bevat die communiceren met de REST-opslagservices.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te verkrijgen
1. Gebruik een commandline-interface zoals **PowerShell** (Windows,) **Terminal** (Mac,) of **Bash** (Unix) en navigeer naar de map waar u uw voorbeeldtoepassing hebt gemaakt.
2. Typ **npm install azure-storage** in het opdrachtvenster. De uitvoer van de opdracht is vergelijkbaar met het volgende voorbeeld.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. U de opdracht **ls** handmatig uitvoeren om te controleren of er een **\_knooppuntmodulesmap** is gemaakt. In deze map vindt u het **azure-storage**-pakket. Dit pakket bevat de bibliotheken die u nodig hebt om toegang te krijgen tot opslag.

### <a name="import-the-package"></a>Het pakket importeren
Voeg het volgende toe aan het bestand **server.js** van de toepassing waar u opslag wilt gebruiken:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Een Azure-opslagverbinding instellen
De azure-module leest de\_omgevingsvariabelen AZURE\_Storage ACCOUNT en\_\_AZURE\_\_STORAGE\_ACCESS\_KEY of AZURE STORAGE CONNECTION STRING voor informatie die nodig is om verbinding te maken met uw Azure-opslagaccount. Als deze omgevingsvariabelen niet zijn ingesteld, moet u de accountgegevens opgeven wanneer u **createQueueService aanroept.**

## <a name="how-to-create-a-queue"></a>How To: Een wachtrij maken
Met de volgende code wordt een **QueueService-object** gemaakt, waarmee u met wachtrijen werken.

```javascript
var queueSvc = azure.createQueueService();
```

Gebruik de methode **createQueueIfNotExist,** die de opgegeven wachtrij retourneert als deze al bestaat of een nieuwe wachtrij met de opgegeven naam maakt als deze nog niet bestaat.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Als de wachtrij `result.created` is gemaakt, is waar. Als de wachtrij `result.created` bestaat, is dit niet waar.

### <a name="filters"></a>Filters
Optionele filterbewerkingen kunnen worden toegepast op bewerkingen die worden uitgevoerd met **QueueService.** Filterbewerkingen kunnen logging omvatten, automatisch opnieuw proberen, enz. Filters zijn objecten die een methode met de handtekening implementeren:

```javascript
function handle (requestOptions, next)
```

Na het doen van de voorbewerking op de aanvraagopties, moet de methode "volgende" aanroepen om een callback te maken met de volgende handtekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze callback en na het verwerken van het returnObject (het antwoord van het verzoek naar de server) moet de callback ofwel worden aanroepen als het bestaat om door te gaan met het verwerken van andere filters of gewoon een beroep te doen op finalCallback om de service te beëindigen Aanroep.

Twee filters die logica implementeren voor nieuwe pogingen zijn opgenomen in de Azure SDK voor Node.js: **ExponentialRetryPolicyFilter** en **LinearRetryPolicyFilter**. Met het volgende wordt een **object QueueService** gemaakt dat het **ExponentialRetryPolicyFilter**gebruikt:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>How To: Een bericht invoegen in een wachtrij
Als u een bericht in een wachtrij wilt invoegen, gebruikt u de methode **createMessage** om een nieuw bericht te maken en toe te voegen aan de wachtrij.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>How To: Gluren bij het volgende bericht
U het bericht voor in een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de **methode peekMessages** aan te roepen. **PeekMessages** gluurt standaard naar één bericht.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Het `result` bevat het bericht.

> [!NOTE]
> Als **u peekMessages** gebruikt wanneer er geen berichten in de wachtrij staan, wordt er geen fout geretourneerd, maar worden er geen berichten geretourneerd.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>How To: Dequeue the Next Message
Het verwerken van een bericht is een proces in twee fasen:

1. Zet het bericht in de wachtrij.
2. Verwijder het bericht.

Als u een bericht wilt dewachtrijen, gebruikt u **getMessages**. Dit maakt de berichten onzichtbaar in de wachtrij, zodat geen andere clients ze kunnen verwerken. Zodra uw toepassing een bericht heeft verwerkt, roept u **DeleteMessage** op om het bericht uit de wachtrij te verwijderen. In het volgende voorbeeld wordt een bericht ontvangen en wordt het verwijderd:

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

> [!NOTE]
> Standaard wordt een bericht slechts 30 seconden verborgen, waarna het zichtbaar is voor andere clients. U een andere `options.visibilityTimeout` waarde opgeven door met **getMessages**te gebruiken.
> 
> [!NOTE]
> Als **u getMessages** gebruikt wanneer er geen berichten in de wachtrij staan, wordt er geen fout geretourneerd, maar worden er geen berichten geretourneerd.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>How To: De inhoud van een bericht in de wachtrij wijzigen
U de inhoud van een bericht in de wachtrij wijzigen met behulp van **updateMessage**. In het volgende voorbeeld wordt de tekst van een bericht bijgewerkt:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>How To: Extra opties voor het dequeuleren van berichten
Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij aanpassen:

* `options.numOfMessages`- Een batch met berichten ophalen (tot 32.)
* `options.visibilityTimeout`- Stel een langere of kortere time-out voor onzichtbaarheid in.

In het volgende voorbeeld wordt de methode **getMessages** gebruikt om 15 berichten in één gesprek te ontvangen. Vervolgens verwerkt het elk bericht met behulp van een voor lus. Het stelt ook de onzichtbaarheid time-out op vijf minuten voor alle berichten geretourneerd door deze methode.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
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

## <a name="how-to-get-the-queue-length"></a>How To: De wachtrijlengte oppakken
**Metagegevens van getQueueMetadata** retourneert metagegevens over de wachtrij, inclusief het geschatte aantal berichten dat in de wachtrij staat.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>How To: Wachtrijen aanbieden
Als u een lijst met wachtrijen wilt ophalen, gebruikt u **listQueuesSegmented**. Als u een lijst wilt ophalen die is gefilterd door een specifiek voorvoegsel, gebruikt u **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Als alle wachtrijen `result.continuationToken` niet kunnen worden geretourneerd, kan worden gebruikt als de eerste parameter van **listQueuesSegmented** of de tweede parameter van **listQueuesSegmentedWithPrefix** om meer resultaten op te halen.

## <a name="how-to-delete-a-queue"></a>How To: Een wachtrij verwijderen
Als u een wachtrij en alle berichten in deze wachtrij wilt verwijderen, roept u de methode **deleteQueue** op het wachtrijobject aan.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Als u alle berichten uit een wachtrij wilt wissen zonder deze te verwijderen, gebruikt u **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Hoe: Werken met handtekeningen voor gedeelde toegang
SAS (Shared Access Signatures) is een veilige manier om gedetailleerde toegang tot wachtrijen te bieden zonder de naam of sleutels van uw opslagaccount op te geven. SAS wordt vaak gebruikt om beperkte toegang tot uw wachtrijen te bieden, zoals het toestaan van een mobiele app om berichten in te dienen.

Een vertrouwde toepassing, zoals een cloudservice, genereert een SAS met de **generateSharedAccessSignature** van de **QueueService**en biedt deze aan een niet-vertrouwde of semi-vertrouwde toepassing. Bijvoorbeeld een mobiele app. De SAS wordt gegenereerd op basis van beleid, waarin de begin- en einddatum wordt vermeld voor de geldigheid van de SAS, evenals het toegangsniveau verleend aan de SAS-houder.

In het volgende voorbeeld wordt een nieuw beleid voor gedeelde toegang gegenereerd waarmee de SAS-houder berichten aan de wachtrij kan toevoegen en 100 minuten na het maken van de wachtrij verloopt.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Houd er rekening mee dat de hostinformatie ook moet worden verstrekt, omdat dit vereist is wanneer de SAS-houder probeert toegang te krijgen tot de wachtrij.

De clienttoepassing gebruikt vervolgens de SAS met **QueueServiceWithSAS** om bewerkingen uit te voeren tegen de wachtrij. In het volgende voorbeeld wordt verbinding maakt met de wachtrij en wordt een bericht weergegeven.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Aangezien de SAS is gegenereerd met add-toegang, als een poging is gedaan om berichten te lezen, bij te werken of te verwijderen, wordt een fout geretourneerd.

### <a name="access-control-lists"></a>Toegangsbeheerlijsten
U kunt ook een ACL (Access Control List) gebruiken om het toegangsbeleid in te stellen voor een SAS. Dit is handig als u meerdere clients toegang wilt geven tot de wachtrij, maar voor elke client verschillende toegangsbeleidsregels wilt bieden.

Een ACL wordt geïmplementeerd met behulp van een matrix van toegangsbeleidregels, met een id die is gekoppeld aan elk beleid. In het volgende voorbeeld worden twee beleidsregels gedefinieerd; een voor 'user1' en een voor 'user2':

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

In het volgende voorbeeld wordt de huidige ACL voor **myqueue**opgebruikt en wordt het nieuwe beleid toegevoegd met **setQueueAcl**. Deze aanpak biedt u de volgende mogelijkheid:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Zodra de ACL is ingesteld, u vervolgens een SAS maken op basis van de ID voor een beleid. In het volgende voorbeeld wordt een nieuwe SAS voor 'gebruiker2' gemaakt:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van wachtrijopslag hebt geleerd, volgt u deze koppelingen om meer te weten te komen over complexere opslagtaken.

* Ga naar de [blog van het Azure Storage-team][Azure Storage Team Blog].
* Ga naar de [Azure Storage SDK for Node-repository][Azure Storage SDK for Node] op GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Een Node.js-web-app maken in Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Een Node.js-toepassing maken en implementeren in een Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
