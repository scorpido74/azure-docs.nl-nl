---
title: Betrouwbaarheids Score-QnA Maker
titleSuffix: Azure Cognitive Services
description: De betrouwbaarheidsscore geeft aan dat het vertrouwen dat het antwoord de juiste overeenkomst voor de opgegeven gebruiker-query is.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 48b38e88ea6d0d99a0734aa36716020ced6e3d78
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193618"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Betrouwbaarheidsscore van een kennisdatabase QnA Maker
Wanneer de aanvraag voor een gebruiker wordt vergeleken met een knowledge base, retourneert de QnA Maker relevante antwoorden, samen met een betrouwbaarheidsscore. Deze score geeft aan dat het vertrouwen dat het antwoord de juiste overeenkomst voor de opgegeven gebruiker-query is. 

De betrouwbaarheidsscore is een getal tussen 0 en 100 liggen. Een score van 100, is waarschijnlijk een exacte overeenkomst, terwijl een score van 0 betekent dat dat er geen overeenkomende antwoord is gevonden. Hoe hoger de score - hoe groter het vertrouwen in het antwoord. Voor een bepaalde query, kunnen er meerdere antwoorden geretourneerd. In dat geval worden de antwoorden geretourneerd in volgorde van de betrouwbaarheidsscore verlagen.

In het onderstaande voorbeeld ziet u één QnA entiteit, met 2 vragen. 


![Voorbeeld van een combinatie van QnA](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

U kunt voor het bovenstaande voorbeeld - scores, zoals het bereik van de voorbeeld-score onderstaande-voor verschillende typen query's van gebruikers verwachten:


![Kerntechnologie score bereik](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


De volgende tabel staan de typische vertrouwen die voor een bepaald scorebereik zijn gekoppeld.

|Score-waarde|Betekenis van score|Voorbeeld van een Query|
|--|--|--|
|90 - 100|Een bijna exact overeenkomen met de gebruikersquery en een vraag KB|' Mijn wijzigingen worden na het publiceren niet bijgewerkt in KB '|
|> 70|Hoge betrouwbaarheid - doorgaans een goed antwoord die volledig vindt u antwoorden op query van de gebruiker|'Ik mijn KB gepubliceerd, maar deze niet wordt bijgewerkt'|
|50 - 70|Gemiddeld vertrouwen - doorgaans een redelijk goed antwoord dat de belangrijkste doel van de gebruikersquery moet beantwoorden|"Moet ik mijn updates opslaan voordat ik mijn KB publiceren?"|
|30 - 50|Lage vertrouwen - doorgaans een gerelateerde antwoord, die gedeeltelijk vindt u antwoorden op de intentie van de gebruiker|"Wat doet de opslaan en train?"|
|< 30|Zeer lage vertrouwen - doorgaans query van de gebruiker niet reageert, maar heeft een overeenkomende woorden of zinsdelen |"Waar kan ik synoniemen toevoegen aan mijn KB"|
|0|Er is geen overeenkomst, zodat het antwoord niet wordt geretourneerd.|"Wat de service kost"|

## <a name="choose-a-score-threshold"></a>Kies een score drempelwaarde
De bovenstaande tabel bevat de scores dat wordt verwacht dat op de meeste kB's. Omdat elke KB echter anders is en verschillende soorten woorden, intenties en doel stellingen heeft, raden we u aan om de drempel waarde te testen en te kiezen die het beste geschikt is voor u. De drempel waarde is standaard ingesteld op 0, zodat alle mogelijke antwoorden worden geretourneerd. De aanbevolen drempel waarde die moet worden gebruikt voor de meeste Kb's, is **50**.

Bij het kiezen van de drempelwaarde, houd rekening met de balans tussen nauwkeurigheid en dekking en de drempelwaarde op basis van uw behoeften aanpassen.

- Als **nauwkeurigheid** (of precisie) is belangrijker voor uw scenario en vervolgens uw drempelwaarde verhogen. Op deze manier telkens wanneer u een antwoord retourneren, worden een nog veel meer CONFIDENT case en nog veel meer waarschijnlijk dat de antwoord-gebruikers op zoek bent. In dit geval als u uiteindelijk meer vragen met openstaande verlaten. *Bijvoorbeeld:* als u de drempelwaarde **70**, mogelijk ontbreken bepaalde niet-eenduidige voorbeelden likes 'wat wordt opgeslagen en trainen?'.

- Als **dekking** (of intrekken) is meer belangrijke - en u wilt beantwoorden als veel vragen mogelijk, zelfs als er slechts een gedeeltelijke relatie van de gebruiker vragen-vervolgens VERLAAGT de drempel. Dit betekent dat er mogelijk meer situaties waarbij het antwoord niet de werkelijke query van de gebruiker beantwoordt, maar sommige andere enigszins gerelateerde antwoord krijgt. *Bijvoorbeeld:* als u de drempel waarde **30**maakt, kunt u antwoorden geven op query's zoals ' waar kan ik mijn KB bewerken? '

> [!NOTE]
> Nieuwere versies van QnA Maker zijn verbeteringen aangebracht aan scoring logica en invloed kunnen zijn op de drempelwaarde. Elk gewenst moment bijwerken van de service, zorg ervoor dat voor het testen en aanpassen van de drempelwaarde, indien nodig. U kunt uw versie van de QnA-Service controleren [hier](https://www.qnamaker.ai/UserSettings), en zien hoe u de meest recente updates [hier](../How-To/set-up-qnamaker-service-azure.md#get-latest-runtime-updates).

## <a name="set-threshold"></a>Drempel waarde instellen 

Stel de drempel Score in als een eigenschap van de [JSON-hoofd tekst](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)van de GENERATEANSWER-API. Dit betekent dat u deze instelt voor elke aanroep van GenerateAnswer. 

Stel in het bot-Framework de score in als onderdeel van het object Options [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) met of [node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Vertrouwen van de scores verbeteren
Ter verbetering van de betrouwbaarheidsscore van een bepaald antwoord op de gebruikersquery van een, kunt u de gebruikersquery toevoegen aan de knowledge base als een alternatieve vraag op dat-antwoord. U kunt ook hoofdletter gevoelige [woord wijzigingen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) gebruiken om synoniemen toe te voegen aan tref woorden in uw KB.


## <a name="similar-confidence-scores"></a>Vergelijkbare vertrouwen scores
Wanneer u meerdere antwoorden op een vergelijkbare betrouwbaarheidsscore hebt, is het waarschijnlijk dat de query te algemeen en daarom overeenkomende kans met meerdere antwoorden was. Probeer uw vragen en antwoorden supereenvoudig beter structuur zodat elke entiteit QnA een afzonderlijke intentie heeft.


## <a name="confidence-score-differences"></a>Vertrouwen van de score verschillen
De betrouwbaarheidsscore van een antwoord kan negligibly wisselen tussen de test- en gepubliceerde versie van de knowledge base, zelfs als de inhoud hetzelfde is. Dit komt doordat de inhoud van de test en de gepubliceerde knowledge base bevinden zich in verschillende Azure Search-index. Wanneer u een kennis database publiceert, wordt de vraag en antwoord inhoud van uw kennis basis verplaatst van de test index naar een productie-index in azure Search. Bekijk hoe de [publicatie](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) bewerking werkt.

Als u een Knowledge Base hebt in verschillende regio's, gebruikt elke regio een eigen Azure Search index. Omdat er verschillende indexen worden gebruikt, zijn de scores niet precies hetzelfde. 


## <a name="no-match-found"></a>Er is geen overeenkomst gevonden
Wanneer er geen goede overeenkomst is gevonden door de kerntechnologie, wordt de betrouwbaarheidsscore van 0,0 of 'Geen' wordt geretourneerd en wordt het standaardantwoord is 'Geen goede treffer gevonden in de KB'. U kunt deze [standaard reactie](#change-default-answer) overschrijven in de bot of toepassings code die het eind punt aanroept. U kunt ook u kunt ook het antwoord voor overschrijven instellen in Azure en Hiermee wordt de standaardwaarde voor alle knowledge bases geïmplementeerd in een bepaalde QnA Maker-service gewijzigd.

## <a name="change-default-answer"></a>Standaard antwoord wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) en navigeer naar de resourcegroep die de QnA Maker-service die u hebt gemaakt.

2. Klik hier om de **App Service**.

    ![In de Azure-portal toegang tot appservice voor QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klik op **toepassingsinstellingen** en bewerk de **DefaultAnswer** veld naar de gewenste Standaardantwoord. Klik op **Opslaan**.

    ![Selecteer de toepassingsinstellingen en bewerk vervolgens DefaultAnswer voor QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uw App-service opnieuw starten

    ![Nadat u de DefaultAnswer hebt gewijzigd, start opnieuw op de appservice QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Gegevensbronnen die worden ondersteund](./data-sources-supported.md)

