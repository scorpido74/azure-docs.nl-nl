---
title: Samen werken op Knowledge Base-QnA Maker
titleSuffix: Azure Cognitive Services
description: Met QnA Maker kunnen meerdere personen samen werken aan een Knowledge Base. Deze functie wordt meegeleverd met de op rollen gebaseerde Access Control van Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660746"
---
# <a name="collaborate-on-your-knowledge-base"></a>Samen werken aan uw Knowledge Base

Met QnA Maker kunnen meerdere mensen samen werken aan alle kennis grondslagen in dezelfde QnA Maker resource. Deze functie wordt meegeleverd met de [op rollen gebaseerde Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)van Azure.

Voer de volgende stappen uit om uw QnA Maker-service te delen met iemand:

1. Meld u aan bij de Azure Portal en ga naar uw QnA Maker-resource.

    ![Lijst met resources voor QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Ga naar het tabblad **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecteer **Toevoegen**.

    ![QnA Maker IAM toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecteer de rol **eigenaar** of **Inzender** . U kunt alleen-lezen toegang verlenen via Access Control op basis van rollen. De rollen eigenaar en Inzender hebben lees-/schrijftoegang tot de QnA Maker-service.

    ![QnA Maker IAM-rol toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Voer het e-mail adres van de gebruiker in en druk op **Opslaan**.

    ![QnA Maker IAM add e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Als de persoon, u uw QnA Maker-service hebt gedeeld met, meldt zich aan bij de [QnA Maker-Portal](https://qnamaker.ai) ze kunnen alle kennissen in die service zien.

Houd er rekening mee dat u een bepaalde Knowledge Base niet kunt delen in een QnA Maker-service. Als u meer nauw keuriger toegangs beheer wilt, kunt u uw kennis grondslagen distribueren over verschillende QnA Maker Services.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennis database testen](./test-knowledge-base.md)
