---
title: Menselijke interactie en time-outs in duurzame functies - Azure
description: Meer informatie over het omgaan met menselijke interactie en time-outs in de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335748"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Menselijke interactie in duurzame functies - Voorbeeld van telefonische verificatie

Dit voorbeeld laat zien hoe je een [orkestratie voor duurzame functies](durable-functions-overview.md) bouwen die menselijke interactie met zich meebrengt. Wanneer een echte persoon betrokken is bij een geautomatiseerd proces, moet het proces in staat zijn om meldingen naar de persoon te verzenden en antwoorden asynchroon te ontvangen. Het moet ook zorgen voor de mogelijkheid dat de persoon niet beschikbaar is. (Dit laatste deel is waar time-outs belangrijk worden.)

Dit voorbeeld implementeert een sms-gebaseerd telefoonverificatiesysteem. Deze typen stromen worden vaak gebruikt bij het verifiëren van het telefoonnummer van een klant of voor multi-factor authenticatie (MFA). Het is een krachtig voorbeeld omdat de hele implementatie wordt gedaan met behulp van een paar kleine functies. Er is geen extern gegevensarchief, zoals een database, vereist.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

Telefonische verificatie wordt gebruikt om te controleren of eindgebruikers van uw toepassing geen spammers zijn en dat ze zijn wie ze zeggen dat ze zijn. Multi-factor authenticatie is een veel voorkomende use case voor het beschermen van gebruikersaccounts tegen hackers. De uitdaging met het implementeren van uw eigen telefoon verificatie is dat het vereist een **stateful interactie** met een mens. Een eindgebruiker krijgt doorgaans een code (bijvoorbeeld een 4-cijferig getal) en moet **binnen een redelijke tijd**reageren.

Gewone Azure-functies zijn stateloos (net als veel andere cloudeindpunten op andere platforms), dus dit soort interacties omvatten expliciet extern beheer van de status in een database of een andere permanente opslag. Bovendien moet de interactie worden opgesplitst in meerdere functies die samen kunnen worden gecoördineerd. U hebt bijvoorbeeld ten minste één functie nodig om te beslissen over een code, deze ergens te blijven gebruiken en deze naar de telefoon van de gebruiker te verzenden. Bovendien hebt u ten minste één andere functie nodig om een antwoord van de gebruiker te ontvangen en deze op de een of andere manier terug te sturen naar de oorspronkelijke functieaanroep om de codevalidatie te doen. Een time-out is ook een belangrijk aspect om de veiligheid te waarborgen. Het kan vrij complex snel.

De complexiteit van dit scenario wordt aanzienlijk verminderd wanneer u duurzame functies gebruikt. Zoals u in dit voorbeeld ziet, kan een orchestrator-functie de stateful interactie eenvoudig beheren zonder externe gegevensopslag te betrekken. Omdat orchestrator functies *duurzaam*zijn, zijn deze interactieve stromen ook zeer betrouwbaar.

## <a name="configuring-twilio-integration"></a>Twilio-integratie configureren

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voorbeeld-app doorlopen:

* `E4_SmsPhoneVerification`: Een [orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die het verificatieproces voor de telefoon uitvoert, inclusief het beheren van time-outs en nieuwe pogingen.
* `E4_SendSmsChallenge`: Een [activiteitsfunctie](durable-functions-bindings.md#activity-trigger) die een code via sms verzendt.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification orchestrator-functie

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Het is misschien niet duidelijk op het eerste, maar deze orchestrator functie is volledig deterministisch. Het is deterministisch `CurrentUtcDateTime` omdat de eigenschap wordt gebruikt om de verlooptijd van de timer te berekenen, en het geeft dezelfde waarde op elke herhaling op dit punt in de orchestrator-code. Dit gedrag is belangrijk om `winner` ervoor te zorgen `Task.WhenAny`dat dezelfde resultaten van elke herhaalde oproep naar .

# <a name="javascript"></a>[Javascript](#tab/javascript)

De **functie E4_SmsPhoneVerification** maakt gebruik van de *standaardfunctie.json* voor orchestrator-functies.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Hier is de code die de functie implementeert:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Het is misschien niet duidelijk op het eerste, maar deze orchestrator functie is volledig deterministisch. Het is deterministisch `currentUtcDateTime` omdat de eigenschap wordt gebruikt om de verlooptijd van de timer te berekenen, en het geeft dezelfde waarde op elke herhaling op dit punt in de orchestrator-code. Dit gedrag is belangrijk om `winner` ervoor te zorgen `context.df.Task.any`dat dezelfde resultaten van elke herhaalde oproep naar .

---

Eenmaal gestart, doet deze orchestrator-functie het volgende:

1. Krijgt een telefoonnummer waarop het de SMS-melding zal *verzenden.*
2. Oproepen **E4_SendSmsChallenge** om een sms-bericht naar de gebruiker te sturen en retourneert de verwachte 4-cijferige uitdagingscode.
3. Hiermee maakt u een duurzame timer die 90 seconden vanaf de huidige tijd activeert.
4. Parallel met de timer, wacht op een **SmsChallengeResponse** evenement van de gebruiker.

De gebruiker ontvangt een sms-bericht met een viercijferige code. Ze hebben 90 seconden om diezelfde viercijferige code terug te sturen naar de orchestrator-functieinstantie om het verificatieproces te voltooien. Als ze de verkeerde code indienen, krijgen ze nog eens drie pogingen om het goed te krijgen (binnen hetzelfde venster van 90 seconden).

> [!WARNING]
> Het is belangrijk om timers te [annuleren](durable-functions-timers.md) als u ze niet langer nodig hebt om te verlopen, zoals in het bovenstaande voorbeeld wanneer een uitdagingsreactie wordt geaccepteerd.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge activiteitsfunctie

De **E4_SendSmsChallenge-functie** gebruikt de Twilio-binding om het sms-bericht met de viercijferige code naar de eindgebruiker te sturen.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Je moet het `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget-pakket installeren om de voorbeeldcode uit te voeren.

# <a name="javascript"></a>[Javascript](#tab/javascript)

De *functie.json* wordt als volgt gedefinieerd:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

En hier is de code die de vier-cijferige uitdaging code genereert en stuurt de SMS-bericht:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Met behulp van de HTTP-geactiveerde functies in het voorbeeld u de orkestratie starten door de volgende HTTP POST-aanvraag te verzenden:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

De orchestrator-functie ontvangt het opgegeven telefoonnummer en stuurt het onmiddellijk een sms-bericht met een willekeurig gegenereerde 4-cijferige verificatiecode &mdash; bijvoorbeeld, *2168*. De functie wacht dan 90 seconden op een reactie.

Als u met de code wilt antwoorden, u [ `RaiseEventAsync` (.NET) of `raiseEvent` (JavaScript)](durable-functions-instance-management.md) in een andere functie gebruiken of de **sendEventUrl** HTTP POST-webhook aanroepen waarnaar in het bovenstaande antwoord van 202 wordt verwezen, vervangen `{eventName}` door de naam van de gebeurtenis: `SmsChallengeResponse`

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Als u dit verzendt voordat de timer verloopt, `output` wordt de `true`orkestratie voltooid en wordt het veld ingesteld op , wat wijst op een geslaagde verificatie.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Als u de timer laat verlopen of als u vier keer de verkeerde `false` code invoert, u de status opvragen en een uitvoer van de orchestration-functie zien, wat aangeeft dat de telefoonverificatie is mislukt.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Volgende stappen

Dit voorbeeld heeft aangetoond dat een aantal van `WaitForExternalEvent` de `CreateTimer` geavanceerde mogelijkheden van duurzame functies, met name en API's. Je hebt gezien hoe deze kunnen `Task.WaitAny` worden gecombineerd met het implementeren van een betrouwbaar time-out systeem, dat vaak handig is voor interactie met echte mensen. U meer te weten komen over het gebruik van duurzame functies door een reeks artikelen te lezen die een diepgaande dekking van specifieke onderwerpen bieden.

> [!div class="nextstepaction"]
> [Ga naar het eerste artikel in de serie](durable-functions-bindings.md)
