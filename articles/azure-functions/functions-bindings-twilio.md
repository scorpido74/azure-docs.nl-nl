---
title: Twilio-binding Azure Functions
description: Meer informatie over het gebruik van Twilio-bindingen met Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715055"
---
# <a name="twilio-binding-for-azure-functions"></a>Twilio-binding voor Azure Functions

In dit artikel wordt uitgelegd hoe u tekst berichten verzendt met behulp van [Twilio](https://www.twilio.com/) -bindingen in azure functions. Azure Functions ondersteunt uitvoer bindingen voor Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De Twilio-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-pakket, versie 1. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De Twilio-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-pakket, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Voor beeld-functions 2. x en hoger

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u een SMS-bericht verzendt met behulp van de uitvoer binding zoals gedefinieerd in de volgende *functie. js*.

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

U kunt een geserialiseerd JSON-object door geven aan de para meter `func.Out` om het SMS-bericht te verzenden.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="javatabjava"></a>[Java](#tab/java)

In het volgende voor beeld ziet u hoe u de [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) -aantekening gebruikt om een SMS-bericht te verzenden. Waarden voor `to`, `from`en `body` zijn vereist in de kenmerk definitie, zelfs als u ze programmatisch overschrijft.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

Zie voor een volledig [ C# voor beeld.](#example)

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="javatabjava"></a>[Java](#tab/java)

Plaats [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) aantekening op een [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) para meter waarbij `T` een systeem eigen Java-type is, zoals `int`, `String`, `byte[]`of een Pojo-type.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `TwilioSms`.

| v1 function. json-eigenschap | v2 function. json-eigenschap | De kenmerkeigenschap |Beschrijving|
|---------|---------|---------|----------------------|
|**type**|**type**| moet worden ingesteld op `twilioSms`.|
|**direction**|**direction**| moet worden ingesteld op `out`.|
|**naam**|**naam**| De naam van de variabele die wordt gebruikt in de functie code voor het SMS-tekst bericht van Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Deze waarde moet worden ingesteld op de naam van een app-instelling die uw Twilio-account-sid (`TwilioAccountSid`) bevat. Als deze niet is ingesteld, is de standaard naam voor de app-instelling ' AzureWebJobsTwilioAccountSid '. |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Deze waarde moet worden ingesteld op de naam van een app-instelling die uw Twilio-verificatie token (`TwilioAccountAuthToken`) bevat. Als deze niet is ingesteld, is de standaard naam voor de app-instelling ' AzureWebJobsTwilioAuthToken '. |
|**Aan**| N.v.t.-opgeven in code | **Aan**| Deze waarde wordt ingesteld op het telefoon nummer waarnaar de SMS-tekst wordt verzonden.|
|**Van**|**Van** | **Van**| Deze waarde wordt ingesteld op het telefoon nummer van waaruit de SMS-tekst wordt verzonden.|
|**organen**|**organen** | **Hoofdtekst**| Deze waarde kan worden gebruikt om het SMS-tekst bericht vast te maken als u het niet dynamisch hoeft in te stellen in de code voor uw functie. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
