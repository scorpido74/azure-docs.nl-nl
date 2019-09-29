---
title: Release opmerkingen bij Azure Media Services Video Indexer | Microsoft Docs
description: Om up-to-date te blijven met de meest recente ontwikkelingen, biedt dit artikel u de meest recente updates op Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: 4953160a30e45b9be1ff249b6cd1f74e01bb14f3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672661"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Release opmerkingen bij Azure Media Services Video Indexer

Als u wilt bijhouden met de meest recente ontwikkelingen, vindt in dit artikel u informatie over:

* De meest recente versies
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functies

## <a name="september-2019"></a>September 2019
 
Meerdere verbeteringen aangekondigd op IBC 2019:
 
* Animatie teken herkenning (open bare preview)

    De mogelijkheid om groeps advertenties te herkennen in inhoud met animatie, via integratie met aangepaste visie. Zie voor meer informatie [tekst detectie met animatie](animated-characters-recognition.md).
* Multi-language Identification (open bare preview)

    Detecteer segmenten in meerdere talen in het audio spoor en maak een meertalige transcript op basis hiervan. Eerste ondersteuning: Engels, Spaans, Duits en Frans. Zie voor meer informatie [automatisch inhoud identificeren en transcriberen in meerdere talen](multi-language-identification-transcription.md).
* Extractie van benoemde entiteiten voor personen en locatie

    Pakt merken, locaties en mensen uit vanuit spraak en visuele tekst via natuurlijke taal verwerking (NLP).
* Classificatie van type redactionele shot

    Het labelen van afbeeldingen met redactionele typen zoals close up, middel grote opname, twee opnamen, binnen en buiten, enzovoort. Zie [redactionele shot type Detection](scenes-shots-keyframes.md#editorial-shot-type-detection)(Engelstalig) voor meer informatie.
* Uitbrei ding voor het afdekken van het onderwerp-nu dekking van niveau 2
    
    Het model voor het defragmenteren van het onderwerp ondersteunt nu een diepere granulatie van de IPTC-taxonomie. Lees de volledige details op [Azure Media Services nieuwe, AI-innovaties](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>2019 augustus
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer geïmplementeerd in UK-zuid

U kunt nu een Video Indexer betaalde account maken in de regio UK Zuid.

### <a name="new-editorial-shot-type-insights-available"></a>Nieuwe redactionele-afbeeldings type inzichten beschikbaar

Nieuwe tags die aan video opnamen worden toegevoegd, bieden redactionele ' shot types ' om ze te identificeren met veelvoorkomende redactionele woord groepen die in de werk stroom voor het maken van inhoud worden gebruikt, zoals: extreme close, close, Wide, medium, two shot, buiten, binnen, links en rechts gezicht (beschikbaar in JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Uitpakken van nieuwe personen en locaties-entiteiten beschikbaar

Video Indexer identificeert benoemde locaties en personen via natuurlijke taal verwerking (NLP) van de OCR-en transcriptie van de video. Video Indexer gebruikt machine learning algoritme om te herkennen wanneer specifieke locaties (bijvoorbeeld de Eiffel-Tower) of mensen (bijvoorbeeld John Splinter) worden aangeroepen in een video.

### <a name="keyframes-extraction-in-native-resolution"></a>Extractie van hoofd frames in systeem eigen resolutie

Hoofd frames die door Video Indexer worden geëxtraheerd, zijn beschikbaar in de oorspronkelijke resolutie van de video.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA naar aangepaste gezichts modellen van afbeeldingen

Trainings gezichten van afbeeldingen die zijn verplaatst van de preview-modus naar GA (beschikbaar via API en in de portal).

> [!NOTE]
> Er zijn geen prijs wijzigingen die betrekking hebben op de overgang ' preview to GA '.

### <a name="hide-gallery-toggle-option"></a>Schakel optie galerie verbergen

De gebruiker kan kiezen om het tabblad Galerie van de portal te verbergen (vergelijkbaar met het verbergen van het tabblad voor beelden).
 
### <a name="maximum-url-size-increased"></a>De maximale URL-grootte is verhoogd

Ondersteuning voor URL-query teken reeks van 4096 (in plaats van 2048) bij het indexeren van een video.
 
### <a name="support-for-multi-lingual-projects"></a>Ondersteuning voor projecten met meerdere talen

Projecten kunnen nu worden gemaakt op basis van Video's die in verschillende talen zijn geïndexeerd (alleen API).

## <a name="july-2019"></a>2019 juli

### <a name="editor-as-a-widget"></a>Editor als een widget

De Video Indexer AI-editor is nu beschikbaar als widget die moeten worden inge sloten in klant toepassingen.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aangepast taal model bijwerken vanuit ondertitelings bestand vanuit de portal

Klanten kunnen VTT-, SRT-en TTML-bestands indelingen opgeven als invoer voor taal modellen op de pagina aanpassing van de portal.

## <a name="june-2019"></a>Juni 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer geïmplementeerd in Japan-Oost

U kunt nu een Video Indexer betaalde account maken in de regio Japan Oost.

### <a name="create-and-repair-account-api-preview"></a>Account-API maken en herstellen (preview-versie)

Er is een nieuwe API toegevoegd waarmee u [het eind punt of de sleutel van de Azure media service-verbinding kunt bijwerken](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Fout afhandeling bij uploaden verbeteren 

Er wordt een beschrijvende bericht weer gegeven in geval van een onjuiste configuratie van het onderliggende Azure Media Services-account.

### <a name="player-timeline-keyframes-preview"></a>Voor beeld van tijdlijn hoofdframes in de speler 

U kunt nu een voor beeld van een afbeelding zien voor elke keer op de tijd lijn van de speler.

### <a name="editor-semi-select"></a>De editor semi-Select

U kunt nu een voor beeld zien van alle inzichten die zijn geselecteerd als gevolg van het kiezen van een specifieke inzichten periode in de editor.

## <a name="may-2019"></a>Mei 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aangepast taal model bijwerken vanuit ondertitelings bestand

[Aangepaste taal model maken](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) en [aangepaste taal modellen bijwerken](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) api's bieden nu ondersteuning voor VTT-, SRT-en TTML-bestands indelingen als invoer voor taal modellen.

Bij het aanroepen van de [Update video transcriptie-API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)wordt de transcripten automatisch toegevoegd. Het trainings model dat is gekoppeld aan de video wordt automatisch bijgewerkt. Zie [een taal model aanpassen met video indexer](customize-language-model-overview.md)voor meer informatie over het aanpassen en trainen van uw taal modellen.

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nieuwe indelingen voor het downloaden van transcripten – TXT en CSV

Naast de indeling voor ondertiteling die al wordt ondersteund (SRT, VTT en TTML), ondersteunt Video Indexer nu het downloaden van transcripten in TXT-en CSV-indelingen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](video-indexer-overview.md)
