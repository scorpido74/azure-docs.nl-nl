---
title: Instanties in Durable Functions-Azure beheren
description: Meer informatie over het beheren van instanties in de Durable Functions-extensie voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: ab9cc9b093008730d175fa3fde4391f9de236a84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231382"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Instanties in Durable Functions in azure beheren

Als u de extensie [Durable functions](durable-functions-overview.md) gebruikt voor Azure functions of als u dit wilt doen, moet u ervoor zorgen dat u het beste kunt profiteren. U kunt uw Durable Functions Orchestration-instanties optimaliseren door te leren hoe u deze beheert. In dit artikel worden de details van elke bewerking van exemplaar beheer beschreven.

U kunt bijvoorbeeld exemplaren starten en beëindigen, en u kunt een query uitvoeren op instanties, met inbegrip van de mogelijkheid om query's uit te zoeken op alle exemplaren en query instanties met filters. Daarnaast kunt u gebeurtenissen naar instanties verzenden, wachten op de Orchestration-voltooiing en de HTTP Management-webhook-Url's ophalen. In dit artikel worden ook andere beheer bewerkingen behandeld, zoals het terugspoelen van instanties, het opschonen van de exemplaar geschiedenis en het verwijderen van een taak hub.

In Durable Functions kunt u kiezen hoe u elk van deze beheer bewerkingen wilt implementeren. In dit artikel vindt u voor beelden van de [Azure functions core tools](../functions-run-local.md) voor .netC#() en Java script.

## <a name="start-instances"></a>Instanties starten

Het is belang rijk om een instantie van indeling te kunnen starten. Dit wordt vaak gedaan wanneer u een Durable Functions binding in de trigger van een andere functie gebruikt.

Met de methode `StartNewAsync` (.NET) of `startNew` (Java script) op de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) wordt een nieuw exemplaar gestart. Intern in deze methode een bericht in de controle wachtrij, waarmee vervolgens het begin van een functie wordt geactiveerd met de opgegeven naam die gebruikmaakt van de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger).

Deze asynchrone bewerking is voltooid wanneer het Orchestration-proces is gepland.

De para meters voor het starten van een nieuwe Orchestration-instantie zijn als volgt:

* **Naam**: de naam van de Orchestrator-functie die u wilt plannen.
* **Invoer**: alle JSON-serialiseerbare gegevens die moeten worden door gegeven als invoer voor de Orchestrator-functie.
* **InstanceId**: (optioneel) de unieke id van het exemplaar. Als u deze para meter niet opgeeft, gebruikt de methode een wille keurige ID.

> [!TIP]
> Gebruik een wille keurige id voor de exemplaar-ID. Wille keurige-exemplaar-Id's helpen een gelijke belasting verdeling te garanderen wanneer u Orchestrator-functies op meerdere Vm's wilt schalen. De juiste tijd voor het gebruik van niet-wille keurig exemplaar-Id's is wanneer de ID afkomstig moet zijn van een externe bron of wanneer u het [Singleton-Orchestrator](durable-functions-singletons.md) -patroon implementeert.

De volgende code is een voor beeld van een functie waarmee een nieuwe Orchestration-instantie wordt gestart:

### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript"></a>JavaScript

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt een instantie ook rechtstreeks starten met behulp van de [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` opdracht. Hierbij worden de volgende para meters gebruikt:

* **`function-name` (vereist)** : de naam van de functie die moet worden gestart.
* **`input` (optioneel)** : de invoer voor de functie, hetzij inline of via een JSON-bestand. Voor bestanden voegt u een voor voegsel toe aan het pad naar het bestand met `@`, zoals `@path/to/file.json`.
* **`id` (optioneel)** : id van het Orchestration-exemplaar. Als u deze para meter niet opgeeft, gebruikt de opdracht een wille keurige GUID.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaard waarde is AzureWebJobsStorage.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De standaard waarde is DurableFunctionsHub. U kunt dit ook instellen in [host. json](durable-functions-bindings.md#host-json) met behulp van DurableTask: HubName.

> [!NOTE]
> In de kern Hulpprogramma's opdrachten wordt ervan uitgegaan dat u ze uitvoert vanuit de hoofdmap van een functie-app. Als u de para meters `connection-string-setting` en `task-hub-name` expliciet opgeeft, kunt u de opdrachten uitvoeren vanuit een wille keurige Directory. Hoewel u deze opdrachten kunt uitvoeren zonder dat er een functie-app wordt uitgevoerd, is het mogelijk dat u niet kunt zien wat sommige gevolgen hebben, tenzij de host wordt uitgevoerd. Met de `start-new` opdracht in bijvoorbeeld een start bericht in de doel taak hub, maar de indeling wordt niet daad werkelijk uitgevoerd, tenzij er een functie-app-hostproces wordt uitgevoerd die het bericht kan verwerken.

Met de volgende opdracht wordt de functie HelloWorld gestart en wordt de inhoud van het bestand `counter-data.json` hierop door gegeven:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Query-exemplaren

Als onderdeel van uw inspanningen om uw orchestrations te beheren, moet u waarschijnlijk informatie verzamelen over de status van een Orchestration-exemplaar (bijvoorbeeld of het normaal of mislukt is voltooid).

De `GetStatusAsync` (.NET) of de methode `getStatus` (Java script) op de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) voert een query uit op de status van een Orchestration-exemplaar.

Hiervoor worden een `instanceId` (vereist), `showHistory` (optioneel), `showHistoryOutput` (optioneel) en `showInput` (optioneel) als para meters nodig.

* **`showHistory`** : als deze is ingesteld op `true`, bevat het antwoord de uitvoerings geschiedenis.
* **`showHistoryOutput`** : als deze is ingesteld op `true`, bevat de uitvoerings geschiedenis activiteiten uitvoer.
* **`showInput`** : als deze optie is ingesteld op `false`, bevat het antwoord niet de invoer van de functie. De standaard waarde is `true`.

De methode retourneert een object met de volgende eigenschappen:

* **Naam**: de naam van de Orchestrator-functie.
* **InstanceId**: de exemplaar-id van de indeling (moet hetzelfde zijn als de `instanceId` invoer).
* **CreatedTime**: het tijdstip waarop de Orchestrator-functie is gestart.
* **LastUpdatedTime**: het tijdstip waarop de Orchestrator het laatst van een controle punt heeft.
* **Invoer**: de invoer van de functie als een JSON-waarde. Als `showInput` onwaar is, wordt dit veld niet ingevuld.
* **CustomStatus**: een aangepaste indelings status in JSON-notatie.
* **Output**: de uitvoer van de functie als een JSON-waarde (als de functie is voltooid). Als de Orchestrator-functie is mislukt, bevat deze eigenschap de fout Details. Als de Orchestrator-functie is beëindigd, bevat deze eigenschap de reden voor de beëindiging (indien van toepassing).
* **RuntimeStatus**: een van de volgende waarden:
  * **In behandeling**: het exemplaar is gepland, maar is nog niet gestart.
  * **Wordt uitgevoerd**: het exemplaar is gestart.
  * **Voltooid**: het exemplaar is normaal voltooid.
  * **ContinuedAsNew**: het exemplaar is opnieuw opgestart met een nieuwe geschiedenis. Deze status is een tijdelijke status.
  * **Mislukt**: het exemplaar is mislukt met een fout.
  * **Beëindigd**: het exemplaar is abrupt gestopt.
* **Geschiedenis**: de uitvoerings geschiedenis van de indeling. Dit veld wordt alleen ingevuld als `showHistory` is ingesteld op `true`.

Deze methode retourneert `null` (.NET) of `undefined` (Java script) als het exemplaar niet bestaat.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

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

Het is ook mogelijk om de status van een Orchestration-exemplaar rechtstreeks op te halen met behulp van de [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` opdracht. Hierbij worden de volgende para meters gebruikt:

* **`id` (vereist)** : id van het Orchestration-exemplaar.
* **`show-input` (optioneel)** : als deze is ingesteld op `true`, bevat het antwoord de invoer van de functie. De standaard waarde is `false`.
* **`show-output` (optioneel)** : als deze is ingesteld op `true`, bevat het antwoord de uitvoer van de functie. De standaard waarde is `false`.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Met de volgende opdracht wordt de status (inclusief invoer en uitvoer) opgehaald van een exemplaar met een indelings exemplaar-ID van 0ab8c55a66644d68a3a8b220b12d209c. Hierbij wordt ervan uitgegaan dat u de `func` opdracht uitvoert vanuit de hoofdmap van de functie-app:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

U kunt de opdracht `durable get-history` gebruiken om de geschiedenis van een Orchestration-exemplaar op te halen. Hierbij worden de volgende para meters gebruikt:

* **`id` (vereist)** : id van het Orchestration-exemplaar.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in host. json worden ingesteld met behulp van durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Query's uitvoeren op alle instanties

In plaats van één exemplaar tegelijk in uw indeling te gebruiken, is het wellicht efficiënter om in één keer op alle query's te zoeken.

U kunt de methode `GetStatusAsync` (.NET) of `getStatusAll` (Java script) gebruiken om de status van alle indelings instanties op te vragen. In .NET kunt u een `CancellationToken`-object door geven als u dit wilt annuleren. De methode retourneert objecten met dezelfde eigenschappen als de `GetStatusAsync` methode met para meters.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

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

Het is ook mogelijk rechtstreeks een query uit te zoeken op instanties met behulp van de [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` opdracht. Hierbij worden de volgende para meters gebruikt:

* **`top` (optioneel)** : deze opdracht ondersteunt paginering. Deze para meter komt overeen met het aantal exemplaren dat per aanvraag wordt opgehaald. De standaard waarde is 10.
* **`continuation-token` (optioneel)** : een token om aan te geven welke pagina of sectie met instanties moet worden opgehaald. Elke `get-instances`-uitvoering retourneert een token naar de volgende set exemplaren.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Query's uitvoeren op exemplaren met filters

Wat gebeurt er als u niet alle informatie nodig hebt die een Standard-exemplaar query kan bieden? Wat als u bijvoorbeeld alleen zoekt naar de Orchestration-aanmaak tijd of de Orchestration runtime-status? U kunt uw query beperken door filters toe te passen.

Gebruik de methode `GetStatusAsync` (.NET) of `getStatusBy` (Java script) om een lijst met indelings instanties op te halen die overeenkomen met een set vooraf gedefinieerde filters.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

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

In de Azure Functions Core Tools kunt u ook de `durable get-instances` opdracht gebruiken met filters. Naast de voor noemde `top`, `continuation-token`, `connection-string-setting`en `task-hub-name` para meters, kunt u drie filter parameters (`created-after`, `created-before`en `runtime-status`) gebruiken.

* **`created-after` (optioneel)** : de instanties ophalen die zijn gemaakt na deze datum/tijd (UTC). ISO 8601 ingedeelde datum/tijd geaccepteerd.
* **`created-before` (optioneel)** : de instanties ophalen die zijn gemaakt voor deze datum/tijd (UTC). ISO 8601 ingedeelde datum/tijd geaccepteerd.
* **`runtime-status` (optioneel)** : de instanties met een bepaalde status ophalen (bijvoorbeeld uitgevoerd of voltooid). Kan meerdere (spaties gescheiden) statussen bieden.
* **`top` (optioneel)** : aantal opgehaalde exemplaren per aanvraag. De standaard waarde is 10.
* **`continuation-token` (optioneel)** : een token om aan te geven welke pagina of sectie met instanties moet worden opgehaald. Elke `get-instances`-uitvoering retourneert een token naar de volgende set exemplaren.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Als u geen filters (`created-after`, `created-before`of `runtime-status`) opgeeft, haalt de opdracht simpelweg `top` instanties op, zonder rekening te houden met de runtime status of de aanmaak tijd.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Exemplaren beëindigen

Als u een indelings instantie hebt die te lang duurt om uit te voeren, of als u deze alleen wilt stoppen voordat deze is voltooid, hebt u de mogelijkheid om deze te beëindigen.

U kunt de `TerminateAsync` (.NET) of de methode `terminate` (Java script) van de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) gebruiken om instanties te beëindigen. De twee para meters zijn een `instanceId` en een `reason` teken reeks, die worden geschreven naar Logboeken en de status van het exemplaar. Een beëindigde instantie stopt zodra het volgende `await` (.NET) of `yield` (Java script)-punt wordt bereikt, of wordt onmiddellijk beëindigd als het al een `await` of `yield`is.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

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

U kunt een Orchestration-exemplaar ook rechtstreeks beëindigen met behulp van de [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` opdracht. Hierbij worden de volgende para meters gebruikt:

* **`id` (vereist)** : id van het te beëindigen Orchestrator-exemplaar.
* **`reason` (optioneel)** : reden voor beëindiging.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

Met de volgende opdracht wordt een Orchestrator-exemplaar met de ID 0ab8c55a66644d68a3a8b220b12d209c beëindigd:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Gebeurtenissen naar instanties verzenden

In sommige gevallen is het belang rijk dat uw Orchestrator-functies kunnen wachten op externe gebeurtenissen en er kan worden geluisterd. Dit omvat [controle functies](durable-functions-overview.md#monitoring) en-functies die wachten op [menselijke interactie](durable-functions-overview.md#human).

Verzend gebeurtenis meldingen naar actieve instanties met behulp van de methode `RaiseEventAsync` (.NET) of de methode `raiseEvent` (Java script) van de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client). Instanties die deze gebeurtenissen kunnen afhandelen, zijn degenen die wachten op het aanroepen van `WaitForExternalEvent` (.NET) of het leveren van een `waitForExternalEvent` (Java script)-aanroep.

De para meters voor `RaiseEventAsync` (.NET) en `raiseEvent` (Java script) zijn als volgt:

* **InstanceId**: de unieke id van het exemplaar.
* **Eventname**: de naam van de gebeurtenis die moet worden verzonden.
* **Event Data**: een JSON-serialiseerbare Payload die naar het exemplaar moet worden verzonden.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!NOTE]
> Als er geen Orchestrator-exemplaar is met de opgegeven exemplaar-ID, wordt het gebeurtenis bericht verwijderd. Als er een instantie bestaat, maar deze nog niet wacht op gebeurtenis, wordt de gebeurtenis opgeslagen in de status van het exemplaar totdat deze klaar is om te worden ontvangen en verwerkt.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt ook rechtstreeks een gebeurtenis naar een Orchestration-instantie verhogen met behulp van de [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` opdracht. Hierbij worden de volgende para meters gebruikt:

* **`id` (vereist)** : id van het Orchestration-exemplaar.
* **`event-name`** : de naam van de gebeurtenis die moet worden verhoogd.
* **`event-data` (optioneel)** : gegevens die naar het Orchestration-exemplaar moeten worden verzonden. Dit kan het pad naar een JSON-bestand zijn, of u kunt de gegevens rechtstreeks op de opdracht regel opgeven.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De standaardwaarde is `DurableFunctionsHub`. Het kan ook in [host. json](durable-functions-bindings.md#host-json)worden ingesteld met behulp van DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Wachten op de Orchestration-voltooiing

In langlopende indelingen wilt u mogelijk wachten en de resultaten van een indeling ophalen. In dergelijke gevallen is het ook handig om een time-outperiode in te stellen voor de indeling. Als de time-out wordt overschreden, moet de status van de indeling worden geretourneerd in plaats van de resultaten.

De `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) of de methode `waitForCompletionOrCreateCheckStatusResponse` (Java script) kan worden gebruikt om de werkelijke uitvoer van een Orchestration-exemplaar synchroon op te halen. Standaard gebruiken deze methoden een standaard waarde van 10 seconden voor `timeout`en 1 seconde voor `retryInterval`.  

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
        Date: Thu, 14 Dec 2018 06:14:29 GMT
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
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> De indeling van de webhook-Url's kan verschillen, afhankelijk van de versie van de Azure Functions host die u uitvoert. Het vorige voor beeld is voor de Azure Functions 2,0-host.

## <a name="retrieve-http-management-webhook-urls"></a>Url's voor HTTP Management webhook ophalen

U kunt een extern systeem gebruiken om gebeurtenissen te controleren of te activeren voor een indeling. Externe systemen kunnen communiceren met Durable Functions via de webhook-Url's die deel uitmaken van het standaard antwoord dat wordt beschreven in [http API-URL-detectie](durable-functions-http-features.md#http-api-url-discovery). De webhook-Url's kunnen met behulp van de [Orchestration client binding](durable-functions-bindings.md#orchestration-client)ook programmatisch worden benaderd. De methoden `CreateHttpManagementPayload` (.NET) of de `createHttpManagementPayload` (Java script) kunnen worden gebruikt voor het ophalen van een serialiseerbaar object dat deze Url's voor webhooks bevat.

De methoden `CreateHttpManagementPayload` (.NET) en `createHttpManagementPayload` (Java script) hebben één para meter:

* **instanceId**: de unieke id van het exemplaar.

De methoden retour neren een object met de volgende teken reeks eigenschappen:

* **Id**: de exemplaar-id van de indeling (moet hetzelfde zijn als de `InstanceId` invoer).
* **StatusQueryGetUri**: de status-URL van het Orchestrator-exemplaar.
* **SendEventPostUri**: de URL van de ' raise gebeurtenis ' van het Orchestrator-exemplaar.
* **TerminatePostUri**: de URL ' Terminate ' van het Orchestrator-exemplaar.
* **PurgeHistoryDeleteUri**: de URL voor het opschonen van de geschiedenis van het Orchestrator-exemplaar.

Functions kunnen instanties van deze objecten naar externe systemen verzenden om gebeurtenissen op de bijbehorende indelingen te controleren of te genereren, zoals wordt weer gegeven in de volgende voor beelden:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
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

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableActivityContext` gebruiken in plaats van `IDurableActivityContext`. u moet het kenmerk `OrchestrationClient` gebruiken in plaats van het kenmerk `DurableClient` en u moet het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

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
> Deze API is niet bedoeld als vervanging voor het correct afhandelen van fouten en het beleid voor opnieuw proberen. In plaats daarvan is het alleen bedoeld om te worden gebruikt in gevallen waarin indelings instanties om onverwachte redenen mislukken. Zie het artikel over [fout afhandeling](durable-functions-error-handling.md) voor meer informatie over het verwerken van fouten en het opnieuw proberen van beleid.

Gebruik de methode `RewindAsync` (.NET) of `rewind` (Java script) van de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) om de indeling weer in te stellen op de status *wordt uitgevoerd* . Met deze methode worden ook de uitvoerings fouten van de activiteit of de onderliggende toepassing opnieuw uitgevoerd die de Orchestration-fout hebben veroorzaakt.

Stel bijvoorbeeld dat u een werk stroom hebt met een reeks [Human goed keuringen](durable-functions-overview.md#human). Stel dat er een reeks activiteit functies zijn die iemand verwittigen dat hun goed keuring nodig is, en wacht de realtime respons. Wanneer alle goedkeurings activiteiten antwoorden hebben ontvangen of een time-out hebben, wordt ervan uitgegaan dat een andere activiteit mislukt als gevolg van een onjuiste configuratie van een toepassing, zoals een ongeldige data base connection string. Het resultaat is een indelings fout diep in de werk stroom. Met de API van `RewindAsync` (.NET) of `rewind` (Java script) kan een toepassings beheerder de configuratie fout herstellen en de mislukte indeling terugspoelen naar de status direct vóór de fout. Geen van de stappen voor menselijke interactie moet opnieuw worden goedgekeurd en de indeling kan nu worden voltooid.

> [!NOTE]
> De functie *rewenteling* biedt geen ondersteuning voor het terugspoelen van indelings instanties die gebruikmaken van duurzame timers.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

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

U kunt een Orchestration-exemplaar ook rechtstreeks terugspoelen met behulp van de [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` opdracht. Hierbij worden de volgende para meters gebruikt:

* **`id` (vereist)** : id van het Orchestration-exemplaar.
* **`reason` (optioneel)** : reden voor het terugspoelen van het Orchestration-exemplaar.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De naam van de taak-hub in het bestand [host. json](durable-functions-bindings.md#host-json) wordt standaard gebruikt.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Instantie geschiedenis opschonen

Als u alle gegevens wilt verwijderen die zijn gekoppeld aan een indeling, kunt u de instantie geschiedenis wissen. U kunt bijvoorbeeld alle Azure-tabel rijen en grote bericht-blobs verwijderen die zijn gekoppeld aan een voltooid exemplaar. Gebruik hiervoor de methode `PurgeInstanceHistoryAsync` (.NET) of `purgeInstanceHistory` (Java script) van de Orchestration- [client binding](durable-functions-bindings.md#orchestration-client).

Deze methode heeft twee Overloads. De eerste overbelaste geschiedenis wordt verwijderd door de ID van het Orchestration-exemplaar:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

In het volgende voor beeld ziet u een functie die door een timer wordt geactiveerd en die de geschiedenis van alle Orchestration-instanties die zijn voltooid na het opgegeven tijds interval, verwijdert. In dit geval worden gegevens verwijderd voor alle exemplaren die 30 of meer dagen geleden zijn voltooid. Het is gepland om één keer per dag uit te voeren, om 12 uur:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
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
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

**Java script** De methode `purgeInstanceHistoryBy` kan worden gebruikt om de exemplaar geschiedenis voor meerdere exemplaren voorwaardelijk op te schonen.

> [!NOTE]
> De runtime status van het doel exemplaar kan alleen worden **voltooid**, **beëindigd**of **mislukt**als de bewerking geschiedenis opschonen is geslaagd.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

U kunt de geschiedenis van een Orchestrator-exemplaar opschonen met behulp van de [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` opdracht. Net als bij het C# tweede voor beeld in de voor gaande sectie, wordt de geschiedenis van alle Orchestration-instanties die tijdens een opgegeven tijds interval zijn gemaakt, verwijderd. U kunt opschoonde instanties verder filteren op runtime status. De opdracht heeft verschillende para meters:

* **`created-after` (optioneel)** : Maak de geschiedenis van exemplaren die zijn gemaakt na deze datum/tijd (UTC). ISO 8601 ingedeelde datum/tijd geaccepteerd.
* **`created-before` (optioneel)** : Maak de geschiedenis van exemplaren die zijn gemaakt voor deze datum/tijd (UTC). ISO 8601 ingedeelde datum/tijd geaccepteerd.
* **`runtime-status` (optioneel)** : de geschiedenis van instanties met een bepaalde status opschonen (bijvoorbeeld uitgevoerd of voltooid). Kan meerdere (spaties gescheiden) statussen bieden.
* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De naam van de taak-hub in het bestand [host. json](durable-functions-bindings.md#host-json) wordt standaard gebruikt.

Met de volgende opdracht wordt de geschiedenis van alle mislukte instanties verwijderd die zijn gemaakt vóór 14 november 2018 om 7:35 uur (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Een task hub verwijderen

Met de [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` opdracht kunt u alle opslag artefacten verwijderen die zijn gekoppeld aan een bepaalde taak hub, waaronder Azure Storage-tabellen,-wacht rijen en-blobs. De opdracht heeft twee para meters:

* **`connection-string-setting` (optioneel)** : de naam van de toepassings instelling met de opslag Connection String die moet worden gebruikt. De standaardwaarde is `AzureWebJobsStorage`.
* **`task-hub-name` (optioneel)** : de naam van de Durable functions taak-hub die moet worden gebruikt. De naam van de taak-hub in het bestand [host. json](durable-functions-bindings.md#host-json) wordt standaard gebruikt.

Met de volgende opdracht worden alle Azure Storage-gegevens verwijderd die zijn gekoppeld aan de `UserTest`-taak hub.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het verwerken van versie beheer](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Ingebouwde HTTP API-referentie voor exemplaar beheer](durable-functions-http-api.md)