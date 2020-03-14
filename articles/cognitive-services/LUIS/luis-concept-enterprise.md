---
title: Zakelijke concepten-LUIS
titleSuffix: Azure Cognitive Services
description: Ontwerpconcepten voor grote LUIS-apps of meerdere apps, met inbegrip van LUIS en QnA Maker samen begrijpen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221060"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Enterprise-strategieën voor een LUIS-app
Bekijk deze ontwerpstrategieën voor uw enterprise-app.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Wanneer u verwacht dat het quotum LUIS aanvragen

LUIS heeft een maandelijks quotum en een quotum per seconde, gebaseerd op de prijs categorie van de Azure-resource. 

Als uw aanvraag frequentie voor de LUIS-app groter is dan het toegestane [quotum](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), kunt u het volgende doen:

* Breid de belasting naar meer LUIS-apps uit met [dezelfde app-definitie](#use-multiple-apps-with-same-app-definition). Dit omvat, eventueel, het uitvoeren van LUIS uit een [container](luis-container-howto.md). 
* [Meerdere sleutels](#assign-multiple-luis-keys-to-same-app) maken en toewijzen aan de app. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Meerdere apps met dezelfde definitie van de app gebruiken
De oorspronkelijke LUIS-app exporteren en importeren van de app terug in afzonderlijke apps. Elke app heeft een eigen app-ID. Wanneer u publiceert, in plaats van met dezelfde sleutel in alle apps, maakt u een aparte sleutel voor elke app. Verdelen in alle apps zodanig dat geen enkele app wordt overbelast. Voeg [Application Insights](luis-tutorial-bot-csharp-appinsights.md) toe om het gebruik te bewaken. 

Om toegang te krijgen van de dezelfde bovenste intentie tussen alle apps, zorg ervoor dat de intentie voorspelling tussen het eerste en tweede doel is groot genoeg dat LUIS niet worden verward is, die verschillende resultaten oplevert tussen apps voor kleine variaties in uitingen. 

Bij het trainen van deze apps op hetzelfde niveau, moet u ervoor zorgen dat [alle gegevens worden getraind](luis-how-to-train.md#train-with-all-data).

Een enkele app aanwijzen als de master. Alle uitingen die worden voorgesteld voor controle moeten worden toegevoegd aan de hoofd-app vervolgens verplaatst naar alle andere apps. Dit is een volledige export van de app, of het laden van de gelabelde uitingen van het model aan de onderliggende objecten. Het laden kan worden uitgevoerd vanaf de [Luis](luis-reference-regions.md) -website of de ontwerp-API voor [één utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) of voor een [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Plan een periodieke beoordeling, bijvoorbeeld elke twee weken, van het [eind punt uitingen](luis-how-to-review-endpoint-utterances.md) voor het actieve leer proces en vervolgens opnieuw te trainen en opnieuw te publiceren. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Meerdere LUIS sleutels aan dezelfde app toewijzen
Als uw LUIS-app meer eindpunt treffers dan is toegestaan door uw één sleutel quotum ontvangt, maken en toewijzen van meer sleutels aan de LUIS-app. Maken van een traffic manager of load balancer voor het beheren van de eindpunt-query's in de eindpunt-sleutels. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Wanneer uw monolithische app verkeerde bedoelingen retourneert
Als uw app een breed scala aan gebruikers uitingen moet voors pellen, kunt u overwegen het [Verzend model](#dispatch-tool-and-model)te implementeren. Een monolithische app worden opgesplitst kunt LUIS focus detectie tussen intents is in plaats van de tussen intents via de app bovenliggende en onderliggende apps maar verwarrend. 

Plan een periodieke [beoordeling van het uitingen](luis-how-to-review-endpoint-utterances.md) voor het actieve leer proces, bijvoorbeeld elke twee weken, en voer vervolgens opnieuw een Train en opnieuw uit. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Als u wilt hebben van meer dan 500 intents
Stel dat u een Office-assistent ontwikkelt met meer dan 500 intenties. Als 200 intents verwant zijn aan de vergaderingen plannen, 200 herinneringen zijn, 200 zijn over het ophalen van informatie over collega's, en 200 zijn voor het verzenden van e-mailbericht, groep intents zodat elke groep is in een enkele app vervolgens maakt u een op het hoogste niveau app die elk doel. Gebruik het [Verzend model](#dispatch-tool-and-model) om de app op het hoogste niveau te bouwen. Wijzig vervolgens uw bot om de trapsgewijze oproep te gebruiken, zoals wordt weer gegeven in de [zelf studie over het verzend model](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Wanneer u moet verschillende LUIS en QnA maker toepassingen combineren
Als u meerdere LUIS-en QnA Maker-apps hebt die moeten reageren op een bot, gebruikt u het [Verzend model](#dispatch-tool-and-model) om de app op het hoogste niveau te bouwen.  Wijzig vervolgens uw bot om de trapsgewijze oproep te gebruiken, zoals wordt weer gegeven in de [zelf studie over het verzend model](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Hulpprogramma voor verzending en het model
Gebruik het opdracht regel programma voor [verzen ding][dispatch-tool] , gevonden in [BotBuilder-hulpprogram MA'S](https://github.com/Microsoft/botbuilder-tools) om meerdere LUIS en/of QnA Maker apps te combi NEREN in een bovenliggende Luis-app. Deze aanpak kunt u een bovenliggende domein met inbegrip van alle onderwerpen en verschillende onderliggende onderwerpsdomeinen in afzonderlijke apps. 

![Conceptuele afbeelding van verzending van de architectuur](./media/luis-concept-enterprise/dispatch-architecture.png)

Het bovenliggende domein wordt vermeld in LUIS met een versie met de naam `Dispatch` in de lijst met apps. 

De chat-bot ontvangt de utterance en verzendt deze vervolgens naar de bovenliggende LUIS-app voor voor spellingen. De eerste voorspelde intentie van de bovenliggende app bepaalt welke LUIS onderliggende app het volgende wordt genoemd. De chat-bot verzendt de utterance naar de onderliggende app voor een specifiekere voor spelling.

Meer informatie over hoe deze hiërarchie van aanroepen wordt gedaan vanuit de Bot Builder v4 [-verzender-toepassing-zelf studie](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Intentie limieten in model voor verzending
Een toepassing verzending heeft 500 verzending bronnen, gelijk aan 500 intents als het maximum. 

## <a name="more-information"></a>Meer informatie

* [Bot Framework SDK](https://github.com/Microsoft/botframework)
* [Zelf studie over verzend modellen](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Verzending CLI](https://github.com/Microsoft/botbuilder-tools)
* Voor beeld van verspreidings model-bot- [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [testen van een batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
