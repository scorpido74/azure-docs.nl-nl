---
title: Samen werken op Knowledge Base-QnA Maker
description: Met QnA Maker kunnen meerdere personen samen werken aan een Knowledge Base. Deze functie wordt meegeleverd met de op rollen gebaseerde Access Control van Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650781"
---
# <a name="collaboration-with-authors-and-editors"></a>Samen werking met auteurs en editors

Samen werking wordt op het QnA Maker resource niveau gegeven, zodat u de toegang van mede werkers kunt beperken op basis van de rol van de mede werker. Meer informatie over de [concepten](../Concepts/role-based-access-control.md)van QnA Maker-samen werker-authenticatie.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Op rollen gebaseerde toegang (RBAC) toevoegen aan uw QnA Maker-resource

Met QnA Maker kunnen meerdere mensen samen werken aan alle kennis grondslagen in dezelfde QnA Maker resource. Deze functie wordt meegeleverd met de [op rollen gebaseerde Access Control](../../../active-directory/role-based-access-control-configure.md)van Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Toegang op het QnA Maker resource niveau

U kunt een bepaalde Knowledge Base niet delen in een QnA Maker-service. Als u meer nauw keuriger toegangs beheer wilt, kunt u uw kennis grondslagen distribueren over verschillende QnA Maker resources en vervolgens functies toevoegen aan elke resource.

## <a name="add-role-to-resource"></a>Rol toevoegen aan resource

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Een gebruikers account toevoegen aan de QnA Maker-resource

De volgende stappen gebruiken de rol samen werken, maar een van de [rollen](../reference-role-based-access-control.md) kan worden toegevoegd met behulp van deze stappen

1. Meld u aan bij [Azure](https://portal.azure.com/) Portal en ga naar uw QnA Maker-resource.

    ![QnA Maker Resource lijst](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Ga naar het tabblad **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecteer **Toevoegen**.

    ![QnA Maker IAM toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecteer een rol in de volgende lijst:

    |Rol|
    |--|
    |Eigenaar|
    |Inzender|
    |QnA Maker lezer|
    |QnA Maker editor|
    |Cognitive Services gebruiker|

    ![QnA Maker IAM-rol toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Voer het e-mail adres van de gebruiker in en druk op **Opslaan**.

    ![QnA Maker IAM add e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Wanneer de persoon met wie u uw QnA Maker-service hebt gedeeld, zich aanmeldt in de [QnA Maker Portal](https://qnamaker.ai), kunnen ze alle kennissen in die service zien op basis van hun rol.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een knowledge base testen](./test-knowledge-base.md)

Meer informatie over samen werking:
* Toegangs beheer op basis van rollen in [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker op rollen gebaseerde toegangs beheer [concepten](../Concepts/role-based-access-control.md)
