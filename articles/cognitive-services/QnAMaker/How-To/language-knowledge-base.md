---
title: Niet-Engelse Knowledge Base-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker ondersteunt Knowledge Base-inhoud in veel talen. Elke QnA Maker-service moet echter voor één taal worden gereserveerd. De eerste Knowledge Base die als doel heeft gericht op een bepaalde QnA Maker service, stelt de taal van die service in.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961480"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Taal ondersteuning van Knowledge Base-inhoud voor QnA Maker

QnA Maker ondersteunt Knowledge Base-inhoud in veel talen. Elke QnA Maker-service moet echter voor één taal worden gereserveerd. De eerste Knowledge Base die als doel heeft gericht op een bepaalde QnA Maker service, stelt de taal van die service in. [Hier](../Overview/languages-supported.md) vindt u een lijst met ondersteunde talen.

De taal wordt automatisch herkend door de inhoud van de gegevens bronnen die worden geëxtraheerd. Zodra u een nieuwe QnA Maker-service en een nieuwe Knowledge Base in die service hebt gemaakt, kunt u controleren of de taal juist is ingesteld.

1. Navigeer naar [Azure Portal](https://portal.azure.com/).

1. Selecteer **resource groepen** en navigeer naar de resource groep waar de QnA Maker-service is geïmplementeerd en selecteer de **Azure Search** resource.

    ![Azure Search resource selecteren](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Selecteer **indexen**en selecteer vervolgens de **testkb** -index. Dit is de eerste Azure Search index die is gemaakt en bevat de opgeslagen inhoud van alle kennis bases in die service. 

1. Selecteer **velden** om de velden in de index weer te geven.

1. De kolom _Analyzer_ van de `questions` velden `answer` en is ingesteld op een specifieke taal. Deze taal is automatisch gedetecteerd tijdens de stap voor het maken van de Knowledge Base van de geïmporteerde bestanden en Url's. Deze taal kan niet worden gewijzigd nadat de resource is gemaakt.

    ![Geselecteerde analyse functie](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een QnA-bot maken met Azure Bot Service](../Tutorials/create-qna-bot.md)
