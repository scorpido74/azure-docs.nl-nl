---
title: Uw manifesten filteren met Dynamic Packager
titleSuffix: Azure Media Services
description: Meer informatie over het maken van filters met Dynamic Packager om uw manifesten te filteren en selectief te streamen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186220"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Uw manifesten filteren met Dynamic Packager

Wanneer u adaptieve streaminginhoud voor bitrate's aan apparaten levert, moet u soms meerdere versies van een manifest publiceren om specifieke apparaatmogelijkheden of beschikbare netwerkbandbreedte te targeten. Met [de Dynamic Packager](dynamic-packaging-overview.md) u filters opgeven die onderweg specifieke codecs, resoluties, bitrates en audiotrackcombinaties kunnen filteren. Met deze filtering hoeft u niet meer meerdere kopieën te maken. U hoeft alleen maar een nieuwe URL te publiceren met een specifieke set filters die zijn geconfigureerd voor uw doelapparaten (iOS, Android, SmartTV of browsers) en de netwerkmogelijkheden (scenario's met hoge bandbreedte, mobiel of lage bandbreedte). In dit geval kunnen clients het streamen van uw inhoud manipuleren via de querytekenreeks (door beschikbare [assetfilters of accountfilters](filters-concept.md)op te geven) en filters gebruiken om specifieke secties van een stream te streamen.

Sommige leveringsscenario's vereisen dat u ervoor zorgt dat een klant geen toegang heeft tot specifieke tracks. Misschien wilt u bijvoorbeeld geen manifest publiceren dat HD-tracks bevat op een specifieke abonneelaag. Of misschien wilt u specifieke adaptieve bitrate -tracks verwijderen om de leveringskosten te verlagen naar een specifiek apparaat dat niet zou profiteren van de extra tracks. In dit geval u een lijst met vooraf gemaakte filters koppelen aan uw [streaminglocator](streaming-locators-concept.md) bij het maken. Clients kunnen vervolgens niet manipuleren hoe de inhoud wordt gestreamd omdat deze is gedefinieerd door de **Streaming Locator.**

U filteren combineren door filters op te geven [op Streaming Locator](filters-concept.md#associating-filters-with-streaming-locator) + extra apparaatspecifieke filters die uw client in de URL opgeeft. Deze combinatie is handig om extra tracks zoals metadata of gebeurtenisstreams, audiotalen of beschrijvende audiotracks te beperken.

Deze mogelijkheid om verschillende filters op uw stream op te geven, biedt een krachtige **Dynamic Manifest-manipulatieoplossing** om meerdere use-case-scenario's voor uw doelapparaten te targeten. In dit onderwerp worden concepten met betrekking tot **dynamische manifesten** uitgelegd en worden voorbeelden gegeven van scenario's waarin u deze functie gebruiken.

> [!NOTE]
> Dynamische manifesten veranderen het actief en het standaardmanifest voor dat actief niet.

## <a name="overview-of-manifests"></a>Overzicht van manifesten

Azure Media Services ondersteunt HLS-, MPEG DASH- en Smooth Streaming-protocollen. Als onderdeel van [Dynamic Packaging](dynamic-packaging-overview.md)worden de streaming clientmanifesten (HLS Master Playlist, DASH Media Presentation Description [MPD] en Smooth Streaming) dynamisch gegenereerd op basis van de formatselector in de URL. Zie de leveringsprotocollen in [de werkstroom voor veelvoorkomende on-demand.](dynamic-packaging-overview.md#delivery-protocols)

### <a name="get-and-examine-manifest-files"></a>Manifestbestanden inpakken en onderzoeken

U geeft een lijst op met eigenschapsvoorwaarden voor filtertracks op basis van welke tracks van uw stream (live of video on-demand [VOD]) moeten worden opgenomen in een dynamisch gemaakt manifest. Om de eigenschappen van de tracks te krijgen en te onderzoeken, moet je eerst het Smooth Streaming-manifest laden.

De [zelfstudie Bestanden uploaden, coderen en streamen met .NET-zelfstudie](stream-files-tutorial-with-api.md#get-streaming-urls) laat zien hoe u de streaming-URL's bouwen met .NET. Als u de app uitvoert, wijst een van de `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`URL's naar het manifest Vloeiend streamen: .<br/> Kopieer en plak de URL in de adresbalk van een browser. Het bestand wordt gedownload. U het openen in elke teksteditor.

Zie Bestanden [uploaden, coderen en streamen met REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)voor een REST-voorbeeld.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>De bitrate van een videostream controleren

U de [demopagina van Azure Media Player](https://aka.ms/azuremediaplayer) gebruiken om de bitrate van een videostream te controleren. Op de demopagina worden diagnostische gegevens weergegeven op het tabblad **Diagnostische gegevens:**

![Azure Media Player-diagnostiek][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Voorbeelden: URL's met filters in querytekenreeks

U filters toepassen op ABR-streamingprotocollen: HLS, MPEG-DASH en Smooth Streaming. In de volgende tabel worden enkele voorbeelden van URL's met filters weergegeven:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Weergavefiltering

U ervoor kiezen om uw asset te coderen op meerdere coderingsprofielen (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) en meerdere kwaliteitsbitrates. Niet alle clientapparaten ondersteunen echter alle profielen en bitrates van uw asset. Oudere Android-apparaten ondersteunen bijvoorbeeld alleen H.264 Baseline+AACL. Het verzenden van hogere bitrates naar een apparaat dat niet kan krijgen van de voordelen verspilt bandbreedte en apparaat berekening. Een dergelijk apparaat moet alle gegeven informatie decoderen, alleen om het af te schalen voor weergave.

Met Dynamic Manifest u apparaatprofielen (zoals mobiel, console of HD/SD) maken en de tracks en kwaliteiten opnemen die u in elk profiel wilt gebruiken. Dat heet weergavefiltering. In het volgende diagram ziet u een voorbeeld ervan.

![Voorbeeld van weergavefiltering met dynamisch manifest][renditions2]

In het volgende voorbeeld werd een encoder gebruikt om een mezzanine-asset te coderen in zeven ISO MP4s-videoweergaven (van 180p tot 1080p). Het gecodeerde element kan dynamisch worden [verpakt](dynamic-packaging-overview.md) in een van de volgende streamingprotocollen: HLS, MPEG DASH en Smooth.

De bovenkant van het volgende diagram toont het HLS-manifest voor het item zonder filters. (Het bevat alle zeven vertolkingen.)  Linksonder toont het diagram een HLS-manifest waarop een filter met de naam "ott" is toegepast. Het "ott"-filter geeft de verwijdering van alle bitrates onder de 1 Mbps aan, zodat de onderste twee kwaliteitsniveaus in de respons zijn gestript. Rechtsonder toont het diagram het HLS-manifest waarop een filter met de naam "mobiel" is toegepast. Het filter "mobiel" geeft de verwijdering van weergaven aan waarbij de resolutie groter is dan 720p, zodat de twee 1080p-weergaven zijn gestript.

![Weergavefilteren met dynamisch manifest][renditions1]

## <a name="removing-language-tracks"></a>Taaltracks verwijderen
Uw assets kunnen meerdere audiotalen bevatten, zoals Engels, Spaans, Frans, enzovoort. Meestal beheert de Player SDK standaard audiotrackselectie en beschikbare audiotracks per gebruikersselectie.

Het ontwikkelen van dergelijke Player SDKs is een uitdaging omdat het verschillende implementaties vereist voor apparaatspecifieke spelersframeworks. Op sommige platforms zijn de API's van de speler ook beperkt en bevatten ze niet de functie voor audioselectie waarbij gebruikers de standaardaudiotrack niet kunnen selecteren of wijzigen. Met assetfilters u het gedrag beheren door filters te maken die alleen de gewenste audiotalen bevatten.

![Filteren van taaltracks met Dynamisch manifest][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Het begin van een actief bijsnijden

In de meeste live streaming-evenementen voeren operators een aantal tests uit voordat de werkelijke gebeurtenis is. Ze kunnen bijvoorbeeld een leisteen als deze bevatten voor de start van het evenement: 'Het programma begint tijdelijk'.

Als het programma archiveert, worden de test- en leigegevens ook gearchiveerd en opgenomen in de presentatie. Deze informatie mag echter niet aan de klanten worden getoond. Met Dynamic Manifest u een starttijdfilter maken en de ongewenste gegevens uit het manifest verwijderen.

![Begin van een asset bijsnijden met Dynamic Manifest][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Subclips (weergaven) maken vanuit een live archief

Veel live-evenementen zijn lang lopend en live archief kan meerdere gebeurtenissen bevatten. Nadat het live evenement is afgelopen, willen omroepen mogelijk het live archief opsplitsen in logische programmastart- en stopsequenties.

U deze virtuele programma's afzonderlijk publiceren zonder het live archief te verwerken en geen afzonderlijke elementen te maken (wat niet het voordeel krijgt van de bestaande in de cache opgeslagen fragmenten in de CDN's). Voorbeelden van dergelijke virtuele programma's zijn de kwartalen van een voetbal of basketbal spel, innings in honkbal, of individuele evenementen van een sportprogramma.

Met Dynamic Manifest u filters maken met begin-/eindtijden en virtuele weergaven maken boven aan uw live-archief.

![Subclipfilter met dynamisch manifest][subclip_filter]

Hier is de gefilterde asset:

![Gefilterd actief met dynamisch manifest][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Het presentatievenster (DVR) aanpassen

Momenteel biedt Azure Media Services een circulair archief waar de duur kan worden geconfigureerd tussen 1 minuut tot 25 uur. Manifestfiltering kan worden gebruikt om een rollend DVR-venster over de bovenkant van het archief te maken, zonder media te verwijderen. Er zijn veel scenario's waarin omroepen een beperkt DVR-venster willen bieden om met de live rand mee te bewegen en tegelijkertijd een groter archiveringsvenster te behouden. Een omroep kan de gegevens die uit het DVR-venster zijn, willen gebruiken om clips te markeren, of ze willen mogelijk verschillende DVR-vensters voor verschillende apparaten bieden. De meeste mobiele apparaten verwerken bijvoorbeeld geen grote DVR-vensters (u een DVR-venster van 2 minuten hebben voor mobiele apparaten en een uur voor desktopclients).

![DVR-venster met dynamisch manifest][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff aanpassen (livepositie)

Manifestfiltering kan worden gebruikt om enkele seconden te verwijderen van de live rand van een live-programma. Met filtering kunnen omroepen de presentatie op het publicatiepunt bekijken en advertentieinvoegpunten maken voordat de kijkers de stream ontvangen (30 seconden uitgeschakeld). Omroepen kunnen deze advertenties dan op tijd naar hun klantkaders pushen, voor hen om de informatie te ontvangen en te verwerken voordat de advertentiemogelijkheid wordt gebruikt.

Naast de advertentieondersteuning kan de live back-off-instelling worden gebruikt om de positie van de kijkers aan te passen, zodat wanneer clients afdrijven en de live rand raken, ze nog steeds fragmenten van de server kunnen krijgen. Op die manier krijgen klanten geen HTTP 404- of 412-fout.

![Filter voor live back-off met Dynamic Manifest][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Meerdere regels combineren in één filter

U meerdere filterregels combineren in één filter. U bijvoorbeeld een 'bereikregel' definiëren om leien uit een live archief te verwijderen en ook beschikbare bitrates filteren. Wanneer u meerdere filterregels toepast, is het eindresultaat het snijpunt van alle regels.

![Meerdere filterregels met dynamisch manifest][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Meerdere filters combineren (filtersamenstelling)

U ook meerdere filters combineren in één URL. In het volgende scenario wordt aangetoond waarom u filters wilt combineren:

1. Je moet je videokwaliteiten filteren op mobiele apparaten, zoals Android of iPad (om videokwaliteiten te beperken). Om de ongewenste eigenschappen te verwijderen, maakt u een accountfilter dat geschikt is voor de apparaatprofielen. U accountfilters gebruiken voor al uw assets onder hetzelfde Media Services-account zonder verdere associatie.
1. U wilt ook de begin- en eindtijd van een actief bijsnijden. Als u het bijsnijden wilt doen, maakt u een assetfilter en stelt u de begin-/eindtijd in.
1. U wilt beide filters combineren. Zonder combinatie, zou u kwaliteitshet filtreren aan het snoeiende filter moeten toevoegen, die filtergebruik moeilijker zou maken.

Als u filters wilt combineren, stelt u de filternamen in op de URL van het manifest/afspeellijst in de puntkomma-afgebakende indeling. Stel dat u een filter met de naam *MyMobileDevice* hebt dat kwaliteiten filtert, en u hebt een andere naam *MyStartTime* om een specifieke begintijd in te stellen. U maximaal drie filters combineren.

Voor meer informatie, zie [deze blogpost](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Overwegingen en beperkingen

- De waarden voor **forceEndTimestamp,** **presentationWindowDuration**en **liveBackoffDuration** mogen niet worden ingesteld voor een VOD-filter. Ze worden alleen gebruikt voor live filterscenario's.
- Een dynamisch manifest werkt in GOP-grenzen (hoofdframes), dus trimmen heeft GOP-nauwkeurigheid.
- U dezelfde filternaam gebruiken voor account- en assetfilters. Assetfilters hebben een hogere prioriteit en overschrijven accountfilters.
- Als u een filter bijwerkt, kan het tot 2 minuten duren voordat het streaming-eindpunt de regels vernieuwt. Als u filters hebt gebruikt om de inhoud te serveren (en u de inhoud in proxy's en CDN-caches hebt opgeslagen), kan het bijwerken van deze filters leiden tot player-fouten. We raden u aan de cache te wissen na het bijwerken van het filter. Als deze optie niet mogelijk is, u overwegen een ander filter te gebruiken.
- Klanten moeten het manifest handmatig downloaden en de exacte starttijdstempel en -tijd sonteren.

    - Als u de eigenschappen van de tracks in een actief wilt bepalen, [krijgt u het manifestbestand op en onderzoekt u deze](#get-and-examine-manifest-files).
    - De formule voor het instellen van de tijdstempeleigenschappen van het assetfilter is: <br/>startTimestamp &lt;= starttijd&gt; +  &lt;in de verwachte&gt; begintijd van het manifest in seconden * tijdschaal

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u filters programmatisch maken:  

- [Filters maken met REST API's](filters-dynamic-manifest-rest-howto.md)
- [Filters maken met .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Filters maken met CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
