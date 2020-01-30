---
title: Standaard antwoord ophalen-QnA Maker
description: Het standaard antwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. Mogelijk wilt u het standaard antwoord wijzigen van het standaard antwoord standaard.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843273"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Standaard antwoord voor een Knowledge Base instellen

Het standaard antwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. Mogelijk wilt u het standaard antwoord wijzigen van het standaard antwoord standaard.

## <a name="change-default-answer"></a>Standaard antwoord wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) en navigeer naar de resourcegroep die de QnA Maker-service die u hebt gemaakt.

2. Klik hier om de **App Service**.

    ![In de Azure-portal toegang tot appservice voor QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klik op **toepassingsinstellingen** en bewerk de **DefaultAnswer** veld naar de gewenste Standaardantwoord. Klik op **Opslaan**.

    ![Selecteer de toepassingsinstellingen en bewerk vervolgens DefaultAnswer voor QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uw App-service opnieuw starten

    ![Nadat u de DefaultAnswer hebt gewijzigd, start opnieuw op de appservice QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Volgende stappen

* [Een bot met QnA Maker en LUIS maken](../tutorials/integrate-qnamaker-luis.md)