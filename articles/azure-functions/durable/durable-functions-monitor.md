---
title: Monitoren in duurzame functies - Azure
description: Meer informatie over het implementeren van een statusmonitor met de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562119"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Monitor scenario in duurzame functies - Weerwatcher voorbeeld

Het monitorpatroon verwijst naar een flexibel *terugkerend* proces in een werkstroom - bijvoorbeeld polling totdat aan bepaalde voorwaarden is voldaan. In dit artikel wordt een voorbeeld uitgelegd dat [duurzame functies](durable-functions-overview.md) gebruikt om monitoring te implementeren.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

Dit voorbeeld bewaakt de huidige weersomstandigheden van een locatie en waarschuwt een gebruiker per sms wanneer de lucht helder is. U een normale functie met timergeactiveerde gebruiken om het weer te controleren en waarschuwingen te verzenden. Echter, een probleem met deze aanpak is **lifetime management**. Als er slechts één waarschuwing moet worden verzonden, moet de monitor zichzelf uitschakelen nadat helder weer is gedetecteerd. Het bewakingspatroon kan onder andere een einde maken aan de eigen uitvoering:

* Monitoren draaien op intervallen, niet op schema's: een timertrigger wordt elk uur *uitgevoerd;* een monitor *wacht* een uur tussen acties. De acties van een monitor overlappen elkaar niet, tenzij gespecificeerd, wat belangrijk kan zijn voor langlopende taken.
* Monitoren kunnen dynamische intervallen hebben: de wachttijd kan veranderen op basis van een bepaalde voorwaarde.
* Monitoren kunnen worden beëindigd wanneer aan een bepaalde voorwaarde is voldaan of door een ander proces worden beëindigd.
* Monitoren kunnen parameters aannemen. Het voorbeeld laat zien hoe hetzelfde weerbewakingsproces kan worden toegepast op elke gevraagde locatie en telefoonnummer.
* Monitoren zijn schaalbaar. Omdat elke monitor een orchestration-instantie is, kunnen meerdere beeldschermen worden gemaakt zonder dat u nieuwe functies hoeft te maken of meer code hoeft te definiëren.
* Monitoren integreren eenvoudig in grotere workflows. Een monitor kan een sectie van een complexere orkestratiefunctie zijn, of een [suborchestration.](durable-functions-sub-orchestrations.md)

## <a name="configuration"></a>Configuratie

### <a name="configuring-twilio-integration"></a>Twilio-integratie configureren

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Weerondergrondse integratie configureren

Dit voorbeeld omvat het gebruik van de Weather Underground API om de huidige weersomstandigheden voor een locatie te controleren.

Het eerste wat je nodig hebt is een Weather Underground account. U er een [https://www.wunderground.com/signup](https://www.wunderground.com/signup)gratis maken op. Zodra u een account hebt, moet u een API-sleutel aanschaffen. U dit [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)doen door naar te gaan en vervolgens Sleutelinstellingen te selecteren. Het Stratus Developer plan is gratis en voldoende om dit voorbeeld uit te voeren.

Zodra u een API-sleutel hebt, voegt u de volgende **app-instelling** toe aan uw functie-app.

| Naam van app-instelling | Waardebeschrijving |
| - | - |
| **WeatherUndergroundapiKey**  | Uw Weather Underground API-sleutel. |

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voorbeeld-app uitgelegd:

* `E3_Monitor`: Een [orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die periodiek aanroept. `E3_GetIsClear` Het `E3_SendGoodWeatherAlert` roept `E3_GetIsClear` als retourneert waar.
* `E3_GetIsClear`: Een [activiteitsfunctie](durable-functions-bindings.md#activity-trigger) die de huidige weersomstandigheden voor een locatie controleert.
* `E3_SendGoodWeatherAlert`: Een activiteitsfunctie die een sms-bericht via Twilio verzendt.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor orchestrator-functie

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

De orchestrator vereist een locatie te controleren en een telefoonnummer om een bericht te sturen naar wanneer de of wordt duidelijk op de locatie. Deze gegevens worden doorgegeven aan de orchestrator als een sterk getypt `MonitorRequest` object.

# <a name="javascript"></a>[Javascript](#tab/javascript)

De **E3_Monitor** functie maakt gebruik van de standaard *function.json* voor orchestrator functies.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Hier is de code die de functie implementeert:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Met deze orchestrator-functie worden de volgende acties uitgevoerd:

1. Hiermee wordt de **MonitorRequest** weergegeven, bestaande uit de te controleren *locatie* en het *telefoonnummer* waarop het een sms-melding zal verzenden.
2. Hiermee bepaalt u de vervaldatum van de monitor. Het monster maakt gebruik van een hard-gecodeerde waarde voor beknoptheid.
3. Oproepen **E3_GetIsClear** om te bepalen of er heldere luchten op de gevraagde locatie.
4. Als het weer duidelijk is, **bellen** E3_SendGoodWeatherAlert om een sms-melding te sturen naar het gevraagde telefoonnummer.
5. Hiermee maakt u een duurzame timer om de orkestratie bij het volgende polling-interval te hervatten. Het monster maakt gebruik van een hard-gecodeerde waarde voor beknoptheid.
6. Blijft actief totdat de huidige UTC-tijd de vervaldatum van de monitor heeft bereikt of er een sms-melding wordt verzonden.

Meerdere orchestrator-exemplaren kunnen gelijktijdig worden uitgevoerd door de orchestrator-functie meerdere keren aan te roepen. De locatie om te controleren en het telefoonnummer om een SMS-melding naar te verzenden, kan worden opgegeven.

### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear activiteitsfunctie

Net als bij andere monsters zijn de activiteitsfuncties van de helper reguliere functies die de `activityTrigger` triggerbinding gebruiken. De **E3_GetIsClear** functie krijgt de huidige weersomstandigheden met behulp van de Weather Underground API en bepaalt of de lucht helder is.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

De *functie.json* wordt als volgt gedefinieerd:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

En hier is de uitvoering.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert activiteitsfunctie

De **E3_SendGoodWeatherAlert-functie** maakt gebruik van de Twilio-binding om een sms-bericht te sturen waarin de eindgebruiker wordt op de hoogte gebracht dat het een goed moment is voor een wandeling.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Je moet het `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget-pakket installeren om de voorbeeldcode uit te voeren.

# <a name="javascript"></a>[Javascript](#tab/javascript)

De *functie.json* is eenvoudig:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

En hier is de code die het SMS-bericht stuurt:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Met behulp van de HTTP-geactiveerde functies in het voorbeeld u de orkestratie starten door de volgende HTTP POST-aanvraag te verzenden:

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

De **E3_Monitor** instantie start en bevraagt de huidige weersomstandigheden voor de aangevraagde locatie. Als het weer helder is, roept het een activiteitsfunctie aan om een waarschuwing te verzenden; anders wordt een timer ingesteld. Wanneer de timer verloopt, wordt de orkestratie hervat.

U de activiteit van de orchestration bekijken door te kijken naar de functielogboeken in de Azure Functions-portal.

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

De orkestratie [eindigt](durable-functions-instance-management.md) zodra de time-out is bereikt of heldere luchten worden gedetecteerd. U `TerminateAsync` ook (.NET) of `terminate` (JavaScript) gebruiken in een andere functie of beroep doen op de `{text}` **terminatePostUri** HTTP POST webhook waarnaar in de bovenstaande 202-reactie wordt verwezen, vervangen door de reden voor beëindiging:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Volgende stappen

In dit voorbeeld is aangetoond hoe u duurzame functies gebruiken om de status van een externe bron te controleren met behulp van [duurzame timers](durable-functions-timers.md) en voorwaardelijke logica. In het volgende voorbeeld ziet u hoe u externe gebeurtenissen en [duurzame timers](durable-functions-timers.md) gebruiken om menselijke interactie te verwerken.

> [!div class="nextstepaction"]
> [Het voorbeeld van menselijke interactie uitvoeren](durable-functions-phone-verification.md)
