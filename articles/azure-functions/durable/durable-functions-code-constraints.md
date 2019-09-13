---
title: Duurzame Orchestrator-code beperkingen-Azure Functions
description: De functie herhalingen en code beperkingen voor Azure Durable Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935766"
---
# <a name="orchestrator-function-code-constraints"></a>Functie code beperkingen van Orchestrator

Durable Functions is een uitbrei ding van [Azure functions](../functions-overview.md) waarmee u stateful toepassingen kunt bouwen. U kunt een [Orchestrator-functie](durable-functions-orchestrations.md) gebruiken om de uitvoering van andere duurzame functies in een functie-app te organiseren. Orchestrator-functies zijn stateful, betrouwbaar en mogelijk lang actief.

## <a name="orchestrator-code-constraints"></a>Orchestrator-code beperkingen

Orchestrator-functies gebruiken [gebeurtenis bronnen](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) om betrouw bare uitvoering te garanderen en om de lokale status van de variabele te onderhouden. Het [gedrag](durable-functions-orchestrations.md#reliability) voor opnieuw afspelen van Orchestrator-code maakt beperkingen voor het type code dat u kunt schrijven in een Orchestrator-functie. Orchestrator-code moet bijvoorbeeld *deterministisch*zijn.  Orchestrator-functies worden meerdere keren herhaald en moeten telkens hetzelfde resultaat opleveren.

De volgende secties bevatten enkele eenvoudige richt lijnen om ervoor te zorgen dat uw code deterministisch is.

### <a name="using-deterministic-apis"></a>Deterministische Api's gebruiken

Orchestrator-functies zijn gratis voor het aanroepen van API'S die ze in hun doel taal willen. Het is echter belang rijk dat Orchestrator-functies alleen *deterministische* api's aanroepen. Een *deterministische API* is een API die altijd exact dezelfde waarde retourneert als dezelfde invoer, ongeacht wanneer of hoe vaak deze wordt aangeroepen.

Hier volgen enkele voor beelden van Api's die moeten worden vermeden, omdat ze *niet* deterministisch zijn. Deze beperkingen gelden alleen voor Orchestrator-functies. Andere functie typen hebben deze beperkingen niet.

| API-categorie | Reason | Tijdelijke oplossing |
| ------------ | ------ | ---------- |
| Datums/tijden  | Api's die de _huidige_ datum of tijd retour neren, zijn niet-deterministisch, omdat de geretourneerde waarde voor elke herhaling verschillend is. | Gebruik de API van [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.net `currentUtcDateTime` ) of (Java script), die veilig is voor opnieuw afspelen. |
| GUID'S/UUID  | Api's die een _wille keurige_ GUID/UUID retour neren, zijn niet-deterministisch, omdat de gegenereerde waarde voor elke herhaling verschillend is. | Gebruik de [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.net) of `newGuid` (Java script) om wille keurige guid's veilig te genereren. |
| Wille keurige getallen | Api's die wille keurige getallen retour neren, zijn niet-deterministisch omdat de gegenereerde waarde voor elke herhaling verschillend is. | Gebruik een functie activiteit om wille keurige getallen te retour neren naar een indeling. De retour waarden van de activiteit functies zijn altijd veilig voor opnieuw afspelen. |
| Bindingen | Invoer-en uitvoer bindingen worden doorgaans I/O en zijn niet-deterministisch. Zelfs de client bindingen van de [Orchestration-client](durable-functions-bindings.md#orchestration-client) en- [entiteit](durable-functions-bindings.md#entity-client) mogen niet rechtstreeks door een Orchestrator-functie worden gebruikt. | Gebruik invoer-en uitvoer bindingen binnen client-of activiteit functies. |
| Netwerk | Netwerk aanroepen zijn vereist voor externe systemen en zijn niet-deterministisch. | Gebruik activiteit functies om netwerk aanroepen te maken. Als u een HTTP-aanroep van uw Orchestrator-functie moet maken, hebt u ook de mogelijkheid om de [duurzame HTTP-api's](durable-functions-http-features.md#consuming-http-apis)te gebruiken. |
| Api's blok keren | Het blok keren van `Thread.Sleep` api's zoals (.net) of andere vergelijk bare api's kan leiden tot prestatie-en schaal problemen voor Orchestrator-functies en moet worden vermeden. In het Azure Functions verbruiks abonnement kunnen ze zelfs leiden tot onnodige runtime kosten. | Gebruik alternatieven om api's te blok keren wanneer deze beschikbaar `CreateTimer` zijn, zoals om vertragingen in de Orchestration-uitvoering uit te voeren. [Duurzame timer](durable-functions-timers.md) vertragingen tellen niet mee voor de uitvoerings tijd van een Orchestrator-functie. |
| Async-Api's | De Orchestrator-code mag **nooit een asynchrone bewerking initiëren** , met uitzonde ring van `context.df` de [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) -API of de API van het object. Bijvoorbeeld Nee `Task.Run`, `Task.Delay` `setTimeout()` of `HttpClient.SendAsync` in .net of en`setInterval()` in Java script. Het duurzame taak raamwerk voert Orchestrator-code uit op één thread en kan niet communiceren met andere threads die kunnen worden gepland door andere async-Api's. | Alleen *duurzame* async-aanroepen moeten worden uitgevoerd door een Orchestrator-functie. Alle andere async-API-aanroepen moeten worden uitgevoerd op basis van de activiteit functies. |
| Asynchrone Java script-functies | Java script Orchestrator-functies kunnen `async` niet worden gebruikt omdat de node. js-runtime niet garandeert dat async-functies deterministisch zijn. | Java script Orchestrator-functies moeten worden gedeclareerd als synchrone Generator functies. |
| Threading-Api's | Het duurzame taak raamwerk voert Orchestrator-code uit op één thread en kan niet communiceren met andere threads. Het introduceren van nieuwe threads in de uitvoering van een Orchestration kan leiden tot niet-deterministische uitvoering of deadlocks. | Threading-Api's moeten vrijwel nooit worden gebruikt in Orchestrator-functies. Als ze nodig zijn, moeten ze beperkt zijn tot activiteiten functies. |
| Statische variabelen | Niet-constante statische variabelen moeten worden vermeden in Orchestrator-functies, omdat hun waarden in de loop van de tijd kunnen veranderen, wat resulteert in niet-deterministisch uitvoerings gedrag. | Gebruik constanten of beperk het gebruik van statische variabelen tot activiteit functies. |
| Omgevingsvariabelen | Gebruik geen omgevings variabelen in Orchestrator-functies. Hun waarden kunnen na verloop van tijd veranderen, wat resulteert in niet-deterministisch uitvoerings gedrag. | Er mag alleen naar omgevings variabelen worden verwezen vanuit client functies of activiteit functies. |
| Oneindige lussen | Oneindige lussen moeten worden vermeden in Orchestrator-functies. Het duurzame taak raamwerk slaat de uitvoerings geschiedenis op als de Orchestration-functie, waardoor een oneindige lus ervoor kan zorgen dat een Orchestrator-exemplaar geen geheugen meer beschikbaar heeft. | Voor oneindige lussen gebruikt u api's zoals [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.net) of `continueAsNew` (Java script) om de uitvoering van de functie opnieuw te starten en de vorige uitvoerings geschiedenis te verwijderen. |

Hoewel deze beperkingen op het eerste gezicht lijken te zijn, kan het voor komen dat ze in de praktijk niet moeilijk te volgen zijn. Het duurzame taak raamwerk probeert schendingen van de bovenstaande regels te detecteren en genereert een `NonDeterministicOrchestrationException`. Dit detectie gedrag is echter het meest geschikt en u moet er niet op vertrouwen.

## <a name="versioning"></a>Versiebeheer

Een duurzame indeling kan continu worden uitgevoerd voor dagen, maanden, jaren of zelfs [eternally](durable-functions-eternal-orchestrations.md). Code-updates die zijn aangebracht in Durable Functions toepassingen die van invloed zijn op niet-voltooide indelingen, kunnen het gedrag van het opnieuw afspelen beëindigen. Het is daarom belang rijk om zorgvuldig te plannen bij het maken van updates voor code. Zie het artikel [versie nummer](durable-functions-versioning.md) voor een gedetailleerde beschrijving van de versie van uw code.

## <a name="durable-tasks"></a>Duurzame taken

> [!NOTE]
> In deze sectie worden de interne implementatie details van het duurzame taak raamwerk beschreven. U kunt Durable Functions gebruiken zonder dat u deze informatie hoeft te weten. Het is alleen bedoeld om u inzicht te geven in het replay-gedrag.

Taken die veilig kunnen worden gewacht in Orchestrator-functies, worden af en toe aangeduid als *duurzame taken*. Deze taken worden gemaakt en beheerd door het duurzame taak raamwerk. Voor beelden zijn de taken die `CallActivityAsync`worden `WaitForExternalEvent`geretourneerd door `CreateTimer` , en in .net Orchestrator-functies.

Deze *duurzame taken* worden intern beheerd met behulp van `TaskCompletionSource` een lijst met objecten in .net. Tijdens het opnieuw afspelen worden deze taken gemaakt als onderdeel van de uitvoering van de Orchestrator-code en worden ze voltooid als de dispatcher de corresponderende geschiedenis gebeurtenissen inventariseert. De uitvoering wordt synchroon uitgevoerd met één thread totdat alle geschiedenis is herhaald. Duurzame taken die niet zijn voltooid door het einde van de replay van de geschiedenis, hebben de juiste acties uitgevoerd. Zo kan een bericht in de wachtrij worden geplaatst om een activiteit functie aan te roepen.

Het uitvoerings gedrag dat hier wordt beschreven, dient u te begrijpen waarom de functie code `await` van `yield` Orchestrator nooit of een niet-duurzame taak moet zijn: de dispatcher-thread kan niet wachten totdat deze is voltooid en eventuele retour aanroepen door deze taak zouden mogelijk de tracerings status van de Orchestrator-functie is beschadigd. Sommige runtime controles zijn aanwezig om te proberen deze schendingen te detecteren.

Als u meer informatie wilt over hoe het duurzame taak raamwerk Orchestrator-functies uitvoert, kunt u het beste de [bron code van de duurzame taak op github](https://github.com/Azure/durabletask)raadplegen. Zie met name [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) en [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het aanroepen van sub-indelingen](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Meer informatie over het verwerken van versie beheer](durable-functions-versioning.md)
