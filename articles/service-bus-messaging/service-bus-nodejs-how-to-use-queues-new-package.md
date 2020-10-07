---
title: Azure Service Bus-wachtrijen gebruiken in Node.js
description: Meer informatie over het schrijven van een Nodejs-programma om berichten van en naar een Service Bus wachtrij te ontvangen en verzenden met behulp van het nieuwe @azure/service-bus-pakket.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: d1d0d2b57ec1ed23082101c9207c4700424dd7cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289749"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Quickstart: Service Bus-wachtrijen gebruiken met Node.js en het pakket azure/service-bus
In deze zelfstudie leert u hoe u een Nodejs-programma kunt schrijven om berichten van en naar een Service Bus wachtrij te ontvangen en verzenden met behulp van het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-pakket. Dit pakket maakt gebruik van het snellere [AMQP 1.0-protocol](service-bus-amqp-overview.md), terwijl het oudere [Azure-sb](https://www.npmjs.com/package/azure-sb)-pakket gebruik maakte van [Service Bus REST runtime-API's](/rest/api/servicebus/service-bus-runtime-rest). De voorbeelden zijn geschreven in JavaScript.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [Azure Portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken. Noteer de verbindingsreeks voor uw Service Bus-exemplaar en de naam van de wachtrij die u hebt gemaakt. We gebruiken deze waarden in de voorbeelden.

> [!NOTE]
> - In deze zelfstudie wordt gebruikgemaakt van voorbeelden die u kunt kopiëren en uitvoeren met [Nodejs](https://nodejs.org/). Zie voor instructies over het maken van een Node.js-toepassing [Een Node.js-toepassing maken en implementeren op een Azure-website](../app-service/quickstart-nodejs.md) of [Node.js-cloudservice met Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-pakket biedt nog geen ondersteuning voor het maken van wachtrijen. Gebruik het [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)-pakket als u deze programmatisch wilt maken.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren
Als u het npm-pakket voor Service Bus wilt installeren, opent u een opdrachtprompt met `npm` in het bijbehorende pad. Wijzig de directory in de map waar u de voorbeelden wilt maken en voer vervolgens deze opdracht uit.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Interactie met een Service Bus-wachtrij begint met het instantiëren van de [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient)-klasse en het gebruiken ervan om de [QueueClient](/javascript/api/@azure/service-bus/queueclient)-klasse te instantiëren. Zodra u de wachtrij-client hebt, kunt u een afzender maken en de [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-)- of [sendbatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)-methode gebruiken om berichten te verzenden.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een bestand met de naam `send.js` en plak de onderstaande code hierin. Met deze code worden 10 berichten naar uw wachtrij verzonden.

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
3. Voer in de bovenstaande code de verbindingsreeks en de naam van uw wachtrij in.
4. Voer vervolgens de opdracht `node send.js` in een opdrachtprompt uit om dit bestand uit te voeren.

Gefeliciteerd! U hebt zojuist berichten verzonden naar een Service Bus-wachtrij.

Berichten bevatten enkele standaardeigenschappen, zoals `label` en `messageId` die u kunt instellen tijdens het verzenden. Als u aangepaste eigenschappen wilt instellen, gebruikt u de `userProperties`. Dit is een JSON-object dat sleutelwaardeparen bevat van uw aangepaste gegevens.

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Zie [Service Bus-quota](service-bus-quotas.md) voor meer informatie over quota.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Interactie met een Service Bus-wachtrij begint met het instantiëren van de [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient)-klasse en het gebruiken ervan om de [QueueClient](/javascript/api/@azure/service-bus/queueclient)-klasse te instantiëren. Zodra u de wachtrij-client hebt, kunt u een ontvanger maken en de [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-)- of [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)-methode gebruiken om berichten te ontvangen.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een bestand met de naam `recieve.js` en plak de onderstaande code hierin. Deze code probeert tien berichten van uw wachtrij te ontvangen. Het werkelijke aantal dat u ontvangt, is afhankelijk van het aantal berichten in de wachtrij en de netwerklatentie.

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
3. Voer in de bovenstaande code de verbindingsreeks en de naam van uw wachtrij in.
4. Voer vervolgens de opdracht `node receiveMessages.js` in een opdrachtprompt uit om dit bestand uit te voeren.

Gefeliciteerd! U hebt zojuist berichten van een Service Bus-wachtrij ontvangen.

Voor de [createReceiver](/javascript/api/@azure/service-bus/queueclient#createreceiver-receivemode-)-methode wordt een `ReceiveMode` als enum met de waarden [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) en [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations) gebruikt. Vergeet niet om uw [berichten af te handelen](message-transfers-locks-settlement.md#settling-receive-operations) als u de modus `PeekLock` gebruikt met een van de `complete()`-, `abandon()`-, `defer()`- of `deadletter()`-methoden voor het bericht.

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende resources voor meer informatie.
- [Wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
- Andere [Nodejs-voorbeelden afhandelen voor Service Bus op GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)