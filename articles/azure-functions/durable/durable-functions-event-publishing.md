---
title: Durable Functions publiceren naar Azure Event Grid (preview-versie)
description: Meer informatie over het configureren van automatische Azure Event Grid publicatie voor Durable Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: 4e1a714a6d46a9422fb298749cfe30ac70ffc8c3
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614912"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publiceren naar Azure Event Grid (preview-versie)

In dit artikel wordt beschreven hoe u Durable Functions instelt voor het publiceren van Orchestrator-levenscyclus gebeurtenissen (zoals gemaakte, voltooide en mislukte) naar een aangepast [Azure Event grid onderwerp](https://docs.microsoft.com/azure/event-grid/overview).

Hier volgen enkele scenario's waarin deze functie nuttig is:

* **DevOps-scenario's zoals Blue/groen-implementaties**: u wilt mogelijk weten of er taken worden uitgevoerd voordat u de [implementatie strategie naast](durable-functions-versioning.md#side-by-side-deployments)elkaar implementeert.

* **Ondersteuning voor geavanceerde bewakings-en diagnostische**gegevens: u kunt de informatie over de Orchestration-status bijhouden in een extern archief dat is geoptimaliseerd voor query's, zoals SQL database of CosmosDB.

* **Langlopende achtergrond activiteit**: als u Durable functions gebruikt voor een langlopende achtergrond activiteit, kunt u met deze functie de huidige status weten.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Installeer [micro soft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in uw Durable functions-project.
* Installeer [Azure Storage emulator](../../storage/common/storage-use-emulator.md).
* [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) installeren of [Azure Cloud shell](../../cloud-shell/overview.md) gebruiken

## <a name="create-a-custom-event-grid-topic"></a>Een aangepast Event grid-onderwerp maken

Een event grid-onderwerp maken voor het verzenden van gebeurtenissen van Durable Functions. De volgende instructies laten zien hoe u een onderwerp maakt met behulp van Azure CLI. Raadpleeg de volgende artikelen voor informatie over hoe u dit doet met Power shell of de Azure Portal:

* [EventGrid-Quick starts: aangepaste gebeurtenis maken-Power shell](../../event-grid/custom-event-quickstart-powershell.md)
* [EventGrid Quick starts: aangepaste gebeurtenis-Azure Portal maken](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht `az group create`. Azure Event Grid biedt momenteel geen ondersteuning voor alle regio's. Zie [Azure Event grid-overzicht](../../event-grid/overview.md)voor meer informatie over welke regio's worden ondersteund.

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een event grid-onderwerp bevat een door de gebruiker gedefinieerd eind punt waarop u uw gebeurtenis plaatst. Vervang `<topic_name>` door een unieke naam voor het onderwerp. De naam van het onderwerp moet uniek zijn omdat het een DNS-vermelding wordt.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Het eind punt en de sleutel ophalen

Het eind punt van het onderwerp ophalen. Vervang `<topic_name>` door de naam die u hebt gekozen.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Haal de sleutel van het onderwerp op. Vervang `<topic_name>` door de naam die u hebt gekozen.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

U kunt nu gebeurtenissen verzenden naar het onderwerp.

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid publicatie configureren

Zoek in uw Durable Functions-project het `host.json` bestand.

Voeg `eventGridTopicEndpoint` en `eventGridKeySettingName` toe in een `durableTask`-eigenschap.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

De mogelijke Azure Event Grid configuratie-eigenschappen vindt u in de [host. json-documentatie](../functions-host-json.md#durabletask). Nadat u het `host.json`-bestand hebt geconfigureerd, verzendt uw functie-app levenscyclus gebeurtenissen naar het event grid-onderwerp. Dit werkt wanneer u de functie-app zowel lokaal als in azure uitvoert.

Stel de app-instelling in voor de sleutel van het onderwerp in de functie-app en `local.setting.json`. De volgende JSON is een voor beeld van de `local.settings.json` voor lokale fout opsporing. Vervang `<topic_key>` door de sleutel onderwerp.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Zorg ervoor dat de [opslag emulator](../../storage/common/storage-use-emulator.md) werkt. Het is een goed idee om de `AzureStorageEmulator.exe clear all` opdracht uit te voeren voordat u het programma uitvoert.

## <a name="create-functions-that-listen-for-events"></a>Functies maken die Luis teren naar gebeurtenissen

Maak een functie-app. U kunt deze het beste vinden in dezelfde regio als het onderwerp Event grid.

### <a name="create-an-event-grid-trigger-function"></a>Een trigger functie voor gebeurtenis raster maken

Maak een functie om de levenscyclus gebeurtenissen te ontvangen. Selecteer **aangepaste functie**.

![Selecteer een aangepaste functie maken.](./media/durable-functions-event-publishing/functions-portal.png)

Kies Event Grid trigger en selecteer `C#`.

![Selecteer de trigger Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Voer de naam van de functie in en selecteer vervolgens `Create`.

![Maak de Event Grid-trigger.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Er wordt een functie gemaakt met de volgende code:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C#Schriften

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Selecteer `Add Event Grid Subscription`. Met deze bewerking wordt een event grid-abonnement toegevoegd voor het event grid-onderwerp dat u hebt gemaakt. Zie [concepten in azure Event grid](https://docs.microsoft.com/azure/event-grid/concepts) voor meer informatie

![Selecteer de koppeling Trigger Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selecteer `Event Grid Topics` voor het **type onderwerp**. Selecteer de resource groep die u hebt gemaakt voor het onderwerp Event grid. Selecteer vervolgens het exemplaar van het onderwerp Event grid. Druk op `Create`.

![Hiermee wordt een Event Grid-abonnement gemaakt.](./media/durable-functions-event-publishing/eventsubscription.png)

Nu bent u klaar om levenscyclus gebeurtenissen te ontvangen.

## <a name="create-durable-functions-to-send-the-events"></a>Durable Functions maken om de gebeurtenissen te verzenden

Start in uw Durable Functions-project de fout opsporing op uw lokale machine.  De volgende code is hetzelfde als de sjabloon code voor de Durable Functions. U hebt `host.json` en `local.settings.json` al geconfigureerd op uw lokale machine.

### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`, het kenmerk `OrchestrationClient` in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

Als u de `Sample_HttpStart` aanroept met behulp van Postman of uw browser, wordt met duurzame functie levenscyclus gebeurtenissen verzonden. Het eind punt wordt gewoonlijk `http://localhost:7071/api/Sample_HttpStart` voor lokale fout opsporing.

Raadpleeg de logboeken van de functie die u hebt gemaakt in de Azure Portal.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Gebeurtenisschema

In de volgende lijst wordt het schema voor levenscyclus gebeurtenissen uitgelegd:

* **`id`** : de unieke id voor de gebeurtenis grid-gebeurtenis.
* **`subject`** : het pad naar het gebeurtenis onderwerp. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` worden `Running`, `Completed`, `Failed`en `Terminated`.  
* **`data`** : Durable functions specifieke para meters.
  * **`hubName`** : [TaskHub](durable-functions-task-hubs.md) -naam.
  * **`functionName`** : Orchestrator-functie naam.
  * **`instanceId`** : Durable functions instanceId.
  * **`reason`** : aanvullende gegevens die zijn gekoppeld aan de tracerings gebeurtenis. Zie [Diagnostische gegevens in Durable functions (Azure functions)](durable-functions-diagnostics.md) voor meer informatie.
  * **`runtimeStatus`** : status van Orchestration runtime. Uitvoeren, voltooid, mislukt, geannuleerd.
* **`eventType`** : ' orchestratorEvent '
* **`eventTime`** : de tijd van de gebeurtenis (UTC).
* **`dataVersion`** : versie van het levenscyclus gebeurtenis schema.
* **`metadataVersion`** : de versie van de meta gegevens.
* **`topic`** : resource grid-onderwerpnaam.

## <a name="how-to-test-locally"></a>Lokaal testen

Als u lokaal wilt testen, gebruikt u [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Instantie beheer leren in Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Versie beheer leren in Durable Functions](durable-functions-versioning.md)