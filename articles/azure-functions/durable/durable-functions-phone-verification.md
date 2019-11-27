---
title: Menselijke interactie en time-outs in Durable Functions-Azure
description: Meer informatie over het verwerken van menselijke interacties en time-outs in de Durable Functions-extensie voor Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9346c53ec122b3e6fac124298029c7f8e70bf622
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232831"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Menselijke interactie in het voor beeld van een Durable Functions-telefoon verificatie

In dit voor beeld wordt gedemonstreerd hoe u een [Durable functions](durable-functions-overview.md) indeling bouwt waarbij menselijke interactie betrokken is. Wanneer een echte persoon betrokken is bij een geautomatiseerd proces, moet het proces meldingen kunnen verzenden naar de persoon en antwoorden asynchroon ontvangen. Het moet ook de mogelijkheid bieden dat de persoon niet beschikbaar is. (Dit laatste deel is waar time-outs belang rijk worden.)

Dit voor beeld implementeert een verificatie systeem op basis van een SMS-toestel. Deze soorten stromen worden vaak gebruikt bij het controleren van het telefoon nummer van een klant of voor multi-factor Authentication (MFA). Het is een krachtig voor beeld omdat de volledige implementatie wordt uitgevoerd met behulp van een paar kleine functies. Er is geen extern gegevens archief, zoals een Data Base, vereist.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

Verificatie via telefoon wordt gebruikt om te controleren of eind gebruikers van uw toepassing geen spammers zijn en dat ze zijn wie ze zeggen. Multi-factor Authentication is een veelvoorkomende use case voor het beveiligen van gebruikers accounts van hackers. De uitdaging bij het implementeren van uw eigen telefoon verificatie is dat er een **stateful interactie** is vereist met een menselijk. Een eind gebruiker krijgt meestal een code (bijvoorbeeld een nummer van vier cijfers) en moet **in een redelijke tijd**reageren.

Gewone Azure Functions zijn stateless (net als veel andere Cloud eindpunten op andere platforms), zodat deze typen interacties een expliciete status van extern beheer in een Data Base of een andere permanente Store moeten beheren. Daarnaast moet de interactie worden opgesplitst in meerdere functies die samen kunnen worden gecoördineerd. U hebt bijvoorbeeld ten minste één functie nodig om te beslissen over een code, ergens op te slaan en deze naar de telefoon van de gebruiker te verzenden. Daarnaast moet u ten minste één andere functie gebruiken om een reactie van de gebruiker te ontvangen en deze vervolgens weer te koppelen aan de oorspronkelijke functie aanroep om de code validatie uit te voeren. Een time-out is ook een belang rijk aspect om beveiliging te garanderen. Dit kan tamelijk complex zijn.

De complexiteit van dit scenario wordt sterk verkleind wanneer u Durable Functions gebruikt. Zoals u in dit voor beeld ziet, kan een Orchestrator-functie de stateful-interactie eenvoudig en zonder externe gegevens opslag beheren. Omdat Orchestrator-functies *duurzaam*zijn, zijn deze interactieve stromen ook zeer betrouwbaar.

## <a name="configuring-twilio-integration"></a>Twilio-integratie configureren

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app behandeld:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

In de volgende secties worden de configuratie en code uitgelegd die worden C# gebruikt voor het uitvoeren van scripts en Java script. De code voor Visual Studio-ontwikkeling wordt aan het einde van het artikel weer gegeven.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>De verificatie-indeling voor SMS (Visual Studio code en Azure Portal voorbeeld code)

De functie **E4_SmsPhoneVerification** maakt gebruik van de standaard *functie. json* voor Orchestrator-functies.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Hier volgt de code voor het implementeren van de functie:

### <a name="c-script"></a>C# Script

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Zodra deze functie is gestart, doet deze Orchestrator het volgende:

1. Hiermee wordt een telefoon nummer opgehaald waarnaar de SMS-melding wordt *verzonden* .
2. Roept **E4_SendSmsChallenge** op om een SMS-bericht naar de gebruiker te verzenden en retourneert de verwachte vraag code van 4 cijfers terug.
3. Maakt een duurzame timer die 90 seconden vanaf de huidige tijd activeert.
4. Parallel met de timer, wacht op een **SmsChallengeResponse** -gebeurtenis van de gebruiker.

De gebruiker ontvangt een SMS-bericht met een code van vier cijfers. Ze hebben 90 seconden om dezelfde 4-cijferige code terug te sturen naar het Orchestrator-functie exemplaar om het verificatie proces te volt ooien. Als ze de verkeerde code verzenden, krijgen ze een extra drie pogingen om het recht te krijgen (binnen hetzelfde 90-Second-venster).

> [!NOTE]
> Het is mogelijk niet altijd duidelijk, maar deze Orchestrator-functie is volledig deterministisch. Het is deterministisch omdat de eigenschappen van de `CurrentUtcDateTime` (.NET) en de `currentUtcDateTime` (Java script) worden gebruikt om de verloop tijd van de timer te berekenen. deze eigenschappen retour neren dezelfde waarde bij elke herspeel op dit punt in de Orchestrator-code. Dit gedrag is belang rijk om ervoor te zorgen dat dezelfde `winner` resulteert van elke herhaalde aanroep naar `Task.WhenAny` (.NET) of `context.df.Task.any` (Java script).

> [!WARNING]
> Het is belang rijk [timers te annuleren](durable-functions-timers.md) als u deze niet langer nodig hebt om te verlopen, zoals in het bovenstaande voor beeld wanneer een antwoord op een Challenge wordt geaccepteerd.

## <a name="send-the-sms-message"></a>SMS-bericht verzenden

De functie **E4_SendSmsChallenge** maakt gebruik van de Twilio-binding om het SMS-bericht met de 4-cijferige code naar de eind gebruiker te verzenden. De *functie. json* wordt als volgt gedefinieerd:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

En dit is de code die de vraag code van vier cijfers genereert en het SMS-bericht verzendt:

### <a name="c-script"></a>C# Script

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Deze **E4_SendSmsChallenge** functie wordt slechts eenmaal aangeroepen, zelfs als het proces vastloopt of opnieuw wordt afgespeeld. Dit is handig omdat u niet wilt dat de eind gebruiker meerdere SMS-berichten krijgt. De `challengeCode` geretourneerde waarde wordt automatisch gehandhaafd, zodat de Orchestrator-functie altijd weet wat de juiste code is.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Met de met HTTP geactiveerde functies die in het voor beeld zijn opgenomen, kunt u de indeling starten door de volgende HTTP POST-aanvraag te verzenden:

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

De Orchestrator-functie ontvangt het opgegeven telefoon nummer en verzendt dit onmiddellijk een SMS-bericht met een wille keurig gegenereerde verificatie code van 4 cijfers &mdash; bijvoorbeeld *2168*. De functie wacht vervolgens 90 seconden op een reactie.

Als u wilt antwoorden met de code, kunt u [`RaiseEventAsync` (.net) of `raiseEvent` (Java script)](durable-functions-instance-management.md) in een andere functie gebruiken of de **sendEventUrl** http post-webhook aanroepen waarnaar wordt verwezen in het 202-antwoord hierboven, waarbij u `{eventName}` vervangt door de naam van de gebeurtenis, `SmsChallengeResponse`:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Als u dit verzendt voordat de timer verloopt, wordt de indeling voltooid en wordt het `output` veld ingesteld op `true`, wat aangeeft dat de verificatie is geslaagd.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Als u de timer laat verlopen of als u de verkeerde code vier keer opgeeft, kunt u een query voor de status uitvoeren en een `false` Orchestration-functie-uitvoer bekijken, waarmee wordt aangegeven dat de telefoon verificatie is mislukt.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio-voorbeeld code

Dit is de indeling als één C# bestand in een Visual Studio-project:

> [!NOTE]
> Als u de voorbeeld code hieronder wilt uitvoeren, moet u het `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget-pakket installeren.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Volgende stappen

Dit voor beeld heeft een aantal geavanceerde mogelijkheden van Durable Functions, met name `WaitForExternalEvent` en `CreateTimer` Api's, gedemonstreerd. U hebt gezien hoe deze kunnen worden gecombineerd met `Task.WaitAny` voor het implementeren van een betrouw bare time-outsysteem, wat vaak nuttig is voor interactie met echte mensen. U vindt meer informatie over het gebruik van Durable Functions door een reeks artikelen te lezen die een gedetailleerde dekking van specifieke onderwerpen bieden.

> [!div class="nextstepaction"]
> [Ga naar het eerste artikel in de reeks](durable-functions-bindings.md)
