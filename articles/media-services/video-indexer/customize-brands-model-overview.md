---
title: Een merkenmodel aanpassen in Video Indexer - Azure
titleSuffix: Azure Media Services
description: Dit artikel geeft een overzicht van wat een Brands-model is in Video Indexer en hoe u dit aanpassen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838359"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Een merkenmodel aanpassen in Video-indexer

Video Indexer ondersteunt merkdetectie van spraak en visuele tekst tijdens het indexeren en opnieuw indexeren van video- en audio-inhoud. De merkdetectiefunctie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merkendatabase van Bing. Als Microsoft bijvoorbeeld wordt vermeld in een video- of audio-inhoud of als deze wordt weergegeven in visuele tekst in een video, detecteert Video Indexer het als een merk in de inhoud. Merken zijn gedesambiguated van andere termen met behulp van context.

Merkdetectie is nuttig in een breed scala aan bedrijfsscenario's, zoals inhoudsarchief en -ontdekking, contextuele reclame, analyse van sociale media, retail-concurrentieanalyse en nog veel meer. Video Indexer merkdetectie stelt u in staat om merkvermeldingen in spraak en visuele tekst te indexeren, met behulp van bing's brands database en met maatwerk door het bouwen van een aangepaste Brands model voor elke Video Indexer account. Met de aangepaste merken-modelfunctie u selecteren of Video Indexer merken uit de Bing-merkendatabase detecteert, bepaalde merken uitsluit dat deze worden gedetecteerd (in wezen een zwarte lijst van merken maken) en merken opnemen die deel moeten uitmaken van uw model dat mogelijk niet in de merkendatabase van Bing staat (in wezen het maken van een witte lijst met merken). Het aangepaste merkenmodel dat u maakt, is alleen beschikbaar in het account waarin u het model hebt gemaakt.

## <a name="out-of-the-box-detection-example"></a>Voorbeeld van out-of-the-boxdetectie

In de [Microsoft Build 2017 Day 2-presentatie](https://www.videoindexer.ai/media/ed6ede78ad/) wordt het merk "Microsoft Windows" meerdere keren weergegeven. Soms in het transcript, soms als visuele tekst en nooit zo letterlijk. Video Indexer detecteert met hoge precisie dat een term inderdaad merk is gebaseerd op de context, die meer dan 90k merken out of the box, en voortdurend updaten. Om 02:25, Video Indexer detecteert het merk van spraak en vervolgens weer om 02:40 van visuele tekst, die deel uitmaakt van het Windows-logo.

![Merken overzicht](./media/content-model-customization/brands-overview.png)

Praten over windows in de context van de bouw zal niet detecteren het woord "Windows" als een merk, en hetzelfde voor Box, Apple, Fox, enz., gebaseerd op geavanceerde Machine Learning algoritmen die weten hoe te disambiguate uit de context. Brand Detection werkt voor al onze ondersteunde talen. Klik hier voor [volledige Microsoft Build 2017 Dag 2 keynote video en index](https://www.videoindexer.ai/media/ed6ede78ad/).

Om je eigen merken mee te nemen, bekijk volgende stappen.

## <a name="next-steps"></a>Volgende stappen

[Merkenmodel aanpassen met API's](customize-brands-model-with-api.md)

[Merkenmodel aanpassen met behulp van de website](customize-brands-model-with-website.md)
