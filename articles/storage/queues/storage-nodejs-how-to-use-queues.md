---
title: Azure-wachtrij opslag gebruiken vanuit node. js-Azure Storage
description: Meer informatie over het gebruik van Azure Queue-service voor het maken en verwijderen van wacht rijen en het invoegen, ophalen en verwijderen van berichten. Voor beelden geschreven in node. js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: c7b5e679fa47437e7019884317d0ab14792055f3
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465419"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Azure Queue-service gebruiken voor het maken en verwijderen van wacht rijen van node. js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Overzicht
In deze hand leiding wordt beschreven hoe u algemene scenario's uitvoert met behulp van de Microsoft Azure Queue-service. De voor beelden zijn geschreven met behulp van de node. js-API. De gedekte scenario's zijn het **Invoegen**, **inspecteren**, **ophalen**en **verwijderen** van wachtrij berichten, en het **maken en verwijderen van wacht rijen**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Een node. js-toepassing maken
Maak een lege node. js-toepassing. Zie een node. [js-web-app maken in azure app service](../../app-service/app-service-web-get-started-nodejs.md), [een node. js-toepassing bouwen en implementeren in een Azure-Cloud service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) met Windows Power shell of [Visual Studio code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)voor instructies voor het maken van een node. js-toepassing.

## <a name="configure-your-application-to-access-storage"></a>Uw toepassing configureren voor toegang tot opslag
Als u Azure Storage wilt gebruiken, hebt u de Azure Storage SDK voor node. js nodig. Dit omvat een set gebruiks vriendelijke bibliotheken die communiceren met de opslag REST-services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Gebruik node Package Manager (NPM) om het pakket te verkrijgen
1. Gebruik een opdracht regel interface zoals **Power shell** (Windows,) **Terminal** (Mac,) of **bash** (UNIX), navigeer naar de map waarin u uw voorbeeld toepassing hebt gemaakt.
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

3. U kunt de **ls** -opdracht hand matig uitvoeren om te controleren of er een map met **knooppunt \_ modules** is gemaakt. In deze map vindt u het **azure-storage**-pakket. Dit pakket bevat de bibliotheken die u nodig hebt om toegang te krijgen tot opslag.

### <a name="import-the-package"></a>Het pakket importeren
Als u Klad blok of een andere tekst editor gebruikt, voegt u het volgende toe aan het bovenste **server. js** -bestand van de toepassing waarin u opslag wilt gebruiken:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Een Azure Storage verbinding instellen
In de Azure-module worden de omgevings variabelen AZURE \_ Storage \_ -account en de \_ \_ toegangs \_ sleutel voor Azure Storage of de \_ verbindings reeks voor Azure Storage gelezen \_ \_ voor informatie die is vereist om verbinding te maken met uw Azure Storage-account. Als deze omgevings variabelen niet zijn ingesteld, moet u de account gegevens opgeven bij het aanroepen van **createQueueService**.

## <a name="how-to-create-a-queue"></a>Procedure: een wachtrij maken
Met de volgende code wordt een **QueueService** -object gemaakt, waarmee u met wacht rijen kunt werken.

```javascript
var queueSvc = azure.createQueueService();
```

Gebruik de methode **createQueueIfNotExists** , die de opgegeven wachtrij retourneert als deze al bestaat of een nieuwe wachtrij met de opgegeven naam maakt als deze nog niet bestaat.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Als de wachtrij is gemaakt, `result.created` is waar. Als de wachtrij bestaat, `result.created` is onwaar.

### <a name="filters"></a>Filters
Optionele filter bewerkingen kunnen worden toegepast op bewerkingen die worden uitgevoerd met behulp van **QueueService**. Filter bewerkingen kunnen logboek registratie, automatisch opnieuw proberen, enzovoort zijn. Filters zijn objecten die een methode implementeren met de hand tekening:

```javascript
function handle (requestOptions, next)
```

Nadat de voor verwerking voor de aanvraag opties is uitgevoerd, moet de methode ' volgende ' aanroepen door een retour aanroep door te geven met de volgende hand tekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze call back en na het verwerken van de returnObject (de reactie van de aanvraag naar de server), moet de call back worden aangeroepen als deze bestaat om andere filters te verwerken of finalCallback alleen te aanroepen om de aanroepen van de service te volt ooien.

Twee filters die logica implementeren voor nieuwe pogingen zijn opgenomen in de Azure SDK voor Node.js: **ExponentialRetryPolicyFilter** en **LinearRetryPolicyFilter**. Hieronder wordt een **QueueService** -object gemaakt dat gebruikmaakt van de **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedure: een bericht in een wachtrij invoegen
Als u een bericht in een wachtrij wilt invoegen, gebruikt u de methode **createMessage** om een nieuw bericht te maken en dit aan de wachtrij toe te voegen.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Procedure: het volgende bericht bekijken
U kunt het bericht aan de voor kant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de methode **peekMessages** aan te roepen. **PeekMessages** bekijkt standaard één bericht.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Het `result` bevat het bericht.

> [!NOTE]
> Als u **peekMessages** gebruikt wanneer er geen berichten in de wachtrij staan, wordt er geen fout geretourneerd. er worden echter geen berichten weer gegeven.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Procedure: het volgende bericht uit de wachtrij verwijderen
Het verwerken van een bericht is een proces in twee fasen:

1. Het bericht uit de wachtrij verwijderen.
2. Verwijder het bericht.

Gebruik **getMessages**om een bericht in de wachtrij te plaatsen. Hierdoor worden de berichten onzichtbaar in de wachtrij geplaatst, zodat andere clients deze niet kunnen verwerken. Wanneer uw toepassing een bericht heeft verwerkt, roept u **deleteMessage** aan om het te verwijderen uit de wachtrij. In het volgende voor beeld wordt een bericht opgehaald en vervolgens verwijderd:

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
> Standaard wordt een bericht 30 seconden verborgen, waarna het zichtbaar is voor andere clients. U kunt een andere waarde opgeven met `options.visibilityTimeout` with **getMessages**.
> 
> [!NOTE]
> Als u **getMessages** gebruikt wanneer er geen berichten in de wachtrij staan, wordt er geen fout geretourneerd. er worden echter geen berichten weer gegeven.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedure: de inhoud van een bericht in de wachtrij wijzigen
U kunt de inhoud van een bericht in de wachtrij wijzigen met behulp van **updateMessage**. In het volgende voor beeld wordt de tekst van een bericht bijgewerkt:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Procedure: aanvullende opties voor het dequeuing van berichten
Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen:

* `options.numOfMessages`-Een batch berichten ophalen (Maxi maal 32.)
* `options.visibilityTimeout`-Stel een langere of korte time-out voor onzichtbaar in.

In het volgende voor beeld wordt de methode **getMessages** gebruikt om 15 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp van een for-lus. Ook wordt de time-out voor ininzicht ingesteld op vijf minuten voor alle berichten die door deze methode worden geretourneerd.

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

## <a name="how-to-get-the-queue-length"></a>Procedure: de lengte van de wachtrij ophalen
De **getQueueMetadata** retourneert meta gegevens over de wachtrij, met inbegrip van het geschatte aantal berichten in de wachtrij.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Procedure: wacht rijen weer geven
Gebruik **listQueuesSegmented**om een lijst met wacht rijen op te halen. Gebruik **listQueuesSegmentedWithPrefix**om een lijst op te halen die wordt gefilterd op een specifiek voor voegsel.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Als alle wacht rijen niet kunnen worden geretourneerd, `result.continuationToken` kan worden gebruikt als de eerste para meter van **listQueuesSegmented** of de tweede para meter van **listQueuesSegmentedWithPrefix** om meer resultaten op te halen.

## <a name="how-to-delete-a-queue"></a>Procedure: een wachtrij verwijderen
Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de methode **Delete Queue** aan in het wachtrij object.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Als u alle berichten uit een wachtrij wilt wissen zonder deze te verwijderen, gebruikt u **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Procedure: werken met hand tekeningen voor gedeelde toegang
Shared Access signatures (SAS) zijn een veilige manier om nauw keurige toegang tot wacht rijen te bieden zonder uw opslag accountnaam of-sleutels op te geven. SA'S worden vaak gebruikt om beperkte toegang tot uw wacht rijen te bieden, zoals het toestaan van een mobiele app om berichten te verzenden.

Een vertrouwde toepassing, zoals een Cloud service, genereert een SAS met behulp van de **generateSharedAccessSignature** van de **QueueService**en levert deze aan een niet-vertrouwde of semi-vertrouwde toepassing. Een voor beeld: een mobiele app. De SAS wordt gegenereerd op basis van beleid, waarin de begin- en einddatum wordt vermeld voor de geldigheid van de SAS, evenals het toegangsniveau verleend aan de SAS-houder.

In het volgende voor beeld wordt een nieuw beleid voor gedeelde toegang gegenereerd waarmee de SAS-houder berichten kan toevoegen aan de wachtrij en 100 minuten na de tijd kan verlopen.

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

Houd er rekening mee dat de informatie over de host moet worden opgegeven, aangezien deze vereist is wanneer de SAS-houder toegang tot de wachtrij probeert te krijgen.

De client toepassing gebruikt vervolgens de SAS met **QueueServiceWithSAS** om bewerkingen uit te voeren op de wachtrij. In het volgende voor beeld wordt verbinding gemaakt met de wachtrij en wordt een bericht gemaakt.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Omdat de SA'S zijn gegenereerd met toegang toevoegen en er een poging is gedaan om berichten te lezen, bij te werken of te verwijderen, wordt er een fout geretourneerd.

### <a name="access-control-lists"></a>Toegangsbeheerlijsten
U kunt ook een ACL (Access Control List) gebruiken om het toegangsbeleid in te stellen voor een SAS. Dit is handig als u wilt toestaan dat meerdere clients toegang hebben tot de wachtrij, maar een ander toegangs beleid bieden voor elke client.

Een ACL wordt geïmplementeerd met behulp van een matrix van toegangsbeleidregels, met een id die is gekoppeld aan elk beleid. In het volgende voor beeld worden twee beleids regels gedefinieerd. een voor Gebruiker1 en één voor ' voor naam ':

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

In het volgende voor beeld wordt de huidige ACL voor **myqueue**opgehaald, waarna de nieuwe beleids regels worden toegevoegd met behulp van **setQueueAcl**. Deze aanpak biedt u de volgende mogelijkheid:

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

Zodra de ACL is ingesteld, kunt u vervolgens een SAS maken op basis van de ID voor een beleid. In het volgende voorbeeld wordt een nieuwe SAS voor 'gebruiker2' gemaakt:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van wachtrij opslag hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken.

* Ga naar de [blog van het Azure Storage-team][Azure Storage Team Blog].
* Ga naar de [Azure Storage SDK voor knooppunt][Azure Storage SDK for Node] opslagplaats op github.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Een Node.js-web-app maken in Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Een Node.js-toepassing maken en implementeren in een Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
