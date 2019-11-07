---
title: 'Snelstartgids: Azure Service Bus-onderwerpen en-abonnementen gebruiken met node. js'
description: 'Snelstartgids: informatie over het gebruik van Service Bus-onderwerpen en abonnementen in azure vanuit een node. js-app.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 40543f55dc0cb56f6bc575f926456faf2d0ae5a3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719201"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Snelstartgids: Service Bus-onderwerpen en-abonnementen gebruiken met node. js en het pakket Azure-SB
> [!div class="op_multi_selector" title1="Programmeertaal" title2="Node. js pakket"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

In deze zelf studie leert u hoe u node. js-toepassingen kunt maken om berichten te verzenden naar een Service Bus onderwerp en berichten van een Service Bus-abonnement te ontvangen met behulp van het pakket [Azure-SB](https://www.npmjs.com/package/azure-sb) . De voor beelden zijn geschreven in Java script en gebruiken de node. js [Azure-module](https://www.npmjs.com/package/azure) die intern gebruikmaakt van het `azure-sb`-pakket.

Het [Azure-SB-](https://www.npmjs.com/package/azure-sb) pakket maakt gebruik van [Service Bus rest runtime-api's](/rest/api/servicebus/service-bus-runtime-rest). U kunt een snellere ervaring verkrijgen met het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) -pakket dat gebruikmaakt van het snellere [AMQP 1,0-protocol](service-bus-amqp-overview.md). Zie [Service Bus-onderwerpen en-abonnementen gebruiken met node. js en @azure/service-bus pakket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package)voor meer informatie over het nieuwe pakket. anders gaat u verder met lezen om te zien hoe u het [Azure](https://www.npmjs.com/package/azure) -pakket kunt gebruiken.

De volgende scenario's zijn van toepassing:

- Onderwerpen en abonnementen maken 
- Abonnements filters maken 
- Berichten verzenden naar een onderwerp 
- Berichten ontvangen van een abonnement
- Onderwerpen en abonnementen verwijderen 

Zie de sectie [volgende stappen](#next-steps) voor meer informatie over onderwerpen en abonnementen.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen van uw Visual Studio-of MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Volg de stappen in de [Snelstartgids: gebruik de Azure Portal om een service bus onderwerp en abonnementen aan het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om een service bus **naam ruimte** te maken en de **Connection String**op te halen.

    > [!NOTE]
    > In deze Quick Start maakt u een **onderwerp** en een **abonnement** op het onderwerp met behulp van **node. js** . 

## <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken
Maak een lege node. js-toepassing. Zie [een node. js-toepassing maken en implementeren op een Azure-website], [node. js-Cloud service][Node.js Cloud Service] met Windows Power shell of een website met webmatrix voor instructies over het maken van een node. js-toepassing.

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Down load het node. js Azure-pakket om Service Bus te gebruiken. Dit pakket bevat een set bibliotheken die communiceren met de Service Bus REST-services.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Gebruik node Package Manager (NPM) om het pakket te verkrijgen
1. Open een opdracht regel interface, zoals **Power shell** (Windows), **Terminal** (Mac) of **bash** (UNIX).
2. Navigeer naar de map waarin u de voorbeeld toepassing hebt gemaakt.
3. Typ **NPM Azure installeren** in het opdracht venster, wat kan leiden tot de volgende uitvoer:

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
3. U kunt de **ls** -opdracht hand matig uitvoeren om te controleren of een **knoop punt\_module modules** is gemaakt. Zoek in die map naar het **Azure** -pakket, dat de bibliotheken bevat die u nodig hebt om toegang te krijgen tot Service Bus onderwerpen.

### <a name="import-the-module"></a>De module importeren
Als u Klad blok of een andere tekst editor gebruikt, voegt u het volgende toe aan de bovenkant van het **server. js** -bestand van de toepassing:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Een Service Bus verbinding instellen
De Azure-module leest de omgevings variabele `AZURE_SERVICEBUS_CONNECTION_STRING` voor de connection string die u hebt verkregen uit de vorige stap, "de referenties verkrijgen." Als deze omgevings variabele niet is ingesteld, moet u de account gegevens opgeven wanneer u `createServiceBusService`aanroept.

Zie [omgevings variabelen instellen](../container-instances/container-instances-environment-variables.md#azure-cli-example)voor een voor beeld van het instellen van omgevings variabelen voor een Azure-Cloud service.



## <a name="create-a-topic"></a>Een onderwerp maken
Met het **ServiceBusService** -object kunt u werken met onderwerpen. Met de volgende code wordt een **ServiceBusService** -object gemaakt. Voeg het toe aan de bovenkant van het bestand **server. js** , na de instructie voor het importeren van de Azure-module:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Als u `createTopicIfNotExists` aanroept voor het object **ServiceBusService** , wordt het opgegeven onderwerp geretourneerd (indien aanwezig), of wordt er een nieuw onderwerp met de opgegeven naam gemaakt. De volgende code maakt gebruik van `createTopicIfNotExists` om het onderwerp met de naam `MyTopic`te maken of te verbinden:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

De methode `createTopicIfNotExists` biedt ook ondersteuning voor extra opties, waarmee u standaard instellingen voor het onderwerp, zoals de bericht tijd, een live of maximale onderwerps grootte kunt overschrijven. 

In het volgende voor beeld wordt de maximum grootte van het onderwerp ingesteld op 5 GB met een time-to-Live van één minuut:

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
Optionele filter bewerkingen kunnen worden toegepast op bewerkingen die worden uitgevoerd met behulp van **ServiceBusService**. Filter bewerkingen kunnen logboek registratie, automatisch opnieuw proberen, enzovoort zijn. Filters zijn objecten die een methode implementeren met de hand tekening:

```javascript
function handle (requestOptions, next)
```

Na het uitvoeren van de voor bereiding op de aanvraag opties roept de methode `next`en wordt er een retour aanroep door gegeven met de volgende hand tekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze call back en na de verwerking van de `returnObject` (de reactie van de aanvraag naar de server), moet de retour aanroep volgende aanroepen (indien aanwezig) om door te gaan met het verwerken van andere filters, of `finalCallback` aanroepen om de service aanroep te beëindigen.

Twee filters die logica implementeren voor nieuwe pogingen zijn opgenomen in de Azure SDK voor Node.js: **ExponentialRetryPolicyFilter** en **LinearRetryPolicyFilter**. Met de volgende code wordt een **ServiceBusService** -object gemaakt dat gebruikmaakt van de **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Abonnementen maken
Er worden ook abonnementen van het onderwerp gemaakt met het **ServiceBusService** -object. Abonnementen krijgen een naam en kunnen een optioneel filter hebben waarmee de set berichten wordt beperkt die aan de virtuele wachtrij van het abonnement wordt geleverd.

> [!NOTE]
> Standaard zijn abonnementen permanent totdat ze zijn, of het onderwerp waaraan ze zijn gekoppeld, worden verwijderd. Als uw toepassing logica bevat om een abonnement te maken, moet u eerst controleren of het abonnement bestaat door de `getSubscription` methode te gebruiken.
>
> U kunt de abonnementen automatisch laten verwijderen door de [eigenschap AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/azure-arm-sb/sbsubscription?view=azure-node-latest#autodeleteonidle)in te stellen.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Het **MatchAll** -filter is het standaard filter dat wordt gebruikt wanneer een abonnement wordt gemaakt. Bij gebruik van het **MatchAll**-filter worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. In het volgende voor beeld wordt een abonnement met de naam AllMessages gemaakt en wordt het standaard **MatchAll** -filter gebruikt.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U kunt ook filters maken waarmee u kunt bepalen welke berichten die naar een onderwerp worden verzonden, moeten worden weer gegeven in een specifiek onderwerp-abonnement.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is de **SqlFilter**, waarmee een SUBSET van SQL92 wordt geïmplementeerd. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Raadpleeg de syntaxis van [SqlFilter. SqlExpression][SqlFilter.SqlExpression] voor meer informatie over de expressies die kunnen worden gebruikt met een SQL-filter.

Filters kunnen worden toegevoegd aan een abonnement met behulp van de methode `createRule` van het object **ServiceBusService** . Met deze methode kunt u nieuwe filters toevoegen aan een bestaand abonnement.

> [!NOTE]
> Omdat het standaard filter automatisch wordt toegepast op alle nieuwe abonnementen, moet u eerst het standaard filter verwijderen of de **MatchAll** alle andere filters overschrijven die u kunt opgeven. U kunt de standaard regel verwijderen met de methode `deleteRule` van het object **ServiceBusService** .
>
>

In het volgende voor beeld wordt een abonnement met de naam `HighMessages` gemaakt met een **SqlFilter** die alleen berichten selecteert die een aangepaste `messagenumber` eigenschap hebben die groter is dan 3:

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

Op dezelfde manier maakt het volgende voor beeld een abonnement met de naam `LowMessages` met een **SqlFilter** die alleen berichten selecteert die een `messagenumber` eigenschap hebben die kleiner is dan of gelijk is aan 3:

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

Wanneer er nu een bericht naar `MyTopic`wordt verzonden, wordt het bezorgd bij ontvangers die zijn geabonneerd op het abonnement van de `AllMessages`-onderwerp en worden ze selectief geleverd aan ontvangers die zijn geabonneerd op de `HighMessages`-en `LowMessages`-abonnements abonnementen (afhankelijk van de inhoud van het bericht).

## <a name="how-to-send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Uw toepassing moet de methode `sendTopicMessage` van het object **ServiceBusService** gebruiken om een bericht naar een service bus onderwerp te verzenden.
Berichten die worden verzonden naar Service Bus-onderwerpen zijn **BrokeredMessage** -objecten.
**BrokeredMessage** -objecten hebben een aantal standaard eigenschappen (zoals `Label` en `TimeToLive`), een woorden lijst die wordt gebruikt voor het opslaan van aangepaste toepassingsspecifieke eigenschappen en een hoofd tekst van teken reeks gegevens. Een toepassing kan de hoofd tekst van het bericht instellen door een teken reeks waarde door te geven aan de `sendTopicMessage` en alle vereiste standaard eigenschappen worden gevuld met standaard waarden.

In het volgende voor beeld ziet u hoe u vijf test berichten naar `MyTopic`verzendt. De waarde van de eigenschap `messagenumber` van elk bericht varieert afhankelijk van de herhaling van de lus (deze eigenschap bepaalt welke abonnementen het heeft ontvangen):

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

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
Er worden berichten ontvangen van een abonnement met behulp van de methode `receiveSubscriptionMessage` voor het object **ServiceBusService** . Standaard worden berichten uit het abonnement verwijderd wanneer ze worden gelezen. U kunt echter de optionele para meter `isPeekLock` instellen op **True** om te lezen (Peek) en het bericht te vergren delen zonder het te verwijderen uit het abonnement.

Het standaard gedrag van het lezen en verwijderen van het bericht als onderdeel van de receive-bewerking is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing niet de verwerking van een bericht kan verdragen als er een fout optreedt. Om dit gedrag te begrijpen, moet u rekening houden met een scenario waarin de consumer de ontvangst aanvraag uitgeeft en vervolgens vastloopt voordat het proces wordt verwerkt. Omdat Service Bus het bericht heeft gemarkeerd als verbruikt, wordt het bericht dat voor het vastlopen is verbruikt, gemist wanneer de toepassing opnieuw wordt gestart en er opnieuw wordt verbruikt.

Als de para meter `isPeekLock` is ingesteld op **waar**, wordt de ontvangst een bewerking met twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen gemiste berichten kunnen verdragen. Wanneer Service Bus een aanvraag ontvangt, wordt het volgende te gebruiken bericht gevonden, vergrendeld om te voor komen dat andere gebruikers het ontvangen, en wordt het naar de toepassing geretourneerd.
Nadat de toepassing het bericht heeft verwerkt (of als het op een betrouw bare manier is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangst proces voltooid door de methode **deleteMessage** aan te roepen en wordt het bericht door gegeven als een para meter. De methode **deleteMessage** markeert het bericht als verbruikt en verwijdert het uit het abonnement.

In het volgende voor beeld ziet u hoe berichten kunnen worden ontvangen en verwerkt met behulp van `receiveSubscriptionMessage`. In het voor beeld wordt eerst een bericht ontvangen en uit het ' Lowmessages gemaakt-abonnement verwijderd. Vervolgens ontvangt u een bericht van het ' HighMessages ' met `isPeekLock` ingesteld op True. Vervolgens wordt het bericht met `deleteMessage`verwijderd:

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
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan deze de `unlockMessage`-methode aanroepen voor het object **ServiceBusService** . Deze methode zorgt ervoor Service Bus om het bericht in het abonnement te ontgrendelen en het beschikbaar te maken zodat het opnieuw kan worden ontvangen. In dit geval, op basis van dezelfde verbruiks toepassing of door een andere consumerende toepassing.

Er is ook een time-out gekoppeld aan een bericht dat in het abonnement is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out voor de vergren deling verloopt (bijvoorbeeld als de toepassing vastloopt), wordt Service Bus het bericht automatisch ontgrendeld en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

In de gebeurtenis die de toepassing vastloopt na het verwerken van het bericht, maar voordat de `deleteMessage` methode wordt aangeroepen, wordt het bericht opnieuw aan de toepassing bezorgd wanneer het opnieuw wordt gestart. Dit gedrag wordt vaak *ten minste eenmaal in de verwerking*genoemd. Dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar in bepaalde situaties kan hetzelfde bericht opnieuw worden bezorgd. Als het scenario dubbele verwerking niet kan verdragen, moet u logica aan uw toepassing toevoegen om de dubbele bericht bezorging af te handelen. U kunt de **MessageId** -eigenschap van het bericht gebruiken, dat constant blijft tijdens bezorgings pogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Onderwerpen en abonnementen zijn permanent, tenzij de [eigenschap autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/azure-arm-sb/sbsubscription?view=azure-node-latest#autodeleteonidle) is ingesteld, en moet expliciet worden verwijderd via de [Azure Portal][Azure portal] of via een programma.
In het volgende voor beeld ziet u hoe u het onderwerp met de naam `MyTopic`verwijdert:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. In het volgende voor beeld ziet u hoe u een abonnement met de naam `HighMessages` verwijdert uit het onderwerp `MyTopic`:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Service Bus onderwerpen hebt geleerd, volgt u deze koppelingen voor meer informatie.

* Zie [wacht rijen, onderwerpen en abonnementen][Queues, topics, and subscriptions].
* API-naslaginformatie voor [SqlFilter][SqlFilter].
* Ga naar de [Azure SDK voor knooppunt][Azure SDK for Node] opslagplaats op github.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Een node. js-toepassing maken en implementeren op een Azure-website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

