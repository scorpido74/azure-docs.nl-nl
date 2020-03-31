---
title: Diagnostiek in duurzame functies - Azure
description: Meer informatie over het diagnosticeren van problemen met de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278192"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostische gegevens in Durable Functions in Azure

Er zijn verschillende opties voor het diagnosticeren van problemen met [duurzame functies.](durable-functions-overview.md) Sommige van deze opties zijn hetzelfde voor gewone functies en sommige zijn uniek voor Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is de aanbevolen manier om diagnostiek en monitoring in Azure-functies uit te voeren. Hetzelfde geldt voor duurzame functies. Zie [Azure-functies controleren](../functions-monitoring.md)voor een overzicht van het gebruik van toepassingsinzichten in uw functie-app.

De duurzame extensie Azure-functies zendt ook *trackinggebeurtenissen* uit waarmee u de end-to-end uitvoering van een orchestration traceren. Deze trackinggebeurtenissen kunnen worden gevonden en opgevraagd met behulp van het hulpprogramma [Application Insights Analytics](../../azure-monitor/app/analytics.md) in de Azure-portal.

### <a name="tracking-data"></a>Gegevens bijhouden

Elke levenscyclusgebeurtenis van een orchestration-instantie zorgt ervoor dat een trackinggebeurtenis wordt geschreven naar de **tracesverzameling** in Application Insights. Deze gebeurtenis bevat een **aangepasteDimensions** payload met verschillende velden.  Veldnamen zijn allemaal `prop__`voorbereid met .

* **hubName:** de naam van de taakhub waarin uw orkestraties worden uitgevoerd.
* **appName:** de naam van de functie-app. Dit veld is handig wanneer u meerdere functie-apps hebt die hetzelfde exemplaar Application Insights delen.
* **slotName:** de [implementatiesleuf](../functions-deployment-slots.md) waarin de huidige functie-app wordt uitgevoerd. Dit veld is handig wanneer u implementatieslots gebruikt om uw orkestraties te versieren.
* **functieNaam:** de naam van de orchestrator of activiteitsfunctie.
* **functieType**: Het type functie, zoals **Orchestrator** of **Activity**.
* **instanceId**: De unieke ID van de instantie orchestration.
* **status:** de uitvoeringsstatus van de instantie. Geldige waarden zijn:
  * **Gepland**: De functie was gepland voor uitvoering, maar is nog niet gestart.
  * **Gestart**: De functie is gestart, maar heeft nog niet gewacht of voltooid.
  * **Wacht :** De orchestrator heeft gepland wat werk en wacht tot het te voltooien.
  * **Luisteren**: De orchestrator luistert naar een externe gebeurtenismelding.
  * **Voltooid**: De functie is voltooid.
  * **Mislukt:** de functie is mislukt met een fout.
* **reden**: Aanvullende gegevens die zijn gekoppeld aan de trackinggebeurtenis. Als een instantie bijvoorbeeld wacht op een externe gebeurtenismelding, geeft dit veld de naam aan van de gebeurtenis waarop wordt gewacht. Als een functie is mislukt, bevat dit veld de foutgegevens.
* **isReplay:** Booleaanse waarde die aangeeft of de trackinggebeurtenis voor uitgestelde uitvoering is.
* **extensionVersion**: De versie van de extensie Duurzame taak. De versie-informatie is vooral belangrijke gegevens bij het melden van mogelijke bugs in de extensie. Langlopende exemplaren kunnen meerdere versies rapporteren als er een update optreedt terwijl deze wordt uitgevoerd.
* **volgordeNummer**: Uitvoeringsvolgordenummer voor een gebeurtenis. In combinatie met de tijdstempel helpt om de gebeurtenissen te bestellen op uitvoeringstijd. *Houd er rekening mee dat dit nummer wordt gereset naar nul als de host opnieuw wordt opgestart terwijl de instantie wordt uitgevoerd, dus het is belangrijk om altijd eerst te sorteren op tijdstempel en vervolgens te sequencenNummer.*

De verbositeit van trackinggegevens die worden uitgezonden naar `logger` Application Insights kan `logging` worden geconfigureerd in het gedeelte `host.json` (Functies 1.x) of (Functies 2.0) van het bestand.

#### <a name="functions-10"></a>Functies 1.0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Functies 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Standaard worden alle niet-replay tracking gebeurtenissen uitgezonden. Het volume van de gegevens `Host.Triggers.DurableTask` `"Warning"` kan `"Error"` worden verminderd door het instellen op of in welk geval tracking gebeurtenissen zullen alleen worden uitgezonden voor uitzonderlijke situaties.

Om het uitzenden van de verbose `LogReplayEvents` orchestration `true` replay-gebeurtenissen in te schakelen, kan deze worden ingesteld in het `host.json` bestand onder `durableTask` zoals weergegeven:

#### <a name="functions-10"></a>Functies 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Functies 2.0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Standaard wordt de telemetrie van Application Insights bemonsterd door de runtime van Azure Functions om te voorkomen dat gegevens te vaak worden uitgezonden. Hierdoor kan trackinginformatie verloren gaan wanneer veel gebeurtenis op de levenscyclus zich in een korte periode voordoet. In [het artikel Azure Functions Monitoring](../functions-monitoring.md#configure-sampling) wordt uitgelegd hoe u dit gedrag configureren.

### <a name="single-instance-query"></a>Query met één instantie

In de volgende query worden historische trackinggegevens weergegeven voor één exemplaar van de functie [Hello Sequence.](durable-functions-sequence.md) Het is geschreven met behulp van de [Application Insights Query Language (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Het filtert replay uitvoering, zodat alleen de *logische* uitvoering pad wordt weergegeven. Gebeurtenissen kunnen worden geordend `timestamp` door `sequenceNumber` te sorteren op en zoals weergegeven in de onderstaande query:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Het resultaat is een lijst met trackinggebeurtenissen die het uitvoeringspad van de orkestratie weergeven, inclusief alle activiteitsfuncties die zijn geordend op basis van de uitvoeringstijd in oplopende volgorde.

![Query Toepassingsinzichten](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Voorbeeldoverzichtsquery

In de volgende query wordt de status weergegeven van alle orchestration-exemplaren die in een opgegeven tijdsbereik zijn uitgevoerd.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Het resultaat is een lijst met instantie-id's en hun huidige runtime-status.

![Query Toepassingsinzichten](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Logboekregistratie

Het is belangrijk om het orchestrator replay gedrag in gedachten te houden bij het schrijven van logs rechtstreeks van een orchestrator functie. Denk bijvoorbeeld aan de volgende orchestrator-functie:

### <a name="precompiled-c"></a>Vooraf gecompileerde C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>C# Script

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-20-only"></a>JavaScript (alleen functies 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

De resulterende loggegevens gaan er ongeveer zo uitzien als de volgende voorbeelduitvoer:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Vergeet niet dat, terwijl de logs beweren te bellen F1, F2 en F3, die functies zijn alleen *eigenlijk* genoemd de eerste keer dat ze worden aangetroffen. Volgende oproepen die tijdens het afspelen plaatsvinden, worden overgeslagen en de uitvoer wordt afgespeeld op de orchestrator-logica.

Als u zich alleen wilt aanmelden bij niet-replay-uitvoering, kunt `IsReplaying` `false`u een voorwaardelijke expressie schrijven om alleen te loggen als dat is . Denk aan het bovenstaande voorbeeld, maar deze keer met replay controles.

#### <a name="precompiled-c"></a>Vooraf gecompileerde C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (alleen functies 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Vanaf duurzame functies 2.0 hebben de functies van .NET `ILogger` orchestrator ook de mogelijkheid om een functie te maken die logstatements tijdens het afspelen automatisch uitfiltert. Deze automatische filtering gebeurt `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` met behulp van de API.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

Bij de eerder genoemde wijzigingen is de logboekuitvoer als volgt:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> De vorige C# voorbeelden zijn voor Duurzame Functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

## <a name="custom-status"></a>Aangepaste status

Met de aangepaste orkestratiestatus u een aangepaste statuswaarde instellen voor uw orchestrator-functie. Deze status wordt verstrekt via de `IDurableOrchestrationClient.GetStatusAsync` HTTP-statusquery-API of de API. De aangepaste orchestration-status maakt rijkere bewaking mogelijk voor orchestrator-functies. De functiecode orchestrator kan `IDurableOrchestrationContext.SetCustomStatus` bijvoorbeeld oproepen bevatten om de voortgang voor een langdurige bewerking bij te werken. Een client, zoals een webpagina of een ander extern systeem, kan vervolgens periodiek de HTTP-statusquery-API's opvragen voor uitgebreidere voortgangsinformatie. Hieronder `IDurableOrchestrationContext.SetCustomStatus` wordt een monster gegeven:

### <a name="precompiled-c"></a>Vooraf gecompileerde C #

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Het vorige C#-voorbeeld is voor Duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-20-only"></a>JavaScript (alleen functies 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Terwijl de orkestratie wordt uitgevoerd, kunnen externe clients deze aangepaste status ophalen:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klanten krijgen het volgende antwoord:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> De aangepaste statuspayload is beperkt tot 16 KB UTF-16 JSON-tekst omdat deze in een kolom Azure Table Storage moet kunnen passen. U externe opslag gebruiken als u een groter laadvermogen nodig hebt.

## <a name="debugging"></a>Foutopsporing

Azure Functions ondersteunt debugging-functiecode rechtstreeks en diezelfde ondersteuning wordt uitgevoerd naar duurzame functies, of deze nu in Azure of lokaal wordt uitgevoerd. Er zijn echter een paar gedragingen om rekening mee te houden bij het debuggen:

* **Replay**: Orchestrator-functies worden regelmatig [afgespeeld](durable-functions-orchestrations.md#reliability) wanneer nieuwe ingangen worden ontvangen. Dit gedrag betekent dat *een* logische uitvoering van een orchestrator-functie kan resulteren in het raken van hetzelfde breekpunt meerdere keren, vooral als het is ingesteld vroeg in de functiecode.
* **Wachten**: Wanneer `await` een wordt aangetroffen in een orchestrator functie, het levert controle terug naar de Duurzame Task Framework dispatcher. Als het de eerste `await` keer is dat een bepaalde taak is aangetroffen, wordt de bijbehorende taak *nooit* hervat. Omdat de taak nooit wordt hervat, is het niet mogelijk om *over* de wacht te stappen (F10 in Visual Studio). Overstappen werkt alleen als een taak wordt overgespeeld.
* **Time-outs voor**berichten: Duurzame functies gebruiken intern wachtrijberichten om de uitvoering van orchestrator-, activiteits- en entiteitsfuncties te stimuleren. In een multi-VM-omgeving kan het inbreken in het foutopsporing voor langere tijd ertoe leiden dat een andere VM het bericht oppikt, wat resulteert in dubbele uitvoering. Dit gedrag bestaat ook voor reguliere wachtrij-triggerfuncties, maar is belangrijk om in deze context aan te geven, omdat de wachtrijen een implementatiedetail zijn.
* **Stoppen en starten:** Berichten in duurzame functies blijven bestaan tussen foutopsporingssessies. Als u stopt met foutopsporing en het lokale hostproces beëindigt terwijl een duurzame functie wordt uitgevoerd, kan die functie automatisch opnieuw worden uitgevoerd in een toekomstige foutopsporingssessie. Dit gedrag kan verwarrend zijn wanneer dit niet wordt verwacht. Het wissen van alle berichten uit de [interne opslagwachtrijen](durable-functions-perf-and-scale.md#internal-queue-triggers) tussen foutopsporingssessies is een techniek om dit gedrag te voorkomen.

> [!TIP]
> Wanneer u breekpunten in orchestrator-functies instelt, u, als u alleen wilt `IsReplaying` breken `false`op niet-replay-uitvoering, een voorwaardelijk breekpunt instellen dat alleen wordt afgebroken als dat is.

## <a name="storage"></a>Storage

Standaard worden er status duurzame functies opgeslagen in Azure Storage. Dit gedrag betekent dat u de status van uw orkestraties inspecteren met behulp van hulpprogramma's zoals [Microsoft Azure Storage Explorer.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)

![Schermafbeelding van Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Dit is handig voor het debuggen omdat u precies ziet in welke staat een orchestration zich kan begeven. Berichten in de wachtrijen kunnen ook worden onderzocht om te zien welk werk in behandeling is (of in sommige gevallen vastzit).

> [!WARNING]
> Hoewel het handig is om de uitvoeringsgeschiedenis in tabelopslag te zien, vermijdt u enige afhankelijkheid van deze tabel. Het kan veranderen naarmate de extensie Duurzame functies evolueert.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over controle in Azure-functies](../functions-monitoring.md)
