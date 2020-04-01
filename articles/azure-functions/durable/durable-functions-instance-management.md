---
title: Instances beheren in duurzame functies - Azure
description: Meer informatie over het beheren van instanties in de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476829"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Instanties beheren in Duurzame functies in Azure

Als u de extensie [Duurzame functies](durable-functions-overview.md) voor Azure-functies gebruikt of dit wilt gaan doen, moet u ervoor zorgen dat u er het beste gebruik van maakt. U uw instanties voor het orkestreren van duurzame functies optimaliseren door meer te weten te komen over het beheren ervan. Dit artikel gaat in op de details van elke instantie beheerbewerking.

U bijvoorbeeld instanties starten en beëindigen en u instanties opvragen, waaronder de mogelijkheid om alle instanties en query-instanties op te vragen met filters. Daarnaast u gebeurtenissen naar instanties verzenden, wachten op de voltooiing van de orkestratie en HTTP-beheer-webhook-URL's ophalen. Dit artikel heeft ook betrekking op andere beheerbewerkingen, waaronder het terugspoelen van instanties, het verwijderen van de instantiegeschiedenis en het verwijderen van een taakhub.

In Duurzame functies hebt u opties voor hoe u elk van deze beheerbewerkingen wilt implementeren. In dit artikel vindt u voorbeelden die gebruikmaken van de [Azure Functions Core Tools](../functions-run-local.md) voor zowel .NET (C#) als JavaScript.

## <a name="start-instances"></a>Instanties starten

Het is belangrijk om een geval van orkestratie te kunnen starten. Dit wordt vaak gedaan wanneer u een duurzame functies binding in de trigger van een andere functie gebruikt.

De `StartNewAsync` methode (.NET) of `startNew` (JavaScript) op de [orchestration clientbinding](durable-functions-bindings.md#orchestration-client) start een nieuwe instantie. Intern wordt met deze methode een bericht in de besturingselementwachtrij geplaatst, waardoor het begin van een functie wordt geactiveerd met de opgegeven naam die de [orchestration-triggerbinding gebruikt.](durable-functions-bindings.md#orchestration-trigger)

Deze synchronisatiebewerking wordt voltooid wanneer het orchestration-proces is gepland.

De parameters voor het starten van een nieuwe orchestration-instantie zijn als volgt:

* **Naam**: De naam van de orchestrator-functie die moet worden gepland.
* **Invoer**: ALLE JSON-serializable gegevens die moeten worden doorgegeven als de input voor de orchestrator-functie.
* **InstanceId**: (Optioneel) De unieke ID van de instantie. Als u deze parameter niet opgeeft, gebruikt de methode een willekeurige id.

> [!TIP]
> Gebruik een willekeurige id voor de instantie-id. Willekeurige instantie-id's zorgen voor een gelijke verdeling van de belasting wanneer u orchestrator-functies over meerdere VM's schaalt. De juiste tijd om niet-willekeurige instantie-id's te gebruiken, is wanneer de ID afkomstig moet zijn van een externe bron of wanneer u het [singleton orchestrator-patroon](durable-functions-singletons.md) implementeert.

De volgende code is een voorbeeldfunctie waarmee een nieuwe instantie voor orkestratie wordt gestart:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

<a name="javascript-function-json"></a>Tenzij anders aangegeven, gebruiken de voorbeelden op deze pagina de HTTP-trigger met de volgende functie.json.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> In dit voorbeeld wordt de functie duurzame functies van versie 2.x. Gebruik in `orchestrationClient` versie 1.x `durableClient`in plaats van .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

U een instantie ook rechtstreeks starten met de opdracht [Core Extra's voor](../functions-run-local.md) `durable start-new` Azure Functions. Er zijn de volgende parameters nodig:

* (vereist) : Naam van de functie om te starten. ** `function-name` **
* (optioneel) : Invoer naar de functie, inline of via een JSON-bestand. ** `input` ** Voor bestanden voegt u een voorvoegsel `@`toe aan `@path/to/file.json`het pad aan het bestand met , zoals .
* (facultatief) : ID van de instantie orchestration. ** `id` ** Als u deze parameter niet opgeeft, gebruikt de opdracht een willekeurige GUID.
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardinstelling is AzureWebJobsStorage.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** De standaardinstelling is DurableFunctionsHub. U dit ook instellen in [host.json](durable-functions-bindings.md#host-json) met behulp van duurzaamTask:HubName.

> [!NOTE]
> Opdrachten voor kernhulpprogramma's gaan ervan uit dat u ze uitvoert vanuit de hoofdmap van een functie-app. Als u de `connection-string-setting` parameters `task-hub-name` expliciet opgeeft, u de opdrachten vanuit elke map uitvoeren. Hoewel u deze opdrachten uitvoeren zonder dat een host van de functie-app wordt uitgevoerd, u merken dat u bepaalde effecten niet waarnemen, tenzij de host wordt uitgevoerd. De `start-new` opdracht maakt bijvoorbeeld een beginbericht in de doeltaakhub, maar de orkestratie wordt niet uitgevoerd, tenzij er een hostproces voor de functie-app wordt uitgevoerd dat het bericht kan verwerken.

Met de volgende opdracht wordt de functie HelloWorld `counter-data.json` gestart en wordt de inhoud van het bestand aan deze functie doorgegeven:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Query-instanties

Als onderdeel van uw inspanningen om uw orkestraties te beheren, zult u waarschijnlijk informatie moeten verzamelen over de status van een orchestration-instantie (bijvoorbeeld of deze normaal is voltooid of mislukt).

De `GetStatusAsync` methode (.NET) of de `getStatus` methode (JavaScript) op de [orchestration client binding](durable-functions-bindings.md#orchestration-client) query's de status van een orchestration instantie.

Het duurt `instanceId` een `showHistory` (verplicht), `showHistoryOutput` (optioneel), `showInput` (optioneel), en (optioneel) als parameters.

* **`showHistory`**: Als `true`ingesteld op , bevat het antwoord de uitvoeringsgeschiedenis.
* **`showHistoryOutput`**: Als `true`ingesteld op , bevat de uitvoeringsgeschiedenis activiteitsuitvoer.
* **`showInput`**: Als `false`ingesteld op , bevat het antwoord de invoer van de functie niet. De standaardwaarde is `true`.

De methode retourneert een object met de volgende eigenschappen:

* **Naam**: De naam van de orchestratorfunctie.
* **InstanceId**: De instantie-ID van de orchestration `instanceId` (moet hetzelfde zijn als de invoer).
* **CreatedTime**: Het tijdstip waarop de orchestratorfunctie begon te lopen.
* **LastUpdatedTime**: Het tijdstip waarop de orkestratie voor het laatst is gecontroleerd.
* **Invoer**: De invoer van de functie als JSON-waarde. Dit veld wordt niet `showInput` ingevuld als dit niet klopt.
* **CustomStatus:** Aangepaste orchestration-status in JSON-indeling.
* **Uitvoer**: De uitvoer van de functie als JSON-waarde (als de functie is voltooid). Als de orchestrator-functie is mislukt, bevat deze eigenschap de foutgegevens. Als de orchestrator-functie is beëindigd, bevat deze eigenschap de reden voor de beëindiging (indien van toepassing).
* **RuntimeStatus:** een van de volgende waarden:
  * **In behandeling**: De instantie is gepland, maar is nog niet gestart.
  * **Actief:** de instantie is gestart met hardlopen.
  * **Voltooid**: De instantie is normaal voltooid.
  * **VervolgAsNew**: De instantie heeft zichzelf opnieuw opgestart met een nieuwe geschiedenis. Deze toestand is een tijdelijke status.
  * **Mislukt:** de instantie is mislukt met een fout.
  * **Beëindigd**: De instantie werd abrupt gestopt.
* **Geschiedenis**: De uitvoeringsgeschiedenis van de orkestratie. Dit veld wordt alleen `showHistory` ingevuld `true`als dit is ingesteld op .

Deze methode `null` retourneert `undefined` (.NET) of (JavaScript) als de instantie niet bestaat.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

Het is ook mogelijk om de status van een orchestration-instantie rechtstreeks te krijgen, met behulp van de opdracht [Core Tools voor](../functions-run-local.md) `durable get-runtime-status` Azure Functions Core. Er zijn de volgende parameters nodig:

* (verplicht) : IDENTITEITSBEWIJS van de instantie orchestration. ** `id` **
* (facultatief) : `true`Indien ingesteld op , bevat het antwoord de input van de functie. ** `show-input` ** De standaardwaarde is `false`.
* (facultatief) : `true`Indien ingesteld op , bevat het antwoord de output van de functie. ** `show-output` ** De standaardwaarde is `false`.
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** De standaardwaarde is `DurableFunctionsHub`. Het kan ook worden ingesteld in [host.json,](durable-functions-bindings.md#host-json)met behulp van durableTask:HubName.

Met de volgende opdracht wordt de status (inclusief invoer en uitvoer) van een instantie met een orchestration instance ID van 0ab8c5a6644d68a3a8b220b12d209c opgehaald. Er wordt van uitgegaan `func` dat u de opdracht uitvoert vanuit de hoofdmap van de functie-app:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

U `durable get-history` de opdracht gebruiken om de geschiedenis van een orchestration-instantie op te halen. Er zijn de volgende parameters nodig:

* (verplicht) : IDENTITEITSBEWIJS van de instantie orchestration. ** `id` **
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** De standaardwaarde is `DurableFunctionsHub`. Het kan ook worden ingesteld in host.json, met behulp van durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Alle instanties opvragen

In plaats van één instantie tegelijk in uw orkestratie op te vragen, u het efficiënter vinden om ze allemaal tegelijk op te vragen.

U `GetStatusAsync` de methode (.NET) of `getStatusAll` (JavaScript) gebruiken om de statussen van alle orchestration-instanties op te vragen. In .NET u `CancellationToken` een object doorgeven voor het geval u het object wilt annuleren. De methode retourneert objecten `GetStatusAsync` met dezelfde eigenschappen als de methode met parameters.

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

Het is ook mogelijk om instanties rechtstreeks op te vragen met de opdracht [Core Tools voor](../functions-run-local.md) `durable get-instances` Azure Functions Core. Er zijn de volgende parameters nodig:

* (optioneel) : Deze opdracht ondersteunt paging. ** `top` ** Deze parameter komt overeen met het aantal exemplaren dat per aanvraag wordt opgehaald. De standaardinstelling is 10.
* (optioneel) : Een token om aan te geven welke pagina of sectie van instanties moet worden opgehaald. ** `continuation-token` ** Elke `get-instances` uitvoering retourneert een token naar de volgende set exemplaren.
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** De standaardwaarde is `DurableFunctionsHub`. Het kan ook worden ingesteld in [host.json,](durable-functions-bindings.md#host-json)met behulp van durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Query-exemplaren met filters

Wat als u niet echt alle informatie nodig hebt die een standaardinstantiequery kan bieden? Bijvoorbeeld, wat als je gewoon op zoek bent naar de orchestration creation tijd, of de orchestration runtime status? U uw query beperken door filters toe te passen.

Gebruik `GetStatusAsync` de methode (.NET) of `getStatusBy` (JavaScript) om een lijst met orchestration-exemplaren te krijgen die overeenkomen met een set vooraf gedefinieerde filters.

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

In de Azure Functions Core Tools `durable get-instances` kunt u de opdracht ook met filters gebruiken. Naast `top`de bovengenoemde `continuation-token` `connection-string-setting`, `task-hub-name` , , en parameters,`created-after`kunt `created-before`u `runtime-status`gebruik maken van drie filterparameters ( , , en ).

* (optioneel) : Haal de exemplaren op die na deze datum/tijd zijn gemaakt (UTC). ** `created-after` ** ISO 8601 opgemaakte datumtijden geaccepteerd.
* (optioneel) : Haal de exemplaren op die vóór deze datum/tijd zijn gemaakt (UTC). ** `created-before` ** ISO 8601 opgemaakte datumtijden geaccepteerd.
* (optioneel) : De instanties met een bepaalde status ophalen (bijvoorbeeld uitgevoerd of voltooid). ** `runtime-status` ** Kan meerdere (ruimtegescheiden) statussen bieden.
* (optioneel) : Aantal exemplaren dat per aanvraag wordt opgehaald. ** `top` ** De standaardinstelling is 10.
* (optioneel) : Een token om aan te geven welke pagina of sectie van instanties moet worden opgehaald. ** `continuation-token` ** Elke `get-instances` uitvoering retourneert een token naar de volgende set exemplaren.
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** De standaardwaarde is `DurableFunctionsHub`. Het kan ook worden ingesteld in [host.json,](durable-functions-bindings.md#host-json)met behulp van durableTask:HubName.

Als u geen filters (`created-after` `created-before`, `runtime-status`of ), de `top` opdracht gewoon ophaalt instanties, met geen rekening met runtime status of creatie tijd.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Instanties beëindigen

Als u een orchestration-instantie hebt die te lang duurt om uit te voeren, of als u deze gewoon hoeft te stoppen voordat deze om welke reden dan ook wordt voltooid, hebt u de optie om deze te beëindigen.

U `TerminateAsync` de (.NET) `terminate` of de (JavaScript)-methode van de [orchestration-clientbinding](durable-functions-bindings.md#orchestration-client) gebruiken om instanties te beëindigen. De twee parameters `instanceId` zijn `reason` een en een tekenreeks, die worden geschreven naar logboeken en naar de instantiestatus.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

Een beëindigd exemplaar zal `Terminated` uiteindelijk de overgang naar de status. Deze overgang zal echter niet onmiddellijk plaatsvinden. In plaats daarvan wordt de bewerking beëindigen in de taakhub geplaatst, samen met andere bewerkingen voor die instantie. U de [API's voor instantiequery's](#query-instances) gebruiken `Terminated` om te weten wanneer een beëindigd instantie daadwerkelijk de status heeft bereikt.

> [!NOTE]
> Instantiebeëindiging wordt momenteel niet doorgegeven. Activiteitsfuncties en suborchestrations worden uitgevoerd tot voltooiing, ongeacht of u de orchestration-instantie hebt beëindigd die ze heeft genoemd.

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

U een orchestration-instantie ook rechtstreeks beëindigen met de opdracht [Core Tools voor](../functions-run-local.md) `durable terminate` Azure Functions. Er zijn de volgende parameters nodig:

* (vereist) : ID van de te beëindigen orchestration instance. ** `id` **
* (facultatief) : Reden voor beëindiging. ** `reason` **
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** De standaardwaarde is `DurableFunctionsHub`. Het kan ook worden ingesteld in [host.json,](durable-functions-bindings.md#host-json)met behulp van durableTask:HubName.

Met de volgende opdracht wordt een orchestration-instantie beëindigd met een ID van 0ab8c55a6644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Gebeurtenissen naar instanties verzenden

In sommige scenario's is het belangrijk dat uw orchestrator-functies kunnen wachten en luisteren naar externe gebeurtenissen. Dit omvat [monitorfuncties](durable-functions-overview.md#monitoring) en functies die wachten op [menselijke interactie.](durable-functions-overview.md#human)

Stuur gebeurtenismeldingen naar uitvoerende `RaiseEventAsync` instanties met behulp `raiseEvent` van de methode (.NET) of de (JavaScript)-methode van de [orchestration-clientbinding](durable-functions-bindings.md#orchestration-client). Instanties die deze gebeurtenissen kunnen verwerken, zijn `WaitForExternalEvent` die welke wachten op `waitForExternalEvent` een oproep naar (.NET) of toegeven aan een (JavaScript)-gesprek.

De parameters `RaiseEventAsync` voor (.NET) en `raiseEvent` (JavaScript) zijn als volgt:

* **InstanceId:** de unieke ID van de instantie.
* **EventName:** de naam van de gebeurtenis die moet worden verzonden.
* **EventData:** een JSON-serializable payload te sturen naar de instantie.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

> [!NOTE]
> Als er geen orchestration-instantie is met de opgegeven instantie-id, wordt het gebeurtenisbericht verwijderd. Als er een instantie bestaat, maar deze nog niet wacht op de gebeurtenis, wordt de gebeurtenis opgeslagen in de instantiestatus totdat deze klaar is om te worden ontvangen en verwerkt.

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

U een gebeurtenis ook rechtstreeks naar een orchestration-instantie verhogen met de opdracht [Core Extra's](../functions-run-local.md) `durable raise-event` voor Azure Functions. Er zijn de volgende parameters nodig:

* (verplicht) : IDENTITEITSBEWIJS van de instantie orchestration. ** `id` **
* **`event-name`**: Naam van het evenement te verhogen.
* (optioneel) : Gegevens die naar de orchestration-instantie moeten worden verzonden. ** `event-data` ** Dit kan het pad naar een JSON-bestand zijn, of u de gegevens rechtstreeks op de opdrachtregel verstrekken.
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** De standaardwaarde is `DurableFunctionsHub`. Het kan ook worden ingesteld in [host.json,](durable-functions-bindings.md#host-json)met behulp van durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Wachten op voltooiing van de orkestratie

In langlopende orkestraties, wilt u misschien wachten en de resultaten van een orkestratie te krijgen. In deze gevallen is het ook handig om een time-outperiode op de orkestratie te kunnen definiëren. Als de time-out wordt overschreden, moet de status van de orkestratie worden geretourneerd in plaats van de resultaten.

De `WaitForCompletionOrCreateCheckStatusResponseAsync` methode (.NET) of (JavaScript) `waitForCompletionOrCreateCheckStatusResponse` kan worden gebruikt om de werkelijke uitvoer van een orchestration-instantie synchroon te krijgen. Standaard gebruiken deze methoden een standaardwaarde van `timeout`10 seconden `retryInterval`voor , en 1 seconde voor .  

Hier volgt een voorbeeld van de HTTP-triggerfunctie die laat zien hoe u deze API gebruiken:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

Roep de functie aan met de volgende regel. Gebruik 2 seconden voor de time-out en 0,5 seconden voor het interval opnieuw proberen:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Afhankelijk van de tijd die nodig is om het antwoord van de orchestration-instantie te krijgen, zijn er twee gevallen:

* De orchestration-exemplaren zijn voltooid binnen de gedefinieerde time-out (in dit geval 2 seconden) en het antwoord is de werkelijke uitvoer van orchestration-exemplaren, synchroon geleverd:

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

* De orchestration-exemplaren kunnen niet worden voltooid binnen de gedefinieerde time-out en het antwoord is de standaardinstelling die wordt beschreven in [de URL-detectie van de HTTP-API:](durable-functions-http-api.md)

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
> De indeling van de webhook-URL's kan verschillen, afhankelijk van de versie van de Azure Functions-host die u uitvoert. Het vorige voorbeeld is voor de Azure Functions 2.0-host.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP-beheer-webhook-URL's ophalen

U een extern systeem gebruiken om gebeurtenissen te controleren of te verhogen tot een orkestratie. Externe systemen kunnen communiceren met duurzame functies via de webhook-URL's die deel uitmaken van de standaardrespons die wordt beschreven in de DETECTIE van [HTTP API-URL's.](durable-functions-http-features.md#http-api-url-discovery) De webhook-URL's kunnen ook programmatisch worden geopend met behulp van de [orchestration clientbinding.](durable-functions-bindings.md#orchestration-client) De `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` of de (JavaScript)-methoden kunnen worden gebruikt om een serializable object te krijgen dat deze webhook-URL's bevat.

De `CreateHttpManagementPayload` methoden (.NET) en `createHttpManagementPayload` (JavaScript) hebben één parameter:

* **instanceId**: de unieke ID van de instantie.

De methoden retourneren een object met de volgende tekenreekseigenschappen:

* **Id**: De instantie-id van de orchestration `InstanceId` (moet hetzelfde zijn als de invoer).
* **StatusQueryGetUri:** de status-URL van de orchestration-instantie.
* **SendEventPostUri**: De URL "raise event" van de orchestration instance.
* **TerminatePostUri**: De URL "beëindigen" van de orchestration-instantie.
* **PurgeHistoryDeleteUri**: De URL "zuiveringsgeschiedenis" van de instantie orchestration.

Functies kunnen exemplaren van deze objecten naar externe systemen sturen om gebeurtenissen op de bijbehorende orkestraties te controleren of te verhogen, zoals in de volgende voorbeelden wordt weergegeven:

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `DurableActivityContext` moet `IDurableActivityContext`u `OrchestrationClient` het kenmerk gebruiken in `DurableClient` plaats van het `DurableOrchestrationClient` kenmerk en `IDurableOrchestrationClient`moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

## <a name="rewind-instances-preview"></a>Instanties terugspoelen (voorbeeld)

Als u een orchestration-fout hebt om een onverwachte reden, u de instantie *terugspoelen* naar een eerder gezonde status met behulp van een API die voor dat doel is gebouwd.

> [!NOTE]
> Deze API is niet bedoeld als vervanging voor een goed beleid voor foutafhandeling en opnieuw proberen. Het is veeleer bedoeld om alleen te worden gebruikt in gevallen waarin orchestration-exemplaren om onverwachte redenen mislukken. Zie het artikel [Foutafhandeling](durable-functions-error-handling.md) voor meer informatie over foutafhandeling en het opnieuw proberen van beleid.

Gebruik `RewindAsync` de methode (.NET) of `rewind` (JavaScript) van de [orchestration client binding](durable-functions-bindings.md#orchestration-client) om de orchestration terug te zetten in de status *Actief.* Met deze methode wordt ook de activiteit staken of suborchestration uitvoeringsfouten uitgevoerd die de orchestration failure hebben veroorzaakt.

Stel dat u een werkstroom hebt met een reeks [menselijke goedkeuringen.](durable-functions-overview.md#human) Stel dat er een reeks activiteitsfuncties zijn die iemand laten weten dat hun goedkeuring nodig is en wachten op de realtime reactie. Nadat alle goedkeuringsactiviteiten reacties hebben ontvangen of een time-out hebben opgemaakt, u ervoor zorgen dat een andere activiteit mislukt als gevolg van een foutieve configuratie van een toepassing, zoals een ongeldige databaseverbindingstekenreeks. Het resultaat is een orchestration-fout diep in de workflow. Met `RewindAsync` de (.NET) of `rewind` (JavaScript)-API kan een toepassingsbeheerder de configuratiefout oplossen en de mislukte orkestratie terugspoelen naar de status vlak voor de fout. Geen van de stappen voor menselijke interactie hoeft opnieuw te worden goedgekeurd en de orkestratie kan nu met succes worden voltooid.

> [!NOTE]
> De *terugspoelfunctie* biedt geen ondersteuning voor terugspoelende orchestration-exemplaren die duurzame timers gebruiken.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

U een orchestration-instantie ook rechtstreeks terugspoelen met de opdracht [Core Extra's](../functions-run-local.md) `durable rewind` voor Azure Functions. Er zijn de volgende parameters nodig:

* (verplicht) : IDENTITEITSBEWIJS van de instantie orchestration. ** `id` **
* (facultatief) : Reden voor het terugwikkelen van de orchestration-instantie. ** `reason` **
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** Standaard wordt de naam van de taakhub in het [bestand host.json](durable-functions-bindings.md#host-json) gebruikt.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Instantiegeschiedenis wissen

Als u alle gegevens wilt verwijderen die aan een orchestration zijn gekoppeld, u de instantiegeschiedenis wissen. U bijvoorbeeld eventuele Azure Table-rijen en grote berichtblobs verwijderen die zijn gekoppeld aan een voltooide instantie. Gebruik hiervoor de `PurgeInstanceHistoryAsync` methode (.NET) of `purgeInstanceHistory` (JavaScript) van de [orchestration client binding](durable-functions-bindings.md#orchestration-client).

Deze methode heeft twee overbelastingen. De eerste overbelasting zuivert geschiedenis door ID van de orchestrationinstantie:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Zie [Instanties starten](#javascript-function-json) voor de configuratie function.json.

---

In het volgende voorbeeld wordt een functie die door een timer wordt geactiveerd en die de geschiedenis voor alle orchestration-instanties die zijn voltooid na het opgegeven tijdsinterval, gewist. In dit geval worden gegevens verwijderd voor alle exemplaren die 30 of meer dagen geleden zijn voltooid. Het is gepland om een keer per dag te draaien, om 12 uur:

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

De `purgeInstanceHistoryBy` methode kan worden gebruikt om de instantiegeschiedenis voor meerdere instanties voorwaardelijk te wissen.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> In dit voorbeeld wordt de functie duurzame functies van versie 2.x. Gebruik in `orchestrationClient` versie 1.x `durableClient`in plaats van .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> Als de zuiveringsgeschiedenisbewerking moet de runtime-status van de doelinstantie **voltooid**, **beëindigd**of **mislukt**zijn .

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

U de geschiedenis van een orchestration-instantie wissen met de opdracht [Core Extra's](../functions-run-local.md) `durable purge-history` voor Azure Functions. Net als bij het tweede C#-voorbeeld in de vorige sectie worden de geschiedenis voor alle orchestration-exemplaren die tijdens een opgegeven tijdsinterval zijn gemaakt, gewist. U gewiste exemplaren verder filteren op runtime-status. De opdracht heeft verschillende parameters:

* (optioneel) : De geschiedenis van instanties verwijderen die na deze datum/tijd zijn gemaakt (UTC). ** `created-after` ** ISO 8601 opgemaakte datumtijden geaccepteerd.
* (optioneel) : De geschiedenis van instanties verwijderen die vóór deze datum/tijd zijn gemaakt (UTC). ** `created-before` ** ISO 8601 opgemaakte datumtijden geaccepteerd.
* (optioneel) : De geschiedenis van instanties met een bepaalde status wissen (bijvoorbeeld uitvoeren of voltooid). ** `runtime-status` ** Kan meerdere (ruimtegescheiden) statussen bieden.
* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** Standaard wordt de naam van de taakhub in het [bestand host.json](durable-functions-bindings.md#host-json) gebruikt.

Met de volgende opdracht wordt de geschiedenis verwijderd van alle mislukte exemplaren die vóór 14 november 2018 zijn gemaakt om 19:35 uur (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Een taakhub verwijderen

Met de opdracht [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` u alle opslagartefacten verwijderen die zijn gekoppeld aan een bepaalde taakhub, waaronder Azure-opslagtabellen, wachtrijen en blobs. De opdracht heeft twee parameters:

* (optioneel) : Naam van de toepassingsinstelling die de te gebruiken opslagverbindingstekenreeks bevat. ** `connection-string-setting` ** De standaardwaarde is `AzureWebJobsStorage`.
* (optioneel) : Naam van de taakhub Duurzame functies om te gebruiken. ** `task-hub-name` ** Standaard wordt de naam van de taakhub in het [bestand host.json](durable-functions-bindings.md#host-json) gebruikt.

Met de volgende opdracht worden alle `UserTest` Azure-opslaggegevens verwijderd die zijn gekoppeld aan de taakhub.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het omgaan met versiebeheer](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Ingebouwde HTTP API-referentie voor bijvoorbeeld beheer](durable-functions-http-api.md)
