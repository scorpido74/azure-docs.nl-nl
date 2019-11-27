---
title: Twilio-binding Azure Functions
description: Meer informatie over het gebruik van Twilio-bindingen met Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee78234b43e94a5c537161556410ee6246e625ca
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230944"
---
# <a name="twilio-binding-for-azure-functions"></a>Twilio-binding voor Azure Functions

In dit artikel wordt uitgelegd hoe u tekst berichten verzendt met behulp van [Twilio](https://www.twilio.com/) -bindingen in azure functions. Azure Functions ondersteunt uitvoer bindingen voor Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De Twilio-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-pakket, versie 1. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De Twilio-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-pakket, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Voor beeld-functions 1. x

Zie het voorbeeld taalspecifieke:

* [C#](#c-example)
* [C#script (. CSX)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#Hierbij

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee een tekst bericht wordt verzonden wanneer het wordt geactiveerd door een wachtrij bericht.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

In dit voor beeld wordt het kenmerk `TwilioSms` gebruikt met de retour waarde van de methode. U kunt het kenmerk ook gebruiken met een `out SMSMessage`-para meter of een `ICollector<SMSMessage>`-of `IAsyncCollector<SMSMessage>`-para meter.

### <a name="c-script-example"></a>C#script voorbeeld

In het volgende voor beeld ziet u een Twilio-uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie maakt gebruik van een `out` para meter voor het verzenden van een tekst bericht.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

Voor beeld van function. json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Dit is C# de script code:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

U kunt geen out-para meters gebruiken in asynchrone code. Hier volgt een voor C# beeld van een asynchroon script code:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Java script-voor beeld

In het volgende voor beeld ziet u een Twilio-uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

Voor beeld van function. json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>Voor beeld-functions 2. x

Zie het voorbeeld taalspecifieke:

* [2.xC#](#2x-c-example)
* [2. x C# -script (. CSX)](#2x-c-script-example)
* [2. x java script](#2x-javascript-example)

### <a name="2x-c-example"></a>2. x C# -voor beeld

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee een tekst bericht wordt verzonden wanneer het wordt geactiveerd door een wachtrij bericht.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

In dit voor beeld wordt het kenmerk `TwilioSms` gebruikt met de retour waarde van de methode. U kunt het kenmerk ook gebruiken met een `out CreateMessageOptions`-para meter of een `ICollector<CreateMessageOptions>`-of `IAsyncCollector<CreateMessageOptions>`-para meter.

### <a name="2x-c-script-example"></a>2. x C# script-voor beeld

In het volgende voor beeld ziet u een Twilio-uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie maakt gebruik van een `out` para meter voor het verzenden van een tekst bericht.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

Voor beeld van function. json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Dit is C# de script code:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

U kunt geen out-para meters gebruiken in asynchrone code. Hier volgt een voor C# beeld van een asynchroon script code:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>2. x java script-voor beeld

In het volgende voor beeld ziet u een Twilio-uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

Voor beeld van function. json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) .

Zie [configuratie](#configuration)voor informatie over kenmerk eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `TwilioSms` kenmerk in een methode handtekening:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Zie voor een volledig [ C# voor beeld.](#c-example)

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `TwilioSms`.

| v1 function. json-eigenschap | v2 function. json-eigenschap | De kenmerkeigenschap |Beschrijving|
|---------|---------|---------|----------------------|
|**type**|**type**| Moet worden ingesteld op `twilioSms`.|
|**direction**|**direction**| Moet worden ingesteld op `out`.|
|**naam**|**naam**| De naam van de variabele die wordt gebruikt in de functie code voor het SMS-tekst bericht van Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Deze waarde moet worden ingesteld op de naam van een app-instelling die uw Twilio-account-sid bevat, bijvoorbeeld TwilioAccountSid. Als deze niet is ingesteld, is de standaard naam voor de app-instelling ' AzureWebJobsTwilioAccountSid '. |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Deze waarde moet worden ingesteld op de naam van een app-instelling die uw Twilio-verificatie token bevat, bijvoorbeeld TwilioAccountAuthToken. Als deze niet is ingesteld, is de standaard naam voor de app-instelling ' AzureWebJobsTwilioAuthToken '. |
|**Aan**| N.v.t.-opgeven in code | **Aan**| Deze waarde wordt ingesteld op het telefoon nummer waarnaar de SMS-tekst wordt verzonden.|
|**Van**|**Van** | **Van**| Deze waarde wordt ingesteld op het telefoon nummer van waaruit de SMS-tekst wordt verzonden.|
|**organen**|**organen** | **Hoofdtekst**| Deze waarde kan worden gebruikt om het SMS-tekst bericht vast te maken als u het niet dynamisch hoeft in te stellen in de code voor uw functie. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
