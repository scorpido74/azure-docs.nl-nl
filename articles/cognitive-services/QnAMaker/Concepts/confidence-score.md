---
title: Betrouwbaarheids Score-QnA Maker
titleSuffix: Azure Cognitive Services
description: Met de betrouwbaarheids score wordt het vertrouwen aangegeven dat het antwoord het juiste resultaat voor de opgegeven gebruikers query is.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a80c61efbcbff569f5fed53734def3979ed70616
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820749"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Betrouwbaarheids Score van een QnA Maker Knowledge Base
Wanneer een gebruikers query wordt vergeleken met een Knowledge Base, retourneert QnA Maker relevante antwoorden, samen met een betrouwbaarheids Score. Met deze score wordt het vertrouwen aangegeven dat het antwoord het juiste resultaat voor de opgegeven gebruikers query is. 

De betrouwbaarheids Score is een getal tussen 0 en 100. Een Score van 100 is waarschijnlijk een exacte overeenkomst, terwijl een Score van 0 betekent dat er geen overeenkomend antwoord is gevonden. Hoe hoger de score, hoe groter het vertrouwen in het antwoord is. Er kunnen meerdere antwoorden voor een bepaalde query worden geretourneerd. In dat geval worden de antwoorden geretourneerd op volg orde van afname van de betrouwbaarheids Score.

In het onderstaande voor beeld ziet u een QnA-entiteit, met twee vragen. 


![Voor beeld van QnA-paar](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Voor het bovenstaande voor beeld: u kunt scores zoals het onderstaande voor beeld Score bereik verwachten voor verschillende soorten gebruikers query's:


![Score bereik van de rang schikking](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


De volgende tabel geeft een typische betrouw baarheid aan dat is gekoppeld aan een bepaalde score.

|Score waarde|Betekenis van Score|Voorbeeld query|
|--|--|--|
|90-100|Bijna exacte overeenkomst met de gebruikers query en een KB-vraag|' Mijn wijzigingen worden na het publiceren niet bijgewerkt in KB '|
|> 70|Hoge betrouw baarheid: doorgaans een goed antwoord dat de query van de gebruiker volledig beantwoordt|"Ik heb mijn KB gepubliceerd, maar dit is niet bijgewerkt"|
|50-70|Gemiddeld betrouw baarheid-meestal een redelijk goed antwoord dat de hoofd intentie van de gebruikers query moet beantwoorden|"Moet ik mijn updates opslaan voordat ik mijn KB Publiceer?"|
|30 - 50|Lage betrouw baarheid: doorgaans een verwant antwoord dat de bedoeling van de gebruiker gedeeltelijk beantwoordt|' Wat doet het opslaan en trainen? '|
|< 30|Zeer lage betrouw baarheid: beantwoordt doorgaans niet de query van de gebruiker, maar heeft een aantal overeenkomende woorden of zinsdelen |"Waar kan ik synoniemen toevoegen aan mijn KB"|
|0|Geen overeenkomst, dus het antwoord wordt niet geretourneerd.|Wat is de kosten van de service?|

## <a name="choose-a-score-threshold"></a>Een score drempelwaarde kiezen
In de bovenstaande tabel worden de scores weer gegeven die op de meeste Kb's worden verwacht. Omdat elke KB echter anders is en verschillende soorten woorden, intenties en doel stellingen heeft, raden we u aan om de drempel waarde te testen en te kiezen die het beste geschikt is voor u. De drempel waarde is standaard ingesteld op 0, zodat alle mogelijke antwoorden worden geretourneerd. De aanbevolen drempel waarde die moet worden gebruikt voor de meeste Kb's, is **50**.

Houd bij het kiezen van de drempel waarde rekening met het evenwicht tussen nauw keurigheid en dekking en pas uw drempel aan op basis van uw vereisten.

- Als **nauw keurigheid** (of precisie) belang rijker is voor uw scenario, verhoogt u de drempel waarde. Op deze manier wordt elke keer dat u een antwoord retourneert, een veel meer VERTROUWENs situatie en veel waarschijnlijker zijn de antwoord gebruikers op zoek naar. In dit geval is het mogelijk dat u uiteindelijk nog meer vragen hebt. *Bijvoorbeeld:* als u de drempel waarde **70**maakt, kunt u enkele ambigue voor beelden missen ' wat is opslaan en trainen? '.

- Als **dekking** (of intrekken) belang rijker is en u zo veel mogelijk vragen wilt beantwoorden, zelfs als er slechts een gedeeltelijke relatie is met de vraag van de gebruiker, moet u de drempel waarde verlagen. Dit betekent dat er meer gevallen zijn waarin het antwoord niet de daad werkelijke query van de gebruiker beantwoordt, maar een ander, iets verwant antwoord geeft. *Bijvoorbeeld:* als u de drempel waarde **30**maakt, kunt u antwoorden geven op query's zoals ' waar kan ik mijn KB bewerken? '

> [!NOTE]
> Nieuwere versies van QnA Maker bevatten verbeteringen in de Score logica en kunnen van invloed zijn op de drempel waarde. Telkens wanneer u de service bijwerkt, moet u ervoor zorgen dat de drempel waarde wordt getest en zo nodig wordt verfijnd. U kunt [hier](https://www.qnamaker.ai/UserSettings)de QnA-Service versie controleren en bekijken hoe u de meest recente updates [hier](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)ophaalt.

## <a name="set-threshold"></a>Drempel waarde instellen 

Stel de drempel Score in als een eigenschap van de [JSON-hoofd tekst](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)van de GENERATEANSWER-API. Dit betekent dat u deze instelt voor elke aanroep van GenerateAnswer. 

Stel in het bot-Framework de score in als onderdeel van het object Options [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) met of [node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Betrouwbaarheids scores verbeteren
Als u de betrouwbaarheids Score van een bepaalde reactie op een gebruikers query wilt verbeteren, kunt u de gebruikers query toevoegen aan de Knowledge Base als een andere vraag over dat antwoord. U kunt ook hoofdletter gevoelige [woord wijzigingen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) gebruiken om synoniemen toe te voegen aan tref woorden in uw KB.


## <a name="similar-confidence-scores"></a>Vergelijk bare betrouwbaarheids scores
Wanneer meerdere antwoorden een vergelijk bare betrouwbaarheids score hebben, is het waarschijnlijk dat de query te algemeen is en daarom overeenkomt met een gelijke kans met meerdere antwoorden. Probeer uw QnAs beter te structureren zodat elke QnA-entiteit een DISTINCT-intentie heeft.


## <a name="confidence-score-differences"></a>Verschillen in Betrouwbaarheids Score
De betrouwbaarheids Score van een antwoord kan negligibly wijzigen tussen de test en de gepubliceerde versie van de Knowledge Base, zelfs als de inhoud hetzelfde is. Dit komt doordat de inhoud van de test en de gepubliceerde Knowledge Base zich bevinden in verschillende Azure Cognitive Search-indexen. Wanneer u een kennis database publiceert, wordt de vraag en antwoord inhoud van uw kennis basis verplaatst van de test index naar een productie-index in azure Search. Bekijk hoe de [publicatie](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) bewerking werkt.

Als u een Knowledge Base hebt in verschillende regio's, gebruikt elke regio een eigen Azure Cognitive Search-index. Omdat er verschillende indexen worden gebruikt, zijn de scores niet precies hetzelfde. 


## <a name="no-match-found"></a>Geen overeenkomst gevonden
Wanneer de rang schikking geen goede overeenkomst heeft gevonden, wordt de betrouwbaarheids Score van 0,0 of ' geen ' geretourneerd en wordt het standaard antwoord ' geen goede overeenkomst gevonden in de KB '. U kunt deze [standaard reactie](#change-default-answer) overschrijven in de bot of toepassings code die het eind punt aanroept. U kunt ook het overschrijvings antwoord in azure instellen en dit wijzigt de standaard waarde voor alle Knowledge bases die zijn geïmplementeerd in een bepaalde QnA Maker service.

## <a name="change-default-answer"></a>Standaard antwoord wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) en navigeer naar de resource groep die de QnA Maker-service vertegenwoordigt die u hebt gemaakt.

2. Klik om de **app service**te openen.

    ![In de Azure Portal Access app service for QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klik op **Toepassings instellingen** en bewerk het veld **DefaultAnswer** naar de gewenste standaard reactie. Klik op **Opslaan**.

    ![Selecteer toepassings instellingen en bewerk vervolgens DefaultAnswer voor QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uw app service opnieuw starten

    ![Nadat u de DefaultAnswer hebt gewijzigd, start u de QnA Maker appservice opnieuw](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Ondersteunde gegevens bronnen](./data-sources-supported.md)

