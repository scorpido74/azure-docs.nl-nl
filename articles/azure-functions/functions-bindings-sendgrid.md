---
title: Azure Functions SendGrid-bindingen
description: Azure Functions SendGrid-bindings verwijzing.
author: craigshoemaker
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 997c9427883e2a099c2c185b618701fb85cb96a6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231085"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid-bindingen

In dit artikel wordt uitgelegd hoe u e-mail verzendt met behulp van [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) -bindingen in azure functions. Azure Functions ondersteunt een uitvoer binding voor SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De SendGrid-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-pakket, versie 2. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De SendGrid-bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-pakket, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) .

> [!NOTE]
> Versie 2. x maakt geen onderwerp of abonnement dat is geconfigureerd in het `ServiceBusTrigger`-exemplaar. Versie 2. x is gebaseerd op [micro soft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) en verwerkt geen wachtrij beheer.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#c-example)
* [C#script (. CSX)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>C#Hierbij

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die gebruikmaakt van een service bus wachtrij trigger en een SendGrid-uitvoer binding.

#### <a name="synchronous-c-example"></a>Synchroon C# voor beeld:

```cs
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
#### <a name="asynchronous-c-example"></a>Asynchroon C# voor beeld:

```cs
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

U kunt de instelling van de eigenschap `ApiKey` van het kenmerk weglaten als u uw API-sleutel in een app-instelling met de naam ' AzureWebJobsSendGridApiKey ' hebt.

### <a name="c-script-example"></a>C#script voorbeeld

In het volgende voor beeld ziet u een SendGrid-uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

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

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

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

### <a name="java-example"></a>Java-voor beeld

In het volgende voor beeld wordt de `@SendGridOutput` annotatie van de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime) gebruikt voor het verzenden van een e-mail bericht met de SendGrid-uitvoer binding.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>Java script-voor beeld

In het volgende voor beeld ziet u een SendGrid-uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

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

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

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

## <a name="attributes"></a>Kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) .

Zie [configuratie](#configuration)voor informatie over kenmerk eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `SendGrid` kenmerk in een methode handtekening:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Zie voor een volledig [ C# voor beeld.](#c-example)

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `SendGrid`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**|| Vereist: moet worden ingesteld op `sendGrid`.|
|**direction**|| Vereist: moet worden ingesteld op `out`.|
|**naam**|| Vereist: de naam van de variabele die wordt gebruikt in de functie code voor de aanvraag of aanvraag tekst. Deze waarde is ```$return``` als er slechts één retour waarde is. |
|**apiKey**|**ApiKey**| De naam van een app-instelling die uw API-sleutel bevat. Als deze niet is ingesteld, is de standaard naam voor de app-instelling ' AzureWebJobsSendGridApiKey '.|
|**Aan**|**Aan**| het e-mail adres van de ontvanger. |
|**Van**|**Van**| het e-mail adres van de afzender. |
|**Onderwerp**|**Onderwerp**| het onderwerp van het e-mail bericht. |
|**SMS**|**Tekst**| de inhoud van het e-mail bericht. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor host.JSON

In deze sectie beschrijft de globale configuratie-instellingen beschikbaar voor deze binding in versie 2.x. Het onderstaande voorbeeld host.json bestand bevat alleen de versie 2.x-instellingen voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x [host. json Reference voor Azure functions versie 2. x](functions-host-json.md).

> [!NOTE]
> Zie [host. json Reference voor Azure functions 1. x](functions-host-json-v1.md)voor een verwijzing naar de host. json in functions 1. x.

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
|from|n.v.t.|Het e-mail adres van de afzender over alle functies.| 


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
