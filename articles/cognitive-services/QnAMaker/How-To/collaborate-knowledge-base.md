---
title: Samen werken op Knowledge Base-QnA Maker
description: Met QnA Maker kunnen meerdere personen samen werken aan een Knowledge Base. Deze functie wordt meegeleverd met het toegangs beheer op basis van rollen (Azure RBAC) van Azure.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 20eb58f346322cb78bff85af3d6a0d366090763d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446946"
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

    ![QnA Maker Resource lijst](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Ga naar het tabblad **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selecteer **Toevoegen**.

    ![QnA Maker IAM toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selecteer een rol in de volgende lijst:

    |Rol|
    |--|
    |Eigenaar|
    |Inzender|
    |QnA Maker lezer Cognitive Services|
    |Cognitive Services QnA Maker editor|
    |Cognitive Services gebruiker|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM-rol toevoegen.":::

1. Voer het e-mail adres van de gebruiker in en druk op **Opslaan**.

    ![QnA Maker IAM add e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker Knowledge bases weer geven

Wanneer de persoon met wie u uw QnA Maker-service hebt gedeeld, zich aanmeldt in de [QnA Maker Portal](https://qnamaker.ai), kunnen ze alle kennissen in die service zien op basis van hun rol.

Wanneer ze een kennis database selecteren, is hun huidige rol op die QnA Maker resource zichtbaar naast de naam van de Knowledge Base.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Scherm opname van de kennis database in de bewerkings modus met de rolnaam tussen haakjes naast de naam van de Knowledge Base in de linkerbovenhoek van de webpagina.":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een knowledge base testen](./test-knowledge-base.md)

Meer informatie over samen werking:
* Toegangs beheer op basis van rollen in [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker op rollen gebaseerde toegangs beheer [concepten](../Concepts/role-based-access-control.md)
