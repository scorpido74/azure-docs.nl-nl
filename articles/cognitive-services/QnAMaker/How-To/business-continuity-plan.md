---
title: Plan voor bedrijfs continuïteit-QnA Maker
titleSuffix: Azure Cognitive Services
description: Het hoofd doel van het plan voor bedrijfs continuïteit is het maken van een robuust eind punt van de Knowledge Base, waardoor er geen verdere tijd is voor de bot of de toepassing die deze gebruikt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: f920a789f1ec631c5d70863b10d8364b3eb81b6c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650466"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Een bedrijfs continuïteits plan maken voor uw QnA Maker-service

Het hoofd doel van het plan voor bedrijfs continuïteit is het maken van een robuust eind punt van de Knowledge Base, waardoor er geen verdere tijd is voor de bot of de toepassing die deze gebruikt.

![QnA Maker BCP-abonnement](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Het idee op hoog niveau zoals hierboven wordt weer gegeven, is als volgt:

1. Stel twee parallelle [QnA Maker Services](../How-To/set-up-qnamaker-service-azure.md) in in [Azure gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Zorg ervoor dat de primaire en secundaire Azure Search-indexen synchroon blijven. Gebruik het GitHub-voor beeld [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) om te zien hoe u een back-up maakt van Azure-indexen.

3. Maak een back-up van de Application Insights met [doorlopend exporteren](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Zodra de primaire en secundaire Stacks zijn ingesteld, gebruikt u [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) om de twee eind punten te configureren en een routerings methode in te stellen.

5. U moet een Secure Sockets Layer (SSL)-certificaat maken voor uw Traffic Manager-eind punt. [BIND het SSL-certificaat](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) in uw app-Services.

6. Ten slotte gebruikt u het Traffic Manager-eind punt in uw bot of app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Capactiy kiezen](./improve-knowledge-base.md)
