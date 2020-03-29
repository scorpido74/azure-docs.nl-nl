---
title: Krijg standaard antwoord - QnA Maker
description: Het standaardantwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. U het standaardantwoord wijzigen vanuit het standaardstandaardantwoord.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843273"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Standaardantwoord instellen voor een kennisbank

Het standaardantwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. U het standaardantwoord wijzigen vanuit het standaardstandaardantwoord.

## <a name="change-default-answer"></a>Standaardantwoord wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) en navigeer naar de resourcegroep die de QnA Maker-service vertegenwoordigt die u hebt gemaakt.

2. Klik hier om de **App-service**te openen.

    ![Toegang tot de App-service voor QnA Maker in de Azure-portal](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klik op **Toepassingsinstellingen** en bewerk het veld **Standaardantwoord** op het gewenste standaardantwoord. Klik op **Opslaan**.

    ![Selecteer Toepassingsinstellingen en bewerk vervolgens DefaultAnswer voor QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uw App-service opnieuw starten

    ![Nadat u de standaardantwoord hebt gewijzigd, start u de appservice QnA Maker opnieuw](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Volgende stappen

* [Maak een bot met QnA Maker en LUIS](../tutorials/integrate-qnamaker-luis.md)