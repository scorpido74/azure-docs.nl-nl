---
title: Ondernemingsconcepten - LUIS
titleSuffix: Azure Cognitive Services
description: Ontwerpconcepten voor grote LUIS-apps of meerdere apps begrijpen, waaronder LUIS en QnA Maker samen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221060"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Ondernemingsstrategieën voor een LUIS-app
Bekijk deze ontwerpstrategieën voor uw bedrijfsapp.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Wanneer u LUIS-aanvragen buiten het quotum verwacht

LUIS heeft een maandelijks quotum en een quotum per seconde, gebaseerd op de prijscategorie van de Azure-bron. 

Als het aanvraagpercentage van uw LUIS-app hoger is dan het toegestane [quotum,](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)u:

* Verspreid de belasting naar meer LUIS-apps met [dezelfde app-definitie.](#use-multiple-apps-with-same-app-definition) Dit omvat, optioneel, het uitvoeren van LUIS vanuit een [container](luis-container-howto.md). 
* Meerdere sleutels aan de app maken en [toewijzen.](#assign-multiple-luis-keys-to-same-app) 

### <a name="use-multiple-apps-with-same-app-definition"></a>Meerdere apps gebruiken met dezelfde app-definitie
Exporteer de oorspronkelijke LUIS-app en importeer de app terug in afzonderlijke apps. Elke app heeft zijn eigen app-id. Wanneer u publiceert, maakt u in plaats van dezelfde sleutel in alle apps te gebruiken, een afzonderlijke sleutel voor elke app. Breng de belasting in balans tussen alle apps, zodat er geen enkele app wordt overweldigd. [Toepassingsinzichten](luis-tutorial-bot-csharp-appinsights.md) toevoegen om het gebruik te controleren. 

Om dezelfde topintentie tussen alle apps te krijgen, moet u ervoor zorgen dat de intentievoorspelling tussen de eerste en tweede intentie breed genoeg is dat LUIS niet wordt verward, waardoor verschillende resultaten tussen apps voor kleine variaties in uitingen worden verkregen. 

Bij het trainen van deze broer of zus apps, zorg ervoor dat [u trainen met alle gegevens](luis-how-to-train.md#train-with-all-data).

Wijs één app aan als het stramien. Alle uitingen die ter beoordeling worden voorgesteld, moeten aan de hoofd-app worden toegevoegd en vervolgens worden verplaatst naar alle andere apps. Dit is een volledige export van de app of het laden van de gelabelde uitingen van het model naar de kinderen. Laden kan vanaf de [LUIS-website](luis-reference-regions.md) of de ontwerp-API voor [één utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) of voor een [batch.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) 

Plan een periodieke beoordeling, zoals elke twee weken, van [eindpuntuitingen](luis-how-to-review-endpoint-utterances.md) voor actief leren en train en herpubliceert. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Meerdere LUIS-sleutels toewijzen aan dezelfde app
Als uw LUIS-app meer eindpunthits ontvangt dan het quotum van uw enkele sleutel toestaat, maakt en wijst u meer sleutels toe aan de LUIS-app. Maak een verkeersbeheerder of load balancer om de eindpuntquery's over de eindpuntsleutels te beheren. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Wanneer uw monolithische app de verkeerde intentie retourneert
Als uw app bedoeld is om een breed scala aan gebruikersuitingen te voorspellen, u overwegen het [verzendmodel](#dispatch-tool-and-model)te implementeren. Door een monolithische app op te breken, kan LUIS de detectie tussen intenties met succes richten in plaats van verward te raken tussen intenties in de bovenliggende app en onderliggende apps. 

Plan een periodieke [beoordeling van eindpuntuitingen](luis-how-to-review-endpoint-utterances.md) voor actief leren, zoals elke twee weken, en train en herpubliceert. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Wanneer u meer dan 500 intents moet hebben
Stel dat je een kantoorassistent ontwikkelt met meer dan 500 intenties. Als 200 intenties betrekking hebben op het plannen van vergaderingen, 200 zijn over herinneringen, 200 zijn over het verkrijgen van informatie over collega's, en 200 zijn voor het verzenden van e-mail, groepsintenties, zodat elke groep in een enkele app, maak dan een app op het hoogste niveau met elke intentie. Gebruik het [verzendmodel](#dispatch-tool-and-model) om de app op het hoogste niveau te bouwen. Wijzig vervolgens uw bot om de trapsgewijze aanroep te gebruiken zoals weergegeven in de zelfstudie van het [verzendmodel.](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs) 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Wanneer u verschillende LUIS- en QnA-maker-apps moet combineren
Als u meerdere LUIS- en QnA-maker-apps hebt die moeten reageren op een bot, gebruikt u het [verzendmodel](#dispatch-tool-and-model) om de app op het hoogste niveau te bouwen.  Wijzig vervolgens uw bot om de trapsgewijze aanroep te gebruiken zoals weergegeven in de zelfstudie van het [verzendmodel.](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs) 

## <a name="dispatch-tool-and-model"></a>Verzendgereedschap en -model
Gebruik het opdrachtregelgereedschap [Verzenden,][dispatch-tool] gevonden in [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) om meerdere LUIS- en/of QnA Maker-apps te combineren in een bovenliggende LUIS-app. Met deze aanpak u een bovenliggend domein hebben met alle onderwerpen en verschillende onderliggende onderwerpdomeinen in afzonderlijke apps. 

![Conceptueel beeld van verzendingsarchitectuur](./media/luis-concept-enterprise/dispatch-architecture.png)

Het bovenliggende domein wordt opgemerkt `Dispatch` in LUIS met een versie die in de lijst met apps wordt genoemd. 

De chatbot ontvangt de utterance en stuurt vervolgens naar de bovenliggende LUIS-app voor voorspelling. De bovenste voorspelde intentie van de bovenliggende app bepaalt welke LUIS-onderliggende app de volgende app wordt aangeroepen. De chatbot stuurt de utterance naar de onderliggende app voor een meer specifieke voorspelling.

Begrijp hoe deze hiërarchie van gesprekken wordt gemaakt vanuit de Bot Builder v4 [dispatcher-application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Intentielimieten in verzendmodel
Een verzendtoepassing heeft 500 verzendbronnen, gelijk aan 500 intents, als maximum. 

## <a name="more-information"></a>Meer informatie

* [Bot framework SDK](https://github.com/Microsoft/botframework)
* [Zelfstudie voor het verzenden van het bericht](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Verzending CLI](https://github.com/Microsoft/botbuilder-tools)
* Voorbeeld van het verzendmodel bot - [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [testen van een batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
