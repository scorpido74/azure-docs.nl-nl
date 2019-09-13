---
title: Duurzame Orchestrations-Azure Functions
description: Inleiding tot de Orchestration-functie voor Azure Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 82c4a27ac2491e668c1d99e2a14b870e82ec5665
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935857"
---
# <a name="durable-orchestrations"></a>Duurzame integraties

Durable Functions is een uitbrei ding van [Azure functions](../functions-overview.md). U kunt een *Orchestrator-functie* gebruiken om de uitvoering van andere duurzame functies in een functie-app te organiseren. Orchestrator-functies hebben de volgende kenmerken:

* Met Orchestrator-functies worden functie werk stromen gedefinieerd met behulp van procedurele code. Er zijn geen declaratieve schema's of ontwerpers nodig.
* Orchestrator-functies kunnen op synchrone en asynchrone wijze andere duurzame functies aanroepen. Uitvoer van aangeroepen functies kan betrouwbaar worden opgeslagen in lokale variabelen.
* Orchestrator-functies zijn duurzaam en betrouwbaar. De voortgang van de uitvoering wordt automatisch gecontroleerd wanneer de functie ' await ' of ' Yielden ' is. Er gaat nooit lokale status verloren wanneer het proces recyclet of de VM opnieuw wordt opgestart.
* Orchestrator-functies kunnen langdurig worden uitgevoerd. De totale levens duur van een *Orchestration-exemplaar* kan seconden, dagen, maanden of nooit eindigend zijn.

In dit artikel vindt u een overzicht van Orchestrator-functies en hoe ze u kunnen helpen bij het oplossen van verschillende ontwikkel uitdagingen voor apps. Als u nog niet bekend bent met de typen functies die beschikbaar zijn in een Durable Functions-app, lees dan eerst het artikel over [duurzame functie typen](durable-functions-types-features-overview.md) .

## <a name="orchestration-identity"></a>Orchestration-identiteit

Elk *exemplaar* van een indeling heeft een exemplaar-id (ook wel een *exemplaar-id*genoemd). Standaard is elke exemplaar-ID een automatisch gegenereerde GUID. Exemplaar-Id's kunnen echter ook een door de gebruiker gegenereerde teken reeks waarde zijn. Elke instantie-ID van de Orchestrator moet uniek zijn binnen een [Task hub](durable-functions-task-hubs.md).

Hier volgen enkele regels voor exemplaar-Id's:

* Exemplaar-Id's moeten tussen 1 en 256 tekens lang zijn.
* Exemplaar-Id's mogen niet beginnen `@`met.
* Exemplaar-id's mogen geen `/`, `\`, `#`, of `?` bevatten.
* Exemplaar-Id's mogen geen besturings tekens bevatten.

> [!NOTE]
> U wordt aangeraden om waar mogelijk automatisch gegenereerde exemplaar-Id's te gebruiken. Door de gebruiker gegenereerde exemplaar-Id's zijn bedoeld voor scenario's waarbij een een-op-een-toewijzing is tussen een Orchestration-exemplaar en een bepaalde externe toepassingsspecifieke entiteit, zoals een aankoop order of een document.

De instantie-ID van een indeling is een vereiste para meter voor de meeste [beheer bewerkingen](durable-functions-instance-management.md)van het exemplaar. Ze zijn ook belang rijk voor diagnostische gegevens, zoals het [zoeken met behulp](durable-functions-diagnostics.md#application-insights) van indelings traceergegevens in Application Insights voor het oplossen van problemen of voor analyse doeleinden. Daarom is het raadzaam om gegenereerde exemplaar-Id's op te slaan op een externe locatie (bijvoorbeeld een Data Base of in toepassings Logboeken), waar u later eenvoudig naar kunt verwijzen.

## <a name="reliability"></a>Betrouwbaarheid

Orchestrator functioneert de uitvoerings status op betrouw bare wijze met behulp van het ontwerp patroon [gebeurtenis sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . In plaats van de huidige status van een indeling rechtstreeks op te slaan, gebruikt het duurzame taak raamwerk een archief dat alleen kan worden toegevoegd om de volledige reeks acties vast te leggen die de functie indeling neemt. Een archief met alleen-lezen heeft veel voor delen ten opzichte van "dumping" van de volledige runtime status. Voor delen zijn onder andere betere prestaties, schaal baarheid en reactie snelheid. U krijgt ook de uiteindelijke consistentie voor transactionele gegevens en volledige controle sporen en geschiedenis. De controle spoor ondersteunt betrouw bare veredelings acties.

Durable Functions gebeurtenis bronnen worden op transparante wijze gebruikt. Achter de schermen levert de `await` operatorC#() `yield` of (Java script) in een Orchestrator-functie de controle over van de Orchestrator-thread terug naar de verzender van het duurzame taak raamwerk. De dispatcher voert vervolgens nieuwe acties uit die de Orchestrator-functie heeft gepland (zoals het aanroepen van een of meer onderliggende functies of het plannen van een duurzame timer) op de opslag. De transparante doorvoer actie wordt toegevoegd aan de uitvoerings geschiedenis van het Orchestration-exemplaar. De geschiedenis wordt opgeslagen in een opslag tabel. Met de actie door voeren worden vervolgens berichten toegevoegd aan een wachtrij om de werkelijke hoeveelheid werk te plannen. Op dit moment kan de Orchestrator-functie uit het geheugen worden verwijderd.

Wanneer een Orchestration-functie meer werk heeft gekregen (bijvoorbeeld wanneer er een antwoord bericht wordt ontvangen of een duurzame timer verloopt), wordt de volledige functie door de Orchestrator geactiveerd en opnieuw uitgevoerd vanaf de start om de lokale status opnieuw op te bouwen. Als de code tijdens het opnieuw afspelen probeert een functie aan te roepen (of andere async-werkzaamheden uit te voeren), wordt in het duurzame taak raamwerk de uitvoerings geschiedenis van de huidige indeling geraadpleegd. Als wordt gedetecteerd dat de [functie activiteit](durable-functions-types-features-overview.md#activity-functions) al is uitgevoerd en een resultaat oplevert, wordt het resultaat van die functie opnieuw afgespeeld en wordt de Orchestrator-code nog steeds uitgevoerd. Opnieuw afspelen gaat door totdat de functie code is voltooid of totdat het nieuwe async-werk is gepland.

> [!NOTE]
> Om het patroon voor opnieuw afspelen goed en betrouwbaar te laten werken, moet Orchestrator-functie code *deterministisch*zijn. Zie het onderwerp [functie code beperkingen van Orchestrator](durable-functions-code-constraints.md) voor meer informatie over code beperkingen voor Orchestrator-functies.

> [!NOTE]
> Als een Orchestrator-functie logboek berichten verzendt, kan het gedrag voor opnieuw afspelen ertoe leiden dat dubbele logboek berichten worden verzonden. Raadpleeg het onderwerp over [logboek registratie](durable-functions-diagnostics.md#logging) voor meer informatie over waarom dit gedrag optreedt en hoe u dit kunt omzeilen.

## <a name="orchestration-history"></a>Orchestration-geschiedenis

Het gedrag gebeurtenis-sourcing van het duurzame taak raamwerk is nauw gekoppeld aan de Orchestrator-functie code die u schrijft. Stel dat u een Orchestrator-functie voor het koppelen van activiteiten hebt, zoals C# de volgende Orchestrator-functie:

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

Als u codeert in Java script, kan de Orchestrator-functie van de activiteiten keten eruitzien als de volgende voorbeeld code:

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

Bij elke `await` (C#)- `yield` of (Java script)-instructie bewaart het duurzame taak raamwerk de uitvoerings status van de functie in een duurzame opslag back-end (doorgaans Azure Table Storage). Deze status is wat de *Orchestration-geschiedenis*wordt genoemd.

### <a name="history-table"></a>Geschiedenis tabel

Normaal gesp roken doet het duurzame taak raamwerk het volgende op elk controle punt:

1. Hiermee slaat u de uitvoerings geschiedenis op in Azure Storage tabellen.
2. In-berichten voor functies die de Orchestrator wil aanroepen.
3. In berichten voor de Orchestrator zelf &mdash; , bijvoorbeeld duurzame timer berichten.

Zodra het controle punt is voltooid, kan de Orchestrator-functie uit het geheugen worden verwijderd totdat er meer werk te doen.

> [!NOTE]
> Azure Storage biedt geen transactionele garanties tussen het opslaan van gegevens in tabel opslag en wacht rijen. Voor het afhandelen van fouten gebruikt de Durable Functions-opslag provider *uiteindelijke consistentie* patronen. Deze patronen zorgen ervoor dat er geen gegevens verloren gaan als er een storing optreedt in het midden van een controle punt.

Na voltooiing ziet de geschiedenis van de eerder weer gegeven functie er ongeveer uit zoals in de volgende tabel in azure Table Storage (afgekort voor afbeeldings doeleinden):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Invoer | Name             | Resultaat                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201 Z |       |                  | "" "Hallo Tokyo!" "                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" "Hallo Seattle!" ""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919 Z |       |                  | "" Hallo Londen! "" "                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044 Z |       |                  | "[" "Hallo Tokyo!" "," "Hallo Seattle!" "," "Hallo Londen!" "]" | Voltooid           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044 Z |       |                  |                                                           |                     |

Enkele opmerkingen over de kolom waarden:

* **PartitionKey**: Bevat de exemplaar-ID van de indeling.
* Type **gebeurtenis**: Vertegenwoordigt het type van de gebeurtenis. Dit kan een van de volgende typen zijn:
  * **OrchestrationStarted**: De Orchestrator-functie is hervat vanaf een wachtend of wordt voor de eerste keer uitgevoerd. De `Timestamp` kolom wordt gebruikt om de deterministische waarde voor de [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) -API in te vullen.
  * **ExecutionStarted**: De Orchestrator-functie is voor de eerste keer gestart. Deze gebeurtenis bevat ook de functie-invoer in `Input` de kolom.
  * **TaskScheduled**: Er is een activiteit functie gepland. De naam van de activiteit functie wordt vastgelegd in de `Name` kolom.
  * **TaskCompleted**: De functie activity is voltooid. Het resultaat van de functie bevindt `Result` zich in de kolom.
  * **TimerCreated**: Er is een duurzame Timer gemaakt. De `FireAt` kolom bevat de geplande UTC-tijd waarop de timer verloopt.
  * **TimerFired**: Een duurzame timer wordt geactiveerd.
  * **EventRaised**: Er is een externe gebeurtenis verzonden naar het Orchestrator-exemplaar. De `Name` kolom bevat de naam van de gebeurtenis en de `Input` kolom legt de payload van de gebeurtenis vast.
  * **OrchestratorCompleted**: De Orchestrator-functie is gewacht.
  * **ContinueAsNew**: De Orchestrator-functie is voltooid en opnieuw opgestart met de nieuwe status. De `Result` kolom bevat de waarde, die wordt gebruikt als invoer in het opnieuw gestarte exemplaar.
  * **ExecutionCompleted**: De Orchestrator-functie is voltooid (of mislukt). De uitvoer van de functie of de fout details worden opgeslagen in de `Result` kolom.
* **Tijds tempel**: De UTC-tijds tempel van de geschiedenis gebeurtenis.
* **Naam**: De naam van de functie die is aangeroepen.
* **Invoer**: De invoer in JSON-indeling van de functie.
* **Resultaat**: De uitvoer van de functie; dat wil zeggen de retour waarde.

> [!WARNING]
> Hoewel het handig is als een hulp programma voor fout opsporing, neemt u geen afhankelijkheid op in deze tabel. Dit kan veranderen als de Durable Functions extensie wordt ontwikkeld.

Telkens wanneer de functie wordt hervat vanuit een `await` (C#) of `yield` (Java script), voert het duurzame taak raamwerk de Orchestrator-functie opnieuw uit. Bij elke herhaling wordt de uitvoerings geschiedenis geraadpleegd om te bepalen of de huidige async-bewerking is uitgevoerd.  Als de bewerking is uitgevoerd, wordt de uitvoer van deze bewerking direct opnieuw afgespeeld en verplaatst naar de volgende `await` (C#) of `yield` (Java script). Dit proces wordt voortgezet totdat de volledige geschiedenis opnieuw is afgespeeld. Zodra de huidige geschiedenis opnieuw is afgespeeld, worden de lokale variabelen hersteld naar de vorige waarden.

## <a name="features-and-patterns"></a>Functies en patronen

In de volgende secties worden de functies en patronen van Orchestrator-functies beschreven.

### <a name="sub-orchestrations"></a>Onderliggende indelingen

Orchestrator-functies kunnen activiteiten functies aanroepen, maar ook andere Orchestrator-functies. U kunt bijvoorbeeld een grotere indeling van een bibliotheek van Orchestrator-functies bouwen. U kunt ook meerdere exemplaren van een Orchestrator-functie parallel uitvoeren.

Zie het artikel over de [Subcontainment](durable-functions-sub-orchestrations.md) voor meer informatie en voor beelden.

### <a name="durable-timers"></a>Duurzame timers

Indelingen kunnen *duurzame timers* plannen voor het implementeren van vertragingen of het instellen van time-outverwerking voor asynchrone acties. Gebruik duurzame timers in Orchestrator-functies in plaats `Thread.Sleep` van `Task.Delay` enC#() `setTimeout()` of `setInterval()` en (Java script).

Zie het artikel over de [duurzame timers](durable-functions-timers.md) voor meer informatie en voor beelden.

### <a name="external-events"></a>Externe gebeurtenissen

Orchestrator-functies kunnen wachten op externe gebeurtenissen om een Orchestrator-exemplaar bij te werken. Deze Durable Functions functie is vaak handig voor het verwerken van een menselijke interactie of andere externe retour aanroepen.

Zie het artikel over [externe gebeurtenissen](durable-functions-external-events.md) voor meer informatie en voor beelden.

### <a name="error-handling"></a>Foutafhandeling

Orchestrator-functies kunnen de functies voor fout afhandeling van de programmeer taal gebruiken. Bestaande patronen zoals `try` /wordenondersteundin deindelingscode`catch` .

Orchestrator-functies kunnen ook beleid voor opnieuw proberen toevoegen aan de functies van de activiteit of de suborchestrator die ze aanroepen. Als een activiteit of suborchestrator-functie mislukt met een uitzonde ring, kan het opgegeven beleid voor opnieuw proberen automatisch worden vertraagd en de uitvoering tot een opgegeven aantal keren opnieuw proberen.

> [!NOTE]
> Als er sprake is van een onverwerkte uitzonde ring in een Orchestrator-functie, wordt het Orchestrator `Failed` -exemplaar voltooid in een status. Een Orchestrator-exemplaar kan niet opnieuw worden uitgevoerd als dit is mislukt.

Zie het artikel over de [fout afhandeling](durable-functions-error-handling.md) voor meer informatie en voor beelden.

### <a name="critical-sections"></a>Essentiële secties

Indelings instanties worden in één thread gestroomd, zodat u zich geen zorgen hoeft te maken over race voorwaarden *binnen* een indeling. Race conditions zijn echter mogelijk wanneer er met de integratie met externe systemen wordt gecommuniceerd. Voor het beperken van race-voor waarden bij interactie met externe systemen, kunnen Orchestrator-functies *essentiële secties* definiëren `LockAsync` met behulp van een methode in .net.

De volgende voorbeeld code toont een Orchestrator-functie die een kritieke sectie definieert. De sectie kritiek wordt met behulp `LockAsync` van de-methode ingevoerd. Voor deze methode moet een of meer verwijzingen naar een [duurzame entiteit](durable-functions-entities.md)worden door gegeven, wat blijvend de vergrendelings status beheert. In slechts één exemplaar van deze indeling kan de code in de sectie kritiek per keer worden uitgevoerd.

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

Het `LockAsync` verwerft de duurzame vergren deling (en) `IDisposable` en retourneert een die het kritieke gedeelte afbreekt wanneer het wordt verwijderd. Dit `IDisposable` resultaat kan worden gebruikt in combi natie `using` met een blok om een syntaxis weergave van de sectie kritiek te verkrijgen. Wanneer een Orchestrator-functie een kritieke sectie invoert, kan slechts één exemplaar dat code blok uitvoeren. Alle andere instanties die proberen de sectie kritiek in te voeren, worden geblokkeerd totdat het vorige exemplaar de sectie kritiek verlaat.

De functie essentiële sectie is ook handig voor het coördineren van wijzigingen aan duurzame entiteiten. Zie het onderwerp [' entiteits coördinatie ' in duurzame entiteiten](durable-functions-entities.md#entity-coordination) voor meer informatie over kritieke secties.

> [!NOTE]
> Essentiële secties zijn beschikbaar in Durable Functions 2,0 en hoger. Op dit moment wordt deze functie alleen geïmplementeerd door .NET-integratie.

### <a name="calling-http-endpoints"></a>HTTP-eind punten aanroepen

Orchestrator-functies zijn niet toegestaan voor I/O, zoals beschreven in de [functie code beperkingen van Orchestrator](durable-functions-code-constraints.md). De gebruikelijke tijdelijke oplossing voor deze beperking is het verpakken van code die I/O in een activiteit functie moet uitvoeren. Integraties die communiceren met externe systemen gebruiken vaak activiteit functies om HTTP-aanroepen te maken en het resultaat te retour neren naar de indeling.

Om dit algemene patroon te vereenvoudigen, kunnen Orchestrator-functies de `CallHttpAsync` methode in .net gebruiken om rechtstreeks http-api's aan te roepen. Naast het ondersteunen van basis patronen `CallHttpAsync` voor aanvraag/antwoord ondersteunt automatische verwerking van veelvoorkomende asynchrone http 202-polling patronen en wordt ook verificatie met externe services met [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md)ondersteund.

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

Zie het artikel [http-functies](durable-functions-http-features.md) voor meer informatie en voor meer gedetailleerde voor beelden.

> [!NOTE]
> Het aanroepen van HTTP-eind punten rechtstreeks vanuit Orchestrator-functies is beschikbaar in Durable Functions 2,0 en hoger. Op dit moment wordt deze functie alleen geïmplementeerd door .NET-integratie.

### <a name="passing-multiple-parameters"></a>Meerdere para meters door geven

Het is niet mogelijk om rechtstreeks meerdere para meters door te geven aan een activiteit functie. De aanbeveling is om een matrix met objecten door te geven of om [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) -objecten in .net te gebruiken.

In het volgende voor beeld worden nieuwe functies van [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) gebruikt die zijn toegevoegd met [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] DurableActivityContext inputs)
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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Orchestrator-code beperkingen](durable-functions-code-constraints.md)
