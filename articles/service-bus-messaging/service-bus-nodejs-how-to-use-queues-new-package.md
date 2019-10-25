---
title: Azure/service-bus-wacht rijen gebruiken in node. js
description: Informatie over het gebruik van Service Bus-wacht rijen in azure vanuit een node. js-app met behulp van het Azure/service bus-pakket.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 58049855cc27d51134b9f76a773f32f49c6381b6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790316"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Service Bus wachtrijen gebruiken met node. js en het Azure/service-bus-pakket
> [!div class="op_multi_selector" title1="Programmeertaal" title2="Node. js pakket"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

In deze zelf studie leert u hoe u een Nodejs-programma kunt schrijven om berichten te verzenden naar en berichten te ontvangen van een Service Bus wachtrij met behulp van het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) -pakket. Dit pakket maakt gebruik van het snellere [AMQP 1,0-protocol](service-bus-amqp-overview.md) , terwijl het oudere [Azure-SB-](https://www.npmjs.com/package/azure-sb) pakket wordt gebruikt [Service Bus rest runtime-api's](/rest/api/servicebus/service-bus-runtime-rest). De voor beelden zijn geschreven in Java script.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) van uw MSDN-abonnee activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om mee te werken, voert u de stappen in het [Azure Portal gebruik uit om een service bus wachtrij](service-bus-quickstart-portal.md) artikel te maken om een wachtrij te maken. Noteer de connection string voor uw Service Bus-exemplaar en de naam van de wachtrij die u hebt gemaakt. We gebruiken deze waarden in de voor beelden.

> [!NOTE]
> - In deze zelf studie wordt gebruikgemaakt van voor beelden die u kunt kopiëren en uitvoeren met behulp van [Nodejs](https://nodejs.org/). Zie [een node. js-toepassing maken en implementeren op een Azure-website](../app-service/app-service-web-get-started-nodejs.md)of [node. js-Cloud service met behulp van Windows Power shell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)voor instructies over het maken van een node. js-toepassing.
> - Het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) -pakket biedt nog geen ondersteuning voor het maken van wacht rijen. Gebruik het [@azure/arm-servicebus-](https://www.npmjs.com/package/@azure/arm-servicebus) pakket als u ze programmatisch wilt maken.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren
Als u het NPM-pakket voor Service Bus wilt installeren, opent u een opdracht prompt met `npm` in het bijbehorende pad. Wijzig de map in de map waar u de voor beelden wilt maken en voer vervolgens deze opdracht uit.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Interactie met een Service Bus wachtrij begint met het instantiëren van de klasse [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) en het gebruiken om de klasse [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) te instantiëren. Zodra u de wachtrij-client hebt, kunt u een afzender maken en de methode [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) of [methode sendbatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) gebruiken om berichten te verzenden.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com/)
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
3. Voer in de bovenstaande code de connection string en de naam van uw wachtrij in.
4. Voer vervolgens de opdracht `node send.js` uit in een opdracht prompt om dit bestand uit te voeren.

Gefeliciteerd! U hebt zojuist berichten verzonden naar een Service Bus wachtrij.

Berichten bevatten enkele standaard eigenschappen, zoals `label` en `messageId`, die u tijdens het verzenden kunt instellen. Als u aangepaste eigenschappen wilt instellen, gebruikt u de `userProperties`. Dit is een JSON-object dat sleutel-waardeparen van uw aangepaste gegevens kan bevatten.

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een kapje aan de totale grootte van de berichten die door een wachtrij worden bewaard. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Zie [Service Bus quota's](service-bus-quotas.md)voor meer informatie over quota's.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Interactie met een Service Bus wachtrij begint met het instantiëren van de klasse [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) en het gebruiken om de klasse [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) te instantiëren. Zodra u de wachtrij-client hebt, kunt u een ontvanger maken en de methode [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) of [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) gebruiken om berichten te ontvangen.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maak een bestand met de naam `recieve.js` en plak de onderstaande code hierin. Met deze code wordt geprobeerd 10 berichten van uw wachtrij te ontvangen. Het werkelijke aantal dat u ontvangt, is afhankelijk van het aantal berichten in de wachtrij en netwerk latentie.

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
3. Voer in de bovenstaande code de connection string en de naam van uw wachtrij in.
4. Voer vervolgens de opdracht `node receiveMessages.js` uit in een opdracht prompt om dit bestand uit te voeren.

Gefeliciteerd! U hebt zojuist berichten ontvangen van een Service Bus wachtrij.

De methode [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) neemt in een `ReceiveMode`. Dit is een enum met waarden [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) en [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Vergeet niet om [uw berichten](message-transfers-locks-settlement.md#settling-receive-operations) af te rekenen als u de `PeekLock`-modus gebruikt met een van de `complete()`-, `abandon()`-, `defer()` of `deadletter()`-methoden in het bericht.

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende bronnen voor meer informatie.
- [Wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
- Andere Nodejs-voor beelden afhandelen [voor service bus op github](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

