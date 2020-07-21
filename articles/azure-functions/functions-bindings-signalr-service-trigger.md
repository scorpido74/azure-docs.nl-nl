---
title: Binding van Azure Functions signalerings service activeren
description: Meer informatie over het verzenden van signaal service berichten van Azure Functions.
author: chenyl
ms.topic: reference
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: ec2952a3093661f0f6ef32908307a8a82c6367ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540227"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Binding van de signaal service-trigger voor Azure Functions

Gebruik de binding van de *signaal* schakelaar om te reageren op berichten die zijn verzonden vanuit de Azure signalerings service. Wanneer de functie wordt geactiveerd, worden berichten die zijn door gegeven aan de functie, geparseerd als een JSON-object.

Zie het [overzicht](functions-bindings-signalr-service.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u een functie waarmee een bericht wordt ontvangen met behulp van de trigger binding en het bericht wordt geregistreerd.

# <a name="c"></a>[C#](#tab/csharp)

Trigger voor de Signa lering-service activeren voor C# heeft twee programmeer modellen. Model op basis van klasse en traditioneel model. Model op basis van een klasse kan een consistente programmerings ervaring voor de server zijde bieden. Het traditionele model biedt meer flexibiliteit en is vergelijkbaar met andere functie bindingen.

### <a name="with-class-based-model"></a>Met model op basis van klasse

Zie [model op basis van klasse](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) voor meer informatie.

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Met traditioneel model

Traditioneel model voldoet aan de Conventie van de Azure-functie die is ontwikkeld door C#. Als u er niet bekend mee bent, kunt u leren van [documenten](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Kenmerk gebruiken `[SignalRParameter]` om te vereenvoudigen`ParameterNames`

Omdat het lastig is om te gebruiken `ParameterNames` , `SignalRParameter` wordt het gebruik van hetzelfde doel gerealiseerd.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Dit is de C#-script code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Dit is de Java script-code:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Hier vindt u een bindings gegevens in de *function.jsvoor* het volgende bestand:

Voor beeld function.jsop:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Dit is de python-code:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Configuration

### <a name="signalrtrigger"></a>SignalRTrigger

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `SignalRTrigger` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type**| N.v.t. | Moet worden ingesteld op `SignalRTrigger` .|
|**direction**| N.v.t. | Moet worden ingesteld op `in` .|
|**naam**| N.v.t. | De naam van de variabele die wordt gebruikt in de functie code voor het aanroep context object voor de trigger. |
|**hubName**|**HubName**| Deze waarde moet worden ingesteld op de naam van de seingevings hub voor de functie die moet worden geactiveerd.|
|**category**|**Categorie**| Deze waarde moet worden ingesteld als de berichten categorie voor de functie die moet worden geactiveerd. De categorie kan een van de volgende waarden hebben: <ul><li>**verbindingen**: inclusief *verbonden* en *verbroken* gebeurtenissen</li><li>**berichten**: inclusief alle andere gebeurtenissen behalve die in de categorie *verbindingen*</li></ul> |
|**gebeurtenislog**|**Gebeurtenis**| Deze waarde moet worden ingesteld als gebeurtenis van berichten voor de functie die moeten worden geactiveerd. Voor *bericht* categorie is gebeurtenis het *doel* in het [aanroep bericht](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) dat clients verzenden. Voor de categorie *verbindingen* wordt alleen *verbinding* gemaakt met en de *verbinding is verbroken* . |
|**parameterNames**|**ParameterNames**| Beschrijving Een lijst met namen die zijn gekoppeld aan de para meters. |
|**connectionStringSetting**|**ConnectionStringSetting**| De naam van de app-instelling die de seingevings service connection string bevat (standaard ingesteld op ' AzureSignalRConnectionString ') |

## <a name="payload"></a>Nettolading

Het invoer type van de trigger wordt gedeclareerd als ofwel `InvocationContext` een aangepast type. Als u ervoor kiest `InvocationContext` om volledige toegang tot de aanvraag inhoud te krijgen. Voor een aangepast type probeert de runtime de JSON-aanvraag tekst te parseren om de object eigenschappen in te stellen.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext bevat alle inhoud in het bericht verzenden van de signalerings service.

|Eigenschap in InvocationContext | Beschrijving|
|------------------------------|------------|
|Argumenten| Beschikbaar voor *berichten* categorie. Bevat *argumenten* in het [aanroep bericht](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Fout| Beschikbaar voor gebeurtenis waarbij de *verbinding is verbroken* . Het kan leeg zijn als de verbinding is gesloten zonder fout of de fout berichten bevat.|
|Hub| De naam van de hub waarvan het bericht deel uitmaakt.|
|Categorie| De categorie van het bericht.|
|Gebeurtenis| De gebeurtenis van het bericht.|
|ConnectionId| De verbindings-ID van de client die het bericht verzendt.|
|UserId| De gebruikers-id van de client die het bericht verzendt.|
|Kopteksten| De headers van de aanvraag.|
|Query’s uitvoeren| De query van de aanvraag wanneer clients verbinding maken met de service.|
|Claims| De claims van de client.|

## <a name="using-parameternames"></a>`ParameterNames` gebruiken

Met de eigenschap `ParameterNames` in `SignalRTrigger` kunt u argumenten van aanroep berichten binden aan de para meters van functions. Hiermee krijgt u een handige manier om toegang te krijgen tot argumenten van `InvocationContext` .

Stel dat u een Java script-signaal-client probeert te invoke methode `broadcast` in een Azure-functie met twee argumenten.

```javascript
await connection.invoke("broadcast", message1, message2);
```

U hebt toegang tot deze twee argumenten vanuit para meter en u kunt ook het type para meter voor hen toewijzen met behulp van `ParameterNames` .

### <a name="remarks"></a>Opmerkingen

Voor de para meter binding is de volg orde van belang. Als u gebruikt `ParameterNames` , wordt de volg orde in `ParameterNames` overeenkomen met de volg orde van de argumenten die u aanroept in de client. Als u kenmerk `[SignalRParameter]` in C# gebruikt, komt de volg orde van de argumenten in functie methoden van Azure overeen met de volg orde van de argumenten in clients.

`ParameterNames`en het kenmerk `[SignalRParameter]` **kan niet** tegelijkertijd worden gebruikt, of u krijgt een uitzonde ring.

## <a name="send-messages-to-signalr-service-trigger-binding"></a>Berichten verzenden naar de trigger binding van de signaal service

De Azure-functie genereert een URL voor de binding van de signaal service-trigger en wordt als volgt opgemaakt:

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

De `API_KEY` wordt gegenereerd door de Azure-functie. U kunt de `API_KEY` van Azure Portal ophalen, omdat u de trigger voor signaal service-Triggers gebruikt.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API-sleutel":::

U moet deze URL instellen in `UrlTemplate` de upstream-instellingen van de signalerings service.

## <a name="next-steps"></a>Volgende stappen

* [Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Bindings voorbeeld van signaal service trigger](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)
