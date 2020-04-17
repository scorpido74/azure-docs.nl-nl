---
title: Publicatie van duurzame functies in Azure Event Grid (voorbeeld)
description: Meer informatie over het configureren van automatische Azure Event Grid publishing voor duurzame functies.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535482"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicatie van duurzame functies in Azure Event Grid (voorbeeld)

In dit artikel ziet u hoe u Duurzame functies instelt voor het publiceren van gebeurtenissen in de levenscyclus van orkestcycli (zoals gemaakt, voltooid en mislukt) in een aangepast [Azure Event Grid-onderwerp](https://docs.microsoft.com/azure/event-grid/overview).

Hieronder volgen enkele scenario's waarin deze functie nuttig is:

* **DevOps-scenario's zoals blauw/groen-implementaties:** U wilt misschien weten of er taken worden uitgevoerd voordat u de [implementatiestrategie naast elkaar](durable-functions-versioning.md#side-by-side-deployments)implementeert.

* **Ondersteuning voor geavanceerde monitoring en diagnostische gegevens:** u de statusgegevens van orchestration bijhouden in een externe winkel die is geoptimaliseerd voor query's, zoals Azure SQL Database of Azure Cosmos DB.

* **Langdurige achtergrondactiviteit:** als u duurzame functies gebruikt voor een langdurige achtergrondactiviteit, helpt deze functie u om de huidige status te weten.

## <a name="prerequisites"></a>Vereisten

* Installeer [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in uw project Duurzame functies.
* Installeer [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) (alleen Windows) of gebruik een bestaand Azure Storage-account.
* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) installeren of Azure [Cloud Shell](../../cloud-shell/overview.md) gebruiken

## <a name="create-a-custom-event-grid-topic"></a>Een aangepast gebeurtenisrasteronderwerp maken

Maak een gebeurtenisrasteronderwerp voor het verzenden van gebeurtenissen vanuit duurzame functies. In de volgende instructies ziet u hoe u een onderwerp maakt met Azure CLI. U dit ook doen [door PowerShell](../../event-grid/custom-event-quickstart-powershell.md) te gebruiken of door [de Azure-portal te gebruiken.](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht `az group create`. Azure Event Grid biedt momenteel geen ondersteuning voor alle regio's. Zie het [overzicht Azure Event Grid](../../event-grid/overview.md)voor informatie over welke regio's worden ondersteund.

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een onderwerp gebeurtenisraster biedt een door de gebruiker gedefinieerd eindpunt waarop u uw gebeurtenis plaatst. Vervang `<topic_name>` door een unieke naam voor het onderwerp. De onderwerpnaam moet uniek zijn omdat het een DNS-vermelding wordt.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Het eindpunt en de sleutel ophalen

Krijg het eindpunt van het onderwerp. Vervang `<topic_name>` de naam die u hebt gekozen.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Haal de onderwerpsleutel. Vervang `<topic_name>` de naam die u hebt gekozen.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nu u evenementen naar het onderwerp sturen.

## <a name="configure-event-grid-publishing"></a>Publicatie van gebeurtenisrasterconfigureren

Zoek het `host.json` bestand in het project Duurzame functies.

### <a name="durable-functions-1x"></a>Duurzame functies 1,x

Toevoegen `eventGridTopicEndpoint` `eventGridKeySettingName` en `durableTask` in een eigenschap.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Duurzame functies 2.x

Voeg `notifications` een sectie `durableTask` toe aan de `<topic_name>` eigenschap van het bestand, vervangen door de naam die u hebt gekozen. Als `durableTask` de `extensions` of-eigenschappen niet bestaan, maakt u deze als volgt:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

De mogelijke configuratie-eigenschappen van Azure Event Grid zijn te vinden in de [documentatie host.json](../functions-host-json.md#durabletask). Nadat u `host.json` het bestand hebt geconfigureerd, verzendt uw functie-app levenscyclusgebeurtenissen naar het onderwerp Gebeurtenisraster. Dit werkt wanneer u uw functie-app zowel lokaal als in Azure uitvoert.

Stel de app-instelling in voor de `local.settings.json`onderwerpsleutel in de functie-app en . De volgende JSON is `local.settings.json` een voorbeeld van de voor lokale debugging. Vervang `<topic_key>` door de onderwerpsleutel.  

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

Als u de [Storage Emulator](../../storage/common/storage-use-emulator.md) (alleen Windows) gebruikt, controleert u of deze werkt. Het is een goed idee `AzureStorageEmulator.exe clear all` om de opdracht uit te voeren voordat u het uitvoert.

Als u een bestaand Azure Storage-account gebruikt, vervangt u de `UseDevelopmentStorage=true` `local.settings.json` verbindingstekenreeks.

## <a name="create-functions-that-listen-for-events"></a>Functies maken die naar gebeurtenissen luisteren

Maak met de Azure-portal een andere functie-app om te luisteren naar gebeurtenissen die zijn gepubliceerd door de app Duurzame functies. Het is het beste om het te vinden in dezelfde regio als het Event Grid-onderwerp.

### <a name="create-an-event-grid-trigger-function"></a>Een triggerfunctie gebeurtenisraster maken

Maak een functie om de levenscyclusgebeurtenissen te ontvangen. Selecteer **Aangepaste functie**.

![Selecteer een aangepaste functie Maken.](./media/durable-functions-event-publishing/functions-portal.png)

Kies Gebeurtenisrastertrigger en selecteer een taal.

![Selecteer de trigger voor gebeurtenisrasters.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Voer de naam van de `Create`functie in en selecteer .

![Maak de trigger voor gebeurtenisrasters.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Er wordt een functie met de volgende code gemaakt:

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Selecteer `Add Event Grid Subscription`. Met deze bewerking wordt een Event Grid-abonnement toegevoegd voor het onderwerp Gebeurtenisraster dat u hebt gemaakt. Zie [Concepten in Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts) voor meer informatie

![Selecteer de koppeling Gebeurtenisrastertrigger.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selecteer `Event Grid Topics` **voor Onderwerptype**. Selecteer de resourcegroep die u hebt gemaakt voor het onderwerp Gebeurtenisraster. Selecteer vervolgens de instantie van het onderwerp Gebeurtenisraster. Druk `Create`op .

![Hiermee wordt een Event Grid-abonnement gemaakt.](./media/durable-functions-event-publishing/eventsubscription.png)

Nu bent u klaar om levenscyclusgebeurtenissen te ontvangen.

## <a name="run-durable-functions-app-to-send-the-events"></a>App Duurzame functies uitvoeren om de gebeurtenissen te verzenden

Ga in het project Duurzame functies dat u eerder hebt geconfigureerd, beginnen met foutopsporing op uw lokale machine en start u een orkestratie. De app publiceert gebeurtenissen over de levenscyclus van duurzame functies in gebeurtenisraster. Controleer of gebeurtenisraster de listenerfunctie activeert die u hebt gemaakt door de logboeken in de Azure-portal te controleren.

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

In de volgende lijst wordt het schema voor levenscyclusgebeurtenissen uitgelegd:

* **`id`**: Unieke id voor de gebeurtenis Event Grid.
* **`subject`**: Pad naar het onderwerp van het evenement. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`zal `Running`, `Completed` `Failed`, `Terminated`en .  
* **`data`**: Duurzame functies Specifieke parameters.
  * **`hubName`**: De naam [van TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: Orchestrator functie naam.
  * **`instanceId`**: Duurzame functies instanceId.
  * **`reason`**: Aanvullende gegevens die zijn gekoppeld aan de trackinggebeurtenis. Zie [Diagnostische functies (Azure-functies) voor](durable-functions-diagnostics.md) meer informatie.
  * **`runtimeStatus`**: Orchestration Runtime Status. Uitvoeren, voltooid, mislukt, geannuleerd.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Gebeurtenistijd (UTC).
* **`dataVersion`**: Versie van het gebeurtenisschema voor levenscyclus.
* **`metadataVersion`**: Versie van de metagegevens.
* **`topic`**: Gebeurtenisrasteronderwerpbron.

## <a name="how-to-test-locally"></a>Lokaal testen

Als u lokaal wilt testen, leest u [Azure Function Event Grid Trigger Local Debugging](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Instance management leren in duurzame functies](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Versiebeheer leren in duurzame functies](durable-functions-versioning.md)
