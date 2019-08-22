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
ms.openlocfilehash: 84f13f7e1d83f1ead00303b694b617d3ba1c8931
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876650"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Een bedrijfs continuïteits plan maken voor uw QnA Maker-service

Het hoofd doel van het plan voor bedrijfs continuïteit is het maken van een robuust eind punt van de Knowledge Base, waardoor er geen verdere tijd is voor de bot of de toepassing die deze gebruikt.

![QnA Maker BCP-abonnement](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Het idee op hoog niveau zoals hierboven wordt weer gegeven, is als volgt:

1. Stel twee parallelle [QnA Maker Services](../How-To/set-up-qnamaker-service-azure.md) in in [Azure gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Zorg ervoor dat de primaire en secundaire Azure Search-indexen synchroon blijven. Gebruik het GitHub-voor beeld [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) om te zien hoe u een back-up maakt van Azure-indexen.

3. Maak een back-up van de Application Insights met doorlopend [exporteren](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Zodra de primaire en secundaire Stacks zijn ingesteld, gebruikt u [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) om de twee eind punten te configureren en een routerings methode in te stellen.

5. U moet een SSL-certificaat voor uw Traffic Manager-eind punt maken. [BIND het SSL-certificaat](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) in uw app-Services.

6. Ten slotte gebruikt u het Traffic Manager-eind punt in uw bot of app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kies de capaciteit voor uw QnA Maker-implementatie](../Tutorials/choosing-capacity-qnamaker-deployment.md)
