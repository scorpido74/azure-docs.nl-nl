---
title: Samenwerken aan kennisbasis - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker stelt meerdere mensen in staat om samen te werken aan een kennisbank. Deze functie is voorzien van het Azure Role-Based Access Control.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660746"
---
# <a name="collaborate-on-your-knowledge-base"></a>Werk samen aan uw kennisbank

QnA Maker stelt meerdere mensen in staat om samen te werken aan alle kennisbases in dezelfde QnA Maker-bron. Deze functie is voorzien van het Azure [Role-Based Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Voer de volgende stappen uit om uw QnA Maker-service met iemand te delen:

1. Meld u aan bij de Azure-portal en ga naar uw QnA Maker-bron.

    ![QnA Maker-resourcelijst](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Ga naar het tabblad **Toegangsbeheer (IAM).**

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecteer **Toevoegen**.

    ![QnA Maker IAM toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecteer de **rol Eigenaar** of **bijdrager.** U alleen-lezen geen toegang verlenen via op rollen gebaseerd toegangsbeheer. Eigenaren en inzenderrollen hebben toegangsmachtigingen voor lezen en schrijven voor de QnA Maker-service.

    ![QnA Maker IAM voegt rol toe](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Voer het e-mailadres van de gebruiker in en druk op **Opslaan**.

    ![QnA Maker IAM e-mail toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Wanneer de persoon, u uw QnA Maker-service met gedeeld, logt in op de [QnA Maker-portal,](https://qnamaker.ai) kunnen ze alle kennisbases in die service zien.

Vergeet niet dat u een bepaalde kennisbasis niet delen in een QnA Maker-service. Als u meer gedetailleerde toegangscontrole wilt, u overwegen uw kennisbases te distribueren over verschillende QnA Maker-services.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een knowledge base testen](./test-knowledge-base.md)
