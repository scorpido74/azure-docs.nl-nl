---
title: Functie typen en functies in de uitbrei ding Durable Functions van Azure Functions
description: Meer informatie over de typen functies en rollen die ondersteuning bieden voor functie-to-function-communicatie in een Durable Functions indeling in Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097744"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions typen en functies (Azure Functions)

Durable Functions is een uitbrei ding van [Azure functions](../functions-overview.md). U kunt Durable Functions gebruiken om de functie-uitvoering in de stateful te organiseren. Een duurzame functie is een oplossing die bestaat uit verschillende Azure-functies. Functies kunnen verschillende rollen afspelen in een duurzame functie-indeling. 

In dit artikel vindt u een overzicht van de typen functies die u kunt gebruiken in een Durable Functions indeling. Het artikel bevat enkele algemene patronen die u kunt gebruiken om functies te verbinden. Meer informatie over hoe Durable Functions u kan helpen bij het oplossen van problemen met de ontwikkeling van uw apps.

![Een afbeelding waarin de typen duurzame functies worden weer gegeven][1]  

## <a name="types-of-durable-functions"></a>Typen duurzame functies

U kunt vier duurzame functie typen gebruiken in Azure Functions: Activity, orchestrator, entity en client.

### <a name="activity-functions"></a>Activiteit functies

Activiteiten functies zijn de basis eenheid van het werk in een duurzame functie indeling. Activiteit functies zijn de functies en taken die in het proces zijn georganiseerd. U kunt bijvoorbeeld een duurzame functie maken om een order te verwerken. De taken omvatten het controleren van de inventarisatie, het opladen van de klant en het maken van een verzen ding. Elke taak zou een activiteit functie zijn. 

De activiteit functies zijn niet beperkt tot het type werk dat u kunt uitvoeren. U kunt een activiteit functie schrijven in elke [taal die Durable functions ondersteunt](durable-functions-overview.md#language-support). Het duurzame taak raamwerk garandeert dat elke aangeroepen activiteit functie ten minste één keer wordt uitgevoerd tijdens een indeling.

Gebruik een [activiteit trigger](durable-functions-bindings.md#activity-triggers) om een activiteit functie te activeren. .NET-functies krijgen een [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) als para meter. U kunt de trigger ook binden aan een ander object om in te stellen op invoer van de functie. In Java script kunt u toegang krijgen tot een invoer `<activity trigger binding name>` via de eigenschap van het [ `context.bindings` object](../functions-reference-node.md#bindings).

De functie van de activiteit kan ook waarden retour neren naar de Orchestrator. Als u een groot aantal waarden van een activiteit functie verzendt of retourneert, kunt u [Tuples of matrices](durable-functions-bindings.md#passing-multiple-parameters)gebruiken. U kunt een activiteit functie alleen activeren vanuit een Orchestration-exemplaar. Hoewel een activiteit functie en een andere functie (zoals een functie die door HTTP wordt geactiveerd) bepaalde code kan delen, kan elke functie slechts één trigger hebben.

Zie [Activity functions](durable-functions-bindings.md#activity-triggers)(Engelstalig) voor meer informatie en voor beelden.

### <a name="orchestrator-functions"></a>Orchestrator-functies

Orchestrator-functies beschrijven hoe acties worden uitgevoerd en de volg orde waarin acties worden uitgevoerd. Met Orchestrator-functies wordt de indeling in code (C# of Java script) beschreven, zoals wordt weer gegeven in [Durable functions patronen en technische concepten](durable-functions-concepts.md). Een indeling kan een groot aantal verschillende typen acties hebben, waaronder [activiteiten functies](#activity-functions), [Sub-](#sub-orchestrations)indelingen, [wachten op externe gebeurtenissen](#external-events)en timers [](#durable-timers). Orchestrator-functies kunnen ook communiceren met [entiteits functies](#entity-functions).

Een Orchestrator-functie moet worden geactiveerd door een [Orchestration-trigger](durable-functions-bindings.md#orchestration-triggers).

Een Orchestrator wordt gestart door een [Orchestrator-client](#client-functions). U kunt de Orchestrator activeren vanuit elke bron (HTTP, wachtrij, gebeurtenis stroom). Elk exemplaar van een indeling heeft een exemplaar-id. De exemplaar-id kan automatisch worden gegenereerd (aanbevolen) of door de gebruiker gegenereerd. U kunt de exemplaar-id gebruiken om exemplaren van de indeling te [beheren](durable-functions-instance-management.md) .

Zie [Orchestration triggers](durable-functions-bindings.md#orchestration-triggers)voor meer informatie en voor beelden.

###  <a name="entity-functions"></a>Entiteit functies (preview-versie)

Met entiteits functies worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status, ook wel *duurzame entiteiten*genoemd. Net als Orchestrator functions zijn entiteits functies functies met een speciaal trigger type, *entiteits trigger*. In tegens telling tot Orchestrator-functies hebben entiteits functies geen specifieke code beperkingen. Met entiteits functies wordt ook de status expliciet beheerd in plaats van impliciet de status via de controle stroom te vertegenwoordigen.

> [!NOTE]
> Entiteits functies en gerelateerde functionaliteit zijn alleen beschikbaar in Durable Functions 2,0 en hoger.

Voor meer informatie over entiteits functies raadpleegt u de documentatie over de functie voor het weer geven van [entiteits functies](durable-functions-preview.md#entity-functions) .

### <a name="client-functions"></a>Client functies

Client functies zijn geactiveerde functies waarmee instanties van Orchestrations en entiteiten worden gemaakt en beheerd. Ze zijn in feite het toegangs punt voor interactie met Durable Functions. U kunt een client functie activeren vanuit elke bron (HTTP, wachtrij, gebeurtenis stroom, enz.). Een client functie maakt gebruik van de [Orchestration client-binding](durable-functions-bindings.md#orchestration-client) voor het maken en beheren van duurzame Orchestrations en entiteiten.

Het Basic-voor beeld van een client functie is een door HTTP geactiveerde functie waarmee een Orchestrator-functie wordt gestart. vervolgens wordt een controle status antwoord geretourneerd. Zie [http API URL Discovery](durable-functions-http-api.md#http-api-url-discovery)(Engelstalig) voor een voor beeld.

Zie [Orchestration client](durable-functions-bindings.md#orchestration-client)voor meer informatie en voor beelden.

## <a name="features-and-patterns"></a>Functies en patronen

In de volgende secties worden de functies en patronen van Durable Functions typen beschreven.

### <a name="sub-orchestrations"></a>Onderliggende indelingen

Orchestrator-functies kunnen activiteit functies aanroepen, maar ze kunnen ook andere Orchestrator-functies aanroepen. U kunt bijvoorbeeld een grotere indeling van een bibliotheek van Orchestrator-functies bouwen. U kunt ook meerdere exemplaren van een Orchestrator-functie parallel uitvoeren.

Zie subcontainment voor meer informatie en [](durable-functions-sub-orchestrations.md)voor beelden.

### <a name="durable-timers"></a>Duurzame timers

[Durable functions](durable-functions-overview.md) biedt *duurzame timers* die u kunt gebruiken in Orchestrator-functies voor het implementeren van vertragingen of het instellen van time-outs voor asynchrone acties. Gebruik duurzame timers in Orchestrator-functies in plaats `Thread.Sleep` van `Task.Delay` enC#() `setTimeout()` of `setInterval()` en (Java script).

Zie voor meer informatie en voor beelden [duurzame timers](durable-functions-timers.md).

### <a name="external-events"></a>Externe gebeurtenissen

Orchestrator-functies kunnen wachten op externe gebeurtenissen om een Orchestrator-exemplaar bij te werken. Deze Durable Functions functie is vaak handig voor het verwerken van een menselijke interactie of andere externe retour aanroepen.

Zie [externe gebeurtenissen](durable-functions-external-events.md)voor meer informatie en voor beelden.

### <a name="error-handling"></a>Foutafhandeling

Gebruik code om Durable Functions orchestrations te implementeren. U kunt de functies voor fout afhandeling van de programmeer taal gebruiken. Patronen zoals `try` / werkinuworganisatie`catch` . 

Durable Functions ook worden geleverd met ingebouwde beleids regels voor opnieuw proberen. Een actie kan de activiteiten automatisch uitstellen en opnieuw proberen wanneer er een uitzonde ring optreedt. U kunt nieuwe pogingen gebruiken om tijdelijke uitzonde ringen af te handelen zonder de indeling te annuleren.

Zie [fout afhandeling](durable-functions-error-handling.md)voor meer informatie en voor beelden.

### <a name="cross-function-app-communication"></a>Communicatie tussen verschillende functies en apps

Hoewel een duurzame Orchestration wordt uitgevoerd in de context van één functie-app, kunt u patronen gebruiken om de integratie van verschillende functie-apps te coördineren. Communicatie tussen apps vindt mogelijk plaats via HTTP, maar het gebruik van het duurzame Framework voor elke activiteit houdt in dat u een duurzaam proces voor twee apps kunt onderhouden.

In C# de volgende voor beelden ziet u de indeling en Java script-functie-app-indeling. In elk voor beeld start de ene activiteit de externe indeling. Een andere activiteit haalt de status op en retourneert deze. De Orchestrator wacht totdat de status wordt `Complete` voortgezet.

Hier volgen enkele voor beelden van app-indeling met meerdere functies:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan, maakt u uw [C#](durable-functions-create-first-csharp.md) eerste duurzame functie in of [Java script](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Meer informatie over Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
