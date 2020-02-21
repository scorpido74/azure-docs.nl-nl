---
title: Azure Service Bus-bindingen voor Azure Functions
description: Meer informatie over het verzenden van Azure Service Bus berichten vanuit Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493044"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus uitvoer binding voor Azure Functions

Gebruik Azure Service Bus-Uitvoerbinding voor het verzenden van berichten in wachtrij of onderwerp.

Zie het [overzicht](functions-bindings-service-bus-output.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een Java-functie die een bericht verzendt naar een Service Bus wachtrij `myqueue` wanneer deze wordt geactiveerd door een HTTP-aanvraag.

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

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

De constructor van het kenmerk wordt de naam van de wachtrij of onderwerp en abonnement. U kunt ook de toegangsrechten van de verbinding opgeven. Het kiezen van de instelling voor toegangs rechten wordt uitgelegd in het gedeelte [output-Configuration](#configuration) . Hier volgt een voorbeeld waarin het kenmerk dat wordt toegepast op de geretourneerde waarde van de functie:

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

Zie voor een volledig voor beeld [uitvoer-voor beeld](#example).

U kunt het `ServiceBusAccount` kenmerk gebruiken om het Service Bus-account op te geven dat u wilt gebruiken bij klasse, methode of parameter niveau.  Zie [trigger-Attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations)(Engelstalig) voor meer informatie.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

De aantekeningen `ServiceBusQueueOutput` en `ServiceBusTopicOutput` zijn beschikbaar om een bericht te schrijven als uitvoer functie. De para meter die is gedecoreerd met deze aantekeningen moet worden gedeclareerd als een `OutputBinding<T>` waarbij `T` het type is dat overeenkomt met het type van het bericht.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `ServiceBus`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op 'Service Bus'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**naam** | N.v.t. | De naam van de variabele die staat voor de wachtrij of onderwerp bericht in de functiecode aan te geven. Ingesteld op '$return' om te verwijzen naar de geretourneerde waarde van de functie. |
|**queueName**|**QueueName**|Naam van de wachtrij.  Alleen ingesteld als Wachtrijberichten, niet voor een onderwerp te verzenden.
|**onderwerpnaam**|**Onderwerpnaam**|De naam van het onderwerp. Alleen ingesteld als onderwerp verzonden, niet voor een wachtrij.|
|**Combi**|**Verbinding**|De naam van een app-instelling met de Service Bus-verbindingsreeks moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', kunt u alleen het restant van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' MyServiceBus ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus connection string in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De verbindingsreeks moet voor een Service Bus-naamruimte, niet beperkt tot een specifieke wachtrij of onderwerp.|
|**accessRights**|**Toegang**|Toegangsrechten voor de verbindingsreeks. Beschik bare waarden zijn `manage` en `listen`. De standaard waarde is `manage`, wat aangeeft dat de `connection` de machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders wordt het Functions runtime mislukken probeert uit te voeren bewerkingen uit waarbij rechten beheren. In Azure Functions versie 2. x en hoger is deze eigenschap niet beschikbaar omdat de meest recente versie van de Service Bus SDK geen ondersteuning biedt voor het beheren van bewerkingen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

In Azure Functions 1. x wordt de wachtrij door de runtime gemaakt als deze nog niet bestaat en hebt u `accessRights` ingesteld op `manage`. In functies versie 2. x en hoger moet de wachtrij of het onderwerp al bestaan; Als u een wachtrij of onderwerp opgeeft dat niet bestaat, mislukt de functie. 

# <a name="c"></a>[C#](#tab/csharp)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T` kan elk type JSON-serialiseerbaar zijn. Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt het bericht in functies gemaakt met een null-object.
* `out string`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out byte[]`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out BrokeredMessage`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 1. x)
* `out Message`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 2. x en hoger)
* `ICollector<T>` of `IAsyncCollector<T>`-voor het maken van meerdere berichten. Er wordt een bericht gemaakt wanneer u de `Add` methode aanroept.

Bij het werken C# met functies:

* Voor async-functies is een retour waarde of een `IAsyncCollector` vereist in plaats van een `out`-para meter.

* Als u toegang wilt krijgen tot de sessie-ID, bindt u met een [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) type en gebruikt u de eigenschap `sessionId`.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T` kan elk type JSON-serialiseerbaar zijn. Als de waarde van parameter is null wanneer de functie wordt afgesloten, wordt het bericht in functies gemaakt met een null-object.
* `out string`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out byte[]`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out BrokeredMessage`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 1. x)
* `out Message`-als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt (voor de functies 2. x en hoger)
* `ICollector<T>` of `IAsyncCollector<T>`-voor het maken van meerdere berichten. Er wordt een bericht gemaakt wanneer u de `Add` methode aanroept.

Bij het werken C# met functies:

* Voor async-functies is een retour waarde of een `IAsyncCollector` vereist in plaats van een `out`-para meter.

* Als u toegang wilt krijgen tot de sessie-ID, bindt u met een [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) type en gebruikt u de eigenschap `sessionId`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Toegang tot de wachtrij of het onderwerp met behulp van `context.bindings.<name from function.json>`. U kunt een teken reeks, een byte matrix of een Java Script-object (gedeserialiseerd in JSON) toewijzen aan `context.binding.<name>`.

# <a name="python"></a>[Python](#tab/python)

Gebruik de [Azure service bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoer binding.

# <a name="java"></a>[Java](#tab/java)

Gebruik de [Azure service bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoer binding.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| Service Bus | [Service Bus fout codes](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus limieten](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor host.JSON

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. Het onderstaande voor beeld van host. JSON-bestand bevat alleen de instellingen voor deze binding. Zie voor meer informatie over globale configuratie-instellingen [host. json Reference voor Azure functions versie](functions-host-json.md).

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
|automatisch aanvullen|true|Hiermee wordt aangegeven of de trigger het bericht onmiddellijk moet markeren als voltooid (automatisch aanvullen) of wacht totdat de functie is afgesloten.|
|maxConcurrentCalls|16|Het maximale aantal gelijktijdige oproepen aan de callback die de bericht-pomp moet starten. Standaard verwerkt de Functions-runtime meerdere berichten tegelijkertijd. Stel `maxConcurrentCalls` in op 1 om de runtime te vragen om slechts één wachtrij of onderwerp bericht tegelijk te verwerken. |

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Service Bus wachtrij of onderwerp bericht wordt gemaakt (trigger)](./functions-bindings-service-bus-trigger.md)
