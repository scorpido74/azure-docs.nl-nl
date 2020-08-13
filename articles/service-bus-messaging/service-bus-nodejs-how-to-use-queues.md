---
title: Azure Service Bus-wachtrijen in Node.js gebruiken met behulp van het azure-sb-pakket
description: Leer hoe u Node.js-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Azure Service Bus-wachtrij met behulp van het azure-sb-pakket.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-javascript
ms.openlocfilehash: ba0fd6cf7214beeaca9e93b1fe003a144c247cb5
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88076996"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Quickstart: Service Bus-wachtrijen in Azure gebruiken met Node.js en het azure-sb-pakket
In deze zelfstudie leert u hoe u Node.js-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Azure Service Bus-wachtrij met behulp van het [azure-sb-pakket](https://www.npmjs.com/package/azure-sb). De voorbeelden zijn geschreven in JavaScript en maken gebruik van de [Azure-module](https://www.npmjs.com/package/azure) van Node.js, die intern gebruikmaakt van het azure-sb-pakket.

> [!IMPORTANT]
> Het [azure-sb](https://www.npmjs.com/package/azure-sb)-pakket maakt gebruik van [Service Bus REST runtime-API's](/rest/api/servicebus/service-bus-runtime-rest). U kunt een snellere ervaring verkrijgen met het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus), dat gebruikmaakt van het snellere [AMQP 1.0-protocol](service-bus-amqp-overview.md). 
> 
> Zie [Service Bus-wachtrijen gebruiken met Node.js en @azure/service-bus-pakket](./service-bus-nodejs-how-to-use-queues-new-package.md) voor meer informatie over het nieuwe pakket, of blijf lezen om te zien hoe u het [azure](https://www.npmjs.com/package/azure)-pakket kunt gebruiken.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [De Azure-portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken.
    1. Lees het beknopte **overzicht** van Service Bus-**wachtrijen**. 
    2. Maak een Service Bus-**naamruimte**. 
    3. Haal de **verbindingsreeks** op. 

        > [!NOTE]
        > In deze zelfstudie maakt u een **wachtrij** in de Service Bus-naamruimte met behulp van Node.js. 
 

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken
Maak een lege Node.js-toepassing. Zie [Een Node.js-toepassing maken en implementeren op een Azure-website][Create and deploy a Node.js application to an Azure Website] of [Node.js-cloudservice][Node.js Cloud Service] met Windows PowerShell voor instructies over het maken van een Node.js-toepassing.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Download het Azure-pakket van Node.js om Azure Service Bus te gebruiken. Dit pakket bevat een set bibliotheken die communiceren met de Service Bus REST-services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te verkrijgen
1. Gebruik het opdrachtvenster van **Windows PowerShell voor Node.js** om naar de map **c:\\node\\sbqueues\\WebRole1** te gaan waarin u de voorbeeldtoepassing hebt gemaakt.
2. Typ **npm install azure** in het opdrachtvenster. Dit zou een uitvoer te zien moeten geven als in het volgende voorbeeld:

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
3. U kunt de **ls**-opdracht handmatig uitvoeren om te controleren of een **node_modules**-map is gemaakt. In deze map zoekt u het **azure**-pakket, dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot Service Bus-wachtrijen.

### <a name="import-the-module"></a>De module importeren
Gebruik Kladblok of een andere teksteditor om het volgende toe te voegen bovenaan het bestand **server.js** van de toepassing:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Een Azure Service Bus-verbinding instellen
De Azure-module leest de omgevingsvariabele `AZURE_SERVICEBUS_CONNECTION_STRING` om informatie te verkrijgen die nodig is om verbinding te maken met Service Bus. Als deze omgevingsvariabele niet is ingesteld, moet u de accountgegevens opgeven bij het aanroepen van `createServiceBusService`.

Zie [Node.js-webtoepassing met opslag][Node.js Web Application with Storage] voor een voorbeeld van het instellen van omgevingsvariabelen in de [Azure-portal][Azure portal] voor een Azure-website.

## <a name="create-a-queue"></a>Een wachtrij maken
Met het **ServiceBusService**-object kunt u werken met Service Bus-wachtrijen. Met de volgende code wordt een **ServiceBusClient**-object gemaakt. Voeg deze toe boven aan het **server.js**-bestand, na de instructie voor het importeren van de Azure-module:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Als u `createQueueIfNotExists` aanroept op het **ServiceBusService**-object, wordt de opgegeven wachtrij geretourneerd (als deze bestaat), of wordt er een nieuwe wachtrij gemaakt met de opgegeven naam. De volgende code maakt gebruik van `createQueueIfNotExists` om de wachtrij met de naam `myqueue` te maken of er verbinding mee te maken:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

De methode `createServiceBusService` ondersteunt ook extra opties waarmee u standaardinstellingen voor de wachtrij kunt overschrijven, zoals de Time to Live van het bericht of de maximale grootte van de wachtrij. In het volgende voorbeeld wordt de maximale wachtrijgrootte ingesteld op 5 GB en de Time to Live-waarde op 1 minuut:

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
Er kunnen optionele filterbewerkingen worden toegepast op bewerkingen die worden uitgevoerd met behulp van **ServiceBusService**. Filterbewerkingen kunnen logboekregistratie, automatische nieuwe pogingen, enzovoort bevatten. Filters zijn objecten die een methode implementeren met de handtekening:

```javascript
function handle (requestOptions, next)
```

Na uitvoering van de voorbewerkingen voor de aanvraagopties, moet de methode `next` aanroepen en een callback doorgeven met de volgende handtekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze callback, en na verwerking van het `returnObject` (de reactie van de aanvraag op de server), moet de callback `next` aanroepen (als dit bestaat) om door te gaan met het verwerken van andere filters, of `finalCallback` aanroepen om te stoppen met service-aanroepen.

Bij de Azure-SDK voor Node.js zijn twee filters opgenomen waarmee logica voor opnieuw proberen wordt geïmplementeerd: `ExponentialRetryPolicyFilter` en `LinearRetryPolicyFilter`. Met de volgende code wordt een `ServiceBusService`-object gemaakt dat gebruikmaakt van `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Als u een bericht naar een Service Bus-wachtrij wilt verzenden, wordt door de toepassing de `sendQueueMessage`-methode voor het **ServiceBusService**-object aangeroepen. Berichten die worden verzonden naar (en ontvangen van) Service Bus-wachtrijen zijn **BrokeredMessage**-objecten. Deze hebben enkele standaardeigenschappen (zoals **Label** en **TimeToLive**), een woordenlijst die wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren en een hoofdtekst met willekeurige toepassingsgegevens. De hoofdtekst van het bericht kan door de toepassing worden ingesteld door een tekenreeks als het bericht door te geven. Eventuele vereiste standaardeigenschappen worden gevuld met standaardwaarden.

In het volgende voorbeeld ziet u hoe u een testbericht verzendt naar de wachtrij met de naam `myqueue` met behulp van `sendQueueMessage`:

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

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Zie [Service Bus-quota][Service Bus quotas] voor meer informatie over quota.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Berichten worden van een wachtrij ontvangen met behulp van de methode `receiveQueueMessage` voor het **ServiceBusService**-object. Standaard worden berichten uit de wachtrij verwijderd wanneer ze worden gelezen. U kunt het bericht echter wel lezen (er een blik op werpen) en vergrendelen zonder het uit de wachtrij te verwijderen door de optionele parameter `isPeekLock` in te stellen op **true**.

Het standaardgedrag van het lezen en verwijderen van het bericht als onderdeel van de ontvangstbewerking is het eenvoudigste model, en werkt het beste voor scenario's waarin wordt getolereerd dat een bericht niet wordt verwerkt als er een fout optreedt. Neem bijvoorbeeld een scenario waarin de verwerkende app de ontvangstaanvraag indient en vervolgens vastloopt voordat deze wordt verwerkt. Omdat Service Bus het bericht als verwerkt heeft gemarkeerd, ontbreekt het bericht dat vóór het vastlopen is verwerkt wanneer de toepassing opnieuw wordt gestart en het verwerken van berichten opnieuw begint.

Als de parameter `isPeekLock` wordt ingesteld op **true**, wordt de ontvangst een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen gemiste berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing het bericht heeft verwerkt (of het veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door de methode `deleteMessage` aan te roepen en het te verwijderen bericht door te geven als een parameter. Met de methode `deleteMessage` wordt het bericht gemarkeerd als verwerkt en wordt het uit de wachtrij verwijderd.

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met behulp van `receiveQueueMessage`. In het voorbeeld wordt eerst een bericht ontvangen en verwijderd. Vervolgens wordt een bericht ontvangen waarbij `isPeekLock` is ingesteld op **true**. Vervolgens wordt het bericht verwijderd met `deleteMessage`:

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
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan deze de methode `unlockMessage` aanroepen op het **ServiceBusService**-object. Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde toepassing die het gebruikt of door een andere toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

Als de toepassing vastloopt na het verwerken van het bericht, maar voordat de methode `deleteMessage` wordt aangeroepen, wordt het bericht opnieuw bij de toepassing bezorgd wanneer deze opnieuw wordt gestart. Deze benadering wordt vaak *Ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de eigenschap **MessageId** van het bericht, dat gelijk blijft bij meerdere bezorgingspogingen.

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor meer informatie over wachtrijen.

* [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions]
* Opslagplaats [Azure SDK voor Node][Azure SDK for Node] op GitHub
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/quickstart-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md