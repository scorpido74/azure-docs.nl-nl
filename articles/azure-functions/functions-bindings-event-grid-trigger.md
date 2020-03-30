---
title: Azure Event Grid trigger voor Azure Functions
description: Lees code wanneer gebeurtenisrastergebeurtenissen in Azure-functies worden verzonden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 2027629e1e9e297c97cbf40485ebe7dc2e3e6c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277724"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Event Grid trigger voor Azure Functions

Gebruik de functietrigger om te reageren op een gebeurtenis die naar een gebeurtenisrasteronderwerp is verzonden.

Zie het [overzicht](./functions-bindings-event-grid.md)voor informatie over de installatie en configuratiedetails.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

Zie [Gebeurtenissen ontvangen naar een HTTP-eindpunt](../event-grid/receive-events.md)voor een http-triggervoorbeeld.

### <a name="c-2x-and-higher"></a>C# (2.x en hoger)

In het volgende voorbeeld wordt een `EventGridEvent` [C#-functie](functions-dotnet-class-library.md) weergegeven die zich bindt aan:

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

Zie Pakketten, [kenmerken](#attributes-and-annotations), [Configuratie](#configuration)en [Gebruik](#usage)voor meer informatie.

### <a name="version-1x"></a>Versie 1.x

In het volgende voorbeeld wordt een functie Functies `JObject`1.x [C#](functions-dotnet-class-library.md) weergegeven die zich bindt aan:

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een triggerbinding weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

Hier is een voorbeeld dat `EventGridEvent`zich bindt aan:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Zie Pakketten, [kenmerken](#attributes-and-annotations), [Configuratie](#configuration)en [Gebruik](#usage)voor meer informatie.

### <a name="version-1x"></a>Versie 1.x

Hier is functies 1.x C# scriptcode `JObject`die bindt aan:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een triggerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

Hier is de JavaScript-code:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt een triggerbinding weergegeven in een *function.json-bestand* en een [Python-functie](functions-reference-python.md) die de binding gebruikt.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

Hier is de Python-code:

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

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [Gebeurtenisrastertrigger, parameter String](#event-grid-trigger-string-parameter)
* [Gebeurtenisrastertrigger, PARAMETER POJO](#event-grid-trigger-pojo-parameter)

In de volgende voorbeelden wordt triggerbinding in [Java](functions-reference-java.md) weergegeven die de binding `String` gebruikt en een gebeurtenis uitprinten, waarbij de gebeurtenis als tweede wordt ontvangen als POJO.

### <a name="event-grid-trigger-string-parameter"></a>Gebeurtenisrastertrigger, parameter String

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

### <a name="event-grid-trigger-pojo-parameter"></a>Gebeurtenisrastertrigger, PARAMETER POJO

In dit voorbeeld wordt de volgende POJO gebruikt, die de eigenschappen op het hoogste niveau van een gebeurtenis eventgrid weergeeft:

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

Bij aankomst wordt de JSON-payload van het ```EventSchema``` evenement gedeserialiseerd in de POJO voor gebruik door de functie. Dit proces stelt de functie in staat om op een objectgeoriënteerde manier toegang te krijgen tot de eigenschappen van de gebeurtenis.

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

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op parameters waarvan de `EventGridTrigger` waarde afkomstig zou zijn van EventGrid. Parameters met deze annotaties zorgen ervoor dat de functie wordt uitgevoerd wanneer een gebeurtenis aankomt.  Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Hier is `EventGridTrigger` een kenmerk in een methodehandtekening:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Zie Voorbeeld C# voor een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Met de [gebeurtenisnetonaantekening](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) u een gebeurtenisrasterbinding declaratief configureren door configuratiewaarden op te geven. Zie het [voorbeeld](#example) en [de configuratiesecties](#configuration) voor meer details.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd die u inhet *bestand function.json* instelt. Er zijn geen constructorparameters of `EventGridTrigger` -eigenschappen die in het kenmerk moeten worden ingesteld.

|functie.json, eigenschap |Beschrijving|
|---------|---------|
| **Type** | Vereist - moet `eventGridTrigger`worden ingesteld op . |
| **direction** | Vereist - moet `in`worden ingesteld op . |
| **Naam** | Vereist - de variabele naam die wordt gebruikt in functiecode voor de parameter die de gebeurtenisgegevens ontvangt. |

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

In Azure Functions 1.x u de volgende parametertypen gebruiken voor de trigger gebeurtenisraster:

* `JObject`
* `string`

In Azure Functions 2.x en hoger u ook het volgende parametertype gebruiken voor de trigger gebeurtenisraster:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Hiermee definieert u eigenschappen voor de velden die gemeenschappelijk zijn voor alle gebeurtenistypen.

> [!NOTE]
> In Functies v1 als u `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`probeert te binden aan, zal de compiler een `Microsoft.Azure.EventGrid.Models.EventGridEvent` "afgeschaft" bericht weergeven en u adviseren om in plaats daarvan te gebruiken. Als u het nieuwere type wilt gebruiken, verwijst u naar het `EventGridEvent` [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-pakket en kwalificeert u de typenaam volledig door deze vooraf op te lossen met `Microsoft.Azure.EventGrid.Models`.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In Azure Functions 1.x u de volgende parametertypen gebruiken voor de trigger gebeurtenisraster:

* `JObject`
* `string`

In Azure Functions 2.x en hoger u ook het volgende parametertype gebruiken voor de trigger gebeurtenisraster:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Hiermee definieert u eigenschappen voor de velden die gemeenschappelijk zijn voor alle gebeurtenistypen.

> [!NOTE]
> In Functies v1 als u `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`probeert te binden aan, zal de compiler een `Microsoft.Azure.EventGrid.Models.EventGridEvent` "afgeschaft" bericht weergeven en u adviseren om in plaats daarvan te gebruiken. Als u het nieuwere type wilt gebruiken, verwijst u naar het `EventGridEvent` [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-pakket en kwalificeert u de typenaam volledig door deze vooraf op te lossen met `Microsoft.Azure.EventGrid.Models`. Zie [NuGet-pakketten gebruiken](functions-reference-csharp.md#using-nuget-packages) voor informatie over het verwijzen naar NuGet-pakketten in een C#-scriptfunctie

# <a name="javascript"></a>[Javascript](#tab/javascript)

De instantie Gebeurtenisraster is beschikbaar via de parameter die is `name` geconfigureerd in de eigenschap van het *function.json-bestand.*

# <a name="python"></a>[Python](#tab/python)

De instantie Gebeurtenisraster is beschikbaar via de parameter die is `name` geconfigureerd in `func.EventGridEvent`de eigenschap van het *function.json-bestand,* getypt als .

# <a name="java"></a>[Java](#tab/java)

The Event Grid event instance is available via the parameter associated to the `EventGridTrigger` attribute, typed as an `EventSchema`. Zie het [voorbeeld](#example) voor meer details.

---

## <a name="event-schema"></a>Gebeurtenisschema

Gegevens voor een gebeurtenis Event Grid worden ontvangen als een JSON-object in de hoofdtekst van een HTTP-aanvraag. De JSON lijkt op het volgende voorbeeld:

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

Het getoonde voorbeeld is een array van één element. Gebeurtenisraster stuurt altijd een array en kan meer dan één gebeurtenis in de array verzenden. De runtime roept uw functie één keer aan voor elk arrayelement.

De eigenschappen op het hoogste niveau in de JSON-gegevens van gebeurtenis `data` zijn hetzelfde voor alle gebeurtenistypen, terwijl de inhoud van de eigenschap specifiek is voor elk gebeurtenistype. Het voorbeeld dat wordt weergegeven is voor een blob-opslaggebeurtenis.

Zie [Gebeurtenis-eigenschappen](../event-grid/event-schema.md#event-properties) in de documentatie van het gebeurtenisraster voor uitleg over de algemene en gebeurtenisspecifieke eigenschappen.

Het `EventGridEvent` type definieert alleen de eigenschappen op het hoogste niveau; de `Data` eigenschap `JObject`is een .

## <a name="create-a-subscription"></a>Een abonnement maken

Als u HTTP-aanvragen voor gebeurtenisraster wilt ontvangen, maakt u een gebeurtenisrasterabonnement dat de URL van het eindpunt opgeeft die de functie aanroept.

### <a name="azure-portal"></a>Azure Portal

Voor functies die u ontwikkelt in de Azure-portal met de trigger gebeurtenisraster, selecteert u **Gebeurtenisrasterabonnement toevoegen**.

![Abonnement maken in portal](media/functions-bindings-event-grid/portal-sub-create.png)

Wanneer u deze koppeling selecteert, opent de portal de pagina **Gebeurtenisabonnement maken** met de URL van het eindpunt vooraf ingevuld.

![Vooraf ingevulde URL voor eindpunt](media/functions-bindings-event-grid/endpoint-url.png)

Zie [Aangepaste gebeurtenis maken - Azure-portal](../event-grid/custom-event-quickstart-portal.md) in de documentatie van het gebeurtenisraster voor meer informatie over het maken van abonnementen met de Azure-portal.

### <a name="azure-cli"></a>Azure-CLI

Als u een abonnement wilt maken met [de Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)gebruikt u de opdracht [voor het maken van een az-eventgrid-gebeurtenisabonnement.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create)

De opdracht vereist de URL van het eindpunt die de functie aanroept. In het volgende voorbeeld ziet u het versiespecifieke URL-patroon:

#### <a name="version-2x-and-higher-runtime"></a>Runtime van versie 2.x (en hoger)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Runtime van versie 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

De systeemsleutel is een autorisatiesleutel die moet worden opgenomen in de URL van het eindpunt voor een trigger voor gebeurtenisraster. In de volgende sectie wordt uitgelegd hoe u de systeemsleutel krijgen.

Hier is een voorbeeld dat zich abonneert op een blob-opslagaccount (met een tijdelijke aanduiding voor de systeemsleutel):

#### <a name="version-2x-and-higher-runtime"></a>Runtime van versie 2.x (en hoger)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Runtime van versie 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Zie [de snelstart van de blobopslag](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) of het andere gebeurtenisraster voor meer informatie over het maken van een abonnement.

### <a name="get-the-system-key"></a>De systeemsleutel oppakken

U de systeemsleutel krijgen met behulp van de volgende API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Runtime van versie 2.x (en hoger)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Runtime van versie 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Dit is een admin-API, dus het vereist uw functie-app [master-toets](functions-bindings-http-webhook-trigger.md#authorization-keys). Verwar de systeemsleutel (voor het inschakelen van een triggerfunctie voor gebeurtenisraster) niet met de hoofdsleutel (voor het uitvoeren van administratieve taken in de functie-app). Wanneer u zich abonneert op een gebeurtenisrasteronderwerp, moet u de systeemsleutel gebruiken.

Hier is een voorbeeld van het antwoord dat de systeemsleutel biedt:

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

U de hoofdsleutel voor uw functie-app ophalen via het tabblad **Instellingen voor de functie-app** in de portal.

> [!IMPORTANT]
> De hoofdsleutel geeft beheerders toegang tot uw functie-app. Deel deze sleutel niet met derden of distribueer deze niet in native clienttoepassingen.

Zie [Autorisatiesleutels](functions-bindings-http-webhook-trigger.md#authorization-keys) in het http-triggerverwijzingsartikel voor meer informatie.

U ook een HTTP PUT sturen om zelf de sleutelwaarde op te geven.

## <a name="local-testing-with-viewer-web-app"></a>Lokale tests met viewerweb-app

Als u een gebeurtenisrastertrigger lokaal wilt testen, moet u HTTP-aanvragen voor gebeurtenisrasters vanuit hun oorsprong in de cloud naar uw lokale machine laten leveren. Een manier om dat te doen is door het vastleggen van verzoeken online en handmatig verzenden op uw lokale machine:

1. [Maak een viewerweb-app](#create-a-viewer-web-app) die gebeurtenisberichten vastlegt.
1. [Maak een abonnement op het gebeurtenisraster](#create-an-event-grid-subscription) dat gebeurtenissen naar de viewer-app verzendt.
1. [Genereer een aanvraag](#generate-a-request) en kopieer de aanvraaginstantie vanuit de viewer-app.
1. [Plaats het verzoek handmatig](#manually-post-the-request) op de URL van de localhost van de triggerfunctie Van het gebeurtenisraster.

Wanneer u klaar bent met testen, u hetzelfde abonnement voor productie gebruiken door het eindpunt bij te werken. Gebruik de opdracht Azure CLI [voor het az-eventgrid-gebeurtenisabonnement.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update)

### <a name="create-a-viewer-web-app"></a>Een web-app voor viewer maken

Als u het vastleggen van gebeurtenisberichten wilt vereenvoudigen, u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren die de gebeurtenisberichten weergeeft. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

![Nieuwe site weergeven](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

Maak een eventgrid-abonnement op het type dat u wilt testen en geef het de URL van uw web-app als eindpunt voor gebeurtenismelding. Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten. Dus, de volledige URL is`https://<your-site-name>.azurewebsites.net/api/updates`

Zie [Aangepaste gebeurtenis maken - Azure-portal](../event-grid/custom-event-quickstart-portal.md) in de documentatie van het gebeurtenisraster voor informatie over het maken van abonnementen met behulp van de Azure-portal.

### <a name="generate-a-request"></a>Een aanvraag genereren

Activeer een gebeurtenis die HTTP-verkeer naar het eindpunt van uw web-app genereert.  Als u bijvoorbeeld een blob-opslagabonnement hebt gemaakt, uploadt of verwijdert u een blob. Wanneer een aanvraag wordt weergegeven in uw web-app, kopieert u de aanvraaginstantie.

Het validatieverzoek voor het abonnement wordt als eerste ontvangen; alle validatieaanvragen negeren en de gebeurtenisaanvraag kopiëren.

![Aanvraaginstantie kopiëren vanuit de web-app](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Plaats het verzoek handmatig

Voer de functie Event Grid lokaal uit.

Gebruik een hulpmiddel zoals [Postbode](https://www.getpostman.com/) of [krul](https://curl.haxx.se/docs/httpscripting.html) om een HTTP POST-aanvraag te maken:

* Stel `Content-Type: application/json` een koptekst in.
* Stel `aeg-event-type: Notification` een koptekst in.
* Plak de RequestBin-gegevens in de aanvraaginstantie.
* Post naar de URL van de triggerfunctie Gebeurtenisraster.
  * Voor 2.x en hoger gebruik het volgende patroon:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Voor 1.x gebruik:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

De `functionName` parameter moet de naam `FunctionName` zijn die in het kenmerk is opgegeven.

De volgende schermafbeeldingen tonen de kopteksten en het aanvraaglichaam in Postman:

![Kopteksten in Postbode](media/functions-bindings-event-grid/postman2.png)

![Aanvraag lichaam in Postbode](media/functions-bindings-event-grid/postman.png)

De triggerfunctie Gebeurtenisraster wordt uitgevoerd en toont logboeken die vergelijkbaar zijn met het volgende voorbeeld:

![Voorbeeldgebeurtenisrastertriggerfunctielogboeken](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Volgende stappen

* [Een gebeurtenis Event Grid verzenden](./functions-bindings-event-grid-trigger.md)
