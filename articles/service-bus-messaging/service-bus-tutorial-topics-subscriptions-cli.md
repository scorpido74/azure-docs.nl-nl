---
title: 'Zelfstudie: Het voorraadassortiment van een detailhandelsbedrijf bijwerken via het publiceren van/abonneren op kanalen en via onderwerpfilters met Azure CLI'
description: 'Zelfstudie: In deze zelfstudie leert u hoe u berichten naar een onderwerp en abonnement kunt versturen en ervan kunt ontvangen, en hoe u filterregels kunt gebruiken en toevoegen met behulp van Azure CLI.'
ms.date: 06/23/2020
ms.topic: tutorial
author: spelluru
ms.author: spelluru
ms.custom: devx-track-azurecli
ms.openlocfilehash: cdbde84780ed7c64f9ad3f4284a74261d2f4a728
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500450"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Zelfstudie: voorraad bijwerken met behulp van CLI en onderwerpen/abonnementen

Microsoft Azure Service Bus is een multitenant-cloudberichtenservice waarmee u gegevens kunt versturen tussen toepassingen en services. Met asynchrone bewerkingen krijgt u flexibele Brokered Messaging, samen met gestructureerde FIFO-messaging (first in, first out) en mogelijkheden voor publiceren/abonneren. In deze zelfstudie leert u hoe u onderwerpen en abonnementen van Service Bus-onderwerpen kunt gebruiken in een scenario met detailhandelvoorraad, waarbij gebruik wordt gemaakt van Azure CLI en Java om kanalen te publiceren/abonneren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Bus-onderwerp en een of meer abonnementen op dat onderwerp maken met Azure CLI.
> * Onderwerpfilters toevoegen met Azure CLI.
> * Twee berichten met verschillende inhoud maken.
> * De berichten verzenden en controleren of ze zijn ontvangen in de verwachte abonnementen
> * Berichten ontvangen van de abonnementen

Een voorbeeld van dit scenario is het bijwerken van het voorraadassortiment voor meerdere winkels. In dit scenario ontvangt elke winkel of reeks winkels berichten op basis waarvan ze hun assortiment kunnen bijwerken. Deze zelfstudie laat zien hoe dit scenario met behulp van abonnementen en filters kan worden geïmplementeerd. Eerst maakt u een onderwerp met drie abonnementen, voegt u een aantal regels en filters toe, en vervolgens verzendt en ontvangt u berichten van het onderwerp en de abonnementen.

![onderwerp](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Als u nog geen abonnement op Azure hebt, kunt u een [gratis account][] maken voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u een Service Bus-app wilt ontwikkelen met behulp van Java, moet het volgende zijn geïnstalleerd:

- [Java Development Kit](https://aka.ms/azure-jdks), de nieuwste versie.
- [Azure-CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.4 of nieuwer uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-onderwerpen en -abonnementen

Elk [abonnement op een onderwerp](service-bus-messaging-overview.md#topics) kan een kopie van elk bericht ontvangen. Onderwerpen zijn qua protocol en semantiek volledig compatibel met Service Bus-wachtrijen. Service Bus-onderwerpen ondersteunen een breed scala aan selectieregels met filtervoorwaarden, met optionele acties waarmee berichteigenschappen worden ingesteld of gewijzigd. Telkens wanneer een regel overeenkomt, wordt er een bericht gegenereerd. Volg deze [koppeling](topic-filters.md) voor meer informatie over regels, filters en acties.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Zodra CLI is geïnstalleerd, opent u een opdrachtprompt en meldt u zich met de volgende opdrachten aan bij Azure. Deze stappen zijn niet nodig als u Cloud Shell gebruikt:

1. Als u Azure CLI gebruikt, kunt u zich ook met de volgende opdracht aanmelden bij Azure. Deze aanmeldingsstap is niet nodig als u deze opdrachten in Cloud Shell uitvoert:

   ```azurecli-interactive
   az login
   ```

2. Stel de context van het huidige abonnement in op het Azure-abonnement dat u wilt gebruiken:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>CLI gebruiken voor het inrichten van resources

Gebruik de volgende opdrachten voor het inrichten van Service Bus-resources. Zorg dat u alle tijdelijke aanduidingen vervangt door de juiste waarden:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Wanneer de laatste opdracht is uitgevoerd, kopieert en plakt u de verbindingstekenreeks en de naam van de wachtrij die u hebt geselecteerd naar een tijdelijke locatie, zoals Kladblok. U hebt deze nodig in de volgende stap.

## <a name="create-filter-rules-on-subscriptions"></a>Filterregels voor abonnementen maken

Nadat de naamruimte en onderwerp/abonnementen zijn ingericht en u over de benodigde referenties beschikt, bent u klaar om filterregels voor de abonnementen te maken, en kunt u vervolgens berichten verzenden en ontvangen. U kunt de code in [deze GitHub-voorbeeldmap](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) controleren.

## <a name="send-and-receive-messages"></a>Berichten verzenden en ontvangen

1. Zorg dat Cloud Shell is geopend en dat de Bash-prompt wordt weergegeven.

2. Kloon de [Service Bus GitHub-opslagplaats](https://github.com/Azure/azure-service-bus/) met behulp van de volgende opdracht:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navigeer naar de voorbeeldmap `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Houd er rekening mee dat in de Bash-shell de opdrachten hoofdlettergevoelig zijn en dat de padscheidingstekens slashes zijn.

3. Geef de volgende opdracht om de toepassing te maken:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Geef de volgende opdracht om het programma uit te voeren. Zorg ervoor dat u de tijdelijke aanduidingen vervangt door de verbindingsreeks en onderwerpnaam die u in de vorige stap hebt verkregen:

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Bekijk tien berichten die naar het onderwerp zijn verzonden en vervolgens van de afzonderlijke abonnementen zijn ontvangen:

   ![programma-uitvoer](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende opdracht uit om de resourcegroep, de naamruimte en alle gerelateerde resources te verwijderen:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

Deze sectie bevat meer informatie over de werking van de voorbeeldcode.

### <a name="get-connection-string-and-queue"></a>De verbindingsreeks en de wachtrij ophalen

De code declareert eerst een set van variabelen die de rest van het programma uitvoert:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

De verbindingsreeks en de naam van het onderwerp zijn de enige waarden die via opdrachtregelparameters worden toegevoegd en aan `main()` worden doorgegeven. De werkelijke uitvoering van de code wordt geactiveerd in de methode `run()`. Vervolgens worden berichten van het onderwerp verzonden en ontvangen:

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Onderwerpclient voor het verzenden van berichten maken

Voor het verzenden en ontvangen van berichten wordt met de methode `sendMessagesToTopic()` een instantie van het clientonderwerp gemaakt. Deze maakt gebruik van de verbindingsreeks en de naam van het onderwerp. Vervolgens wordt een andere methode aangeroepen om het bericht te verzenden:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Berichten ontvangen van de afzonderlijke abonnementen

De methode `receiveAllMessages()` roept de methode `receiveAllMessageFromSubscription()` aan die vervolgens per aanroep een clientabonnement maakt en berichten van de afzonderlijke abonnementen ontvangt:

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u resources ingericht met behulp van Azure CLI en vervolgens berichten verzonden en ontvangen van een Service Bus-onderwerp en de daarbij behorende abonnementen. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Service Bus-onderwerp en een of meer abonnementen voor dat onderwerp maken met Azure Portal
> * Onderwerpfilters toevoegen met .NET-code
> * Twee berichten met verschillende inhoud maken.
> * De berichten verzenden en controleren of ze zijn ontvangen in de verwachte abonnementen
> * Berichten ontvangen van de abonnementen

Meer voorbeelden voor het verzenden en ontvangen van berichten vindt u in [de Service Bus-voorbeelden op GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Ga naar de volgende zelfstudie voor meer informatie over het gebruik van de mogelijkheden voor publiceren/abonneren van Service Bus.

> [!div class="nextstepaction"]
> [Voorraad bijwerken met behulp van PowerShell en onderwerpen/abonnementen](service-bus-tutorial-topics-subscriptions-portal.md)

[gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
