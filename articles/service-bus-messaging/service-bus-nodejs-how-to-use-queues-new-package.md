---
title: Azure/service-buswachtrijen gebruiken in Node.js
description: Meer informatie over het schrijven van een Nodejs-programma om berichten naar @azure/service-bus berichten te verzenden en berichten te ontvangen vanuit een servicebuswachtrij met behulp van het nieuwe pakket.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: c2e24e9dea2c8463294c85f04c9e4d7d2da17261
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330648"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Snelstart: Wachtrijen voor servicebussen gebruiken met Node.js en het azure/service-buspakket
In deze zelfstudie leert u hoe u een Nodejs-programma schrijft om berichten naar [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) berichten te verzenden en berichten te ontvangen vanuit een wachtrij voor servicebus met behulp van het nieuwe pakket. Dit pakket maakt gebruik van het snellere [AMQP 1.0-protocol,](service-bus-amqp-overview.md) terwijl het oudere [azure-sb-pakket](https://www.npmjs.com/package/azure-sb) gebruik maakte van [Service Bus REST run-time API's](/rest/api/servicebus/service-bus-runtime-rest). De voorbeelden zijn geschreven in JavaScript.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Als u geen wachtrij hebt om mee te werken, voert u stappen uit in de [Azure-portal gebruiken om een wachtrijartikel voor servicebus te maken](service-bus-quickstart-portal.md) om een wachtrij te maken. Noteer de verbindingstekenreeks voor uw instantie ServiceBus en de naam van de wachtrij die u hebt gemaakt. We gebruiken deze waarden in de monsters.

> [!NOTE]
> - Deze zelfstudie werkt met voorbeelden die u kopiëren en uitvoeren met Behulp van [Nodejs](https://nodejs.org/). Zie [Een Node.js-toepassing maken en implementeren voor](../app-service/app-service-web-get-started-nodejs.md)instructies voor het maken van een Node.js-toepassing naar een Azure-website of een [Cloudservice Node.js met Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Het [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) nieuwe pakket biedt nog geen ondersteuning voor het maken van wachtrijen. Gebruik het [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) pakket als u ze programmatisch wilt maken.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren
Als u het npm-pakket voor Service Bus `npm` wilt installeren, opent u een opdrachtprompt die op zijn pad is, wijzigt u de map in de map waar u uw voorbeelden wilt hebben en voert u deze opdracht uit.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Interactie met een wachtrij voor servicebus begint met het instantiëren van de [servicebusclientklasse](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) en deze gebruiken om de klasse [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) te instantiëren. Zodra u de wachtrijclient hebt, u een afzender maken en de methode [Verzenden](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) of [verzenden batch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) gebruiken om berichten te verzenden.

1. Open uw favoriete editor, zoals [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een `send.js` bestand aangeroepen en plak de onderstaande code erin. Deze code stuurt 10 berichten naar uw wachtrij.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Voer de verbindingstekenreeks en de naam van uw wachtrij in de bovenstaande code in.
4. Voer vervolgens `node send.js` de opdracht uit in een opdrachtprompt om dit bestand uit te voeren.

Gefeliciteerd! Je hebt net berichten naar een servicebuswachtrij gestuurd.

Berichten hebben een aantal `label` `messageId` standaardeigenschappen zoals en die u instellen bij het verzenden. Als u aangepaste eigenschappen wilt instellen, gebruikt u het `userProperties`Json-object dat sleutelwaardeparen van uw aangepaste gegevens kan bevatten.

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). Er is geen limiet voor het aantal berichten dat in een wachtrij wordt gehouden, maar er is een limiet op de totale grootte van de berichten die in een wachtrij worden bewaard. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Zie [ServiceBus-quota voor](service-bus-quotas.md)meer informatie over quota.

## <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij
Interactie met een wachtrij voor servicebus begint met het instantiëren van de [servicebusclientklasse](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) en deze gebruiken om de klasse [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) te instantiëren. Zodra u de wachtrijclient hebt, u een ontvanger maken en de [methode receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) of [de Methode MessageHandler erop](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) gebruiken om berichten te ontvangen.

1. Open uw favoriete editor, zoals [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een `recieve.js` bestand aangeroepen en plak de onderstaande code erin. Met deze code worden 10 berichten uit uw wachtrij verzonden. Het werkelijke aantal dat u ontvangt, is afhankelijk van het aantal berichten in de wachtrij en de netwerklatentie.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Voer de verbindingstekenreeks en de naam van uw wachtrij in de bovenstaande code in.
4. Voer vervolgens `node receiveMessages.js` de opdracht uit in een opdrachtprompt om dit bestand uit te voeren.

Gefeliciteerd! U hebt zojuist berichten ontvangen uit een wachtrij voor servicebussen.

De [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) methode `ReceiveMode` neemt in een die is een enum met waarden [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) en [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Vergeet niet om uw berichten te [vereffenen](message-transfers-locks-settlement.md#settling-receive-operations) `abandon()`als `defer()`u `deadletter()` de `PeekLock` modus gebruikt met behulp van een van `complete()`, , of methoden in het bericht.

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie.
- [Wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
- Andere [Nodejs-voorbeelden afrekenen voor servicebus op GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

