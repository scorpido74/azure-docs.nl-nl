---
title: Functie koppeling in Durable Functions-Azure
description: Meer informatie over het uitvoeren van een Durable Functions-voor beeld dat een reeks functies uitvoert.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 133169c659328fa4f713eb4b75bc460dee7a3f76
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614692"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Functie koppeling in Durable Functions-Hello-reeks voorbeeld

Functie koppeling heeft betrekking op het patroon van het uitvoeren van een reeks functies in een bepaalde volg orde. Vaak moet de uitvoer van de ene functie worden toegepast op de invoer van een andere functie. In dit artikel wordt de keten volgorde beschreven die u maakt wanneer u de Durable Functions Quick Start[C#](durable-functions-create-first-csharp.md) (of [Java script](quickstart-js-vscode.md)) voltooit. Zie [Durable functions Overview](durable-functions-overview.md)voor meer informatie over Durable functions.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app uitgelegd:

* `E1_HelloSequence`: een Orchestrator-functie die `E1_SayHello` meerdere keren in een reeks aanroept. De uitvoer van de `E1_SayHello`-aanroepen wordt opgeslagen en de resultaten worden geregistreerd.
* `E1_SayHello`: een activiteit functie die een teken reeks samenvoegt met ' Hello '.

In de volgende secties worden de configuratie en code uitgelegd die worden C# gebruikt voor het uitvoeren van scripts en Java script. De code voor Visual Studio-ontwikkeling wordt aan het einde van het artikel weer gegeven.

> [!NOTE]
> Java script-Durable Functions zijn alleen beschikbaar voor de functies 2,0 runtime.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>bestand function. json

Als u Visual Studio code of de Azure Portal voor ontwikkeling gebruikt, is dit de inhoud van het bestand *Function. json* voor de functie Orchestrator. De meeste Orchestrator *Function. json* -bestanden zien er bijna precies zo uit.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Het belangrijkste is het `orchestrationTrigger` bindings type. Alle Orchestrator-functies moeten dit trigger type gebruiken.

> [!WARNING]
> Als u de regel ' geen I/O ' van Orchestrator-functies wilt gebruiken, gebruik dan geen invoer-of uitvoer bindingen wanneer u de binding van de `orchestrationTrigger` trigger gebruikt.  Als er andere invoer-of uitvoer bindingen nodig zijn, moeten ze in plaats daarvan worden gebruikt in de context van `activityTrigger`-functies, die worden aangeroepen door de Orchestrator. Zie het artikel [Orchestrator functie code constraints](durable-functions-code-constraints.md) voor meer informatie.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#script (voorbeeld code Visual Studio code en Azure Portal)

Dit is de bron code:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Alle C# Orchestration-functies moeten een para meter van het type `DurableOrchestrationContext`hebben, die voor komt in de `Microsoft.Azure.WebJobs.Extensions.DurableTask`-assembly. Als u script gebruikt C# , kan naar de assembly worden verwezen met behulp van de `#r` notatie. Met dit context object kunt u andere *activiteiten* functies aanroepen en invoer parameters door geven met behulp van de `CallActivityAsync` methode.

De code roept `E1_SayHello` drie keer op met verschillende parameter waarden. De retour waarde van elke aanroep wordt toegevoegd aan de lijst `outputs`, die aan het einde van de functie wordt geretourneerd.

### <a name="javascript"></a>Javascript

Dit is de bron code:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alle Java script-Orchestration-functies moeten de [module`durable-functions`](https://www.npmjs.com/package/durable-functions)bevatten. Het is een bibliotheek waarmee u Durable Functions kunt schrijven in Java script. Er zijn drie belang rijke verschillen tussen een Orchestration-functie en andere Java script-functies:

1. De functie is een [functie generator.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. De functie wordt ingepakt in een aanroep van de `orchestrator` methode van de `durable-functions`-module (hier `df`).
3. De functie moet synchroon zijn. Omdat de ' Orchestrator '-methode de aanroep van ' context. done ' verwerkt, moet de functie gewoon ' return ' zijn.

Het `context`-object bevat een `df`-object waarmee u andere *activiteiten* functies aanroept en invoer parameters kunt door geven met behulp van de bijbehorende `callActivity` methode. De code roept `E1_SayHello` drie keer op met verschillende parameter waarden, met behulp van `yield` om aan te geven dat de uitvoering moet wachten op de functie aanroepen van de async-activiteit die moeten worden geretourneerd. De retour waarde van elke aanroep wordt toegevoegd aan de lijst `outputs`, die aan het einde van de functie wordt geretourneerd.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>bestand function. json

Het bestand *Function. json* voor de functie activity `E1_SayHello` is vergelijkbaar met dat van `E1_HelloSequence`, behalve dat er een `activityTrigger` bindings type wordt gebruikt in plaats van een `orchestrationTrigger` bindings type.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Een functie die wordt aangeroepen door een Orchestration-functie moet de binding `activityTrigger` gebruiken.

De implementatie van `E1_SayHello` is een relatief tamelijk opmaak bewerking voor teken reeksen.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Deze functie heeft een para meter van het type `DurableActivityContext`, die wordt gebruikt om de invoer op te halen die werd door gegeven door de Orchestrator-functie aanroep van `CallActivityAsync<T>`.

### <a name="javascript"></a>Javascript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

In tegens telling tot een Java script-Orchestration-functie heeft een activiteit functie geen speciale configuratie nodig. De invoer die wordt door gegeven door de Orchestrator-functie bevindt zich op het `context.bindings`-object onder de naam van de `activityTrigger` binding-in dit geval `context.bindings.name`. De naam van de binding kan worden ingesteld als een para meter van de geëxporteerde functie en direct worden geopend. Dit is de voorbeeld code.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Als u de `E1_HelloSequence` indeling wilt uitvoeren, moet u de volgende HTTP POST-aanvraag verzenden.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> In het vorige HTTP-fragment wordt ervan uitgegaan dat er een vermelding in het `host.json` bestand is waarmee het standaard `api/` voorvoegsel wordt verwijderd uit alle Url's van de HTTP-trigger functies. U kunt de opmaak voor deze configuratie vinden in het `host.json`-bestand in de voor beelden.

Als u bijvoorbeeld het voor beeld uitvoert in een functie-app met de naam ' myfunctionapp ', vervangt u ' {host} ' door ' myfunctionapp.azurewebsites.net '.

Het resultaat is een HTTP 202-antwoord, zoals dit (afgekapt voor de boog):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Op dit moment wordt de indeling in de wachtrij geplaatst en begint deze onmiddellijk te worden uitgevoerd. De URL in de `Location` header kan worden gebruikt om de status van de uitvoering te controleren.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Het resultaat is de status van de indeling. De service wordt uitgevoerd en snel voltooid, zodat deze in de status *voltooid* wordt weer gegeven met een antwoord dat er als volgt uitziet (afgekapt voor de boog):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Zoals u ziet, is de `runtimeStatus` van het exemplaar *voltooid* en bevat het `output` het JSON-serialisatie resultaat van de uitvoering van Orchestrator-functies.

> [!NOTE]
> Het HTTP POST-eind punt dat de Orchestrator-functie heeft gestart, wordt in de voor beeld-app geïmplementeerd als een HTTP-trigger functie met de naam ' HttpStart '. U kunt soort gelijke starter Logic implementeren voor andere trigger typen, zoals `queueTrigger`, `eventHubTrigger`of `timerTrigger`.

Bekijk de uitvoer logboeken van de functie. De functie `E1_HelloSequence` is meerdere keren gestart en voltooid als gevolg van het gedrag voor opnieuw afspelen dat wordt beschreven in het onderwerp over de [Orchestration-betrouw baarheid](durable-functions-orchestrations.md#reliability) . Aan de andere kant waren er maar drie uitvoeringen van `E1_SayHello`, omdat de uitvoeringen van deze functies niet opnieuw worden afgespeeld.

## <a name="visual-studio-sample-code"></a>Visual Studio-voorbeeld code

Dit is de indeling als één C# bestand in een Visual Studio-project:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld is een eenvoudige functie koppelings architectuur gedemonstreerd. In het volgende voor beeld ziet u hoe u het patroon uitwaaieren/ventilatoren implementeert.

> [!div class="nextstepaction"]
> [Voer het voor beeld uit voor uitwaaieren/ventilatoren](durable-functions-cloud-backup.md)
