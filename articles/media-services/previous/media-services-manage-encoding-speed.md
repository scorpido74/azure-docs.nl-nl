---
title: Snelheid en gelijktijdigheid van uw codering beheren met Azure Media Services | Microsoft Documenten
description: In dit artikel vindt u een kort overzicht van hoe u de snelheid en gelijktijdigheid van uw coderingstaken/taken beheren met Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463749"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>De snelheid en gelijktijdigheid van uw codering beheren  

Dit artikel geeft een kort overzicht van hoe u de snelheid en gelijktijdigheid van uw coderingstaken/taken beheren.

## <a name="overview"></a>Overzicht

In Media Services bepaalt een **type gereserveerde eenheid** de snelheid waarmee uw mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**). In het onderwerp [coderingseenheden schalen](media-services-scale-media-processing-overview.md) wordt een tabel weergegeven waarmee u een beslissing nemen bij het kiezen tussen verschillende coderingssnelheden.

Naast het opgeven van het type gereserveerde eenheid, u opgeven om uw account in te richten bij **gereserveerde eenheden.** Op basis van het aantal ingerichte, gereserveerde eenheden wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account. Als uw account bijvoorbeeld vijf gereserveerde eenheden heeft, worden vijf mediataken gelijktijdig uitgevoerd zolang er taken zijn die moeten worden verwerkt. De overige taken wachten in de wachtrij en worden opgehaald voor het opeenvolgend verwerken wanneer een lopende taak is voltooid. Als een account geen gereserveerde eenheden heeft ingericht, worden taken achtereenvolgens opgehaald. In dit geval is de wachttijd tussen de afwerking van de ene taak en de volgende taak die wordt gestart, afhankelijk van de beschikbaarheid van resources in het systeem.

Zie [dit](media-services-scale-media-processing-overview.md) onderwerp voor gedetailleerde informatie en voorbeelden die laten zien hoe u coderingseenheden schalen.

## <a name="next-step"></a>Volgende stap

[Coderingseenheden schalen](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

