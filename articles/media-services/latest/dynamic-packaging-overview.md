---
title: Overzicht van dynamische verpakking van Azure Media Services | Microsoft Docs
description: In het artikel wordt een overzicht gegeven van dynamische verpakkingen in Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/10/2019
ms.author: juliako
ms.openlocfilehash: 152a767ad1aa2494579f15dd8051c6bc1f718a92
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910263"
---
# <a name="dynamic-packaging"></a>Dynamische verpakking

Microsoft Azure Media Services kan worden gebruikt voor het coderen van veel media bron bestands indelingen en om ze te leveren via verschillende streaming protocollen, met of zonder inhouds beveiliging, om alle grote apparaten te bereiken (bijvoorbeeld iOS-en Android-apparaten). Deze clients begrijpen verschillende protocollen, bijvoorbeeld iOS vereist dat stromen worden geleverd in HTTP Live Streaming-indeling (HLS) en Android-apparaten ondersteunen HLS en MPEG DASH. 

In Media Services vertegenwoordigt een [streaming-eind punt](streaming-endpoint-concept.md) een dynamische (just-in-time) verpakkings-en bron service die uw Live-en on-demand-inhoud rechtstreeks aan een client speler kan leveren, met behulp van een van de algemene protocollen voor streaming media vermeld in de volgende sectie. Dynamische pakketten is een functie die standaard wordt geleverd op alle streaming-eindpunten (Standard of Premium). 

## <a name="a-iddelivery-protocolsto-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Voor bereiding van de bron bestanden voor levering

Als u gebruik wilt maken van dynamische pakketten, moet u uw mezzanine-bestand (bron) [coderen](encoding-concept.md) in een set van meerdere bitrate MP4-bestanden (ISO Base media 14496-12). U moet een [Asset](assets-concept.md) hebben met de gecodeerde MP4-bestanden en de streaming-configuratie bestanden die nodig zijn voor Media Services dynamische verpakking. Vanuit deze set MP4-bestanden kunt u dynamische pakketten gebruiken voor het leveren van video via de volgende protocollen voor streaming media:

|Protocol|Voorbeeld|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH/KVP| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

Als u van plan bent om uw inhoud te beschermen met Media Services dynamische versleuteling, raadpleegt u [streaming protocollen en versleutelings typen](content-protection-overview.md#streaming-protocols-and-encryption-types).

> [!TIP]
> Een van de manieren om de MP4-en streaming-configuratie bestanden op te halen is het [versleutelen van uw mezzanine-bestand met Media Services](#encode-to-adaptive-bitrate-mp4s). 

Als u Video's in het gecodeerde activum beschikbaar wilt maken voor het afspelen van clients, moet u een [streaming-Locator](streaming-locators-concept.md) maken en streaming-url's bouwen. Op basis van de opgegeven indeling in het streaming-client manifest (HLS, MPEG DASH of Smooth Streaming) ontvangt u de stroom in het protocol dat u hebt gekozen.

Hierdoor hoeft u voor slechts één opslagindeling de bestanden op te slaan en hiervoor te betalen. De Media Services-service bouwt en levert de juiste reactie op basis van aanvragen van een client. 

## <a name="on-demand-streaming-workflow"></a>Werk stroom voor streaming op aanvraag

Hier volgt een algemene Media Services streaming-werk stroom waarbij dynamische pakketten worden gebruikt samen met de standaard encoder in Azure Media Services.

1. Upload een invoer bestand zoals een QuickTime/MOV-of MXF-bestand (Zie indelingen die worden [ondersteund door de Media Encoder Standard](media-encoder-standard-formats.md)voor de lijst met ondersteunde indelingen. Dit wordt ook wel het mezzanine-of bron bestand genoemd.
1. [Codeer](#encode-to-adaptive-bitrate-mp4s) uw mezzanine-bestand in een adaptieve bitsnelheid-set van H. 264/AAC MP4. 
1. Publiceer het uitvoer activum dat de Adaptive bitrate MP4-set bevat. U publiceert door een streaming-Locator te maken.
1. Bouw Url's die zijn gericht op verschillende indelingen (HLS, MPEG-DASH en Smooth Streaming). Het **streaming-eind punt** zorgt ervoor dat het juiste manifest en aanvragen voor al deze verschillende indelingen worden bewaard.

In het volgende diagram ziet u de on-demand streaming met dynamische verpakkings werk stroom.

![Diagram van een werk stroom voor streaming op aanvraag met dynamische verpakking](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Coderen naar adaptieve bitrate Mp4's

In de volgende artikelen ziet u voor beelden van het [coderen van een video met Media Services](encoding-concept.md):

* [Coderen van een HTTPS-URL met behulp van ingebouwde voor instellingen](job-input-from-http-how-to.md)
* [Een lokaal bestand coderen met behulp van ingebouwde voor instellingen](job-input-from-local-file-how-to.md)
* [Een aangepaste voor instelling maken om uw specifieke scenario-of apparaat vereisten te bereiken](customize-encoder-presets-how-to.md)

Bekijk de lijst met Media Encoder Standard [indelingen en codecs](media-encoder-standard-formats.md).

## <a name="live-streaming-workflow"></a>Werk stroom voor live streamen

Een live gebeurtenis kan een van de volgende twee typen zijn: Pass-Through of Live code ring. 

Hier volgt een algemene werk stroom voor live streamen met dynamische pakketten:

1. Een [live gebeurtenis](live-events-outputs-concept.md)maken.
1. Haal de opname-URL op en configureer uw on-premises Encoder voor het gebruik van de URL om de contributie feed te verzenden.
1. Haal de Preview-URL op en gebruik deze om te controleren of de invoer van het coderings programma wordt ontvangen.
1. Maak een nieuwe Asset.
1. Een live uitvoer maken en de Asset-naam gebruiken die u hebt gemaakt.<br />De live uitvoer archiveert de stroom naar de Asset.
1. Maak een streaming-Locator met de ingebouwde streaming-beleids typen.<br />Als u van plan bent om uw inhoud te versleutelen, raadpleegt u [overzicht van inhouds beveiliging](content-protection-overview.md).
1. Geef een lijst van de paden in de streaming-Locator op om de Url's te verkrijgen die u wilt gebruiken.
1. Haal de hostnaam op voor het streaming-eind punt van waaruit u wilt streamen.
1. Bouw Url's die zijn gericht op verschillende indelingen (HLS, MPEG-DASH en Smooth Streaming). Het streaming-eind punt zorgt ervoor dat het juiste manifest en aanvragen voor de verschillende indelingen worden bewaard.

Dit diagram toont de werk stroom voor live streamen met dynamische pakketten:

![Diagram van een werk stroom voor Pass-Through-code ring met dynamische verpakking](./media/live-streaming/pass-through.svg)

Zie [overzicht van live streaming](live-streaming-overview.md)voor meer informatie over live streamen in Media Services v3.

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video-codecs die worden ondersteund door dynamische pakketten

Dynamische verpakking ondersteunt MP4-bestanden die video bevatten die is gecodeerd met [H. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC of AVC1) of [H. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 of hvc1).

> [!NOTE]
> Resoluties van Maxi maal 4.000 en de frame snelheid van Maxi maal 60 frames per seconde zijn getest met dynamische verpakking. De [Premium encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) ondersteunt de code ring van H. 265 via de oudere v2-api's. Neem contact amshelp@microsoft.com op met als u vragen hebt over dit onderwerp. 

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Audio-codecs die worden ondersteund door dynamische pakketten

Dynamische verpakking ondersteunt audio die is gecodeerd met de volgende protocollen:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 of HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 of E-AC3)
* Dolby Atmos<br />
   Het streamen van Dolby Atmos-inhoud wordt ondersteund voor standaarden zoals het MPEG-DASH-protocol met de gefragmenteerde MP4-indeling (KVP) of common media-toepassings indeling (CMAF), gesplitste webnetstream en via HTTP Live Streaming (HLS) met CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   De DTS-codecs die worden ondersteund door streepje-KVP, streepje-CMAF, HLS-M2TS en HLS-CMAF-verpakkings indelingen zijn:  

    * DTS Digital Surround (dtsc)
    * DTS-HD-hoge resolutie en DTS-HD-Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD zonder kwaliteits verlies (geen kern geheugens) (dtsl)

Dynamische verpakkingen ondersteunt meerdere audio sporen met een streepje of HLS (versie 4 of hoger) voor streaming-assets met meerdere audio tracks met meerdere codecs en talen.

### <a name="additional-notes"></a>Aanvullende opmerkingen

Dynamische pakketten bieden geen ondersteuning voor bestanden die [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) audio bevatten (een verouderde codec).

> [!NOTE]
> De [Premium encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) ondersteunt code ring voor Dolby Digital Plus, via de oudere v2-api's. Neem contact amshelp@microsoft.com op met als u vragen hebt over dit onderwerp. 

## <a name="manifests"></a>Manifesten 
 
In Media Services dynamische verpakking worden de streaming-client manifesten voor HLS, MPEG-DASH en Smooth Streaming dynamisch gegenereerd op basis van de indelings kiezer in de URL.  

Een manifest bestand bevat streaming-meta gegevens zoals het type spoor (audio, video of tekst), de naam van het spoor, de begin-en eind tijd, de bitsnelheid (kwaliteiten), de bijgehouden talen, het presentatie venster (sliding window van de vaste duur) en de videocodec (FourCC). Het geeft ook de speler de opdracht het volgende fragment op te halen door informatie over de volgende Speel bare video fragmenten te verstrekken die beschikbaar zijn en hun locatie. Fragmenten (of segmenten) zijn de werkelijke ' chunks ' van video-inhoud.

### <a name="examples"></a>Voorbeelden

#### <a name="hls"></a>HLS

Hier volgt een voor beeld van een HLS-manifest bestand, ook wel een HLS-hoofd lijst genoemd: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Hier volgt een voor beeld van een MPEG-DASH-manifest bestand, ook wel een MPEG-DASH media Presentation Description (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Hier volgt een voor beeld van een manifest bestand van Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Naamgeving van sporen in het manifest

Als er een audio track naam is opgegeven in het ISM-bestand, Media Services een `Label` element binnen een `AdaptationSet` toevoegen om de texturische informatie voor het specifieke audio spoor op te geven. Een voor beeld van het uitvoer streepje-manifest:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

De speler kan het `Label` element gebruiken om weer te geven in de gebruikers interface.

### <a name="signaling-audio-description-tracks"></a>Signa lering audio beschrijvings sporen

Een klant kan aantekeningen maken op een audio track als audio beschrijving in het manifest. Hiertoe voegen ze de para meters Accessibility en Role toe aan het. ISM-bestand. Media Services herkent de audio beschrijving als een audio track de para meter ' Accessibility ' met de waarde ' description ' en ' Role ' met de waarde ' alternatief ' bevat. Als Media Services de audio beschrijving in het. ISM-bestand detecteert, wordt de informatie over de beschrijving van de audio `Accessibility="description"` door `Role="alternate"` gegeven aan het `StreamIndex` client manifest, evenals kenmerken in het-element.

Als de combi natie van ' Accessibility ' = ' description ' en ' Role ' = ' Alternate ' is ingesteld in. ISM-bestand, worden het streepje-manifest en het gladde manifest waarden die zijn ingesteld in de para meters ' Accessibility ' en ' Role '. Het is de verantwoordelijkheid van de klant om deze twee waarden recht in te stellen en een audio track als audio beschrijving te markeren. Per streepje spec, ' Accessibility ' = ' description ' en ' Role ' = ' Alternate ', wordt een audio track in een audio beschrijving genoemd.

Voor HLS V7 en hoger (`format=m3u8-cmaf`) geldt de afspeel lijst `CHARACTERISTICS="public.accessibility.describes-video"` alleen wanneer de combi natie van ' Accessibility ' = ' description ' en ' Role ' = ' Alternate ' is ingesteld in. ISM-bestand. 

## <a name="dynamic-manifest"></a>Dynamisch manifest

Als u het aantal sporen, indelingen, bitsnelheden en presentatie tijd vensters wilt beheren die naar spelers worden verzonden, kunt u dynamische filtering gebruiken met de Media Services Dynamic packager. Zie voor meer informatie [het vooraf filteren van manifesten met de dynamische pakket](filters-dynamic-manifest-overview.md)functie.

## <a name="dynamic-encryption"></a>Dynamische versleuteling

U kunt *dynamische versleuteling* gebruiken om uw Live of on-demand inhoud dynamisch te versleutelen met AES-128 of een van de drie primaire Digital Rights Management (DRM)-systemen: Micro soft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties aan geautoriseerde clients. Zie [dynamische versleuteling](content-protection-overview.md)voor meer informatie.

## <a name="more-information"></a>Meer informatie

Raadpleeg [Azure Media Services Community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates over Media Services te krijgen.

## <a name="next-steps"></a>Volgende stappen

> [!NOTE]
> U kunt momenteel geen gebruik maken van de Azure-portal om v3-resources te beheren. Gebruik de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

Meer informatie over het [uploaden, coderen en streamen van Video's](stream-files-tutorial-with-api.md).

