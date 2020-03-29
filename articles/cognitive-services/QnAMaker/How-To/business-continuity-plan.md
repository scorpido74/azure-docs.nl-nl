---
title: Bedrijfscontinuïteitsplan - QnA Maker
titleSuffix: Azure Cognitive Services
description: Het primaire doel van het bedrijfscontinuïteitsplan is het creëren van een veerkrachtig eindpunt voor kennisbasis, dat ervoor zou zorgen dat de Bot of de toepassing deze niet uitsluit.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: f920a789f1ec631c5d70863b10d8364b3eb81b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650466"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Maak een bedrijfscontinuïteitsplan voor uw QnA Maker-service

Het primaire doel van het bedrijfscontinuïteitsplan is het creëren van een veerkrachtig eindpunt voor kennisbasis, dat ervoor zou zorgen dat de Bot of de toepassing deze niet uitsluit.

![QnA Maker bcp plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Het idee op hoog niveau zoals hierboven weergegeven is als volgt:

1. Stel twee parallelle [QnA Maker-services](../How-To/set-up-qnamaker-service-azure.md) in [azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)in.

2. Houd de primaire en secundaire Azure-zoekindexen gesynchroniseerd. Gebruik het GitHub-voorbeeld [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) om te zien hoe azure-indexen een back-up kunnen maken.

3. Een back-up maken van de toepassingsinzichten met [continue export.](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)

4. Zodra de primaire en secundaire stapels zijn ingesteld, gebruikt u [verkeersbeheerbeheer](https://docs.microsoft.com/azure/traffic-manager/) om de twee eindpunten te configureren en een routeringsmethode in te stellen.

5. U moet een SSL-certificaat (Secure Sockets Layer) maken voor het eindpunt van uw verkeersbeheerder. [Bind het SSL-certificaat](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) in uw App-services.

6. Gebruik ten slotte het eindpunt van de verkeersbeheerder in uw Bot of App.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kies capactiy](./improve-knowledge-base.md)
