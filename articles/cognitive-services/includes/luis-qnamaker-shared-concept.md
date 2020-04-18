---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 02610e647e2138cbf52f86c22107feec2d61273b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604971"
---
Cognitive Services biedt twee natuurlijke taalverwerkingsservices, [Language Understanding](../luis/what-is-luis.md) en [QnA Maker,](../qnamaker/overview/overview.md)elk met een ander doel. Begrijp wanneer u elke service moet gebruiken en hoe ze elkaar complimenteren.

Met Natural Language Processing (NLP) kan uw clienttoepassing, zoals een chatbot, met uw gebruikers werken met behulp van natuurlijke taal. Een gebruiker voert een zin of zin in. De tekst van de gebruiker kan een slechte grammatica, spelling en interpunctie hebben. De Cognitieve Service kan werken door de gebruiker zin toch, het retourneren van informatie van de chat bot nodig heeft om de gebruiker te helpen.

## <a name="cognitive-services-with-nlp"></a>Cognitieve services met NLP

Language Understanding (LUIS) en QnA Maker bieden NLP. De clientaanvraag dient tekst in natuurlijke taal in. De service neemt de tekst, verwerkt deze en retourneert een resultaat.

## <a name="when-to-use-each-service"></a>Wanneer elke service te gebruiken

Language Understanding (LUIS) en QnA Maker lossen verschillende problemen op. LUIS bepaalt de bedoeling van de tekst van een gebruiker (bekend als een utterance), terwijl QnA Maker het antwoord bepaalt op de tekst van een gebruiker (een query genoemd).

Om de juiste service te kiezen, moet u de gebruikerstekst van de clienttoepassing begrijpen en welke informatie de clienttoepassing moet krijgen van de Cognitive Service.

Als uw chatbot `How do I get to the Human Resources building on the Seattle North campus?`de tekst ontvangt, gebruikt u de onderstaande grafiek om te begrijpen hoe elke service met de tekst werkt.

|Service|Clienttoepassing bepaalt|
|--|--|
|LUIS|Hiermee bepaalt u de intentie van de gebruiker om tekst te **sms'en** - de service geeft het antwoord op de vraag niet terug. Deze tekst wordt bijvoorbeeld geclassificeerd `FindLocation` als overeenkomend met de intentie.<br>|
|QnA Maker|**Retourneert het antwoord op de vraag** vanuit een aangepaste kennisbank. Deze tekst wordt bijvoorbeeld bepaald als een vraag `Get on the #9 bus and get off at Franklin street`met het statische tekstantwoord van .|
|||

## <a name="when-do-you-use-luis"></a>Wanneer gebruikt u LUIS?

Gebruik LUIS wanneer u de bedoeling van de uiting moet weten als onderdeel van een proces in de chatbot. `How do I get to the Human Resources building on the Seattle North campus?`Als u eenmaal weet dat de gebruiker een locatie wil vinden, u details over de utterance (uitgetrokken met entiteiten) doorgeven aan een andere service, zoals een transportserver, om het antwoord te krijgen.

U hoeft LUIS en QnA Maker niet te combineren om de intentie te bepalen.

U de twee services voor deze utterance combineren als de chatbot de tekst moet verwerken op basis van intenties en entiteiten (met LUIS) en het specifieke statische tekstantwoord moet vinden (met QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Wanneer gebruikt u QnA Maker?

Gebruik QnA Maker als je een statische kennisbasis van antwoorden hebt. Deze kennisbank is aangepast aan uw behoeften, die u hebt gebouwd met documenten zoals PDF's en URL's.

Doorgaan met de `How do I get to the Human Resources building on the Seattle North campus?`voorbeeldutterance, stuur de tekst als query naar uw gepubliceerde QnA Maker-service en ontvang het beste antwoord.

U hoeft LUIS en QnA Maker niet te combineren om het antwoord op de vraag te bepalen.

U de twee services voor deze utterance combineren als de chatbot de tekst moet verwerken op basis van intenties en entiteiten (met LUIS) en het antwoord moet vinden (met QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Beide services gebruiken wanneer uw kennisbank onvolledig is

Als u uw QnA Maker-kennisbank bouwt, maar weet dat het onderwerpdomein verandert (zoals tijdige informatie), u luis- en QnA Maker-services combineren. Hierdoor u de informatie in uw kennisbank gebruiken, maar ook LUIS gebruiken om de intentie van een gebruiker te bepalen. Zodra de clientapplicatie de intentie heeft, kan deze relevante informatie opvragen bij een andere bron.

Uw clienttoepassing moet zowel LUIS- als QnA Maker-antwoorden controleren op scores. Als de score van QnA Maker onder een willekeurige drempelwaarde ligt, gebruikt u de intentie- en entiteitsgegevens die van LUIS zijn geretourneerd om de informatie door te geven aan een service van derden.

Als u verdergaat `How do I get to the Human Resources building on the Seattle North campus?`met de voorbeeldtekst, u ervoor zorgen dat QnA Maker een lage betrouwbaarheidsscore retourneert. Gebruik de intentie die `FindLocation` is geretourneerd van LUIS `Human Resources building` en `Seattle North campus`alle geëxtraheerde entiteiten, zoals en , om deze informatie naar een toewijzings- of zoekservice te verzenden voor een ander antwoord.

U dit antwoord van derden aan de gebruiker voor validatie presenteren. Zodra u de goedkeuring van de gebruiker hebt, u teruggaan naar QnA Maker om de informatie toe te voegen om uw kennis te laten groeien.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Beide services gebruiken wanneer uw chatbot meer informatie nodig heeft

Als uw chatbot meer informatie nodig heeft dan een van beide services biedt, gebruikt u beide services en verwerkt u beide antwoorden in de clienttoepassing om door een beslissingsstructuur te gaan.

Gebruik de Tool Bot framework **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** om een proces op te bouwen om met beide services te werken. Deze tool bouwt een top LUIS-app met intents die wordt verzonden tussen LUIS en QnA Maker als onderliggende apps.

Gebruik het voorbeeld van de Bot-bouwer, **NLP met verzending**, in [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) of [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), om dit type chatbot te implementeren.

## <a name="best-practices"></a>Aanbevolen procedures

Implementeer aanbevolen procedures voor elke service:

* [LUIS](../luis/luis-concept-best-practices.md) best practices
* Best practices voor [QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Zie ook

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Verzending CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot framework samples](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure-botservice](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot framework webchat](https://github.com/microsoft/BotFramework-WebChat)