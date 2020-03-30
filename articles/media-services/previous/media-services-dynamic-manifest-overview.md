---
title: Filters en dynamische manifesten | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stream te streamen. Media Services maakt dynamische manifesten om deze selectieve streaming te archiveren.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 1234263fa800a17d0a5c235df54ca2751e3094bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015854"
---
# <a name="filters-and-dynamic-manifests"></a>Filters en dynamische manifesten

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 2](media-services-dynamic-manifest-overview.md)
> * [Versie 3](../latest/filters-dynamic-manifest-overview.md)

Vanaf 2.17 release u met Media Services filters voor uw assets definiëren. Deze filters zijn regels aan de serverzijde waarmee uw klanten kunnen kiezen om dingen te doen zoals: speel slechts een gedeelte van een video af (in plaats van de hele video af te spelen), of geef alleen een subset van audio- en videoweergaven op die het apparaat van uw klant aankan ( in plaats van alle weergaven die aan het actief zijn gekoppeld). Deze filtering van uw assets wordt bereikt via **Dynamic Manifest**s die zijn gemaakt op verzoek van uw klant om een video te streamen op basis van opgegeven filter(s).

In dit onderwerp worden veelvoorkomende scenario's besproken waarin het gebruik van filters gunstig is voor uw klanten en koppelingen naar onderwerpen die aantonen hoe u filters programmatisch maken.

## <a name="overview"></a>Overzicht
Bij het leveren van uw content aan klanten (live-evenementen streamen of video-on-demand) is het uw doel om een video van hoge kwaliteit te leveren aan verschillende apparaten onder verschillende netwerkomstandigheden. Om dit doel te bereiken moet u het volgende doen:

* coderen van uw stream naar multi-bitrate[(adaptieve bitrate)](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)videostream (dit zal zorgen voor de kwaliteit en netwerkomstandigheden) en 
* gebruik Media Services [Dynamic Packaging](media-services-dynamic-packaging-overview.md) om uw stream dynamisch opnieuw in te verpakken in verschillende protocollen (dit zorgt voor streaming op verschillende apparaten). Media Services ondersteunt de levering van de volgende adaptieve bitrate streaming technologieën: HTTP Live Streaming (HLS), Smooth Streaming en MPEG DASH. 

### <a name="manifest-files"></a>Manifestbestanden
Wanneer u een asset codeert voor adaptieve bitrate streaming, wordt een **manifestbestand** (afspeellijst) gemaakt (het bestand is tekstgebaseerd of op XML gebaseerd). Het **manifestbestand** bevat streamingmetadata zoals: tracktype (audio, video of tekst), tracknaam, begin- en eindtijd, bitrate (kwaliteiten), tracktalen, presentatievenster (schuifvenster van vaste duur), videocodec (FourCC). Het instrueert de speler ook om het volgende fragment op te halen door informatie te verstrekken over de volgende speelbare videofragmenten die beschikbaar zijn en hun locatie. Fragmenten (of segmenten) zijn de werkelijke "brokken" van een video-inhoud.

Hier is een voorbeeld van een manifestbestand: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Dynamische manifesten
Er zijn [scenario's](media-services-dynamic-manifest-overview.md#scenarios) waarin uw klant meer flexibiliteit nodig heeft dan wat wordt beschreven in het manifestbestand van het standaardassetbestand. Bijvoorbeeld:

* Apparaatspecifiek: lever alleen de opgegeven weergaves en/of opgegeven taaltracks die worden ondersteund door het apparaat dat wordt gebruikt om de inhoud af te spelen ("weergavefiltering"). 
* Verklein het manifest om een subclip van een live-gebeurtenis weer te geven ('subclipfiltering').
* Het begin van een video bijsnijden ('een video bijsnijden').
* Het presentatievenster (DVR) aanpassen om een beperkte lengte van het DVR-venster in de speler te bieden ("presentatievenster aanpassen").

Om deze flexibiliteit te bereiken, biedt Media Services **dynamische manifesten** op basis van vooraf gedefinieerde [filters.](media-services-dynamic-manifest-overview.md#filters)  Zodra u de filters hebt gedefinieerd, kunnen uw klanten deze gebruiken om een specifieke weergave of subclips van uw video te streamen. Ze zouden filter(s) opgeven in de streaming-URL. Filters kunnen worden toegepast op adaptieve bitrate streaming protocollen ondersteund door [Dynamic Packaging:](media-services-dynamic-packaging-overview.md)HLS, MPEG-DASH, en Smooth Streaming. Bijvoorbeeld:

URL mpeg-streepje met filter

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Url vloeiend streamen met filter

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Zie [Inhoudsoverzicht leveren](media-services-deliver-content-overview.md)voor meer informatie over het leveren van uw inhoud en het maken van streaming-URL's.

> [!NOTE]
> Houd er rekening mee dat dynamische manifesten het actief en het standaardmanifest voor dat actief niet wijzigen. Uw klant kan ervoor kiezen om een stream aan te vragen met of zonder filters. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filters
Er zijn twee soorten assetfilters: 

* Globale filters (kunnen worden toegepast op elk item in het Azure Media Services-account, hebben een levensduur van het account) en 
* Lokale filters (kan alleen worden toegepast op een asset waarmee het filter is gekoppeld aan de creatie, hebben een levensduur van het item). 

Globale en lokale filtertypen hebben exact dezelfde eigenschappen. Het belangrijkste verschil tussen de twee is voor welke scenario's welk type van een filer is meer geschikt. Globale filters zijn over het algemeen geschikt voor apparaatprofielen (weergavefiltering) waarbij lokale filters kunnen worden gebruikt om een specifiek element bij te snijden.

## <a name="common-scenarios"></a><a id="scenarios"></a>Algemene scenario's
Zoals eerder vermeld, bij het leveren van uw inhoud aan klanten (streaming live evenementen of video-on-demand) uw doel is om een video van hoge kwaliteit te leveren aan verschillende apparaten onder verschillende netwerkomstandigheden. Daarnaast u andere vereisten hebben die betrekking hebben op het filteren van uw assets en het gebruik van **Dynamic Manifest**s. De volgende secties geven een kort overzicht van verschillende filterscenario's.

* Geef alleen een subset van audio- en videoweergaven op die bepaalde apparaten kunnen verwerken (in plaats van alle weergaven die aan het item zijn gekoppeld). 
* Alleen een gedeelte van een video afspelen (in plaats van de hele video af te spelen).
* DVR-presentatievenster aanpassen.

## <a name="rendition-filtering"></a>Weergavefiltering
U ervoor kiezen om uw asset te coderen op meerdere coderingsprofielen (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) en meerdere kwaliteitsbitrates. Niet alle clientapparaten ondersteunen echter alle profielen en bitrates van uw asset. Oudere Android-apparaten ondersteunen bijvoorbeeld alleen H.264 Baseline+AACL. Het verzenden van hogere bitrates naar een apparaat dat niet kan krijgen van de voordelen, verspilt bandbreedte en apparaat berekening. Een dergelijk apparaat moet alle gegeven informatie decoderen, alleen om deze af te schalen voor weergave.

Met Dynamic Manifest u apparaatprofielen maken, zoals mobiel, console, HD/SD, enz.

![Voorbeeld van weergavefiltering][renditions2]

In het volgende voorbeeld werd een encoder gebruikt om een mezzanine-asset te coderen in zeven ISO MP4s-videoweergaven (van 180p tot 1080p). Het gecodeerde element kan dynamisch worden verpakt in een van de volgende streamingprotocollen: HLS, Smooth en MPEG DASH.  Boven aan het diagram wordt het HLS-manifest voor het asset zonder filters weergegeven (het bevat alle zeven weergaven).  Linksonder wordt het HLS-manifest weergegeven waarop een filter met de naam "ott" is toegepast. De "ott" filter geeft aan om alle bitrates onder 1Mbps te verwijderen, wat resulteerde in de onderste twee kwaliteitsniveaus worden ontdaan in de reactie. Rechtsonder wordt het HLS-manifest weergegeven waarop een filter met de naam "mobiel" is toegepast. Het filter "mobiel" geeft aan dat de weergave wordt verwijderd wanneer de resolutie groter is dan 720p, waardoor de twee 1080p-weergaven zijn gestript.

![Weergavefiltering][renditions1]

## <a name="removing-language-tracks"></a>Taaltracks verwijderen
Uw assets kunnen meerdere audiotalen bevatten, zoals Engels, Spaans, Frans, enz. Meestal zijn de Player SDK-managers standaard audiotrackselectie en beschikbare audiotracks per gebruikersselectie. Het is een uitdaging om dergelijke Player SDKs te ontwikkelen, het vereist verschillende implementaties tussen apparaatspecifieke speler-frameworks. Ook op sommige platforms zijn De API's van de speler beperkt en omvatten geen audioselectiefunctie waar de gebruikers het standaardaudiospoor niet kunnen selecteren of wijzigen. Met assetfilters u het gedrag beheren door filters te maken die alleen de gewenste audiotalen bevatten.

![Taaltracks filteren][language_filter]

## <a name="trimming-start-of-an-asset"></a>Begin van een actief bijsnijden
In de meeste live streaming-evenementen voeren operators een aantal tests uit voordat de werkelijke gebeurtenis is. Ze kunnen bijvoorbeeld een leisteen als deze bevatten voor de start van het evenement: "Het programma begint tijdelijk". Als het programma archiveert, worden de test- en leigegevens ook gearchiveerd en opgenomen in de presentatie. Deze informatie mag echter niet aan de klanten worden getoond. Met Dynamic Manifest u een starttijdfilter maken en de ongewenste gegevens uit het manifest verwijderen.

![Begin bijsnijden][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Subclips (weergaven) maken vanuit een live archief
Veel live-evenementen zijn lang lopend en live archief kan meerdere gebeurtenissen bevatten. Nadat het live evenement is afgelopen, willen omroepen mogelijk het live archief opsplitsen in logische programmastart- en stopsequenties. Publiceer vervolgens deze virtuele programma's afzonderlijk zonder het live archief te verwerken en geen afzonderlijke assets te maken (wat niet het voordeel krijgt van de bestaande in de cache opgeslagen fragmenten in de CDN's). Voorbeelden van dergelijke virtuele programma's zijn de kwartalen van een voetbal of basketbal spel, innings in honkbal, of individuele evenementen van een sportprogramma.

Met Dynamic Manifest u filters maken met begin-/eindtijden en virtuele weergaven maken boven aan uw live-archief. 

![Subclip, filter][subclip_filter]

Gefilterd actief:

![Skiën][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Presentatievenster (DVR aanpassen)
Momenteel biedt Azure Media Services een circulair archief waar de duur kan worden geconfigureerd tussen 5 minuten - 25 uur. Manifestfiltering kan worden gebruikt om een rollend DVR-venster over de bovenkant van het archief te maken, zonder media te verwijderen. Er zijn veel scenario's waarin omroepen een beperkt DVR-venster willen bieden om met de live rand mee te bewegen en tegelijkertijd een groter archiveringsvenster te behouden. Een omroep kan de gegevens die uit het DVR-venster zijn, willen gebruiken om clips te markeren, of ze willen mogelijk verschillende DVR-vensters voor verschillende apparaten bieden. De meeste mobiele apparaten verwerken bijvoorbeeld geen grote DVR-vensters (u een DVR-venster van 2 minuten hebben voor mobiele apparaten en een uur voor desktopclients).

![DVR-venster][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff aanpassen (livepositie)
Manifestfiltering kan worden gebruikt om enkele seconden te verwijderen van de live rand van een live-programma. Met filtering kunnen omroepen de presentatie op het publicatiepunt bekijken en advertentieinvoegpunten maken voordat de kijkers de stream ontvangen (met een back-off van 30 seconden). Omroepen kunnen deze advertenties dan op tijd naar hun klantkaders pushen, voor hen om de informatie te ontvangen en te verwerken voordat de advertentiemogelijkheid wordt gebruikt.

Naast de advertentieondersteuning kan de LiveBackoff-instelling worden gebruikt om de positie van de kijkers aan te passen, zodat wanneer clients afdrijven en de live rand raken, ze nog steeds fragmenten van de server kunnen krijgen in plaats van een HTTP 404- of 412-fout te krijgen.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Meerdere regels combineren in één filter
U meerdere filterregels combineren in één filter. Als voorbeeld u een bereikregel definiëren om leien uit een live archief te verwijderen en ook beschikbare bitrates eruit filteren. Bij het toepassen van meerdere filterregels is het eindresultaat het snijpunt van alle regels.

![meerdere regels][multiple-rules]

## <a name="create-filters-programmatically"></a>Filters programmatisch maken
In het volgende artikel worden mediaservicesbesproken die gerelateerd zijn aan filters. Het artikel laat ook zien hoe u filters programmatisch maken.  

[Filters maken met REST API's](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Meerdere filters combineren (filtersamenstelling)
U ook meerdere filters combineren in één URL. 

In het volgende scenario wordt aangetoond waarom u filters wilt combineren:

1. Je moet je videokwaliteiten filteren op mobiele apparaten zoals Android of iPAD (om videokwaliteiten te beperken). Om de ongewenste kwaliteiten te verwijderen, maakt u een globaal filter dat geschikt is voor de apparaatprofielen. Zoals eerder vermeld in dit artikel, globale filters kunnen worden gebruikt voor al uw activa onder dezelfde media diensten account zonder verdere associatie. 
2. U wilt ook de begin- en eindtijd van een actief bijsnijden. Om dit te bereiken, maakt u een lokaal filter en stelt u de begin-/eindtijd in. 
3. U wilt beide filters combineren (zonder combinatie moet u kwaliteitsfiltering toevoegen aan het bijsnijdfilter, wat het gebruik van filters moeilijker maakt).

Als u filters wilt combineren, moet u de filternamen instellen op de URL van het manifest/afspeellijst met puntkomma die is afgebakend. Stel dat u een filter met de naam *MyMobileDevice* hebt dat kwaliteiten filtert en u hebt een andere naam *MyStartTime* om een specifieke begintijd in te stellen. Je ze als volgt combineren:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

U maximaal drie filters combineren. 

Voor meer informatie, zie [deze](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Problemen en beperkingen kennen
* Dynamisch manifest werkt in GOP grenzen (Key Frames) dus trimmen heeft GOP nauwkeurigheid. 
* U dezelfde filternaam gebruiken voor lokale en globale filters. Lokale filters hebben een hogere prioriteit en overschrijven globale filters.
* Als u een filter bijwerkt, kan het tot 2 minuten duren voordat streaming eindpunt om de regels te vernieuwen. Als de inhoud is weergegeven met behulp van een aantal filters (en in de cache opgeslagen in proxy's en CDN-caches), kan het bijwerken van deze filters leiden tot player-fouten. Het wordt aanbevolen om de cache te wissen na het bijwerken van het filter. Als deze optie niet mogelijk is, u overwegen een ander filter te gebruiken.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Content leveren aan klantenoverzicht](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
