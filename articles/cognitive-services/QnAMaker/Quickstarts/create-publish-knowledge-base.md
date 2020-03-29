---
title: 'Quickstart: Kennisbank maken, trainen en publiceren - QnA Maker'
description: U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. Dit artikel bevat een voorbeeld van het creëren van een QnA Maker kennisbank van een eenvoudige FAQ webpagina, om vragen QnA Maker te beantwoorden.
ms.topic: conceptual
ms.date: 02/08/2020
ms.openlocfilehash: b1f80006e2dc72b5b623f4c29c093c734dc1efea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220576"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Snelstart: maak, train en publiceer uw QnA Maker kennisbank

U kunt een QnA Maker-knowledge base (KB) maken van uw eigen inhoud, zoals veelgestelde vragen of producthandleidingen. Dit artikel bevat een voorbeeld van het creëren van een QnA Maker kennisbank van een eenvoudige FAQ webpagina, om vragen QnA Maker te beantwoorden.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
> * Een QnA [Maker-bron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) die is gemaakt in de Azure-portal. Onthoud uw Azure Active Directory ID, Abonnement, QnA-bronnaam die u hebt geselecteerd toen u de bron maakte.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Creëer je eerste QnA Maker kennisbank

1. Meld u aan bij de [QnAMaker.ai](https://QnAMaker.ai) portal met uw Azure-referenties.

1. Selecteer in de QnA Maker-portal de optie **Een kennisbank maken.**

1. Sla **stap 1** op de pagina **Maken** over als u al uw QnA Maker-bron hebt.

    Als u de resource nog niet hebt gemaakt, selecteert u **Een QnA-service maken**. U wordt omgeleid naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) om een QnA Maker-service in te stellen in uw abonnement. Onthoud uw Azure Active Directory ID, Abonnement, QnA-bronnaam die u hebt geselecteerd toen u de bron maakte.

    Wanneer u klaar bent met het maken van de bron in de Azure-portal, keert u terug naar de QnA Maker-portal, vernieuwt u de browserpagina en gaat u verder naar **stap 2.**

1. Selecteer in **stap 3**uw Active directory, abonnement, service (resource) en de taal voor alle kennisbanken die in de service zijn gemaakt.

   ![Schermafbeelding van het selecteren van een QnA Maker-servicekennisbank](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. In **stap 3,** de naam van uw kennisbank **Mijn Voorbeeld QnA KB**.

1. Configureer in **stap 4**de instellingen met de volgende tabel:

    |Instelling|Waarde|
    |--|--|
    |**Multi-turn extractie uit URL's, .pdf- of .docx-bestanden inschakelen.**|Geselecteerd|
    |**Standaardantwoordtekst**| `Quickstart - default answer not found.`|
    |**+ URL toevoegen**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Selecteer **Professional**|

1. Selecteer uw **KB maken**in **stap 5.**

    Het extractieproces duurt enkele ogenblikken om het document te lezen en vragen en antwoorden te identificeren.

    Nadat QnA Maker met succes de kennisbank heeft gemaakt, wordt de **Knowledge base** pagina geopend. U de inhoud van de kennisbank op deze pagina bewerken.

## <a name="add-a-new-question-and-answer-set"></a>Een nieuwe vraag- en antwoordset toevoegen

1. Selecteer in de QnA Maker-portal op de pagina **Bewerken** de optie **+ QnA-paar toevoegen** op de contextwerkbalk.
1. Voeg de volgende vraag toe:

    `How many Azure services are used by a knowledge base?`

1. Voeg het antwoord toe dat is opgemaakt met _markdown:_

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Voeg de vraag toe als tekst en het antwoord dat is opgemaakt met afwaardering.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Het markeringssymbool `*`wordt gebruikt voor opsommingstekens. Het `\n` wordt gebruikt voor een nieuwe lijn.

    Op de pagina **Bewerken** wordt de markering weergegeven. Wanneer u het **deelvenster Testen** later gebruikt, ziet u de markering correct weergegeven.

## <a name="save-and-train"></a>Opslaan en trainen

Selecteer rechtsboven **Opslaan en trainen** om uw bewerkingen op te slaan en Train QnA Maker . Bewerkingen worden alleen bewaard als ze worden opgeslagen.

## <a name="test-the-knowledge-base"></a>Test de kennisbank

1. Selecteer in de QnA Maker-portal rechtsboven De optie **Testen** om te testen of de wijzigingen die u hebt aangebracht van kracht zijn geworden.
1. Voer een voorbeeldvan gebruikersquery in het tekstvak in.

    `How many Azure services are used by a knowledge base?`

    ![ Voer een voorbeeldvan gebruikersquery in het tekstvak in. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Selecteer **Inspect** (Inspecteren) om het antwoord gedetailleerder te onderzoeken. Het testvenster wordt gebruikt om uw wijzigingen in de kennisbank te testen voordat u uw kennisbank publiceert.

1. Selecteer Opnieuw **testen** om het **testpaneel** te sluiten.

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren

Wanneer u een kennisbank publiceert, wordt de `test` inhoud van `prod` uw kennisbank verplaatst van de index naar een index in Azure-zoekopdrachten.

![Schermafbeelding van het verplaatsen van de inhoud van uw kennisbank](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Selecteer **Publiceren**in de qna-makerportal . Selecteer vervolgens **Publish** (Publiceren) op de pagina om te bevestigen.

    De QnA Maker-service is nu gepubliceerd. U kunt het eindpunt in uw toepassing of botcode gebruiken.

    ![Schermafbeelding van succesvol publiceren](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Een bot maken

Na publicatie u een bot maken op de **pagina Publiceren:**

* U snel verschillende bots maken, allemaal wijzend op dezelfde kennisbasis voor verschillende regio's of prijsplannen voor de afzonderlijke bots.
* Als u slechts één bot voor de kennisbank wilt, gebruikt u de **koppeling Al uw bots weergeven op de Azure-portalkoppeling** om een lijst met uw huidige bots weer te geven.

Wanneer u wijzigingen aanbrengt in de kennisbank en opnieuw publiceert, hoeft u geen verdere actie te ondernemen met de bot. Het is al geconfigureerd om te werken met de kennisbank, en werkt met alle toekomstige wijzigingen in de kennisbank. Elke keer dat u een kennisbank publiceert, worden alle bots die ermee verbonden zijn automatisch bijgewerkt.

1. Selecteer in de QnA Maker-portal op de pagina **Publiceren** de optie **Bot maken**. Deze knop wordt pas weergegeven nadat u de kennisbank hebt gepubliceerd.

    ![Schermafbeelding van het maken van een bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Er wordt een nieuw browsertabblad geopend voor de Azure-portal, met de pagina voor het maken van de Azure Bot-service. Configureer de Azure-botservice. De bot en QnA Maker kunnen het webapp-serviceplan delen, maar kunnen de web-app niet delen. Dit betekent dat de **naam van** de app voor de bot anders moet zijn dan de naam van de app voor de QnA Maker-service.

    * **Wel doen**
        * Verander bot handvat - als het niet uniek is.
        * Selecteer SDK-taal. Zodra de bot is gemaakt, u de code downloaden naar uw lokale ontwikkelingsomgeving en het ontwikkelingsproces voortzetten.
    * **Niet doen**
        * wijzig de volgende instellingen in de Azure-portal bij het maken van de bot. Ze zijn vooraf ingevuld voor uw bestaande kennisbank:
           * QnA Auth-sleutel
           * App-serviceplan en -locatie


1. Nadat de bot is gemaakt, opent u de **Bot-servicebron.**
1. Selecteer **onder Botbeheer**de optie **Testen in webchat**.
1. Voer bij de chatprompt van **Typ uw bericht**in:

    `Azure services?`

    De chatbot reageert met een antwoord van uw kennisbank.

    ![Voer een gebruikersquery in de webchat voor tests in.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Wat heb je bereikt?

Je hebt een nieuwe kennisbank gemaakt, een publieke URL toegevoegd aan de kennisbank, je eigen QnA-set toegevoegd, de kennisbank getraind, getest en gepubliceerd.

Na het publiceren van de kennisbank hebt u een bot gemaakt en de bot getest.

Dit alles werd bereikt in een paar minuten zonder enige code te schrijven of de inhoud schoon te maken.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet doorgaat naar de volgende quickstart, verwijdert u de QnA Maker- en Bot-frameworkbronnen in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Vragen toevoegen met metagegevens](add-question-metadata-portal.md)

Voor meer informatie:

* [Markdown-indeling in antwoorden](../reference-markdown-format.md)
* QnA [Maker-gegevensbronnen](../concepts/knowledge-base.md).


