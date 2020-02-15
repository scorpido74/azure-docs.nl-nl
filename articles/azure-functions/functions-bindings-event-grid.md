---
title: Azure Event Grid bindingen voor Azure Functions
description: Meer informatie over het afhandelen van Event Grid gebeurtenissen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/03/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: df851a79ef3fbb7473e100619f58b7f35bce1d45
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212014"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u [Event grid](../event-grid/overview.md) gebeurtenissen in azure functions kunt verwerken. Lees [gebeurtenissen ontvangen naar een HTTP-eind](../event-grid/receive-events.md)punt voor meer informatie over het afhandelen van Event grid berichten in een http-eind punt.

Event Grid is een Azure-service die HTTP-aanvragen verzendt om u op de hoogte te stellen van gebeurtenissen die zich in *uitgevers*voordoen. Een uitgever is de service of resource die van de gebeurtenis afkomstig is. Een Azure Blob-opslag account is bijvoorbeeld een uitgever en [een BLOB uploaden of verwijderen is een gebeurtenis](../storage/blobs/storage-blob-event-overview.md). Sommige [Azure-Services hebben ingebouwde ondersteuning voor het publiceren van gebeurtenissen naar Event grid](../event-grid/overview.md#event-sources).

Gebeurtenis- *handlers* ontvangen en verwerken gebeurtenissen. Azure Functions is een van [de verschillende Azure-Services met ingebouwde ondersteuning voor het verwerken van Event grid-gebeurtenissen](../event-grid/overview.md#event-handlers). In dit artikel leert u hoe u een Event Grid trigger kunt gebruiken om een functie aan te roepen wanneer een gebeurtenis wordt ontvangen van Event Grid en om de uitvoer binding te gebruiken voor het verzenden van gebeurtenissen naar een [Event grid aangepast onderwerp](../event-grid/post-to-custom-topic.md).

Als u wilt, kunt u een HTTP-trigger gebruiken om Event Grid gebeurtenissen te verwerken. Zie [gebeurtenissen ontvangen naar een HTTP-eind punt](../event-grid/receive-events.md). Op dit moment kunt u geen Event Grid trigger gebruiken voor een Azure Functions-app wanneer de gebeurtenis wordt bezorgd in het [CloudEvents-schema](../event-grid/cloudevents-schema.md#azure-functions). Gebruik in plaats daarvan een HTTP-trigger.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De Event Grid-bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet, versie 2. x. De bron code voor het pakket bevindt zich in de [Azure-functions-eventgrid-extension github-](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) opslag plaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De trigger Event Grid is opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet, versie 1. x. De bron code voor het pakket bevindt zich in de [Azure-functions-eventgrid-extension github-](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) opslag plaats.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Trigger

Gebruik de functie trigger om te reageren op een gebeurtenis die wordt verzonden naar een Event Grid onderwerp.

## <a name="trigger---example"></a>Trigger - voorbeeld

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Zie [gebeurtenissen op een HTTP-eind punt ontvangen](../event-grid/receive-events.md)voor een voor beeld van een http-trigger.

### <a name="c-2x-and-higher"></a>C#(2. x en hoger)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die wordt gekoppeld aan `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Zie pakketten, [kenmerken](#trigger---attributes), [configuratie](#trigger---configuration)en [gebruik](#trigger---usage)voor meer informatie.

### <a name="version-1x"></a>Versie 1. x

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) van de functies 1. x die aan `JObject`is gekoppeld:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-script-version-2x-and-higher"></a>C#script (versie 2. x en hoger)

Hier volgt een voor beeld dat is gekoppeld aan `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Zie pakketten, [kenmerken](#trigger---attributes), [configuratie](#trigger---configuration)en [gebruik](#trigger---usage)voor meer informatie.

### <a name="version-1x"></a>Versie 1. x

Dit is de functie 1. C# x-script code die is gekoppeld aan `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Dit is de python-code:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [Event Grid-trigger, teken reeks parameter](#event-grid-trigger-string-parameter)
* [Event Grid trigger, para meter POJO](#event-grid-trigger-pojo-parameter)

In de volgende voor beelden ziet u de trigger binding in [Java](functions-reference-java.md) die gebruikmaakt van het binden en afdrukken van een gebeurtenis, waarbij u eerst de gebeurtenis ontvangt als `String` en seconde als een POJO.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid-trigger, teken reeks parameter

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid trigger, para meter POJO

In dit voor beeld worden de volgende POJO gebruikt, die de eigenschappen van het hoogste niveau van een Event Grid gebeurtenis vertegenwoordigen:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Na aankomst wordt de JSON-payload van de gebeurtenis gedeserialiseerd in de ```EventSchema``` POJO voor gebruik door de functie. Hierdoor kan de functie de eigenschappen van de gebeurtenis op een object gerichte manier benaderen.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `EventGridTrigger` annotatie op para meters waarvan de waarde afkomstig is van EventGrid. Door para meters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis binnenkomt.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van `Optional<T>`.

---

## <a name="trigger---attributes"></a>Trigger - kenmerken

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) .

Hier volgt een `EventGridTrigger` kenmerk in een hand tekening van de methode:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Zie C# voor een volledig voor beeld.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="javatabjava"></a>[Java](#tab/java)

Met de aantekening [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) kunt u een event grid binding declaratief configureren door configuratie waarden op te geven. Zie het voor [beeld](#trigger---example) en de [configuratie](#trigger---configuration) secties voor meer informatie.

---

## <a name="trigger---configuration"></a>Trigger - configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u in het bestand *Function. json* hebt ingesteld. Er zijn geen constructor-para meters of-eigenschappen om in te stellen in het kenmerk `EventGridTrigger`.

|de eigenschap Function.JSON |Beschrijving|
|---------|---------|
| **type** | Vereist: moet worden ingesteld op `eventGridTrigger`. |
| **direction** | Vereist: moet worden ingesteld op `in`. |
| **naam** | Vereist: de naam van de variabele die wordt gebruikt in de functie code voor de para meter waarmee de gebeurtenis gegevens worden ontvangen. |

## <a name="trigger---usage"></a>Trigger - gebruik

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In Azure Functions 1. x kunt u de volgende parameter typen gebruiken voor de Event Grid trigger:

* `JObject`
* `string`

In Azure Functions 2. x en hoger hebt u ook de mogelijkheid om het volgende parameter type te gebruiken voor de Event Grid trigger:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-definieert eigenschappen voor de overeenkomende velden voor alle gebeurtenis typen.

> [!NOTE]
> In functions v1 als u verbinding probeert te maken met `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, wordt in de compiler een ' afgeschaft ' bericht weer gegeven. u wordt aangeraden `Microsoft.Azure.EventGrid.Models.EventGridEvent` in plaats daarvan te gebruiken. Als u het nieuwe type wilt gebruiken, verwijst u naar het pakket [micro soft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet en geeft u de naam van het `EventGridEvent` volledig door met behulp van `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

In Azure Functions 1. x kunt u de volgende parameter typen gebruiken voor de Event Grid trigger:

* `JObject`
* `string`

In Azure Functions 2. x en hoger hebt u ook de mogelijkheid om het volgende parameter type te gebruiken voor de Event Grid trigger:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-definieert eigenschappen voor de overeenkomende velden voor alle gebeurtenis typen.

> [!NOTE]
> In functions v1 als u verbinding probeert te maken met `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, wordt in de compiler een ' afgeschaft ' bericht weer gegeven. u wordt aangeraden `Microsoft.Azure.EventGrid.Models.EventGridEvent` in plaats daarvan te gebruiken. Als u het nieuwe type wilt gebruiken, verwijst u naar het pakket [micro soft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet en geeft u de naam van het `EventGridEvent` volledig door met behulp van `Microsoft.Azure.EventGrid.Models`. Zie [NuGet-pakketten gebruiken](functions-reference-csharp.md#using-nuget-packages) voor meer informatie over het C# verwijzen naar NuGet-pakketten in een script functie

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Het Event Grid-exemplaar is beschikbaar via de para meter die is geconfigureerd in de eigenschap `name` van het bestand *Function. json* .

# <a name="pythontabpython"></a>[Python](#tab/python)

Het Event Grid-exemplaar is beschikbaar via de para meter die is geconfigureerd in de eigenschap `name` van het bestand *Function. json* , getypeerd als `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

De Event Grid gebeurtenis instantie is beschikbaar via de para meter die is gekoppeld aan het kenmerk `EventGridTrigger`, dat is opgegeven als een `EventSchema`. Zie het voor [beeld](#trigger---example) voor meer informatie.

---

## <a name="trigger---event-schema"></a>Trigger-gebeurtenis schema

Gegevens voor een Event Grid gebeurtenis worden ontvangen als een JSON-object in de hoofd tekst van een HTTP-aanvraag. De JSON ziet er ongeveer uit zoals in het volgende voor beeld:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Het voor beeld dat wordt weer gegeven, is een matrix van één element. Event Grid verzendt altijd een matrix en kan meer dan één gebeurtenis in de matrix verzenden. De runtime roept de functie eenmaal aan voor elk matrix element.

De eigenschappen op het hoogste niveau in de JSON-gegevens van de gebeurtenis zijn hetzelfde voor alle gebeurtenis typen, terwijl de inhoud van de eigenschap `data` specifiek is voor elk gebeurtenis type. Het voor beeld dat wordt weer gegeven, is voor een Blob Storage-gebeurtenis.

Zie [gebeurtenis eigenschappen](../event-grid/event-schema.md#event-properties) in de Event grid-documentatie voor uitleg van de algemene en gebeurtenis-specifieke eigenschappen.

Het `EventGridEvent` type definieert alleen de eigenschappen op het hoogste niveau; de eigenschap `Data` is een `JObject`.

## <a name="trigger---create-a-subscription"></a>Trigger-een abonnement maken

Als u Event Grid HTTP-aanvragen wilt ontvangen, moet u een Event Grid-abonnement maken dat de eind punt-URL opgeeft waarmee de functie wordt aangeroepen.

### <a name="azure-portal"></a>Azure-portal

Selecteer **Event grid abonnement toevoegen**voor functies die u in de Azure Portal met de Event grid trigger ontwikkelt.

![Een abonnement maken in de portal](media/functions-bindings-event-grid/portal-sub-create.png)

Wanneer u deze koppeling selecteert, wordt de pagina **gebeurtenis abonnement maken** geopend met de vooraf gevulde eind punt-URL.

![Eind punt-URL vooraf ingevuld](media/functions-bindings-event-grid/endpoint-url.png)

Zie voor meer informatie over het maken van abonnementen met behulp van de Azure Portal [aangepaste gebeurtenis maken-Azure Portal](../event-grid/custom-event-quickstart-portal.md) in de Event grid documentatie.

### <a name="azure-cli"></a>Azure CLI

Als u een abonnement wilt maken met behulp van [de Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), gebruikt u de opdracht [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

De opdracht vereist de eind punt-URL die de functie aanroept. In het volgende voor beeld wordt het versie-specifieke URL-patroon weer gegeven:

#### <a name="version-2x-and-higher-runtime"></a>Versie 2. x (en hoger) runtime

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Versie 1. x runtime

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

De systeem sleutel is een autorisatie sleutel die moet worden opgenomen in de eind punt-URL voor een Event Grid trigger. In de volgende sectie wordt uitgelegd hoe u de systeem sleutel kunt ophalen.

Hier volgt een voor beeld waarin wordt geabonneerd op een Blob Storage-account (met een tijdelijke aanduiding voor de systeem sleutel):

#### <a name="version-2x-and-higher-runtime"></a>Versie 2. x (en hoger) runtime

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Versie 1. x runtime

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Zie voor meer informatie over het maken van een abonnement [de Snelstartgids voor Blob-opslag](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) of de andere Event grid Quick starts.

### <a name="get-the-system-key"></a>De systeem sleutel ophalen

U kunt de systeem sleutel ophalen met behulp van de volgende API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Versie 2. x (en hoger) runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Versie 1. x runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Dit is een beheer-API, zodat hiervoor de [hoofd sleutel](functions-bindings-http-webhook-trigger.md#authorization-keys)van de functie-app is vereist. Verwar de systeem sleutel (voor het aanroepen van een Event Grid geactiveerde functie) niet met de hoofd sleutel (voor het uitvoeren van beheer taken voor de functie-app). Wanneer u zich abonneert op een Event Grid onderwerp, moet u ervoor zorgen dat u de systeem sleutel gebruikt.

Hier volgt een voor beeld van het antwoord dat de systeem sleutel levert:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

U kunt de hoofd sleutel voor uw functie-app ophalen via het tabblad **functie-app-instellingen** in de portal.

> [!IMPORTANT]
> De hoofd sleutel biedt beheerders toegang tot uw functie-app. Deel deze sleutel niet met derden of distribueer deze niet in native client toepassingen.

Zie [autorisatie sleutels](functions-bindings-http-webhook-trigger.md#authorization-keys) in het naslag artikel over http-triggers voor meer informatie.

U kunt ook een HTTP-PUT verzenden om de sleutel waarde zelf op te geven.

## <a name="trigger---local-testing-with-viewer-web-app"></a>Trigger-Local testen met Viewer web app

Als u een Event Grid trigger lokaal wilt testen, moet u Event Grid HTTP-aanvragen ontvangen van hun oorsprong in de Cloud naar uw lokale computer. Een manier om dat te doen, is door aanvragen online te vastleggen en ze hand matig opnieuw te verzenden op uw lokale computer:

1. [Maak een web-app Viewer](#create-a-viewer-web-app) waarmee gebeurtenis berichten worden vastgelegd.
1. [Maak een event grid-abonnement](#create-an-event-grid-subscription) dat gebeurtenissen naar de viewer-app verzendt.
1. [Genereer een aanvraag](#generate-a-request) en kopieer de aanvraag tekst uit de viewer-app.
1. Plaats [de aanvraag hand matig](#manually-post-the-request) naar de LOCALHOST-URL van uw event grid-activerings functie.

Wanneer u klaar bent met testen, kunt u hetzelfde abonnement voor productie gebruiken door het eind punt bij te werken. Gebruik de opdracht [AZ eventgrid Event-Subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure cli.

### <a name="create-a-viewer-web-app"></a>Een web-app voor Viewer maken

Om het vastleggen van gebeurtenis berichten te vereenvoudigen, kunt u een [vooraf gemaakte web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarin de gebeurtenis berichten worden weer gegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

![Nieuwe site weergeven](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

Maak een Event Grid-abonnement van het type dat u wilt testen en geef dit de URL van uw web-app als eind punt voor gebeurtenis meldingen. Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten. De volledige URL is dus `https://<your-site-name>.azurewebsites.net/api/updates`

Voor informatie over het maken van abonnementen met behulp van de Azure Portal, Zie [aangepaste gebeurtenis maken-Azure Portal](../event-grid/custom-event-quickstart-portal.md) in de Event grid documentatie.

### <a name="generate-a-request"></a>Een aanvraag genereren

Hiermee wordt een gebeurtenis geactiveerd waarmee HTTP-verkeer wordt gegenereerd naar het eind punt van uw web-app.  Als u bijvoorbeeld een Blob Storage-abonnement hebt gemaakt, uploadt of verwijdert u een blob. Wanneer een aanvraag in uw web-app wordt weer gegeven, kopieert u de hoofd tekst van de aanvraag.

De aanvraag voor abonnements validatie wordt als eerste ontvangen. Negeer validatie aanvragen en kopieer de gebeurtenis aanvraag.

![Hoofd tekst van de aanvraag kopiëren van de web-app](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>De aanvraag hand matig plaatsen

Voer uw Event Grid-functie lokaal uit.

Gebruik een hulp programma zoals [postman](https://www.getpostman.com/) of [krul](https://curl.haxx.se/docs/httpscripting.html) om een HTTP POST-aanvraag te maken:

* Stel een `Content-Type: application/json` koptekst in.
* Stel een `aeg-event-type: Notification`-header in.
* Plak de RequestBin-gegevens in de hoofd tekst van de aanvraag.
* Post naar de URL van uw Event Grid-trigger functie.
  * Voor 2. x en hoger wordt het volgende patroon gebruikt:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Voor 1. x gebruik:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

De para meter `functionName` moet de naam zijn die is opgegeven in het kenmerk `FunctionName`.

In de volgende scherm afbeeldingen worden de kopteksten en de hoofd tekst van de aanvraag weer gegeven in postman:

![Koppen in postman](media/functions-bindings-event-grid/postman2.png)

![Aanvraag tekst in postman](media/functions-bindings-event-grid/postman.png)

De functie Trigger Event Grid wordt uitgevoerd en logboeken worden weer gegeven die vergelijkbaar zijn met het volgende voor beeld:

![Voorbeeld functie Logboeken Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="output"></a>Uitvoer

Gebruik de Event Grid uitvoer binding om gebeurtenissen te schrijven naar een aangepast onderwerp. U moet een geldige [toegangs sleutel voor het aangepaste onderwerp](../event-grid/security-authentication.md#custom-topic-publishing)hebben.

> [!NOTE]
> De Event Grid uitvoer binding biedt geen ondersteuning voor hand tekeningen voor gedeelde toegang (SAS-tokens). U moet de toegangs sleutel van het onderwerp gebruiken.

Zorg ervoor dat de vereiste pakket verwijzingen aanwezig zijn voordat u een uitvoer binding implementeert.

> [!IMPORTANT]
> De Event Grid uitvoer binding is alleen beschikbaar voor functions 2. x en hoger.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die een bericht naar een event grid aangepast onderwerp schrijft, met behulp van de retour waarde van de methode als uitvoer:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

In het volgende voor beeld ziet u hoe u de `IAsyncCollector`-interface gebruikt om een batch berichten te verzenden.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u de Event Grid uitvoer bindings gegevens in het bestand *Function. json* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Dit is C# de script code voor het maken van één gebeurtenis:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Dit is C# de script code waarmee meerdere gebeurtenissen worden gemaakt:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u de Event Grid uitvoer bindings gegevens in het bestand *Function. json* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Dit is de Java script-code waarmee één gebeurtenis wordt gemaakt:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Dit is de Java script-code waarmee meerdere gebeurtenissen worden gemaakt:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

# <a name="javatabjava"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

---

## <a name="output---attributes-and-annotations"></a>Uitvoer-kenmerken en aantekeningen

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Gebruik voor [ C# class-bibliotheken](functions-dotnet-class-library.md)het kenmerk [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

De constructor van het kenmerk maakt de naam van een app-instelling die de naam van het aangepaste onderwerp bevat en de naam van een app-instelling die de sleutel van het onderwerp bevat. Zie [output-Configuration (](#output---configuration)Engelstalig) voor meer informatie over deze instellingen. Hier volgt een voor beeld van een `EventGrid`-kenmerk:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer- C# voor beeld](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="pythontabpython"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

# <a name="javatabjava"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

---

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `EventGrid`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' eventGrid '. |
|**direction** | N.v.t. | Moet worden ingesteld op 'out'. Deze para meter wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code waarmee de gebeurtenis wordt aangeduid. |
|**topicEndpointUri** |**TopicEndpointUri** | De naam van een app-instelling die de URI voor het aangepaste onderwerp bevat, zoals `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | De naam van een app-instelling die een toegangs sleutel voor het aangepaste onderwerp bevat. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Zorg ervoor dat u de waarde van de eigenschap `TopicEndpointUri` Configuration instelt op de naam van een app-instelling die de URI van het aangepaste onderwerp bevat. Geef de URI van het aangepaste onderwerp niet rechtstreeks op in deze eigenschap.

## <a name="output---usage"></a>Uitvoer - gebruik

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Berichten verzenden met behulp van een methode parameter, zoals `out EventGridEvent paramName`. Als u meerdere berichten wilt schrijven, kunt u `ICollector<EventGridEvent>` of `IAsyncCollector<EventGridEvent>` gebruiken in plaats van `out EventGridEvent`.

# <a name="c-scripttabcsharp-script"></a>[C#Schriften](#tab/csharp-script)

Berichten verzenden met behulp van een methode parameter, zoals `out EventGridEvent paramName`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. Als u meerdere berichten wilt schrijven, kunt u `ICollector<EventGridEvent>` of `IAsyncCollector<EventGridEvent>` gebruiken in plaats van `out EventGridEvent`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Open de uitvoer gebeurtenis met behulp van `context.bindings.<name>` waarbij `<name>` de waarde is die is opgegeven in de eigenschap `name` van *Function. json*.

# <a name="pythontabpython"></a>[Python](#tab/python)

De Event Grid uitvoer binding is niet beschikbaar voor python.

# <a name="javatabjava"></a>[Java](#tab/java)

De Event Grid uitvoer binding is niet beschikbaar voor Java.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Meer informatie over Event Grid](../event-grid/overview.md)
