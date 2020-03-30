---
title: SendGrid-bindingen voor Azure-functies
description: Verwijzing naar Azure Functions SendGrid-bindingen.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 9ed2b81c12c698822b9542bb6903189c865b572b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277464"
---
# <a name="azure-functions-sendgrid-bindings"></a>SendGrid-bindingen voor Azure-functies

In dit artikel wordt uitgelegd hoe u e-mail verzendt met [SendGrid-bindingen](https://sendgrid.com/docs/User_Guide/index.html) in Azure-functies. Azure Functions ondersteunt een uitvoerbinding voor SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - Functies 1.x

De SendGrid-bindingen worden geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, versie 2.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten - Functies 2.x en hoger

De SendGrid-bindingen worden geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, versie 3.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die gebruikmaakt van een wachtrijtrigger voor servicebus en een SendGrid-uitvoerbinding.

### <a name="synchronous"></a>Synchrone

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Asynchrone

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

U de `ApiKey` eigenschap van het kenmerk weglaten als u uw API-sleutel hebt in een app-instelling met de naam 'AzureWebJobsSendGridApiKey'.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een SendGrid-uitvoerbinding weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt.

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een SendGrid-uitvoerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt.

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

In de [configuratiesectie](#configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt een http-geactiveerde functie weergegeven die een e-mail verzendt met de SendGrid-binding. U standaardwaarden opgeven in de bindingsconfiguratie. Het *e-mailadres van e-mailadres* is bijvoorbeeld geconfigureerd in *function.json*. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

In de volgende functie ziet u hoe u aangepaste waarden opgeven voor optionele eigenschappen.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

In het volgende `@SendGridOutput` voorbeeld wordt de annotatie van de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime) gebruikt om een e-mail te verzenden met de sendgrid-uitvoerbinding.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [SendGrid.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs)

Zie [Configuratie](#configuration)voor informatie over kenmerkeigenschappen die u configureren. Hier is `SendGrid` een kenmerkvoorbeeld in een methodehandtekening:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
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

Met de [sendgridoutput-annotatie](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) u de SendGrid-binding declaratief configureren door configuratiewaarden op te geven. Zie het [voorbeeld](#example) en [de configuratiesecties](#configuration) voor meer details.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen `SendGrid` weergegeven die beschikbaar zijn in het *bestand function.json* en het kenmerk/annotatie.

| *functie.json,* eigenschap | Eigenschap Kenmerk/annotatie | Beschrijving | Optioneel |
|--------------------------|-------------------------------|-------------|----------|
| type |N.v.t.| Moet ingesteld `sendGrid`zijn op.| Nee |
| richting |N.v.t.| Moet ingesteld `out`zijn op.| Nee |
| name |N.v.t.| De variabele naam die wordt gebruikt in de functiecode voor de aanvraag- of aanvraaginstantie. Deze waarde `$return` is wanneer er slechts één retourwaarde is. | Nee |
| apiKey | ApiKey (ApiKey) | De naam van een app-instelling die uw API-sleutel bevat. Als dit niet is ingesteld, is de standaardnaam voor de instelling van de app *AzureWebJobsSendGridApiKey*.| Nee |
| tot| Handeling | Het e-mailadres van de ontvanger. | Ja |
| from| Van | Het e-mailadres van de afzender. |  Ja |
| Onderwerp| Onderwerp | Het onderwerp van de e-mail. | Ja |
| tekst| Tekst | De e-mailinhoud. | Ja |

Optionele eigenschappen kunnen standaardwaarden hebben die zijn gedefinieerd in de binding en programmatisch worden toegevoegd of overschreven.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json-instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versies 2.x en hoger. Het voorbeeld host.json-bestand hieronder bevat alleen de instellingen van versie 2.x+ voor deze binding. Zie [host.json-verwijzing naar Azure-functies voor](functions-host-json.md)meer informatie over algemene configuratie-instellingen in versies 2.x en verder.

> [!NOTE]
> Zie [host.json-verwijzing naar Azure-functies 1.x voor](functions-host-json-v1.md)een verwijzing naar host.json in Functies 1.x .

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|from|N.v.t.|Het e-mailadres van de afzender voor alle functies.| 


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)
