---
title: Sub-Orchestrations voor Durable Functions-Azure
description: Het aanroepen van indelingen van Orchestrations in de Durable Functions-extensie voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: cf160b767ee82701bad4c88d3b83951a3b875296
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614651"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Sub-indelingen in Durable Functions (Azure Functions)

Naast het aanroepen van activiteit functies kunnen Orchestrator-functies andere Orchestrator-functies aanroepen. U kunt bijvoorbeeld een grotere indeling van een bibliotheek met kleinere Orchestrator-functies bouwen. U kunt ook meerdere exemplaren van een Orchestrator-functie parallel uitvoeren.

Een Orchestrator-functie kan een andere Orchestrator-functie aanroepen met behulp van de `CallSubOrchestratorAsync` of de `CallSubOrchestratorWithRetryAsync`-methoden in .NET of de methoden `callSubOrchestrator` of `callSubOrchestratorWithRetry` in Java script. De [fout afhandeling & compensatie](durable-functions-error-handling.md#automatic-retry-on-failure) artikel bevat meer informatie over automatische opnieuw proberen.

Suborchestrator-functies gedragen zich op dezelfde manier als de activiteit functies van het perspectief van de aanroeper. Ze kunnen een waarde Retour neren, een uitzonde ring genereren en kunnen wachten door de bovenliggende Orchestrator-functie. 
## <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u een IoT-scenario (' Internet of Things ') waarin meerdere apparaten moeten worden ingericht. De volgende functie vertegenwoordigt de inrichtings werk stroom die moet worden uitgevoerd voor elk apparaat:

### <a name="c"></a>C#

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

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

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

Deze Orchestrator-functie kan worden gebruikt als-is voor eenmalige inrichting van apparaten of kan deel uitmaken van een grotere indeling. In het laatste geval kan de functie van de bovenliggende Orchestrator instanties van `DeviceProvisioningOrchestration` plannen met de API van `CallSubOrchestratorAsync` (.NET) of `callSubOrchestrator` (Java script).

Hier volgt een voor beeld waarin wordt getoond hoe u meerdere Orchestrator-functies parallel kunt uitvoeren.

### <a name="c"></a>C#

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
> De vorige C# voor beelden zijn voor Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

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

> [!NOTE]
> Sub-indelingen moeten worden gedefinieerd in dezelfde functie-app als de bovenliggende indeling. Als u in een andere functie-app-integratie moet aanroepen en wachten, kunt u overwegen de ingebouwde ondersteuning voor HTTP-Api's en het HTTP 202-polling-consument patroon te gebruiken. Zie het onderwerp [http-functies](durable-functions-http-features.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het instellen van een aangepaste indelings status](durable-functions-custom-orchestration-status.md)