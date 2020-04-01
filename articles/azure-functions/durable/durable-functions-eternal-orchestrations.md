---
title: Eeuwige orkestraties in duurzame functies - Azure
description: Meer informatie over het implementeren van eeuwige orkestraties met de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478815"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eeuwige orkestraties in duurzame functies (Azure-functies)

*Eeuwige orkestraties* zijn orkestorfuncties die nooit eindigen. Ze zijn handig wanneer u [duurzame functies](durable-functions-overview.md) wilt gebruiken voor aggregators en elk scenario dat een oneindige lus vereist.

## <a name="orchestration-history"></a>Orkestratiegeschiedenis

Zoals uitgelegd in het [orchestration history](durable-functions-orchestrations.md#orchestration-history) topic, houdt het Sustainable Task Framework de geschiedenis van elke functieorkestratie bij. Deze geschiedenis groeit voortdurend zolang de orchestrator-functie nieuw werk blijft plannen. Als de orchestrator-functie in een oneindige lus gaat en continu werk schema's uitwerkt, kan deze geschiedenis kritisch groot worden en aanzienlijke prestatieproblemen veroorzaken. Het *eeuwige orkestratieconcept* is ontworpen om dit soort problemen te beperken voor toepassingen die oneindige lussen nodig hebben.

## <a name="resetting-and-restarting"></a>Opnieuw instellen en opnieuw starten

In plaats van oneindige lussen te gebruiken, resetten orchestrator-functies hun status door de `ContinueAsNew` (.NET) of `continueAsNew` (JavaScript)-methode van de [orchestration triggerbinding](durable-functions-bindings.md#orchestration-trigger)aan te roepen. Deze methode neemt een enkele JSON-serializable parameter, die de nieuwe input voor de volgende orchestrator functie generatie wordt.

Wanneer `ContinueAsNew` wordt aangeroepen, wordt een bericht voor zichzelf indewachtrijen voordat het wordt afgesloten. Het bericht start de instantie opnieuw met de nieuwe invoerwaarde. Dezelfde instantie-ID wordt bewaard, maar de geschiedenis van de orchestrator-functie wordt effectief afgekapt.

> [!NOTE]
> Het framework Duurzame taak behoudt dezelfde instantie-id, maar maakt intern een nieuwe `ContinueAsNew` *uitvoerings-ID* voor de orchestrator-functie die wordt gereset door . Deze uitvoerings-ID is over het algemeen niet extern blootgesteld, maar het kan nuttig zijn om te weten over bij het debuggen orchestration uitvoering.

## <a name="periodic-work-example"></a>Voorbeeld van periodiek werk

Een voorbeeld voor eeuwige orkestraties is code die periodiek werk voor onbepaalde tijd moet doen.

# <a name="c"></a>[C #](#tab/csharp)

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
> Het vorige C#-voorbeeld is voor Duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Het verschil tussen dit voorbeeld en een functie die door een timer wordt geactiveerd, is dat de triggertijden voor het opschonen hier niet zijn gebaseerd op een planning. Een CRON-schema dat elk uur een functie uitvoert, voert deze bijvoorbeeld uit om 1:00, 2:00, 3:00 enz. In dit voorbeeld, echter, als de opruiming duurt 30 minuten, dan zal het worden gepland om 1:00, 2:30, 4:00, etc. en er is geen kans op overlap.

## <a name="starting-an-eternal-orchestration"></a>Het starten van een eeuwige orkestratie

Gebruik `StartNewAsync` de (.NET) `startNew` of de (JavaScript) methode om een eeuwige orchestration te starten, net zoals je zou elke andere orchestration functie.  

> [!NOTE]
> Als u ervoor moet zorgen dat er een singleton eeuwige orkestratie `id` wordt uitgevoerd, is het belangrijk om dezelfde instantie te behouden bij het starten van de orkestratie. Zie [Instantiebeheer](durable-functions-instance-management.md)voor meer informatie.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> De vorige code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Exit van een eeuwige orkestratie

Als een orchestrator-functie uiteindelijk moet worden voltooid, hoeft `ContinueAsNew` u alleen maar *niet* te bellen en de functie te laten afsluiten.

Als een orchestrator-functie zich in een oneindige `TerminateAsync` lus bevindt `terminate` en moet worden gestopt, gebruikt u de methode (.NET) of (JavaScript) van de [orchestration-clientbinding](durable-functions-bindings.md#orchestration-client) om deze te stoppen. Zie [Instantiebeheer](durable-functions-instance-management.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van singleton orchestrations](durable-functions-singletons.md)
