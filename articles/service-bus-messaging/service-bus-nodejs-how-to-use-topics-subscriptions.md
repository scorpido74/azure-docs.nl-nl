---
title: Azure Service Bus-onderwerpen gebruiken met het Node.js-pakket azure/service-bus
description: Meer informatie over het gebruik van Service Bus-onderwerpen en -abonnementen in Azure vanuit een Node.js-app met behulp van het azure/service-bus-pakket.'
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: c85b63b4a56e74b0fef9a122ec995b4106496cbe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330443"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Snelstart: Servicebus-onderwerpen en abonnementen gebruiken met Node.js en het azure-sb-pakket
In deze zelfstudie leert u hoe u Node.js-toepassingen maakt om berichten naar een servicebusonderwerp te verzenden en berichten te ontvangen van een Service Bus-abonnement met behulp van het [azure-sb-pakket.](https://www.npmjs.com/package/azure-sb) De voorbeelden zijn geschreven in JavaScript en maken gebruik van `azure-sb` de Node.js [Azure-module](https://www.npmjs.com/package/azure) die intern het pakket gebruikt.

> [!IMPORTANT]
> Het [azure-sb-pakket](https://www.npmjs.com/package/azure-sb) maakt gebruik van [Service Bus REST run-time API's](/rest/api/servicebus/service-bus-runtime-rest). U een snellere ervaring [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) krijgen met behulp van het nieuwe pakket dat gebruik maakt van het snellere [AMQP 1.0-protocol.](service-bus-amqp-overview.md) 
> 
> Zie [Servicebus-onderwerpen en abonnementen gebruiken met Node.js en @azure/service-bus package](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package)voor meer informatie over het nieuwe pakket, anders lees je verder om te zien hoe je het [azure-pakket kunt](https://www.npmjs.com/package/azure) gebruiken.

De hier behandelde scenario's zijn:

- Onderwerpen en abonnementen maken 
- Abonnementsfilters maken 
- Berichten naar een onderwerp verzenden 
- Berichten ontvangen van een abonnement
- Onderwerpen en abonnementen verwijderen 

Zie [Volgende stappen](#next-steps) sectie voor meer informatie over onderwerpen en abonnementen.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [Visual Studio- of MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Volg stappen in de [Quickstart: gebruik de Azure-portal om een servicebusonderwerp en abonnementen op het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om een **naamruimte** voor servicebus te maken en de **verbindingstekenreeks op te**halen.

    > [!NOTE]
    > Je maakt een **onderwerp** en een **abonnement op** het onderwerp door **Node.js** in deze quickstart te gebruiken. 

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken
Maak een lege Node.js-toepassing. Zie [Een Node.js-toepassing maken en implementeren voor]instructies voor het maken van een Node.js-toepassing naar een Azure-website, [Node.js Cloud Service][Node.js Cloud Service] met Windows PowerShell of Website met WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren om Service Bus te gebruiken
Als u Service Bus wilt gebruiken, downloadt u het Azure-pakket node.js. Dit pakket bevat een reeks bibliotheken die communiceren met de Service Bus REST-services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te verkrijgen
1. Open een command-line interface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Navigeer naar de map waar u uw voorbeeldtoepassing hebt gemaakt.
3. Typ **npm installeer azure** in het opdrachtvenster, wat moet resulteren in de volgende uitvoer:

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
3. U de opdracht **ls** handmatig uitvoeren om te controleren of er een **\_knooppuntmodulesmap** is gemaakt. Zoek in die map het **azure-pakket,** dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot Service Bus-onderwerpen.

### <a name="import-the-module"></a>De module importeren
Voeg het volgende toe aan het volgende aan het bestand **server.js** van de toepassing:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Een ServiceBus-verbinding instellen
De Azure-module leest `AZURE_SERVICEBUS_CONNECTION_STRING` de omgevingsvariabele voor de verbindingstekenreeks die u in de eerdere stap hebt verkregen: 'De referenties verkrijgen'. Als deze omgevingsvariabele niet is ingesteld, moet `createServiceBusService`u de accountgegevens opgeven bij het aanroepen.

Zie [Omgevingsvariabelen instellen](../container-instances/container-instances-environment-variables.md#azure-cli-example)voor een voorbeeld van het instellen van de omgevingsvariabelen voor een Azure Cloud Service.



## <a name="create-a-topic"></a>Een onderwerp maken
Met het **object ServiceBusService** u met onderwerpen werken. Met de volgende code wordt een **ServiceBusService-object** gemaakt. Voeg het toe aan de bovenkant van het **server.js-bestand,** nadat de instructie de azure-module wilt importeren:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Als u `createTopicIfNotExists` een aanroep doet op het object **ServiceBusService,** wordt het opgegeven onderwerp geretourneerd (als het bestaat) of wordt een nieuw onderwerp met de opgegeven naam gemaakt. De volgende `createTopicIfNotExists` code wordt gebruikt om `MyTopic`het onderwerp met de naam te maken of verbinding te maken:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

De `createTopicIfNotExists` methode ondersteunt ook extra opties, waarmee u standaardonderwerpinstellingen overschrijven, zoals berichttijd naar live of maximale onderwerpgrootte. 

In het volgende voorbeeld wordt de maximale onderwerpgrootte ingesteld op 5 GB met een tijd van één minuut:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filters
Optionele filterbewerkingen kunnen worden toegepast op bewerkingen die worden uitgevoerd met **ServiceBusService.** Filterbewerkingen kunnen logging omvatten, automatisch opnieuw proberen, enz. Filters zijn objecten die een methode met de handtekening implementeren:

```javascript
function handle (requestOptions, next)
```

Nadat de methode voorbewerking op de `next`aanvraagopties hebt uitgevoerd, wordt een callback aangebeld en wordt een terugbelgesprek uitgevoerd met de volgende handtekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze callback en `returnObject` na verwerking van het (het antwoord van het verzoek naar de server) moet de `finalCallback` callback ofwel een beroep doen op de volgende (als deze bestaat) om door te gaan met het verwerken van andere filters of een beroep te doen op het beëindigen van de serviceaanroep.

Twee filters die logica implementeren voor nieuwe pogingen zijn opgenomen in de Azure SDK voor Node.js: **ExponentialRetryPolicyFilter** en **LinearRetryPolicyFilter**. Met de volgende code wordt een **ServiceBusService-object** gemaakt dat het **ExponentialRetryPolicyFilter**gebruikt:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Abonnementen maken
Onderwerpabonnementen worden ook gemaakt met het object **ServiceBusService.** Abonnementen krijgen de naam en kunnen een optioneel filter hebben dat de set berichten beperkt die worden geleverd in de virtuele wachtrij van het abonnement.

> [!NOTE]
> Standaard zijn abonnementen blijvend totdat ze of het onderwerp waar ze aan zijn gekoppeld, worden verwijderd. Als uw toepassing logica bevat om een abonnement te maken, `getSubscription` moet deze eerst controleren of het abonnement bestaat met behulp van de methode.
>
> U de abonnementen automatisch laten verwijderen door de [eigenschap AutoDeleteOnIdle in te](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle)stellen.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Het filter **MatchAll** is het standaardfilter dat wordt gebruikt wanneer een abonnement wordt gemaakt. Bij gebruik van het **MatchAll**-filter worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. In het volgende voorbeeld wordt een abonnement met de naam AllMessages gemaakt en wordt het standaard **matchall-filter** gebruikt.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U ook filters maken waarmee u onderzoeken welke berichten die naar een onderwerp worden verzonden, moeten worden weergegeven in een specifiek onderwerpabonnement.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is het **SqlFilter**, dat een subset van SQL92 implementeert. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Raadpleeg de syntaxis [van SqlFilter.SqlExpression][SqlFilter.SqlExpression] voor meer informatie over de expressies die met een SQL-filter kunnen worden gebruikt.

Filters kunnen aan een abonnement `createRule` worden toegevoegd met behulp van de methode van het **object ServiceBusService.** Met deze methode u nieuwe filters toevoegen aan een bestaand abonnement.

> [!NOTE]
> Omdat het standaardfilter automatisch wordt toegepast op alle nieuwe abonnementen, moet u eerst het standaardfilter verwijderen of de **MatchAll** alle andere filters die u opgeeft overschrijven. U de standaardregel `deleteRule` verwijderen met de methode van het **object ServiceBusService.**
>
>

In het volgende voorbeeld `HighMessages` wordt een abonnement gemaakt met de naam `messagenumber` **SqlFilter** dat alleen berichten selecteert met een aangepaste eigenschap groter dan 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

In het volgende voorbeeld wordt `LowMessages` ook een abonnement gemaakt met de naam `messagenumber` **SqlFilter** dat alleen berichten selecteert met een eigenschap die kleiner is dan of gelijk is aan 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Wanneer een bericht nu `MyTopic`wordt verzonden naar , `AllMessages` wordt het geleverd aan ontvangers die `HighMessages` `LowMessages` zijn geabonneerd op het onderwerpabonnement en selectief geleverd aan ontvangers die zijn geabonneerd op de en onderwerpabonnementen (afhankelijk van de inhoud van het bericht).

## <a name="how-to-send-messages-to-a-topic"></a>Berichten naar een onderwerp verzenden
Als u een bericht wilt verzenden naar een `sendTopicMessage` servicebusonderwerp, moet uw toepassing de methode van het **servicebusserviceobject** gebruiken.
Berichten die naar servicebus-onderwerpen worden verzonden, zijn **BrokeredMessage-objecten.**
**BrokeredMessage-objecten** hebben een set standaardeigenschappen (zoals `Label` en `TimeToLive`), een woordenboek dat wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen vast te houden, en een hoofdtekst van tekenreeksgegevens. Een toepassing kan de hoofdtekst van het bericht `sendTopicMessage` instellen door een tekenreekswaarde door te geven aan de standaardeigenschappen en de vereiste standaardeigenschappen worden standaardwaarden ingevuld.

In het volgende voorbeeld wordt uitgelegd hoe `MyTopic`u vijf testberichten verzenden naar. De `messagenumber` eigenschapswaarde van elk bericht is afhankelijk van de iteratie van de lus (deze eigenschap bepaalt welke abonnementen het ontvangen):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet aan het aantal berichten dat in een onderwerp wordt gehouden, maar er is een limiet op de totale grootte van de berichten die in het bezit zijn van een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
Berichten worden ontvangen van een `receiveSubscriptionMessage` abonnement met behulp van de methode op het object **ServiceBusService.** Standaard worden berichten uit het abonnement verwijderd terwijl ze worden gelezen. U de optionele `isPeekLock` parameter echter instellen op **true** to read (peek) en het bericht vergrendelen zonder het bericht uit het abonnement te verwijderen.

Het standaardgedrag van het lezen en verwijderen van het bericht als onderdeel van de ontvangstbewerking is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing kan tolereren dat een bericht niet wordt verwerkt wanneer er een fout optreedt. Als u dit gedrag wilt begrijpen, moet u een scenario overwegen waarin de consument het ontvangen verzoek uitgeeft en vervolgens vastloopt voordat het wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als wordt verbruikt, heeft de toepassing, wanneer de toepassing opnieuw wordt opgestart en opnieuw berichten begint te consumeren, het bericht gemist dat voorafgaand aan de crash is verbruikt.

Als `isPeekLock` de parameter is ingesteld op **true,** wordt het ontvangen een bewerking in twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die gemiste berichten niet kunnen tolereren. Wanneer Service Bus een verzoek ontvangt, vindt het het volgende bericht te consumeren, vergrendelt het om te voorkomen dat andere consumenten het ontvangen en stuurt het terug naar de toepassing.
Nadat de toepassing het bericht verwerkt (of betrouwbaar opslaat voor toekomstige verwerking), voltooit het de tweede fase van het ontvangstproces door **deleteMessage-methode** aan te roepen en geeft het bericht door om te verwijderen als parameter. De **methode deleteMessage** markeert het bericht als verbruikt en verwijdert het uit het abonnement.

In het volgende voorbeeld wordt uitgelegd hoe `receiveSubscriptionMessage`berichten kunnen worden ontvangen en verwerkt met behulp van . Het voorbeeld ontvangt en verwijdert eerst een bericht uit het 'LowMessages'-abonnement en ontvangt `isPeekLock` vervolgens een bericht van het abonnement 'HighMessages' met set op true. Vervolgens wordt het bericht `deleteMessage`verwijderd met :

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangertoepassing het bericht om de een of `unlockMessage` andere reden niet kan verwerken, kan deze de methode op het **object ServiceBusService** aanroepen. Deze methode zorgt ervoor dat Service Bus het bericht binnen het abonnement ontgrendelt en beschikbaar maakt om opnieuw te worden ontvangen. In dit geval, hetzij door dezelfde verbruikende toepassing of door een andere verbruikende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat is vergrendeld in het abonnement. Als de toepassing het bericht niet verwerkt voordat de vergrendelingstime-out verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en maakt het beschikbaar om opnieuw te worden ontvangen.

In het geval dat de toepassing crasht `deleteMessage` na het verwerken van het bericht, maar voordat de methode wordt aangeroepen, wordt het bericht opnieuw geleverd aan de toepassing wanneer het opnieuw wordt opgestart. Dit gedrag wordt vaak *At Least Once Processing*genoemd. Dat wil zeggen, elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan tolereren, moet u logica toevoegen aan uw toepassing om dubbele berichtbezorging te verwerken. U de eigenschap **MessageId** van het bericht gebruiken, die constant blijft bij leveringspogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Onderwerpen en abonnementen zijn blijvend, tenzij de [eigenschap autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) is ingesteld en expliciet moet worden verwijderd via de [Azure-portal][Azure portal] of programmatisch.
In het volgende voorbeeld wordt uitgelegd `MyTopic`hoe u het onderwerp met de naam verwijdert:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. In het volgende voorbeeld ziet `HighMessages` u `MyTopic` hoe u een abonnement met de naam uit het onderwerp verwijdert:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu je de basisprincipes van Service Bus-onderwerpen hebt geleerd, volg je deze links om meer te weten te komen.

* Zie [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions].
* API-naslaginformatie voor [SqlFilter][SqlFilter].
* Ga naar de [Azure SDK for Node-repository][Azure SDK for Node] op GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Een Node.js-toepassing maken en implementeren op een Azure-website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

