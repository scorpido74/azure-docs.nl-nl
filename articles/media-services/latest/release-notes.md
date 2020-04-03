---
title: Notities voor v3-release van Azure Media Services | Microsoft Documenten
description: Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt dit artikel u de nieuwste updates over Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 76ef9f92b6b6633982242ccafab1950e0ef5f410
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582801"
---
# <a name="azure-media-services-v3-release-notes"></a>Notities voor v3-release van Azure Media Services

>Ontvang een melding over wanneer u deze pagina opnieuw moet `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` bezoeken voor updates door deze URL te kopiëren en te plakken: in uw RSS-feedlezer.

Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt dit artikel u informatie over:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="known-issues"></a>Bekende problemen

> [!NOTE]
> U de [Azure-portal](https://portal.azure.com/) gebruiken om v3 [Live Events](live-events-outputs-concept.md), bekijk [v3-assets](assets-concept.md), informatie te krijgen over toegang tot API's. Gebruik voor alle andere beheertaken (bijvoorbeeld Transformaties en Taken) de [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's.](media-services-apis-overview.md#sdks)

Zie [Migratierichtlijnen voor het overstappen van Media Services v2 naar v3](migrate-from-v2-to-v3.md#known-issues)voor meer informatie.
 
## <a name="january-2020"></a>Januari 2020

### <a name="improvements-in-media-processors"></a>Verbeteringen in mediaprocessors

- Verbeterde ondersteuning voor interlaced bronnen in Video Analysis - dergelijke inhoud is nu correct gedelaceerd voordat ze naar inference engines worden verzonden.
- Bij het genereren van miniaturen met de modus 'Beste' zoekt de encoder nu meer dan 30 seconden om een frame te selecteren dat niet monochromatisch is.

### <a name="azure-government-cloud-updates"></a>Cloudupdates van Azure Government

Media Services GA'ed in de volgende Azure Government regio's: *USGov Arizona* en *USGov Texas*.

## <a name="december-2019"></a>December 2019

CDN-ondersteuning toegevoegd voor *Origin-Assist Prefetch-headers* voor zowel live als video-on-demand streaming; beschikbaar voor klanten die een direct contract hebben met Akamai CDN. Origin-Assist CDN-Prefetch-functie omvat de volgende HTTP-headeruitwisselingen tussen Akamai CDN en Azure Media Services:

|HTTP-koptekst|Waarden|Afzender|Ontvanger|Doel|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1 (standaard) of 0 |CDN|Oorsprong|Cdn vooraf ophalen ingeschakeld|
|CDN-Origin-Assist-Prefetch-Path| Voorbeeld: <br/>Fragmenten(video=14000000000,format=mpd-time-cmaf)|Oorsprong|CDN|Voorhalen-pad naar CDN verstrekken|
|CDN-Origin-Assist-Prefetch-Request|1 (prefetch-aanvraag) of 0 (regulier verzoek)|CDN|Oorsprong|Het aangeven van het verzoek van CDN is een prefetch|

Als u een deel van de header-uitwisseling in actie wilt zien, u de volgende stappen proberen:

1. Gebruik Postman of curl om een verzoek uit te geven aan Media Services origin voor een audio- of videosegment of fragment. Zorg ervoor dat u de kop-CDN-Origin-Assist-Prefetch-Enabled: 1 toevoegt aan de aanvraag.
2. In het antwoord ziet u de header CDN-Origin-Assist-Prefetch-Path met een relatief pad als waarde.

## <a name="november-2019"></a>November 2019

### <a name="live-transcription-preview"></a>Voorbeeld van live transcriptie

Live transcriptie is nu in openbare preview en beschikbaar voor gebruik in de West US 2 regio.

Live transcriptie is ontworpen om te werken in combinatie met live gebeurtenissen als een add-on mogelijkheid.  Het wordt ondersteund op zowel pass-through en Standard of Premium codering live evenementen.  Wanneer deze functie is ingeschakeld, gebruikt de service de [spraak-naar-tekst-functie](../../cognitive-services/speech-service/speech-to-text.md) van Cognitive Services om de gesproken woorden in de binnenkomende audio in tekst te transcriberen. Deze tekst wordt vervolgens beschikbaar gesteld voor levering, samen met video en audio in MPEG-DASH en HLS protocollen. Facturering is gebaseerd op een nieuwe invoegmeter die extra kosten met zich meebrengt voor de live-gebeurtenis wanneer deze zich in de status 'Lopend' bevindt.  Zie [Live transcriptie](live-transcription.md) voor meer informatie over live transcriptie en facturering

> [!NOTE]
> Momenteel is live transcriptie alleen beschikbaar als voorbeeldfunctie in de regio West US 2. Het ondersteunt transcriptie van gesproken woorden in het Engels (en-ons) alleen op dit moment.

### <a name="content-protection"></a>Inhoudsbeveiliging

De *functie Token Replay Prevention* die in beperkte regio's in september is uitgebracht, is nu beschikbaar in alle regio's.
Media Services-klanten kunnen nu een limiet instellen op het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of een licentie aan te vragen. Zie [Token Replay Prevention](content-protection-overview.md#token-replay-prevention)voor meer informatie .

### <a name="new-recommended-live-encoder-partners"></a>Nieuwe aanbevolen live encoder partners

Ondersteuning toegevoegd voor de volgende nieuwe aanbevolen partnerencoders voor RTMP live streaming:

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 en Max actiecamera's](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Verbeteringen voor bestandscodering

- Er is nu een nieuwe voorinstelling voor contentbewuste codering beschikbaar. Het produceert een set van GOP-uitgelijnde MP4's met behulp van content-aware codering. Gezien alle invoerinhoud voert de service een eerste lichtgewicht analyse van de invoerinhoud uit. Het gebruikt deze resultaten om het optimale aantal lagen, de juiste bitsnelheid en resolutie-instellingen voor levering door adaptieve streaming te bepalen. Deze preset is bijzonder effectief voor video's met een lage complexiteit en gemiddelde complexiteit, waarbij de uitvoerbestanden lagere bitsnelheden hebben, maar een kwaliteit die kijkers nog steeds een goede ervaring biedt. De uitvoer bevat MP4-bestanden met video en audio interleaved. Zie voor meer informatie de [open API-specificaties](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Verbeterde prestaties en multi-threading voor de re-sizer in Standard Encoder. Onder specifieke omstandigheden moet de klant een prestatieverbetering zien tussen 5-40% VOD-codering. Lage complexiteitsinhoud gecodeerd in meerdere bitsnelheden zal de hoogste prestatiestijgingen zien. 
- Standaardcodering behoudt nu een normale GOP-cadans voor vfr-inhoud (variable frame rate) tijdens VOD-codering bij het gebruik van de op tijd gebaseerde GOP-instelling.  Dit betekent dat de klant het indienen van gemengde frame rate inhoud die varieert tussen 15-30 fps bijvoorbeeld moet nu zien regelmatige GOP afstanden berekend op output naar adaptieve bitrate streaming MP4-bestanden. Dit zal de mogelijkheid om naadloos te schakelen tussen tracks bij het leveren via HLS of DASH verbeteren. 
-  Verbeterde AV-synchronisatie voor vfr-broninhoud (variable frame rate)

### <a name="video-indexer-video-analytics"></a>Video-indexer, video-analyse

- Hoofdframes die zijn geëxtraheerd met de voorinstelling VideoAnalyzer, bevinden zich nu in de oorspronkelijke resolutie van de video in plaats van het formaat te worden aangepast. Hoge resolutie keyframe extractie geeft u originele kwaliteit beelden en u gebruik maken van de beeld-gebaseerde kunstmatige intelligentie modellen die door de Microsoft Computer Vision en Custom Vision diensten om nog meer inzichten uit uw video te krijgen.

## <a name="september-2019"></a>September 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Live lineaire codering van live-evenementen

Media Services v3 kondigt de preview aan van 24 uur x 365 dagen live lineaire codering van live-evenementen.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Afschaffing van mediaverwerkers

We kondigen afschaffing van *Azure Media Indexer* en *Azure Media Indexer 2 Preview*aan. Zie voor de pensioendatums het onderwerp [oudere onderdelen.](../previous/legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze oudere mediaprocessors.

Zie [Migreren van Azure Media Indexer en Azure Media Indexer 2 naar Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md)voor meer informatie.

## <a name="august-2019"></a>Augustus 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Zuid-Afrika regionale paar staat open voor Media Services 

Media Services is nu beschikbaar in Zuid-Afrika Noord-en Zuid-Afrika West regio's.

Zie [Wolken en regio's waarin Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie .

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Afschaffing van mediaverwerkers

We kondigen de afschaffing aan van de *Media-processors van Windows Azure Media Encoder* (WAME) en Azure Media *Encoder* (AME), die worden teruggetrokken. Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](../previous/legacy-components.md)

Zie [WAME migreren naar Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) en [Migreren naar Media Encoder Standard voor](https://go.microsoft.com/fwlink/?LinkId=2101335)meer informatie.
 
## <a name="july-2019"></a>Juli 2019

### <a name="content-protection"></a>Inhoudsbeveiliging

Wanneer inhoud wordt beschermd met tokenbeperking, moeten eindgebruikers een token verkrijgen dat wordt verzonden als onderdeel van het verzoek om de sleutelte levering. Met de functie *Token Replay Prevention* kunnen mediaservices-klanten een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of een licentie aan te vragen. Zie [Token Replay Prevention](content-protection-overview.md#token-replay-prevention)voor meer informatie .

Vanaf juli was de preview-functie alleen beschikbaar in US Central en US West Central.

## <a name="june-2019"></a>Juni 2019

### <a name="video-subclipping"></a>Videosubclipping

U nu een video bijsnijden of subclipen wanneer u deze codeert met een [taak.](https://docs.microsoft.com/rest/api/media/jobs) 

Deze functionaliteit werkt met elke [transformatie](https://docs.microsoft.com/rest/api/media/transforms) die is gebouwd met de [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) presets of de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) presets. 

Zie voorbeelden:

* [Een video onderknippen met .NET](subclip-video-dotnet-howto.md)
* [Een video onderknippen met REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Mei 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor-ondersteuning voor diagnostische logboeken en statistieken van Media Services

U Azure Monitor nu gebruiken om telemetriegegevens van Media Services weer te geven.

* Gebruik de diagnostische logboeken van Azure Monitor om aanvragen te controleren die zijn verzonden door het eindpunt van de hoofdweergave van de sleutelvan Media Services. 
* Monitor statistieken die worden uitgezonden door [streamingeindpunten van](streaming-endpoint-concept.md)Media Services .   

Zie Statistieken [van Monitor Media Services en diagnostische logboeken voor](media-services-metrics-diagnostic-logs.md)meer informatie .

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Ondersteuning voor multiaudiotracks in Dynamic Packaging 

Wanneer u assets streamt met meerdere audiotracks met meerdere codecs en talen, ondersteunt [Dynamic Packaging](dynamic-packaging-overview.md) nu multi-audiotracks voor de HLS-uitvoer (versie 4 of hoger).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea regionale paar staat open voor Media Services 

Media Services is nu beschikbaar in Korea Centraal en Korea Zuid regio's. 

Zie [Wolken en regio's waarin Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie .

### <a name="performance-improvements"></a>Prestatieverbeteringen

Updates toegevoegd met verbeteringen in de prestaties van Media Services.

* De maximale bestandsgrootte die wordt ondersteund voor verwerking is bijgewerkt. Zie, [Quota en limieten](limits-quotas-constraints.md).
* [Codering versnelt verbeteringen](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Nieuwe voorinstellingen

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) is toegevoegd aan de ingebouwde analyzer presets.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) is toegevoegd aan de ingebouwde encoder presets. Zie [Inhoudsbewuste codering](content-aware-encoding.md)voor meer informatie. 

## <a name="march-2019"></a>Maart 2019

Dynamic Packaging ondersteunt nu Dolby Atmos. Zie [Audiocodecs ondersteund door dynamische verpakkingen voor](dynamic-packaging-overview.md#audio-codecs)meer informatie.

U nu een lijst met activa- of accountfilters opgeven, die van toepassing zou zijn op uw streaminglocator. Zie [Filters koppelen aan Streaming Locator voor](filters-concept.md#associating-filters-with-streaming-locator)meer informatie.

## <a name="february-2019"></a>Februari 2019

Media Services v3 wordt nu ondersteund in nationale azureclouds. Nog niet alle functies zijn beschikbaar in alle clouds. Zie Clouds [en regio's waarin Azure Media Services v3 bestaat](azure-clouds-regions.md)voor meer informatie .

De gebeurtenis [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) is toegevoegd aan de Azure Event Grid-schema's voor Media Services.

## <a name="january-2019"></a>Januari 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard- en MPI-bestanden 

Wanneer u codeert met Media Encoder Standard om MP4-bestanden te produceren, wordt een nieuw .mpi-bestand gegenereerd en toegevoegd aan de uitvoerasset. Dit MPI-bestand is bedoeld om de prestaties voor [dynamische verpakkings-](dynamic-packaging-overview.md) en streamingscenario's te verbeteren.

U mag het MPI-bestand niet wijzigen of verwijderen of enige afhankelijkheid in uw service nemen van het al dan niet bestaan van een dergelijk bestand.

## <a name="december-2018"></a>December 2018

Updates van de GA-release van de V3 API zijn:
       
* De eigenschappen **van PresentationTimeRange** zijn niet langer 'vereist' voor **assetfilters** en **accountfilters**. 
* De $top- en $skip queryopties voor **taken** en **transformaties** zijn verwijderd en $orderby is toegevoegd. Als onderdeel van het toevoegen van de nieuwe bestelfunctionaliteit, werd ontdekt dat de $top en $skip opties per ongeluk eerder waren blootgesteld, ook al zijn ze niet geïmplementeerd.
* Uitbreidbaarheid van opsommingen werd opnieuw ingeschakeld. Deze functie is ingeschakeld in de preview-versies van de SDK en werd per ongeluk uitgeschakeld in de GA-versie.
* Twee vooraf gedefinieerde streamingbeleidsregels zijn hernoemd. **SecureStreaming** is nu **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** is nu **Predefined_MultiDrmStreaming.**

## <a name="november-2018"></a>November 2018

De CLI 2.0-module is nu beschikbaar voor [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nieuwe opdrachten

- [az ams-account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - stelt u in staat om Media Reserved Units te beheren. Zie [Gereserveerde eenheden schalen voor](media-reserved-units-cli-how-to.md)meer informatie .

### <a name="new-features-and-breaking-changes"></a>Nieuwe functies en baanbrekende wijzigingen

#### <a name="asset-commands"></a>Opdrachten voor activa

- ```--storage-account```en ```--container``` argumenten toegevoegd.
- Standaardwaarden voor vervaldatum (Nu+23h) en machtigingen (Lees) in ```az ams asset get-sas-url``` opdracht toegevoegd.

#### <a name="job-commands"></a>Opdrachten voor taak

- ```--correlation-data```en ```--label``` argumenten toegevoegd
- ```--output-asset-names```hernoemd ```--output-assets```naar . Nu accepteert het een ruimte-gescheiden lijst van activa in 'assetName=label' formaat. Een asset zonder label kan als volgt worden verzonden: 'assetName='.

#### <a name="streaming-locator-commands"></a>Opdrachten voor streaminglocator

- ```az ams streaming locator```basisopdracht vervangen ```az ams streaming-locator```door .
- ```--streaming-locator-id```en ```--alternative-media-id support``` argumenten toegevoegd.
- ```--content-keys argument```argument bijgewerkt.
- ```--content-policy-name```hernoemd ```--content-key-policy-name```naar .

#### <a name="streaming-policy-commands"></a>Opdrachten voor streamingbeleid

- ```az ams streaming policy```basisopdracht vervangen ```az ams streaming-policy```door .
- Ondersteuning voor ```az ams streaming-policy create``` versleutelingsparameters in added.

#### <a name="transform-commands"></a>Opdrachten transformeren

- ```--preset-names```argument vervangen ```--preset```door . Nu u slechts 1 uitvoer/voorinstelling tegelijk instellen (om meer toe te voegen moet u uitvoeren). ```az ams transform output add``` U ook aangepaste StandardEncoderPreset instellen door het pad door te geven aan uw aangepaste JSON.
- ```az ams transform output remove```kan worden uitgevoerd door de uitvoerindex door te geven om te verwijderen.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argumenten toegevoegd ```az ams transform create``` ```az ams transform output add``` aan en opdrachten.

## <a name="october-2018---ga"></a>Oktober 2018 - GA

In deze sectie worden updates van Azure Media Services (AMS) oktober beschreven.

### <a name="rest-v3-ga-release"></a>REST v3 GA release

De [REST v3 GA-release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) bevat meer API's voor manifestfilters op account-, account-/assetniveau en DRM-ondersteuning.

#### <a name="azure-resource-management"></a>Azure ResourceBeheer 

Ondersteuning voor Azure Resource Management maakt unified management en operations API mogelijk (nu alles op één plek).

Vanaf deze release u Resource Manager-sjablonen gebruiken om Live-evenementen te maken.

#### <a name="improvement-of-asset-operations"></a>Verbetering van de activaactiviteiten 

De volgende verbeteringen werden geïntroduceerd:

- Inname van HTTP-URL's of Azure Blob Storage SAS-URL's.
- Geef u zelf containernamen op voor Assets. 
- Eenvoudigere uitvoerondersteuning voor het maken van aangepaste werkstromen met Azure-functies.

#### <a name="new-transform-object"></a>Nieuw object Transformeren

Het nieuwe object **Transform** vereenvoudigt het coderingsmodel. Het nieuwe object maakt het eenvoudig om coderingssjablonen en voorinstellingen te maken en te delen. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory-verificatie en RBAC

Azure AD-verificatie en op rollen gebaseerde toegangscontrole (RBAC) maken veilige transformaties, LiveEvents, Content Key Policies of Assets per rol of gebruikers in Azure AD mogelijk.

#### <a name="client-sdks"></a>Client-SDK 's  

Talen die worden ondersteund in Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Live coderingsupdates

De volgende live coderingsupdates worden geïntroduceerd:

- Nieuwe lage latentiemodus voor live (10 seconden end-to-end).
- Verbeterde RTMP-ondersteuning (meer stabiliteit en meer ondersteuning voor bronencoder).
- RTMPS veilig inname.

    Wanneer u een live-evenement maakt, krijgt u nu 4 inname-URL's. De 4 inname URL's zijn bijna identiek, hebben dezelfde streaming token (AppId), alleen het poortnummer deel is anders. Twee van de URL's zijn primair en back-up voor RTMPS. 
- Ondersteuning voor 24-uurs transcodering. 
- Verbeterde ondersteuning voor ad-signalering in RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Ondersteuning voor verbeterde gebeurtenisgrid

U de volgende verbeteringen voor de ondersteuning van het gebeurtenisraster bekijken:

- Azure Event Grid-integratie voor eenvoudigere ontwikkeling met Logic Apps en Azure-functies. 
- Abonneer je op evenementen op Encoding, Live Channels en meer.

### <a name="cmaf-support"></a>CMAF-ondersteuning

CMAF- en 'cbcs'-encryptieondersteuning voor Apple HLS (iOS 11+) en MPEG-DASH-spelers die CMAF ondersteunen.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA release werd aangekondigd in augustus. Zie [Wat is Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)voor nieuwe informatie over momenteel ondersteunde functies. 

### <a name="plans-for-changes"></a>Plannen voor wijzigingen

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
De Azure CLI 2.0-module die bewerkingen bevat voor alle functies (inclusief Live, Content Key Policies, Account/Asset Filters, Streaming Policies) komt binnenkort. 

### <a name="known-issues"></a>Bekende problemen

Alleen klanten die de preview-API voor Asset- of AccountFilters hebben gebruikt, worden beïnvloed door het volgende probleem.

Als u activa- of accountfilters hebt gemaakt tussen 09/28 en 10/12 met Media Services v3 CLI of API's, moet u alle asset- en accountfilters verwijderen en opnieuw maken vanwege een versieconflict. 

## <a name="may-2018---preview"></a>Mei 2018 - Preview

### <a name="net-sdk"></a>.NET SDK

De volgende functies zijn aanwezig in de .NET SDK:

* **Transformeert** en **taken** om media-inhoud te coderen of te analyseren. Zie Bijvoorbeeld [Bestanden streamen](stream-files-tutorial-with-api.md) en [analyseren](analyze-videos-tutorial-with-api.md).
* **Streaming Locators** voor het publiceren en streamen van content naar apparaten van eindgebruikers
* **Streamingbeleid** en **beleid voor inhoudssleutel** om de belangrijkste weergave- en inhoudsbescherming (DRM) te configureren bij het leveren van inhoud.
* **Live-evenementen** en **live-uitvoer** om de inname en archivering van live streaming-inhoud te configureren.
* **Elementen** voor het opslaan en publiceren van media-inhoud in Azure Storage. 
* **Streaming Endpoints** voor het configureren en schalen van dynamische verpakkingen, versleuteling en streaming voor zowel live als on-demand mediacontent.

### <a name="known-issues"></a>Bekende problemen

* Wanneer u een taak indient, u opgeven om uw bronvideo in te nemen met HTTPS-URL's, SAS-URL's of paden naar bestanden in Azure Blob-opslag. Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht](media-services-overview.md)
- [Media Services v2 release notes](../previous/media-services-release-notes.md)
