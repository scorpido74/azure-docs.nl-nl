---
title: Event Grid trigger voor Azure Functions
description: Meer informatie over het afhandelen van Event Grid gebeurtenissen in Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: d2e15c63ac6d93824aeab3f251c2860b7ea114d6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086831"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Event Grid trigger voor Azure Functions

In dit artikel wordt uitgelegd hoe u [Event grid](../event-grid/overview.md) gebeurtenissen in azure functions kunt verwerken.

Event Grid is een Azure-service die HTTP-aanvragen verzendt om u op de hoogte te stellen van gebeurtenissen die zich in *uitgevers*voordoen. Een uitgever is de service of resource die van de gebeurtenis afkomstig is. Een Azure Blob-opslag account is bijvoorbeeld een uitgever en [een BLOB uploaden of verwijderen is een gebeurtenis](../storage/blobs/storage-blob-event-overview.md). Sommige [Azure-Services hebben ingebouwde ondersteuning voor het publiceren van gebeurtenissen naar Event grid](../event-grid/overview.md#event-sources).

Gebeurtenis -handlers ontvangen en verwerken gebeurtenissen. Azure Functions is een van [de verschillende Azure-Services met ingebouwde ondersteuning voor het verwerken van Event grid-gebeurtenissen](../event-grid/overview.md#event-handlers). In dit artikel leert u hoe u een Event Grid trigger kunt gebruiken om een functie aan te roepen wanneer een gebeurtenis wordt ontvangen van Event Grid.

Als u wilt, kunt u een HTTP-trigger gebruiken om Event Grid gebeurtenissen te verwerken. Zie [een HTTP-trigger gebruiken als Event grid trigger](#use-an-http-trigger-as-an-event-grid-trigger) verderop in dit artikel. Op dit moment kunt u geen Event Grid trigger gebruiken voor een Azure Functions-app wanneer de gebeurtenis wordt bezorgd in het [CloudEvents-schema](../event-grid/cloudevents-schema.md). Gebruik in plaats daarvan een HTTP-trigger.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

De trigger Event Grid wordt gegeven in het [micro soft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-pakket, versie 2. x. De bron code voor het pakket bevindt zich in de [Azure-functions-eventgrid-extension github-](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) opslag plaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De trigger Event Grid is opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet, versie 1. x. De bron code voor het pakket bevindt zich in de [Azure-functions-eventgrid-extension github-](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) opslag plaats.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Voorbeeld

Zie het taalspecifieke voor beeld voor een Event Grid trigger:

* C#
* [C# script (.csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Zie [HTTP-trigger gebruiken](#use-an-http-trigger-as-an-event-grid-trigger) verderop in dit artikel voor een voor beeld van een http-trigger.

### <a name="c-2x"></a>C#(2. x)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) van 2. x die `EventGridEvent`is gekoppeld aan:

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

Zie pakketten, [kenmerken](#attributes), [configuratie](#configuration)en [gebruik](#usage)voor meer informatie.

### <a name="c-version-1x"></a>C#(Versie 1. x)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) van 1. x die `JObject`is gekoppeld aan:

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

### <a name="c-script-example"></a>C#script voorbeeld

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding.

Hier volgt de binding-gegevens de *function.json* bestand:

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

#### <a name="c-script-version-2x"></a>C#script (versie 2. x)

Dit is de functie 2. C# x-script code waarmee een `EventGridEvent`binding wordt gemaakt:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Zie pakketten, [kenmerken](#attributes), [configuratie](#configuration)en [gebruik](#usage)voor meer informatie.

#### <a name="c-script-version-1x"></a>C#script (versie 1. x)

Dit is de functie 1. C# x-script code waarmee een `JObject`binding wordt gemaakt:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

### <a name="javascript-example"></a>Java script-voor beeld

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding.

Hier volgt de binding-gegevens de *function.json* bestand:

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

### <a name="python-example"></a>Python-voor beeld

In het volgende voor beeld ziet u een trigger binding in een *Function. json* -bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding.

Hier volgt de binding-gegevens de *function.json* bestand:

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
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Trigger-Java-voor beelden

Deze sectie bevat de volgende voorbeelden:

* [Event Grid-trigger, teken reeks parameter](#event-grid-trigger-string-parameter-java)
* [Event Grid trigger, para meter POJO](#event-grid-trigger-pojo-parameter-java)

In de volgende voor beelden wordt trigger binding weer gegeven in een *Function. json* -bestand en [Java-functies](functions-reference-java.md) die gebruikmaken van de binding en afdrukken van een ```String``` gebeurtenis, waarbij de gebeurtenis eerst als en seconde wordt ontvangen als een POJO.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

#### <a name="event-grid-trigger-string-parameter-java"></a>Event Grid trigger, teken reeks parameter (Java)

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: " + content);      
  }
```

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Trigger voor Event Grid, POJO para meter (Java)

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

Na aankomst wordt de JSON-nettolading van de gebeurtenis in de ```EventSchema``` Pojo gedeserialiseerd voor gebruik door de functie. Hierdoor kan de functie de eigenschappen van de gebeurtenis op een object gerichte manier benaderen.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

Gebruik in de [runtime-bibliotheek van Java functions](/java/api/overview/azure/functions/runtime)de `EventGridTrigger` annotatie voor para meters waarvan de waarde afkomstig is van EventGrid. Door para meters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis binnenkomt.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable `Optional<T>`-waarden met.

## <a name="attributes"></a>Kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) .

Hier volgt een `EventGridTrigger` kenmerk in een methode handtekening:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Zie C# voor een volledig voor beeld.

## <a name="configuration"></a>Configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u in het bestand *Function. json* hebt ingesteld. Er zijn geen constructor-para meters of-eigenschappen om `EventGridTrigger` in het kenmerk in te stellen.

|de eigenschap Function.JSON |Description|
|---------|---------|
| **type** | Vereist: moet worden ingesteld op `eventGridTrigger`. |
| **direction** | Vereist: moet worden ingesteld op `in`. |
| **name** | Vereist: de naam van de variabele die wordt gebruikt in de functie code voor de para meter waarmee de gebeurtenis gegevens worden ontvangen. |

## <a name="usage"></a>Gebruik

Voor C# en F# functies in azure functions 1. x kunt u de volgende parameter typen gebruiken voor de Event grid trigger:

* `JObject`
* `string`

Voor C# en F# functies in azure functions 2. x hebt u ook de mogelijkheid om het volgende parameter type te gebruiken voor de Event grid trigger:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Definieert eigenschappen voor de overeenkomende velden voor alle gebeurtenis typen.

> [!NOTE]
> In functions v1 als u probeert te binden `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`aan, wordt in de compiler een ' afgeschaft ' bericht weer gegeven en `Microsoft.Azure.EventGrid.Models.EventGridEvent` wordt u aangeraden om in plaats daarvan te gebruiken. Als u het nieuwe type wilt gebruiken, verwijst u naar het NuGet-pakket [micro soft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) en geeft u de `EventGridEvent` type naam volledig door met `Microsoft.Azure.EventGrid.Models`het voor voegsel. Zie [NuGet-pakketten gebruiken](functions-reference-csharp.md#using-nuget-packages) voor meer informatie over het C# verwijzen naar NuGet-pakketten in een script functie

Voor Java script-functies bevat de para meter met de eigenschap *Function. json* `name` een verwijzing naar het gebeurtenis object.

## <a name="event-schema"></a>Gebeurtenisschema

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

De eigenschappen op het hoogste niveau in de JSON-gegevens van de gebeurtenis zijn hetzelfde voor alle gebeurtenis typen, terwijl de `data` inhoud van de eigenschap specifiek is voor elk gebeurtenis type. Het voor beeld dat wordt weer gegeven, is voor een Blob Storage-gebeurtenis.

Zie [gebeurtenis eigenschappen](../event-grid/event-schema.md#event-properties) in de Event grid-documentatie voor uitleg van de algemene en gebeurtenis-specifieke eigenschappen.

Het `EventGridEvent` type definieert alleen de eigenschappen op het hoogste niveau; `Data` de eigenschap is `JObject`een.

## <a name="create-a-subscription"></a>Een abonnement maken

Als u Event Grid HTTP-aanvragen wilt ontvangen, moet u een Event Grid-abonnement maken dat de eind punt-URL opgeeft waarmee de functie wordt aangeroepen.

### <a name="azure-portal"></a>Azure Portal

Selecteer **Event grid abonnement toevoegen**voor functies die u in de Azure Portal met de Event grid trigger ontwikkelt.

![Een abonnement maken in de portal](media/functions-bindings-event-grid/portal-sub-create.png)

Wanneer u deze koppeling selecteert, wordt de pagina **gebeurtenis abonnement maken** geopend met de vooraf gevulde eind punt-URL.

![Eind punt-URL vooraf ingevuld](media/functions-bindings-event-grid/endpoint-url.png)

Zie voor meer informatie over het maken van abonnementen met behulp van de Azure Portal [aangepaste gebeurtenis maken-Azure Portal](../event-grid/custom-event-quickstart-portal.md) in de Event grid documentatie.

### <a name="azure-cli"></a>Azure-CLI

Als u een abonnement wilt maken met behulp van [de Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), gebruikt u de opdracht [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

De opdracht vereist de eind punt-URL die de functie aanroept. In het volgende voor beeld wordt het versie-specifieke URL-patroon weer gegeven:

#### <a name="version-2x-runtime"></a>Versie 2. x runtime

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Versie 1. x runtime

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

De systeem sleutel is een autorisatie sleutel die moet worden opgenomen in de eind punt-URL voor een Event Grid trigger. In de volgende sectie wordt uitgelegd hoe u de systeem sleutel kunt ophalen.

Hier volgt een voor beeld waarin wordt geabonneerd op een Blob Storage-account (met een tijdelijke aanduiding voor de systeem sleutel):

#### <a name="version-2x-runtime"></a>Versie 2. x runtime

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

#### <a name="version-2x-runtime"></a>Versie 2. x runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Versie 1. x runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Dit is een beheer-API, zodat hiervoor de [hoofd sleutel](functions-bindings-http-webhook.md#authorization-keys)van de functie-app is vereist. Verwar de systeem sleutel (voor het aanroepen van een Event Grid geactiveerde functie) niet met de hoofd sleutel (voor het uitvoeren van beheer taken voor de functie-app). Wanneer u zich abonneert op een Event Grid onderwerp, moet u ervoor zorgen dat u de systeem sleutel gebruikt.

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

Zie [autorisatie sleutels](functions-bindings-http-webhook.md#authorization-keys) in het naslag artikel over http-triggers voor meer informatie.

U kunt ook een HTTP-PUT verzenden om de sleutel waarde zelf op te geven.

## <a name="local-testing-with-viewer-web-app"></a>Lokale tests met de Web-App van viewer

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

Maak een Event Grid-abonnement van het type dat u wilt testen en geef dit de URL van uw web-app als eind punt voor gebeurtenis meldingen. Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten. De volledige URL is dus`https://<your-site-name>.azurewebsites.net/api/updates`

Voor informatie over het maken van abonnementen met behulp van de Azure Portal, Zie [aangepaste gebeurtenis maken-Azure Portal](../event-grid/custom-event-quickstart-portal.md) in de Event grid documentatie.

### <a name="generate-a-request"></a>Een aanvraag genereren

Hiermee wordt een gebeurtenis geactiveerd waarmee HTTP-verkeer wordt gegenereerd naar het eind punt van uw web-app.  Als u bijvoorbeeld een Blob Storage-abonnement hebt gemaakt, uploadt of verwijdert u een blob. Wanneer een aanvraag in uw web-app wordt weer gegeven, kopieert u de hoofd tekst van de aanvraag.

De aanvraag voor abonnements validatie wordt als eerste ontvangen. Negeer validatie aanvragen en kopieer de gebeurtenis aanvraag.

![Hoofd tekst van de aanvraag kopiëren van de web-app](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>De aanvraag hand matig plaatsen

Voer uw Event Grid-functie lokaal uit.

Gebruik een hulp programma zoals [postman](https://www.getpostman.com/) of [krul](https://curl.haxx.se/docs/httpscripting.html) om een HTTP POST-aanvraag te maken:

* Stel een `Content-Type: application/json` koptekst in.
* Stel een `aeg-event-type: Notification` koptekst in.
* Plak de RequestBin-gegevens in de hoofd tekst van de aanvraag.
* Post naar de URL van uw Event Grid-trigger functie.
  * Voor 2. x gebruikt u het volgende patroon:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Voor 1. x gebruik:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

De `functionName` para meter moet de naam zijn die is `FunctionName` opgegeven in het kenmerk.

In de volgende scherm afbeeldingen worden de kopteksten en de hoofd tekst van de aanvraag weer gegeven in postman:

![Koppen in postman](media/functions-bindings-event-grid/postman2.png)

![Aanvraag tekst in postman](media/functions-bindings-event-grid/postman.png)

De functie Trigger Event Grid wordt uitgevoerd en logboeken worden weer gegeven die vergelijkbaar zijn met het volgende voor beeld:

![Voorbeeld functie Logboeken Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Lokaal testen met ngrok

Een andere manier om een Event Grid trigger lokaal te testen, is de HTTP-verbinding tussen internet en uw ontwikkel computer te automatiseren. U kunt dit doen met een hulp programma zoals [ngrok](https://ngrok.com/):

1. [Een ngrok-eind punt maken](#create-an-ngrok-endpoint).
1. [Voer de functie voor het activeren van Event grid](#run-the-event-grid-trigger-function).
1. [Maak een event grid-abonnement](#create-a-subscription) dat gebeurtenissen naar het ngrok-eind punt verzendt.
1. [Activeer een gebeurtenis](#trigger-an-event).

Wanneer u klaar bent met testen, kunt u hetzelfde abonnement voor productie gebruiken door het eind punt bij te werken. Gebruik de opdracht [AZ eventgrid Event-Subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure cli.

### <a name="create-an-ngrok-endpoint"></a>Een ngrok-eind punt maken

Down load *ngrok. exe* van [ngrok](https://ngrok.com/)en voer de volgende opdracht uit:

```
ngrok http -host-header=localhost 7071
```

De-host-header-para meter is vereist omdat de functions-runtime aanvragen van localhost verwacht wanneer deze op localhost worden uitgevoerd. 7071 is het standaard poort nummer wanneer de runtime lokaal wordt uitgevoerd.

De opdracht maakt uitvoer als de volgende:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

U gebruikt de `https://{subdomain}.ngrok.io` URL voor uw event grid-abonnement.

### <a name="run-the-event-grid-trigger-function"></a>De functie Trigger Event Grid uitvoeren

De ngrok-URL biedt geen speciale verwerking door Event Grid. Daarom moet uw functie lokaal worden uitgevoerd wanneer het abonnement wordt gemaakt. Als dat niet het geval is, wordt het validatie antwoord niet verzonden en mislukt het maken van het abonnement.

### <a name="create-a-subscription"></a>Een abonnement maken

Maak een Event Grid-abonnement van het type dat u wilt testen en geef het uw ngrok-eind punt.

Gebruik dit eindpunt patroon voor functies 2. x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Gebruik dit eindpunt patroon voor de functies 1. x:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

De `{FUNCTION_NAME}` para meter moet de naam zijn die is `FunctionName` opgegeven in het kenmerk.

Hier volgt een voor beeld van het gebruik van de Azure CLI:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Zie eerder in dit artikel [een abonnement maken](#create-a-subscription) voor meer informatie over het maken van een abonnement.

### <a name="trigger-an-event"></a>Een gebeurtenis activeren

Hiermee wordt een gebeurtenis geactiveerd waarmee HTTP-verkeer wordt gegenereerd naar uw ngrok-eind punt.  Als u bijvoorbeeld een Blob Storage-abonnement hebt gemaakt, uploadt of verwijdert u een blob.

De functie Trigger Event Grid wordt uitgevoerd en logboeken worden weer gegeven die vergelijkbaar zijn met het volgende voor beeld:

![Voorbeeld functie Logboeken Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Een HTTP-trigger gebruiken als een Event Grid trigger

Event Grid gebeurtenissen worden ontvangen als HTTP-aanvragen, zodat u gebeurtenissen kunt afhandelen met behulp van een HTTP-trigger in plaats van een Event Grid-trigger. Een mogelijke reden hiervoor is om meer controle te krijgen over de eind punt-URL die de functie aanroept. Een andere reden is wanneer u gebeurtenissen moet ontvangen in het [CloudEvents-schema](../event-grid/cloudevents-schema.md). Op dit moment ondersteunt de trigger van de Event Grid het CloudEvents-schema niet. In de voor beelden in deze sectie worden oplossingen weer gegeven voor zowel het Event Grid schema als het CloudEvents-schema.

Als u een HTTP-trigger gebruikt, moet u code schrijven voor wat de Event Grid trigger automatisch doet:

* Hiermee verzendt u een validatie antwoord naar een [validatie aanvraag](../event-grid/security-authentication.md#webhook-event-delivery)voor het abonnement.
* Hiermee wordt de functie aangeroepen per element van de gebeurtenis matrix die is opgenomen in de hoofd tekst van de aanvraag.

Voor informatie over de URL die moet worden gebruikt om de functie lokaal aan te roepen of wanneer deze wordt uitgevoerd in azure, raadpleegt u de [referentie documentatie voor http-trigger bindingen](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Event Grid-schema

Met de volgende C# voorbeeld code voor een http-trigger wordt Event grid trigger gedrag gesimuleerd. Gebruik dit voor beeld voor gebeurtenissen die in het Event Grid schema worden bezorgd.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Met de volgende Java script-voorbeeld code voor een HTTP-trigger wordt Event Grid trigger gedrag gesimuleerd. Gebruik dit voor beeld voor gebeurtenissen die in het Event Grid schema worden bezorgd.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

De code voor het afhandelen van gebeurtenissen gaat in `messages` de lus door de matrix.

### <a name="cloudevents-schema"></a>CloudEvents-schema

Met de volgende C# voorbeeld code voor een http-trigger wordt Event grid trigger gedrag gesimuleerd.  Gebruik dit voor beeld voor gebeurtenissen die in het CloudEvents-schema worden bezorgd.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Met de volgende Java script-voorbeeld code voor een HTTP-trigger wordt Event Grid trigger gedrag gesimuleerd. Gebruik dit voor beeld voor gebeurtenissen die in het CloudEvents-schema worden bezorgd.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en bindingen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Meer informatie over Event Grid](../event-grid/overview.md)
