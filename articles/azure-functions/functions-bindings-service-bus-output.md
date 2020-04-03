---
title: Azure Service Bus-bindingen voor Azure-functies
description: Meer informatie over het verzenden van Azure Service Bus-berichten vanuit Azure-functies.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582261"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus-uitvoerbinding voor Azure-functies

Gebruik Azure Service Bus-uitvoerbinding om wachtrij- of onderwerpberichten te verzenden.

Zie het [overzicht](functions-bindings-service-bus-output.md)voor informatie over de installatie en configuratiedetails.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een wachtrijbericht servicebus verzendt:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een uitvoerbinding servicebus weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie maakt gebruik van een timertrigger om elke 15 seconden een wachtrijbericht te verzenden.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

Hier is C#-scriptcode die één bericht maakt:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Hier is C#-scriptcode die meerdere berichten maakt:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een servicebus-uitvoerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie maakt gebruik van een timertrigger om elke 15 seconden een wachtrijbericht te verzenden.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

Hier is JavaScript-scriptcode die één bericht maakt:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Hier is JavaScript-scriptcode die meerdere berichten maakt:

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

In het volgende voorbeeld wordt uitgelegd hoe u een wachtrij voor servicebus in Python uitschrijven.

Een bindingsdefinitie voor servicebus wordt gedefinieerd in *function.json* waar *type* is ingesteld op `serviceBus`.

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

* _ \_Ininit_\_.py*u een bericht naar de wachtrij `set` schrijven door een waarde door te geven aan de methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voorbeeld wordt een Java-functie `myqueue` weergegeven die een bericht naar een wachtrij van een servicebus stuurt wanneer deze wordt geactiveerd door een HTTP-verzoek.

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

 Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op functieparameters waarvan de `@QueueOutput` waarde naar een wachtrij voor servicebus wordt geschreven.  Het parametertype `OutputBinding<T>`moet zijn, waar T een native Java-type van een POJO is.

Java-functies kunnen ook schrijven naar een Service Bus onderwerp. In het volgende `@ServiceBusTopicOutput` voorbeeld wordt de annotatie gebruikt om de configuratie voor de uitvoerbinding te beschrijven. 

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

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het [ServiceBusAttribuut](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

De constructor van het kenmerk neemt de naam van de wachtrij of het onderwerp en abonnement. U ook de toegangsrechten van de verbinding opgeven. Hoe u de instelling voor toegangsrechten kiest, wordt uitgelegd in de sectie [Uitvoer - configuratie.](#configuration) Hier is een voorbeeld dat het kenmerk toont dat is toegepast op de retourwaarde van de functie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

U `Connection` instellen dat de eigenschap de naam opgeeft van een app-instelling die de verbindingstekenreeks ServiceBus bevat, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Zie Uitvoer - [voorbeeld](#example)voor een volledig voorbeeld .

U `ServiceBusAccount` het kenmerk gebruiken om het Service Bus-account op te geven dat u op klasse-, methode- of parameterniveau wilt gebruiken.  Zie [Trigger - kenmerken voor](functions-bindings-service-bus-trigger.md#attributes-and-annotations)meer informatie.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

De `ServiceBusQueueOutput` `ServiceBusTopicOutput` annotaties zijn beschikbaar om een bericht te schrijven als een functie-uitvoer. De parameter versierd met deze annotaties `OutputBinding<T>` `T` moet worden aangegeven als een waar is het type dat overeenkomt met het type van het bericht.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `ServiceBus` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet worden ingesteld op "serviceBus". Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op "out". Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. | De naam van de variabele die het wachtrij- of onderwerpbericht in functiecode vertegenwoordigt. Instellen op '$return' om naar de waarde voor functieretour te verwijzen. |
|**queueName**|**QueueName**|Naam van de wachtrij.  Alleen instellen als u wachtrijberichten verzendt, niet voor een onderwerp.
|**topicNaam**|**TopicName**|Naam van het onderwerp. Alleen instellen als het verzenden van onderwerpberichten, niet voor een wachtrij.|
|**verbinding**|**Verbinding**|De naam van een app-instelling die de verbindingstekenreeks ServiceBus bevat om te gebruiken voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', u alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyServiceBus', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'AzureWebJobsMyServiceBus'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de standaardverbindingstekenreeks servicebus in de app-instelling met de naam 'AzureWebJobsServiceBus'.<br><br>Als u een verbindingstekenreeks wilt verkrijgen, voert u de stappen uit die worden weergegeven bij [De beheerreferenties opvragen opvragen.](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string) De verbindingstekenreeks moet zijn voor een naamruimte van servicebus, niet beperkt tot een specifieke wachtrij of onderwerp.|
|**accessRights accessRights accessRights accessRights**|**Toegang**|Toegangsrechten voor de verbindingstekenreeks. Beschikbare waarden `manage` `listen`zijn en . De standaardinstelling is `manage`, `connection` wat aangeeft dat de machtiging **Beheren** is. Als u een verbindingstekenreeks gebruikt **Manage** die geen `accessRights` machtiging beheren heeft, stelt u in op 'luisteren'. Anders kan de runtime van functies niet worden uitgevoerd om bewerkingen uit te voeren waarvoor beheerrechten vereist zijn. In Azure Functions versie 2.x en hoger is deze eigenschap niet beschikbaar omdat de nieuwste versie van de Service Bus SDK geen beheerbewerkingen ondersteunt.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

In Azure Functions 1.x maakt de runtime de wachtrij als `accessRights` deze `manage`niet bestaat en u bent ingesteld op . In functies versie 2.x en hoger moet de wachtrij of het onderwerp al bestaan; Als u een wachtrij of onderwerp opgeeft dat niet bestaat, mislukt de functie. 

# <a name="c"></a>[C#](#tab/csharp)

Gebruik de volgende parametertypen voor de uitvoerbinding:

* `out T paramName` - `T`kan elk JSON-serializable type zijn. Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions het bericht met een null-object.
* `out string`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht.
* `out byte[]`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht.
* `out BrokeredMessage`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht (voor functies 1.x)
* `out Message`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht (voor functies 2.x en hoger)
* `ICollector<T>`of `IAsyncCollector<T>` - Voor het maken van meerdere berichten. Er wordt een bericht `Add` gemaakt wanneer u de methode aanroept.

Bij het werken met C#-functies:

* Async-functies hebben een `IAsyncCollector` retourwaarde `out` of in plaats van een parameter nodig.

* Als u toegang wilt krijgen [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) tot de `sessionId` sessie-id, bindt u aan een type en gebruikt u de eigenschap.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Gebruik de volgende parametertypen voor de uitvoerbinding:

* `out T paramName` - `T`kan elk JSON-serializable type zijn. Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions het bericht met een null-object.
* `out string`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht.
* `out byte[]`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht.
* `out BrokeredMessage`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht (voor functies 1.x)
* `out Message`- Als de parameterwaarde null is wanneer de functie wordt afgesloten, maakt Functions geen bericht (voor functies 2.x en hoger)
* `ICollector<T>`of `IAsyncCollector<T>` - Voor het maken van meerdere berichten. Er wordt een bericht `Add` gemaakt wanneer u de methode aanroept.

Bij het werken met C#-functies:

* Async-functies hebben een `IAsyncCollector` retourwaarde `out` of in plaats van een parameter nodig.

* Als u toegang wilt krijgen [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) tot de `sessionId` sessie-id, bindt u aan een type en gebruikt u de eigenschap.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot de wachtrij `context.bindings.<name from function.json>`of het onderwerp met behulp van . U een tekenreeks, een bytearray of een JavaScript-object (gedeserialiseerd in JSON) toewijzen aan `context.binding.<name>`.

# <a name="python"></a>[Python](#tab/python)

Gebruik de [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoerbinding.

# <a name="java"></a>[Java](#tab/java)

Gebruik de [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoerbinding.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| Service Bus | [Foutcodes servicebus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Beperkingen voor servicebussen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json-instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versies 2.x en hoger. Het onderstaande voorbeeldhost.json-bestand bevat alleen de instellingen voor deze binding. Zie [host.json-verwijzing naar de versie Azure-functies voor](functions-host-json.md)meer informatie over algemene configuratie-instellingen.

> [!NOTE]
> Zie [host.json-verwijzing naar Azure-functies 1.x voor](functions-host-json-v1.md)een verwijzing naar host.json in Functies 1.x .

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
|prefetchCount prefetchCount prefetchCount prefetch|0|Hiermee wordt het aantal berichten dat de ontvanger van het bericht tegelijk kan aanvragen, ontvangen of ingesteld.|
|maxAutoRenewDuur|00:05:00|De maximale duur waarbinnen het berichtslot automatisch wordt verlengd.|
|Autoaanvullen|waar|Of de trigger het bericht onmiddellijk als voltooid moet markeren (automatisch aanvullen) of moet wachten tot de functie is afgesloten om te bellen.|
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige oproepen naar de callback die de berichtpomp moet initiëren. Standaard verwerkt de runtime Functies meerdere berichten tegelijk. Als u de runtime wilt richten op het verwerken `maxConcurrentCalls` van slechts één wachtrij of onderwerpbericht tegelijk, stelt u deze in op 1. |

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een wachtrij of onderwerpbericht van een servicebus wordt gemaakt (Trigger)](./functions-bindings-service-bus-trigger.md)
