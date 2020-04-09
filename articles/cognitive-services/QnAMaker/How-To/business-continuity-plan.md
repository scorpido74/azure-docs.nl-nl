---
title: Bedrijfscontinuïteitsplan - QnA Maker
description: Het primaire doel van het bedrijfscontinuïteitsplan is het creëren van een veerkrachtig eindpunt voor kennisbasis, dat ervoor zou zorgen dat de Bot of de toepassing deze niet uitsluit.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887062"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Maak een bedrijfscontinuïteitsplan voor uw QnA Maker-service

Het primaire doel van het bedrijfscontinuïteitsplan is het creëren van een veerkrachtig eindpunt voor kennisbasis, dat ervoor zou zorgen dat de Bot of de toepassing deze niet uitsluit.

## <a name="business-continuity-with-traffic-manager"></a>Bedrijfscontinuïteit met verkeersbeheerder

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Het idee op hoog niveau zoals hierboven weergegeven is als volgt:

1. Stel twee parallelle [QnA Maker-services](set-up-qnamaker-service-azure.md) in [azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)in.

2. [Maak een back-up van](../../../app-service/manage-backup.md) uw primaire QnA Maker App-service en [herstel](../../../app-service/web-sites-restore.md) deze in de secundaire installatie. Dit zorgt ervoor dat beide opstellingen werken met dezelfde hostname en toetsen.

3. Houd de primaire en secundaire Azure-zoekindexen gesynchroniseerd. Gebruik het GitHub-voorbeeld [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) om te zien hoe azure-indexen een back-up kunnen maken.

4. Een back-up maken van de toepassingsinzichten met [continue export.](../../../application-insights/app-insights-export-telemetry.md)

5. Zodra de primaire en secundaire stapels zijn ingesteld, gebruikt u [verkeersbeheerbeheer](../../../traffic-manager/traffic-manager-overview.md) om de twee eindpunten te configureren en een routeringsmethode in te stellen.

6. U moet een TLS (Transport Layer Security) maken, voorheen bekend als Secure Sockets Layer (SSL), certificaat voor uw eindpunt voor verkeersbeheerbeheer. [Bind het TLS/SSL-certificaat](../../../app-service/configure-ssl-bindings.md) in uw App-services.

7. Gebruik ten slotte het eindpunt van de verkeersbeheerder in uw Bot of App.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Capaciteit kiezen](./improve-knowledge-base.md)