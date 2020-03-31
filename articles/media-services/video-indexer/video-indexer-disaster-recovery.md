---
title: Video Indexer failover en disaster recovery
titleSuffix: Azure Media Services
description: Meer informatie over het mislukken naar een secundair Video Indexer-account als er een regionaal datacentermislukt of -ramp optreedt.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499619"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Video Indexer failover en disaster recovery

Azure Media Services Video Indexer biedt geen directe failover van de service als er een regionale datacenterstoring of -storing optreedt. In dit artikel wordt uitgelegd hoe u uw omgeving configureert voor een failover om een optimale beschikbaarheid voor apps te garanderen en de hersteltijd te minimaliseren als zich een ramp voordoet.

We raden u aan het herstel van bedrijfscontinuïteitsrampen (BCDR) in regionale paren te configureren om te profiteren van het isolatie- en beschikbaarheidsbeleid van Azure. Zie [Azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie .

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, meldt u zich aan voor [de gratis proefversie van Azure.](https://azure.microsoft.com/free/)

## <a name="failover-to-a-secondary-account"></a>Failover naar een secundair e-mailaccount

Als u BCDR wilt implementeren, moet u twee Video Indexer-accounts hebben om redundantie af te handelen.

1. Maak twee Video Indexer-accounts die zijn verbonden met Azure (zie [Een Video Indexer-account maken).](connect-to-azure.md) Maak één account voor uw primaire regio en het andere naar het gekoppelde azure-gebied.
1. Als er een fout optreedt in uw primaire regio, schakelt u over op indexering met het secundaire account.

> [!TIP]
> U BCDR automatiseren door meldingen voor activiteitenlogboeken in te stellen voor meldingen over servicestatus per [Meldingen voor activiteitenlogboeken maken op servicemeldingen.](../../service-health/alerts-activity-log-service-notifications.md)

Zie Meerdere tenants beheren voor informatie over het gebruik van meerdere [tenants.](manage-multiple-tenants.md) Als u BCDR wilt implementeren, kiest u een van deze twee opties: [Video Indexer-account per tenant](manage-multiple-tenants.md#video-indexer-account-per-tenant) of [Azure-abonnement per tenant](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Volgende stappen

[Een Video Indexer-account beheren dat is verbonden met Azure](manage-account-connected-to-azure.md).
