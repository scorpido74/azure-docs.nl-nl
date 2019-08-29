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
ms.openlocfilehash: 1168963c0698c6bdafe20babe2e5143585bf90a8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087110"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Functie koppeling in Durable Functions-Hello-reeks voorbeeld

Functie koppeling heeft betrekking op het patroon van het uitvoeren van een reeks functies in een bepaalde volg orde. Vaak moet de uitvoer van de ene functie worden toegepast op de invoer van een andere functie. In dit artikel wordt de keten volgorde beschreven die u maakt wanneer u de Durable Functions Quick Start[C#](durable-functions-create-first-csharp.md) (of [Java script](quickstart-js-vscode.md)) voltooit. Zie [Durable functions patronen en technische concepten](durable-functions-concepts.md)voor meer informatie over Durable functions.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app uitgelegd:

* `E1_HelloSequence`: Een Orchestrator-functie die meerdere `E1_SayHello` keren in een reeks aanroept. De uitvoer van de `E1_SayHello` aanroepen wordt opgeslagen en de resultaten worden vastgelegd.
* `E1_SayHello`: Een activiteit functie die een teken reeks samenvoegt met ' Hello '.

In de volgende secties worden de configuratie en code uitgelegd die worden C# gebruikt voor het uitvoeren van scripts en Java script. De code voor Visual Studio-ontwikkeling wordt aan het einde van het artikel weer gegeven.

> [!NOTE]
> Java script-Durable Functions zijn alleen beschikbaar voor de functies 2. x runtime.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>bestand function. json

Als u Visual Studio code of de Azure Portal voor ontwikkeling gebruikt, is dit de inhoud van het bestand *Function. json* voor de functie Orchestrator. De meeste Orchestrator *Function. json* -bestanden zien er bijna precies zo uit.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

De belang rijke ding is `orchestrationTrigger` het bindings type. Alle Orchestrator-functies moeten dit trigger type gebruiken.

> [!WARNING]
> Als u de regel ' geen I/O ' van Orchestrator-functies wilt gebruiken, gebruikt u geen invoer-of uitvoer bindingen wanneer `orchestrationTrigger` u de trigger binding gebruikt.  Als er andere invoer-of uitvoer bindingen nodig zijn, moeten ze in plaats daarvan worden gebruikt `activityTrigger` in de context van functions, die worden aangeroepen door de Orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#script (voorbeeld code Visual Studio code en Azure Portal)

Dit is de bron code:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Alle C# Orchestration-functies moeten een para meter van het `DurableOrchestrationContext`type, die voor komt `Microsoft.Azure.WebJobs.Extensions.DurableTask` in de assembly, hebben. Als u script gebruikt C# , kan er naar de assembly worden verwezen met `#r` de notatie. Met dit context object kunt u andere *activiteiten* functies aanroepen en invoer parameters door geven met behulp van de methode [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) .

De code roept `E1_SayHello` drie keer op met verschillende parameter waarden. De geretourneerde waarde van elke aanroep wordt toegevoegd aan `outputs` de lijst, die aan het einde van de functie wordt geretourneerd.

### <a name="javascript"></a>Javascript

Dit is de bron code:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alle Java script-Orchestration-functies moeten de [ `durable-functions` module](https://www.npmjs.com/package/durable-functions)bevatten. Dit is een bibliotheek waarmee u Durable Functions kunt schrijven in Java script. Er zijn drie belang rijke verschillen tussen een Orchestration-functie en andere Java script-functies:

1. De functie is een [functie generator.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. De functie wordt ingepakt in een aanroep naar `durable-functions` de methode `orchestrator` van de module `df`(hier).
3. De functie moet synchroon zijn. Omdat de ' Orchestrator '-methode de aanroep van ' context. done ' verwerkt, moet de functie gewoon ' return ' zijn.

Het `context` object bevat een `df` object, waarmee u andere *activiteiten* functies aanroept en invoer parameters kunt `callActivity` door geven met behulp van de methode. De code roept `E1_SayHello` drie keer op met verschillende parameter waarden, met `yield` om aan te geven dat de uitvoering moet wachten op de functie aanroepen van de async-activiteit die moeten worden geretourneerd. De geretourneerde waarde van elke aanroep wordt toegevoegd aan `outputs` de lijst, die aan het einde van de functie wordt geretourneerd.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>bestand function. json

Het bestand *Function. json* voor de `E1_SayHello` functie activity is vergelijkbaar met dat van `E1_HelloSequence` , behalve dat er een `activityTrigger` bindings type wordt gebruikt in plaats van een `orchestrationTrigger` bindings type.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Een functie die wordt aangeroepen door een Orchestration-functie, `activityTrigger` moet de binding gebruiken.

De implementatie van `E1_SayHello` is een relatief tamelijk opmaak bewerking voor teken reeksen.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Deze functie heeft een para meter van het type [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), die wordt gebruikt om de invoer op te halen die werd door gegeven door de Orchestrator-functie [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

In tegens telling tot een Java script-Orchestration-functie heeft een activiteit functie geen speciale configuratie nodig. De invoer die wordt door gegeven door de Orchestrator `context.bindings` `context.bindings.name`-functie bevindt zich op het object onder de `activityTrigger` naam van de binding-in dit geval. De naam van de binding kan worden ingesteld als een para meter van de geëxporteerde functie en direct worden geopend. Dit is de voorbeeld code.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Als u de `E1_HelloSequence` indeling wilt uitvoeren, verzendt u de volgende HTTP POST-aanvraag.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> In het vorige http-fragment wordt ervan uitgegaan dat er `host.json` een vermelding in het bestand `api/` staat waarmee het standaard voorvoegsel van alle http-trigger functies url's wordt verwijderd. U kunt de opmaak voor deze configuratie vinden in het `host.json` bestand in de voor beelden.

Als u bijvoorbeeld het voor beeld uitvoert in een functie-app met de naam ' myfunctionapp ', vervangt u ' {host} ' door ' myfunctionapp.azurewebsites.net '.

Het resultaat is een HTTP 202-antwoord, zoals dit (afgekapt voor de boog):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Op dit moment wordt de indeling in de wachtrij geplaatst en begint deze onmiddellijk te worden uitgevoerd. De URL in de `Location` header kan worden gebruikt om de status van de uitvoering te controleren.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Het resultaat is de status van de indeling. De service wordt uitgevoerd en snel voltooid, zodat deze in de status *voltooid* wordt weer gegeven met een antwoord dat er als volgt uitziet (afgekapt voor de boog):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Zoals u ziet, is de `runtimeStatus` van de instantie `output` *voltooid* en bevat het JSON-serialisatie resultaat van de functie-uitvoering van Orchestrator.

> [!NOTE]
> Het HTTP POST-eind punt dat de Orchestrator-functie heeft gestart, wordt in de voor beeld-app geïmplementeerd als een HTTP-trigger functie met de naam ' HttpStart '. U kunt soort gelijke starter Logic implementeren voor andere trigger typen, `queueTrigger`zoals `eventHubTrigger`, of `timerTrigger`.

Bekijk de uitvoer logboeken van de functie. De `E1_HelloSequence` functie is meerdere keren gestart en voltooid als gevolg van het gedrag voor opnieuw afspelen dat wordt beschreven in het [overzicht](durable-functions-concepts.md). Aan de andere kant waren er maar drie uitvoeringen van `E1_SayHello` , omdat deze functies niet opnieuw worden afgespeeld.

## <a name="visual-studio-sample-code"></a>Visual Studio-voorbeeld code

Dit is de indeling als één C# bestand in een Visual Studio-project:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld is een eenvoudige functie koppelings architectuur gedemonstreerd. In het volgende voor beeld ziet u hoe u het patroon uitwaaieren/ventilatoren implementeert.

> [!div class="nextstepaction"]
> [Voer het voor beeld uit voor uitwaaieren/ventilatoren](durable-functions-cloud-backup.md)
