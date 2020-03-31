---
title: Duurzame orkestraties - Azure-functies
description: Inleiding tot de orchestration-functie voor duurzame azure-functies.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: caa62483373a240991cfec96437cea7849d9b19c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241358"
---
# <a name="durable-orchestrations"></a>Duurzame orkestraties

Duurzame functies is een uitbreiding van [Azure-functies.](../functions-overview.md) U een *orchestrator-functie* gebruiken om de uitvoering van andere duurzame functies binnen een functie-app te orkestreren. Orchestrator-functies hebben de volgende kenmerken:

* Orchestrator-functies definiëren functiewerkstromen met behulp van procedurele code. Er zijn geen declaratieve schema's of ontwerpers nodig.
* Orchestrator-functies kunnen andere duurzame functies synchroon en asynchroon aanroepen. Uitvoer van aangeroepen functies kan op betrouwbare wijze worden opgeslagen op lokale variabelen.
* Orchestrator functies zijn duurzaam en betrouwbaar. De voortgang van de uitvoering wordt automatisch gecontroleerd wanneer de functie "wacht" of "levert". Er gaat nooit lokale status verloren wanneer het proces recyclet of de VM opnieuw wordt opgestart.
* Orchestrator-functies kunnen lang duren. De totale levensduur van een *orchestration-instantie* kan seconden, dagen, maanden of nooit eindigende zijn.

Dit artikel geeft u een overzicht van orchestrator functies en hoe ze u kunnen helpen bij het oplossen van verschillende uitdagingen op het gebied van app-ontwikkeling. Als u nog niet bekend bent met de typen functies die beschikbaar zijn in een app Duurzame functies, leest u eerst het artikel [Over duurzame functietypen.](durable-functions-types-features-overview.md)

## <a name="orchestration-identity"></a>Orkestratie-identiteit

Elke *instantie* van een orkestratie heeft een instantie-id (ook wel *instantie-id genoemd).* Standaard is elke instantie-id een automatisch gegenereerde GUID. Instantie-id's kunnen echter ook elke door de gebruiker gegenereerde tekenreekswaarde zijn. Elke orchestration instance ID moet uniek zijn binnen een [taakhub.](durable-functions-task-hubs.md)

Hieronder volgen enkele regels voor instantie-iD's:

* Instantie-id's moeten tussen 1 en 256 tekens zijn.
* Instantie-id's mogen `@`niet beginnen met .
* Instantie-id's mogen `/` `\`geen `#`tekens `?` of tekens bevatten.
* Instantie-id's mogen geen controletekens bevatten.

> [!NOTE]
> Het wordt over het algemeen aanbevolen om automatisch gegenereerde instantie-id's te gebruiken waar mogelijk. Door gebruikers gegenereerde instantie-id's zijn bedoeld voor scenario's waarbij er een één-op-één toewijzing is tussen een orchestration-instantie en een externe toepassingsspecifieke entiteit, zoals een inkooporder of een document.

De instantie-id van een orchestration is een vereiste parameter voor de meeste [beheerbewerkingen voor bijvoorbeeld.](durable-functions-instance-management.md) Ze zijn ook belangrijk voor diagnostiek, zoals [het doorzoeken van orchestration tracking gegevens](durable-functions-diagnostics.md#application-insights) in Application Insights voor probleemoplossing of analysedoeleinden. Om deze reden wordt aanbevolen om gegenereerde instantie-id's op te slaan op een externe locatie (bijvoorbeeld een database of in toepassingslogboeken) waar ze later gemakkelijk kunnen worden verwezen.

## <a name="reliability"></a>Betrouwbaarheid

Orchestrator-functies behouden op betrouwbare wijze hun uitvoeringsstatus met behulp van het [ontwerppatroon voor het zoeken naar gebeurtenissen.](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) In plaats van de huidige status van een orkestratie rechtstreeks op te slaan, gebruikt het Framework Voor duurzame taken een append-only winkel om de volledige reeks acties op te nemen die de functieorkestratie uitvoert. Een append-only winkel heeft veel voordelen in vergelijking met "dumping" de volledige runtime staat. Voordelen zijn onder meer betere prestaties, schaalbaarheid en responsiviteit. U krijgt ook uiteindelijke consistentie voor transactionele gegevens en volledige audit trails en geschiedenis. De audittrails ondersteunen betrouwbare compenserende acties.

Sustainable Functions maakt op transparante wijze gebruik van event sourcing. Achter de schermen `await` geeft de `yield` operator (C#) of (JavaScript) in een orchestrator-functie de controle over de orchestratorthread terug naar de dispatcher Van het Duurzame Taakkader. De dispatcher verbindt vervolgens alle nieuwe acties die de orchestrator-functie heeft gepland (zoals het aanroepen van een of meer onderliggende functies of het plannen van een duurzame timer) naar opslag. De transparante commit-actie wordt toegevoegd aan de uitvoeringsgeschiedenis van de orchestration-instantie. De geschiedenis wordt opgeslagen in een opslagtabel. De commit-actie voegt vervolgens berichten toe aan een wachtrij om het werkelijke werk te plannen. Op dit punt kan de orchestrator-functie uit het geheugen worden gelost.

Wanneer een orchestration-functie meer werk te doen krijgt (bijvoorbeeld een antwoordbericht wordt ontvangen of een duurzame timer verloopt), wordt de orchestrator wakker en voert de hele functie vanaf het begin opnieuw uit om de lokale status opnieuw op te bouwen. Als de code tijdens de herhaling een functie probeert aan te roepen (of ander asyncwerk te doen), raadpleegt het Kader voor duurzame taken de uitvoeringsgeschiedenis van de huidige orkestratie. Als wordt vastgesteld dat de [activiteitsfunctie](durable-functions-types-features-overview.md#activity-functions) al is uitgevoerd en een resultaat heeft opgeleverd, wordt het resultaat van die functie opnieuw afgespeeld en wordt de orchestratorcode voortgezet. Replay gaat door totdat de functiecode is voltooid of totdat er nieuw async-werk is gepland.

> [!NOTE]
> Om het replaypatroon correct en betrouwbaar te laten werken, moet de orchestrator-functiecode *deterministisch*zijn. Zie het onderwerp [Orchestrator Function code constraints](durable-functions-code-constraints.md) voor meer informatie over codebeperkingen voor orchestrator-functies.

> [!NOTE]
> Als een orchestrator-functie logboekberichten uitzendt, kan het replay-gedrag ervoor zorgen dat dubbele logboekberichten worden uitgezonden. Raadpleeg het [onderwerp Logboekregistratie](durable-functions-diagnostics.md#logging) voor meer informatie over waarom dit gedrag optreedt en hoe u er omheen werken.

## <a name="orchestration-history"></a>Orkestratiegeschiedenis

Het event-sourcing gedrag van het Sustainable Task Framework is nauw gekoppeld aan de orchestrator functiecode die u schrijft. Stel dat u een activiteitsketen orchestrator-functie hebt, zoals de volgende orchestrator-functie:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

---

Bij `await` elke (C#) of `yield` (JavaScript)-instructie wordt in het Framework Voor duurzame taken de uitvoeringsstatus van de functie in een duurzame backend voor duurzame opslag (meestal Azure Table-opslag) gecontroleerd. Deze staat is wat wordt aangeduid als de *orkestratie geschiedenis*.

### <a name="history-table"></a>Geschiedenistabel

In het algemeen doet het Kader duurzame taken bij elk controlepunt het volgende:

1. Hiermee slaat u uitvoeringsgeschiedenis op in Azure Storage-tabellen.
2. Enqueues berichten voor functies die de orchestrator wil aanroepen.
3. Enqueues berichten voor de orchestrator zelf &mdash; bijvoorbeeld, duurzame timer berichten.

Zodra het controlepunt is voltooid, is de orchestrator-functie vrij om uit het geheugen te worden verwijderd totdat er meer werk voor is.

> [!NOTE]
> Azure Storage biedt geen transactionele garanties tussen het opslaan van gegevens in tabelopslag en wachtrijen. Om fouten op te lossen, gebruikt de opslagprovider duurzame functies *uiteindelijkconsistentiepatronen.* Deze patronen zorgen ervoor dat er geen gegevens verloren gaan als er een crash of verlies van connectiviteit in het midden van een controlepunt.

Na voltooiing ziet de geschiedenis van de eerder weergegeven functie er ongeveer uit als de volgende tabel in Azure Table Storage (afgekort voor illustratiedoeleinden):

| PartitionKey (Instanceid)                     | EventType (EventType)             | Tijdstempel               | Invoer | Name             | Resultaat                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | Uitvoeringgestart      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted OrchestratorStarted OrchestratorStarted Orchestrat   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | Taakgepland         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorVoltooid | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | Taak voltooid         | 2017-05-05T18:45:34.201Z |       |                  | """Hallo Tokio!""                                        |                     |
| eaee885b | OrchestratorStarted OrchestratorStarted OrchestratorStarted Orchestrat   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | Taakgepland         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorVoltooid | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | Taak voltooid         | 2017-05-05T18:45:34.763Z |       |                  | """Hallo Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted OrchestratorStarted OrchestratorStarted Orchestrat   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Taakgepland         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorVoltooid | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Taak voltooid         | 2017-05-05T18:45:34.919Z |       |                  | ""Hallo Londen!"""                                       |                     |
| eaee885b | OrchestratorStarted OrchestratorStarted OrchestratorStarted Orchestrat   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorVoltooid | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | Uitvoeringvoltooid    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hallo Tokio!"""Hallo Seattle!"",""Hello London!""]" | Voltooid           |

Een paar opmerkingen over de kolomwaarden:

* **PartitionKey:** bevat de instantie-id van de orchestration.
* **EventType:** geeft het type gebeurtenis weer. Kan een van de volgende typen zijn:
  * **OrchestrationStarted**: De orchestrator functie hervat van een wachten of loopt voor de eerste keer. De `Timestamp` kolom wordt gebruikt om de `CurrentUtcDateTime` deterministische waarde `currentUtcDateTime` voor de (.NET) en (JavaScript) API's te vullen.
  * **ExecutionStarted**: De orchestrator-functie is voor het eerst begonnen met uitvoeren. Deze gebeurtenis bevat ook de `Input` functie-invoer in de kolom.
  * **Taakgepland:** er is een activiteitsfunctie gepland. De naam van de activiteitsfunctie wordt vastgelegd in de `Name` kolom.
  * **Taakvoltooid:** een activiteitsfunctie voltooid. Het resultaat van de `Result` functie bevindt zich in de kolom.
  * **TimerGemaakt:** Er is een duurzame timer gemaakt. De `FireAt` kolom bevat de geplande UTC-tijd waarop de timer verloopt.
  * **TimerFired**: Een duurzame timer ontslagen.
  * **EventRaised**: Er is een externe gebeurtenis naar de instantie orchestration verzonden. De `Name` kolom legt de naam `Input` van de gebeurtenis vast en de kolom legt de lading van de gebeurtenis vast.
  * **OrchestratorVoltooid**: De orchestratorfunctie wachtte.
  * **ContinueAsNew**: De orchestrator-functie voltooid en opnieuw opgestart met nieuwe status. De `Result` kolom bevat de waarde, die wordt gebruikt als invoer in de instantie opnieuw gestart.
  * **ExecutionCompleted**: De orchestrator-functie is voltooid (of mislukt). De uitvoer van de functie of de `Result` foutgegevens worden opgeslagen in de kolom.
* **Tijdstempel**: De UTC-tijdstempel van de geschiedenisgebeurtenis.
* **Naam:** de naam van de functie die is aangeroepen.
* **Invoer**: De JSON-geformatteerde invoer van de functie.
* **Resultaat**: De output van de functie; dat wil zeggen, de rendementswaarde.

> [!WARNING]
> Hoewel het handig is als een foutopsporingshulpmiddel, hoeft u geen afhankelijkheid te nemen van deze tabel. Het kan veranderen naarmate de extensie Duurzame functies evolueert.

Elke keer dat de `await` functie wordt hervat `yield` vanuit een (C#) of (JavaScript), voert het Duurzame taakkader de orchestrator-functie vanaf nul uit. Bij elke bewerking raadpleegt het de uitvoeringsgeschiedenis om te bepalen of de huidige synchronisatiebewerking heeft plaatsgevonden.  Als de bewerking heeft plaatsgevonden, wordt de uitvoer van die `await` bewerking onmiddellijk afgespeeld `yield` en wordt deze naar de volgende (C#) of (JavaScript) verplaatst. Dit proces gaat door totdat de hele geschiedenis is afgespeeld. Zodra de huidige geschiedenis is afgespeeld, worden de lokale variabelen hersteld naar hun vorige waarden.

## <a name="features-and-patterns"></a>Kenmerken en patronen

De volgende secties beschrijven de kenmerken en patronen van orchestrator functies.

### <a name="sub-orchestrations"></a>Onderliggende indelingen

Orchestrator-functies kunnen activiteitsfuncties aanroepen, maar ook andere orchestrator-functies. U bijvoorbeeld een grotere orkestratie bouwen uit een bibliotheek met orchestrator-functies. U ook meerdere exemplaren van een orchestratorfunctie parallel uitvoeren.

Zie het artikel [Suborchestrations voor](durable-functions-sub-orchestrations.md) meer informatie en voorbeelden.

### <a name="durable-timers"></a>Duurzame timers

Orchestrations kunnen *duurzame timers* plannen om vertragingen te implementeren of om time-outafhandeling voor async-acties in te stellen. Gebruik duurzame timers in orchestrator-functies in `Thread.Sleep` plaats van en `Task.Delay` (C#) of `setTimeout()` (JavaScript). `setInterval()`

Zie het artikel [Duurzame timers](durable-functions-timers.md) voor meer informatie en voorbeelden.

### <a name="external-events"></a>Externe gebeurtenissen

Orchestrator-functies kunnen wachten op externe gebeurtenissen om een orchestration-instantie bij te werken. Deze functie Duurzame functies is vaak handig voor het hanteren van een menselijke interactie of andere externe callbacks.

Zie het artikel [Externe gebeurtenissen](durable-functions-external-events.md) voor meer informatie en voorbeelden.

### <a name="error-handling"></a>Foutafhandeling

Orchestrator-functies kunnen de functies voor foutafhandeling van de programmeertaal gebruiken. Bestaande patronen `try` / `catch` zoals worden ondersteund in orchestration code.

Orchestrator-functies kunnen ook een nieuw beleid toevoegen aan de activiteits- of suborchestrator-functies die ze aanroepen. Als een activiteit of suborchestrator-functie mislukt met een uitzondering, kan het opgegeven beleid voor opnieuw proberen de uitvoering automatisch vertragen en opnieuw proberen tot een bepaald aantal keren.

> [!NOTE]
> Als er een niet-behandelde uitzondering is in een orchestrator-functie, wordt de orchestration-instantie in een `Failed` status voltooid. Een orchestration-instantie kan niet opnieuw worden geprobeerd nadat deze is mislukt.

Zie het artikel [Foutafhandeling](durable-functions-error-handling.md) voor meer informatie en voorbeelden.

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritieke secties (Duurzame functies 2.x, momenteel alleen .NET)

Orchestration-exemplaren zijn single-threaded, dus het is niet nodig om je zorgen te maken over de raceomstandigheden *binnen* een orkestratie. Raceomstandigheden zijn echter mogelijk wanneer orkestraties interageren met externe systemen. Om de raceomstandigheden bij interactie met externe systemen te beperken, kunnen orchestrator-functies *kritieke secties* definiëren met behulp van een `LockAsync` methode in .NET.

De volgende voorbeeldcode toont een orchestrator-functie die een kritieke sectie definieert. Het komt in de `LockAsync` kritieke sectie met behulp van de methode. Deze methode vereist het doorgeven van een of meer verwijzingen naar een [duurzame entiteit](durable-functions-entities.md), die de vergrendelingsstatus blijvend beheert. Slechts één exemplaar van deze orkestratie kan de code tegelijk uitvoeren in de kritieke sectie.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

Het `LockAsync` verwerft de duurzame slot(s) en retourneert een `IDisposable` dat de kritieke sectie beëindigt wanneer verwijderd. Dit `IDisposable` resultaat kan samen `using` met een blok worden gebruikt om een syntactische weergave van de kritieke sectie te krijgen. Wanneer een orchestrator-functie een kritieke sectie invoert, kan slechts één instantie dat codeblok uitvoeren. Alle andere instanties die de kritieke sectie proberen in te voeren, worden geblokkeerd totdat de vorige instantie de kritieke sectie verlaat.

De kritieke sectiefunctie is ook handig voor het coördineren van wijzigingen in duurzame entiteiten. Zie het onderwerp ['Entiteiten coördinatie'](durable-functions-entities.md#entity-coordination) voor meer informatie over kritieke secties.

> [!NOTE]
> Kritieke secties zijn beschikbaar in duurzame functies 2.0 en hoger. Momenteel implementeren alleen .NET-orkestraties deze functie.

### <a name="calling-http-endpoints-durable-functions-2x"></a>HTTP-eindpunten aanroepen (duurzame functies 2.x)

Orchestrator-functies zijn niet toegestaan om I/O uit te voeren, zoals beschreven in [orchestrator-functiecodebeperkingen](durable-functions-code-constraints.md). De typische tijdelijke oplossing voor deze beperking is om elke code die i/o moet doen in een activiteitsfunctie te verpakken. Orkestraties die met externe systemen werken, gebruiken vaak activiteitsfuncties om HTTP-aanroepen te voeren en het resultaat terug te sturen naar de orkestratie.

# <a name="c"></a>[C #](#tab/csharp)

Om dit algemene patroon te vereenvoudigen, `CallHttpAsync` kunnen orchestrator-functies de methode gebruiken om HTTP-API's rechtstreeks aan te roepen.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Naast het ondersteunen van basisaanvraag-/antwoordpatronen ondersteunt de methode automatische afhandeling van veelvoorkomende async HTTP 202-stempatronen en ondersteunt deze ook verificatie met externe services met behulp van [Beheerde identiteiten.](../../active-directory/managed-identities-azure-resources/overview.md)

Zie het [http-functiesartikel](durable-functions-http-features.md) voor meer informatie en gedetailleerde voorbeelden.

> [!NOTE]
> Het rechtstreeks aanroepen van HTTP-eindpunten vanuit orchestrator-functies is beschikbaar in Duurzame functies 2.0 en hoger.

### <a name="passing-multiple-parameters"></a>Meerdere parameters doorgeven

Het is niet mogelijk om meerdere parameters rechtstreeks door te geven aan een activiteitsfunctie. De aanbeveling is om door te geven in een array van objecten of samengestelde objecten.

# <a name="c"></a>[C #](#tab/csharp)

In .NET u ook [ValueTuples-objecten](https://docs.microsoft.com/dotnet/csharp/tuples) gebruiken. Het volgende voorbeeld is met behulp van nieuwe functies van [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) toegevoegd met [C # 7:](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples)

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="activity"></a>Activiteit

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Codebeperkingen voor orchestrator](durable-functions-code-constraints.md)
