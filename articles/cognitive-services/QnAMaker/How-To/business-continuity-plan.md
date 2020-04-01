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
ms.openlocfilehash: 2100426923418104c31933bf9d1406a6441d3841
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410915"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Maak een bedrijfscontinuïteitsplan voor uw QnA Maker-service

Het primaire doel van het bedrijfscontinuïteitsplan is het creëren van een veerkrachtig eindpunt voor kennisbasis, dat ervoor zou zorgen dat de Bot of de toepassing deze niet uitsluit.

![QnA Maker bcp plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Het idee op hoog niveau zoals hierboven weergegeven is als volgt:

1. Stel twee parallelle [QnA Maker-services](../How-To/set-up-qnamaker-service-azure.md) in [azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)in.

2. Houd de primaire en secundaire Azure-zoekindexen gesynchroniseerd. Gebruik het GitHub-voorbeeld [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) om te zien hoe azure-indexen een back-up kunnen maken.

3. Een back-up maken van de toepassingsinzichten met [continue export.](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)

4. Zodra de primaire en secundaire stapels zijn ingesteld, gebruikt u [verkeersbeheerbeheer](https://docs.microsoft.com/azure/traffic-manager/) om de twee eindpunten te configureren en een routeringsmethode in te stellen.

5. U moet een TLS (Transport Layer Security) maken, voorheen bekend als Secure Sockets Layer (SSL), certificaat voor uw eindpunt voor verkeersbeheerbeheer. [Bind het TLS/SSL-certificaat](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) in uw App-services.

6. Gebruik ten slotte het eindpunt van de verkeersbeheerder in uw Bot of App.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Kies capactiy](./improve-knowledge-base.md)
