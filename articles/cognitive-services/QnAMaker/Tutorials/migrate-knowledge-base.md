---
title: Migreer kennisbanken - QnA Maker
description: Het migreren van een kennisbank vereist exporteren vanuit de ene kennisbank en vervolgens importeren in een andere.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258087"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Een kennisbank migreren met export-import

Migratie is het proces van het creëren van een nieuwe kennisbasis vanuit een bestaande kennisbank. U dit doen om verschillende redenen:

* back-up- en herstelproces
* CI/CD-pijplijn
* regio's verplaatsen

Het migreren van een kennisbank vereist exporteren vanuit een bestaande kennisbank en vervolgens importeren in een andere.

## <a name="prerequisites"></a>Vereisten

* Maak een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* Een nieuwe [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md) instellen

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migreer een kennisbank van QnA Maker
1. Meld u aan bij [qnA Maker portal](https://qnamaker.ai).
1. Selecteer de oorsprongskennisbank die u wilt migreren.

1. Selecteer **op** de pagina Instellingen **de optie Knowledge Base exporteren** om een .tsv-bestand te downloaden met de inhoud van uw oorsprongskennisbank - vragen, antwoorden, metagegevens, follow-upprompts en de gegevensbronnamen waaruit ze zijn geëxtraheerd.

1. Selecteer **Een kennisbank maken** in het bovenste menu en maak vervolgens een _lege_ kennisbank. Het is leeg, want wanneer u het maakt, gaat u geen URL's of bestanden toevoegen. Deze worden toegevoegd tijdens de importstap, na het maken.

    Configureer de kennisbank. Stel alleen de nieuwe knowledge base-naam in. Dubbele namen worden ondersteund en speciale tekens worden ook ondersteund.

    Selecteer niets uit stap 4 omdat deze waarden worden overschreven wanneer u het bestand importeert.

1. Selecteer in stap 5 **Maken**.

1. Open in deze nieuwe kennisbank het tabblad **Instellingen** en selecteer **Kennisbank importeren**. Hiermee worden de vragen, antwoorden, metagegevens, follow-upprompts geïmporteerd en worden de gegevensbronnamen bewaard waaruit ze zijn geëxtraheerd.

   > [!div class="mx-imgBorder"]
   > [![Kennisbank importeren](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Test** de nieuwe kennisbank met behulp van het testpaneel. Leer hoe [u uw kennisbank testen.](../How-To/test-knowledge-base.md)

1. **Publiceer** de kennisbank en maak een chatbot. Meer informatie over het [publiceren van uw kennisbank.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programmatisch migreren van een kennisbank van QnA Maker

Het migratieproces is programmatisch beschikbaar met behulp van de volgende REST API's:

**Exporteren**

* [Knowledge base API downloaden](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importeren**

* [API vervangen (opnieuw laden met dezelfde knowledge base ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [API maken (laden met nieuwe knowledge base ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Chatlogboeken en wijzigingen
Case-ongevoelige wijzigingen (synoniemen) worden niet automatisch geïmporteerd. Gebruik de [V4 API's](https://go.microsoft.com/fwlink/?linkid=2092179) om de wijzigingen in de nieuwe kennisbank te verplaatsen.

Er is geen manier om chatlogs te migreren, omdat de nieuwe kennisbank Application Insights gebruikt voor het opslaan van chatlogs.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een knowledge base bewerken](../How-To/edit-knowledge-base.md)
