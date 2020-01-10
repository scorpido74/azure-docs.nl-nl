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
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752380"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Wat gebeurt er met mijn app tijdens de implementatie?

Alle officieel ondersteunde implementatie methoden brengen wijzigingen aan in de bestanden in de map `/home/site/wwwroot` van uw app. Deze bestanden worden gebruikt om uw app uit te voeren. Daarom kan de implementatie mislukken vanwege vergrendelde bestanden. De app kan ook onvoorspelbaar werken tijdens de implementatie, omdat niet alle bestanden tegelijk worden bijgewerkt. Dit is niet wenselijk voor een klant gerichte app. Er zijn een aantal verschillende manieren om deze problemen te voor komen:

- Stop uw app of schakel de offlinemodus in voor uw app tijdens de implementatie. Zie voor meer informatie [omgaan met vergrendelde bestanden tijdens de implementatie](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implementeren naar een [staging-sleuf](../articles/app-service/deploy-staging-slots.md) met [automatisch wisselen](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) ingeschakeld. 
- Gebruik [uitvoeren vanuit pakket](https://github.com/Azure/app-service-announcements/issues/84) in plaats van continue implementatie.
