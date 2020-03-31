---
title: Twilio-binding met Azure-functies
description: Meer informatie over het gebruik van Twilio-bindingen met Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715055"
---
# <a name="twilio-binding-for-azure-functions"></a>Twilio-binding voor Azure-functies

In dit artikel wordt uitgelegd hoe u tekstberichten verzendt met [Twilio-bindingen](https://www.twilio.com/) in Azure-functies. Azure Functions ondersteunt uitvoerbindingen voor Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - Functies 1.x

De Twilio-bindingen worden geleverd in het [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-pakket, versie 1.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten - Functies 2.x en hoger

De Twilio-bindingen worden geleverd in het [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet-pakket, versie 3.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Voorbeeld - Functies 2.x en hoger

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een sms-bericht verzendt wanneer het wordt geactiveerd door een wachtrijbericht.

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

In dit `TwilioSms` voorbeeld wordt het kenmerk met de retourwaarde van de methode gebruikt. Een alternatief is het gebruik `out CreateMessageOptions` van `ICollector<CreateMessageOptions>` het `IAsyncCollector<CreateMessageOptions>` kenmerk met een parameter of een parameter.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een Twilio-uitvoerbinding in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie `out` gebruikt een parameter om een sms-bericht te verzenden.

Hier vindt u bindende gegevens in het *bestand function.json:*

Voorbeeld functie.json:

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

Hier is C# scriptcode:

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

U geen parameters gebruiken in asynchrone code. Hier is een asynchrone C# scriptcode voorbeeld:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een Twilio-uitvoerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt.

Hier vindt u bindende gegevens in het *bestand function.json:*

Voorbeeld functie.json:

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

Hier is de JavaScript-code:

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

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld ziet u hoe u een sms-bericht verzendt met de uitvoerbinding zoals gedefinieerd in de volgende *functie.js*.

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

U een geserialiseerd JSON-object doorgeven aan de `func.Out` parameter om het sms-bericht te verzenden.

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

# <a name="java"></a>[Java](#tab/java)

In het volgende voorbeeld ziet u hoe u de [twilioSmsOutput-annotatie](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) gebruiken om een sms-bericht te verzenden. Waarden `to`voor `from`, `body` en zijn vereist in de kenmerkdefinitie, zelfs als u ze programmatisch overschrijft.

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

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [TwilioSms.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs)

Zie [Configuratie](#configuration)voor informatie over kenmerkeigenschappen die u configureren. Hier is `TwilioSms` een kenmerkvoorbeeld in een methodehandtekening:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Zie [Voorbeeld C#](#example)voor een volledig voorbeeld .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Plaats [TwilioSmsOutput-annotatie](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) op [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) een parameter waarbij `T` een `int` `String`native `byte[]`Java-type kan zijn, zoals , , of een POJO-type.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `TwilioSms` die u instelt in het *function.json-bestand* en het kenmerk.

| v1 function.json, eigenschap | v2 function.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|---------|----------------------|
|**Type**|**Type**| moeten worden `twilioSms`ingesteld op .|
|**direction**|**direction**| moeten worden `out`ingesteld op .|
|**Naam**|**Naam**| Variabele naam gebruikt in functiecode voor het Twilio SMS-bericht. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Deze waarde moet worden ingesteld op de naam van een app-instelling die uw Twilio Account Sid bevat (`TwilioAccountSid`). Als dit niet is ingesteld, is de standaardnaam voor de instelling van de app "AzureWebJobsTwilioAccountSid". |
|**AuthToken**|**authTokenInstelling**|**AuthTokenSetting**| Deze waarde moet worden ingesteld op de naam van een app-instelling die uw Twilio-verificatietoken bevat (`TwilioAccountAuthToken`). Als dit niet is ingesteld, is de standaardnaam voor app-instellingen "AzureWebJobsTwilioAuthToken". |
|**Aan**| N/A - opgeven in code | **Aan**| Deze waarde is ingesteld op het telefoonnummer waarnaar de sms-tekst wordt verzonden.|
|**Van**|**Van** | **Van**| Deze waarde is ingesteld op het telefoonnummer waarvan de sms-tekst wordt verzonden.|
|**Lichaam**|**Lichaam** | **Hoofdtekst**| Deze waarde kan worden gebruikt om het sms-bericht hard te coderen als u het niet dynamisch hoeft in te stellen in de code voor uw functie. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)
