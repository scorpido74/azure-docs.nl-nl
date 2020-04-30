---
title: Azure/service-bus-onderwerpen en-abonnementen gebruiken met node. js
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
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 6088b4c54ed16c5ef46d2c0671e619884cad29d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78330614"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Snelstartgids: Service Bus-onderwerpen en-abonnementen gebruiken met node. js en het Azure/service-bus-pakket
In deze zelf studie leert u hoe u een node. js-programma kunt schrijven om berichten naar een Service Bus onderwerp te verzenden en berichten van een Service Bus-abonnement [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) te ontvangen met behulp van het nieuwe pakket. Dit pakket maakt gebruik van het snellere [AMQP 1,0-protocol](service-bus-amqp-overview.md) , terwijl het oudere [Azure-SB-](https://www.npmjs.com/package/azure-sb) pakket wordt gebruikt [Service Bus rest runtime-api's](/rest/api/servicebus/service-bus-runtime-rest). De voor beelden zijn geschreven in Java script.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt de [voor delen](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) van uw MSDN-abonnee activeren of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen onderwerp en abonnement hebt om te werken met, volgt u de stappen in het artikel [Azure Portal gebruiken om een service bus onderwerpen en abonnementen te maken](service-bus-quickstart-topics-subscriptions-portal.md) om ze te maken. Noteer de connection string voor uw Service Bus-exemplaar en de namen van het onderwerp en het abonnement dat u hebt gemaakt. We gebruiken deze waarden in de voor beelden.

> [!NOTE]
> - In deze zelf studie wordt gebruikgemaakt van voor beelden die u kunt kopiëren en uitvoeren met behulp van [Nodejs](https://nodejs.org/). Zie [een node. js-toepassing maken en implementeren op een Azure-website](../app-service/app-service-web-get-started-nodejs.md)of [node. js-Cloud service met behulp van Windows Power shell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)voor instructies over het maken van een node. js-toepassing.
> - Het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pakket biedt nog geen ondersteuning voor het maken van topcis en abonnementen. Gebruik het [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) pakket als u deze programmatisch wilt maken.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren
Als u het NPM-pakket voor Service Bus wilt installeren, opent u een `npm` opdracht prompt met in het pad naar de map waarin u de voor beelden wilt maken en voert u deze opdracht uit.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Interactie met een Service Bus onderwerp begint met het instantiëren van de klasse [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) en het gebruiken om de klasse [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) te instantiëren. Zodra u de onderwerp-client hebt, kunt u een afzender maken en de methode [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) of [methode sendbatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) gebruiken om berichten te verzenden.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maak een bestand met `send.js` de naam en plak de onderstaande code hierin. Met deze code worden 10 berichten verzonden naar uw onderwerp.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Voer in de bovenstaande code de connection string en de naam van uw onderwerp in.
4. Voer vervolgens de opdracht `node send.js` uit in een opdracht prompt om dit bestand uit te voeren. 

Gefeliciteerd! U hebt zojuist berichten verzonden naar een Service Bus wachtrij.

Berichten bevatten enkele standaard eigenschappen, `label` zoals `messageId` en die u kunt instellen bij het verzenden. Als u aangepaste eigenschappen wilt instellen, gebruikt u de `userProperties`. Dit is een JSON-object dat sleutel-waardeparen van uw aangepaste gegevens kan bevatten.

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). Er is geen limiet voor het aantal berichten dat in een onderwerp wordt bewaard, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB. Zie [Service Bus quota's](service-bus-quotas.md)voor meer informatie over quota's.

## <a name="receive-messages-from-a-subscription"></a>Berichten ontvangen van een abonnement
Interactie met een Service Bus-abonnement begint bij het instantiëren van de klasse [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) en het gebruiken om de klasse [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) te instantiëren. Zodra u de abonnements-client hebt, kunt u een ontvanger maken en de methode [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) of [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) gebruiken om berichten te ontvangen.

1. Open uw favoriete editor, zoals [Visual Studio code](https://code.visualstudio.com/)
2. Maak een bestand met `recieve.js` de naam en plak de onderstaande code hierin. Met deze code wordt geprobeerd 10 berichten van uw abonnement te ontvangen. Het werkelijke aantal dat u ontvangt, is afhankelijk van het aantal berichten in het abonnement en de netwerk latentie.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Voer in de bovenstaande code de connection string en namen in van uw onderwerp en abonnement.
4. Voer vervolgens de opdracht `node receiveMessages.js` uit in een opdracht prompt om dit bestand uit te voeren.

Gefeliciteerd! U hebt zojuist berichten ontvangen van een Service Bus-abonnement.

De [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) -methode heeft een `ReceiveMode` enum met waarden [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) en [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Vergeet niet [om uw berichten](message-transfers-locks-settlement.md#settling-receive-operations) af te rekenen als `PeekLock` u de modus gebruikt met `complete()`behulp van een van `abandon()` `defer()`de-,-of `deadletter()` -methoden in het bericht.

## <a name="subscription-filters-and-actions"></a>Abonnements filters en-acties
Service Bus biedt ondersteuning voor [filters en acties op abonnementen](topic-filters.md), waarmee u de inkomende berichten kunt filteren op een abonnement en de eigenschappen van deze kunnen bewerken.

Zodra u een exemplaar van een `SubscriptionClient` hebt, kunt u de onderstaande methoden hiervoor gebruiken om regels in het abonnement op te halen, toe te voegen en te verwijderen om de filters en acties te beheren.

- getRules
- addRule
- removeRule

Elk abonnement heeft een standaard regel die gebruikmaakt van het ware filter om alle inkomende berichten toe te staan. Wanneer u een nieuwe regel toevoegt, moet u het standaard filter verwijderen om het filter in uw nieuwe regel te laten werken. Als een abonnement geen regels heeft, worden er geen berichten weer gegeven.

> [!NOTE]
> U kunt Service Bus-resources beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met de Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus naam ruimte en de Messa ging-entiteiten op een eenvoudige manier beheren. Het hulp programma biedt geavanceerde functies zoals de functionaliteit voor importeren/exporteren of de mogelijkheid om onderwerp, wacht rijen, abonnementen, relay-Services, Notification hubs en Events hubs te testen. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende bronnen voor meer informatie.

- [Wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
- Andere Nodejs-voor beelden afhandelen [voor service bus op github](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)


