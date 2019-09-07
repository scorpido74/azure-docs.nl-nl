---
title: Codering in de cloud met Media Services - Azure | Microsoft Docs
description: In dit onderwerp beschrijft het coderingsproces bij het gebruik van Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 590b84acd57199b291aa44f7120507023ffd026a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389757"
---
# <a name="encoding-with-media-services"></a>Codering met mediaservices

De term encoding in Media Services is van toepassing op het proces van het converteren van bestanden met digitale video en/of audio van de ene standaard indeling naar een andere, met het doel van (a) om de grootte van de bestanden te reduceren en/of (b) die een indeling produceert die compatibel is met een breed scala aan apparaten en toepassingen. Dit proces wordt ook wel video compressie of transcode ring genoemd. Zie [gegevens compressie](https://en.wikipedia.org/wiki/Data_compression) en [Wat is code ring en trans codering?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) voor verdere bespreking van de concepten.

Video's worden meestal aangeboden aan apparaten en toepassingen door middel van [progressief downloaden](https://en.wikipedia.org/wiki/Progressive_download) of via [Adaptive bitrate streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Voor het leveren van een progressieve down load kunt u Azure Media Services gebruiken om een digitaal media bestand (mezzanine) te converteren naar een [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) -bestand, dat video bevat die is gecodeerd met de [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) -codec en audio die is gecodeerd met de [AAC ](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)codec. Dit MP4-bestand wordt naar een asset in uw opslag account geschreven. U kunt de Azure Storage Api's of Sdk's (bijvoorbeeld [Storage rest API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) gebruiken om het bestand rechtstreeks te downloaden. Als u het uitvoer activum hebt gemaakt met een specifieke container naam in Storage, gebruikt u die locatie. Als dat niet het geval is, kunt u Media Services gebruiken om [de url's van de Asset-container weer te geven](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Om inhoud voor te bereiden voor levering door Adaptive Bitrate Streaming, moet het mezzanine-bestand worden gecodeerd op meerdere bitsnelheden (hoog naar laag). Om te zorgen voor een correcte overgang van kwaliteit, naarmate de bitsnelheid lager is, is dit de resolutie van de video. Dit resulteert in een zogenaamde coderings ladder, een tabel met resoluties en bitrates (zie een [automatisch gegenereerde, Adaptive bitrate ladder](autogen-bitrate-ladder.md)). U kunt Media Services gebruiken om uw mezzanine-bestanden op meerdere bitsnelheden te coderen. in dat geval krijgt u een set MP4-bestanden en bijbehorende streaming-configuratie bestanden, geschreven naar een asset in uw opslag account. U kunt vervolgens de [dynamische verpakkings](dynamic-packaging-overview.md) functie in Media Services gebruiken om de video via streaming protocollen zoals [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) en [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)te leveren. Hiervoor moet u een streaming- [Locator](streaming-locators-concept.md) maken en streaming-url's bouwen die overeenkomen met de ondersteunde protocollen. deze kunnen vervolgens worden door gegeven aan apparaten/toepassingen op basis van hun mogelijkheden.

In het volgende diagram ziet u de werk stroom voor code ring op aanvraag met dynamische verpakking.

![Dynamische verpakking](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

In dit onderwerp vindt u richtlijnen over hoe u uw inhoud codeert met Media Services v3.

## <a name="transforms-and-jobs"></a>Transformaties en taken

Als u wilt coderen met Media Services v3, moet u een [trans formatie](https://docs.microsoft.com/rest/api/media/transforms) en een [taak](https://docs.microsoft.com/rest/api/media/jobs)maken. De trans formatie definieert een recept voor uw coderings instellingen en-uitvoer. de taak is een exemplaar van het recept. Zie voor meer informatie, [transformaties en taken](transforms-jobs-concept.md)

Wanneer de codering met Media Services, gebruikt u voorinstellingen voor het coderingsprogramma vertellen hoe de invoer media-bestanden moeten worden verwerkt. U kunt bijvoorbeeld de video oplossing en/of het aantal audio kanalen die u wilt opgeven in de gecodeerde inhoud. 

U kunt snel aan de slag met een van de aanbevolen ingebouwde voorinstellingen op basis van aanbevolen procedures voor de bedrijfstak of u kunt kiezen om het bouwen van een aangepaste voorinstelling wilt richten op uw specifieke vereisten voor scenario of het apparaat. Zie voor meer informatie, [coderen met een aangepaste transformatie](customize-encoder-presets-how-to.md). 

Met ingang van 1 januari 2019 tijdens het coderen met Media Encoder Standard voor het maken van MP4-bestand (en), wordt er een nieuw. mpi-bestand gegenereerd dat wordt toegevoegd aan de uitvoer Asset. Dit MPI-bestand is bedoeld om de prestaties te verbeteren voor [dynamische pakket](dynamic-packaging-overview.md) -en streaming-scenario's.

> [!NOTE]
> U moet het MPI-bestand niet wijzigen of verwijderen of afhankelijk van de aanwezigheid (of niet) van een dergelijk bestand afnemen in uw service.

### <a name="creating-job-input-from-an-https-url"></a>Taak invoer maken op basis van een HTTPS-URL

Wanneer u taken voor het verwerken van uw Video's verzendt, moet u Media Services waar u de invoer video kunt vinden. Een van de opties is om een HTTPS-URL op te geven als een taak invoer. Media Services V3 biedt momenteel geen ondersteuning voor gesegmenteerde overdrachts codering via HTTPS-Url's. 

#### <a name="examples"></a>Voorbeelden

* [Coderen van een HTTPS-URL met .NET](stream-files-dotnet-quickstart.md)
* [Coderen van een HTTPS-URL met REST](stream-files-tutorial-with-rest.md)
* [Coderen van een HTTPS-URL met CLI](stream-files-cli-quickstart.md)
* [Coderen van een HTTPS-URL met behulp van node. js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Taak invoer maken op basis van een lokaal bestand

De invoer video kan worden opgeslagen als een media service-Asset. in dat geval kunt u een invoer element maken op basis van een bestand (lokaal opgeslagen of in Azure Blob-opslag). 

#### <a name="examples"></a>Voorbeelden

[Een lokaal bestand coderen met ingebouwde voor instellingen](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Taak invoer met subknipsel maken

Bij het coderen van een video kunt u opgeven dat u het bron bestand ook wilt knippen of knippen en een uitvoer wilt genereren die alleen een gedeelte van de invoer video bevat. Deze functionaliteit werkt met elke [trans formatie](https://docs.microsoft.com/rest/api/media/transforms) die is gebouwd met behulp van de [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -voor instellingen of de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) -voor waarden. 

U kunt opgeven om een [taak](https://docs.microsoft.com/rest/api/media/jobs/create) te maken met één clip op aanvraag of Live Archive (een vastgelegde gebeurtenis). De taak invoer kan een activa-of een HTTPS-URL zijn.

> [!TIP]
> Als u een sublip van uw video wilt streamen zonder de video opnieuw te coderen, kunt u overwegen om voor het gebruiken van het [vooraf filteren van manifesten met dynamische pakket](filters-dynamic-manifest-overview.md)functie.

#### <a name="examples"></a>Voorbeelden

Zie voor beelden:

* [Een video met .NET knippen](subclip-video-dotnet-howto.md)
* [Een video met REST samenknippen](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Ingebouwde voorinstellingen

Media Services ondersteunt momenteel de volgende ingebouwde coderingsstandaarden:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) wordt gebruikt voor het instellen van een ingebouwde voor instelling voor het coderen van de invoer video met de standaard encoder. 

De volgende voorinstellingen worden momenteel ondersteund:

- **EncoderNamedPreset.AACGoodQualityAudio** -produceert één MP4-bestand met alleen stereo audio gecodeerd met een 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (aanbevolen). Zie voor meer informatie, [automatisch een bitrateladder genereren](autogen-bitrate-ladder.md).
- **EncoderNamedPreset. ContentAwareEncodingExperimental** : beschrijft een experimentele voor instelling voor code ring met inhoud. Op basis van de invoer inhoud probeert de service automatisch het optimale aantal lagen, de juiste bitrate en resolutie-instellingen te bepalen voor levering door adaptieve streaming. De onderliggende algoritmen blijven in de loop van de tijd worden uitgevoerd. De uitvoer bevat MP4-bestanden met Interleaved video-en audio-indeling. Zie voor meer informatie [experimentele voor instelling voor code ring met inhoud](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** -produceert een reeks van 8 GOP uitgelijnde MP4-bestanden, variërend van 6000 kbps tot 400 kbps en stereo AAC-audio. Resolutie begint bij 1080p en gaat naar de 360 p.
- **EncoderNamedPreset.H264MultipleBitrate720p** -produceert een set 6 GOP uitgelijnde MP4-bestanden, variërend van 3400 kbps tot 400 kbps en stereo AAC-audio. Resolutie begint bij 720p en gaat naar de 360 p.
- **EncoderNamedPreset.H264MultipleBitrateSD** -produceert een reeks van 5 GOP uitgelijnde MP4-bestanden, variërend van 1600 kbps tot 400 kbps en stereo AAC-audio. Resolutie begint bij 480p en gaat naar de 360 p.
- **EncoderNamedPreset. H264SingleBitrate1080p** -produceert een MP4-bestand waarin de video is gecodeerd met de H. 264-codec van 6750 kbps en een afbeeldings hoogte van 1080 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 64 kbps.
- **EncoderNamedPreset. H264SingleBitrate720p** -produceert een MP4-bestand waarin de video is gecodeerd met de H. 264-codec van 4500 kbps en een afbeeldings hoogte van 720 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 64 kbps.
- **EncoderNamedPreset. H264SingleBitrateSD** -produceert een MP4-bestand waarin de video is gecodeerd met de H. 264-codec van 2200 kbps en een afbeeldings hoogte van 480 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 64 kbps.

Zie [ingebouwde voor instellingen voor het coderen van Video's](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)voor een overzicht van de meest recente lijst met voor waarden.

Bekijk [bestanden uploaden, coderen en streamen](stream-files-tutorial-with-api.md)om te zien hoe de voor instellingen worden gebruikt.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) beschrijft de instellingen die moeten worden gebruikt bij het coderen van de invoer video met de standaard encoder. Gebruik deze definitie bij het aanpassen van voorinstellingen voor transformatie. 

#### <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voor instellingen gelden de volgende overwegingen:

- Alle waarden voor de hoogte en breedte van AVC-inhoud moeten een meervoud van 4 zijn.
- In Azure Media Services v3 bevinden alle coderings bitsnelheden zich in bits per seconde. Dit wijkt af van de voor instellingen met onze v2 Api's, die kilobits/seconde als eenheid hebben gebruikt. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

### <a name="customizing-presets"></a>Voor instellingen aanpassen

Media Services biedt volledige ondersteuning voor het aanpassen van alle waarden in de standaardinstellingen om te voldoen aan uw specifieke behoeften voor codering en vereisten. Zie voor voor beelden die laten zien hoe u de voor instellingen van code ring kunt aanpassen:

#### <a name="examples"></a>Voorbeelden

- [Voor instellingen aanpassen met .NET](customize-encoder-presets-how-to.md)
- [Voor instellingen aanpassen met CLI](custom-preset-cli-howto.md)
- [Voor instellingen aanpassen met REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Vooraf ingesteld schema

In Media Services v3 zijn de voor instellingen sterk getypeerde entiteiten in de API zelf. U kunt de definitie ' schema ' voor deze objecten vinden in de [open API-specificatie (of Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). U kunt de vooraf ingestelde definities (zoals **StandardEncoderPreset**) ook weer geven in de [rest API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (of een andere Media Services v3 SDK-referentie documentatie).

## <a name="scaling-encoding-in-v3"></a>Encoding in v3 schalen

Zie [schalen met CLI](media-reserved-units-cli-how-to.md)voor informatie over het schalen van media verwerking.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

* [Uploaden, coderen en streamen met Media Services](stream-files-tutorial-with-api.md)
* [Coderen van een HTTPS-URL met ingebouwde voor instellingen](job-input-from-http-how-to.md)
* [Een lokaal bestand coderen met ingebouwde voor instellingen](job-input-from-local-file-how-to.md)
* [Een aangepaste voor instelling maken om uw specifieke scenario-of apparaat vereisten te bereiken](customize-encoder-presets-how-to.md)