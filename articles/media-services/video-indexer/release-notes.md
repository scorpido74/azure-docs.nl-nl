---
title: Releasenotes voor video-indexer van Azure Media Services | Microsoft Documenten
description: Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt dit artikel u de nieuwste updates over Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: 5aa7abf7aafc14e71af5618cec892ef9f843d88a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733058"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Opmerkingen vrijgeven van Azure Media Services Video Indexer

>Ontvang een melding over wanneer u deze pagina opnieuw moet `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` bezoeken voor updates door deze URL te kopiëren en te plakken: in uw RSS-feedlezer.

Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt dit artikel u informatie over:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="april-2020"></a>April 2020

### <a name="new-widget-parameters-capabilities"></a>Nieuwe mogelijkheden voor widgetparameters

De **widget Insights** bevat `language` `control`nieuwe parameters: en .

De **widget Player** `locale` heeft een nieuwe parameter. Beide `locale` `language` parameters bepalen de taal van de speler.

Zie de sectie [Widgettypen](video-indexer-embed-widgets.md#widget-types) voor meer informatie. 

## <a name="new-player-skin"></a>Nieuwe spelershuid

Een nieuwe speler huid gelanceerd met een bijgewerkt ontwerp.

## <a name="january-2020"></a>Januari 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Ondersteuning voor aangepaste talen voor extra talen

Video Indexer ondersteunt nu `ar-SY` aangepaste `en-UK`taalmodellen voor , en `en-AU` (alleen API).
 
### <a name="delete-account-timeframe-action-update"></a>Update van de actie-update van de accountperiode

Accountactie verwijderen verwijdert het account nu binnen 90 dagen in plaats van 48 uur.
 
### <a name="new-video-indexer-github-repository"></a>Nieuwe Video Indexer GitHub repository

Een nieuwe Video Indexer GitHub met verschillende projecten, aan de slag gidsen en code monsters is nu beschikbaar:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger-update

Video Indexer unified **authentications** and **operations** into single [Video Indexer OpenAPI Specification (swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Ontwikkelaars kunnen de API's vinden in [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>December 2019

### <a name="update-transcript-with-the-new-api"></a>Transcript bijwerken met de nieuwe API

Werk een specifieke sectie in het transcript bij met de [UPDATE-Video-Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) API.

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Accountconfiguratie herstellen vanuit de Portal VideoIndexer

U nu de verbindingsconfiguratie van Media Services bijwerken om zelf hulp te bieden bij problemen als: 

* onjuiste Azure Media Services-bron
* wachtwoordwijzigingen
* Media Services-bronnen zijn verplaatst tussen abonnementen  

Als u de accountconfiguratie wilt herstellen, navigeert u in de portal Videoindexer naar Instellingen > Accounttabblad (als eigenaar).

### <a name="configure-the-custom-vision-account"></a>Het aangepaste visie-account configureren

Configureer het aangepaste visie-account op betaalde accounts met behulp van de Video Indexer-portal (voorheen werd dit alleen ondersteund door API). Als u zich hiervoor aanmeldt bij de portal Videoindexer, kiest u Modelaanpassing > geanimeerde tekens > configureren. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scènes, opnamen en hoofdframes – nu in één inzichtsvenster

Scènes, opnamen en hoofdframes worden nu samengevoegd tot één inzicht voor eenvoudiger verbruik en navigatie. Wanneer u de gewenste scène selecteert, u zien uit welke opnamen en hoofdframes het bestaat. 

### <a name="notification-about-a-long-video-name"></a>Melding over een lange videonaam

Wanneer een videonaam langer is dan 80 tekens, geeft Video Indexer een beschrijvende fout bij het uploaden weer.

### <a name="streaming-endpoint-is-disabled-notification"></a>Streaming eindpunt is uitgeschakeld melding

Wanneer streaming-eindpunt is uitgeschakeld, geeft Video Indexer een beschrijvende fout weer op de spelerspagina.

### <a name="error-handling-improvement"></a>Verbetering van de foutafhandeling

Statuscode 409 wordt nu geretourneerd uit [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) en [Update Video Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) API's in het geval een video actief wordt geïndexeerd, om te voorkomen dat de huidige wijzigingen van de re-index per ongeluk worden overschrijven.

## <a name="november-2019"></a>November 2019
 
* Ondersteuning voor Koreaanse aangepaste taalmodellen

    Video-indexer ondersteunt nu aangepaste`ko-KR`taalmodellen in het Koreaans ( ) in zowel de API als portal. 
* Nieuwe talen ondersteund voor spraak-naar-tekst (STT)

    Video Indexer API's ondersteunen nu STT in Arabisch Levantine (ar-SY), Engels Brits dialect (en-GB) en Engels Australisch dialect (en-AU).
    
    Voor video-upload, hebben we zh-HANS vervangen naar zh-CN, beide worden ondersteund, maar zh-CN wordt aanbevolen en nauwkeuriger.
    
## <a name="october-2019"></a>Oktober 2019
 
* Zoeken naar geanimeerde tekens in de galerie

    Bij het indexeren van geanimeerde tekens kun je er nu naar zoeken in de videokombuis van het account. Zie [Herkenning van geanimeerde tekens](animated-characters-recognition.md)voor meer informatie .

## <a name="september-2019"></a>September 2019
 
Meerdere ontwikkelingen aangekondigd op IBC 2019:
 
* Herkenning van geanimeerde tekens (openbare preview)

    Mogelijkheid om groepsadvertentieteertekens in geanimeerde inhoud te detecteren, via integratie met aangepaste visie. Zie [Detectie van tekens geanimeerd](animated-characters-recognition.md)voor meer informatie.
* Meertalige identificatie (openbare preview)

    Detecteer segmenten in meerdere talen in het audiospoor en maak er een meertalige transcriptie op basis van. Eerste ondersteuning: Engels, Spaans, Duits en Frans. Zie [Meertalige inhoud automatisch identificeren en transcriberen](multi-language-identification-transcription.md)voor meer informatie.
* Benoemde entiteitsextractie voor Personen en locatie

    Haalt merken, locaties en mensen uit spraak en visuele tekst via natural language processing (NLP).
* Classificatie van type redactionele schot

    Tagging van foto's met redactionele types zoals close-up, medium shot, twee shot, binnen, outdoor etc. Zie [Typedetectie van redactionele schoten](scenes-shots-keyframes.md#editorial-shot-type-detection)voor meer informatie .
* Onderwerp inferencing enhancement - nu die niveau 2
    
    Het onderwerp inferencing model ondersteunt nu diepere granulariteit van de IPTC taxonomie. Lees alle details bij [Azure Media Services nieuwe AI-aangedreven innovatie.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>Augustus 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer ingezet in Verenigd Koninkrijk Zuid

U nu een betaalde video-indexeraccount aanmaken in de regio In het Verenigd Koninkrijk-Zuid.

### <a name="new-editorial-shot-type-insights-available"></a>Nieuwe Editorial Shot Type-inzichten beschikbaar

Nieuwe tags toegevoegd aan video shots biedt redactionele "shot types" om ze te identificeren met gemeenschappelijke redactionele zinnen die worden gebruikt in de content creatie workflow, zoals: extreme close-up, close-up, breed, medium, twee shot, outdoor, binnen, links gezicht en rechtergezicht (Beschikbaar in de JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Nieuwe entiteiten en locaties-extractie beschikbaar

Video Indexer identificeert benoemde locaties en mensen via natural language processing (NLP) uit de OCR en transcriptie van de video. Video Indexer gebruikt het machine learning-algoritme om te herkennen wanneer specifieke locaties (bijvoorbeeld de Eiffeltoren) of mensen (bijvoorbeeld John Doe) in een video worden opgeroepen.

### <a name="keyframes-extraction-in-native-resolution"></a>Hoofdframes extractie in native resolutie

Hoofdframes die door Video Indexer zijn geëxtraheerd, zijn beschikbaar in de oorspronkelijke resolutie van de video.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA voor het trainen van aangepaste gezichtsmodellen uit afbeeldingen

Trainingsgezichten van afbeeldingen die zijn verplaatst van de preview-modus naar GA (beschikbaar via API en in de portal).

> [!NOTE]
> Er is geen prijseffect in verband met de overgang 'Voorbeeld naar GA'.

### <a name="hide-gallery-toggle-option"></a>Optie Galerie-schakelen verbergen

De gebruiker kan ervoor kiezen om het tabblad galerij te verbergen voor de portal (vergelijkbaar met het verbergen van het tabblad voorbeelden).
 
### <a name="maximum-url-size-increased"></a>Maximale URL-grootte verhoogd

Ondersteuning voor URL-queryreeks van 4096 (in plaats van 2048) bij het indexeren van een video.
 
### <a name="support-for-multi-lingual-projects"></a>Steun voor meertalige projecten

Projecten kunnen nu worden gemaakt op basis van video's die in verschillende talen zijn geïndexeerd (alleen API).

## <a name="july-2019"></a>Juli 2019

### <a name="editor-as-a-widget"></a>Editor als widget

De Video Indexer AI-editor is nu beschikbaar als een widget die kan worden ingebed in klanttoepassingen.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aangepast taalmodel bijwerken vanuit het bestand met ondertiteling van de portal

Klanten kunnen VTT-, SRT- en TTML-bestandsindelingen leveren als invoer voor taalmodellen op de aanpassingspagina van de portal.

## <a name="june-2019"></a>Juni 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer ingezet in Japan East

U nu een betaalde video-indexeraccount aanmaken in de regio Japan-Oost.

### <a name="create-and-repair-account-api-preview"></a>API voor account maken en herstellen (voorbeeld)

Added a new API that enables you to [update the Azure Media Service connection endpoint or key.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)

### <a name="improve-error-handling-on-upload"></a>Foutafhandeling bij upload verbeteren 

Een beschrijvend bericht wordt geretourneerd in geval van verkeerde configuratie van het onderliggende Azure Media Services-account.

### <a name="player-timeline-keyframes-preview"></a>Voorbeeld van hoofdframes voor player-tijdlijn 

Je nu een afbeeldingsvoorbeeld zien voor elke keer op de tijdlijn van de speler.

### <a name="editor-semi-select"></a>Editor semi-selecteren

U nu een voorbeeld zien van alle inzichten die zijn geselecteerd als gevolg van het kiezen van een specifiek inzichtstijdsbestek in de editor.

## <a name="may-2019"></a>Mei 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aangepast taalmodel bijwerken vanuit het bestand met ondertiteling

[Aangepaste taalmodellen maken](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) en [aangepaste taalmodellen bijwerken](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API's ondersteunen nu VTT-, SRT- en TTML-bestandsindelingen als invoer voor taalmodellen.

Bij het aanroepen van de [UPDATE Video transcript API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)wordt het transcript automatisch toegevoegd. Het trainingsmodel dat aan de video is gekoppeld, wordt ook automatisch bijgewerkt. Zie [Een taalmodel aanpassen met videoindexer](customize-language-model-overview.md)voor informatie over het aanpassen en trainen van uw taalmodellen.

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nieuwe download transcript formaten - TXT en CSV

Naast de reeds ondersteunde ondertitelingsindeling (SRT, VTT en TTML) ondersteunt Video Indexer nu het downloaden van het transcript in TXT- en CSV-indelingen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](video-indexer-overview.md)
