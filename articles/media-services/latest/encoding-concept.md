---
title: Video en audio coderen met Media Services
titleSuffix: Azure Media Services
description: In dit artikel wordt uitgelegd over het coderen van video en audio met Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366581"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Video en audio coderen met Media Services

De term codering in Media Services is van toepassing op het proces van het converteren van bestanden die digitale video en/of audio bevatten van het ene standaardformaat naar het andere, met als doel (a) de grootte van de bestanden te verkleinen en/of (b) een formaat te produceren dat compatibel is met een breed scala aan apparaten en apps. Dit proces wordt ook wel videocompressie of transcodering genoemd. Zie de [gegevens compressie](https://en.wikipedia.org/wiki/Data_compression) en de Wat is codering [en transcodering?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) voor verdere bespreking van de concepten.

Video's worden meestal geleverd aan apparaten en apps door [progressieve download](https://en.wikipedia.org/wiki/Progressive_download) of via [adaptieve bitrate streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Media Services factureert geen geannuleerde of foutibte taken. Een taak die bijvoorbeeld 50% voortgang heeft bereikt en wordt geannuleerd, wordt niet gefactureerd op 50% van de werkminuten. Er worden alleen kosten in rekening gebracht voor voltooide taken.

* Als u wilt leveren door progressief te downloaden, u Azure Media Services gebruiken om een bestand voor digitale media (mezzanine) om te zetten in een [MP4-bestand,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) dat video bevat die is gecodeerd met de [H.264-codec](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) en audio die is gecodeerd met de [OC-codec.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Dit MP4-bestand is geschreven naar een asset in uw opslagaccount. U de Azure Storage API's of SDK's (bijvoorbeeld [Storage REST API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)gebruiken om het bestand rechtstreeks te downloaden. Als u het uitvoeritem hebt gemaakt met een specifieke containernaam in de opslag, gebruikt u die locatie. Anders u Media Services gebruiken om [de URL's van de assetcontainer weer te geven.](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) 
* Om inhoud voor te bereiden op levering door adaptieve bitrate streaming, moet het mezzaninebestand worden gecodeerd bij meerdere bitrates (van hoog naar laag). Om een sierlijke overgang van kwaliteit te garanderen, wordt de resolutie van de video verlaagd naarmate de bitrate wordt verlaagd. Dit resulteert in een zogenaamde coderingladder– een tabel met resoluties en bitrates (zie [automatisch gegenereerde adaptieve bitrate ladder).](autogen-bitrate-ladder.md) U Media Services gebruiken om uw mezzaninebestanden op meerdere bitrates te coderen. Zo krijgt u een set MP4-bestanden en bijbehorende streamingconfiguratiebestanden die naar een asset worden geschreven in uw opslagaccount. U vervolgens de [Dynamische verpakkingsmogelijkheid](dynamic-packaging-overview.md) in Media Services gebruiken om de video te leveren via streamingprotocollen zoals [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) en [HLS.](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) Hiervoor moet u een [Streaming Locator](streaming-locators-concept.md) maken en streaming URL's bouwen die overeenkomen met de ondersteunde protocollen, die vervolgens kunnen worden overgedragen aan apparaten/apps op basis van hun mogelijkheden.

In het volgende diagram ziet u de workflow voor on-demand codering met dynamische verpakking.

![Workflow voor on-demand codering met dynamische verpakking](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Dit onderwerp geeft u richtlijnen over hoe u uw inhoud coderen met Media Services v3.

## <a name="transforms-and-jobs"></a>Transformaties en taken

Als u wilt coderen met Media Services v3, moet u een [transformatie](https://docs.microsoft.com/rest/api/media/transforms) en een [taak maken.](https://docs.microsoft.com/rest/api/media/jobs) De transformatie definieert een recept voor uw coderinginstellingen en -uitgangen; de taak is een voorbeeld van het recept. Zie [Transformeren en taken voor](transforms-jobs-concept.md)meer informatie.

Wanneer u codeert met Media Services, gebruikt u voorinstellingen om de encoder te vertellen hoe de invoermediabestanden moeten worden verwerkt. In Media Services v3 gebruikt u Standard Encoder om uw bestanden te coderen. U bijvoorbeeld de videoresolutie en/of het gewenste aantal audiokanalen opgeven in de gecodeerde inhoud.

U snel aan de slag met een van de aanbevolen ingebouwde voorinstellingen op basis van best practices in de branche of u ervoor kiezen om een aangepaste voorinstelling te bouwen om uw specifieke scenario- of apparaatvereisten te targeten. Zie [Coderen met een aangepaste transformatie](customize-encoder-presets-how-to.md)voor meer informatie.

Vanaf januari 2019, wanneer u codeert met de Standaardencoder om MP4-bestand(en) te produceren, wordt een nieuw .mpi-bestand gegenereerd en toegevoegd aan de uitvoerasset. Dit MPI-bestand is bedoeld om de prestaties voor [dynamische verpakkings-](dynamic-packaging-overview.md) en streamingscenario's te verbeteren.

> [!NOTE]
> U moet het MPI-bestand niet wijzigen of verwijderen of enige afhankelijkheid in uw service nemen van het al dan niet bestaan van een dergelijk bestand.

### <a name="creating-job-input-from-an-https-url"></a>Taakinvoer maken vanuit een HTTPS-URL

Wanneer je Vacatures indient om je video's te verwerken, moet je Media Services vertellen waar je de invoervideo vinden. Een van de opties is het opgeven van een HTTPS-URL als taakinvoer. Momenteel ondersteunt Media Services v3 geen chunked transfer codering via HTTPS URL's.

#### <a name="examples"></a>Voorbeelden

* [Coderen vanaf een HTTPS-URL met .NET](stream-files-dotnet-quickstart.md)
* [Coderen vanaf een HTTPS-URL met REST](stream-files-tutorial-with-rest.md)
* [Coderen vanaf een HTTPS-URL met CLI](stream-files-cli-quickstart.md)
* [Coderen vanaf een HTTPS-URL met Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Taakinvoer maken vanuit een lokaal bestand

De invoervideo kan worden opgeslagen als Media Service-asset, in welk geval u een invoerasset maakt op basis van een bestand (lokaal opgeslagen of in Azure Blob-opslag).

#### <a name="examples"></a>Voorbeelden

[Een lokaal bestand coderen met ingebouwde voorinstellingen](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Taakinvoer maken met subclipping

Wanneer u een video codeert, u opgeven om ook het bronbestand bij te snijden of te knippen en een uitvoer te produceren die slechts een gewenst gedeelte van de invoervideo heeft. Deze functionaliteit werkt met elke [transformatie](https://docs.microsoft.com/rest/api/media/transforms) die is gebouwd met de [BuiltInStandardEncoderPreset-voorinstellingen](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) of de [StandardEncoderPreset-voorinstellingen.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

U opgeven om een [taak](https://docs.microsoft.com/rest/api/media/jobs/create) te maken met één clip van een video on-demand of live archief (een opgenomen gebeurtenis). De taakinvoer kan een asset of een HTTPS-URL zijn.

> [!TIP]
> Als je een sublip van je video wilt streamen zonder de video opnieuw te coderen, kun je overwegen [pre-filtering manifesten te gebruiken met Dynamic Packager.](filters-dynamic-manifest-overview.md)

#### <a name="examples"></a>Voorbeelden

Zie voorbeelden:

* [Een video onderknippen met .NET](subclip-video-dotnet-howto.md)
* [Een video onderknippen met REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Ingebouwde voorinstellingen

Media Services ondersteunt de volgende ingebouwde coderingsvoorinstellingen:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) wordt gebruikt om een ingebouwde voorinstelling in te stellen voor het coderen van de invoervideo met de Standaard Encoder.

De volgende voorinstellingen worden momenteel ondersteund:

- **EncoderNamedPreset.AACGoodQualityAudio:** produceert één MP4-bestand met alleen stereoaudio gecodeerd bij 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (aanbevolen): Voor meer informatie, zie [automatisch genereren van een bitrate ladder](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: onthult een experimentele preset voor contentbewuste codering. Gezien alle invoerinhoud probeert de service automatisch het optimale aantal lagen te bepalen en de juiste bitrate- en resolutie-instellingen voor levering door adaptieve streaming. De onderliggende algoritmen zullen in de loop van de tijd blijven evolueren. De uitvoer bevat MP4-bestanden met video en audio interleaved. Zie [Experimentele voorinstelling voor inhoudsbewuste codering voor](content-aware-encoding.md)meer informatie.
- **EncoderNamedPreset.H264MultipleBitrate1080p**: produceert een set van acht GOP-aligned MP4-bestanden, variërend van 6000 kbps tot 400 kbps, en stereo OC-audio. Resolutie begint bij 1080p en gaat naar beneden naar 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: produceert een set van zes GOP-aligned MP4-bestanden, variërend van 3400 kbps tot 400 kbps en stereo OC-audio. Resolutie begint bij 720p en gaat naar beneden naar 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: produceert een set van vijf GOP-aligned MP4-bestanden, variërend van 1600 kbps tot 400 kbps en stereo OC-audio. Resolutie begint bij 480p en gaat naar beneden naar 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: produceert een MP4-bestand waarbij de video is gecodeerd met H.264-codec bij 6750 kbps en een beeldhoogte van 1080 pixels, en de stereoaudio is gecodeerd met AAC-LC-codec bij 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: produceert een MP4-bestand waarbij de video is gecodeerd met H.264-codec bij 4500 kbps en een beeldhoogte van 720 pixels, en de stereoaudio is gecodeerd met AAC-LC-codec bij 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD**: produceert een MP4-bestand waarbij de video is gecodeerd met H.264-codec bij 2200 kbps en een beeldhoogte van 480 pixels, en de stereoaudio is gecodeerd met AAC-LC-codec bij 64 kbps.

Zie [ingebouwde voorinstellingen die moeten worden gebruikt voor het coderen van video's](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)om de lijst met meest up-to-date voorinstellingen te bekijken.

Zie [Bestanden uploaden, coderen en streamen](stream-files-tutorial-with-api.md)om te zien hoe de voorinstellingen worden gebruikt.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) beschrijft instellingen die moeten worden gebruikt bij het coderen van de invoervideo met de Standaard encoder. Gebruik deze voorinstelling bij het aanpassen van transformatievoorinstellingen.

#### <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voorinstellingen zijn de volgende overwegingen van toepassing:

- Alle waarden voor hoogte en breedte op AVC-inhoud moeten een veelvoud van vier zijn.
- In Azure Media Services v3 zijn alle coderingsbitrates in bits per seconde. Dit is anders dan de presets met onze v2 API's, die kilobits per seconde als eenheid gebruikten. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

### <a name="customizing-presets"></a>Voorinstellingen aanpassen

Media Services ondersteunt volledig het aanpassen van alle waarden in voorinstellingen om aan uw specifieke coderingsbehoeften en -vereisten te voldoen. Zie de onderstaande lijst voor voorbeelden die laten zien hoe u voorinstellingen voor encoder aanpassen:

#### <a name="examples"></a>Voorbeelden

- [Voorinstellingen aanpassen met .NET](customize-encoder-presets-how-to.md)
- [Voorinstellingen aanpassen met CLI](custom-preset-cli-howto.md)
- [Voorinstellingen aanpassen met REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Vooraf ingesteld schema

In Media Services v3 zijn voorinstellingen sterk getypte entiteiten in de API zelf. U de definitie 'schema' voor deze objecten vinden in [Open API Specification (of Swagger).](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) U ook de vooraf ingestelde definities (zoals **StandardEncoderPreset)** bekijken in de [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (of andere Media Services v3 SDK-referentiedocumentatie).

## <a name="scaling-encoding-in-v3"></a>Codering schalen in v3

Zie Schalen met [CLI](media-reserved-units-cli-how-to.md)als u mediaverwerking wilt schalen.

## <a name="billing"></a>Billing

Media Services factureert geen geannuleerde of foutibte taken. Een taak die bijvoorbeeld 50% voortgang heeft bereikt en wordt geannuleerd, wordt niet gefactureerd op 50% van de werkminuten. Er worden alleen kosten in rekening gebracht voor voltooide taken.

Ga voor meer informatie naar het overzicht van [prijzen](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* [Uploaden, coderen en streamen met Media Services](stream-files-tutorial-with-api.md).
* [Coderen vanuit een HTTPS-URL met ingebouwde voorinstellingen](job-input-from-http-how-to.md).
* [Codeer een lokaal bestand met ingebouwde voorinstellingen.](job-input-from-local-file-how-to.md)
* [Maak een aangepaste voorinstelling om uw specifieke scenario- of apparaatvereisten te targeten.](customize-encoder-presets-how-to.md)
