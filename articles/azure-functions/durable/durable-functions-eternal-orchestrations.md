---
title: Eeuwige-integratie in Durable Functions-Azure
description: Meer informatie over het implementeren van eeuwige-integratie met behulp van de Durable Functions-extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 99f57f2e0b34f2e596ff9cf1a872650228ef0acd
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614854"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eeuwige-integraties in Durable Functions (Azure Functions)

*Eeuwige* -indelingen zijn Orchestrator-functies die nooit eindigen. Ze zijn handig wanneer u [Durable functions](durable-functions-overview.md) wilt gebruiken voor aggregators en elk scenario waarvoor een oneindige lus is vereist.

## <a name="orchestration-history"></a>Orchestration-geschiedenis

Zoals uitgelegd in het onderwerp [Orchestration-geschiedenis](durable-functions-orchestrations.md#orchestration-history) , houdt het duurzame taak raamwerk de geschiedenis bij van elke functie indeling. Deze geschiedenis neemt voortdurend toe zolang de Orchestrator-functie blijft werken om nieuwe werkzaamheden te plannen. Als de Orchestrator-functie een oneindige lus doorloopt en voortdurend schema's werken, kan deze geschiedenis kritiek toenemen en aanzienlijke prestatie problemen veroorzaken. Het *eeuwige-Orchestration* -concept is ontworpen om dit soort problemen te verhelpen voor toepassingen die oneindige lussen nodig hebben.

## <a name="resetting-and-restarting"></a>Opnieuw instellen en opnieuw starten

In plaats van een oneindige lus te gebruiken, wordt de status door Orchestrator-functies opnieuw ingesteld door de `ContinueAsNew` (.NET) of `continueAsNew` (Java script)-methode van de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger)aan te roepen. Deze methode heeft één JSON-Serializable-para meter, die de nieuwe invoer wordt voor de volgende Orchestrator-functie generatie.

Als `ContinueAsNew` wordt aangeroepen, in het exemplaar een bericht naar zichzelf voordat het wordt afgesloten. Het bericht start het exemplaar opnieuw met de nieuwe invoer waarde. Dezelfde exemplaar-ID wordt bewaard, maar de geschiedenis van de Orchestrator-functie wordt in feite afgekapt.

> [!NOTE]
> Het duurzame taak raamwerk houdt dezelfde exemplaar-ID bij, maar maakt intern een nieuwe *uitvoerings-id* voor de Orchestrator-functie die opnieuw wordt ingesteld door `ContinueAsNew`. Deze uitvoerings-ID wordt over het algemeen niet extern weer gegeven, maar het kan nuttig zijn om te weten wanneer de uitvoering van een fout in de Orchestration is opgetreden.

## <a name="periodic-work-example"></a>Voor beeld van periodieke werk

Een use-case voor eeuwige-indelingen is code die voor onbepaalde tijd periodiek moet worden uitgevoerd.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> Het vorige C# voor beeld is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

Het verschil tussen dit voor beeld en een door een timer geactiveerde functie is dat hier niet op basis van een schema wordt geactiveerd. Zo kan een CRON-schema waarmee een functie elk uur wordt uitgevoerd, worden uitgevoerd op 1:00, 2:00, 3:00 enz. Dit kan mogelijk worden uitgevoerd in overlappende problemen. Als het opschonen echter 30 minuten duurt, wordt het gepland op 1:00, 2:30, 4:00 enzovoort. er is geen kans op overlap ping.

## <a name="starting-an-eternal-orchestration"></a>Een eeuwige-indeling starten

Gebruik de `StartNewAsync` (.NET) of de methode `startNew` (Java script) om een eeuwige-indeling te starten, net als bij andere Orchestration-functies.  

> [!NOTE]
> Als u er zeker van wilt zijn dat er een singleton eeuwige-indeling wordt uitgevoerd, is het belang rijk dat u dezelfde instantie `id` houdt bij het starten van de indeling. Zie [instance Management](durable-functions-instance-management.md)(Engelstalig) voor meer informatie.

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

## <a name="exit-from-an-eternal-orchestration"></a>Afsluiten vanuit een eeuwige-indeling

Als een Orchestrator-functie uiteindelijk moet worden voltooid, hoeft u *niet* `ContinueAsNew` aan te roepen en kunt u de functie afsluiten.

Als een Orchestrator-functie zich in een oneindige lus bevindt en moet worden gestopt, gebruikt u de methode `TerminateAsync` (.NET) of `terminate` (Java script) van de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) om deze te stoppen. Zie [instance Management](durable-functions-instance-management.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van Singleton-indelingen](durable-functions-singletons.md)
