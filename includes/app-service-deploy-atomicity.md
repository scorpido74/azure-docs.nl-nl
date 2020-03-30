---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945159"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Wat gebeurt er met mijn app tijdens de implementatie?

Alle officieel ondersteunde implementatiemethoden brengen wijzigingen aan `/home/site/wwwroot` in de bestanden in de map van uw app. Deze bestanden worden gebruikt om uw app uit te voeren. Daarom kan de implementatie mislukken vanwege vergrendelde bestanden. De app kan zich ook onvoorspelbaar gedragen tijdens de implementatie, omdat niet alle bestanden tegelijkertijd zijn bijgewerkt. Dit is ongewenst voor een klantgerichte app. Er zijn een paar verschillende manieren om deze problemen te voorkomen:

- [Voer uw app rechtstreeks uit het ZIP-pakket](../articles/app-service/deploy-run-package.md) zonder deze uit te pakken.
- Stop uw app of schakel de offlinemodus in voor uw app tijdens de implementatie. Zie [Omgaan met vergrendelde bestanden tijdens de implementatie voor](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)meer informatie.
- Implementeren in een [faseringssleuf](../articles/app-service/deploy-staging-slots.md) met [ingeschakelde automatische swap.](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) 
