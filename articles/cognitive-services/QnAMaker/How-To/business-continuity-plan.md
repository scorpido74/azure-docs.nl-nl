---
title: Plan voor bedrijfs continuïteit-QnA Maker
description: Het hoofd doel van het plan voor bedrijfs continuïteit is het maken van een robuust eind punt van de Knowledge Base, waardoor er geen verdere tijd is voor de bot of de toepassing die deze gebruikt.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887062"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Een bedrijfs continuïteits plan maken voor uw QnA Maker-service

Het hoofd doel van het plan voor bedrijfs continuïteit is het maken van een robuust eind punt van de Knowledge Base, waardoor er geen verdere tijd is voor de bot of de toepassing die deze gebruikt.

## <a name="business-continuity-with-traffic-manager"></a>Bedrijfs continuïteit met Traffic Manager

> [!div class="mx-imgBorder"]
> ![QnA Maker BCP-abonnement](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Het idee op hoog niveau zoals hierboven wordt weer gegeven, is als volgt:

1. Stel twee parallelle [QnA Maker Services](set-up-qnamaker-service-azure.md) in in [Azure gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Back-up maken](../../../app-service/manage-backup.md) van uw primaire QnA Maker app-service en deze [herstellen](../../../app-service/web-sites-restore.md) in de secundaire installatie. Dit zorgt ervoor dat beide instellingen werken met dezelfde hostnaam en sleutels.

3. Zorg ervoor dat de primaire en secundaire Azure Search-indexen synchroon blijven. Gebruik het GitHub-voor beeld [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) om te zien hoe u een back-up maakt van Azure-indexen.

4. Maak een back-up van de Application Insights met [doorlopend exporteren](../../../application-insights/app-insights-export-telemetry.md).

5. Zodra de primaire en secundaire Stacks zijn ingesteld, gebruikt u [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) om de twee eind punten te configureren en een routerings methode in te stellen.

6. U moet een Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), maken van het certificaat voor uw Traffic Manager-eind punt. [BIND het TLS/SSL-certificaat](../../../app-service/configure-ssl-bindings.md) in uw app-Services.

7. Ten slotte gebruikt u het Traffic Manager-eind punt in uw bot of app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Capaciteit kiezen](./improve-knowledge-base.md)