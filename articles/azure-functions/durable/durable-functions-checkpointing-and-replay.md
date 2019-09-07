---
title: Controle punten en herhaling in Durable Functions-Azure
description: Meer informatie over het werken met controle punten en antwoorden in de Durable Functions extensie voor Azure Functions.
services: functions
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 5d0527de556c25a1d369d7b22c3f62579bc508f0
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735251"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Controle punten en opnieuw afspelen in Durable Functions (Azure Functions)

Een van de belangrijkste kenmerken van Durable Functions is **betrouw bare uitvoering**. Orchestrator-functies en-activiteit functies kunnen worden uitgevoerd op verschillende Vm's in een Data Center en deze Vm's of de onderliggende netwerk infrastructuur is niet 100% betrouwbaar.

Ondanks deze methode zorgt Durable Functions voor een betrouw bare uitvoering van Orchestrations. Dit doet u door opslag wachtrijen te gebruiken voor het aanroepen van de functie en door regel matige controle van uitvoerings geschiedenis in opslag tabellen (met behulp van een Cloud ontwerp patroon bekend als [gebeurtenis bronnen](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Deze geschiedenis kan vervolgens opnieuw worden afgespeeld om de status in het geheugen van een Orchestrator-functie automatisch opnieuw samen te stellen.

## <a name="orchestration-history"></a>Orchestration-geschiedenis

Stel dat u de volgende Orchestrator-functie hebt:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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

Bij elke `await` (C#)- `yield` of (Java script)-instructie bewaart het duurzame taak raamwerk de uitvoerings status van de functie in tabel opslag. Deze status is wat de *Orchestration-geschiedenis*wordt genoemd.

## <a name="history-table"></a>Geschiedenis tabel

Normaal gesp roken doet het duurzame taak raamwerk het volgende op elk controle punt:

1. Hiermee slaat u de uitvoerings geschiedenis op in Azure Storage tabellen.
2. In-berichten voor functies die de Orchestrator wil aanroepen.
3. In berichten voor de Orchestrator zelf &mdash; , bijvoorbeeld duurzame timer berichten.

Zodra het controle punt is voltooid, kan de Orchestrator-functie uit het geheugen worden verwijderd totdat er meer werk te doen.

> [!NOTE]
> Azure Storage biedt geen transactionele garanties tussen het opslaan van gegevens in tabel opslag en wacht rijen. Voor het afhandelen van fouten gebruikt de Durable Functions-opslag provider *uiteindelijke consistentie* patronen. Deze patronen zorgen ervoor dat er geen gegevens verloren gaan als er een storing optreedt in het midden van een controle punt.

Na voltooiing ziet de geschiedenis van de eerder weer gegeven functie er ongeveer als volgt uit in azure Table Storage (afgekort voor de afbeeldings doeleinden):

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

Telkens wanneer de functie wordt hervat vanuit een `await` (C#) of `yield` (Java script), voert het duurzame taak raamwerk de Orchestrator-functie opnieuw uit. Bij elke opnieuw uitvoeren wordt de uitvoerings geschiedenis geraadpleegd om te bepalen of de huidige async-bewerking is uitgevoerd.  Als de bewerking is uitgevoerd, wordt de uitvoer van deze bewerking direct opnieuw afgespeeld en verplaatst naar de volgende `await` (C#) of `yield` (Java script). Dit proces wordt voortgezet totdat de volledige geschiedenis is herhaald, waarbij alle lokale variabelen in de Orchestrator-functie worden hersteld naar hun vorige waarden.

## <a name="orchestrator-code-constraints"></a>Orchestrator-code beperkingen

Het gedrag voor opnieuw afspelen maakt beperkingen voor het type code dat kan worden geschreven in een Orchestrator-functie:

* Orchestrator-code moet **deterministisch**zijn. Het wordt meerdere keren herhaald en moet telkens hetzelfde resultaat opleveren. Er zijn bijvoorbeeld geen directe aanroepen om de huidige datum/tijd op te halen, wille keurige getallen op te halen, wille keurige GUID'S te genereren of externe eind punten aan te roepen.

  Als Orchestrator code de huidige datum/tijd moet verkrijgen, moet deze gebruikmaken van de API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.net) of `currentUtcDateTime` (Java script), die veilig is voor replay.

  Als Orchestrator-code een wille keurige GUID moet genereren, moet deze gebruikmaken van de [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.net) `newGuid` of de (Java script) API, die veilig is voor replay.

   Met uitzonde ring van deze speciale gevallen moeten niet-deterministische bewerkingen worden uitgevoerd in de activiteit functies. Dit omvat alle interactie met andere invoer-of uitvoer bindingen. Dit zorgt ervoor dat alle niet-deterministische waarden eenmaal worden gegenereerd bij de eerste uitvoering en worden opgeslagen in de uitvoerings geschiedenis. Volgende uitvoeringen gebruiken de opgeslagen waarde vervolgens automatisch.

* Orchestrator-code moet **niet worden geblokkeerd**. Dit betekent bijvoorbeeld dat er geen I/O-en geen- `Thread.Sleep` aanroepen naar (.net) of equivalente api's zijn.

  Als een Orchestrator moet worden uitgesteld, kan deze de [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.net) of `createTimer` (Java script) API gebruiken.

* De Orchestrator-code mag **nooit een asynchrone bewerking initiëren** , met uitzonde ring van `context.df` de [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) -API of de API van het object. Bijvoorbeeld Nee `Task.Run`, `Task.Delay` `setTimeout()` of `HttpClient.SendAsync` in .net of en`setInterval()` in Java script. Het duurzame taak raamwerk voert Orchestrator-code uit op één thread en kan niet communiceren met andere threads die kunnen worden gepland door andere async-Api's. Als dit gebeurt, `InvalidOperationException` wordt er een uitzonde ring gegenereerd.

> [!NOTE]
> De [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -API voert asynchrone I/O uit, wat niet is toegestaan in een Orchestrator-functie en kan alleen worden gebruikt in niet-Orchestrator-functies.

* **Oneindige lussen moeten worden vermeden** in Orchestrator-code. Omdat het duurzame taak raamwerk uitvoerings geschiedenis opslaat als de Orchestration-functie wordt uitgevoerd, kan een oneindige lus ertoe leiden dat een Orchestrator-exemplaar geen geheugen meer beschikbaar heeft. Voor oneindige lussen gebruikt u api's zoals [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.net) of `continueAsNew` (Java script) om de uitvoering van de functie opnieuw te starten en de vorige uitvoerings geschiedenis te verwijderen.

* Java script Orchestrator-functies mogen `async`niet zijn. Ze moeten worden gedeclareerd als synchrone Generator functies.

Hoewel deze beperkingen op het eerste gezicht lijken te zijn, kan het voor komen dat ze in de praktijk niet moeilijk te volgen zijn. Het duurzame taak raamwerk probeert schendingen van de bovenstaande regels te detecteren en genereert een `NonDeterministicOrchestrationException`. Dit detectie gedrag is echter het meest geschikt en u moet er niet op vertrouwen.

> [!NOTE]
> Al deze regels zijn alleen van toepassing op functies die worden geactiveerd `orchestrationTrigger` door de binding. Activiteit functies die worden geactiveerd door `activityTrigger` de binding en functies die gebruikmaken van `orchestrationClient` de binding, hebben geen enkele beperking.

## <a name="durable-tasks-net"></a>Duurzame taken (.NET)

> [!NOTE]
> In deze sectie worden de interne implementatie details van het duurzame taak raamwerk beschreven. U kunt Durable Functions gebruiken zonder dat u deze informatie hoeft te weten. Het is alleen bedoeld om u inzicht te geven in het replay-gedrag.

Taken die veilig kunnen worden gewacht in Orchestrator-functies, worden af en toe aangeduid als *duurzame taken*. Dit zijn taken die door het duurzame taak raamwerk worden gemaakt en beheerd. Voor beelden zijn de taken die `CallActivityAsync`worden `WaitForExternalEvent`geretourneerd door `CreateTimer`, en.

Deze *duurzame taken* worden intern beheerd met behulp van `TaskCompletionSource` een lijst met objecten. Tijdens het opnieuw afspelen worden deze taken gemaakt als onderdeel van de uitvoering van de Orchestrator-code en worden ze voltooid als de dispatcher de corresponderende geschiedenis gebeurtenissen inventariseert. Dit wordt allemaal synchroon uitgevoerd met één thread totdat alle geschiedenis is herhaald. Duurzame taken die niet zijn voltooid door het einde van de replay van de geschiedenis, hebben de juiste acties uitgevoerd. Zo kan een bericht in de wachtrij worden geplaatst om een activiteit functie aan te roepen.

Het uitvoerings gedrag dat hier wordt beschreven, dient u te begrijpen waarom de functie code `await` van Orchestrator nooit een niet-duurzame taak mag hebben: de dispatcher-thread kan niet wachten totdat deze is voltooid en eventuele retour aanroepen voor deze taak kunnen het volgen mogelijk beschadigen de status van de Orchestrator-functie. Sommige runtime controles zijn aanwezig om dit te voor komen.

Als u meer informatie wilt over hoe het duurzame taak raamwerk Orchestrator-functies uitvoert, kunt u het beste de [bron code van de duurzame taak op github](https://github.com/Azure/durabletask)raadplegen. Zie met name [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) en [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over instantie beheer](durable-functions-instance-management.md)