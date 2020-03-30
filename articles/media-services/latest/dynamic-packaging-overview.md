---
title: Dynamische verpakking in Azure Media Services v3
titleSuffix: Azure Media Services
description: Dit artikel geeft een overzicht van dynamische verpakkingen in Azure Media Services.
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
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: ae049d7486007696d8038eb4e6593cf996df659e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372596"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamische verpakking in Media Services v3

Microsoft Azure Media Services kan worden gebruikt om veel mediabronbestandsindelingen te coderen. Het levert ze via verschillende streaming protocollen, met of zonder bescherming van de inhoud, om alle belangrijke apparaten (zoals iOS en Android-apparaten) te bereiken. Deze klanten begrijpen verschillende protocollen. IOS vereist bijvoorbeeld dat streams worden geleverd in het HLS-formaat (Http Live Streaming) en Android-apparaten ondersteunen HLS en MPEG DASH.

In Media Services vertegenwoordigt een [Streaming Endpoint](streaming-endpoint-concept.md) een dynamische (just-in-time) verpakkings- en herkomstservice die uw live en on-demand content rechtstreeks aan een clientplayer-app kan leveren. Het maakt gebruik van een van de gemeenschappelijke streaming media protocollen genoemd in de volgende sectie. Dynamische pakketten is een functie die standaard wordt geleverd op alle streaming-eindpunten (Standard of Premium).

> [!NOTE]
> U de [Azure-portal](https://portal.azure.com/) gebruiken om v3 [Live Events](live-events-outputs-concept.md), bekijk [v3-assets](assets-concept.md), informatie te krijgen over toegang tot API's. Gebruik voor alle andere beheertaken (bijvoorbeeld Transformaties en Taken) de [REST API,](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)of een van de ondersteunde [SDK's.](media-services-apis-overview.md#sdks)

## <a name="to-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Uw bronbestanden voorbereiden op levering

Om gebruik te kunnen maken van Dynamic Packaging, moet u uw mezzanine (bron)bestand [coderen](encoding-concept.md) in een set mp4-bestanden (ISO Base Media 14496-12). U moet een [asset](assets-concept.md) hebben met de gecodeerde MP4- en streamingconfiguratiebestanden die nodig zijn voor Media Services Dynamic Packaging. Van deze set MP4-bestanden u Dynamic Packaging gebruiken om video te leveren via de onderstaande streaming mediaprotocollen.

> [!TIP]
> Een manier om de MP4- en streamingconfiguratiebestanden te krijgen, is door uw mezzaninebestand te [coderen met Media Services.](#encode-to-adaptive-bitrate-mp4s) 

Als u video's in het gecodeerde item beschikbaar wilt maken voor clients voor afspelen, moet u een [streaminglocator](streaming-locators-concept.md) maken en streaming-URL's maken. Vervolgens ontvangt u, op basis van de opgegeven indeling in het streaming clientmanifest (HLS, MPEG DASH of Smooth Streaming), de stream in het door u gekozen protocol.

Hierdoor hoeft u voor slechts één opslagindeling de bestanden op te slaan en hiervoor te betalen. De Media Services-service bouwt en levert de juiste reactie op basis van aanvragen van een client.

Zie [Streamingprotocollen en versleutelingstypen](content-protection-overview.md#streaming-protocols-and-encryption-types)als u van plan bent uw inhoud te beschermen met dynamische versleuteling van Media Services.

### <a name="hls-protocol"></a>HLS-protocol

Uw streamingclient kan de volgende HLS-indelingen opgeven:

|Protocol|Voorbeeld|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>MPEG-DASH-protocol

Uw streamingclient kan de volgende MPEG-DASH-indelingen opgeven:

|Protocol|Voorbeeld|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Vloeiend streaming-protocol

Uw streamingclient kan de volgende vloeiend streaming-indelingen opgeven:

|Protocol|Notities/voorbeelden| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2.0 (legacy manifest)|Standaard bevat smooth streaming manifest-indeling de repeat tag (r-tag). Echter, sommige spelers niet `r-tag`ondersteunen de . Clients met deze spelers kunnen een indeling gebruiken waarmee de r-tag wordt uitgeschakeld:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming vereist dat zowel audio als video aanwezig moeten zijn in je stream.

## <a name="on-demand-streaming-workflow"></a>Streamingworkflow op aanvraag

De volgende stappen tonen een algemene streamingworkflow voor Media Services waarin Dynamische verpakking wordt gebruikt, samen met de standaardencoder in Azure Media Services.

1. Upload een invoerbestand zoals een QuickTime/MOV- of MXF-bestand. Dit bestand wordt ook wel de mezzanine of bronbestand genoemd. Zie [Formaten die worden ondersteund door de standaardencoder voor](media-encoder-standard-formats.md)de lijst met ondersteunde indelingen.
1. [Codeer](#encode-to-adaptive-bitrate-mp4s) uw mezzaninebestand in een H.264/AAC MP4 adaptieve bitrate-set.
1. Publiceer het uitvoerelement dat de adaptieve bitrate MP4-set bevat. U publiceert door een streaming locator te maken.
1. URL's maken die zich richten op verschillende indelingen (HLS, MPEG-DASH en Smooth Streaming). Het **Streaming Endpoint** zou zorgen voor het serveren van het juiste manifest en verzoeken voor al deze verschillende formaten.

Het volgende diagram toont de on-demand streaming met Dynamic Packaging workflow.

![Diagram van een workflow voor on-demand streaming met Dynamic Packaging](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Coderen op adaptieve bitrate MP4's

In de volgende artikelen worden voorbeelden weergegeven van het coderen van [een video met Media Services:](encoding-concept.md)

* [Coderen vanuit een HTTPS-URL met ingebouwde voorinstellingen](job-input-from-http-how-to.md).
* [Codeer een lokaal bestand met ingebouwde voorinstellingen.](job-input-from-local-file-how-to.md)
* [Maak een aangepaste voorinstelling om uw specifieke scenario- of apparaatvereisten te targeten.](customize-encoder-presets-how-to.md)

Zie de lijst met standaard encoderformaten [en codecs](media-encoder-standard-formats.md).

## <a name="live-streaming-workflow"></a>Werkstroom voor live streaming

Een live-evenement kan worden ingesteld op een *pass-through* (een on-premises live encoder stuurt een meervoudige bitrate stream) of *live codering* (een on-premises live encoder stuurt een enkele bitrate stream). 

Hier is een veelgebruikte workflow voor live streaming met Dynamic Packaging:

1. Maak een [live-evenement](live-events-outputs-concept.md).
1. Download de inname-URL en configureer uw on-premises encoder om de URL te gebruiken om de bijdragefeed te verzenden.
1. Download de preview-URL en gebruik deze om te controleren of de invoer van de encoder wordt ontvangen.
1. Maak een nieuw item.
1. Maak een live-uitvoer en gebruik de assetnaam die u hebt gemaakt.<br />De live-uitvoer archiveert de stream in het actief.
1. Maak een streaminglocator met de ingebouwde typen streamingbeleid.<br />Als u van plan bent uw inhoud te versleutelen, controleert u [het overzicht Inhoudsbescherming](content-protection-overview.md).
1. Vermeld de paden op de streaminglocator om de URL's te gebruiken.
1. Download de hostnaam voor het streamingeindpunt waaru van wilt streamen.
1. URL's maken die zich richten op verschillende indelingen (HLS, MPEG-DASH en Smooth Streaming). Het streaming eindpunt zorgt voor het serveren van het juiste manifest en aanvragen voor de verschillende formaten.

In dit diagram ziet u de workflow voor live streaming met Dynamic Packaging:

![Diagram van een workflow voor pass-through codering met Dynamic Packaging](./media/live-streaming/pass-through.svg)

Zie [Live streaming overzicht](live-streaming-overview.md)voor informatie over live streaming in Media Services v3.

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Videocodecs ondersteund door Dynamic Packaging

Dynamic Packaging ondersteunt MP4-bestanden die video bevatten die is gecodeerd met [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC of AVC1) of [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 of hvc1).

> [!NOTE]
> Resoluties tot 4K en framerates tot 60 frames per seconde zijn getest met Dynamic Packaging. De [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) ondersteunt codering aan H.265 via de legacy v2 API's.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Audiocodecs ondersteund door Dynamic Packaging

Dynamic Packaging ondersteunt audio die is gecodeerd met de volgende protocollen:

* [OC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (OC-LC, HE-AAC v1 of HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Verbeterde AC-3 of E-AC3)
* Dolby Atmos<br />
   Streaming Dolby Atmos content wordt ondersteund voor standaarden zoals het MPEG-DASH protocol met common streaming format (CSF) of Common Media Application Format (CMAF) gefragmenteerde MP4, en via HTTP Live Streaming (HLS) met CMAF.

* [Dts](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DTS-codecs die worden ondersteund door DASH-CSF, DASH-CMAF, HLS-M2TS en HLS-CMAF-verpakkingsformaten zijn:  

    * DTS Digital Surround (dtsc)
    * DTS-HD hoge resolutie en DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (geen core) (dtsl)

Dynamic Packaging ondersteunt meerdere audiotracks met DASH of HLS (versie 4 of hoger) voor streaming-assets met meerdere audiotracks met meerdere codecs en talen.

### <a name="additional-notes"></a>Aanvullende opmerkingen

Dynamic Packaging ondersteunt geen bestanden die [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) audio bevatten (het is een legacy codec).

> [!NOTE]
> De [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) ondersteunt codering met Dolby Digital Plus via de oudere v2 API's.

## <a name="manifests"></a>Manifesteert

In Media Services Dynamic Packaging worden de streamingclientmanifesten voor HLS, MPEG-DASH en Smooth Streaming dynamisch gegenereerd op basis van de indelingskiezer in de URL.  

Een manifestbestand bevat streamingmetagegevens zoals tracktype (audio, video of tekst), tracknaam, begin- en eindtijd, bitrate (kwaliteiten), tracktalen, presentatievenster (schuifvenster van vaste duur) en videocodec (FourCC). Het instrueert de speler ook om het volgende fragment op te halen door informatie te verstrekken over de volgende speelbare videofragmenten die beschikbaar zijn en hun locatie. Fragmenten (of segmenten) zijn de werkelijke "brokken" van video-inhoud.

### <a name="examples"></a>Voorbeelden

#### <a name="hls"></a>HLS

Hier is een voorbeeld van een HLS-manifestbestand, ook wel een HLS-hoofdafspeellijst genoemd: 

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

Hier is een voorbeeld van een MPEG-DASH manifestbestand, ook wel een MPEG-DASH Media Presentation Description (MPD):

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

Hier is een voorbeeld van een manifestbestand vloeiend streamen:

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

Als een audiotracknaam is opgegeven in het .ism-bestand, voegt Media Services een `Label` element toe `AdaptationSet` om de tekstuele informatie voor de specifieke audiotrack op te geven. Een voorbeeld van het uitvoerDASH-manifest:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

De speler kan `Label` het element gebruiken om weer te geven op de gebruikersinterface.

### <a name="signaling-audio-description-tracks"></a>Audiobeschrijvingstracks signaleren

U een gesproken kuur aan uw video toevoegen om gebruikers met een visuele beperking te helpen de video-opname te volgen door naar de gesproken tekst te luisteren. U moet een audiotrack annoteren als audiobeschrijving in het manifest. Voeg daarvoor 'toegankelijkheid' en 'rolparameters' toe aan het ISM-bestand. Het is uw verantwoordelijkheid om deze parameters correct in te stellen om een audiotrack als audiobeschrijving te signaleren. Voeg bijvoorbeeld `<param name="accessibility" value="description" />` toe `<param name="role" value="alternate"` aan het .ism-bestand voor een specifieke audiotrack. 

Zie Het voorbeeld [van een beschrijvend audiospoor](signal-descriptive-audio-howto.md) voor meer informatie.

#### <a name="smooth-streaming-manifest"></a>Vloeiend streaming manifest

Als u een vloeiende streamingstream afspeelt, worden `Accessibility` waarden `Role` in het manifest en kenmerken voor die audiotrack in het manifest geplaatst. Er `Role="alternate" Accessibility="description"` wordt bijvoorbeeld in `StreamIndex` het element toegevoegd om aan te geven dat het een audiobeschrijving is.

#### <a name="dash-manifest"></a>DASH-manifest

Voor DASH-manifest worden de volgende twee elementen toegevoegd om de audiobeschrijving te signaleren:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS-afspeellijst

Voor HLS v7 `(format=m3u8-cmaf)`en hoger, `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` zou de afspeellijst dragen wanneer de audio beschrijving track wordt gesignaleerd.

#### <a name="example"></a>Voorbeeld

Zie [Audiobeschrijvingstracks](signal-descriptive-audio-howto.md)voor meer informatie.

## <a name="dynamic-manifest"></a>Dynamisch manifest

Als u het aantal tracks, formaten, bitrates en presentatietijdvensters wilt beheren dat naar spelers wordt verzonden, u dynamische filtering gebruiken met de dynamische verpakker van Media Services. Zie [Pre-filtering manifesten met de dynamische verpakker voor](filters-dynamic-manifest-overview.md)meer informatie.

## <a name="dynamic-encryption"></a>Dynamische versleuteling

U *dynamische versleuteling* gebruiken om uw live of on-demand content dynamisch te versleutelen met AES-128 of een van de drie grote DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties aan geautoriseerde clients. Zie [dynamische versleuteling voor](content-protection-overview.md)meer informatie.

> [!NOTE]
> Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="more-information"></a>Meer informatie

Bekijk [de Azure Media Services-community](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="need-help"></a>Hebt u hulp nodig?

U een ondersteuningsticket openen door te navigeren naar [nieuw ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

[Video's uploaden, coderen en streamen](stream-files-tutorial-with-api.md)
