---
title: Betrouwbaarheidsscore - QnA Maker
titleSuffix: Azure Cognitive Services
description: Er moet een kennisbank worden gepubliceerd. Eenmaal gepubliceerd, wordt de knowledge base opgevraagd op het eindpunt voor runtime voorspelling met behulp van de generateAnswer API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d901a803311805825c22503af6098e805a67e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843449"
---
# <a name="the-confidence-score-of-an-answer"></a>De betrouwbaarheidsscore van een antwoord
Wanneer een gebruikersquery wordt gekoppeld aan een kennisbank, retourneert QnA Maker relevante antwoorden, samen met een vertrouwensscore. Deze score geeft het vertrouwen aan dat het antwoord de juiste match is voor de opgegeven gebruikersquery.

De betrouwbaarheidsscore is een getal tussen 0 en 100. Een score van 100 is waarschijnlijk een exacte match, terwijl een score van 0 betekent, dat er geen overeenkomend antwoord werd gevonden. Hoe hoger de score, hoe groter het vertrouwen in het antwoord. Voor een bepaalde query kunnen er meerdere antwoorden worden geretourneerd. In dat geval worden de antwoorden geretourneerd in volgorde van afnemende betrouwbaarheidsscore.

In het onderstaande voorbeeld ziet u één QnA-entiteit, met 2 vragen.


![Voorbeeld van QnA-paar](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Voor het bovenstaande voorbeeld u scores verwachten, zoals het onderstaande voorbeeldscorebereik, voor verschillende typen gebruikersquery's:


![Ranker score bereik](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


In de volgende tabel wordt het typische vertrouwen aangegeven dat is gekoppeld aan een bepaalde score.

|Scorewaarde|Scorebetekenis|Voorbeeldquery|
|--|--|--|
|90 - 100|Een bijna exacte overeenkomst van gebruikersquery en een KB-vraag|"Mijn wijzigingen worden niet bijgewerkt in KB na publicatie"|
|> 70|Veel vertrouwen - meestal een goed antwoord dat volledig beantwoordt aan de vraag van de gebruiker|"Ik heb mijn KB gepubliceerd, maar het is niet bijgewerkt"|
|50 - 70|Gemiddeld vertrouwen - meestal een vrij goed antwoord dat de belangrijkste bedoeling van de gebruikersquery moet beantwoorden|"Moet ik mijn updates opslaan voordat ik mijn KB publiceer?"|
|30 - 50|Weinig vertrouwen - meestal een gerelateerd antwoord, dat gedeeltelijk beantwoordt aan de intentie van de gebruiker|"Wat doet de save en trein doen?"|
|< 30|Zeer weinig vertrouwen - meestal niet beantwoorden van de vraag van de gebruiker, maar heeft een aantal overeenkomende woorden of zinnen |" Waar kan ik synoniemen toevoegen aan mijn KB"|
|0|Geen overeenkomst, dus het antwoord wordt niet geretourneerd.|"Hoeveel kost de service"|

## <a name="choose-a-score-threshold"></a>Kies een scoredrempel
De bovenstaande tabel toont de scores die worden verwacht op de meeste KBs. Echter, omdat elke KB anders is, en heeft verschillende soorten woorden, bedoelingen en doelen- raden we u testen en kies de drempel die het beste werkt voor jou. Standaard is de drempelwaarde ingesteld op 0, zodat alle mogelijke antwoorden worden geretourneerd. De aanbevolen drempel die voor de meeste KB's zou moeten werken, is **50**.

Houd bij het kiezen van uw drempel rekening met de balans tussen nauwkeurigheid en dekking en pas uw drempelwaarde aan op basis van uw vereisten.

- Als **nauwkeurigheid** (of precisie) belangrijker is voor uw scenario, verhoog dan uw drempelwaarde. Op deze manier, elke keer dat u een antwoord terug, zal het een veel meer vertrouwen geval, en veel meer kans om het antwoord gebruikers op zoek zijn naar. In dit geval u uiteindelijk meer vragen onbeantwoord laten. *Bijvoorbeeld:* als u de drempel **70**maakt, mist u mogelijk enkele dubbelzinnige voorbeelden zoals "wat is opslaan en trainen?".

- Als **dekking** (of terugroepen) belangrijker is en u zoveel mogelijk vragen wilt beantwoorden, zelfs als er slechts een gedeeltelijke relatie is met de vraag van de gebruiker, dan verlaagt u de drempel. Dit betekent dat er meer gevallen kunnen zijn waarin het antwoord geen antwoord geeft op de werkelijke vraag van de gebruiker, maar een ander enigszins gerelateerd antwoord geeft. *Bijvoorbeeld:* als u de drempel **30**maakt, u antwoorden geven op vragen als 'Waar kan ik mijn KB bewerken?'

> [!NOTE]
> Nieuwere versies van QnA Maker bevatten verbeteringen in de scorelogica en kunnen van invloed zijn op uw drempelwaarde. Elke keer dat u de service bijwerkt, moet u de drempel testen en zo nodig aanpassen. U uw QnA Service-versie [hier](https://www.qnamaker.ai/UserSettings)controleren en zien hoe u hier de nieuwste updates [kunt](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)ontvangen.

## <a name="set-threshold"></a>Drempelwaarde instellen

Stel de drempelscore in als eigenschap van de [Json-instantie GenerateAnswer API.](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) Dit betekent dat u deze voor elke oproep instelt op GenerateAnswer.

Stel in het botkader de score in als onderdeel van het object Opties met [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) of [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Verbeter de vertrouwensscores
Als u de vertrouwensscore van een bepaald antwoord op een gebruikersquery wilt verbeteren, u de gebruikersquery aan de kennisbank toevoegen als een alternatieve vraag over dat antwoord. U ook hoofdlettergevoelige [woordwijzigingen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) gebruiken om synoniemen toe te voegen aan zoekwoorden in uw KB.


## <a name="similar-confidence-scores"></a>Vergelijkbare vertrouwensscores
Wanneer meerdere antwoorden een vergelijkbare betrouwbaarheidsscore hebben, is het waarschijnlijk dat de query te algemeen was en daarom gepaard ging met dezelfde waarschijnlijkheid met meerdere antwoorden. Probeer uw QnA's beter te structureren, zodat elke QnA-entiteit een duidelijke intentie heeft.


## <a name="confidence-score-differences-between-test-and-production"></a>Vertrouwensscoreverschillen tussen test en productie
De betrouwbaarheidsscore van een antwoord kan verwaarloosbaar veranderen tussen de test en de gepubliceerde versie van de kennisbank, zelfs als de inhoud hetzelfde is. Dit komt omdat de inhoud van de test en de gepubliceerde kennisbank zich bevinden in verschillende Azure Cognitive Search-indexen.

De testindex bevat alle QnA-paren van uw kennisbanken. Bij het opvragen van de testindex is de query van toepassing op de volledige index, waarna de resultaten beperkt zijn tot de partitie voor die specifieke kennisbasis. Als de testqueryresultaten een negatieve invloed hebben op uw vermogen om de kennisbank te valideren, u het als:
* uw kennisbank te organiseren met een van de volgende opties:
    * 1 resource beperkt tot 1 KB: beperk uw enkele QnA-bron (en de resulterende Azure Cognitive Search-testindex) tot één kennisbank.
    * 2 resources - 1 voor test, 1 voor productie: hebben twee QnA Maker middelen, met behulp van een voor het testen (met zijn eigen test-en productie-indexen) en een voor het product (ook met een eigen test en productie-indexen)
* en gebruik altijd dezelfde parameters, zoals **[bovenaan](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** bij het opvragen van zowel uw test- als productiekennisbestand

Wanneer u een kennisbank publiceert, wordt de vraag- en antwoordinhoud van uw kennisbank verplaatst van de testindex naar een productie-index in Azure-zoekopdrachten. Bekijk hoe de [publicatiebewerking](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) werkt.

Als u een kennisbank in verschillende regio's hebt, gebruikt elke regio zijn eigen Azure Cognitive Search-index. Omdat verschillende indexen worden gebruikt, zullen de scores niet precies hetzelfde zijn.


## <a name="no-match-found"></a>Geen overeenkomst gevonden
Wanneer er geen goede overeenkomst wordt gevonden door de ranker, wordt de betrouwbaarheidsscore van 0,0 of "Geen" geretourneerd en is de standaardrespons "Geen goede match gevonden in de KB". U dit [standaardantwoord](../How-To/metadata-generateanswer-usage.md) overschrijven in de bot- of toepassingscode die het eindpunt aanroept. U ook de overschrijfreactie in Azure instellen en dit verandert de standaardinstelling voor alle kennisbases die zijn geïmplementeerd in een bepaalde QnA Maker-service.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Aanbevolen procedures](./best-practices.md)

