---
title: Fan-out/fan-in scenario's in duurzame functies - Azure
description: Meer informatie over het implementeren van een ventilator-uit-ventilator-in-scenario in de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562187"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/fan-in scenario in duurzame functies - Voorbeeld van cloudback-up

*Fan-out/fan-in* verwijst naar het patroon van het gelijktijdig uitvoeren van meerdere functies en vervolgens het uitvoeren van een aggregatie op de resultaten. In dit artikel wordt een voorbeeld uitgelegd dat [duurzame functies](durable-functions-overview.md) gebruikt om een fan-in/fan-out-scenario te implementeren. Het voorbeeld is een duurzame functie die een back-up maakt van alle of een deel van de site-inhoud van een app in Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

In dit voorbeeld uploaden de functies alle bestanden onder een opgegeven map recursief naar blob-opslag. Ze tellen ook het totale aantal bytes dat is geüpload.

Het is mogelijk om een enkele functie te schrijven die voor alles zorgt. Het belangrijkste probleem dat je zou tegenkomen is **schaalbaarheid.** Eén functieuitvoering kan slechts op één virtuele machine worden uitgevoerd, zodat de doorvoer wordt beperkt door de doorvoer van die ene vm. Een ander probleem is **betrouwbaarheid.** Als er halverwege een storing optreedt of als het hele proces meer dan 5 minuten duurt, kan de back-up in een gedeeltelijk voltooide status mislukken. Het zou dan opnieuw moeten worden opgestart.

Een meer robuuste aanpak zou zijn om twee reguliere functies te schrijven: een zou de bestanden opsommen en de bestandsnamen toevoegen aan een wachtrij, en een ander zou lezen uit de wachtrij en de bestanden uploaden naar blob-opslag. Deze aanpak is beter in termen van doorvoer en betrouwbaarheid, maar het vereist dat u een wachtrij indient en beheert. Wat nog belangrijker is, wordt significante ingewikkeldheid geïntroduceerd in termen van **staatsbeheer** en **coördinatie** als u om het even wat meer wilt doen, zoals rapport het totale aantal geüploade bytes.

Een duurzame functies aanpak geeft u alle genoemde voordelen met een zeer lage overhead.

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voorbeeld-app uitgelegd:

* `E2_BackupSiteContent`: Een [orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die aanroept `E2_GetFileList` om een lijst `E2_CopyFileToBlob` met bestanden te verkrijgen om een back-up te maken en vervolgens een back-up van elk bestand te maken.
* `E2_GetFileList`: Een [activiteitsfunctie](durable-functions-bindings.md#activity-trigger) die een lijst met bestanden in een map retourneert.
* `E2_CopyFileToBlob`: Een activiteitsfunctie die een back-up maakt van één bestand naar Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent orchestrator-functie

Deze orchestrator functie doet in wezen het volgende:

1. Neemt `rootDirectory` een waarde als een invoerparameter.
2. Roept een functie aan om een recursieve lijst met bestanden te krijgen onder `rootDirectory`.
3. Maakt meerdere parallelle functieoproepen om elk bestand te uploaden naar Azure Blob Storage.
4. Wacht tot alle uploads zijn voltooid.
5. Geeft als resultaat de som totaal aantal bytes die zijn geüpload naar Azure Blob Storage.

# <a name="c"></a>[C #](#tab/csharp)

Hier is de code die de orchestrator-functie implementeert:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Let `await Task.WhenAll(tasks);` op de lijn. Alle individuele oproepen `E2_CopyFileToBlob` naar de functie werden *niet* afgewacht, waardoor ze parallel kunnen lopen. Wanneer we deze reeks `Task.WhenAll`taken doorgeven aan , krijgen we een taak terug die niet wordt voltooid *totdat alle kopieerbewerkingen zijn voltooid.* Als u bekend bent met de Taak parallelbibliotheek (TPL) in .NET, dan is dit niet nieuw voor u. Het verschil is dat deze taken gelijktijdig op meerdere virtuele machines kunnen worden uitgevoerd en de extensie Duurzame functies zorgt ervoor dat de end-to-end uitvoering bestand is tegen procesrecycling.

Na het `Task.WhenAll`wachten van , weten we dat alle functie oproepen hebben voltooid en hebben waarden terug naar ons. Elke aanroep om het aantal geüploade bytes te `E2_CopyFileToBlob` retourneren, dus het berekenen van het totale aantal bytes is een kwestie van al die retourwaarden bij elkaar optellen.

# <a name="javascript"></a>[Javascript](#tab/javascript)

De functie maakt gebruik van de standaard *function.json* voor orchestrator functies.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Hier is de code die de orchestrator-functie implementeert:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Let `yield context.df.Task.all(tasks);` op de lijn. Alle individuele oproepen `E2_CopyFileToBlob` naar de functie werden *niet* opgebracht, waardoor ze parallel kunnen lopen. Wanneer we deze reeks `context.df.Task.all`taken doorgeven aan , krijgen we een taak terug die niet wordt voltooid *totdat alle kopieerbewerkingen zijn voltooid.* Als u bekend [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) bent met javascript, dan is dit niet nieuw voor u. Het verschil is dat deze taken gelijktijdig op meerdere virtuele machines kunnen worden uitgevoerd en de extensie Duurzame functies zorgt ervoor dat de end-to-end uitvoering bestand is tegen procesrecycling.

> [!NOTE]
> Hoewel taken conceptueel vergelijkbaar zijn met JavaScript-beloften, moeten `Promise.all` `Promise.race` orchestrator-functies functies gebruiken `context.df.Task.all` en `context.df.Task.any` in plaats van en beheren van taakparallelisatie.

Na het `context.df.Task.all`opleveren van, weten we dat alle functie oproepen hebben voltooid en hebben waarden terug naar ons. Elke aanroep om het aantal geüploade bytes te `E2_CopyFileToBlob` retourneren, dus het berekenen van het totale aantal bytes is een kwestie van al die retourwaarden bij elkaar optellen.

---

### <a name="helper-activity-functions"></a>Helper-activiteitsfuncties

De helper activiteit functies, net als bij andere `activityTrigger` monsters, zijn gewoon reguliere functies die gebruik maken van de trigger binding.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList activiteitsfunctie

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Het *bestand functie.json* voor `E2_GetFileList` ziet er als volgt uit:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

En hier is de uitvoering:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

De functie `readdirp` gebruikt de module (versie 2.x) om de mapstructuur opnieuw te lezen.

---

> [!NOTE]
> Je vraagt je misschien af waarom je deze code niet direct in de orchestrator-functie kon plaatsen. Je zou kunnen, maar dit zou breken een van de fundamentele regels van orchestrator functies, dat is dat ze nooit moeten doen I / O, met inbegrip van de lokale bestandssysteem toegang. Zie [Beperkingen van de functie orchestrator](durable-functions-code-constraints.md)voor meer informatie .

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob activiteitsfunctie

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Je moet het `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet-pakket installeren om de voorbeeldcode uit te voeren.

De functie maakt gebruik van een aantal geavanceerde functies van Azure Functions-bindingen (dat wil zeggen het gebruik van de [ `Binder` parameter),](../functions-dotnet-class-library.md#binding-at-runtime)maar u hoeft zich geen zorgen te maken over deze details met het oog op deze walkthrough.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Het *functie.json-bestand* voor `E2_CopyFileToBlob` is ook eenvoudig:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

De JavaScript-implementatie maakt gebruik van de [Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node) om de bestanden te uploaden naar Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

De implementatie laadt het bestand vanaf de schijf en streamt de inhoud asynchroon naar een blob met dezelfde naam in de container 'back-ups'. De retourwaarde is het aantal bytes dat naar opslag wordt gekopieerd, dat vervolgens wordt gebruikt door de orchestrator-functie om de totale som te berekenen.

> [!NOTE]
> Dit is een perfect voorbeeld van het `activityTrigger` verplaatsen van I/O-bewerkingen naar een functie. Niet alleen kan het werk worden verdeeld over veel verschillende machines, maar je krijgt ook de voordelen van checkpointing de voortgang. Als het hostproces om welke reden dan ook wordt beëindigd, weet u welke uploads al zijn voltooid.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U de orkestratie starten door de volgende HTTP POST-aanvraag te verzenden.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> De `HttpStart` functie die u aanroept, werkt alleen met inhoud die is geformeerd met JSON. Om deze reden `Content-Type: application/json` is de koptekst vereist en wordt het mappad gecodeerd als een JSON-tekenreeks. Bovendien gaat HTTP-fragment ervan uit dat `host.json` er een `api/` vermelding in het bestand is die het standaardvoorvoegsel van alle HTTP-triggerfuncties-URL's verwijdert. U de opmaak voor `host.json` deze configuratie vinden in het bestand in de voorbeelden.

Deze HTTP-aanvraag `E2_BackupSiteContent` activeert de orchestrator en passeert de tekenreeks `D:\home\LogFiles` als parameter. Het antwoord biedt een koppeling om de status van de back-upbewerking te krijgen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Afhankelijk van het aantal logbestanden dat u in uw functie-app hebt, kan deze bewerking enkele minuten in beslag nemen. U de laatste status verkrijgen door `Location` de URL op te vragen in de koptekst van het vorige HTTP 202-antwoord.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

In dit geval wordt de functie nog steeds uitgevoerd. U de invoer zien die is opgeslagen in de orchestratorstatus en de laatste bijgewerkte tijd. U de `Location` kopwaarden blijven gebruiken om te peilen voor voltooiing. Wanneer de status 'Voltooid' is, ziet u een HTTP-antwoordwaarde die vergelijkbaar is met de volgende:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Nu u zien dat de orkestratie is voltooid en ongeveer hoeveel tijd het duurde om te voltooien. U ziet ook een `output` waarde voor het veld, wat aangeeft dat ongeveer 450 KB aan logboeken is geüpload.

## <a name="next-steps"></a>Volgende stappen

Dit voorbeeld heeft aangetoond hoe het fan-out/fan-in patroon te implementeren. Het volgende voorbeeld laat zien hoe u het monitorpatroon implementeert met behulp van [duurzame timers.](durable-functions-timers.md)

> [!div class="nextstepaction"]
> [Het monitorvoorbeeld uitvoeren](durable-functions-monitor.md)