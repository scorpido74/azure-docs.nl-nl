---
title: Migreren van Indexer v1 en v2 naar Azure Media Services Video Indexer | Microsoft Documenten
description: In dit onderwerp wordt besproken hoe u migreren van Azure Media Indexer v1 en v2 naar Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513232"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migreren van Media Indexer en Media Indexer 2 naar Video Indexer

De [mediaprocessor Azure Media Indexer](media-services-index-content.md) en [de Media Indexer 2 Preview-mediaprocessors](media-services-process-content-with-indexer2.md) van Azure Media Indexer worden buiten gebruik gesteld. Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze oudere mediaprocessors.

Azure Media Services Video Indexer is gebouwd op Azure Media Analytics, Azure Cognitive Search, Cognitive Services (zoals de Face API, Microsoft Translator, de Computer Vision API en Custom Speech Service). Hiermee kunt u inzichten ophalen uit uw video's met Video Indexer-modellen voor audio en video. Zie [Video Indexer-video- en audiomodellen](../video-indexer/video-indexer-overview.md)voor video-indexeren om te zien in welke scenario's Video Indexer kan worden gebruikt, welke functies deze bieden en hoe u aan de slag gaan. 

U inzichten uit uw video- en audiobestanden extraheren met behulp van de [v3-voorinstellingen van Azure Media Services v3-analyzer](../latest/analyzing-video-audio-files-concept.md) of rechtstreeks met behulp van de [API's voor videoindexer.](https://api-portal.videoindexer.ai/) Momenteel is er een overlap tussen functies die worden aangeboden door de Video Indexer API's en de Media Services v3 API's.

> [!NOTE]
> Als u wilt begrijpen wanneer u voorinstellingen voor video-indexer versus Media Services-analyzer wilt gebruiken, raadpleegt u het [vergelijkingsdocument](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

In dit artikel worden de stappen besproken voor het migreren van de Azure Media Indexer en Azure Media Indexer 2 naar Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Migratieopties 

|Als u  |dan |
|---|---|
|een oplossing die een spraak-naar-teksttranscriptie biedt voor elke mediabestandsindeling in bestandsindelingen met ondertiteling: VTT, SRT of TTML<br/>evenals aanvullende audio-inzichten zoals: trefwoorden, onderwerpinferencing, akoestische gebeurtenissen, luidsprekerdiarisatie, entiteiten extractie en vertaling| werk uw toepassingen bij om de Azure Video Indexer-mogelijkheden te gebruiken via de Video Indexer v2 REST API of de voorinstelling azure media services v3 Audio Analyzer.|
|spraak-naar-tekstmogelijkheden| de Cognitive Services Speech API rechtstreeks te gebruiken.|  

## <a name="getting-started-with-video-indexer"></a>Aan de slag met Video Indexer

In de volgende sectie wordt u gewezen op relevante links: [Hoe kan ik aan de slag met Video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Aan de slag met Media Services v3 API's

Met Azure Media Services v3 API u inzichten uit uw video- en audiobestanden extraheren via de [v3-voorinstellingen van Azure Media Services.](../latest/analyzing-video-audio-files-concept.md) 

**Met AudioAnalyzerPreset** u meerdere audio-inzichten uit een audio- of videobestand halen. De uitvoer bevat een VTT- of TTML-bestand voor het audiotranscript en een JSON-bestand (met alle extra audio-inzichten). De audio-inzichten omvatten trefwoorden, luidsprekerindexering en analyse van spraaksentimenten. AudioAnalyzerPreset ondersteunt ook taaldetectie voor specifieke talen. Zie [Transformaties voor](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)gedetailleerde informatie.

### <a name="get-started"></a>Aan de slag

Om aan de slag te gaan zie:

* [Zelfstudie](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset samples: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) of [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset samples: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) of [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Aan de slag met Cognitive Services Speech Services

[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) biedt een spraak-naar-tekstservice die audiostreams naar tekst transcribeert in realtime die uw toepassingen, hulpprogramma's of apparaten kunnen gebruiken of weergeven. U spraak-naar-tekst gebruiken om [uw eigen akoestische model, taalmodel of uitspraakmodel aan](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)te passen. Zie [Cognitive Services speech-to-text voor](../../cognitive-services/speech-service/speech-to-text.md)meer informatie. 

> [!NOTE] 
> De spraak-naar-tekstservice neemt geen videobestandsindelingen en neemt alleen [bepaalde audioformaten .](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats) 

Zie [Wat is spraak-naar-tekst voor](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text) meer informatie over de tekst-naar-spraakservice en hoe u aan de slag gaan?

## <a name="known-differences-from-deprecated-services"></a>Bekende verschillen met afgeschafte diensten 

U zult merken dat Video Indexer, Azure Media Services v3 AudioAnalyzerPreset en Cognitive Services Speech Services-services betrouwbaarder zijn en betere kwaliteitsuitvoer produceren dan de gepensioneerde Azure Media Indexer 1- en Azure Media Indexer 2-processors.  

Enkele bekende verschillen zijn: 

* Cognitive Services Speech Services ondersteunt geen trefwoordextractie. Video Indexer en Media Services v3 AudioAnalyzerPreset bieden echter beide een robuustere set zoekwoorden in JSON-bestandsindeling. 

## <a name="need-help"></a>Hebt u hulp nodig?

U een ondersteuningsticket openen door te navigeren naar [nieuw ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)


