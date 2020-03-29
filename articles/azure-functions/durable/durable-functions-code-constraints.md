---
title: Beperkingen voor duurzame orchestrator-code - Azure-functies
description: Orchestration-functiereplay en codebeperkingen voor duurzame azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562136"
---
# <a name="orchestrator-function-code-constraints"></a>Beperkingen van orchestrator-functiecode

Duurzame functies is een uitbreiding van [Azure-functies](../functions-overview.md) waarmee u stateful-apps bouwen. U een [orchestrator-functie](durable-functions-orchestrations.md) gebruiken om de uitvoering van andere duurzame functies binnen een functie-app te orkestreren. Orchestrator-functies zijn stateful, betrouwbaar en mogelijk langlopend.

## <a name="orchestrator-code-constraints"></a>Codebeperkingen voor orchestrator

Orchestrator-functies maken gebruik van [event sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) om betrouwbare uitvoering te garanderen en de lokale variabele status te behouden. Het [replay-gedrag](durable-functions-orchestrations.md#reliability) van orchestrator-code creëert beperkingen op het type code dat u schrijven in een orchestrator-functie. Orkestorfuncties moeten bijvoorbeeld *deterministisch*zijn: een orchestratorfunctie wordt meerdere keren afgespeeld en moet telkens hetzelfde resultaat opleveren.

### <a name="using-deterministic-apis"></a>Deterministische API's gebruiken

Deze sectie bevat een aantal eenvoudige richtlijnen die ervoor zorgen dat uw code deterministisch is.

Orchestrator-functies kunnen elke API in hun doeltalen aanroepen. Het is echter belangrijk dat orchestrator-functies alleen deterministische API's noemen. Een *deterministische API* is een API die altijd dezelfde waarde retourneert gezien dezelfde invoer, ongeacht wanneer of hoe vaak deze wordt aangeroepen.

In de volgende tabel worden voorbeelden weergegeven van API's die u moet vermijden omdat ze *niet* deterministisch zijn. Deze beperkingen zijn alleen van toepassing op orchestrator-functies. Andere functietypen hebben dergelijke beperkingen niet.

| API-categorie | Reden | Tijdelijke oplossing |
| ------------ | ------ | ---------- |
| Datums en tijden  | API's die de huidige datum of tijd retourneren, zijn niet deterministisch omdat de geretourneerde waarde voor elke herhaling anders is. | Gebruik`CurrentUtcDateTime` de API in `currentUtcDateTime` .NET of de API in JavaScript, die veilig zijn voor opnieuw afspelen. |
| GUIDs en UUIDs  | API's die een willekeurige GUID of UUID retourneren, zijn niet-deterministisch omdat de gegenereerde waarde voor elke herhaling anders is. | Gebruik `NewGuid` in .NET of `newGuid` in JavaScript om veilig willekeurige GUID's te genereren. |
| Willekeurige getallen | API's die willekeurige getallen retourneren zijn niet deterministisch omdat de gegenereerde waarde voor elke herhaling verschilt. | Gebruik een activiteitsfunctie om willekeurige getallen terug te sturen naar een orkestratie. De retourwaarden van activiteitsfuncties zijn altijd veilig voor herhaling. |
| Bindingen | Invoer- en uitvoerbindingen doen doorgaans I/O en zijn niet-deterministisch. Een orchestrator-functie mag zelfs de [orchestration client](durable-functions-bindings.md#orchestration-client) en [entiteitclientbindingen](durable-functions-bindings.md#entity-client) niet rechtstreeks gebruiken. | Gebruik invoer- en uitvoerbindingen in client- of activiteitsfuncties. |
| Netwerk | Netwerkoproepen hebben betrekking op externe systemen en zijn niet-deterministisch. | Gebruik activiteitsfuncties om netwerkaante-oproepen uit te voeren. Als u een HTTP-gesprek wilt voeren vanuit uw orchestrator-functie, u ook de [duurzame HTTP-API's](durable-functions-http-features.md#consuming-http-apis)gebruiken. |
| API's blokkeren | Het blokkeren `Thread.Sleep` van API's zoals in .NET en vergelijkbare API's kan prestatie- en schaalproblemen veroorzaken voor orchestrator-functies en moeten worden vermeden. In het Azure Functions-gebruiksplan kunnen ze zelfs leiden tot onnodige runtime-kosten. | Gebruik alternatieven voor het blokkeren van API's wanneer deze beschikbaar zijn. Gebruik bijvoorbeeld `CreateTimer` om vertragingen in de uitvoering van orkestratie in te voeren. [Duurzame timervertragingen](durable-functions-timers.md) tellen niet mee voor de uitvoeringstijd van een orchestrator-functie. |
| Async API's | Orchestrator-code mag nooit een synchronisatiebewerking `IDurableOrchestrationContext` starten, `context.df` behalve met behulp van de API of de API van het object. U bijvoorbeeld geen `Task.Run`gebruik `Task.Delay`maken `HttpClient.SendAsync` van , `setTimeout` `setInterval` , en in .NET of en in JavaScript. In het Framework Voor duurzame taken wordt orchestrator-code op één thread uitgevoerd. Het kan niet communiceren met andere threads die kunnen worden aangeroepen door andere async API's. | Een orchestrator-functie moet alleen duurzame async-aanroepen. Activiteitsfuncties moeten elke andere async API-aanroepen uitvoeren. |
| JavaScript-functies async | U JavaScript orchestrator-functies `async` niet declareren omdat de runtime node.js niet garandeert dat asynchrone functies deterministisch zijn. | JavaScript-orchestrator-functies declareren als synchrone generatorfuncties. |
| Threading API's | In het Framework Voor duurzame taken wordt orchestrator-code uitgevoerd op één thread en kan het niet communiceren met andere threads. Het introduceren van nieuwe threads in de uitvoering van een orkestratie kan resulteren in niet-deterministische uitvoering of impasses. | Orchestrator-functies mogen bijna nooit threading API's gebruiken. Vermijd bijvoorbeeld in .NET `ConfigureAwait(continueOnCapturedContext: false)`het gebruik van ; dit zorgt ervoor dat taakvoortzettingen worden uitgevoerd `SynchronizationContext`op het origineel van de orchestrator-functie. Als dergelijke API's nodig zijn, beperk het gebruik ervan tot alleen activiteitsfuncties. |
| Statische variabelen | Vermijd het gebruik van niet-constante statische variabelen in orchestrator-functies omdat hun waarden in de loop van de tijd kunnen veranderen, wat resulteert in niet-deterministisch runtime-gedrag. | Gebruik constanten of beperk het gebruik van statische variabelen tot activiteitsfuncties. |
| Omgevingsvariabelen | Gebruik geen omgevingsvariabelen in orchestrator-functies. Hun waarden kunnen in de loop van de tijd veranderen, wat resulteert in niet-deterministisch runtime-gedrag. | Omgevingsvariabelen moeten alleen worden verwezen vanuit clientfuncties of activiteitsfuncties. |
| Oneindige lussen | Vermijd oneindige lussen in orchestrator-functies. Omdat het framework voor duurzame taken de uitvoeringsgeschiedenis opslaat naarmate de orchestration-functie vordert, kan een oneindige lus ervoor zorgen dat een orchestrator-instantie zonder geheugen komt te zitten. | Gebruik API's zoals `ContinueAsNew` in .NET `continueAsNew` of JavaScript voor oneindige lusscenario's om de functie-uitvoering opnieuw te starten en eerdere uitvoeringsgeschiedenis te verwijderen. |

Hoewel het toepassen van deze beperkingen in eerste instantie misschien moeilijk lijkt, zijn ze in de praktijk gemakkelijk te volgen.

Het Framework Duurzame taken probeert schendingen van de voorgaande regels op te sporen. Als er een overtreding wordt gevonden, wordt in het framework een **uitzondering voor nondeterministicorchestrationexception gehesen.** Dit detectiegedrag zal echter niet alle schendingen opvangen en u moet er niet van afhankelijk zijn.

## <a name="versioning"></a>Versiebeheer

Een duurzame orkestratie kan continu draaien voor dagen, maanden, jaren, of zelfs [eeuwig](durable-functions-eternal-orchestrations.md). Alle code-updates die zijn gemaakt voor apps voor duurzame functies die van invloed zijn op onvoltooide orkestraties, kunnen het replay-gedrag van de orkestraties verbreken. Daarom is het belangrijk om zorgvuldig te plannen bij het maken van updates voor code. Zie [het versieartikel](durable-functions-versioning.md)voor een meer gedetailleerde beschrijving van de versie van uw code.

## <a name="durable-tasks"></a>Duurzame taken

> [!NOTE]
> In dit gedeelte worden de interne implementatiedetails van het kader voor duurzame taken beschreven. U duurzame functies gebruiken zonder deze informatie te kennen. Het is alleen bedoeld om u te helpen het replaygedrag te begrijpen.

Taken die veilig kunnen wachten in orchestrator-functies worden af en toe *duurzame taken*genoemd. Het framework Duurzame taken maakt en beheert deze taken. Voorbeelden hiervan zijn de taken die worden geretourneerd door **CallActivityAsync,** **WaitForExternalEvent**en **CreateTimer** in .NET orchestrator-functies.

Deze duurzame taken worden intern beheerd `TaskCompletionSource` door een lijst met objecten in .NET. Tijdens het opnieuw afspelen worden deze taken gemaakt als onderdeel van de uitvoering van orchestrator-code. Ze zijn klaar als de dispatcher de bijbehorende geschiedenisgebeurtenissen opsomt.

De taken worden synchroon uitgevoerd met behulp van één thread totdat alle geschiedenis is afgespeeld. Duurzame taken die nog niet zijn voltooid aan het einde van de geschiedenis replay hebben de juiste acties uitgevoerd. Een bericht kan bijvoorbeeld in de wachtrij staan om een activiteitsfunctie aan te roepen.

De beschrijving van het runtimegedrag in deze sectie moet u helpen `await` `yield` te begrijpen waarom een orchestrator-functie niet kan worden gebruikt of in een niet-duurzame taak. Er zijn twee redenen: de dispatcherthread kan niet wachten tot de taak is voltooid en elke terugroep van die taak kan mogelijk de trackingstatus van de orchestrator-functie beschadigen. Sommige runtime controles zijn op zijn plaats om te helpen detecteren deze schendingen.

Raadpleeg de [broncode duurzame taak op GitHub](https://github.com/Azure/durabletask)voor meer informatie over hoe het Framework voor duurzame taken orkestloper uitvoert. Zie in het bijzonder [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) en [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het aanroepen van suborchestrations](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Meer informatie over het omgaan met versiebeheer](durable-functions-versioning.md)
