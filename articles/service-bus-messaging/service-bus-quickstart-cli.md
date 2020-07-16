---
title: 'Quickstart: de Azure-CLI gebruiken om een Service Bus-wachtrij te maken | Microsoft Docs'
description: In deze quickstart leert u hoe u de Azure-CLI gebruikt om een Service Bus-wachtrij te maken. Vervolgens gebruikt u een voorbeeld-Java-toepassing om berichten te verzenden naar en te ontvangen van de wachtrij.
author: spelluru
ms.devlang: java
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: ed09a72fd5eed72e7a07fefed53bf010a543b658
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85337400"
---
# <a name="quickstart-use-the-azure-cli-to-create-a-service-bus-queue"></a>Quickstart: De Azure-CLI gebruiken om een Service Bus-wachtrij te maken
In deze quickstart wordt beschreven hoe u berichten verzendt en ontvangt met Service Bus met behulp van Azure CLI en de Service Bus-Java-bibliotheek. Als u ook bent geïnteresseerd in meer technische informatie, dan kunt u [de uitleg lezen](#understand-the-sample-code) van de belangrijkste elementen van de voorbeeldcode.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Vereisten
Als u nog geen abonnement op Azure hebt, kunt u een [gratis account][free account] maken voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Als u de knop **Probeer** gebruikt om de Cloud Shell te starten, meldt u zich aan bij Azure met behulp van uw aanmeldingsgegevens. 

Als u de Cloud Shell in uw webbrowser hebt gestart, rechtstreeks of in de Azure-portal, schakelt u naar **Bash** als u **Power Shell** in de linkerbovenhoek van de Cloud Shell ziet. 

## <a name="use-the-azure-cli-to-create-resources"></a>De Azure-CLI gebruiken om resources te maken
Geef in Cloud-Shell bij de Bash-prompt de volgende opdrachten voor het inrichten van Service Bus-resources. Zorg dat u alle tijdelijke aanduidingen vervangt door de juiste waarden: De Java-voorbeeldcode verwacht dat de naam van de wachtrij BasicQueue is, wijzig deze dus niet. U kunt de opdrachten één voor één kopiëren/plakken, zodat u de waarden vervangt voordat u ze uitvoert. 

```azurecli-interactive
# Create a resource group
resourceGroupName="myResourceGroup"

az group create --name $resourceGroupName --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create --resource-group $resourceGroupName --name $namespaceName --location eastus

# Create a Service Bus queue
az servicebus queue create --resource-group $resourceGroupName --namespace-name $namespaceName --name BasicQueue

# Get the connection string for the namespace
connectionString=$(az servicebus namespace authorization-rule keys list --resource-group $resourceGroupName --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv)
```

Wanneer de laatste opdracht is uitgevoerd, kopieert en plakt u de verbindingstekenreeks en de naam van de wachtrij die u hebt geselecteerd naar een tijdelijke locatie, zoals Kladblok. U hebt deze gegevens nodig in de volgende stap.

## <a name="send-and-receive-messages"></a>Berichten verzenden en ontvangen

Wanneer u de naamruimte en wachtrij hebt gemaakt en u over de benodigde referenties beschikt, kunt u berichten gaan verzenden en ontvangen. U kunt de code in [deze GitHub-voorbeeldmap](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) controleren.

1. Kloon de [Service Bus GitHub-opslagplaats](https://github.com/Azure/azure-service-bus/) op uw computer met behulp van de volgende opdracht:

   ```bash
   git clone https://github.com/Azure/azure-service-bus.git
   ```

1. Wijzig de huidige map in de voorbeeldmap, waarbij u de slash (/) als padscheidingstekens gebruikt:

   ```bash
   cd azure-service-bus/samples/Java/azure-servicebus/QueuesGettingStarted
   ```

1. Geef de volgende opdracht om de toepassing te compileren:
   
   ```bash
   mvn clean package -DskipTests
   ```

1. Als u het programma wilt uitvoeren, moet u de volgende opdracht geven nadat u de verbindingsreeks hebt vervangen door de waarde die u eerder hebt gekopieerd:

   ```bash
   java -jar ./target/queuesgettingstarted-1.0.0-jar-with-dependencies.jar -c "<SERVICE BUS NAMESPACE CONNECTION STRING>" 
   ```

1. Zie hoe 10 berichten naar de wachtrij worden verzonden. De volgorde van de berichten wordt niet gegarandeerd, maar u ziet dat de berichten worden verzonden en daarna worden bevestigd en ontvangen, samen met de payload-gegevens:

    ```
    Message sending: Id = 0
    Message sending: Id = 1
    Message sending: Id = 2
    Message sending: Id = 3
    Message sending: Id = 4
    Message sending: Id = 5
    Message sending: Id = 6
    Message sending: Id = 7
    Message sending: Id = 8
    Message sending: Id = 9
            Message acknowledged: Id = 9
            Message acknowledged: Id = 3
                                    Message received:
                                                    MessageId = 9,
                                                    SequenceNumber = 54324670505156609,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Nikolaus, name = Kopernikus ]
    
            Message acknowledged: Id = 2
            Message acknowledged: Id = 5
            Message acknowledged: Id = 1
            Message acknowledged: Id = 8
            Message acknowledged: Id = 7
            Message acknowledged: Id = 0
            Message acknowledged: Id = 6
            Message acknowledged: Id = 4
                                    Message received:
                                                    MessageId = 3,
                                                    SequenceNumber = 58828270132527105,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Steven, name = Hawking ]
    
                                    Message received:
                                                    MessageId = 2,
                                                    SequenceNumber = 9288674231451649,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.012Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.012Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Marie, name = Curie ]
    
                                    Message received:
                                                    MessageId = 1,
                                                    SequenceNumber = 22799473113563137,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.025Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.025Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Werner, name = Heisenberg ]
    
                                    Message received:
                                                    MessageId = 8,
                                                    SequenceNumber = 67835469387268097,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.028Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.028Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Johannes, name = Kepler ]
    
                                    Message received:
                                                    MessageId = 7,
                                                    SequenceNumber = 4785074604081153,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.020Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.020Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Galileo, name = Galilei ]
    
                                    Message received:
                                                    MessageId = 5,
                                                    SequenceNumber = 13792273858822145,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.027Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.027Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Niels, name = Bohr ]
    
                                    Message received:
                                                    MessageId = 0,
                                                    SequenceNumber = 18295873486192641,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.021Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.021Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Albert, name = Einstein ]
    
                                    Message received:
                                                    MessageId = 6,
                                                    SequenceNumber = 281474976710657,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.019Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.019Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Michael, name = Faraday ]
    
                                    Message received:
                                                    MessageId = 4,
                                                    SequenceNumber = 63331869759897601,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.964Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.964Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Isaac, name = Newton ]
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Voer in de Azure Cloud Shell de volgende opdracht uit om de resourcegroep, de naamruimte en alle gerelateerde resources te verwijderen:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

Deze sectie bevat meer informatie over de belangrijkste secties van de voorbeeldcode. U kunt [hier](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) bladeren door de code, die zich in de GitHub-opslagplaats bevindt.

### <a name="get-connection-string"></a>Verbindingstekenreeks ophalen

De runApp-methode leest de waarde van de verbindingstekenreeks van de argumenten naar het programma. 

```java
public static void main(String[] args) {

    System.exit(runApp(args, (connectionString) -> {
        QueuesGettingStarted app = new QueuesGettingStarted();
        try {
            app.run(connectionString);
            return 0;
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 1;
        }
    }));
}

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
```

### <a name="create-queue-clients-to-send-and-receive"></a>Wachtrijclients maken om berichten te verzenden en te ontvangen

Om berichten te verzenden en te ontvangen, maakt de methode `run()` exemplaren van wachtrijclients. Deze zijn samengesteld uit de verbindingsreeks en de naam van de wachtrij. Deze code maakt twee wachtrijclients, één voor het verzenden en één voor het ontvangen:

```java
public void run(String connectionString) throws Exception {

    // Create a QueueClient instance for receiving using the connection string builder
    // We set the receive mode to "PeekLock", meaning the message is delivered
    // under a lock and must be acknowledged ("completed") to be removed from the queue
    QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    // We are using single thread executor as we are only processing one message at a time
    ExecutorService executorService = Executors.newSingleThreadExecutor();
    this.registerReceiver(receiveClient, executorService);

    // Create a QueueClient instance for sending and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    // wait for ENTER or 10 seconds elapsing
    waitForEnter(10);

    // shut down receiver to close the receive loop
    receiveClient.close();
    executorService.shutdown();
}
``` 

### <a name="construct-and-send-messages"></a>Berichten maken en verzenden

De methode `sendMessagesAsync()` maakt een reeks van 10 berichten en verzendt deze asynchroon met behulp van de wachtrijclient:

```java
CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
    List<HashMap<String, String>> data =
            GSON.fromJson(
                    "[" +
                            "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                            "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                            "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                            "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                            "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                            "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                            "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                            "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                            "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                            "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                            "]",
                    new TypeToken<List<HashMap<String, String>>>() {}.getType());

    List<CompletableFuture> tasks = new ArrayList<>();
    for (int i = 0; i < data.size(); i++) {
        final String messageId = Integer.toString(i);
        Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
        message.setContentType("application/json");
        message.setLabel("Scientist");
        message.setMessageId(messageId);
        message.setTimeToLive(Duration.ofMinutes(2));
        System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
        tasks.add(
                sendClient.sendAsync(message).thenRunAsync(() -> {
                    System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                }));
    }
    return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
}
```

### <a name="receive-messages"></a>Berichten ontvangen

De methode `registerReceiver()` registreert de `RegisterMessageHandler`-callback en stelt ook enkele opties van de berichtenhandler in:

```java
void registerReceiver(QueueClient queueClient, ExecutorService executorService) throws Exception {

    
    // register the RegisterMessageHandler callback with executor service
    queueClient.registerMessageHandler(new IMessageHandler() {
                                            // callback invoked when the message handler loop has obtained a message
                                            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                                                // receives message is passed to callback
                                                if (message.getLabel() != null &&
                                                        message.getContentType() != null &&
                                                        message.getLabel().contentEquals("Scientist") &&
                                                        message.getContentType().contentEquals("application/json")) {

                                                    byte[] body = message.getBody();
                                                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                                                    System.out.printf(
                                                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                                                            message.getMessageId(),
                                                            message.getSequenceNumber(),
                                                            message.getEnqueuedTimeUtc(),
                                                            message.getExpiresAtUtc(),
                                                            message.getContentType(),
                                                            scientist != null ? scientist.get("firstName") : "",
                                                            scientist != null ? scientist.get("name") : "");
                                                }
                                                return CompletableFuture.completedFuture(null);
                                            }

                                            // callback invoked when the message handler has an exception to report
                                            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                                                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                                            }
                                        },
            // 1 concurrent call, messages are auto-completed, auto-renew duration
            new MessageHandlerOptions(1, true, Duration.ofMinutes(1)),
            executorService);

}
```

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Service Bus-naamruimte en andere resources gemaakt die zijn vereist voor het verzenden en ontvangen van berichten in een wachtrij. Voor meer informatie over het schrijven van code voor het verzenden en ontvangen van berichten, gaat u naar de zelfstudies in de sectie **Berichten verzenden en ontvangen**. 

> [!div class="nextstepaction"]
> [Berichten verzenden en ontvangen](service-bus-dotnet-get-started-with-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
