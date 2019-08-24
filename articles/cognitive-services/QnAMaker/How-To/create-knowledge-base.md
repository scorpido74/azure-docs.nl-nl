---
title: Knowledge Base maken-QnA Maker
titleSuffix: Azure Cognitive Services
description: U kunt de QnA Maker-API-service portal gebruiken om een kennis database maken toe te voegen met Chit-Chat. Hierdoor is uw app actief. Voeg een vooraf ingevulde set van de bovenste Chit toe aan uw KB als uitgangs punt voor de Chit van uw bot en bespaar uw tijd en kosten om ze volledig te schrijven.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b2fb7496f16359f01ddbbe6db31b2d047a2ab4df
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012815"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Quickstart: Een kennis database maken met behulp van de QnA Maker-API-Service Portal

Met de QnA Maker-API Service Portal kunt u eenvoudig uw bestaande gegevens bronnen toevoegen wanneer u een Knowledge Base maakt. U kunt een nieuwe QnA Maker-kennisdatabase maken van de volgende documenttypen:

<!-- added for scanability -->
* Veelgestelde vragen over pagina 's
* Producten handleidingen
* Gestructureerde documenten

Neem een Chit-Chat persoonlijkheid op om uw kennis beter te maken met uw gebruikers.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="create-a-new-knowledge-base"></a>Een knowledge base maken

1. Meld u aan bij naar de [QnA Maker portal](https://qnamaker.ai) met uw Azure-referenties in en selecteer **maken van een kennisdatabase**.

1. Als u nog geen een QnA Maker-service hebt gemaakt, selecteert u **maken van een service QnA**. 

1. Selecteer uw Azure-tenant, de naam van de Azure-abonnement en de naam van de Azure-resource die is gekoppeld aan de QnA Maker-service in de lijsten in **stap 2** in de portal voor QnA Maker. Selecteer de Azure QnA Maker-service die als voor de Knowledge Base host.

    ![QnA-service instellen](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Voer de naam van uw knowledge base en de gegevensbronnen voor de nieuwe knowledge base.

    ![Set-gegevensbronnen](../media/qnamaker-how-to-create-kb/set-data-sources.png)

1. Geef uw service een **naam** zoals `my first kb`. Dubbele namen en speciale tekens worden ondersteund.

1. Voeg de pagina voor het oplossen van problemen QnA Maker `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`toe als URL `+ Add URL`: en selecteer deze. Zie voor meer informatie over de verschillende typen gegevensbronnen ondersteund [hier](../Concepts/data-sources-supported.md). Voor deze Snelstartgids uploadt u **geen bestanden** voor gegevens die u wilt extra heren. Zie de [informatie over de prijzen](https://aka.ms/qnamaker-pricing) om te zien hoeveel documenten kunnen toevoegen.

1. Voeg  **_professionele_ Chit-Chat** toe aan uw KB. 

1. Selecteer **maken uw KB**.

    ![KB maken](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Het kan enkele minuten duren voordat gegevens worden geëxtraheerd.

    ![Extractie](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Wanneer uw Knowledge Base is gemaakt, wordt u omgeleid naar de pagina **Knowledge Base** .

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de Knowledge Base, verwijdert u deze in de QnA Maker Portal.

## <a name="next-steps"></a>Volgende stappen

Voor kosten besparingen kunt u enkele, maar niet alle Azure-resources die zijn gemaakt voor QnA Maker [delen](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) .

> [!div class="nextstepaction"]
> [Chit chat persoonlijke toevoegen](./chit-chat-knowledge-base.md)
