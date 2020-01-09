---
title: Azure Service Bus-wacht rijen gebruiken in node. js met behulp van het pakket Azure-SB
description: 'Quick Start: informatie over het gebruik van Service Bus wachtrijen in azure vanuit een node. js-app.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 5fa74bdc632154e361fc4d95ed602e4b4d39a198
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462183"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Snelstartgids: Service Bus-wacht rijen in azure gebruiken met node. js en het pakket Azure-SB

> [!div class="op_multi_selector" title1="Programmeertaal" title2="Node. js pakket"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

In deze zelf studie leert u hoe u node. js-toepassingen kunt maken om berichten te verzenden naar en berichten te ontvangen van een Azure Service Bus wachtrij met behulp van het pakket [Azure-SB](https://www.npmjs.com/package/azure-sb) . De voor beelden zijn geschreven in Java script en gebruiken de node. js [Azure-module](https://www.npmjs.com/package/azure) die intern gebruikmaakt van het pakket Azure-SB.

Het [Azure-SB-](https://www.npmjs.com/package/azure-sb) pakket maakt gebruik van [Service Bus rest runtime-api's](/rest/api/servicebus/service-bus-runtime-rest). U kunt een snellere ervaring krijgen met de nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) die gebruikmaakt van het snellere [AMQP 1,0-protocol](service-bus-amqp-overview.md). Zie [service bus-wacht rijen gebruiken met node. js en @azure/service-bus pakket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package)voor meer informatie over het nieuwe pakket. anders gaat u verder met lezen om te zien hoe u het [Azure](https://www.npmjs.com/package/azure) -pakket kunt gebruiken.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) van uw MSDN-abonnee activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om mee te werken, voert u de stappen in het [Azure Portal gebruik uit om een service bus wachtrij](service-bus-quickstart-portal.md) artikel te maken om een wachtrij te maken.
    1. Lees het kort **overzicht** van service bus- **wacht rijen**. 
    2. Maak een Service Bus **naam ruimte**. 
    3. Haal de **Connection String**op. 

        > [!NOTE]
        > In deze zelf studie maakt u een **wachtrij** in de service bus naam ruimte met behulp van node. js. 
 

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken
Maak een lege node. js-toepassing. Zie [een node. js-toepassing maken en implementeren op een Azure-website][Create and deploy a Node.js application to an Azure Website]of [node. js-Cloud service][Node.js Cloud Service] met behulp van Windows Power shell voor instructies over het maken van een node. js-toepassing.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Als u Azure Service Bus wilt gebruiken, moet u het node. js Azure-pakket downloaden en gebruiken. Dit pakket bevat een set bibliotheken die communiceren met de Service Bus REST-services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Gebruik node Package Manager (NPM) om het pakket te verkrijgen
1. Gebruik de **Windows Power shell voor node. js** opdracht venster om naar de map **c:\\\\sbqueues\\WebRole1** te navigeren waarin u de voorbeeld toepassing hebt gemaakt.
2. Typ **NPM Azure installeren** in het opdracht venster. dit moet resulteren in uitvoer zoals in het volgende voor beeld:

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
3. U kunt de **ls**-opdracht handmatig uitvoeren om te controleren of een **node_modules**-map is gemaakt. Zoek in die map naar het **Azure** -pakket, dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot Service Bus wachtrijen.

### <a name="import-the-module"></a>De module importeren
Als u Klad blok of een andere tekst editor gebruikt, voegt u het volgende toe aan de bovenkant van het **server. js** -bestand van de toepassing:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Een Azure Service Bus verbinding instellen
De Azure-module leest de omgevings variabele `AZURE_SERVICEBUS_CONNECTION_STRING` voor het verkrijgen van informatie die nodig is om verbinding te maken met Service Bus. Als deze omgevings variabele niet is ingesteld, moet u de account gegevens opgeven wanneer u `createServiceBusService`aanroept.

Zie de [node. js-webtoepassing met Storage][Node.js Web Application with Storage]voor een voor beeld van het instellen van omgevings variabelen in de [Azure Portal][Azure portal] voor een Azure-website.

## <a name="create-a-queue"></a>Een wachtrij maken
Met het **ServiceBusService** -object kunt u werken met Service bus-wacht rijen. Met de volgende code wordt een **ServiceBusService** -object gemaakt. Voeg het toe aan de bovenkant van het bestand **server. js** , na de instructie voor het importeren van de Azure-module:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Door `createQueueIfNotExists` aan te roepen voor het object **ServiceBusService** , wordt de opgegeven wachtrij geretourneerd (indien aanwezig), of wordt er een nieuwe wachtrij met de opgegeven naam gemaakt. De volgende code maakt gebruik van `createQueueIfNotExists` voor het maken of koppelen van de wachtrij met de naam `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

De methode `createServiceBusService` biedt ook ondersteuning voor extra opties, waarmee u standaard instellingen voor de wachtrij kunt overschrijven, zoals de bericht tijd in een live of maximale wachtrij grootte. In het volgende voor beeld wordt de maximale wachtrij grootte ingesteld op 5 GB en een TTL-waarde (time to Live) van 1 minuut:

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
Optionele filter bewerkingen kunnen worden toegepast op bewerkingen die worden uitgevoerd met behulp van **ServiceBusService**. Filter bewerkingen kunnen logboek registratie, automatisch opnieuw proberen, enzovoort zijn. Filters zijn objecten die een methode implementeren met de hand tekening:

```javascript
function handle (requestOptions, next)
```

Nadat de voor bereiding op de aanvraag opties is uitgevoerd, moet de methode `next`aanroepen, waarbij een call back wordt door gegeven met de volgende hand tekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze call back en na de verwerking van de `returnObject` (de reactie van de aanvraag naar de server), moet de retour aanroep `next` aanroepen als deze bestaat om andere filters te kunnen verwerken of `finalCallback`aan te roepen, waardoor de service aanroep wordt beëindigd.

De Azure SDK voor node. js, `ExponentialRetryPolicyFilter` en `LinearRetryPolicyFilter`bevat twee filters die logica voor nieuwe pogingen implementeren. Met de volgende code maakt u een `ServiceBusService`-object dat gebruikmaakt van de `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u een bericht wilt verzenden naar een Service Bus wachtrij, wordt de methode `sendQueueMessage` door de toepassing aangeroepen voor het **ServiceBusService** -object. Berichten die worden verzonden naar (en ontvangen van) Service Bus-wacht rijen zijn **BrokeredMessage** -objecten en hebben een aantal standaard eigenschappen (zoals **Label** en **TimeToLive**), een woorden lijst die wordt gebruikt voor het opslaan van aangepaste toepassingsspecifieke eigenschappen en een hoofd tekst van wille keurige toepassings gegevens. Een toepassing kan de hoofd tekst van het bericht instellen door een teken reeks als het bericht door te geven. Alle vereiste standaard eigenschappen worden gevuld met standaard waarden.

In het volgende voor beeld ziet u hoe u een test bericht verzendt naar de wachtrij met de naam `myqueue` met behulp van `sendQueueMessage`:

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

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een kapje aan de totale grootte van de berichten die door een wachtrij worden bewaard. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Zie [Service Bus quota's][Service Bus quotas]voor meer informatie over quota's.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Er worden berichten van een wachtrij ontvangen met behulp van de methode `receiveQueueMessage` voor het object **ServiceBusService** . Standaard worden berichten uit de wachtrij verwijderd wanneer ze worden gelezen. u kunt het bericht echter wel lezen (bekijken) en dit vergren delen zonder het uit de wachtrij te verwijderen door de optionele para meter `isPeekLock` in te stellen op **True**.

Het standaard gedrag van het lezen en verwijderen van het bericht als onderdeel van de receive-bewerking is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing niet de verwerking van een bericht kan verdragen wanneer zich een fout voordoet. Om dit gedrag te begrijpen, moet u rekening houden met een scenario waarin de consumer de ontvangst aanvraag uitgeeft en vervolgens vastloopt voordat het proces wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, wordt het bericht dat voor het vastlopen is verbruikt, overgeslagen wanneer de toepassing opnieuw wordt gestart en er opnieuw wordt verbruikt.

Als de para meter `isPeekLock` is ingesteld op **True**, wordt de ontvangst een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of op betrouw bare wijze is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door `deleteMessage` methode aan te roepen en het bericht te verwijderen als een para meter. Met de methode `deleteMessage` wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

In het volgende voor beeld ziet u hoe u berichten ontvangt en verwerkt met behulp van `receiveQueueMessage`. In het voor beeld wordt eerst een bericht ontvangen en verwijderd. Vervolgens ontvangt u een bericht met `isPeekLock` ingesteld op **waar**en vervolgens wordt het bericht met `deleteMessage`verwijderd:

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
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan deze de `unlockMessage`-methode aanroepen voor het object **ServiceBusService** . het zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), wordt het bericht automatisch door Service Bus ontgrendeld en wordt het beschikbaar om opnieuw te worden ontvangen.

In het geval dat de toepassing vastloopt na het verwerken van het bericht, maar voordat de `deleteMessage` methode wordt aangeroepen, wordt het bericht opnieuw aan de toepassing geleverd wanneer het opnieuw wordt gestart. Deze aanpak wordt vaak ten *minste*eenmaal verwerkt, dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan verdragen, moeten toepassings ontwikkelaars extra logica toevoegen aan de toepassing om dubbele bericht bezorging af te handelen. Dit wordt vaak bereikt met de **MessageId** -eigenschap van het bericht, die constant blijft tijdens bezorgings pogingen.

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende bronnen voor meer informatie over wacht rijen.

* [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]
* [Azure SDK voor knooppunt][Azure SDK for Node] opslagplaats op github
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
