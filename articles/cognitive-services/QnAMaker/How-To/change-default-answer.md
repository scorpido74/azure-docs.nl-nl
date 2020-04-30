---
title: Standaard antwoord ophalen-QnA Maker
description: Het standaard antwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. Mogelijk wilt u het standaard antwoord wijzigen van het standaard antwoord standaard.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097095"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Standaard antwoord voor een QnA Maker resource wijzigen

Het standaard antwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. Mogelijk wilt u het standaard antwoord wijzigen van het standaard antwoord standaard.

## <a name="change-default-answer-in-the-azure-portal"></a>Standaard antwoord in de Azure Portal wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) en navigeer naar de resource groep die de QnA Maker-service vertegenwoordigt die u hebt gemaakt.

2. Klik om de **app service**te openen.

    ![In de Azure Portal Access app service for QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klik op **Toepassings instellingen** en bewerk het veld **DefaultAnswer** naar de gewenste standaard reactie. Klik op **Opslaan**.

    ![Selecteer toepassings instellingen en bewerk vervolgens DefaultAnswer voor QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uw app service opnieuw starten

    ![Nadat u de DefaultAnswer hebt gewijzigd, start u de QnA Maker appservice opnieuw](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Volgende stappen

* [Een kennisdatabase maken](../How-to/manage-knowledge-bases.md)