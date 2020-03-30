---
title: Azure Service Bus-wachtrijen gebruiken in Node.js met azure-sb-pakket
description: Meer informatie over het maken van Node.js-toepassingen om berichten naar berichten te verzenden en te ontvangen vanuit een Azure Service Bus-wachtrij met behulp van het azure-sb-pakket.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 7ee3939c1a1b450f2458267ab0b70e3924a4869b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330597"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Snelstart: Servicebuswachtrijen gebruiken in Azure met Node.js en het azure-sb-pakket
In deze zelfstudie leert u hoe u Node.js-toepassingen maakt om berichten naar berichten te verzenden en te ontvangen vanuit een Azure Service Bus-wachtrij met behulp van het [azure-sb-pakket.](https://www.npmjs.com/package/azure-sb) De voorbeelden zijn geschreven in JavaScript en maken gebruik van de Node.js [Azure-module](https://www.npmjs.com/package/azure) die intern gebruik maakt van het azure-sb-pakket.

> [!IMPORTANT]
> Het [azure-sb-pakket](https://www.npmjs.com/package/azure-sb) maakt gebruik van [Service Bus REST run-time API's](/rest/api/servicebus/service-bus-runtime-rest). U een snellere ervaring [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) met behulp van de nieuwe die gebruik maakt van de snellere [AMQP 1.0 protocol](service-bus-amqp-overview.md). 
> 
> Zie [Servicebus-wachtrijen gebruiken met Node.js en @azure/service-bus package](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package)voor meer informatie over het nieuwe pakket, anders lees je verder om te zien hoe je het [azure-pakket](https://www.npmjs.com/package/azure) gebruiken.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Als u geen wachtrij hebt om mee te werken, voert u stappen uit in de [Azure-portal gebruiken om een wachtrijartikel voor servicebus te maken](service-bus-quickstart-portal.md) om een wachtrij te maken.
    1. Lees het snelle **overzicht** van wachtrijen voor **servicebussen.** 
    2. Een **naamruimte**voor servicebus maken . 
    3. Haal de **verbindingstekenreeks**. 

        > [!NOTE]
        > U maakt een **wachtrij** in de naamruimte servicebus met Behulp van Node.js in deze zelfstudie. 
 

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken
Maak een lege Node.js-toepassing. Zie [Een Node.js-toepassing maken en implementeren voor][Create and deploy a Node.js application to an Azure Website]instructies voor het maken van een Node.js-toepassing naar een Azure-website of [Node.js Cloud Service][Node.js Cloud Service] met Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren om Service Bus te gebruiken
Als u Azure Service Bus wilt gebruiken, downloadt en gebruikt u het Azure-pakket Node.js. Dit pakket bevat een reeks bibliotheken die communiceren met de Service Bus REST-services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te verkrijgen
1. Gebruik het opdrachtvenster **Windows PowerShell voor Node.js** om naar de map **c:\\node\\sbqueues\\WebRole1** te navigeren waarin u uw voorbeeldtoepassing hebt gemaakt.
2. Typ **npm installeer azure** in het opdrachtvenster, wat moet resulteren in uitvoer vergelijkbaar met het volgende voorbeeld:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. U kunt de **ls**-opdracht handmatig uitvoeren om te controleren of een **node_modules**-map is gemaakt. Zoek in die map het **azure-pakket,** dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot wachtrijen voor servicebus.

### <a name="import-the-module"></a>De module importeren
Voeg het volgende toe aan het volgende aan het bestand **server.js** van de toepassing:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Een Azure Service Bus-verbinding instellen
De Azure-module leest `AZURE_SERVICEBUS_CONNECTION_STRING` de omgevingsvariabele om informatie te verkrijgen die nodig is om verbinding te maken met Service Bus. Als deze omgevingsvariabele niet is ingesteld, moet u `createServiceBusService`de accountgegevens opgeven bij het aanroepen.

Zie [Node.js Web Application with Storage][Node.js Web Application with Storage]voor een voorbeeld van het instellen van de omgevingsvariabelen in de [Azure-portal][Azure portal] voor een Azure-website.

## <a name="create-a-queue"></a>Een wachtrij maken
Met het **servicebusserviceobject** u werken met wachtrijen voor servicebussen. Met de volgende code wordt een **ServiceBusService-object** gemaakt. Voeg het toe aan de bovenkant van het **server.js-bestand,** nadat de instructie de Azure-module wilt importeren:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Door `createQueueIfNotExists` het object **ServiceBusService** aan te roepen, wordt de opgegeven wachtrij geretourneerd (als deze bestaat) of wordt een nieuwe wachtrij met de opgegeven naam gemaakt. De volgende `createQueueIfNotExists` code wordt gebruikt om `myqueue`de wachtrij met de naam te maken of verbinding te maken:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

De `createServiceBusService` methode ondersteunt ook extra opties, waarmee u standaardwachtrijinstellingen overschrijven, zoals berichttijd naar live of maximale wachtrijgrootte. In het volgende voorbeeld wordt de maximale wachtrijgrootte ingesteld op 5 GB en een tijd van 1 minuut om te leven:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filters
Optionele filterbewerkingen kunnen worden toegepast op bewerkingen die worden uitgevoerd met **ServiceBusService.** Filterbewerkingen kunnen logging omvatten, automatisch opnieuw proberen, enz. Filters zijn objecten die een methode met de handtekening implementeren:

```javascript
function handle (requestOptions, next)
```

Na het doen van de pre-processing op `next`de aanvraag opties, moet de methode aanroepen , het passeren van een callback met de volgende handtekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze callback en `returnObject` na het verwerken van het antwoord van het `next` verzoek naar de server, moet `finalCallback`de callback zich aanroepen als het bestaat om door te gaan met het verwerken van andere filters, of een beroep te doen op de aanroep van de service.

Twee filters die logica voor het opnieuw proberen implementeren, `ExponentialRetryPolicyFilter` zijn `LinearRetryPolicyFilter`opgenomen in de Azure SDK voor Node.js en . Met de volgende `ServiceBusService` code wordt `ExponentialRetryPolicyFilter`een object gemaakt dat de:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u een bericht naar een wachtrij `sendQueueMessage` voor servicebus wilt verzenden, roept uw toepassing de methode op het **ServiceBusService-object.** Berichten die naar wachtrijen voor servicebus worden verzonden (en ontvangen) zijn **BrokeredMessage-objecten** en hebben een reeks standaardeigenschappen (zoals **Label** en **TimeToLive),** een woordenboek dat wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bevatten en een reeks willekeurige toepassingsgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door een tekenreeks als bericht door te geven. Alle vereiste standaardeigenschappen worden gevuld met standaardwaarden.

In het volgende voorbeeld wordt uitgelegd hoe u `myqueue` `sendQueueMessage`een testbericht verzendt naar de wachtrij met de naam :

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten dat in een wachtrij wordt gehouden, maar er is een limiet op de totale grootte van de berichten die in een wachtrij worden bewaard. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Zie [ServiceBus-quota voor][Service Bus quotas]meer informatie over quota.

## <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij
Berichten worden ontvangen vanuit een `receiveQueueMessage` wachtrij met de methode op het object **ServiceBusService.** Standaard worden berichten tijdens het lezen uit de wachtrij verwijderd. U het bericht echter lezen (gluren) en vergrendelen zonder het bericht `isPeekLock` uit de wachtrij te verwijderen door de optionele parameter in te stellen op **true.**

Het standaardgedrag van het lezen en verwijderen van het bericht als onderdeel van de ontvangstbewerking is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing kan tolereren dat een bericht niet wordt verwerkt wanneer er een fout optreedt. Als u dit gedrag wilt begrijpen, moet u een scenario overwegen waarin de consument het ontvangen verzoek uitgeeft en vervolgens vastloopt voordat het wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als wordt verbruikt, heeft de toepassing het bericht dat vóór de crash is verbruikt, gemist wanneer de toepassing opnieuw wordt opgestart en berichten opnieuw begint te consumeren.

Als `isPeekLock` de parameter is ingesteld op **true,** wordt het ontvangen een tweetrapsbewerking, waardoor het mogelijk is om toepassingen te ondersteunen die ontbrekende berichten niet kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing is voltooid verwerking van het bericht (of slaat het betrouwbaar voor toekomstige `deleteMessage` verwerking), het voltooit de tweede fase van het ontvangstproces door aanroepen methode en het verstrekken van het bericht te worden verwijderd als een parameter. De `deleteMessage` methode markeert het bericht als wordt verbruikt en verwijdert het uit de wachtrij.

In het volgende voorbeeld wordt uitgelegd hoe `receiveQueueMessage`u berichten ontvangen en verwerken met behulp van. Het voorbeeld ontvangt en verwijdert eerst een bericht `isPeekLock` en ontvangt vervolgens een bericht `deleteMessage`met set op **true**en verwijdert vervolgens het bericht met :

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangertoepassing het bericht om de een of `unlockMessage` andere reden niet kan verwerken, kan deze de methode op het **object ServiceBusService** aanroepen. Het zal ervoor zorgen dat Service Bus om het bericht te ontgrendelen in de wachtrij en maken het beschikbaar om opnieuw te worden ontvangen, hetzij door dezelfde verbruikende toepassing of door een andere verbruikende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat is vergrendeld in de wachtrij, en als de toepassing het bericht niet verwerkt voordat de vergrendelingstime-out verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te ontvangen.

In het geval dat de toepassing crasht `deleteMessage` na het verwerken van het bericht, maar voordat de methode wordt aangeroepen, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Deze aanpak wordt vaak *At Least Once Processing*genoemd, dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan tolereren, moeten toepassingsontwikkelaars extra logica toevoegen aan hun toepassing om dubbele berichtbezorging te verwerken. Het wordt vaak bereikt met behulp van de eigenschap **MessageId** van het bericht, dat constant blijft tijdens leveringspogingen.

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie over wachtrijen.

* [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]
* [Azure SDK voor Knooppuntrepository][Azure SDK for Node] op GitHub
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
