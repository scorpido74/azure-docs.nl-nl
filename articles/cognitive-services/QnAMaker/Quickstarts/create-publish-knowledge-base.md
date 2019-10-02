---
title: 'Quickstart: Knowledge Base-QnA Maker maken, trainen en publiceren'
titleSuffix: Azure Cognitive Services
description: U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. De QnA Maker Knowledge Base in dit voor beeld wordt gemaakt op basis van een eenvoudige webpagina met veelgestelde vragen over het beantwoorden van vragen over BitLocker-sleutel herstel.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: cafc1e2f3f195301a6c0f9485ebaa10111b08c7d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803053"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Quickstart: Uw QnA Maker-knowledge base maken, trainen en publiceren

U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. Dit artikel bevat een voor beeld van het maken van een QnA Maker Knowledge Base van een eenvoudige webpagina met veelgestelde vragen over het beantwoorden van vragen over BitLocker-sleutel herstel.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Vereiste

> [!div class="checklist"]
> * Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-qna-maker-knowledge-base"></a>Een QnA Maker-knowledge base maken

1. Meld u aan bij de [QnAMaker.ai](https://QnAMaker.ai) -Portal met uw Azure-referenties.

1. Selecteer op de QnA Maker-Portal **een Knowledge Base maken**.

   ![Scherm opname van QnA Maker Portal](../media/qna-maker-create-kb.png)

1. Selecteer op de pagina **Create** (Maken) in stap 1 de optie **Create a QnA service** (Een QnA-service maken). U wordt omgeleid naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) om een QnA Maker-service in te stellen in uw abonnement. Als er een time-out optreedt in de Azure Portal, selecteert u **Opnieuw proberen** op de site. Wanneer de verbinding is gemaakt, wordt uw Azure-dashboard weergegeven.

1. Nadat u een nieuwe QnA Maker-service hebt gemaakt in Azure, gaat u terug naar qnamaker.ai/create. Selecteer uw QnA Maker-service in de vervolg keuzelijsten in stap 2. Als u een nieuwe QnA Maker-service hebt gemaakt, moet u ervoor zorgen dat u de pagina vernieuwt.

   ![Scherm opname van het selecteren van een QnA Maker service Knowledge Base](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. In stap 3 moet u de Knowledge Base het voor **beeld QnA KB**noemen.

1. Als u inhoud wilt toevoegen aan uw Knowledge Base, selecteert u drie typen gegevens bronnen. Voeg in stap 4, onder **Populate your KB** (De KB vullen), de URL [Veelgestelde vragen over BitLocker-herstel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) toe in het vak **URL**.

   ![Scherm opname van het toevoegen van gegevens bronnen](../media/qnamaker-quickstart-kb/add-datasources.png)

1. Selecteer in stap 5 **Create your KB** (De KB maken).

1. Terwijl QnA Maker de Knowledge Base maakt, wordt er een pop-upvenster weer gegeven. Tijdens het extractieproces, dat enkele minuten duurt, wordt de HTML-pagina gelezen en worden de vragen en antwoorden geïdentificeerd.

1. Nadat QnA Maker de Knowledge Base hebt gemaakt, wordt de pagina **Knowledge Base** geopend. U kunt de inhoud van de Knowledge Base op deze pagina bewerken.

## <a name="edit-the-knowledge-base"></a>De Knowledge Base bewerken

1. Selecteer in de QnA Maker Portal, in de sectie **bewerken** , **QnA paar toevoegen** om een nieuwe rij aan de Knowledge Base toe te voegen. Typ onder **Question** (Vraag) **Hoi.** Typ onder **Answer** (Antwoord) **Hallo. Vraag me BitLocker-vragen.**

    ![Scherm opname van QnA Maker Portal](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Selecteer in de rechterbovenhoek **Save and train** (Opslaan en trainen) om de wijzigingen op te slaan en het QnA Maker-model te trainen. Bewerkingen worden alleen bewaard als ze worden opgeslagen.

## <a name="test-the-knowledge-base"></a>De Knowledge Base testen

1. Selecteer in de portal QnA Maker in de rechter bovenhoek de optie **testen** om te testen of de wijzigingen die u hebt aangebracht van kracht zijn geworden. Typ `hi there` in het vak en selecteer Enter. Als het goed is, ziet u nu het antwoord dat u hebt gemaakt.

1. Selecteer **Inspect** (Inspecteren) om het antwoord gedetailleerder te onderzoeken. Het venster test wordt gebruikt om uw wijzigingen in de Knowledge Base te testen voordat ze worden gepubliceerd.

    ![Scherm afbeelding van het test paneel](../media/qnamaker-quickstart-kb/inspect.png)

1. Selecteer **Test** (Testen) nogmaals om de pop-up **Test** te sluiten.

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren

Wanneer u een kennis database publiceert, wordt de vraag en antwoord inhoud van uw kennis basis verplaatst van de test index naar een productie-index in azure Search.

![Scherm afbeelding van het verplaatsen van de inhoud van uw Knowledge Base](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Selecteer in de QnA Maker Portal in het menu naast **bewerken**de optie **publiceren**. Selecteer vervolgens **Publish** (Publiceren) op de pagina om te bevestigen.

1. De QnA Maker-service is nu gepubliceerd. U kunt het eindpunt in uw toepassing of botcode gebruiken.

    ![Scherm opname van geslaagde publicatie](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Een bot maken

Na het publiceren kunt u een bot maken op de pagina **publiceren** : 

* U kunt snel een aantal botsingen maken, die allemaal verwijzen naar dezelfde Knowledge Base voor verschillende regio's of prijs plannen voor het individuele bots. 
* Als u slechts één bot wilt gebruiken voor de Knowledge Base, gebruikt u de **weer gave alles uw bots op de Azure Portal** koppeling om een lijst met uw huidige bots weer te geven. 

Wanneer u wijzigingen aanbrengt in de Knowledge Base en opnieuw publiceert, hoeft u geen verdere actie te ondernemen met de bot. Het is al geconfigureerd voor gebruik met de Knowledge Base en werkt met alle toekomstige wijzigingen in de kennis database. Telkens wanneer u een kennis database publiceert, worden alle botsen die met de Knowledge Base zijn verbonden, automatisch bijgewerkt.

1. Selecteer in de QnA Maker Portal op de pagina **publiceren** de optie **bot maken**. Deze knop wordt alleen weer gegeven nadat u de Knowledge Base hebt gepubliceerd.

    ![Scherm afbeelding van het maken van een bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Er wordt een nieuw browser tabblad geopend voor de Azure Portal, met de pagina voor het maken van de Azure Bot Service. De Azure bot-service configureren. Zie [een QnA-bot maken met Azure bot service v4](../tutorials/create-qna-bot.md)voor meer informatie over deze configuratie-instellingen.
    
    * Wijzig de volgende instellingen in de Azure Portal niet tijdens het maken van de bot. Ze zijn vooraf ingevuld voor uw bestaande Knowledge Base: 
        * QnA-verificatie sleutel
        * App service-plan en-locatie
        * Azure Storage
    * De bot en QnA Maker kunnen het web app service-abonnement delen, maar kunnen de web-app niet delen. Dit betekent dat de naam van de **app** moet verschillen van de app-naam die u hebt gebruikt bij het maken van de QnA Maker service. 


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase maken](../How-To/create-knowledge-base.md)
