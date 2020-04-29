---
title: Functie koppeling in Durable Functions-Azure
description: Meer informatie over het uitvoeren van een Durable Functions-voor beeld dat een reeks functies uitvoert.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77562055"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Functie koppeling in Durable Functions-Hello-reeks voorbeeld

Functie koppeling heeft betrekking op het patroon van het uitvoeren van een reeks functies in een bepaalde volg orde. Vaak moet de uitvoer van de ene functie worden toegepast op de invoer van een andere functie. In dit artikel wordt de keten volgorde beschreven die u maakt wanneer u de Durable Functions Quick Start ([C#](durable-functions-create-first-csharp.md) of [Java script](quickstart-js-vscode.md)) voltooit. Zie [Durable functions Overview](durable-functions-overview.md)voor meer informatie over Durable functions.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app uitgelegd:

* `E1_HelloSequence`: Een [Orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die meerdere `E1_SayHello` keren in een reeks aanroept. De uitvoer van de `E1_SayHello` aanroepen wordt opgeslagen en de resultaten worden vastgelegd.
* `E1_SayHello`: Een [activiteit functie](durable-functions-bindings.md#activity-trigger) die een teken reeks samenvoegt met ' Hello '.
* `HttpStart`: Een door HTTP geactiveerde functie waarmee een exemplaar van de Orchestrator wordt gestart.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence Orchestrator-functie

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Alle C#-Orchestration-functies moeten een para meter van `DurableOrchestrationContext`het type, die voor `Microsoft.Azure.WebJobs.Extensions.DurableTask` komt in de assembly, hebben. Met dit context object kunt u andere *activiteiten* functies aanroepen en invoer parameters door `CallActivityAsync` geven met behulp van de methode.

De code roept `E1_SayHello` drie keer op met verschillende parameter waarden. De geretourneerde waarde van elke aanroep wordt toegevoegd aan `outputs` de lijst, die aan het einde van de functie wordt geretourneerd.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> Java script-Durable Functions zijn alleen beschikbaar voor de functies 2,0 runtime.

#### <a name="functionjson"></a>function.json

Als u Visual Studio code of de Azure Portal voor ontwikkeling gebruikt, is dit de inhoud van het bestand *Function. json* voor de functie Orchestrator. De meeste Orchestrator *Function. json* -bestanden zien er bijna precies zo uit.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

De belang rijke ding is `orchestrationTrigger` het bindings type. Alle Orchestrator-functies moeten dit trigger type gebruiken.

> [!WARNING]
> Als u de regel ' geen I/O ' van Orchestrator-functies wilt gebruiken, gebruikt u geen invoer-of uitvoer bindingen wanneer `orchestrationTrigger` u de trigger binding gebruikt.  Als er andere invoer-of uitvoer bindingen nodig zijn, moeten ze in plaats daarvan worden gebruikt `activityTrigger` in de context van functions, die worden aangeroepen door de Orchestrator. Zie het artikel [Orchestrator functie code constraints](durable-functions-code-constraints.md) voor meer informatie.

#### <a name="indexjs"></a>index. js

Dit is de functie:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alle Java script-Orchestration-functies moeten de [ `durable-functions` module](https://www.npmjs.com/package/durable-functions)bevatten. Het is een bibliotheek waarmee u Durable Functions kunt schrijven in Java script. Er zijn drie belang rijke verschillen tussen een Orchestration-functie en andere Java script-functies:

1. De functie is een [functie generator.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. De functie wordt ingepakt in een aanroep naar `durable-functions` de methode `orchestrator` van de module `df`(hier).
3. De functie moet synchroon zijn. Omdat de ' Orchestrator '-methode de aanroep van ' context. done ' verwerkt, moet de functie gewoon ' return ' zijn.

Het `context` object bevat een `df` duurzaam Orchestration context-object waarmee u andere *activiteiten* functies kunt aanroepen en invoer parameters met behulp van de `callActivity` methode kan door geven. De code roept `E1_SayHello` drie keer op met verschillende parameter waarden, met `yield` om aan te geven dat de uitvoering moet wachten op de functie aanroepen van de async-activiteit die moeten worden geretourneerd. De geretourneerde waarde van elke aanroep wordt toegevoegd aan `outputs` de matrix, die aan het einde van de functie wordt geretourneerd.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello-activiteit functie

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Activiteiten gebruiken het `ActivityTrigger` -kenmerk. Gebruik de opgegeven `IDurableActivityContext` voor het uitvoeren van activiteiten die betrekking hebben op activiteit, zoals het `GetInput<T>`openen van de invoer waarde met behulp van.

De implementatie van `E1_SayHello` is een relatief tamelijk opmaak bewerking voor teken reeksen.

In plaats van een binding `IDurableActivityContext`met een, kunt u rechtstreeks verbinding maken met het type dat wordt door gegeven aan de functie activity. Bijvoorbeeld:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

Het *bestand function. json* voor `E1_SayHello` de functie activity is vergelijkbaar met dat van `E1_HelloSequence` , behalve dat er een `activityTrigger` bindings type wordt gebruikt `orchestrationTrigger` in plaats van een bindings type.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Een functie die wordt aangeroepen door een Orchestration-functie, `activityTrigger` moet de binding gebruiken.

De implementatie van `E1_SayHello` is een relatief tamelijk opmaak bewerking voor teken reeksen.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

In tegens telling tot een Java script-Orchestration-functie heeft een activiteit functie geen speciale configuratie nodig. De invoer die wordt door gegeven door de Orchestrator-functie bevindt `context.bindings` zich op het object onder de `activityTrigger` naam van de binding-in `context.bindings.name`dit geval. De naam van de binding kan worden ingesteld als een para meter van de geëxporteerde functie en direct worden geopend. Dit is de voorbeeld code.

---

### <a name="httpstart-client-function"></a>HttpStart-client functie

U kunt een exemplaar van Orchestrator-functie starten met behulp van een client functie. U gebruikt de `HttpStart` http-geactiveerde functie om exemplaren van `E1_HelloSequence`te starten.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

De functie moet een `DurableClient` invoer binding bevatten om te kunnen communiceren met Orchestrator. U gebruikt de-client om een indeling te starten. Het kan ook helpen u een HTTP-antwoord met Url's te retour neren voor het controleren van de status van de nieuwe indeling.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function. json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

De functie moet een `durableClient` invoer binding bevatten om te kunnen communiceren met Orchestrator.

#### <a name="httpstartindexjs"></a>HttpStart/index. js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Gebruiken `df.getClient` om een `DurableOrchestrationClient` object te verkrijgen. U gebruikt de-client om een indeling te starten. Het kan ook helpen u een HTTP-antwoord met Url's te retour neren voor het controleren van de status van de nieuwe indeling.

---

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Als u de `E1_HelloSequence` indeling wilt uitvoeren, verzendt u de volgende HTTP Post- `HttpStart` aanvraag naar de functie.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> In het vorige HTTP-fragment wordt ervan uitgegaan dat er `host.json` een vermelding in het bestand `api/` staat waarmee het standaard voorvoegsel van alle http-trigger functies url's wordt verwijderd. U kunt de opmaak voor deze configuratie vinden in het `host.json` bestand in de voor beelden.

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

Zoals u ziet, is de `runtimeStatus` van de instantie *voltooid* en `output` bevat het JSON-serialisatie resultaat van de functie-uitvoering van Orchestrator.

> [!NOTE]
> U kunt soort gelijke starter Logic implementeren voor andere trigger typen, `queueTrigger`zoals `eventHubTrigger`, of `timerTrigger`.

Bekijk de uitvoer logboeken van de functie. De `E1_HelloSequence` functie is meerdere keren gestart en voltooid als gevolg van het gedrag voor opnieuw afspelen dat wordt beschreven in het onderwerp over de [Orchestration-betrouw baarheid](durable-functions-orchestrations.md#reliability) . Aan de andere kant waren er maar drie uitvoeringen van `E1_SayHello` , omdat deze functies niet opnieuw worden afgespeeld.

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld is een eenvoudige functie koppelings architectuur gedemonstreerd. In het volgende voor beeld ziet u hoe u het patroon uitwaaieren/ventilatoren implementeert.

> [!div class="nextstepaction"]
> [Voer het voor beeld uit voor uitwaaieren/ventilatoren](durable-functions-cloud-backup.md)
