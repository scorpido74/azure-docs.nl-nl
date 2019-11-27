---
title: Monitors in Durable Functions-Azure
description: Meer informatie over het implementeren van een status monitor met de extensie Durable Functions voor Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9c8edf5e8fb32160280a1ce9bff827c2e3fa14f8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232865"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Het scenario bewaken in het Durable Functions-weers Watcher-voor beeld

Het monitor patroon verwijst naar een flexibel *terugkerend* proces in een werk stroom, bijvoorbeeld polling totdat aan bepaalde voor waarden wordt voldaan. In dit artikel vindt u een voor beeld waarin [Durable functions](durable-functions-overview.md) wordt gebruikt voor het implementeren van bewaking.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

In dit voor beeld worden de huidige weers omstandigheden van een locatie bewaakt en wordt een gebruiker door SMS gewaarschuwd wanneer de Skies duidelijk zijn. U kunt een normale, door timer geactiveerde functie gebruiken om het weer te controleren en waarschuwingen te verzenden. Maar een probleem met deze aanpak is het **beheer van de levens duur**. Als er slechts één waarschuwing moet worden verzonden, moet de monitor zichzelf uitschakelen nadat het weer gegeven opnieuw is gedetecteerd. Het bewakings patroon kan zijn eigen uitvoering kunnen beëindigen, onder andere voor delen:

* Bewaakte uitvoeringen op intervallen, niet schema's: een timer trigger wordt elk uur *uitgevoerd* ; een monitor *wacht* één uur tussen de acties. De acties van een monitor hebben geen overlap, tenzij opgegeven, wat belang rijk kan zijn voor langlopende taken.
* Monitors kunnen dynamische intervallen hebben: de wacht tijd kan worden gewijzigd op basis van een voor waarde.
* Monitors kunnen worden beëindigd als aan een bepaalde voor waarde is voldaan of door een ander proces wordt beëindigd.
* Monitors kunnen para meters hebben. In het voor beeld ziet u hoe hetzelfde proces voor weer bewaking kan worden toegepast op elke aangevraagde locatie en telefoon nummer.
* Monitors zijn schaalbaar. Omdat elke monitor een indelings exemplaar is, kunnen er meerdere monitors worden gemaakt zonder nieuwe functies te maken of meer code te definiëren.
* Monitors kunnen eenvoudig worden geïntegreerd in grotere werk stromen. Een monitor kan één sectie van een complexere Orchestration-functie of een subindeling [zijn.](durable-functions-sub-orchestrations.md)

## <a name="configuring-twilio-integration"></a>Twilio-integratie configureren

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Ondergrondse integratie configureren

In dit voor beeld moet u de weers ondergrondse API gebruiken om de huidige weers omstandigheden voor een locatie te controleren.

Het eerste wat u nodig hebt, is een weers ondergrondse-account. U kunt een gratis abonnement maken op [https://www.wunderground.com/signup](https://www.wunderground.com/signup). Zodra u een account hebt, moet u een API-sleutel aanschaffen. U kunt dit doen door naar [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)te gaan en vervolgens sleutel instellingen te selecteren. Het Stratus-ontwikkelaars abonnement is gratis en voldoende om dit voor beeld uit te voeren.

Zodra u een API-sleutel hebt, voegt u de volgende **app-instelling** toe aan uw functie-app.

| Naam van app-instelling | Waarde beschrijving |
| - | - |
| **WeatherUndergroundApiKey**  | Uw weers-en ondergrondse API-sleutel. |

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app uitgelegd:

* `E3_Monitor`: een Orchestrator-functie die regel matig `E3_GetIsClear` aanroept. Het roept `E3_SendGoodWeatherAlert` als `E3_GetIsClear` waar retourneert.
* `E3_GetIsClear`: een activiteit functie die de huidige weers omstandigheden voor een locatie controleert.
* `E3_SendGoodWeatherAlert`: een activiteit functie waarmee een SMS-bericht via Twilio wordt verzonden.

In de volgende secties worden de configuratie en code uitgelegd die worden C# gebruikt voor het uitvoeren van scripts en Java script. De code voor Visual Studio-ontwikkeling wordt aan het einde van het artikel weer gegeven.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>De weer gave-indeling (Visual Studio code en Azure Portal voorbeeld code)

De functie **E3_Monitor** maakt gebruik van de standaard *functie. json* voor Orchestrator-functies.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Hier volgt de code voor het implementeren van de functie:

### <a name="c-script"></a>C# Script

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Deze Orchestrator-functie voert de volgende acties uit:

1. Hiermee wordt de **MonitorRequest** opgehaald die bestaat uit de te bewaken *locatie* en het *telefoon nummer* waarnaar een SMS-bericht wordt verzonden.
2. Bepaalt de verloop tijd van de monitor. In het voor beeld wordt een in code vastgelegde waarde gebruikt voor de boog.
3. Roept **E3_GetIsClear** aan om te bepalen of er op de aangevraagde locatie duidelijke skies zijn.
4. Als de weer gave duidelijk is, roept **E3_SendGoodWeatherAlert** om een SMS-bericht naar het aangevraagde telefoon nummer te verzenden.
5. Hiermee maakt u een duurzame timer om de indeling te hervatten bij het volgende polling-interval. In het voor beeld wordt een in code vastgelegde waarde gebruikt voor de boog.
6. Blijft actief totdat de `CurrentUtcDateTime` (.NET) of `currentUtcDateTime` (Java script) de verloop tijd van de monitor geeft of een SMS-waarschuwing wordt verzonden.

Meerdere Orchestrator-exemplaren kunnen tegelijkertijd worden uitgevoerd door meerdere **MonitorRequests**te verzenden. De locatie die moet worden bewaakt en het telefoon nummer waarnaar een SMS-waarschuwing moet worden verzonden.

## <a name="strongly-typed-data-transfer-net-only"></a>Sterk getypeerde gegevens overdracht (alleen .NET)

Voor de Orchestrator zijn meerdere gegevens nodig, waardoor [gedeelde poco-objecten](../functions-reference-csharp.md#reusing-csx-code) worden gebruikt voor gegevens overdracht met een hoge waarde C# in C# en script:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

In het Java script-voor beeld worden reguliere JSON-objecten gebruikt als para meters.

## <a name="helper-activity-functions"></a>Functies van de Help-activiteit

Net als bij andere voor beelden zijn de functies van de Help-activiteit reguliere functies die gebruikmaken van de `activityTrigger` trigger binding. De functie **E3_GetIsClear** haalt de huidige weers omstandigheden op met behulp van de weers ondergrondse API en bepaalt of de lucht duidelijk is. De *functie. json* wordt als volgt gedefinieerd:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

En dit is de implementatie. Net als de POCOs die wordt gebruikt voor gegevens overdracht, is logica voor het afhandelen van de API-aanroep en het parseren C#van de reactie-json in een gedeelde klasse in. U kunt het vinden als onderdeel van de [Visual Studio-voorbeeld code](#run-the-sample).

### <a name="c-script"></a>C# Script

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

De functie **E3_SendGoodWeatherAlert** maakt gebruik van de Twilio-binding voor het verzenden van een SMS-bericht met de mede deling dat het een goede tijd is voor een walk. De *functie. json* is eenvoudig:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

En dit is de code die het SMS-bericht verzendt:

### <a name="c-script"></a>C# Script

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Met de met HTTP geactiveerde functies die in het voor beeld zijn opgenomen, kunt u de indeling starten door de volgende HTTP POST-aanvraag te verzenden:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Het **E3_Monitor** exemplaar wordt gestart en voert een query uit op de huidige weers omstandigheden voor de aangevraagde locatie. Als het weer duidelijk is, wordt een activiteit functie aangeroepen om een waarschuwing te verzenden. anders wordt een timer ingesteld. Wanneer de timer is verlopen, wordt de indeling hervat.

U kunt de activiteit van de Orchestration bekijken door te kijken naar de functie Logboeken in de Azure Functions Portal.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

De indeling wordt [beëindigd](durable-functions-instance-management.md) zodra de time-out is bereikt of als er duidelijke skies zijn gedetecteerd. U kunt ook `TerminateAsync` (.NET) of `terminate` (Java script) in een andere functie gebruiken of de **terminatePostUri** http post-webhook aanroepen waarnaar wordt verwezen in het 202-antwoord hierboven, waarbij u `{text}` vervangt door de reden voor beëindiging:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-voorbeeld code

Dit is de indeling als één C# bestand in een Visual Studio-project:

> [!NOTE]
> Als u de voorbeeld code hieronder wilt uitvoeren, moet u het `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget-pakket installeren.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld wordt gedemonstreerd hoe u Durable Functions kunt gebruiken om de status van een externe bron te bewaken met behulp van [duurzame timers](durable-functions-timers.md) en voorwaardelijke logica. In het volgende voor beeld ziet u hoe u externe gebeurtenissen en [duurzame timers](durable-functions-timers.md) gebruikt om menselijke interactie te verwerken.

> [!div class="nextstepaction"]
> [Het voor beeld voor menselijke interactie uitvoeren](durable-functions-phone-verification.md)
