---
title: Azure Service Bus-onderwerpen gebruiken met het Node.js-pakket azure/service-bus
description: Leer hoe u Service Bus-onderwerpen en -abonnementen in Azure kunt gebruiken vanuit een Node.js app met behulp van het pakket azure/service-bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: d4b382a0cf857f9cfe1065815e9b07b8260023a8
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339790"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Quickstart: Service Bus-onderwerpen en-abonnementen gebruiken met Node. js en het pakket azure-sb
In deze zelfstudie leert u hoe u Node.js toepassingen kunt maken om berichten te versturen naar een Service Bus-onderwerp en berichten te ontvangen van een Service Bus-abonnement met behulp van het [azure-sb](https://www.npmjs.com/package/azure-sb)-pakket. De voorbeelden zijn geschreven in JavaScript en maken gebruik van de [Azure-module](https://www.npmjs.com/package/azure) Node.js, die intern het `azure-sb`-pakket gebruikt.

> [!IMPORTANT]
> Het [azure-sb](https://www.npmjs.com/package/azure-sb)-pakket maakt gebruik van [Service Bus REST runtime-API's](/rest/api/servicebus/service-bus-runtime-rest). U kunt een snellere ervaring verkrijgen met het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-pakket, dat gebruikmaakt van het snellere [AMQP 1.0-protocol](service-bus-amqp-overview.md). 
> 
> Zie [Service Bus-onderwerpen en-abonnementen gebruiken met Node.js en @azure/service-bus-pakket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package)als u meer wilt weten over het nieuwe pakket, of blijf lezen om te zien hoe u het [azure](https://www.npmjs.com/package/azure)-pakket kunt gebruiken.

De hier behandelde scenario's zijn:

- Onderwerpen en abonnementen maken 
- Abonnementsfilters maken 
- Berichten verzenden naar een onderwerp 
- Berichten van een abonnement ontvangen
- Onderwerpen en abonnementen verwijderen 

Zie de sectie [Volgende stappen](#next-steps) voor meer informatie over onderwerpen en abonnementen.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Volg de stappen in de [Quickstart: De Azure-portal gebruiken om een Service Bus-onderwerp en abonnementen te maken voor het onderwerp](service-bus-quickstart-topics-subscriptions-portal.md) om een Service Bus-**naamruimte** te maken en de **verbindingsreeks** op te halen.

    > [!NOTE]
    > In deze Quick Start maakt u een **onderwerp** en een **abonnement** op het onderwerp door **Node.js** te gebruiken. 

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken
Maak een lege Node.js-toepassing. Zie voor instructies over het maken van een Node.js-toepassing [Een Node.js-toepassing maken en implementeren op een Azure-website], [Node.js-cloudservice][Node.js Cloud Service] met Windows PowerShell of website met WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Download het Node.js Azure-pakket om Service Bus te gebruiken. Dit pakket bevat een set bibliotheken die communiceren met de Service Bus REST-services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te verkrijgen
1. Open een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix).
2. Navigeer naar de map waar u de voorbeeldtoepassing hebt gemaakt.
3. Typ **npm install azure** in het opdrachtvenster. Dit zou moeten resulteren in de volgende uitvoer:

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
3. U kunt handmatig de opdracht **ls** uitvoeren om te controleren of de map **node\_modules** is gemaakt. In deze map vindt u het **azure**-pakket, dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot Service Bus-onderwerpen.

### <a name="import-the-module"></a>De module importeren
Gebruik Kladblok of een andere teksteditor om het volgende toe te voegen bovenaan het bestand **server.js** van de toepassing:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Een Service Bus-verbinding instellen
De Azure-module leest de omgevingsvariabele `AZURE_SERVICEBUS_CONNECTION_STRING` voor de verbindingsreeks die u hebt verkregen als onderdeel van de [vereisten](#prerequisites). Als u instructies nodig hebt voor het opnieuw ophalen van de verbindingsreeks, raadpleegt u [De verbindingsreeks](service-bus-quickstart-topics-subscriptions-portal.md#get-the-connection-string) ophalen. Als deze omgevingsvariabele niet is ingesteld, moet u de accountgegevens opgeven bij het aanroepen van `createServiceBusService`.

Zie [Omgevingsvariabelen instellen](../container-instances/container-instances-environment-variables.md#azure-cli-example) voor een voorbeeld van het instellen van de omgevingsvariabelen voor een Azure-cloudservice.



## <a name="create-a-topic"></a>Een onderwerp maken
Met het **ServiceBusService**-object kunt u werken met onderwerpen. Met de volgende code wordt een **ServiceBusClient**-object gemaakt. Voeg deze toe bovenaan het **server.js**-bestand, na de instructie voor het importeren van de Azure-module:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Als u `createTopicIfNotExists` aanroept op het **ServiceBusService**-object, wordt het opgegeven onderwerp geretourneerd (als het bestaat), of wordt er een nieuw onderwerp gemaakt met de opgegeven naam. De volgende code maakt gebruik van `createTopicIfNotExists` om het onderwerp met de naam `MyTopic` te maken of er verbinding mee te maken:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

De methode `createTopicIfNotExists` ondersteunt ook extra opties waarmee u standaardinstellingen voor het onderwerp kunt overschrijven, zoals de 'time to live' van het bericht of de maximale grootte van het onderwerp. 

In het volgende voorbeeld wordt de maximumgrootte van het onderwerp ingesteld op 5 GB met een time to live van één minuut:

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
Er kunnen optionele filterbewerkingen worden toegepast op bewerkingen die worden uitgevoerd met behulp van **ServiceBusService**. Filterbewerkingen kunnen logboekregistratie, automatische nieuwe pogingen, enzovoort bevatten. Filters zijn objecten die een methode implementeren met de handtekening:

```javascript
function handle (requestOptions, next)
```

Na het uitvoeren van de voorbewerkingen voor de aanvraagopties, roept de methode `next` aan en geeft een callback door met de volgende handtekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze callback, en na verwerking van het `returnObject` (de reactie van de aanvraag op de server), moet de callback 'next' aanroepen (als dit bestaat) om door te gaan met het verwerken van andere filters, of `finalCallback` aanroepen om te stoppen met service-aanroepen.

Twee filters die logica implementeren voor nieuwe pogingen zijn opgenomen in de Azure SDK voor Node.js: **ExponentialRetryPolicyFilter** en **LinearRetryPolicyFilter**. Met de volgende code wordt een **ServiceBusService**-object gemaakt dat gebruikmaakt van de **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Abonnementen maken
Er worden ook onderwerpabonnementen gemaakt met het object **ServiceBusService**. Abonnementen hebben een naam en kunnen een optioneel filter hebben waarmee de verzameling berichten wordt beperkt die worden bezorgd in de virtuele wachtrij van het abonnement.

> [!NOTE]
> Standaard zijn abonnementen persistent totdat zij, of het onderwerp waaraan ze zijn gekoppeld, worden verwijderd. Als uw toepassing logica bevat om een abonnement te maken, moet deze eerst de `getSubscription`-methode gebruiken om te controleren of het abonnement bestaat.
>
> U kunt de abonnementen automatisch laten verwijderen door de [eigenschap AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle)in te stellen.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Het **MatchAll**-filter is het standaardfilter dat wordt gebruikt wanneer een abonnement wordt gemaakt. Bij gebruik van het **MatchAll**-filter worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. Met het volgende voorbeeld maakt u een abonnement met de naam AllMessages en wordt het standaardfilter **MatchAll** gebruikt.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U kunt ook filters maken waarmee u kunt opgeven welke bereik met berichten die naar een onderwerp worden verzonden, moet worden weergegeven in een bepaald onderwerpabonnement.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is **SqlFilter**, waarmee een subset van SQL92 wordt geïmplementeerd. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Zie de [SqlFilter.SqlExpression][SqlFilter.SqlExpression]-syntaxis voor meer informatie over de expressies die kunnen worden gebruikt met een SQL-filter.

Filters kunnen worden toegevoegd aan een abonnement met behulp van de methode `createRule` van het **ServiceBusService**-object. Met deze methode kunt u nieuwe filters toevoegen aan een bestaand abonnement.

> [!NOTE]
> Omdat het standaardfilter automatisch wordt toegepast op alle nieuwe abonnementen, moet u eerst het standaardfilter verwijderen, anders overschrijft **MatchAll** alle andere filters die u eventueel opgeeft. U kunt de standaardregel verwijderen met de methode `deleteRule` van het **ServiceBusService**-object.
>
>

In het volgende voorbeeld wordt een abonnement met de naam `HighMessages` gemaakt met een **SqlFilter** waarmee alleen berichten worden geselecteerd die een aangepaste `messagenumber`-eigenschap groter dan 3 hebben:

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

Op dezelfde manier wordt in het volgende voorbeeld een abonnement met de naam `LowMessages` gemaakt met een **SqlFilter** waarmee alleen berichten worden geselecteerd die een `messagenumber`-eigenschap kleiner dan of gelijk aan 3 hebben:

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

Wanneer er nu een bericht naar `MyTopic` wordt verzonden, wordt het bezorgd bij ontvangers die zijn geabonneerd op het `AllMessages`-onderwerpabonnement, en selectief bezorgd bij ontvangers die zijn geabonneerd op de onderwerpabonnementen `HighMessages` en `LowMessages` (afhankelijk van de inhoud van het bericht).

## <a name="how-to-send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Om een bericht naar een Service Bus-onderwerp te verzenden, moet uw toepassing de methode `sendTopicMessage` van het **ServiceBusService**-object aanroepen.
Berichten die worden verzonden naar Service Bus-onderwerpen, zijn **BrokeredMessage**-objecten.
**BrokeredMessage**-objecten hebben een aantal standaardeigenschappen (zoals `Label` en `TimeToLive`), een woordenlijst die wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren en de hoofdtekst met tekenreeksgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door een tekenreekswaarde door te geven aan `sendTopicMessage`, en alle vereiste standaardeigenschappen worden gevuld met standaardwaarden.

In het volgende voorbeeld ziet u hoe u vijf testberichten naar `MyTopic` verzendt. De waarde van de eigenschap `messagenumber` van elk bericht varieert afhankelijk van de herhaling van de lus (deze eigenschap bepaalt welke abonnementen het bericht ontvangen):

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

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
Berichten worden van een abonnement ontvangen met behulp van de methode `receiveSubscriptionMessage` van het **ServiceBusService**-object. Standaard worden berichten uit het abonnement verwijderd wanneer ze worden gelezen. U kunt echter de optionele parameter `isPeekLock` instellen op **true** om het bericht te lezen (peek) en te vergrendelen zonder het uit het abonnement te verwijderen.

Het standaardgedrag van het lezen en verwijderen van het bericht als onderdeel van de ontvangstbewerking is het eenvoudigste model, en werkt het beste voor scenario's waarin een toepassing kan tolereren dat een bericht niet wordt verwerkt als er een fout optreedt. Neem bijvoorbeeld een scenario waarin de verwerkende app de ontvangstaanvraag indient en vervolgens vastloopt voordat deze wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, ontbreekt het bericht dat voor het vastlopen is verbruikt wanneer de toepassing opnieuw wordt gestart en het verbruik van berichten opnieuw begint.

Als de parameter `isPeekLock` wordt ingesteld op **true**, wordt de ontvangst een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen gemiste berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd.
Nadat de toepassing het bericht heeft verwerkt (of het veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door de methode **deleteMessage** aan te roepen en het te verwijderen bericht door te geven als een parameter. Met de methode **deleteMessage** wordt het bericht gemarkeerd als verwerkt en wordt het uit het abonnement verwijderd.

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met behulp van `receiveSubscriptionMessage`. In het voorbeeld wordt eerst een bericht ontvangen verwijderd uit het abonnement 'LowMessages', en wordt vervolgens een bericht ontvangen uit het abonnement 'HighMessages' met `isPeekLock` ingesteld op 'true'. Vervolgens wordt het bericht verwijderd met `deleteMessage`:

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
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan deze de methode `unlockMessage` aanroepen op het **ServiceBusService**-object. Deze methode zorgt ervoor dat Service Bus het bericht in het abonnement ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen. In dit geval door dezelfde of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in het abonnement is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out voor vergrendeling verloopt (bijvoorbeeld als de app vastloopt), dan ontgrendelt Service Bus automatisch het bericht en maakt het beschikbaar zodat het weer kan worden ontvangen.

Als de toepassing vastloopt na het verwerken van het bericht, maar voordat de methode `deleteMessage` wordt aangeroepen, wordt het bericht opnieuw bij de app bezorgd wanneer deze opnieuw wordt gestart. Dit gedrag wordt wel *Ten minste één keer verwerken* genoemd. Dat wil zeggen, elk bericht wordt minstens één keer verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, moet u logica toevoegen aan uw toepassing om dubbele berichtbezorging af te handelen. U kunt gebruikmaken van de **MessageId**-eigenschap van het bericht, die gelijk blijft bij alle bezorgingspogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Onderwerpen en abonnementen zijn persistent, tenzij de [eigenschap autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) is ingesteld, en moeten expliciet via de [Azure-portal][Azure portal] of programmatisch worden verwijderd.
In het volgende voorbeeld ziet u hoe u het onderwerp met de naam `MyTopic` kunt verwijderen:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. In het volgende voorbeeld ziet u hoe u een abonnement met de naam `HighMessages` kunt verwijderen uit het onderwerp `MyTopic`:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basisprincipes van Service Bus-onderwerpen hebt geleerd, kunt u deze koppelingen volgen voor meer informatie.

* Zie [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions].
* API-naslaginformatie voor [SqlFilter][SqlFilter].
* Ga naar de opslagplaats [Azure SDK voor Node.js][Azure SDK for Node] op GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Een Node.js-toepassing maken en implementeren op een Azure-website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

