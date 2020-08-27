---
title: Azure/service-bus-onderwerpen en -abonnementen met Node.js gebruiken
description: 'Quickstart: Meer informatie over het gebruik van Service Bus-onderwerpen en -abonnementen met een Node.js-app.'
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: bbb0f530d95c78b8b5da178ee1544830ac3f7132
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660593"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Quickstart: Service Bus-onderwerpen en-abonnementen gebruiken met Node.js en het pakket azure/service-bus
In deze zelfstudie leert u hoe u een Node.js-programma kunt schrijven om berichten te versturen naar een Service Bus-onderwerp en berichten te ontvangen van een Service Bus-abonnement met behulp van het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-pakket. Dit pakket maakt gebruik van het snellere [AMQP 1.0-protocol](service-bus-amqp-overview.md), terwijl het oudere [Azure-sb](https://www.npmjs.com/package/azure-sb)-pakket gebruik maakte van [Service Bus REST runtime-API's](/rest/api/servicebus/service-bus-runtime-rest). De voorbeelden zijn geschreven in Javascript.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) of u [aanmelden voor een gratis proefversie](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen onderwerp en abonnement hebt, volgt u de stappen in het artikel [Azure-portal gebruiken om Service Bus-onderwerpen en -abonnementen te maken](service-bus-quickstart-topics-subscriptions-portal.md) om ze te maken. Noteer de verbindingstekenreeks voor uw Service Bus-exemplaar en de namen van het onderwerp en het abonnement dat u hebt gemaakt. We gebruiken deze waarden in de voorbeelden.

> [!NOTE]
> - In deze zelfstudie wordt gebruikgemaakt van voorbeelden die u kunt kopiëren en uitvoeren met [Nodejs](https://nodejs.org/). Zie voor instructies over het maken van een Node.js-toepassing [Een Node.js-toepassing maken en implementeren op een Azure-website](../app-service/quickstart-nodejs.md) of [Node.js-cloudservice](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) met Windows PowerShell.
> - Het nieuwe [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-pakket biedt nog geen ondersteuning voor het maken van onderwerpen en abonnementen. Gebruik het [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)-pakket als u ze programmatisch wilt maken.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren
Als u het npm-pakket voor Service Bus wilt installeren, opent u een opdrachtprompt met `npm` in het bijbehorende pad. Wijzig de directory in de map waar u de voorbeelden wilt maken en voer vervolgens deze opdracht uit.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Interactie met een Service Bus-onderwerp begint met het instantiëren van de [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient)-klasse en het gebruiken ervan om de [TopicClient](/javascript/api/@azure/service-bus/topicclient)-klasse te instantiëren. Zodra u de onderwerp-client hebt, kunt u een afzender maken en de [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-)- of [sendbatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)-methode gebruiken om berichten te verzenden.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een bestand met de naam `send.js` en plak de onderstaande code hierin. Met deze code worden 10 berichten naar uw onderwerp verzonden.

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
3. Voer in de bovenstaande code de verbindingstekenreeks en de naam van uw onderwerp in.
4. Voer vervolgens de opdracht `node send.js` in een opdrachtprompt uit om dit bestand uit te voeren. 

Gefeliciteerd! U hebt zojuist berichten verzonden naar een Service Bus-wachtrij.

Berichten bevatten enkele standaardeigenschappen, zoals `label` en `messageId` die u kunt instellen tijdens het verzenden. Als u aangepaste eigenschappen wilt instellen, gebruikt u de `userProperties`. Dit is een JSON-object dat sleutelwaardeparen bevat van uw aangepaste gegevens.

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB. Zie [Service Bus-quota](service-bus-quotas.md) voor meer informatie over quota.

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
Interactie met een Service Bus-abonnement begint bij het instantiëren van de [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient)-klasse en het gebruiken ervan om de [SubscriptionClient](/javascript/api/@azure/service-bus/subscriptionclient)-klasse te instantiëren. Zodra u de abonnementsclient hebt, kunt u een ontvanger maken en de [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-)- of [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)-methode gebruiken om berichten te ontvangen.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een bestand met de naam `receive.js` en plak de onderstaande code hierin. Deze code probeert 10 berichten van uw abonnement te ontvangen. Het werkelijke aantal dat u ontvangt, is afhankelijk van het aantal berichten in het abonnement en de netwerklatentie.

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
3. Voer in de bovenstaande code de verbindingstekenreeks en namen in van uw onderwerp en abonnement.
4. Voer vervolgens de opdracht `node receive.js` in een opdrachtprompt uit om dit bestand uit te voeren.

Gefeliciteerd! U heeft zojuist berichten van een Service Bus-abonnement ontvangen.

De [createReceiver](/javascript/api/@azure/service-bus/subscriptionclient#createreceiver-receivemode-)-methode gebruikt een `ReceiveMode` als enum met de waarden [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) en [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Vergeet niet om uw [berichten af te handelen ](message-transfers-locks-settlement.md#settling-receive-operations) als u de modus `PeekLock` gebruikt met een van de `complete()`-, `abandon()`-, `defer()`- of `deadletter()`-methoden voor het bericht.

## <a name="subscription-filters-and-actions"></a>Abonnementsfilters en -acties
Service Bus biedt ondersteuning voor [filters en acties voor abonnementen](topic-filters.md), waarmee u de inkomende berichten kunt filteren op een abonnement en de eigenschappen kunt bewerken.

Zodra u een exemplaar van een `SubscriptionClient` hebt, kunt u de onderstaande methoden hiervoor gebruiken om regels voor het abonnement op te halen, toe te voegen en te verwijderen om de filters en acties te beheren.

- getRules
- addRule
- removeRule

Elk abonnement heeft een standaardregel die gebruikmaakt van het true filter om alle binnenkomende berichten toe te staan. Wanneer u een nieuwe regel toevoegt, moet u het standaardfilter verwijderen om het filter in uw nieuwe regel te laten werken. Als een abonnement geen regels heeft, worden er geen berichten weergegeven.

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende resources voor meer informatie.

- [Wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
- Andere [Nodejs-voorbeelden afhandelen voor Service Bus op GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)
