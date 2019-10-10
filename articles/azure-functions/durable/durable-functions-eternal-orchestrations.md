---
title: Eeuwige-integratie in Durable Functions-Azure
description: Meer informatie over het implementeren van eeuwige-integratie met behulp van de Durable Functions-extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d96229bb5e3d288915b64e5a7ce29a8651f2a181
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177384"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eeuwige-integraties in Durable Functions (Azure Functions)

*Eeuwige* -indelingen zijn Orchestrator-functies die nooit eindigen. Ze zijn handig wanneer u [Durable functions](durable-functions-overview.md) wilt gebruiken voor aggregators en elk scenario waarvoor een oneindige lus is vereist.

## <a name="orchestration-history"></a>Orchestration-geschiedenis

Zoals uitgelegd in het onderwerp [Orchestration-geschiedenis](durable-functions-orchestrations.md#orchestration-history) , houdt het duurzame taak raamwerk de geschiedenis bij van elke functie indeling. Deze geschiedenis neemt voortdurend toe zolang de Orchestrator-functie blijft werken om nieuwe werkzaamheden te plannen. Als de Orchestrator-functie een oneindige lus doorloopt en voortdurend schema's werken, kan deze geschiedenis kritiek toenemen en aanzienlijke prestatie problemen veroorzaken. Het *eeuwige-Orchestration* -concept is ontworpen om dit soort problemen te verhelpen voor toepassingen die oneindige lussen nodig hebben.

## <a name="resetting-and-restarting"></a>Opnieuw instellen en opnieuw starten

In plaats van een oneindige lus te gebruiken, wordt de status van Orchestrator-functies opnieuw ingesteld door de [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) -methode aan te roepen. Deze methode heeft één JSON-Serializable-para meter, die de nieuwe invoer wordt voor de volgende Orchestrator-functie generatie.

Als `ContinueAsNew` wordt aangeroepen, in het exemplaar een bericht naar zichzelf voordat het wordt afgesloten. Het bericht start het exemplaar opnieuw met de nieuwe invoer waarde. Dezelfde exemplaar-ID wordt bewaard, maar de geschiedenis van de Orchestrator-functie wordt in feite afgekapt.

> [!NOTE]
> Het duurzame taak raamwerk houdt dezelfde exemplaar-ID bij, maar maakt intern een nieuwe *uitvoerings-id* voor de Orchestrator-functie die opnieuw wordt ingesteld door `ContinueAsNew`. Deze uitvoerings-ID wordt over het algemeen niet extern weer gegeven, maar het kan nuttig zijn om te weten wanneer de uitvoering van een fout in de Orchestration is opgetreden.

## <a name="periodic-work-example"></a>Voor beeld van periodieke werk

Een use-case voor eeuwige-indelingen is code die voor onbepaalde tijd periodiek moet worden uitgevoerd.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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
Gebruik de methode [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) om een eeuwige-indeling te starten. Dit wijkt af van het activeren van een andere Orchestration-functie.  

> [!NOTE]
> Als u er zeker van wilt zijn dat er een singleton eeuwige-indeling wordt uitgevoerd, is het belang rijk dat u dezelfde instantie `id` behoudt bij het starten van de indeling. Zie [instance Management](durable-functions-instance-management.md)(Engelstalig) voor meer informatie.

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [OrchestrationClient] DurableOrchestrationClientBase client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Afsluiten vanuit een eeuwige-indeling

Als een Orchestrator-functie uiteindelijk moet worden voltooid, hoeft u *niet* `ContinueAsNew` te bellen en kunt u de functie afsluiten.

Als een Orchestrator-functie zich in een oneindige lus bevindt en moet worden gestopt, gebruikt u de methode [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) om deze te stoppen. Zie [instance Management](durable-functions-instance-management.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van Singleton-indelingen](durable-functions-singletons.md)
