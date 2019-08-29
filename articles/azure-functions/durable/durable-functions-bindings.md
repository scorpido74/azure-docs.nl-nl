---
title: Bindingen voor Durable Functions-Azure
description: Triggers en bindingen gebruiken voor de uitbrei ding van de Durable Functions voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbd645ef9f5e687e71ce110fc84b8342e31defed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087538"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindingen voor Durable Functions (Azure Functions)

De uitbrei ding [Durable functions](durable-functions-overview.md) introduceert twee nieuwe trigger bindingen die de uitvoering van Orchestrator-en activiteit functies regelen. Er wordt ook een uitvoer binding geïntroduceerd die fungeert als een client voor de Durable Functions runtime.

## <a name="orchestration-triggers"></a>Orchestration-triggers

Met de Orchestration-trigger kunt u duurzame Orchestrator-functies ontwerpen. Deze trigger ondersteunt het starten van nieuwe Orchestrator-functie instanties en het hervatten van bestaande Orchestrator-functie instanties die ' wachten op ' een taak.

Wanneer u de Visual Studio-hulpprogram ma's voor Azure Functions gebruikt, wordt de Orchestration-trigger geconfigureerd met het kenmerk [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .net.

Wanneer u Orchestrator-functies schrijft in script talen (bijvoorbeeld Java script of C# scripting), wordt de Orchestration-trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van het bestand *Function. json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`is de naam van de indeling. Dit is de waarde die clients moeten gebruiken wanneer ze nieuwe exemplaren van deze Orchestrator-functie willen starten. Deze eigenschap is optioneel. Als u niets opgeeft, wordt de naam van de functie gebruikt.

Intern deze trigger roept een reeks wacht rijen aan in het standaard opslag account voor de functie-app. Deze wacht rijen zijn interne implementatie details van de uitbrei ding. Daarom worden ze niet expliciet geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Gedrag activeren

Hier volgen enkele opmerkingen over de Orchestration-trigger:

* **Eén threading** : er wordt één dispatcher-thread gebruikt voor alle Orchestrator-functies die worden uitgevoerd op één exemplaar van een host. Daarom is het belang rijk om ervoor te zorgen dat de functie code van Orchestrator efficiënt is en geen I/O uitvoert. Het is ook belang rijk om ervoor te zorgen dat deze thread geen async-werk doet, behalve wanneer u op Durable Functions-specifieke taak typen wacht.
* **Poison-Message Handling** -er is geen ondersteuning voor Poison-berichten in Orchestration-triggers.
* **Bericht zichtbaarheid** : indelings trigger berichten worden niet in de wachtrij geplaatst en blijven onzichtbaar voor een Configureer bare duur. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : retour waarden worden GESERIALISEERD in JSON en persistent gemaakt in de Orchestration-geschiedenis tabel in azure Table Storage. Deze retour waarden kunnen worden opgevraagd door de Orchestrator-client binding, die later wordt beschreven.

> [!WARNING]
> Orchestrator-functies mogen nooit alle invoer-of uitvoer bindingen gebruiken, behalve de Orchestration-trigger binding. Als u dit doet, is het mogelijk om problemen met de extensie duurzame taak te veroorzaken, omdat deze bindingen de enkelvoudige threading en I/O-regels niet kunnen navolgen.

> [!WARNING]
> Java script Orchestrator-functies mogen nooit worden `async`gedeclareerd.

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

De Orchestration-trigger binding ondersteunt zowel invoer als uitvoer. Hier volgen enkele dingen die u moet weten over de verwerking van de invoer en uitvoer:

* **invoer** -.net-Orchestration-functies ondersteunen alleen [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) als parameter type. Het deserialiseren van invoer rechtstreeks in de functie handtekening wordt niet ondersteund. Code moet de [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.net) of `getInput` (Java script)-methode gebruiken om de functie-invoer van Orchestrator te verkrijgen. Deze invoer moet JSON-Serializable typen zijn.
* **outputs** : indelings triggers ondersteunen uitvoer waarden en invoer. De geretourneerde waarde van de functie wordt gebruikt om de uitvoer waarde toe te wijzen en moet JSON serialiseerbaar zijn. Als een .net-functie `Task` retourneert `void`of, `null` wordt een waarde opgeslagen als uitvoer.

### <a name="trigger-sample"></a>Voor beeld trigger

Hier volgt een voor beeld van wat de eenvoudigste functie ' Hallo wereld ' Orchestrator kan zien:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Het `context` object in Java script vertegenwoordigt niet de DurableOrchestrationContext, maar de [functie context als geheel.](../functions-reference-node.md#context-object) U kunt toegang krijgen tot indelings methoden `context` via de `df` eigenschap van het object.

> [!NOTE]
> Java script-Orchestrator moet `return`gebruiken. De `durable-functions` tape wisselaar zorgt ervoor dat de `context.done` methode wordt aangeroepen.

De meeste functies van Orchestrator roepen activiteit functies aan, dus hier volgt een voor beeld van een Hallo wereld dat laat zien hoe u een activiteit functie aanroept:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Activiteit triggers

Met de trigger activiteit kunt u functies maken die worden aangeroepen door Orchestrator-functies.

Als u Visual Studio gebruikt, wordt de activiteit trigger geconfigureerd met het [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .net-kenmerk.

Als u VS code gebruikt of de Azure portal voor ontwikkeling, wordt de activiteit trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van *Function. json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`is de naam van de activiteit. Dit is de waarde die de Orchestrator-functies gebruiken om deze activiteit functie aan te roepen. Deze eigenschap is optioneel. Als u niets opgeeft, wordt de naam van de functie gebruikt.

Intern deze trigger roept een wachtrij aan in het standaard opslag account voor de functie-app. Deze wachtrij is een interne implementatie details van de uitbrei ding, wat daarom niet expliciet is geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Gedrag activeren

Hier volgen enkele opmerkingen over de trigger voor activiteiten:

* **Threading** : in tegens telling tot de Orchestration-trigger gelden er geen beperkingen voor threading of I/O. Ze kunnen worden behandeld als normale functies.
* **Poison-verwerking van berichten** -er is geen Poison-bericht ondersteuning in activiteit triggers.
* **Bericht zichtbaarheid** : activiteit trigger berichten worden niet in de wachtrij geplaatst en worden gedurende een Configureer bare duur onzichtbaar bewaard. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : retour waarden worden GESERIALISEERD in JSON en persistent gemaakt in de Orchestration-geschiedenis tabel in azure Table Storage.

> [!WARNING]
> De opslag back-end voor activiteit functies is een implementatie detail en gebruikers code mogen niet rechtstreeks met deze opslag entiteiten werken.

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

De activiteit trigger binding ondersteunt zowel invoer als uitvoer, net als de Orchestration-trigger. Hier volgen enkele dingen die u moet weten over de verwerking van de invoer en uitvoer:

* **invoer** : .net-activiteit functies gebruiken systeem eigen [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) als parameter type. U kunt ook een functie activity declareren met elk parameter type dat kan worden geserialiseerd. Wanneer u gebruikt `DurableActivityContext`, kunt u [\<GetInput > T](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) aanroepen om de invoer van de activiteit functie op te halen en te deserialiseren.
* **uitvoer** -activiteit functies ondersteunen uitvoer waarden en invoer. De geretourneerde waarde van de functie wordt gebruikt om de uitvoer waarde toe te wijzen en moet JSON serialiseerbaar zijn. Als een .net-functie `Task` retourneert `void`of, `null` wordt een waarde opgeslagen als uitvoer.
* **meta gegevens** -.net-activiteit functies kunnen worden `string instanceId` gebonden aan een para meter om de exemplaar-id van de bovenliggende indeling op te halen.

### <a name="trigger-sample"></a>Voor beeld trigger

Hier volgt een voor beeld van hoe een eenvoudige activiteit functie ' Hallo wereld ' er als volgt uitziet:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Het standaard parameter type voor de .net `ActivityTriggerAttribute` -binding `DurableActivityContext`is. .NET-activiteit Triggers bieden echter ook ondersteuning voor het rechtstreeks binden aan JSON-serializeable-typen (waaronder primitieve typen), zodat dezelfde functie als volgt kan worden vereenvoudigd:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Java script-bindingen kunnen ook worden door gegeven als aanvullende para meters, zodat dezelfde functie als volgt kan worden vereenvoudigd:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Meerdere para meters door geven

Het is niet mogelijk om rechtstreeks meerdere para meters door te geven aan een activiteit functie. De aanbeveling in dit geval is om een matrix met objecten door te geven of om [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) -objecten in .net te gebruiken.

In het volgende voor beeld worden nieuwe functies van [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) gebruikt die zijn toegevoegd met [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
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

## <a name="orchestration-client"></a>Orchestration-client

Met de Orchestrator client binding kunt u functies schrijven die communiceren met Orchestrator-functies. U kunt bijvoorbeeld op de volgende manieren handelen op indelings instanties:

* Start deze.
* Hun status opvragen.
* Deze beëindigen.
* Verzend gebeurtenissen naar hen terwijl ze worden uitgevoerd.
* Exemplaar geschiedenis opschonen.

Als u Visual Studio gebruikt, kunt u verbinding maken met de Orchestration-client met behulp van het [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net-kenmerk.

Als u script talen (bijvoorbeeld. *CSX* -of *. js* -bestanden) gebruikt voor ontwikkeling, wordt de Orchestration-trigger gedefinieerd door het volgende JSON-object in `bindings` de matrix van *Function. json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`-Wordt gebruikt in scenario's waarbij meerdere functie-apps hetzelfde opslag account delen, maar van elkaar moeten worden geïsoleerd. Als u niets opgeeft, wordt de standaard `host.json` waarde van gebruikt. Deze waarde moet overeenkomen met de waarde die wordt gebruikt door de doel Orchestrator-functies.
* `connectionName`-De naam van een app-instelling die een opslag account bevat connection string. Het opslag account dat door deze connection string wordt weer gegeven, moet hetzelfde zijn als de naam die wordt gebruikt door de doel Orchestrator-functies. Als dat niet is opgegeven, wordt het standaard-opslag account connection string voor de functie-app gebruikt.

> [!NOTE]
> In de meeste gevallen wordt u aangeraden deze eigenschappen over te slaan en te vertrouwen op het standaard gedrag.

### <a name="client-usage"></a>Client gebruik

In .net functions maakt u doorgaans een `DurableOrchestrationClient`binding aan. Dit biedt volledige toegang tot alle client-api's die door Durable functions worden ondersteund. In Java script worden dezelfde api's weer gegeven door het `DurableOrchestrationClient` object dat wordt `getClient`geretourneerd door. Api's op het client object zijn onder andere:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (alleen .NET)

.NET-functies kunnen ook worden gebonden aan `IAsyncCollector<T>` de `T` locatie [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) of `JObject`.

Raadpleeg de [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API-documentatie voor meer informatie over deze bewerkingen.

> [!WARNING]
> Bij het lokaal ontwikkelen in Java script moet u de omgevings variabele `WEBSITE_HOSTNAME` instellen op `localhost:<port>`, bijvoorbeeld. `localhost:7071`om methoden te gebruiken `DurableOrchestrationClient`in. Zie het [github-probleem](https://github.com/Azure/azure-functions-durable-js/issues/28)voor meer informatie over deze vereiste.

### <a name="client-sample-visual-studio-development"></a>Client-voor beeld (Visual Studio-ontwikkeling)

Hier volgt een voor beeld van een door de wachtrij geactiveerde functie waarmee de indeling HelloWorld wordt gestart.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Client-voor beeld (niet Visual Studio)

Als u Visual Studio niet gebruikt voor ontwikkeling, kunt u het volgende *Function. json* -bestand maken. In dit voor beeld ziet u hoe u een functie die door een wachtrij wordt geactiveerd, configureert die gebruikmaakt van de duurzame Orchestration-client binding:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Hieronder vindt u taalspecifieke voor beelden die nieuwe Orchestrator-functie instanties starten.

#### <a name="c-sample"></a>Voorbeeld van C#

In het volgende voor beeld ziet u hoe u de duurzame Orchestration-client binding kunt gebruiken om een nieuw functie C# -exemplaar te starten vanuit een script functie:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript-voorbeeld

In het volgende voor beeld ziet u hoe u de duurzame Orchestration-client binding kunt gebruiken om een nieuw functie-exemplaar te starten vanuit een Java script-functie:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Meer informatie over begin instanties vindt u in [exemplaar beheer](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>instellingen voor host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het gedrag van controle punten en herhalingen](durable-functions-checkpointing-and-replay.md)