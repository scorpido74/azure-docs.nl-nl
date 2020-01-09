---
title: 'Zelf studie: QnA bot-Azure Bot Service-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Deze zelf studie laat zien hoe u een QnA-chat-bot maakt op de pagina publiceren voor een bestaande kennis database. Deze bot maakt gebruik van de bot Framework SDK v4. U hoeft geen code te schrijven om de bot samen te stellen, maar alle code wordt voor u gegeven.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: diberry
ms.openlocfilehash: 0ddce3e4112dfb14309878927493abb3cb6b451a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447369"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Zelf studie: een QnA-bot maken met Azure Bot Service v4

Maak een QnA-chat-bot op de pagina **publiceren** voor een bestaande kennis database. Deze bot maakt gebruik van de bot Framework SDK v4. U hoeft geen code te schrijven om de bot samen te stellen, maar alle code wordt voor u gegeven.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Een Azure Bot Service maken op basis van een bestaande Knowledge Base
> * Chatten met de bot om te controleren of dat de code werkt

## <a name="prerequisites"></a>Vereisten

U moet een gepubliceerde knowledge base voor deze zelfstudie. Als u er nog geen hebt, volgt u de stappen in de zelf studie [maken en beantwoorden van KB](create-publish-query-in-portal.md) om een QnA Maker Knowledge Base te maken met vragen en antwoorden.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Een QnA-bot maken

Maak een bot als een client toepassing voor de Knowledge Base.

1. Ga in de QnA Maker Portal naar de pagina **publiceren** en publiceer uw kennis database. Selecteer **bot maken**.

    ![Ga in de QnA Maker Portal naar de pagina publiceren en publiceer uw kennis database. Selecteer Bot maken.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Azure Portal wordt geopend met de configuratie voor het maken van de bot.

1.  Geef de instellingen voor het maken van de bot op:

    |Instelling|Waarde|Doel|
    |--|--|--|
    |Bot-ingang|`qna-maker-central-us-bot`|Dit is de naam van de Azure-resource voor de bot.|
    |Abonnement|Zie doel.|Selecteer hetzelfde abonnement dat u hebt gebruikt om de QnA Maker resources te maken.|
    |Resourcegroep|`docs`|De resource groep die wordt gebruikt voor alle bot-gerelateerde Azure-resources.|
    |Locatie|`west us`|De Azure-resource locatie van de bot.|
    |Prijscategorie|`F0`|De gratis laag voor de Azure bot-service.|
    |Naam van app|`qna-maker-central-us-bot-app`|Dit is een web-app ter ondersteuning van uw bot. Dit mag niet dezelfde app-naam zijn als uw QnA Maker-service al gebruikt. Het delen van de Web-App van QnA Maker met een andere resource wordt niet ondersteund.|
    |SDK-taal|C#|Dit is de onderliggende programmeer taal die wordt gebruikt door de bot Framework SDK. Uw keuzes zijn [C#](https://github.com/Microsoft/botbuilder-dotnet) of [node. js](https://github.com/Microsoft/botbuilder-js).|
    |QnA-verificatie sleutel|**Niet wijzigen**|Deze waarde wordt voor u ingevuld.|
    |App Service-plan/-locatie|**Niet wijzigen**|Voor deze zelf studie is de locatie niet belang rijk.|
    |Application Insights|**Niet wijzigen**|Logboek registratie wordt verzonden naar Application Insights.|
    |Micro soft App-ID|**Niet wijzigen**|Active Directory-gebruiker en-wacht woord zijn vereist.|

    > [!div class="mx-imgBorder"]
    > ![de bot voor de Knowledge Base maken met deze instellingen.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Wacht een paar minuten totdat het proces voor het maken van de bot een geslaagde melding meldt.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Met de Bot chatten

1. Open de nieuwe botresource vanuit de melding in Azure Portal.

    ![Open de nieuwe botresource vanuit de melding in Azure Portal.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Selecteer in **bot Management** **testen in Web Chat** en voer: `How large can my KB be?`in. De bot reageert met:


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![De nieuwe bot van de Knowledge Base testen.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Zie [QnA Maker gebruiken om vragen te beantwoorden](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs) voor meer informatie over Azure-bots

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de bot van deze zelfstudie, verwijdert u de bot in Azure portal.

Als u een nieuwe resource groep voor de resources van de bot hebt gemaakt, verwijdert u de resource groep.

Als u geen nieuwe resource groep hebt gemaakt, moet u de resources zoeken die aan de bot zijn gekoppeld. De eenvoudigste manier is om te zoeken op de naam van de bot-en bot-app. De bot-resources zijn onder andere:

* De App Service-plan
* De Search-service
* De Cognitive service
* De appservice
* (Optioneel) kan het ook de application insights-service en de opslag voor de application insights-gegevens


## <a name="related-to-qna-maker-bots"></a>Gerelateerd aan QnA Maker bots

* De QnA Maker Help-bot die in de QnA Maker portal wordt gebruikt, is beschikbaar als een bot-voor [beeld](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support).
    het pictogram ![QnA Maker Help-bot is een rode robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* Het gebruik van de [gezondheids zorg](https://docs.microsoft.com/HealthBot/qna_model_howto) QnA Maker als een van de [taal modellen](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Concept: knowledge base](../concepts/knowledge-base.md)

## <a name="see-also"></a>Zie ook

- [Beheren van uw knowledge base](https://qnamaker.ai)
- [Uw bot in verschillende kanalen](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
