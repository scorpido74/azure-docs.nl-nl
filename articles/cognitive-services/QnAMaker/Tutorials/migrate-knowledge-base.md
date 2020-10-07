---
title: Knowledge bases migreren-QnA Maker
description: Voor het migreren van een Knowledge Base moet u vanuit één kennis database exporteren en vervolgens importeren in een andere.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 9ba5f1d3d240867a8f6da52b9666c500c5b6446e
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777380"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Een Knowledge Base migreren met behulp van exporteren/importeren

Migratie is het proces van het maken van een nieuwe Knowledge Base op basis van een bestaande Knowledge Base. Dit kan verschillende oorzaken hebben:

* back-up-en herstel proces
* CI/CD-pijplijn
* regio's verplaatsen

Voor het migreren van een Knowledge Base moet u vanuit een bestaande Knowledge Base exporteren en vervolgens importeren in een andere.

## <a name="prerequisites"></a>Vereisten

* Maak een [gratis account](https://azure.microsoft.com/free/cognitive-services/) voordat u begint.
* Een nieuwe QnA Maker- [service](../How-To/set-up-qnamaker-service-azure.md) instellen

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Een Knowledge Base migreren uit QnA Maker
1. Meld u aan bij [QnA Maker Portal](https://qnamaker.ai).
1. Selecteer de Knowledge Base van de oorsprong die u wilt migreren.

1. Selecteer op de pagina **instellingen** de optie **Knowledge Base exporteren** om een TSV-bestand te downloaden dat de inhoud van de Knowledge Base van uw oorsprong bevat: vragen, antwoorden, meta gegevens, opvolgings prompts en de namen van de gegevens bronnen waaruit ze zijn geëxtraheerd.

1. Selecteer **een Knowledge Base maken** in het bovenste menu en maak vervolgens een _lege_ Knowledge Base. Het is leeg omdat u geen Url's of bestanden gaat toevoegen wanneer u deze maakt. Deze worden toegevoegd tijdens de stap importeren na het maken.

    De Knowledge Base configureren. Stel alleen de nieuwe naam van de Knowledge Base in. Dubbele namen worden ondersteund en speciale tekens worden ook ondersteund.

    Selecteer niets uit stap 4 omdat deze waarden worden overschreven wanneer u het bestand importeert.

1. Selecteer in stap 5 **maken**.

1. Open in deze nieuwe Knowledge Base het tabblad **instellingen** en selecteer **Knowledge Base importeren**. Hiermee worden de vragen, antwoorden, meta gegevens, opvolgings aanwijzingen geïmporteerd en blijven de namen van de gegevens bronnen waarvan ze zijn geëxtraheerd.

   > [!div class="mx-imgBorder"]
   > [![Knowledge Base importeren](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Test** de nieuwe Knowledge Base met behulp van het test paneel. Meer informatie over het [testen van uw Knowledge Base](../How-To/test-knowledge-base.md).

1. **Publiceer** de Knowledge Base en maak een chat-bot. Meer informatie over het [publiceren van uw Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Een kennis database programmatisch migreren vanuit QnA Maker

Het migratie proces is programmatisch beschikbaar met behulp van de volgende REST-Api's:

**Exporteren**

* [Knowledge Base-API downloaden](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importeren**

* [API vervangen (opnieuw laden met dezelfde Knowledge Base-ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [API maken (laden met nieuwe Knowledge Base-ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Chat-logboeken en-wijzigingen
Niet-hoofdletter gevoelige wijzigingen (synoniemen) worden niet automatisch geïmporteerd. Gebruik de [v4-api's](https://go.microsoft.com/fwlink/?linkid=2092179) om de wijzigingen in de nieuwe Knowledge Base te verplaatsen.

Het is niet mogelijk om chat-logboeken te migreren omdat de nieuwe Knowledge Base gebruikmaakt van Application Insights voor het opslaan van chat-Logboeken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Knowledge Base bewerken](../How-To/edit-knowledge-base.md)
