---
title: Azure Service Bus-bindingen voor Azure Functions
description: Over het gebruik van Azure Service Bus-triggers en bindingen in Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: d27058ed0ff3044d98d8428b3065b02e2c24c451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231050"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u werkt met Azure Service Bus-bindingen in Azure Functions. Azure Functions ondersteunt activeren en uitvoerbindingen voor Service Bus-wachtrijen en onderwerpen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De Service Bus bindingen zijn opgenomen in het [micro soft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-pakket, versie 2. x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De Service Bus-bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet, versie 3. x. De bron code voor het pakket bevindt zich in de [Azure-functions-servicebus-extension github-](https://github.com/Azure/azure-functions-servicebus-extension) opslag plaats.

> [!NOTE]
> Versie 2. x maakt geen onderwerp of abonnement dat is geconfigureerd in het `ServiceBusTrigger`-exemplaar. Versie 2. x is gebaseerd op [micro soft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) en verwerkt geen wachtrij beheer.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

De Service Bus-trigger te gebruiken om te reageren op berichten van een Service Bus-wachtrij of onderwerp. 

## <a name="trigger---example"></a>Trigger - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger - voorbeeld met C#

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee de [meta gegevens](#trigger---message-metadata) van een bericht worden gelezen en een service bus wachtrij bericht wordt geregistreerd:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

### <a name="trigger---c-script-example"></a>Trigger - voorbeeld van C#-script

In het volgende voor beeld ziet u een binding van een service bus trigger in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van berichten](#trigger---message-metadata) gelezen en wordt een service bus wachtrij bericht geregistreerd.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Dit is de C#-scriptcode:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Trigger - F# voorbeeld

In het volgende voor beeld ziet u de binding van een service bus trigger in een *Function. json* -bestand en een [ F# functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie vastlegt een Service Bus-wachtrij-bericht. 

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Hier volgt de F# code script:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Trigger - Java-voorbeeld

De volgende Java-functie maakt gebruik van de `@ServiceBusQueueTrigger` annotatie van de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime) om de configuratie voor een service bus wachtrij trigger te beschrijven. De functie trekt het bericht dat in de wachtrij is geplaatst en voegt het toe aan de logboeken.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Java-functies kunnen ook worden geactiveerd wanneer een bericht wordt toegevoegd aan een Service Bus onderwerp. In het volgende voor beeld wordt de `@ServiceBusTopicTrigger` aantekening gebruikt om de trigger configuratie te beschrijven.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Trigger - JavaScript-voorbeeld

In het volgende voor beeld ziet u een Service Bus trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van berichten](#trigger---message-metadata) gelezen en wordt een service bus wachtrij bericht geregistreerd. 

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Dit is het JavaScript-script-code:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---python-example"></a>Voor beeld van een trigger-python

In het volgende voor beeld ziet u hoe u een ServiceBus-wachtrij bericht via een trigger kunt lezen.

Een ServiceBus-binding wordt gedefinieerd in *Function. json* waarbij *type* is ingesteld op `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

De code in  *_\_init_\_. py* declareert een para meter als `func.ServiceBusMessage` waarmee u het wachtrij bericht in uw functie kunt lezen.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Trigger - kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)de volgende kenmerken om een service bus trigger te configureren:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  De constructor van het kenmerk wordt de naam van de wachtrij of onderwerp en abonnement. In Azure Functions-versie 1.x, kunt u ook de toegangsrechten van de verbinding opgeven. Als u geen toegangs rechten opgeeft, wordt de standaard waarde `Manage`. Zie de sectie [trigger-configuratie](#trigger---configuration) voor meer informatie.

  Hier volgt een voorbeeld waarin het kenmerk wordt gebruikt met een queryreeks-parameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  U kunt de eigenschap `Connection` instellen om het Service Bus account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Zie voor een volledig voor beeld [trigger- C# voor beeld](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Biedt een andere manier om op te geven van de Service Bus-account moet worden gebruikt. De constructor, wordt de naam van een app-instelling met een Service Bus-verbindingsreeks. Het kenmerk kan worden toegepast op de parameter, klasseniveau of methode. Het volgende voorbeeld toont het klasseniveau en methode:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

De Service Bus-account moet worden gebruikt, wordt bepaald in de volgende volgorde:

* De eigenschap `Connection` van het `ServiceBusTrigger`-kenmerk.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op dezelfde para meter als het kenmerk `ServiceBusTrigger`.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de functie.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de klasse.
* De 'AzureWebJobsServiceBus' app-instelling.

## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `ServiceBusTrigger`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op 'serviceBusTrigger'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | n.v.t. | Moet worden ingesteld op 'in'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**naam** | n.v.t. | De naam van de variabele die staat voor de wachtrij of onderwerp bericht in de functiecode aan te geven. Ingesteld op '$return' om te verwijzen naar de geretourneerde waarde van de functie. |
|**queueName**|**QueueName**|Naam van de wachtrij om te controleren.  Alleen ingesteld als bewaking van een wachtrij, niet voor een onderwerp.
|**onderwerpnaam**|**Onderwerpnaam**|Naam van het onderwerp om te controleren. Alleen ingesteld als een onderwerp, voor een wachtrij niet controleren.|
|**subscriptionName**|**SubscriptionName**|De naam van het abonnement om te controleren. Alleen ingesteld als een onderwerp, voor een wachtrij niet controleren.|
|**Combi**|**Verbinding**|De naam van een app-instelling met de Service Bus-verbindingsreeks moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', kunt u alleen het restant van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' MyServiceBus ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus connection string in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De verbindingsreeks moet voor een Service Bus-naamruimte, niet beperkt tot een specifieke wachtrij of onderwerp. |
|**accessRights**|**Toegang**|Toegangsrechten voor de verbindingsreeks. Beschik bare waarden zijn `manage` en `listen`. De standaard waarde is `manage`, wat aangeeft dat de `connection` de machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders wordt het Functions runtime mislukken probeert uit te voeren bewerkingen uit waarbij rechten beheren. In Azure Functions versie 2.x gebruikt, deze eigenschap is niet beschikbaar omdat de nieuwste versie van de Storage-SDK biedt geen ondersteuning voor bewerkingen beheren.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - gebruik

In C# en C#-script, kunt u de volgende parametertypen voor de wachtrij of onderwerp bericht:

* `string`-als het bericht tekst is.
* `byte[]`-nuttig voor binaire gegevens.
* Een aangepast type - als het bericht JSON bevat, Azure Functions probeert te deserialiseren van de JSON-gegevens.
* `BrokeredMessage`: geeft u het gedeserialiseerd bericht met de methode [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze para meters zijn voor Azure Functions versie 1. x; gebruik voor 2. x [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage`.

In Java script opent u de wachtrij of het onderwerp bericht met behulp van `context.bindings.<name from function.json>`. De Service Bus-bericht wordt doorgegeven in de functie als een tekenreeks of een JSON-object.

## <a name="trigger---poison-messages"></a>Trigger - Onverwerkbare berichten

Beheer van Onverwerkbare berichten afhandelen, kan niet worden beheerd of geconfigureerd in Azure Functions. Service Bus verwerkt Onverwerkbare berichten zelf.

## <a name="trigger---peeklock-behavior"></a>Trigger - PeekLock gedrag

De functions-runtime ontvangt een bericht in de [modus PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Als de functie is voltooid, wordt `Complete` op het bericht aangeroepen, of wordt `Abandon` aangeroepen als de functie mislukt. Als de functie langer dan de `PeekLock`-time-out wordt uitgevoerd, wordt de vergren deling automatisch vernieuwd zolang de functie wordt uitgevoerd. 

De `maxAutoRenewDuration` kan worden geconfigureerd in *host. json*, dat wordt toegewezen aan [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). De maximaal toegestane aantal voor deze instelling is 5 minuten op basis van de documentatie voor Service Bus, terwijl u de tijdslimiet functies van de standaardwaarde van 5 minuten tot 10 minuten kunt verhogen. Voor Service Bus-functies wouldn't u vervolgens doen omdat u de limiet voor het vernieuwen van Service Bus wordt overschreden.

## <a name="trigger---message-metadata"></a>Trigger - bericht-metagegevens

De trigger Service Bus biedt verschillende [Eigenschappen van meta gegevens](./functions-bindings-expressions-patterns.md#trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van de expressies in andere bindingen voor gegevensbinding of als parameters in uw code. Dit zijn eigenschappen van de klasse [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Het aantal bezorgingen.|
|`DeadLetterSource`|`string`|De dead-letter-bron.|
|`ExpiresAtUtc`|`DateTime`|De vervaldatum tijd in UTC.|
|`EnqueuedTimeUtc`|`DateTime`|De in de wachtrij-tijd in UTC.|
|`MessageId`|`string`|Een door de gebruiker gedefinieerde waarde die Service Bus gebruiken om dubbele berichten te identificeren als ingeschakeld.|
|`ContentType`|`string`|Een inhoudstype-id die door de afzender en de ontvanger voor specifieke logica van toepassingen wordt gebruikt.|
|`ReplyTo`|`string`|Het antwoordadres wachtrij.|
|`SequenceNumber`|`Int64`|Het unieke nummer toegewezen aan een bericht door Service Bus.|
|`To`|`string`|De verzenden naar een adres.|
|`Label`|`string`|De toepassingsspecifiek label.|
|`CorrelationId`|`string`|De correlatie-ID.|

> [!NOTE]
> Op dit moment is Service Bus-trigger die werkt met wacht rijen voor sessie ingeschakeld en abonnementen in de preview-versie. Volg [dit item](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) voor verdere updates hierover. 

Zie [code voorbeelden](#trigger---example) die gebruikmaken van deze eigenschappen eerder in dit artikel.

## <a name="output"></a>Uitvoer

Gebruik Azure Service Bus-Uitvoerbinding voor het verzenden van berichten in wachtrij of onderwerp.

## <a name="output---example"></a>Uitvoer - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#output---c-example)
* [C#script (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Uitvoer - voorbeeld met C#

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die een service bus wachtrij bericht verzendt:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Uitvoer - voorbeeld van C#-script

In het volgende voor beeld ziet u een service bus uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie een timertrigger gebruikt voor het verzenden van een wachtrijbericht elke 15 seconden.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Hier volgt een C#-script-code die wordt gemaakt van een enkel bericht:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Hier volgen enkele redenen C#-script-code die wordt gemaakt van meerdere berichten:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

### <a name="output---f-example"></a>Uitvoer - F# voorbeeld

In het volgende voor beeld ziet u een service bus uitvoer binding in een *Function. json* -bestand en een [ F# script functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie een timertrigger gebruikt voor het verzenden van een wachtrijbericht elke 15 seconden.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Hier volgt F# code die een enkel bericht maakt een script:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Uitvoer - Java-voorbeeld

In het volgende voor beeld ziet u een Java-functie die een bericht verzendt naar een Service Bus wachtrij `myqueue` wanneer dit wordt geactiveerd door een HTTP-aanvraag.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Gebruik in de [runtime-bibliotheek van Java functions](/java/api/overview/azure/functions/runtime)de `@QueueOutput` annotatie voor functie parameters waarvan de waarde naar een service bus wachtrij wordt geschreven.  Het parameter type moet `OutputBinding<T>`zijn, waarbij T een eigen Java-type is van een POJO.

Java-functies kunnen ook worden geschreven naar een Service Bus onderwerp. In het volgende voor beeld wordt de `@ServiceBusTopicOutput` aantekening gebruikt om de configuratie voor de uitvoer binding te beschrijven. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

### <a name="output---javascript-example"></a>Uitvoer - JavaScript-voorbeeld

In het volgende voor beeld ziet u een Service Bus uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie een timertrigger gebruikt voor het verzenden van een wachtrijbericht elke 15 seconden.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Hier volgt een JavaScript-script-code die wordt gemaakt van een enkel bericht:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Hier volgt een JavaScript-script-code die wordt gemaakt van meerdere berichten:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Voor beeld van de uitvoer-python

In het volgende voor beeld ziet u hoe u kunt schrijven naar een ServiceBus-wachtrij in python.

Een ServiceBue bindings definitie wordt gedefinieerd in *Function. json* waarbij *type* is ingesteld op `serviceBus`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

In  *_\_init_\_. py*kunt u een bericht naar de wachtrij schrijven door een waarde door te geven aan de `set`-methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

## <a name="output---attributes"></a>Uitvoer - kenmerken

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

De constructor van het kenmerk wordt de naam van de wachtrij of onderwerp en abonnement. U kunt ook de toegangsrechten van de verbinding opgeven. Het kiezen van de instelling voor toegangs rechten wordt uitgelegd in het gedeelte [output-Configuration](#output---configuration) . Hier volgt een voorbeeld waarin het kenmerk dat wordt toegepast op de geretourneerde waarde van de functie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

U kunt de eigenschap `Connection` instellen om het Service Bus account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer- C# voor beeld](#output---c-example).

U kunt het `ServiceBusAccount` kenmerk gebruiken om het Service Bus-account op te geven dat u wilt gebruiken bij klasse, methode of parameter niveau.  Zie [trigger-Attributes](#trigger---attributes)(Engelstalig) voor meer informatie.

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `ServiceBus`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op 'Service Bus'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | n.v.t. | Moet worden ingesteld op 'out'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**naam** | n.v.t. | De naam van de variabele die staat voor de wachtrij of onderwerp in de functiecode aan te geven. Ingesteld op '$return' om te verwijzen naar de geretourneerde waarde van de functie. |
|**queueName**|**QueueName**|Naam van de wachtrij.  Alleen ingesteld als Wachtrijberichten, niet voor een onderwerp te verzenden.
|**onderwerpnaam**|**Onderwerpnaam**|Naam van het onderwerp om te controleren. Alleen ingesteld als onderwerp verzonden, niet voor een wachtrij.|
|**Combi**|**Verbinding**|De naam van een app-instelling met de Service Bus-verbindingsreeks moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', kunt u alleen het restant van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' MyServiceBus ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus connection string in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De verbindingsreeks moet voor een Service Bus-naamruimte, niet beperkt tot een specifieke wachtrij of onderwerp.|
|**accessRights**|**Toegang**|Toegangsrechten voor de verbindingsreeks. Beschik bare waarden zijn `manage` en `listen`. De standaard waarde is `manage`, wat aangeeft dat de `connection` de machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders wordt het Functions runtime mislukken probeert uit te voeren bewerkingen uit waarbij rechten beheren. In Azure Functions versie 2.x gebruikt, deze eigenschap is niet beschikbaar omdat de nieuwste versie van de Storage-SDK biedt geen ondersteuning voor bewerkingen beheren.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

In Azure Functions 1. x wordt de wachtrij door de runtime gemaakt als deze nog niet bestaat en hebt u `accessRights` ingesteld op `manage`. In functies versie moet 2.x, de wachtrij of onderwerp al bestaan; Als u een wachtrij of onderwerp die niet bestaat opgeeft, wordt de functie mislukt. 

In C# en C#-script, kunt u de volgende parametertypen voor de Uitvoerbinding:

* `out T paramName` - `T` kan elk type JSON-serialiseerbaar zijn. Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt het bericht in functies gemaakt met een null-object.
* `out string`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out byte[]`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out BrokeredMessage`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 1. x)
* `out Message`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 2. x)
* `ICollector<T>` of `IAsyncCollector<T>`-voor het maken van meerdere berichten. Er wordt een bericht gemaakt wanneer u de `Add` methode aanroept.

Bij het werken C# met functies:

* Voor async-functies is een retour waarde of een `IAsyncCollector` vereist in plaats van een `out`-para meter.

* Als u toegang wilt krijgen tot de sessie-ID, bindt u met een [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) type en gebruikt u de eigenschap `sessionId`.

In Java script opent u de wachtrij of het onderwerp met behulp van `context.bindings.<name from function.json>`. U kunt een teken reeks, een byte matrix of een Java Script-object (gedeserialiseerd in JSON) toewijzen aan `context.binding.<name>`.

Als u een bericht wilt verzenden naar een wachtrij met ingeschakelde sessiesC# in niet-talen, gebruikt u de [Azure service bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoer binding.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Service Bus | [Service Bus fout codes](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus limieten](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor host.JSON

In deze sectie beschrijft de globale configuratie-instellingen beschikbaar voor deze binding in versie 2.x. Het onderstaande voorbeeld host.json bestand bevat alleen de versie 2.x-instellingen voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x [host. json Reference voor Azure functions versie 2. x](functions-host-json.md).

> [!NOTE]
> Zie [host. json Reference voor Azure functions 1. x](functions-host-json-v1.md)voor een verwijzing naar de host. json in functions 1. x.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|De maximale tijdsduur waarbinnen de vergrendeling van het bericht automatisch wordt vernieuwd.|
|automatisch aanvullen|true|De trigger moet of onmiddellijk markeren als voltooid (automatisch aanvullen) of wacht op verwerking voor het aanroepen van voltooid.|
|maxConcurrentCalls|16|Het maximale aantal gelijktijdige oproepen aan de callback die de bericht-pomp moet starten. Standaard verwerkt de Functions-runtime meerdere berichten tegelijkertijd. Stel `maxConcurrentCalls` in op 1 om de runtime te vragen om slechts één wachtrij of onderwerp bericht tegelijk te verwerken. |
|prefetchCount|n.v.t.|De standaard PrefetchCount die wordt gebruikt door de onderliggende MessageReceiver.|


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
