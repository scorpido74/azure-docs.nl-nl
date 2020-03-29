---
title: Suborchestrations voor duurzame functies - Azure
description: Orkestraties aanroepen vanuit orkestraties in de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d4d599063f727510cbf504ea3d121bdabfe001c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261514"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Suborchestrations in duurzame functies (Azure-functies)

Naast het aanroepen van activiteitsfuncties kunnen orchestrator-functies andere orchestrator-functies aanroepen. U bijvoorbeeld een grotere orkestratie bouwen uit een bibliotheek met kleinere orchestrator-functies. U ook meerdere exemplaren van een orchestratorfunctie parallel uitvoeren.

Een orchestrator-functie kan een andere `CallSubOrchestratorAsync` orchestrator-functie aanroepen met behulp van de of de `CallSubOrchestratorWithRetryAsync` methoden in .NET of de of-methoden `callSubOrchestrator` `callSubOrchestratorWithRetry` in JavaScript. Het artikel [Foutafhandeling & compensatie](durable-functions-error-handling.md#automatic-retry-on-failure) bevat meer informatie over automatisch opnieuw proberen.

Suborchestrator-functies gedragen zich net als activiteitsfuncties vanuit het perspectief van de beller. Ze kunnen een waarde retourneren, een uitzondering maken en kunnen worden afgewacht door de bovenliggende orchestrator-functie. 
## <a name="example"></a>Voorbeeld

Het volgende voorbeeld illustreert een IoT-scenario ("Internet of Things") waarbij er meerdere apparaten zijn die moeten worden ingericht. De volgende functie vertegenwoordigt de inrichtingswerkstroom die voor elk apparaat moet worden uitgevoerd:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Deze orchestrator-functie kan worden gebruikt als-is voor eenmalige apparaatinrichting of het kan deel uitmaken van een grotere orkestratie. In het laatste geval kan de bovenliggende `DeviceProvisioningOrchestration` orchestrator-functie instanties plannen voor het gebruik van de `CallSubOrchestratorAsync` (.NET) of `callSubOrchestrator` (JavaScript)-API.

Hier is een voorbeeld dat laat zien hoe meerdere orchestrator functies parallel uit te voeren.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> De vorige C# voorbeelden zijn voor Duurzame Functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Suborchestrations moeten worden gedefinieerd in dezelfde functie-app als de bovenliggende orkestratie. Als u moet bellen en wachten op orkestraties in een andere functie-app, u overwegen de ingebouwde ondersteuning voor HTTP-API's en het http 202-polling-consumentenpatroon te gebruiken. Zie het onderwerp [HTTP-functies voor](durable-functions-http-features.md) meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het instellen van een aangepaste orchestration-status](durable-functions-custom-orchestration-status.md)