---
title: Het leveren van inhoud aan klanten | Microsoft Documenten
description: In dit onderwerp wordt een overzicht gegeven van wat er komt kijken bij het leveren van uw inhoud met Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c8d32a6434db0fad18b9fe7c2d6e2117795eb651
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476735"
---
# <a name="deliver-content-to-customers"></a>Content leveren aan klanten
Wanneer u uw streaming- of video-on-demand-inhoud aan klanten levert, is het uw doel om video van hoge kwaliteit aan verschillende apparaten te leveren onder verschillende netwerkomstandigheden.

Om dit doel te bereiken, u:

* Codeer je stream op een multi-bitrate (adaptive bitrate) videostream. Dit zorgt voor de kwaliteit en de netwerkomstandigheden.
* Gebruik [dynamische verpakking](media-services-dynamic-packaging-overview.md) van Microsoft Azure Media Services om uw stream dynamisch opnieuw te verpakken in verschillende protocollen. Dit zorgt voor streaming op verschillende apparaten. Media Services ondersteunt de levering van de volgende adaptieve bitrate streaming technologieën: <br/>
    * **HTTP Live Streaming** (HLS) - voeg "(format=m3u8-aapl)" pad toe aan het "/Manifest"-gedeelte van de URL om de streaming origin-server te vertellen hls-inhoud terug te sturen voor consumptie op **apple iOS-native** apparaten (voor meer informatie, [zie locators](#locators) en [URL's](#URLs)),
    * **MPEG-DASH** - voeg "(format=mpd-time-csf)" pad toe aan het "/Manifest"-gedeelte van de URL om de streaming origin-server te vertellen mpeg-DASH terug te keren (voor meer informatie, [zie locators](#locators) en [URL's),](#URLs)
    * **Vloeiende streaming**.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 

Dit artikel geeft een overzicht van belangrijke content delivery concepten.

Zie Bekende problemen [controleren](media-services-deliver-content-overview.md#known-issues)om bekende problemen te controleren.

## <a name="dynamic-packaging"></a>Dynamische verpakking
Met de dynamische verpakking die Media Services biedt, u uw adaptieve bitrate MP4- of Smooth Streaming-gecodeerde inhoud leveren in streamingformaten die worden ondersteund door Media Services (MPEG-DASH, HLS, Smooth Streaming),) zonder opnieuw te hoeven verpakken in deze streaming-indelingen. Wij raden u aan uw content te leveren met een dynamische verpakking.

Om te profiteren van dynamische verpakking, moet u uw mezzanine (bron) bestand coderen in een set van adaptive-bitrate MP4-bestanden of adaptieve bitrate Smooth Streaming-bestanden.

Met dynamische verpakking slaat u de bestanden op en betaalt u deze in één opslagformaat. Media Services zal het juiste antwoord bouwen en serveren op basis van uw verzoeken.

Dynamische verpakkingen zijn beschikbaar voor standaard en premium streaming endpoints. 

Zie [Dynamische verpakking](media-services-dynamic-packaging-overview.md)voor meer informatie.

## <a name="filters-and-dynamic-manifests"></a>Filters en dynamische manifesten
U filters voor uw assets definiëren met Media Services. Deze filters zijn regels aan de serverzijde die uw klanten helpen dingen te doen zoals het afspelen van een specifiek gedeelte van een video of het opgeven van een subset van audio- en videoweergaven die het apparaat van uw klant kan verwerken (in plaats van alle weergaven die aan het item zijn gekoppeld). Deze filtering wordt bereikt door *middel van dynamische manifesten* die worden gemaakt wanneer uw klant vraagt om een video te streamen op basis van een of meer opgegeven filters.

Zie [Filters en dynamische manifesten voor](media-services-dynamic-manifest-overview.md)meer informatie.

## <a name="locators"></a><a id="locators"/>Locators
Als u uw gebruiker een URL wilt geven die kan worden gebruikt om uw inhoud te streamen of te downloaden, moet u eerst uw asset publiceren door een locator te maken. Een locator biedt een toegangspunt om toegang te krijgen tot de bestanden in een asset. Media Services ondersteunt twee typen locators:

* OnDemandOrigin locators. Deze worden gebruikt om media te streamen (bijvoorbeeld MPEG-DASH, HLS of Smooth Streaming) of om bestanden geleidelijk te downloaden.
* Url-locators (Shared Access Signature) (SAS). Deze worden gebruikt om mediabestanden naar uw lokale computer te downloaden.

Een *toegangsbeleid* wordt gebruikt om de machtigingen (zoals lezen, schrijven en lijst) en de duur waarvoor een client toegang heeft voor een bepaald item te definiëren. Houd er rekening mee dat de lijstmachtiging (AccessPermissions.List) niet mag worden gebruikt bij het maken van een OnDemandOrigin-locator.

Locators hebben vervaldatums. De Azure-portal stelt in de toekomst een vervaldatum in van 100 jaar voor locators.

> [!NOTE]
> Als u de Azure-portal hebt gebruikt om locators vóór maart 2015 te maken, zouden deze locators na twee jaar verlopen.
> 
> 

Als u de vervaldatum van een locator wilt bijwerken, gebruikt u [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator)- of [.NET](https://go.microsoft.com/fwlink/?LinkID=533259)-API's. Wanneer u de vervaldatum van een SAS-locator bijwerkt, wordt de URL gewijzigd.

Locators zijn niet ontworpen om toegangsbeheer per gebruiker te beheren. U verschillende toegangsrechten aan individuele gebruikers geven met behulp van DRM-oplossingen (Digital Rights Management). Zie [Media beveiligen voor](https://msdn.microsoft.com/library/azure/dn282272.aspx)meer informatie.

Wanneer u een locator maakt, kan er een vertraging van 30 seconden optreden als gevolg van vereiste opslag- en propagatieprocessen in Azure Storage.

## <a name="adaptive-streaming"></a>Adaptieve streaming
Dankzij adaptieve bitrate-technologieën kunnen videospelertoepassingen de netwerkomstandigheden bepalen en uit verschillende bitrates kiezen. Wanneer de netwerkcommunicatie degradeert, kan de client een lagere bitrate selecteren, zodat het afspelen kan doorgaan met een lagere videokwaliteit. Naarmate de netwerkomstandigheden verbeteren, kan de client overschakelen naar een hogere bitrate met verbeterde videokwaliteit. Azure Media Services ondersteunt de volgende adaptieve bitrate-technologieën: HTTP Live Streaming (HLS), Smooth Streaming en MPEG-DASH.

Als u gebruikers streaming-URL's wilt bieden, moet u eerst een OnDemandOrigin-locator maken. Als u de locator maakt, krijgt u het basispad naar het element dat de inhoud bevat die u wilt streamen. Om deze inhoud te kunnen streamen, moet u dit pad echter verder wijzigen. Als u een volledige URL wilt maken voor het streamingmanifestbestand, moet u de padwaarde van de locator en de manifestnaam (filename.ism) toevoegen. Voeg **vervolgens /Manifest** en een geschikte indeling (indien nodig) toe aan het zoekpad.

> [!NOTE]
> U uw inhoud ook streamen via een TLS-verbinding. Zorg ervoor dat je streaming-URL's beginnen met HTTPS. Houd er rekening mee dat AMS momenteel geen TLS ondersteunt met aangepaste domeinen.  
> 

Je alleen via TLS streamen als het streaming-eindpunt van waaruit je je content levert, is gemaakt na 10 september 2014. Als uw streaming-URL's zijn gebaseerd op de streamingeindpunten die na 10 september 2014 zijn gemaakt, bevat de URL 'streaming.mediaservices.windows.net'. Streaming-URL's die 'origin.mediaservices.windows.net' (de oude indeling) bevatten, ondersteunen geen TLS. Als uw URL in de oude indeling is en u via TLS wilt kunnen streamen, maakt u een nieuw streamingeindpunt. Gebruik URL's op basis van het nieuwe streaming-eindpunt om uw inhoud via TLS te streamen.

## <a name="streaming-url-formats"></a><a id="URLs"/>URL-indelingen voor streaming

### <a name="mpeg-dash-format"></a>MPEG-DASH-indeling
{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4-formaat
{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3-indeling
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) indeling met audio-only filter
Standaard worden alleen audiotracks opgenomen in het HLS-manifest. Dit is vereist voor Apple Store-certificering voor mobiele netwerken. Als een client in dit geval niet voldoende bandbreedte heeft of is verbonden via een 2G-verbinding, schakelt het afspelen over naar alleen audio. Dit helpt om content te streamen zonder buffering, maar er is geen video. In sommige scenario's kan het bufferen van spelers de voorkeur krijgen boven alleen audio. Als u de track met alleen audio wilt verwijderen, voegt u **audio-only=false** toe aan de URL.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Zie [Ondersteuning voor dynamische manifestsamenstelling en HLS-uitvoer extra functies](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)voor meer informatie.

### <a name="smooth-streaming-format"></a>Vloeiende streaming-indeling
{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

Voorbeeld:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Smooth Streaming 2.0 manifest (legacy manifest)
Standaard bevat smooth streaming manifest-indeling de repeat tag (r-tag). Sommige spelers ondersteunen de r-tag echter niet. Clients met deze spelers kunnen een indeling gebruiken waarmee de r-tag wordt uitgeschakeld:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progressieve download
Met progressieve download u beginnen met het afspelen van media voordat het hele bestand is gedownload. U geen .ism* (ismv, isma, ismt of ismc) bestanden downloaden.

Als u inhoud geleidelijk wilt downloaden, gebruikt u het ondemandoorsprongtype locator. In het volgende voorbeeld wordt de URL weergegeven die is gebaseerd op het type locator van OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

U moet alle opslagversleutelde elementen die u wilt streamen vanaf de origin-service decoderen voor progressief downloaden.

## <a name="download"></a>Download
Als u uw inhoud wilt downloaden naar een clientapparaat, moet u een SAS-locator maken. De SAS-locator geeft u toegang tot de Azure Storage-container waar uw bestand zich bevindt. Als u de download-URL wilt samenstellen, moet u de bestandsnaam tussen de host en de SAS-handtekening insluiten.

In het volgende voorbeeld wordt de URL weergegeven die is gebaseerd op de SAS-locator:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

De volgende overwegingen zijn van toepassing:

* U moet alle opslagversleutelde elementen die u wilt streamen vanaf de origin-service decoderen voor progressief downloaden.
* Een download die niet binnen 12 uur is voltooid, mislukt.

## <a name="streaming-endpoints"></a>Streaming-eindpunten

Een streamingeindpunt vertegenwoordigt een streamingservice die inhoud rechtstreeks kan leveren aan een clientplayertoepassing of aan een content delivery network (CDN) voor verdere distributie. De uitgaande stream van een streaming endpoint-service kan een live stream of een video-on-demand-asset in uw Media Services-account zijn. Er zijn twee soorten streaming endpoints, **standaard** en **premium.** Zie [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Overzicht van streaming-eindpunten) voor meer informatie.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 

## <a name="known-issues"></a>Bekende problemen
### <a name="changes-to-smooth-streaming-manifest-version"></a>Wijzigingen in de manifestversie voor vloeiend streamen
Vóór de release van de service van juli 2016 - toen assets geproduceerd door Media Encoder Standard, Media Encoder Premium Workflow of de eerdere Azure Media Encoder werden gestreamd met behulp van dynamische verpakking - zou het Smooth Streaming manifest dat is geretourneerd in overeenstemming zijn met versie 2.0. In versie 2.0 gebruiken de fragmentduur geen de zogenaamde repeat ('r') tags. Bijvoorbeeld:


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

In de release van de service van juli 2016 voldoet het gegenereerde Smooth Streaming-manifest aan versie 2.2, met fragmentduur met behulp van herhalentags. Bijvoorbeeld:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Sommige van de oudere Smooth Streaming-clients ondersteunen mogelijk de herhalende tags niet en laden het manifest niet. Om dit probleem te verhelpen, u de parameter legacy manifest formaat **(format=fmp4-v20)** gebruiken of uw client bijwerken naar de nieuwste versie, die herhaaltags ondersteunt. Zie [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20)voor meer informatie.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Media Services-locators bijwerken na het rollen van opslagsleutels](media-services-roll-storage-access-keys.md)

