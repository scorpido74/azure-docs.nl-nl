---
title: Azure Service Bus-bindingen voor Azure Functions
description: Over het gebruik van Azure Service Bus-triggers en bindingen in Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 424d797410c091dc53687284c2b32e2f1f0358e1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549067"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u werkt met Azure Service Bus-bindingen in Azure Functions. Azure Functions ondersteunt trigger- en uitvoerbindingen voor Service Bus-wachtrijen en -onderwerpen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De Service Bus-bindingen zijn opgegeven in de [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-pakket versie 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De Service Bus-bindingen zijn opgegeven in de [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet-pakket versie 3.x. De bron code voor het pakket bevindt zich in de [Azure-functions-servicebus-extension github-](https://github.com/Azure/azure-functions-servicebus-extension) opslag plaats.

> [!NOTE]
> In versie 2. x en hoger wordt het onderwerp of abonnement dat is geconfigureerd in het `ServiceBusTrigger`-exemplaar niet gemaakt. Deze versies zijn gebaseerd op [micro soft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) , waarmee wachtrij beheer niet wordt afgehandeld.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

De Service Bus-trigger te gebruiken om te reageren op berichten van een Service Bus-wachtrij of onderwerp. 

## <a name="trigger---example"></a>Trigger - voorbeeld

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) die leest [bericht metagegevens](#trigger---message-metadata) en een Service Bus-wachtrij-bericht vastlegt:

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

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Het volgende voorbeeld ziet u een Service Bus-trigger binding in een *function.json* bestand en een [C#-scriptfunctie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie leest [bericht metagegevens](#trigger---message-metadata) en een Service Bus-wachtrij-bericht vastlegt.

Hier volgt de binding-gegevens de *function.json* bestand:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Het volgende voorbeeld ziet u een Service Bus-trigger binding in een *function.json* bestand en een [JavaScript-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie leest [bericht metagegevens](#trigger---message-metadata) en een Service Bus-wachtrij-bericht vastlegt. 

Hier volgt de binding-gegevens de *function.json* bestand:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u een Service Bus wachtrij bericht kunt lezen via een trigger.

Een Service Bus binding is gedefinieerd in *Function. json* waarbij *type* is ingesteld op `serviceBusTrigger`.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="trigger---attributes-and-annotations"></a>Trigger-kenmerken en aantekeningen

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In [C#-klassebibliotheken](functions-dotnet-class-library.md), de volgende kenmerken gebruiken om te configureren van een Service Bus-trigger:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  De constructor van het kenmerk wordt de naam van de wachtrij of onderwerp en abonnement. In Azure Functions-versie 1.x, kunt u ook de toegangsrechten van de verbinding opgeven. Als u geen toegangsrechten opgeeft, wordt standaard `Manage`. Zie voor meer informatie de [Trigger - configuratie](#trigger---configuration) sectie.

  Hier volgt een voorbeeld waarin het kenmerk wordt gebruikt met een queryreeks-parameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  U kunt de eigenschap `Connection` instellen om de naam op te geven van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Zie voor een volledig voor beeld [trigger-voor beeld](#trigger---example).

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

* De `ServiceBusTrigger` van kenmerk `Connection` eigenschap.
* De `ServiceBusAccount` kenmerk toegepast op de dezelfde parameter als de `ServiceBusTrigger` kenmerk.
* De `ServiceBusAccount` kenmerk toegepast op de functie.
* De `ServiceBusAccount` kenmerk toegepast op de klasse.
* De 'AzureWebJobsServiceBus' app-instelling.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="javatabjava"></a>[Java](#tab/java)

Met de `ServiceBusQueueTrigger` aantekening kunt u een functie maken die wordt uitgevoerd wanneer een Service Bus wachtrij bericht wordt gemaakt. Beschik bare configuratie opties zijn wachtrij naam en connection string naam.

Met de `ServiceBusTopicTrigger` aantekening kunt u een onderwerp en een abonnement aanwijzen om te bepalen welke gegevens de functie activeren.

Zie het [voor beeld](#trigger---example) van de trigger voor meer details.

---

## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `ServiceBusTrigger` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op 'serviceBusTrigger'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | n.v.t. | Moet worden ingesteld op 'in'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**De naam** | n.v.t. | De naam van de variabele die staat voor de wachtrij of onderwerp bericht in de functiecode aan te geven. |
|**queueName**|**Wachtrijnaam**|Naam van de wachtrij om te controleren.  Alleen ingesteld als bewaking van een wachtrij, niet voor een onderwerp.
|**topicName**|**topicName**|Naam van het onderwerp om te controleren. Alleen ingesteld als een onderwerp, voor een wachtrij niet controleren.|
|**subscriptionName**|**subscriptionName**|De naam van het abonnement om te controleren. Alleen ingesteld als een onderwerp, voor een wachtrij niet controleren.|
|**verbinding**|**Verbinding**|De naam van een app-instelling met de Service Bus-verbindingsreeks moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', kunt u alleen het restant van de naam opgeven. Als u bijvoorbeeld `connection` naar 'MyServiceBus', de Functions-runtime ziet eruit voor een app-instelling die is met de naam "AzureWebJobsMyServiceBus." Als u niets `connection` leeg is, wordt de Functions-runtime maakt gebruik van de standaard Service Bus-verbindingsreeks in de app-instelling met de naam 'AzureWebJobsServiceBus'.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De verbindingsreeks moet voor een Service Bus-naamruimte, niet beperkt tot een specifieke wachtrij of onderwerp. |
|**accessRights**|**Toegang**|Toegangsrechten voor de verbindingsreeks. Beschikbare waarden zijn `manage` en `listen`. De standaardwaarde is `manage`, waarmee wordt aangegeven dat de `connection` heeft de **beheren** machtiging. Als u een verbindingsreeks die u niet beschikt over de **beheren** , machtigingenset `accessRights` ' luisteren '. Anders wordt het Functions runtime mislukken probeert uit te voeren bewerkingen uit waarbij rechten beheren. In Azure Functions versie 2. x en hoger is deze eigenschap niet beschikbaar omdat de meest recente versie van de Service Bus SDK geen ondersteuning biedt voor het beheren van bewerkingen.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` als er verbinding wordt gemaakt met een [sessie bewuste](../service-bus-messaging/message-sessions.md) wachtrij of een abonnement. `false` anders de standaard waarde.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - gebruik

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

De volgende parameter typen zijn beschikbaar voor het bericht in de wachtrij of het onderwerp:

* `string` -Als het bericht tekst is.
* `byte[]` -Dit is handig voor binaire gegevens.
* Een aangepast type - als het bericht JSON bevat, Azure Functions probeert te deserialiseren van de JSON-gegevens.
* `BrokeredMessage`: geeft u het gedeserialiseerd bericht met de methode [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze parameter typen zijn voor Azure Functions versie 1. x; gebruik voor 2. x en hoger [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage`.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

De volgende parameter typen zijn beschikbaar voor het bericht in de wachtrij of het onderwerp:

* `string` -Als het bericht tekst is.
* `byte[]` -Dit is handig voor binaire gegevens.
* Een aangepast type - als het bericht JSON bevat, Azure Functions probeert te deserialiseren van de JSON-gegevens.
* `BrokeredMessage`: geeft u het gedeserialiseerd bericht met de methode [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze para meters zijn voor Azure Functions versie 1. x; gebruik voor 2. x en hoger [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Toegang tot de wachtrij of het onderwerp bericht met behulp van `context.bindings.<name from function.json>`. De Service Bus-bericht wordt doorgegeven in de functie als een tekenreeks of een JSON-object.

# <a name="pythontabpython"></a>[Python](#tab/python)

Het wachtrij bericht is beschikbaar voor de functie via een para meter die is getypt als `func.ServiceBusMessage`. De Service Bus-bericht wordt doorgegeven in de functie als een tekenreeks of een JSON-object.

# <a name="javatabjava"></a>[Java](#tab/java)

Het binnenkomende Service Bus-bericht is beschikbaar via een `ServiceBusQueueMessage`-of `ServiceBusTopicMessage`-para meter.

[Zie het voor beeld voor meer informatie](#trigger).

---

## <a name="trigger---poison-messages"></a>Trigger - Onverwerkbare berichten

Beheer van Onverwerkbare berichten afhandelen, kan niet worden beheerd of geconfigureerd in Azure Functions. Service Bus verwerkt Onverwerkbare berichten zelf.

## <a name="trigger---peeklock-behavior"></a>Trigger - PeekLock gedrag

De Functions-runtime ontvangt een bericht in [PeekLock modus](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Wordt de `Complete` op dat wordt weergegeven als de functie is voltooid of aanroepen `Abandon` als de functie is mislukt. Als de functie langer duurt dan uitvoeren de `PeekLock` time-out voor de vergrendeling automatisch is verlengd, zolang de functie wordt uitgevoerd. 

De `maxAutoRenewDuration` kan worden geconfigureerd in *host. json*, dat wordt toegewezen aan [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). De maximaal toegestane aantal voor deze instelling is 5 minuten op basis van de documentatie voor Service Bus, terwijl u de tijdslimiet functies van de standaardwaarde van 5 minuten tot 10 minuten kunt verhogen. Voor Service Bus-functies wouldn't u vervolgens doen omdat u de limiet voor het vernieuwen van Service Bus wordt overschreden.

## <a name="trigger---message-metadata"></a>Trigger - bericht-metagegevens

De Service Bus-trigger bevat diverse [metagegevenseigenschappen](./functions-bindings-expressions-patterns.md#trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van de expressies in andere bindingen voor gegevensbinding of als parameters in uw code. Dit zijn de eigenschappen van de [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) klasse.

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
|`Label`|`string`|Het toepassingsspecifiek label.|
|`CorrelationId`|`string`|De correlatie-ID.|

> [!NOTE]
> Service Bus trigger die werkt met wacht rijen voor sessie ingeschakeld en abonnementen worden weer gegeven in de preview-versie. Volg [dit item](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) voor verdere updates hierover. 

Zie [codevoorbeelden](#trigger---example) die eerder in dit artikel gebruikmaken van deze eigenschappen.

## <a name="output"></a>Uitvoer

Gebruik Azure Service Bus-Uitvoerbinding voor het verzenden van berichten in wachtrij of onderwerp.

### <a name="output---example"></a>Uitvoer - voorbeeld

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) het bericht van een Service Bus-wachtrij worden verzonden:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Het volgende voorbeeld ziet u een Service Bus-Uitvoerbinding een *function.json* bestand en een [C#-scriptfunctie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie een timertrigger gebruikt voor het verzenden van een wachtrijbericht elke 15 seconden.

Hier volgt de binding-gegevens de *function.json* bestand:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Het volgende voorbeeld ziet u een Service Bus-Uitvoerbinding een *function.json* bestand en een [JavaScript-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie een timertrigger gebruikt voor het verzenden van een wachtrijbericht elke 15 seconden.

Hier volgt de binding-gegevens de *function.json* bestand:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u kunt schrijven naar een Service Bus wachtrij in python.

Een definitie van een Service Bus binding is gedefinieerd in *Function. json* waarbij *type* is ingesteld op `serviceBus`.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Het volgende voorbeeld ziet u een Java-functie die een bericht naar een Service Bus-wachtrij verzendt `myqueue` wanneer ze worden geactiveerd door een HTTP-aanvraag.

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

 In de [Java functions runtime library](/java/api/overview/azure/functions/runtime), gebruikt u de `@QueueOutput` aantekening op functieparameters waarvan de waarde kan worden geschreven naar een Service Bus-wachtrij.  Het parametertype moet `OutputBinding<T>`, waarbij T alle systeemeigen Java-type van een POJO.

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

---

## <a name="output---attributes-and-annotations"></a>Uitvoer-kenmerken en aantekeningen

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In [C#-klassebibliotheken](functions-dotnet-class-library.md), gebruikt u de [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

De constructor van het kenmerk wordt de naam van de wachtrij of onderwerp en abonnement. U kunt ook de toegangsrechten van de verbinding opgeven. Het kiezen van de toegangsrechten instellen wordt uitgelegd in de [uitvoer - configuratie](#output---configuration) sectie. Hier volgt een voorbeeld waarin het kenmerk dat wordt toegepast op de geretourneerde waarde van de functie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

U kunt de eigenschap `Connection` instellen om de naam op te geven van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer-voor beeld](#output---example).

U kunt de `ServiceBusAccount` kenmerk om op te geven van de Service Bus-account moet worden gebruikt op het niveau van klasse, methode of parameter.  Zie voor meer informatie, [Trigger - kenmerken](#trigger---attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="javatabjava"></a>[Java](#tab/java)

De aantekeningen `ServiceBusQueueOutput` en `ServiceBusTopicOutput` zijn beschikbaar om een bericht te schrijven als uitvoer functie. De para meter die is gedecoreerd met deze aantekeningen moet worden gedeclareerd als een `OutputBinding<T>` waarbij `T` het type is dat overeenkomt met het type van het bericht.

---

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `ServiceBus` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op 'Service Bus'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | n.v.t. | Moet worden ingesteld op 'out'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**De naam** | n.v.t. | De naam van de variabele die staat voor de wachtrij of onderwerp bericht in de functiecode aan te geven. Ingesteld op '$return' om te verwijzen naar de geretourneerde waarde van de functie. |
|**queueName**|**Wachtrijnaam**|Naam van de wachtrij.  Alleen ingesteld als Wachtrijberichten, niet voor een onderwerp te verzenden.
|**topicName**|**topicName**|De naam van het onderwerp. Alleen ingesteld als onderwerp verzonden, niet voor een wachtrij.|
|**verbinding**|**Verbinding**|De naam van een app-instelling met de Service Bus-verbindingsreeks moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', kunt u alleen het restant van de naam opgeven. Als u bijvoorbeeld `connection` naar 'MyServiceBus', de Functions-runtime ziet eruit voor een app-instelling die is met de naam "AzureWebJobsMyServiceBus." Als u niets `connection` leeg is, wordt de Functions-runtime maakt gebruik van de standaard Service Bus-verbindingsreeks in de app-instelling met de naam 'AzureWebJobsServiceBus'.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De verbindingsreeks moet voor een Service Bus-naamruimte, niet beperkt tot een specifieke wachtrij of onderwerp.|
|**accessRights**|**Toegang**|Toegangsrechten voor de verbindingsreeks. Beschikbare waarden zijn `manage` en `listen`. De standaardwaarde is `manage`, waarmee wordt aangegeven dat de `connection` heeft de **beheren** machtiging. Als u een verbindingsreeks die u niet beschikt over de **beheren** , machtigingenset `accessRights` ' luisteren '. Anders wordt het Functions runtime mislukken probeert uit te voeren bewerkingen uit waarbij rechten beheren. In Azure Functions versie 2. x en hoger is deze eigenschap niet beschikbaar omdat de meest recente versie van de Service Bus SDK geen ondersteuning biedt voor het beheren van bewerkingen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

In Azure Functions 1.x, de runtime maakt de wachtrij als deze nog niet bestaat en u hebt ingesteld `accessRights` naar `manage`. In functies versie 2. x en hoger moet de wachtrij of het onderwerp al bestaan; Als u een wachtrij of onderwerp opgeeft dat niet bestaat, mislukt de functie. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T` geen JSON-geserialiseerd type kan zijn. Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt het bericht in functies gemaakt met een null-object.
* `out string` -Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt in functies een bericht niet maken.
* `out byte[]` -Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt in functies een bericht niet maken.
* `out BrokeredMessage`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 1. x)
* `out Message`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 2. x en hoger)
* `ICollector<T>` of `IAsyncCollector<T>` : voor het maken van meerdere berichten. Een bericht wordt gemaakt bij het aanroepen van de `Add` methode.

Bij het werken C# met functies:

* Voor async-functies is een retour waarde of een `IAsyncCollector` vereist in plaats van een `out`-para meter.

* Als u toegang wilt krijgen tot de sessie-ID, bindt u met een [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) type en gebruikt u de eigenschap `sessionId`.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T` geen JSON-geserialiseerd type kan zijn. Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt het bericht in functies gemaakt met een null-object.
* `out string` -Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt in functies een bericht niet maken.
* `out byte[]` -Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt in functies een bericht niet maken.
* `out BrokeredMessage`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 1. x)
* `out Message`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 2. x en hoger)
* `ICollector<T>` of `IAsyncCollector<T>` : voor het maken van meerdere berichten. Een bericht wordt gemaakt bij het aanroepen van de `Add` methode.

Bij het werken C# met functies:

* Voor async-functies is een retour waarde of een `IAsyncCollector` vereist in plaats van een `out`-para meter.

* Als u toegang wilt krijgen tot de sessie-ID, bindt u met een [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) type en gebruikt u de eigenschap `sessionId`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Toegang tot de wachtrij of het onderwerp met behulp van `context.bindings.<name from function.json>`. U kunt een teken reeks, een byte matrix of een Java Script-object (gedeserialiseerd in JSON) toewijzen aan `context.binding.<name>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gebruik de [Azure service bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoer binding.

# <a name="javatabjava"></a>[Java](#tab/java)

Gebruik de [Azure service bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoer binding.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Service Bus | [Service Bus-foutcodes](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus-limieten](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor host.JSON

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. Het onderstaande voor beeld van host. JSON-bestand bevat alleen de instellingen voor deze binding. Zie voor meer informatie over globale configuratie-instellingen [host. json Reference voor Azure functions versie](functions-host-json.md).

> [!NOTE]
> Voor een verwijzing van host.json in functies 1.x, Zie [naslaginformatie over host.json voor Azure Functions 1.x](functions-host-json-v1.md).

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
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|De maximale tijdsduur waarbinnen de vergrendeling van het bericht automatisch wordt vernieuwd.|
|automatisch aanvullen|waar|Hiermee wordt aangegeven of de trigger het bericht onmiddellijk moet markeren als voltooid (automatisch aanvullen) of wacht totdat de functie is afgesloten.|
|maxConcurrentCalls|16|Het maximale aantal gelijktijdige oproepen aan de callback die de bericht-pomp moet starten. Standaard verwerkt de Functions-runtime meerdere berichten tegelijkertijd. Instellen om de runtime voor het verwerken van alleen een één wachtrij of onderwerp bericht op een tijdstip, `maxConcurrentCalls` op 1. |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en bindingen](functions-triggers-bindings.md)
