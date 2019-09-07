---
title: Diagnostische gegevens in Durable Functions-Azure
description: Meer informatie over het vaststellen van problemen met de Durable Functions extensie voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7c02d4dfde7869da7985817b06f6de398bbef38d
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734495"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostische gegevens in Durable Functions in azure

Er zijn verschillende opties voor het vaststellen van problemen met [Durable functions](durable-functions-overview.md). Sommige van deze opties zijn hetzelfde voor gewone functies en sommige zijn uniek voor Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is de aanbevolen methode voor het uitvoeren van diagnostische gegevens en bewaking in azure functions. Hetzelfde geldt voor Durable Functions. Zie [Azure functions bewaken](../functions-monitoring.md)voor een overzicht van het gebruik van Application Insights in uw functie-app.

De Azure Functions duurzame extensie levert ook *gebeurtenissen* op waarmee u de end-to-end-uitvoering van een indeling kunt volgen. Deze kunnen worden gevonden en opgevraagd met behulp van het hulp programma [Application Insights Analytics](../../azure-monitor/app/analytics.md) in de Azure Portal.

### <a name="tracking-data"></a>Gegevens bijhouden

Elke levens cyclus gebeurtenis van een indelings instantie zorgt ervoor dat een tracerings gebeurtenis wordt geschreven naar de verzameling **traces** in Application Insights. Deze gebeurtenis bevat een **customDimensions** -nettolading met verschillende velden.  Veld namen zijn allemaal voor `prop__`.

* **hubName**: De naam van de taak hub waarin uw Orchestrations worden uitgevoerd.
* **AppName**: De naam van de functie-app. Dit is handig wanneer u meerdere functie-apps hebt die hetzelfde Application Insights exemplaar delen.
* **sleuf**: De [implementatie site](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) waarin de huidige functie-app wordt uitgevoerd. Dit is handig wanneer u implementatie sleuven gebruikt voor het versie gebruik van uw Orchestrations.
* **functie naam**: De naam van de functie Orchestrator of activiteit.
* **functionType**: Het type functie, zoals **Orchestrator** of **Activity**.
* **instanceId**: De unieke ID van het Orchestration-exemplaar.
* **status**: De uitvoerings status van de levens cyclus van het exemplaar. Geldige waarden zijn:
  * **Gepland**: De functie is gepland om te worden uitgevoerd, maar is nog niet gestart.
  * **Gestart**: De functie is gestart, maar is nog niet gewacht of voltooid.
  * In **afwachting**: De Orchestrator heeft een aantal werk gepland en wacht tot het is voltooid.
  * **Luis teren**: De Orchestrator luistert naar een externe gebeurtenis melding.
  * **Voltooid**: De functie is voltooid.
  * **Mislukt**: De functie is mislukt met een fout.
* **reden**: Aanvullende gegevens die zijn gekoppeld aan de tracerings gebeurtenis. Als een instantie bijvoorbeeld wacht op een externe gebeurtenis melding, wordt in dit veld de naam van de gebeurtenis aangegeven waarin wordt gewacht. Als een functie is mislukt, bevat dit de fout Details.
* **isReplay**: Booleaanse waarde waarmee wordt aangegeven of de tracerings gebeurtenis is ingesteld op opnieuw gespelde uitvoering.
* **extensionVersion**: De versie van de duurzame taak uitbreiding. Dit is met name belang rijke gegevens bij het rapporteren van mogelijke fouten in de uitbrei ding. Langlopende instanties rapporteren mogelijk meerdere versies als er een update wordt uitgevoerd terwijl deze actief is.
* **sequenceNumber**: Het Volg nummer van de uitvoering voor een gebeurtenis. In combi natie met de tijds tempel kunnen de gebeurtenissen worden gesorteerd op uitvoerings tijd. *Houd er rekening mee dat dit aantal wordt ingesteld op nul als de host opnieuw wordt opgestart terwijl het exemplaar wordt uitgevoerd. het is dus belang rijk om altijd eerst op tijds tempel te sorteren en vervolgens op sequenceNumber.*

De uitgebreide tracerings gegevens die naar Application Insights worden verzonden, kunnen worden geconfigureerd in `logger` de sectie (functions 1. `logging` x) of (functions 2. x `host.json` ) van het bestand.

#### <a name="functions-1x"></a>Functions 1.x

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

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Standaard worden alle gebeurtenissen voor het bijhouden van niet-opnieuw afspelen verzonden. Het gegevens volume kan worden verkleind door `Host.Triggers.DurableTask` in `"Warning"` te `"Error"` stellen of in het geval dat het volgen van gebeurtenissen alleen wordt verzonden voor uitzonderlijke situaties.

Als u de uitgebreide Orchestration replay-gebeurtenissen wilt laten verzenden, kunt `LogReplayEvents` u `true` deze instellen in het `host.json` bestand onder `durableTask` zoals hieronder wordt weer gegeven:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

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
> Application Insights telemetrie wordt standaard door de Azure Functions-runtime bemonsterd om te voor komen dat gegevens te vaak worden verzonden. Dit kan ervoor zorgen dat de tracerings informatie verloren gaat wanneer er veel levenscyclus gebeurtenissen in korte tijd optreden. In het [artikel over Azure functions bewaking](../functions-monitoring.md#configure-sampling) wordt uitgelegd hoe u dit gedrag kunt configureren.

### <a name="single-instance-query"></a>Query met één exemplaar

Met de volgende query worden historische tracking gegevens weer gegeven voor één exemplaar van de functie Orchestration van de [Hello-reeks](durable-functions-sequence.md) . Het is geschreven met behulp van de [Application Insights query language (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Hiermee wordt de uitvoering van herhalingen gefilterd, zodat alleen het *logische* uitvoerings traject wordt weer gegeven. Gebeurtenissen kunnen worden gerangschikt door te sorteren `timestamp` op `sequenceNumber` en zoals wordt weer gegeven in de onderstaande query:

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

Het resultaat is een lijst met tracerings gebeurtenissen waarmee het pad van de uitvoering van de indeling wordt weer gegeven, inclusief de activiteit functies die worden besteld door de uitvoerings tijd in oplopende volg orde.

![Application Insights query](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Query voor exemplaar samenvatting

Met de volgende query wordt de status weer gegeven van alle indelings instanties die zijn uitgevoerd in een opgegeven tijds bereik.

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

Het resultaat is een lijst met exemplaar-Id's en de huidige runtime status.

![Application Insights query](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Logboekregistratie

Het is belang rijk om ervoor te zorgen dat het Orchestrator-gedrag voor het opnieuw afspelen van een logboeken rechtstreeks vanuit een Orchestrator-functie wordt geschreven. Denk bijvoorbeeld aan de volgende Orchestrator-functie:

### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
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
    DurableOrchestrationContext context,
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

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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

De resulterende logboek gegevens gaan er ongeveer als volgt uit:

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
> Houd er rekening mee dat de logboeken van de claim F1, F2 en F3 worden aangeroepen. deze functies *worden alleen de* eerste keer dat ze worden aangetroffen. Volgende aanroepen die tijdens het herhalen plaatsvinden, worden overgeslagen en de uitvoer wordt opnieuw afgespeeld naar de Orchestrator-logica.

Als u zich alleen wilt aanmelden voor een niet-replay-uitvoering, kunt u een voorwaardelijke expressie schrijven om alleen `IsReplaying` te `false`registreren als dat zo is. Bekijk het bovenstaande voor beeld, maar deze keer met replay controles.

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
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
    DurableOrchestrationContext context,
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

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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

Met deze wijziging ziet de logboek uitvoer er als volgt uit:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Aangepaste status

Met de aangepaste indelings status kunt u een aangepaste status waarde instellen voor uw Orchestrator-functie. Deze status wordt weer gegeven via de HTTP-status query- `DurableOrchestrationClient.GetStatusAsync` API of de API. De aangepaste indelings status maakt uitgebreide bewaking mogelijk voor Orchestrator-functies. De functie code van Orchestrator kan bijvoorbeeld aanroepen bevatten `DurableOrchestrationContext.SetCustomStatus` om de voortgang voor een langlopende bewerking bij te werken. Een-client, zoals een webpagina of een ander extern systeem, kan vervolgens periodiek query's uitvoeren op de HTTP-status query Api's voor informatie over rijkere voortgang. Hieronder vindt u `DurableOrchestrationContext.SetCustomStatus` een voor beeld van het gebruik:

### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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

Terwijl de Orchestration wordt uitgevoerd, kunnen externe clients deze aangepaste status ophalen:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Clients ontvangen het volgende antwoord:

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
> De nettolading van de aangepaste status is beperkt tot 16 KB aan UTF-16-JSON-tekst omdat deze moet kunnen passen in een Azure Table Storage-kolom. U kunt externe opslag gebruiken als u een grotere Payload nodig hebt.

## <a name="debugging"></a>Foutopsporing

Azure Functions ondersteunt functie code voor fout opsporing rechtstreeks en dezelfde ondersteuning gaat naar Durable Functions, ongeacht of deze wordt uitgevoerd in azure of lokaal. Er zijn echter enkele gedragingen waarmee u rekening moet houden bij fout opsporing:

* Opnieuw **afspelen**: Orchestrator-functies worden regel matig herhaald wanneer er nieuwe invoer wordt ontvangen. Dit betekent dat een enkele *logische* uitvoering van een Orchestrator-functie kan leiden tot hetzelfde onderbrekings punt meerdere keren, vooral als het vroegtijdig is ingesteld in de functie code.
* **Wacht**op: Wanneer er `await` een fout optreedt, wordt het besturings element teruggebracht naar de verzender van het duurzame taak raamwerk. Als dit de eerste keer is dat een `await` bepaald is aangetroffen, wordt de bijbehorende taak *nooit* hervat. Omdat de taak nooit wordt hervat, is *het niet* mogelijk om door te lopen (F10 in Visual Studio). Alleen uitvoeren werkt wanneer een taak wordt herhaald.
* **Time-outs van berichten**: Durable Functions intern gebruikt wachtrij berichten om de uitvoering van Orchestrator-functies en-activiteit functies te verzorgen. In een omgeving met meerdere VM'S kan de fout opsporing voor langere Peri Oden ertoe leiden dat een andere virtuele machine het bericht ophaalt, wat resulteert in dubbele uitvoering. Dit gedrag bestaat ook voor normale functies van de wachtrij-trigger, maar is belang rijk om in deze context te wijzen, omdat de wacht rijen een implementatie detail zijn.

> [!TIP]
> Als u onderbrekings punten wilt instellen, kunt u, als u alleen een niet-replay-uitvoering wilt onderbreken, een voorwaardelijk onderbrekings `IsReplaying` punt `false`instellen dat alleen als is afgebroken.

## <a name="storage"></a>Storage

Durable Functions-status wordt standaard opgeslagen in Azure Storage. Dit betekent dat u de status van uw Orchestrations kunt inspecteren met behulp van hulpprogram ma's zoals [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Scherm afbeelding Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Dit is handig voor het opsporen van fouten, omdat u precies ziet in welke staat een indeling kan zijn. Berichten in de wacht rijen kunnen ook worden onderzocht om te ontdekken wat er in behandeling is (of in sommige gevallen vastgelopen).

> [!WARNING]
> Hoewel het handig is om uitvoerings geschiedenis te zien in tabel opslag, moet u geen afhankelijkheid van deze tabel maken. Dit kan veranderen als de Durable Functions extensie wordt ontwikkeld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van duurzame timers](durable-functions-timers.md)