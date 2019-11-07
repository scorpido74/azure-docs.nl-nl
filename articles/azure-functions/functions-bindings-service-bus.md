---
title: Azure Service Bus bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Service Bus triggers en bindingen in Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functies, gebeurtenis verwerking, dynamische compute, serverloze architectuur
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 64c99c6e7e33be5856e67db0500bf48123cdcf09
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614484"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u met Azure Service Bus bindingen in Azure Functions kunt werken. Azure Functions ondersteunt trigger-en uitvoer bindingen voor Service Bus-wacht rijen en-onderwerpen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

De Service Bus bindingen zijn opgenomen in het [micro soft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-pakket, versie 2. x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakketten-functions 2. x

De Service Bus-bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet, versie 3. x. De bron code voor het pakket bevindt zich in de [Azure-functions-servicebus-extension github-](https://github.com/Azure/azure-functions-servicebus-extension) opslag plaats.

> [!NOTE]
> Versie 2. x maakt geen onderwerp of abonnement dat is geconfigureerd in het `ServiceBusTrigger`-exemplaar. Versie 2. x is gebaseerd op [micro soft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) en verwerkt geen wachtrij beheer.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Gebruik de Service Bus-trigger om te reageren op berichten van een Service Bus wachtrij of onderwerp. 

## <a name="trigger---example"></a>Trigger-voor beeld

Zie het taalspecifieke voor beeld:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Trigger- C# voor beeld

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

### <a name="trigger---c-script-example"></a>Voor beeld C# van trigger-script

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

Dit is de C# script code:

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

### <a name="trigger---f-example"></a>Trigger- F# voor beeld

In het volgende voor beeld ziet u de binding van een service bus trigger in een *Function. json* -bestand en een [ F# functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie registreert een Service Bus wachtrij bericht. 

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

Dit is de F# script code:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Trigger-Java-voor beeld

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

### <a name="trigger---javascript-example"></a>Trigger-java script-voor beeld

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

Hier volgt de Java script-script code:

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

## <a name="trigger---attributes"></a>Trigger-kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)de volgende kenmerken om een service bus trigger te configureren:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  De constructor van het kenmerk neemt de naam van de wachtrij of het onderwerp en het abonnement. In Azure Functions versie 1. x kunt u ook de toegangs rechten voor de verbinding opgeven. Als u geen toegangs rechten opgeeft, wordt de standaard waarde `Manage`. Zie de sectie [trigger-configuratie](#trigger---configuration) voor meer informatie.

  Hier volgt een voor beeld waarin het kenmerk wordt weer gegeven met een teken reeks parameter:

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

  Biedt een andere manier om het Service Bus-account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een Service Bus connection string bevat. Het kenmerk kan worden toegepast op de para meter, methode of class-niveau. In het volgende voor beeld wordt het niveau klasse en de methode weer gegeven:

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

Het Service Bus account dat moet worden gebruikt, wordt in de volgende volg orde bepaald:

* De eigenschap `Connection` van het `ServiceBusTrigger`-kenmerk.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op dezelfde para meter als het kenmerk `ServiceBusTrigger`.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de functie.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de klasse.
* De instelling van de app ' AzureWebJobsServiceBus '.

## <a name="trigger---configuration"></a>Trigger-configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `ServiceBusTrigger`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' serviceBusTrigger '. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op in. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die de wachtrij of het onderwerp van het bericht in de functie code vertegenwoordigt. Ingesteld op ' $return ' om te verwijzen naar de retour waarde van de functie. |
|**queueName**|**QueueName**|De naam van de wachtrij die moet worden bewaakt.  Stel deze waarde alleen in als u een wachtrij bewaken, niet voor een onderwerp.
|**onderwerpnaam**|**Onderwerpnaam**|De naam van het te bewaken onderwerp. Stel deze waarde alleen in als u een onderwerp bewaken, niet voor een wachtrij.|
|**subscriptionName**|**SubscriptionName**|De naam van het abonnement dat moet worden bewaakt. Stel deze waarde alleen in als u een onderwerp bewaken, niet voor een wachtrij.|
|**Combi**|**Verbinding**|De naam van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met "AzureWebJobs", kunt u alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' MyServiceBus ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus connection string in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De connection string moet voor een Service Bus naam ruimte zijn, niet beperkt tot een specifieke wachtrij of een specifiek onderwerp. |
|**accessRights**|**Toegang**|Toegangs rechten voor de connection string. Beschik bare waarden zijn `manage` en `listen`. De standaard waarde is `manage`, wat aangeeft dat de `connection` de machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders kan het uitvoeren van de functions-runtime geen bewerkingen uitvoeren waarvoor het beheer van rechten nodig is. In Azure Functions versie 2. x is deze eigenschap niet beschikbaar, omdat de meest recente versie van de opslag-SDK geen beheer bewerkingen ondersteunt.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger-gebruik

In C# en C# script kunt u de volgende parameter typen gebruiken voor het bericht in de wachtrij of het onderwerp:

* `string`-als het bericht tekst is.
* `byte[]`-nuttig voor binaire gegevens.
* Een aangepast type: als het bericht JSON bevat, probeert Azure Functions de JSON-gegevens te deserialiseren.
* `BrokeredMessage`: geeft u het gedeserialiseerd bericht met de methode [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze para meters zijn voor Azure Functions versie 1. x; gebruik voor 2. x [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage`.

In Java script opent u de wachtrij of het onderwerp bericht met behulp van `context.bindings.<name from function.json>`. Het Service Bus bericht wordt door gegeven aan de functie als een teken reeks of een JSON-object.

## <a name="trigger---poison-messages"></a>Trigger-verontreinigde berichten

De verwerking van verontreinigde berichten kan niet worden beheerd of geconfigureerd in Azure Functions. Service Bus verwerkt verontreinigde berichten zelf.

## <a name="trigger---peeklock-behavior"></a>Trigger-PeekLock-gedrag

De functions-runtime ontvangt een bericht in de [modus PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Als de functie is voltooid, wordt `Complete` op het bericht aangeroepen, of wordt `Abandon` aangeroepen als de functie mislukt. Als de functie langer dan de `PeekLock`-time-out wordt uitgevoerd, wordt de vergren deling automatisch vernieuwd zolang de functie wordt uitgevoerd. 

De `maxAutoRenewDuration` kan worden geconfigureerd in *host. json*, dat wordt toegewezen aan [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Het maximum aantal dat is toegestaan voor deze instelling is 5 minuten volgens de Service Bus-documentatie, terwijl u de tijds limiet van functies kunt verhogen van de standaard waarde van 5 minuten tot 10 minuten. Voor Service Bus functies wilt u dat niet doen, omdat u de Service Bus vernieuwings limiet overschrijdt.

## <a name="trigger---message-metadata"></a>Trigger-bericht meta gegevens

De trigger Service Bus biedt verschillende [Eigenschappen van meta gegevens](./functions-bindings-expressions-patterns.md#trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van binding expressies in andere bindingen of als para meters in uw code. Dit zijn eigenschappen van de klasse [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Het aantal leveringen.|
|`DeadLetterSource`|`string`|De bron voor de onbestelbare letter.|
|`ExpiresAtUtc`|`DateTime`|De verloop tijd in UTC.|
|`EnqueuedTimeUtc`|`DateTime`|De in wachtrij geplaatste tijd in UTC.|
|`MessageId`|`string`|Een door de gebruiker gedefinieerde waarde die Service Bus kan gebruiken om dubbele berichten te identificeren, indien ingeschakeld.|
|`ContentType`|`string`|Een inhouds type-id die wordt gebruikt door de afzender en ontvanger voor toepassingsspecifieke logica.|
|`ReplyTo`|`string`|Het adres van de wachtrij voor beantwoorden.|
|`SequenceNumber`|`Int64`|Het unieke nummer dat is toegewezen aan een bericht door de Service Bus.|
|`To`|`string`|Het adres voor verzenden naar.|
|`Label`|`string`|Het specifieke label van de toepassing.|
|`CorrelationId`|`string`|De correlatie-ID.|

> [!NOTE]
> Op dit moment is Service Bus-trigger die werkt met wacht rijen voor sessie ingeschakeld en abonnementen in de preview-versie. Volg [dit item](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) voor verdere updates hierover. 

Zie [code voorbeelden](#trigger---example) die gebruikmaken van deze eigenschappen eerder in dit artikel.

## <a name="trigger---hostjson-properties"></a>Trigger-host. json-eigenschappen

Het bestand [host. json](functions-host-json.md#servicebus) bevat instellingen die het gedrag van service bus trigger regelen.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige aanroepen naar de retour aanroep dat de bericht pomp moet initiëren. De functie runtime verwerkt standaard meerdere berichten tegelijk. Stel `maxConcurrentCalls` in op 1 om de runtime te vragen om slechts één wachtrij of onderwerp bericht tegelijk te verwerken. |
|prefetchCount|N.v.t.|De standaard PrefetchCount die wordt gebruikt door de onderliggende MessageReceiver.|
|maxAutoRenewDuration|00:05:00|De maximale duur waarbinnen de bericht vergrendeling automatisch wordt vernieuwd.|

## <a name="output"></a>Uitvoer

Gebruik Azure Service Bus uitvoer binding om berichten uit de wachtrij of het onderwerp te verzenden.

## <a name="output---example"></a>Uitvoer-voor beeld

Zie het taalspecifieke voor beeld:

* [C#](#output---c-example)
* [C#script (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Uitvoer- C# voor beeld

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

### <a name="output---c-script-example"></a>Voor beeld C# van uitvoer script

In het volgende voor beeld ziet u een service bus uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie gebruikt een timer trigger om elke 15 seconden een wachtrij bericht te verzenden.

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

Dit is C# de script code waarmee één bericht wordt gemaakt:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Dit is C# de script code voor het maken van meerdere berichten:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

### <a name="output---f-example"></a>Uitvoer- F# voor beeld

In het volgende voor beeld ziet u een service bus uitvoer binding in een *Function. json* -bestand en een [ F# script functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. De functie gebruikt een timer trigger om elke 15 seconden een wachtrij bericht te verzenden.

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

Dit is F# de script code waarmee één bericht wordt gemaakt:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Uitvoer-Java-voor beeld

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

### <a name="output---javascript-example"></a>Uitvoer-java script-voor beeld

In het volgende voor beeld ziet u een Service Bus uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie gebruikt een timer trigger om elke 15 seconden een wachtrij bericht te verzenden.

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

Hier volgt een Java script-script code waarmee één bericht wordt gemaakt:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Dit is de Java script-script code waarmee meerdere berichten worden gemaakt:

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

## <a name="output---attributes"></a>Uitvoer kenmerken

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

De constructor van het kenmerk neemt de naam van de wachtrij of het onderwerp en het abonnement. U kunt ook de toegangs rechten van de verbinding opgeven. Het kiezen van de instelling voor toegangs rechten wordt uitgelegd in het gedeelte [output-Configuration](#output---configuration) . Hier volgt een voor beeld waarin het kenmerk wordt weer gegeven dat wordt toegepast op de geretourneerde waarde van de functie:

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

## <a name="output---configuration"></a>Uitvoer-configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `ServiceBus`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' serviceBus '. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op out. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die de wachtrij of het onderwerp in de functie code vertegenwoordigt. Ingesteld op ' $return ' om te verwijzen naar de retour waarde van de functie. |
|**queueName**|**QueueName**|De naam van de wachtrij.  Stel deze waarde alleen in als u wachtrij berichten verzendt, niet voor een onderwerp.
|**onderwerpnaam**|**Onderwerpnaam**|De naam van het te bewaken onderwerp. Stel deze waarde alleen in als er onderwerp-berichten worden verzonden, niet voor een wachtrij.|
|**Combi**|**Verbinding**|De naam van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met "AzureWebJobs", kunt u alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' MyServiceBus ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus connection string in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De connection string moet voor een Service Bus naam ruimte zijn, niet beperkt tot een specifieke wachtrij of een specifiek onderwerp.|
|**accessRights**|**Toegang**|Toegangs rechten voor de connection string. Beschik bare waarden zijn `manage` en `listen`. De standaard waarde is `manage`, wat aangeeft dat de `connection` de machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders kan het uitvoeren van de functions-runtime geen bewerkingen uitvoeren waarvoor het beheer van rechten nodig is. In Azure Functions versie 2. x is deze eigenschap niet beschikbaar, omdat de meest recente versie van de opslag-SDK geen beheer bewerkingen ondersteunt.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

In Azure Functions 1. x wordt de wachtrij door de runtime gemaakt als deze nog niet bestaat en hebt u `accessRights` ingesteld op `manage`. In functies versie 2. x moet de wachtrij of het onderwerp al bestaan; Als u een wachtrij of onderwerp opgeeft dat niet bestaat, mislukt de functie. 

In C# en C# script kunt u de volgende parameter typen gebruiken voor de uitvoer binding:

* `out T paramName` - `T` kan elk type JSON-serialiseerbaar zijn. Als de waarde van de para meter null is wanneer de functie wordt afgesloten, worden met functies het bericht met een null-object gemaakt.
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

## <a name="exceptions-and-return-codes"></a>Uitzonde ringen en retour codes

| Dwingen | Naslaginformatie |
|---|---|
| Service Bus | [Service Bus fout codes](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus limieten](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor de host. json

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x. Het onderstaande voor beeld van host. JSON-bestand bevat alleen de instellingen van versie 2. x voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x [host. json Reference voor Azure functions versie 2. x](functions-host-json.md).

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
|maxAutoRenewDuration|00:05:00|De maximale duur waarbinnen de bericht vergrendeling automatisch wordt vernieuwd.|
|Automatisch aanvullen|true|Hiermee wordt aangegeven of de trigger onmiddellijk moet markeren als voltooid (automatisch aanvullen) of wacht totdat de verwerking is voltooid.|
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige aanroepen naar de retour aanroep dat de bericht pomp moet initiëren. De functie runtime verwerkt standaard meerdere berichten tegelijk. Stel `maxConcurrentCalls` in op 1 om de runtime te vragen om slechts één wachtrij of onderwerp bericht tegelijk te verwerken. |
|prefetchCount|N.v.t.|De standaard PrefetchCount die wordt gebruikt door de onderliggende MessageReceiver.|


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
