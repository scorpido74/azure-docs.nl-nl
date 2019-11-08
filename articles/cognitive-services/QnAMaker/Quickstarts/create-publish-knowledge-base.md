---
title: 'Snelstartgids: Knowledge Base-QnA Maker maken, trainen en publiceren'
titleSuffix: Azure Cognitive Services
description: U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. De QnA Maker Knowledge Base in dit voor beeld wordt gemaakt op basis van een eenvoudige webpagina met veelgestelde vragen over het beantwoorden van vragen over BitLocker-sleutel herstel.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 8ce35156cd772487c4787f2f5fb99feb1225826f
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794193"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Snelstartgids: uw QnA Maker Knowledge Base maken, trainen en publiceren

U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. Dit artikel bevat een voor beeld van het maken van een QnA Maker Knowledge Base van een eenvoudige webpagina met veelgestelde vragen over het beantwoorden van vragen over BitLocker-sleutel herstel.

Neem een Chit-Chat persoonlijkheid op om uw kennis beter te maken met uw gebruikers.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Vereiste

> [!div class="checklist"]
> * Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Een nieuwe QnA Maker Knowledge Base maken

1. Meld u aan bij de [QnAMaker.ai](https://QnAMaker.ai) -Portal met uw Azure-referenties.

1. Selecteer op de QnA Maker-Portal **een Knowledge Base maken**.

1. Selecteer op de pagina **maken** de optie **een QnA-service maken**. U wordt omgeleid naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) om een QnA Maker-service in te stellen in uw abonnement. 

1. Selecteer in de QnA Maker Portal uw QnA Maker-service in de vervolg keuzelijst. Als u een nieuwe QnA Maker-service hebt gemaakt, moet u ervoor zorgen dat u de pagina vernieuwt.

   ![Scherm opname van het selecteren van een QnA Maker service Knowledge Base](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Noem uw kennis basis van **mijn voor beeld QnA KB**.

1. Een voor beeld van een Word-document als URL toevoegen: 

    `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`

1. Selecteer `+ Add URL`.

1. Voeg  **_professionele_ Chit-Chat** toe aan uw KB. 

1. Selecteer **uw KB maken**.

    Het uitpakken van het proces duurt een paar minuten om het document te lezen en vragen en antwoorden te identificeren.

    Nadat QnA Maker de Knowledge Base hebt gemaakt, wordt de pagina **Knowledge Base** geopend. U kunt de inhoud van de Knowledge Base op deze pagina bewerken.

## <a name="add-a-new-question-and-answer-set"></a>Een nieuwe vraag en antwoord reeks toevoegen

1. Selecteer in de QnA Maker-Portal op de pagina **bewerken** de optie **QnA paar toevoegen**.
1. Voeg de volgende vraag toe: 

    `How many Azure services are used by a knowledge base?`

1. Het antwoord toevoegen dat is opgemaakt met de _prijs verlaging_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Voeg de vraag toe als tekst en het antwoord dat is opgemaakt met de prijs verlaging.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Het symbool voor de prijs verlaging, `*`, wordt gebruikt voor punten van een opsommings teken. De `\n` wordt gebruikt voor een nieuwe regel.  

    Op de pagina **bewerken** wordt de prijs verlaging weer gegeven. Wanneer u het deel venster **testen** later gebruikt, ziet u dat de prijs correct wordt weer gegeven. 

## <a name="save-and-train"></a>Opslaan en trainen

Selecteer in de rechterbovenhoek **Save and train** (Opslaan en trainen) om de wijzigingen op te slaan en het QnA Maker-model te trainen. Bewerkingen worden alleen bewaard als ze worden opgeslagen.

## <a name="test-the-knowledge-base"></a>De Knowledge Base testen

1. Selecteer in de portal QnA Maker in de rechter bovenhoek de optie **testen** om te testen of de wijzigingen die u hebt aangebracht van kracht zijn geworden. 
1. Voer een voor beeld van een gebruikers query in het tekstvak in. 

    `How many Azure services are used by a knowledge base?`  

    ![ Voer een voor beeld van een gebruikers query in het tekstvak in. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Selecteer **Inspect** (Inspecteren) om het antwoord gedetailleerder te onderzoeken. Het venster test wordt gebruikt om uw wijzigingen in de Knowledge Base te testen voordat u uw kennis database publiceert.

1. Selecteer **testen** opnieuw om het **test** paneel te sluiten.

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren

Wanneer u een kennis database publiceert, wordt de inhoud van uw kennis basis verplaatst van de `test` index naar een `prod` index in azure Search.

![Scherm afbeelding van het verplaatsen van de inhoud van uw Knowledge Base](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Selecteer in de QnA Maker Portal de optie **publiceren**. Selecteer vervolgens **Publish** (Publiceren) op de pagina om te bevestigen.

    De QnA Maker-service is nu gepubliceerd. U kunt het eindpunt in uw toepassing of botcode gebruiken.

    ![Scherm opname van geslaagde publicatie](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Een bot maken

Na het publiceren kunt u een bot maken op de pagina **publiceren** : 

* U kunt snel een aantal botsingen maken, die allemaal verwijzen naar dezelfde Knowledge Base voor verschillende regio's of prijs plannen voor het individuele bots. 
* Als u slechts één bot wilt gebruiken voor de Knowledge Base, gebruikt u de **weer gave alles uw bots op de Azure Portal** koppeling om een lijst met uw huidige bots weer te geven. 

Wanneer u wijzigingen aanbrengt in de Knowledge Base en opnieuw publiceert, hoeft u geen verdere actie te ondernemen met de bot. Het is al geconfigureerd voor gebruik met de Knowledge Base en werkt met alle toekomstige wijzigingen in de kennis database. Telkens wanneer u een kennis database publiceert, worden alle botsen die met de Knowledge Base zijn verbonden, automatisch bijgewerkt.

1. Selecteer in de QnA Maker Portal op de pagina **publiceren** de optie **bot maken**. Deze knop wordt alleen weer gegeven nadat u de Knowledge Base hebt gepubliceerd.

    ![Scherm afbeelding van het maken van een bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Er wordt een nieuw browser tabblad geopend voor de Azure Portal, met de pagina voor het maken van de Azure Bot Service. De Azure bot-service configureren. 
    
    * Wijzig de volgende instellingen in de Azure Portal niet tijdens het maken van de bot. Ze zijn vooraf ingevuld voor uw bestaande Knowledge Base: 
        * QnA-verificatie sleutel
        * App service-plan en-locatie
    * De bot en QnA Maker kunnen het web app service-abonnement delen, maar kunnen de web-app niet delen. Dit betekent dat de **app-naam** voor de bot afwijkt van de app-naam voor de QnA Maker-service. 

1. Nadat de bot is gemaakt, opent u de **bot service** -resource. 
1. Selecteer in het gedeelte **bot Management**de optie **testen in Web Chat**.
1. Typ het volgende bij de chat prompt van **uw bericht**:

    `Azure services?`

    De chat-bot reageert met een antwoord van uw Knowledge Base. 

    ![Voer een gebruikers query in op de Web Chat testen.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>Resources opschonen

Ruim de QnA Maker-en bot-Framework resources op in de Azure Portal. 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie:

* [Indeling voor prijs verlaging in antwoorden](../concepts/data-sources-supported.md)
* [Uw prijs verlaging testen](../concepts/data-sources-supported.md#testing-your-markdown)
* QnA Maker [gegevens bronnen](../Concepts/data-sources-supported.md). 
* [Bot-bron configuratie-instellingen](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Vragen toevoegen met meta gegevens](add-question-metadata-portal.md)

