---
title: Instanties in Durable Functions-Azure beheren
description: Meer informatie over het beheren van instanties in de Durable Functions-extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3db0cd3dd01e3f5f6af6b4b668d1ccac094624a2
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735178"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Instanties in Durable Functions in azure beheren

Als u de extensie [Durable functions](durable-functions-overview.md) gebruikt voor Azure functions of als u dit wilt doen, moet u ervoor zorgen dat u het beste kunt profiteren. U kunt uw Durable Functions Orchestration-instanties optimaliseren door te leren hoe u deze beheert. In dit artikel worden de details van elke bewerking van exemplaar beheer beschreven.

U kunt bijvoorbeeld exemplaren starten en beëindigen, en u kunt een query uitvoeren op instanties, met inbegrip van de mogelijkheid om query's uit te zoeken op alle exemplaren en query instanties met filters. Daarnaast kunt u gebeurtenissen naar instanties verzenden, wachten op de Orchestration-voltooiing en de HTTP Management-webhook-Url's ophalen. In dit artikel worden ook andere beheer bewerkingen behandeld, zoals het terugspoelen van instanties, het opschonen van de exemplaar geschiedenis en het verwijderen van een taak hub.

In Durable Functions kunt u kiezen hoe u elk van deze beheer bewerkingen wilt implementeren. In dit artikel vindt u voor beelden van de [Azure functions core tools](../functions-run-local.md) voor .netC#() en Java script.

## <a name="start-instances"></a>Instanties starten

Het is belang rijk om een instantie van indeling te kunnen starten. Dit wordt vaak gedaan wanneer u een Durable Functions binding in de trigger van een andere functie gebruikt.

Met de methode [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) op de [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) `startNew` of op `DurableOrchestrationClient` de (Java script) wordt een nieuw exemplaar gestart. U verkrijgt instanties van deze klasse met behulp van de `orchestrationClient` binding. Intern in deze methode een bericht in de controle wachtrij, waarmee vervolgens het begin van een functie wordt geactiveerd met de opgegeven naam die de `orchestrationTrigger` trigger binding gebruikt.

Deze asynchrone bewerking is voltooid wanneer het Orchestration-proces is gepland. Het Orchestration-proces moet binnen 30 seconden beginnen. Als het langer duurt, ziet u een `TimeoutException`.

> [!WARNING]
> Wanneer u lokaal ontwikkelt in Java script, stelt u `WEBSITE_HOSTNAME` de `localhost:<port>` omgevings variabele in `localhost:7071`op (bijvoorbeeld) om `DurableOrchestrationClient`de methoden te gebruiken. Zie het [github-probleem](https://github.com/Azure/azure-functions-durable-js/issues/28)voor meer informatie over deze vereiste.

### <a name="net"></a>.NET

De para meters voor [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) zijn als volgt:

* **Naam**: De naam van de Orchestrator-functie die moet worden gepland.
* **Invoer**: Alle JSON-serialiseerbare gegevens die moeten worden door gegeven als invoer voor de Orchestrator-functie.
* **InstanceId**: Beschrijving De unieke ID van het exemplaar. Als u deze para meter niet opgeeft, gebruikt de methode een wille keurige ID.

Hier volgen enkele voorbeelden:

### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

De para meters `startNew` zijn als volgt:

* **Naam**: De naam van de Orchestrator-functie die moet worden gepland.
* **InstanceId**: Beschrijving De unieke ID van het exemplaar. Als u deze para meter niet opgeeft, gebruikt de methode een wille keurige ID.
* **Invoer**: Beschrijving Alle JSON-serialiseerbare gegevens die moeten worden door gegeven als invoer voor de Orchestrator-functie.

Hier volgt een eenvoudig voor beeld van Java script:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Gebruik een wille keurige id voor de exemplaar-ID. Dit zorgt voor een gelijke belasting verdeling wanneer u Orchestrator-functies op meerdere Vm's wilt schalen. De juiste tijd voor het gebruik van niet-wille keurig exemplaar-Id's is wanneer de ID afkomstig moet zijn van een externe bron of wanneer u het [Singleton-Orchestrator](durable-functions-singletons.md) -patroon implementeert.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt een exemplaar ook rechtstreeks starten met behulp van de [Azure functions core tools](../functions-run-local.md) `durable start-new` opdracht. Hierbij worden de volgende para meters gebruikt:

* (vereist): **`function-name`** De naam van de functie die moet worden gestart.
* (optioneel): **`input`** Invoer voor de functie, hetzij inline of door middel van een JSON-bestand. Voor bestanden voegt u een voor voegsel toe aan het pad naar het `@`bestand met, `@path/to/file.json`zoals.
* (optioneel): **`id`** ID van het Orchestration-exemplaar. Als u deze para meter niet opgeeft, gebruikt de opdracht een wille keurige GUID.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaard waarde is AzureWebJobsStorage.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaard waarde is DurableFunctionsHub. U kunt dit ook instellen in [host. json](durable-functions-bindings.md#host-json) met behulp van DurableTask: HubName.

> [!NOTE]
> In de kern Hulpprogramma's opdrachten wordt ervan uitgegaan dat u ze uitvoert vanuit de hoofdmap van een functie-app. Als u de `connection-string-setting` para meters `task-hub-name` en expliciet opgeeft, kunt u de opdrachten vanuit elke directory uitvoeren. Hoewel u deze opdrachten kunt uitvoeren zonder dat er een functie-app wordt uitgevoerd, is het mogelijk dat u niet kunt zien wat sommige gevolgen hebben, tenzij de host wordt uitgevoerd. De `start-new` opdracht in bijvoorbeeld een start bericht naar de doel taak hub, maar de indeling wordt niet daad werkelijk uitgevoerd, tenzij er een functie-app-hostproces wordt uitgevoerd die het bericht kan verwerken.

Met de volgende opdracht wordt de functie HelloWorld gestart en wordt de inhoud van het bestand `counter-data.json` door gegeven:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Query-exemplaren

Als onderdeel van uw inspanningen om uw orchestrations te beheren, moet u waarschijnlijk informatie verzamelen over de status van een Orchestration-exemplaar (bijvoorbeeld of het normaal of mislukt is voltooid).

De methode [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) voor de klasse [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) of de `getStatus` methode in de `DurableOrchestrationClient` klasse (Java script) voert een query uit op de status van een Orchestration-exemplaar.

Het duurt een `instanceId` (vereist `showHistory` ), ( `showHistoryOutput` optioneel), (optioneel) en `showInput` (optioneel, alleen .net) als para meters.

* **`showHistory`** : Als deze is `true`ingesteld op, bevat het antwoord de uitvoerings geschiedenis.
* **`showHistoryOutput`** : Als deze is `true`ingesteld op, bevat de uitvoerings geschiedenis activiteiten uitvoer.
* **`showInput`** : Als deze optie `false`is ingesteld op, bevat het antwoord niet de invoer van de functie. De standaardwaarde is `true`. (Alleen .NET)

De methode retourneert een JSON-object met de volgende eigenschappen:

* **Naam**: De naam van de Orchestrator-functie.
* **InstanceId**: De exemplaar-id van de indeling (moet hetzelfde zijn als de `instanceId` invoer).
* **CreatedTime**: Het tijdstip waarop de Orchestrator-functie is gestart.
* **LastUpdatedTime**: Het tijdstip waarop de indeling voor het laatst door een controle punt is ingesteld.
* **Invoer**: De invoer van de functie als een JSON-waarde. Dit veld wordt niet ingevuld als `showInput` is ingesteld op false.
* **CustomStatus**: Aangepaste Orchestration-status in JSON-indeling.
* **Uitvoer**: De uitvoer van de functie als een JSON-waarde (als de functie is voltooid). Als de Orchestrator-functie is mislukt, bevat deze eigenschap de fout Details. Als de Orchestrator-functie is beëindigd, bevat deze eigenschap de reden voor de beëindiging (indien van toepassing).
* **RuntimeStatus**: Een van de volgende waarden:
  * **In behandeling**: Het exemplaar is gepland, maar is nog niet gestart.
  * **Uitvoeren**: De uitvoering van het exemplaar is gestart.
  * **Voltooid**: Het exemplaar is normaal voltooid.
  * **ContinuedAsNew**: Het exemplaar is opnieuw opgestart met een nieuwe geschiedenis. Dit is een tijdelijke status.
  * **Mislukt**: Het exemplaar is mislukt met een fout.
  * **Beëindigd**: Het exemplaar is abrupt gestopt.
* **Geschiedenis**: De uitvoerings geschiedenis van de indeling. Dit veld wordt alleen ingevuld als `showHistory` deze is ingesteld op. `true`

Met deze methode `null` wordt geretourneerd of het exemplaar niet bestaat of nog niet is gestart.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Het is ook mogelijk om de status van een indelings exemplaar rechtstreeks op te halen met behulp van de [Azure functions core tools](../functions-run-local.md) `durable get-runtime-status` opdracht. Hierbij worden de volgende para meters gebruikt:

* (vereist): **`id`** ID van het Orchestration-exemplaar.
* (optioneel): **`show-input`** Als deze optie `true`is ingesteld op, bevat het antwoord de invoer van de functie. De standaardwaarde is `false`.
* (optioneel): **`show-output`** Als deze optie `true`is ingesteld op, bevat het antwoord de uitvoer van de functie. De standaardwaarde is `false`.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Met de volgende opdracht wordt de status (inclusief invoer en uitvoer) opgehaald van een exemplaar met een indelings exemplaar-ID van 0ab8c55a66644d68a3a8b220b12d209c. Hierbij wordt ervan uitgegaan dat u de `func` opdracht uitvoert vanuit de hoofdmap van de functie-app:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

U kunt de `durable get-history` opdracht gebruiken om de geschiedenis van een Orchestration-exemplaar op te halen. Hierbij worden de volgende para meters gebruikt:

* (vereist): **`id`** ID van het Orchestration-exemplaar.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in host. json worden ingesteld met behulp van durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Query's uitvoeren op alle instanties

In plaats van één exemplaar tegelijk in uw indeling te gebruiken, is het wellicht efficiënter om in één keer op alle query's te zoeken.

U kunt de `GetStatusAsync` (.net) of `getStatusAll` (Java script)-methode gebruiken om de status van alle indelings instanties op te vragen. In .net kunt u een `CancellationToken` object door geven als u dit wilt annuleren. De methode retourneert objecten met dezelfde eigenschappen als de `GetStatusAsync` methode met para meters.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Het is ook mogelijk rechtstreeks een query uit te zoeken op instanties met behulp van de [Azure functions core tools](../functions-run-local.md) `durable get-instances` opdracht. Hierbij worden de volgende para meters gebruikt:

* (optioneel): **`top`** Met deze opdracht wordt paginering ondersteund. Deze para meter komt overeen met het aantal exemplaren dat per aanvraag wordt opgehaald. De standaard waarde is 10.
* (optioneel): **`continuation-token`** Een token om aan te geven welke pagina of sectie met instanties moet worden opgehaald. Elke `get-instances` uitvoering retourneert een token naar de volgende set exemplaren.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Query's uitvoeren op exemplaren met filters

Wat gebeurt er als u niet alle informatie nodig hebt die een Standard-exemplaar query kan bieden? Wat als u bijvoorbeeld alleen zoekt naar de Orchestration-aanmaak tijd of de Orchestration runtime-status? U kunt uw query beperken door filters toe te passen.

Gebruik de `GetStatusAsync` (.net) of `getStatusBy` (Java script)-methode om een lijst op te halen met indelings instanties die overeenkomen met een set vooraf gedefinieerde filters.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

In de Azure functions core tools kunt u ook de `durable get-instances` opdracht met filters gebruiken. `top`Naast de voor noemde `continuation-token``created-after` `runtime-status` `created-before`,, `task-hub-name` , en-para meters kunt u drie filter parameters (, en) gebruiken. `connection-string-setting`

* (optioneel): **`created-after`** De instanties ophalen die zijn gemaakt na deze datum/tijd (UTC). ISO 8601 ingedeelde datum/tijd geaccepteerd.
* (optioneel): **`created-before`** De instanties ophalen die zijn gemaakt voor deze datum/tijd (UTC). ISO 8601 ingedeelde datum/tijd geaccepteerd.
* (optioneel): **`runtime-status`** Haal de instanties met een bepaalde status op (bijvoorbeeld uitgevoerd of voltooid). Kan meerdere (spaties gescheiden) statussen bieden.
* (optioneel): **`top`** Aantal opgehaalde exemplaren per aanvraag. De standaard waarde is 10.
* (optioneel): **`continuation-token`** Een token om aan te geven welke pagina of sectie met instanties moet worden opgehaald. Elke `get-instances` uitvoering retourneert een token naar de volgende set exemplaren.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Als u geen filters`created-after`(, `created-before`, of `runtime-status`) opgeeft, wordt met de opdracht simpelweg `top` instanties opgehaald, zonder rekening te houden met de runtime status of de aanmaak tijd.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Exemplaren beëindigen

Als u een indelings instantie hebt die te lang duurt om uit te voeren, of als u deze alleen wilt stoppen voordat deze is voltooid, hebt u de mogelijkheid om deze te beëindigen.

U kunt de methode [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) van de klasse [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) of de `terminate` methode van de `DurableOrchestrationClient` klasse (Java script) gebruiken. De twee para meters `instanceId` zijn een `reason` en een teken reeks, die worden geschreven naar Logboeken en de status van het exemplaar. Een beëindigd exemplaar stopt zodra het `await` volgende (.net) of `yield` (Java script)-punt wordt bereikt, of het wordt onmiddellijk beëindigd als het al op een `await` of `yield`is.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Het beëindigen van het exemplaar wordt momenteel niet door gegeven. Activiteit functies en subcontains worden uitgevoerd om te worden voltooid, ongeacht of u het Orchestrator-exemplaar dat deze heeft aangeroepen, hebt beëindigd.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt een Orchestration-exemplaar ook rechtstreeks beëindigen met behulp van de [Azure functions core tools](../functions-run-local.md) `durable terminate` opdracht. Hierbij worden de volgende para meters gebruikt:

* (vereist): **`id`** ID van het te beëindigen Orchestrator-exemplaar.
* (optioneel): **`reason`** Reden voor beëindiging.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Met de volgende opdracht wordt een Orchestrator-exemplaar met de ID 0ab8c55a66644d68a3a8b220b12d209c beëindigd:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Gebeurtenissen naar instanties verzenden

In sommige gevallen is het belang rijk dat uw Orchestrator-functies kunnen wachten op externe gebeurtenissen en er kan worden geluisterd. Dit omvat [controle functies](durable-functions-concepts.md#monitoring) en-functies die wachten op [menselijke interactie](durable-functions-concepts.md#human).

Verzend gebeurtenis meldingen naar actieve instanties met behulp van de methode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) van de klasse [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) `raiseEvent` of de methode `DurableOrchestrationClient` van de klasse (Java script). Exemplaren die deze gebeurtenissen kunnen afhandelen, zijn de instanties die wachten op [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) het aanroepen van WaitForExternalEvent `waitForExternalEvent` (.net) of (Java script).

De para meters voor [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.net `raiseEvent` ) en (Java script) zijn als volgt:

* **InstanceId**: De unieke ID van het exemplaar.
* **Eventname**: De naam van de gebeurtenis die moet worden verzonden.
* **Event Data**: Een JSON-serialiseerbare Payload die naar het exemplaar moet worden verzonden.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Als er geen Orchestrator-exemplaar met de opgegeven exemplaar-ID is, of als het exemplaar niet op de opgegeven gebeurtenis naam wacht, wordt het gebeurtenis bericht verwijderd. Zie het [github-probleem](https://github.com/Azure/azure-functions-durable-extension/issues/29)voor meer informatie over dit gedrag.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt ook rechtstreeks een gebeurtenis naar een Orchestration-instantie verhogen met behulp van de [Azure functions core tools](../functions-run-local.md) `durable raise-event` opdracht. Hierbij worden de volgende para meters gebruikt:

* (vereist): **`id`** ID van het Orchestration-exemplaar.
* (optioneel): **`event-name`** De naam van de gebeurtenis die moet worden verhoogd. De standaardwaarde is `$"Event_{RandomGUID}"`.
* (optioneel): **`event-data`** Gegevens die naar de indelings instantie moeten worden verzonden. Dit kan het pad naar een JSON-bestand zijn, of u kunt de gegevens rechtstreeks op de opdracht regel opgeven.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Wachten op de Orchestration-voltooiing

In langlopende indelingen wilt u mogelijk wachten en de resultaten van een indeling ophalen. In dergelijke gevallen is het ook handig om een time-outperiode in te stellen voor de indeling. Als de time-out wordt overschreden, moet de status van de indeling worden geretourneerd in plaats van de resultaten.

De klasse [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) toont een [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) -API in .net. U kunt deze API gebruiken om de daad werkelijke uitvoer van een Orchestration-exemplaar synchroon op te halen. In Java script maakt `DurableOrchestrationClient` de klasse een `waitForCompletionOrCreateCheckStatusResponse` API voor hetzelfde doel. Als ze niet zijn ingesteld, gebruiken de methoden een standaard waarde van 10 seconden voor `timeout`en 1 seconde voor. `retryInterval`  

Hier volgt een voor beeld van een HTTP-activerings functie die laat zien hoe u deze API gebruikt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Roep de functie aan met de volgende regel. Gebruik 2 seconden voor de time-out en 0,5 seconden voor het interval voor nieuwe pogingen:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Afhankelijk van de tijd die nodig is om de reactie van de Orchestration-instantie op te halen, zijn er twee situaties:

* De indelings instanties worden binnen de gedefinieerde time-out (in dit geval 2 seconden) voltooid en het antwoord is de daad werkelijke indelings uitvoer van het exemplaar dat synchroon wordt bezorgd:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* De indelings instanties kunnen niet binnen de ingestelde time-out worden voltooid en het antwoord is de standaard die wordt beschreven in [http API-URL-detectie](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> De indeling van de webhook-Url's kan verschillen, afhankelijk van de versie van de Azure Functions host die u uitvoert. Het vorige voor beeld is voor de host Azure Functions 2. x.

## <a name="retrieve-http-management-webhook-urls"></a>Url's voor HTTP Management webhook ophalen

U kunt een extern systeem gebruiken om gebeurtenissen in een indeling te controleren of te verhogen. Externe systemen kunnen communiceren met Durable Functions via de webhook-Url's die deel uitmaken van het standaard antwoord dat wordt beschreven in [http API-URL-detectie](durable-functions-http-api.md). De webhook-Url's zijn echter ook programmatisch toegankelijk in de Orchestration-client of in een activiteit functie. Doe dit met behulp van de methode [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) van de klasse [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) of `createHttpManagementPayload` de methode van `DurableOrchestrationClient` de klasse (Java script).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) en `createHttpManagementPayload` hebben één para meter:

* **instanceId**: De unieke ID van het exemplaar.

De methoden retour neren een exemplaar van [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.net) of een object (Java script) met de volgende teken reeks eigenschappen:

* **Id**: De exemplaar-id van de indeling (moet hetzelfde zijn als de `InstanceId` invoer).
* **StatusQueryGetUri**: De status-URL van het Orchestrator-exemplaar.
* **SendEventPostUri**: De URL van de functie ' raise ' van het Orchestrator-exemplaar.
* **TerminatePostUri**: De ' Terminate ' URL van het Orchestrator-exemplaar.
* **RewindPostUri**: De ' rewinde ' URL van het Orchestrator-exemplaar.

Activiteit functies kunnen een exemplaar van deze objecten naar externe systemen verzenden om gebeurtenissen te controleren of te verhogen naar een indeling:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>Instanties terugspoelen (preview-versie)

Als u een indelings fout hebt om een onverwachte reden, kunt u het exemplaar *terugspoelen* naar een eerder gezonde status door gebruik te maken van een API die hiervoor is gemaakt.

> [!NOTE]
> Deze API is niet bedoeld als vervanging voor het correct afhandelen van fouten en het beleid voor opnieuw proberen. In plaats daarvan is het alleen bedoeld om te worden gebruikt in gevallen waarin indelings instanties om onverwachte redenen mislukken. Zie het onderwerp [fout afhandeling](durable-functions-error-handling.md) voor meer informatie over het verwerken van fouten en het opnieuw proberen van het beleid.

Gebruik de API van [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.net `rewindAsync` ) of (Java script) om de indeling weer in te stellen op de status *wordt uitgevoerd* . Voer de activiteit of de uitvoerings fouten van de suborchestrator die de Orchestration-fout hebben veroorzaakt, opnieuw uit.

Stel bijvoorbeeld dat u een werk stroom hebt met een reeks [Human goed keuringen](durable-functions-concepts.md#human). Stel dat er een reeks activiteit functies zijn die iemand verwittigen dat hun goed keuring nodig is, en wacht de realtime respons. Wanneer alle goedkeurings activiteiten antwoorden hebben ontvangen of een time-out hebben, wordt ervan uitgegaan dat een andere activiteit mislukt als gevolg van een onjuiste configuratie van een toepassing, zoals een ongeldige data base connection string. Het resultaat is een indelings fout diep in de werk stroom. Met de `RewindAsync` API (.net) `rewindAsync` of (Java script) kan een toepassings beheerder de configuratie fout herstellen en de mislukte indeling terugspoelen naar de status direct vóór de fout. Geen van de stappen voor menselijke interactie moet opnieuw worden goedgekeurd en de indeling kan nu worden voltooid.

> [!NOTE]
> De functie *rewenteling* biedt geen ondersteuning voor het terugspoelen van indelings instanties die gebruikmaken van duurzame timers.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt een Orchestration-exemplaar ook rechtstreeks terugspoelen met behulp van de [Azure functions core tools](../functions-run-local.md) `durable rewind` opdracht. Hierbij worden de volgende para meters gebruikt:

* (vereist): **`id`** ID van het Orchestration-exemplaar.
* (optioneel): **`reason`** De reden voor het terugspoelen van het Orchestration-exemplaar.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Instantie geschiedenis opschonen

Als u alle gegevens wilt verwijderen die zijn gekoppeld aan een indeling, kunt u de instantie geschiedenis wissen. Zo wilt u misschien Azure Table-rijen en grote bericht-blobs verwijderen, indien aanwezig. Als u dit wilt doen, gebruikt u de [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) -API.

> [!NOTE]
> De `PurgeInstanceHistoryAsync` API is momenteel alleen beschikbaar voor C#.

 De methode heeft twee Overloads. De eerste keer dat de geschiedenis wordt gewist met de ID van het Orchestrator-exemplaar:

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

In het tweede voor beeld ziet u een door een timer geactiveerde functie die de geschiedenis van alle Orchestration-instanties die zijn voltooid na het opgegeven tijds interval verwijdert. In dit geval worden gegevens verwijderd voor alle exemplaren die 30 of meer dagen geleden zijn voltooid. Het is gepland om één keer per dag uit te voeren, om 12 uur:

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> De runtime status moet worden **voltooid**, **beëindigd**of **mislukt**als het functie proces met tijd activering slaagt.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt de geschiedenis van een Orchestrator-exemplaar opschonen met behulp van de [Azure functions core tools](../functions-run-local.md) `durable purge-history` opdracht. Net als bij het C# tweede voor beeld in de voor gaande sectie, wordt de geschiedenis van alle Orchestration-instanties die tijdens een opgegeven tijds interval zijn gemaakt, verwijderd. U kunt opschoonde instanties verder filteren op runtime status. De opdracht heeft verschillende para meters:

* (optioneel): **`created-after`** De geschiedenis van exemplaren die zijn gemaakt na deze datum/tijd (UTC) opschonen. ISO 8601 ingedeelde datum/tijd geaccepteerd.
* (optioneel): **`created-before`** De geschiedenis van exemplaren die zijn gemaakt voor deze datum/tijd (UTC) opschonen. ISO 8601 ingedeelde datum/tijd geaccepteerd.
* (optioneel): **`runtime-status`** De geschiedenis van instanties met een bepaalde status opschonen (bijvoorbeeld uitgevoerd of voltooid). Kan meerdere (spaties gescheiden) statussen bieden.
* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Met de volgende opdracht wordt de geschiedenis van alle mislukte instanties verwijderd die zijn gemaakt vóór 14 november 2018 om 7:35 uur (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Een task hub verwijderen

Met de [Azure functions core tools](../functions-run-local.md) `durable delete-task-hub` opdracht kunt u alle opslag artefacten verwijderen die zijn gekoppeld aan een bepaalde taak hub. Dit omvat Azure Storage-tabellen,-wacht rijen en-blobs. De opdracht heeft twee para meters:

* (optioneel): **`connection-string-setting`** De naam van de toepassings instelling die de opslag connection string bevat die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel): **`task-hub-name`** De naam van de Durable Functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Met de volgende opdracht worden alle Azure Storage-gegevens verwijderd `UserTest` die zijn gekoppeld aan de taak hub.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van de HTTP-Api's voor instantie beheer](durable-functions-http-api.md)