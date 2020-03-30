---
title: Functieketen in duurzame functies - Azure
description: Meer informatie over het uitvoeren van een voorbeeld van duurzame functies dat een reeks functies uitvoert.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562055"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Functiechaining in duurzame functies - Hello sequentie monster

Functiechaining verwijst naar het patroon van het uitvoeren van een reeks functies in een bepaalde volgorde. Vaak moet de uitvoer van de ene functie worden toegepast op de invoer van een andere functie. In dit artikel wordt de chainingvolgorde beschreven die u maakt wanneer u de quickstart duurzame functies[(C#](durable-functions-create-first-csharp.md) of [JavaScript) voltooit.](quickstart-js-vscode.md) Zie Overzicht duurzame [functies](durable-functions-overview.md)voor meer informatie over duurzame functies.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voorbeeld-app uitgelegd:

* `E1_HelloSequence`: Een [orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die meerdere keren in een reeks aanroept. `E1_SayHello` Het slaat de uitgangen van de `E1_SayHello` gesprekken op en registreert de resultaten.
* `E1_SayHello`: Een [activiteitsfunctie](durable-functions-bindings.md#activity-trigger) die een tekenreeks voorbereidt met 'Hallo'.
* `HttpStart`: Een HTTP-geactiveerde functie die een instantie van de orchestrator start.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence orchestrator-functie

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Alle c# orchestration-functies moeten `DurableOrchestrationContext`een parameter van `Microsoft.Azure.WebJobs.Extensions.DurableTask` het type hebben, die in de assemblage bestaat. Met dit contextobject u andere *activiteitsfuncties* aanroepen en invoerparameters doorgeven met behulp van de `CallActivityAsync` methode.

De code `E1_SayHello` roept drie keer achter elkaar aan met verschillende parameterwaarden. De retourwaarde van elke oproep `outputs` wordt toegevoegd aan de lijst, die wordt geretourneerd aan het einde van de functie.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> JavaScript Duurzame functies zijn alleen beschikbaar voor de functions 2.0-runtime.

#### <a name="functionjson"></a>function.json

Als u Visual Studio Code of de Azure-portal gebruikt voor ontwikkeling, vindt u hier de inhoud van het *function.json-bestand* voor de orchestrator-functie. De meeste orchestrator *function.json* bestanden zien er bijna precies zo uit.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Het belangrijkste is `orchestrationTrigger` het bindende type. Alle orchestratorfuncties moeten dit triggertype gebruiken.

> [!WARNING]
> Als u zich wilt houden aan de "geen I/O"-regel van orchestratorfuncties, gebruikt u geen invoer- of uitvoerbindingen bij het gebruik van de `orchestrationTrigger` triggerbinding.  Als andere input- of uitvoerbindingen nodig zijn, moeten `activityTrigger` ze in plaats daarvan worden gebruikt in de context van functies, die door de orchestrator worden aangeroepen. Zie het artikel voor beperkingen van de [orchestrator-functiecode voor](durable-functions-code-constraints.md) meer informatie.

#### <a name="indexjs"></a>index.js

Hier is de functie:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Alle JavaScript-orkestratiefuncties [ `durable-functions` ](https://www.npmjs.com/package/durable-functions)moeten de module bevatten. Het is een bibliotheek waarmee u duurzame functies schrijven in JavaScript. Er zijn drie significante verschillen tussen een orchestration-functie en andere JavaScript-functies:

1. De functie is een [generatorfunctie.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. De functie is verpakt in `durable-functions` een aanroep naar de methode van `orchestrator` de module (hier). `df`
3. De functie moet synchroon zijn. Omdat de 'orchestrator'-methode omgaat met het aanroepen van 'context.done', moet de functie gewoon 'terugkeren'.

Het `context` object `df` bevat een duurzaam orchestration-contextobject waarmee u andere `callActivity` *activiteitsfuncties* aanroepen en invoerparameters doorgeven met behulp van de methode. De code `E1_SayHello` roept drie keer achter elkaar `yield` aan met verschillende parameterwaarden, waarbij wordt gebruikt om aan te geven dat de uitvoering moet wachten op de async-activiteitsfunctieoproepen die moeten worden geretourneerd. De retourwaarde van elke aanroep wordt toegevoegd aan de `outputs` array, die wordt geretourneerd aan het einde van de functie.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello activiteitsfunctie

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Activiteiten gebruiken `ActivityTrigger` het kenmerk. Gebruik de `IDurableActivityContext` meegeleverde om activiteitsgerelateerde acties uit `GetInput<T>`te voeren, zoals toegang tot de invoerwaarde met behulp van .

De implementatie `E1_SayHello` van is een relatief triviale string opmaak operatie.

In plaats van `IDurableActivityContext`te binden aan een, u direct binden aan het type dat wordt doorgegeven aan de activiteitfunctie. Bijvoorbeeld:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

Het *functie.json-bestand* voor `E1_SayHello` de activiteitsfunctie is vergelijkbaar met dat van, `E1_HelloSequence` behalve dat het een `activityTrigger` bindend type gebruikt in plaats van een `orchestrationTrigger` bindend type.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Elke functie die door een orkestratiefunctie wordt aangeroepen, moet de `activityTrigger` binding gebruiken.

De implementatie `E1_SayHello` van is een relatief triviale string opmaak operatie.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

In tegenstelling tot een JavaScript-orkestratiefunctie heeft een activiteitsfunctie geen speciale installatie nodig. De input die door de orchestrator-functie `context.bindings` aan het object `activityTrigger` wordt doorgegeven, `context.bindings.name`bevindt zich op het object onder de naam van de binding - in dit geval . De bindingsnaam kan worden ingesteld als een parameter van de geëxporteerde functie en rechtstreeks worden geopend, wat de voorbeeldcode doet.

---

### <a name="httpstart-client-function"></a>HttpStart-client, functie

U een instantie van orchestrator-functie starten met behulp van een clientfunctie. U gebruikt `HttpStart` de http-geactiveerde `E1_HelloSequence`functie om exemplaren van .

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Om met orchestrators te communiceren, `DurableClient` moet de functie een invoerbinding bevatten. U gebruikt de client om een orkestratie te starten. Het kan u ook helpen een HTTP-antwoord met URL's terug te sturen voor het controleren van de status van de nieuwe orchestration.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Om met orchestrators te communiceren, `durableClient` moet de functie een invoerbinding bevatten.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Gebruiken `df.getClient` om `DurableOrchestrationClient` een object te verkrijgen. U gebruikt de client om een orkestratie te starten. Het kan u ook helpen een HTTP-antwoord met URL's terug te sturen voor het controleren van de status van de nieuwe orchestration.

---

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Als u `E1_HelloSequence` de orkestratie wilt uitvoeren, `HttpStart` stuurt u het volgende HTTP POST-verzoek naar de functie.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> In het vorige HTTP-fragment wordt `host.json` ervan uitgegaan dat `api/` er een vermelding in het bestand is waardoor het standaardvoorvoegsel van alle HTTP-triggerfuncties-URL's wordt verwijderd. U de opmaak voor `host.json` deze configuratie vinden in het bestand in de voorbeelden.

Als u bijvoorbeeld het voorbeeld uitvoert in een functie-app met de naam 'myfunctionapp', vervangt u {host} door 'myfunctionapp.azurewebsites.net'.

Het resultaat is een HTTP 202-antwoord, zoals dit (bijgesneden voor beknoptheid):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Op dit punt staat de orkestratie in de wachtrij en begint onmiddellijk te worden uitgevoerd. De URL `Location` in de koptekst kan worden gebruikt om de status van de uitvoering te controleren.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Het resultaat is de status van de orkestratie. Het wordt uitgevoerd en snel voltooid, zodat u het in de *status Voltooid* ziet met een antwoord dat er als volgt uitziet (bijgesneden voor beknoptheid):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Zoals u `runtimeStatus` zien, is de `output` instantie *Voltooid* en bevat het JSON-geserialiseerde resultaat van de uitvoering van de orchestrator-functie.

> [!NOTE]
> U vergelijkbare starterlogica implementeren voor `queueTrigger` `eventHubTrigger`andere `timerTrigger`triggertypen, zoals, of.

Kijk naar de functie uitvoeringslogboeken. De `E1_HelloSequence` functie is meerdere keren gestart en voltooid vanwege het replaygedrag dat wordt beschreven in het onderwerp van de betrouwbaarheid van de [orkestratie.](durable-functions-orchestrations.md#reliability) Aan de andere kant waren er `E1_SayHello` slechts drie executies van, omdat deze functie-uitvoeringen niet worden overspeeld.

## <a name="next-steps"></a>Volgende stappen

Deze steekproef heeft een eenvoudige functie-chaining orchestration aangetoond. Het volgende voorbeeld laat zien hoe je het fan-out/fan-in patroon implementeert.

> [!div class="nextstepaction"]
> [Voer het fan-out/fan-in monster uit](durable-functions-cloud-backup.md)
