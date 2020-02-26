---
title: Fout codes voor Live-gebeurtenissen Azure Media Services | Microsoft Docs
description: In dit artikel vindt u een overzicht van fout codes voor Live-gebeurtenissen.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599468"
---
# <a name="media-services-live-event-error-codes"></a>Fout codes voor Live-gebeurtenissen Media Services

De volgende tabel bevat de fout codes voor [Live-gebeurtenissen](live-events-outputs-concept.md) :

|Fout|Beschrijving|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Deze fout treedt op wanneer het binnenkomende coderings programma stromen verzendt die groter zijn dan 30fps voor het coderen van Live-gebeurtenissen/kanalen.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Deze fout treedt op wanneer het binnenkomende coderings programma stromen verzendt die de volgende oplossingen overschrijden: 1920x1088 voor het coderen van Live Events/channels en 4096 x 2160 voor Pass-Through Live-gebeurtenissen/kanalen.|

## <a name="see-also"></a>Zie ook

[Fout codes voor streaming-eind punten (oorsprong)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: live streamen met Media Services](stream-live-tutorial-with-api.md)
