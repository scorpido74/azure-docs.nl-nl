---
title: Gefragmenteerde MP4-live-innamespecificatie | Microsoft Documenten
description: Deze specificatie beschrijft het protocol en de indeling voor gefragmenteerde MP4-gebaseerde live streaming voor Azure Media Services. In dit document worden ook best practices besproken voor het bouwen van zeer redundante en robuuste leefmechanismen.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 507afad294e8233ea4de4130795f29925870fcdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888050"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services gefragmenteerde MP4 live inname specificatie 

Deze specificatie beschrijft het protocol en de indeling voor gefragmenteerde MP4-gebaseerde live streaming voor Azure Media Services. Media Services biedt een live streamingservice die klanten kunnen gebruiken om live-evenementen te streamen en inhoud in realtime uit te zenden met Azure als cloudplatform. In dit document worden ook best practices besproken voor het bouwen van zeer redundante en robuuste leefmechanismen.

## <a name="1-conformance-notation"></a>1. Conformance notatie
De trefwoorden "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" en "OPTIONAL" in dit document moeten worden geïnterpreteerd zoals ze zijn beschreven in RFC 2119.

## <a name="2-service-diagram"></a>2. Servicediagram
In het volgende diagram ziet u de architectuur op hoog niveau van de live streamingservice in Media Services:

1. Een live-encoder pusht live feeds naar kanalen die zijn gemaakt en ingericht via de Azure Media Services SDK.
1. Kanalen, programma's en streaming-eindpunten in Media Services verwerken alle live streaming-functionaliteiten, waaronder inname, opmaak, cloud DVR, beveiliging, schaalbaarheid en redundantie.
1. Optioneel kunnen klanten ervoor kiezen om een Azure Content Delivery Network-laag te implementeren tussen het streaming-eindpunt en de clienteindpunten.
1. Clienteindpunten streamen vanaf het streamingeindpunt met behulp van HTTP Adaptive Streaming-protocollen. Voorbeelden hiervan zijn Microsoft Smooth Streaming, Dynamic Adaptive Streaming via HTTP (DASH of MPEG-DASH) en Apple HTTP Live Streaming (HLS).

![inname stroom][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream-indeling – ISO 14496-12 gefragmenteerde MP4
Het draadformaat voor live streaming in dit document is gebaseerd op [ISO-14496-12]. Voor een gedetailleerde uitleg van gefragmenteerde MP4-formaat en extensies, zowel voor video-on-demand bestanden en live streaming opname, zie [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definities van live inname-indelingen
In de volgende lijst worden definities van speciale indelingen beschreven die van toepassing zijn op live inname in Azure Media Services:

1. De **ftyp,** **Live Server Manifest Box**en **moov** boxen MOETEN bij elk verzoek worden verzonden (HTTP POST). Deze vakken MOETEN worden verzonden aan het begin van de stream en op elk moment dat de encoder opnieuw verbinding moet maken om de stream ingest te hervatten. Zie sectie 6 in [1].
1. Sectie 3.3.2 in [1] definieert een optioneel vak genaamd **StreamManifestBox** voor live inname. Vanwege de routeringslogica van de Azure-load balancer wordt het gebruik van dit vak afgeschaft. De doos mag niet aanwezig zijn bij het innemen in Media Services. Als dit vak aanwezig is, negeert Media Services het in stilte.
1. Het vak **TrackFragmentExtendedHeaderBox** dat in punt 3.2.3.2 in [1] is gedefinieerd, moet voor elk fragment aanwezig zijn.
1. Versie 2 van het vak **TrackFragmentExtendedHeaderBox** MOET worden gebruikt om mediasegmenten te genereren met identieke URL's in meerdere datacenters. Het fragmentindexveld is vereist voor cross-datacenterfailover van op indexgebaseerde streamingformaten zoals Apple HLS en op indexgebaseerde MPEG-DASH. Om cross-datacenter failover mogelijk te maken, moet de fragmentindex worden gesynchroniseerd over meerdere encoders en met 1 worden verhoogd voor elk opeenvolgende mediafragment, zelfs voor opnieuw opstarten of fouten van encoders.
1. Sectie 3.3.6 in [1] definieert een vak genaamd **MovieFragmentRandomAccessBox** **(mfra)** dat aan het einde van de live opname kan worden verzonden om het einde van de stream (EOS) aan het kanaal aan te geven. Vanwege de inname logica van Media Services, met behulp van EOS is afgeschaft, en de **mfra** vak voor live inname mag niet worden verzonden. Als deze wordt verzonden, negeert Media Services dit in stilte. Als u de status van het innamepunt wilt resetten, raden we u aan [Kanaalreset te](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels)gebruiken. We raden je ook aan [programmastop](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) te gebruiken om een presentatie en stream te beëindigen.
1. De MP4-fragmentduur moet constant zijn om de grootte van de clientmanifesten te verminderen. Een constante MP4 fragment duur verbetert ook client download heuristiek door het gebruik van repeat tags. De duur kan fluctueren om de niet-gehele framepercentages te compenseren.
1. De MP4 fragment duur moet worden tussen ongeveer 2 en 6 seconden.
1. MP4 fragment tijdstempels en indexen (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` en `fragment_index`) MOET aankomen in toenemende volgorde. Hoewel Media Services bestand is tegen het dupliceren van fragmenten, heeft het beperkte mogelijkheid om fragmenten opnieuw te ordenen volgens de mediatijdlijn.

## <a name="4-protocol-format--http"></a>4. Protocolformaat – HTTP
ISO gefragmenteerde MP4-gebaseerde live inname voor Media Services maakt gebruik van een standaard langlopende HTTP POST-verzoek om gecodeerde mediagegevens die zijn verpakt in gefragmenteerde MP4-indeling naar de service te verzenden. Elke HTTP POST stuurt een volledige gefragmenteerde MP4 bitstream ("stream"), vanaf het begin met header boxes **(ftyp**, **Live Server Manifest Box**, en **moov** dozen), en verder met een reeks fragmenten **(moof** en **mdat** dozen). Zie paragraaf 9.2 in [1] voor de syntaxis van de URL voor de HTTP POST-aanvraag. Een voorbeeld van de URL van POST is: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Vereisten
Hier zijn de gedetailleerde eisen:

1. De encoder MOET de uitzending starten door een HTTP POST-verzoek te verzenden met een lege "body" (nul inhoudslengte) met dezelfde inname-URL. Dit kan de encoder helpen snel te detecteren of het live opnameeindpunt geldig is en of er verificatie of andere voorwaarden vereist zijn. Volgens het HTTP-protocol kan de server geen HTTP-antwoord terugsturen totdat het volledige verzoek, inclusief de POST-hoofd, is ontvangen. Gezien het langlopende karakter van een live-evenement, zonder deze stap, kan de encoder mogelijk geen fouten detecteren totdat het verzenden van alle gegevens is voltooid.
1. De encoder MOET eventuele fouten of authenticatie uitdagingen als gevolg van (1). Als (1) slaagt met een 200-antwoord, ga dan verder.
1. De encoder MOET een nieuwe HTTP POST-aanvraag starten met de gefragmenteerde MP4-stream. De payload MOET beginnen met de kopvakken, gevolgd door fragmenten. Houd er rekening mee dat de **ftyp,** **Live Server Manifest Box**en **moov** dozen (in deze volgorde) moeten worden verzonden met elk verzoek, zelfs als de encoder opnieuw verbinding moet maken omdat de vorige aanvraag werd beëindigd voorafgaand aan het einde van de stream. 
1. De encoder MOET chunked transfer codering gebruiken voor het uploaden, omdat het onmogelijk is om de volledige inhoudsduur van het live evenement te voorspellen.
1. Wanneer de gebeurtenis voorbij is, na het verzenden van het laatste fragment, moet de encoder de gechunked transfer-coderingsberichtreeks op een elegante manier beëindigen (de meeste HTTP-clientstacks verwerken het automatisch). De encoder moet wachten tot de service de definitieve antwoordcode retourneert en vervolgens de verbinding beëindigen. 
1. De encoder MAG `Events()` HET'snaamwoord zoals beschreven in 9.2 in [1] NIET gebruiken voor live opname in Media Services.
1. Als de HTTP POST-aanvraag wordt beëindigd of een time-out heeft met een TCP-fout vóór het einde van de stream, moet de encoder een nieuwe POST-aanvraag indienen met behulp van een nieuwe verbinding en de voorgaande vereisten volgen. Bovendien moet de encoder de vorige twee MP4-fragmenten opnieuw verzenden voor elk nummer in de stream en hervatten zonder een discontinuïteit in de mediatijdlijn in te voeren. Het opnieuw verzenden van de laatste twee MP4-fragmenten voor elk nummer zorgt ervoor dat er geen gegevensverlies is. Met andere woorden, als een stream zowel een audio- als een videotrack bevat en de huidige POST-aanvraag mislukt, moet de encoder de laatste twee fragmenten opnieuw verbinden en opnieuw verzenden voor de audiotrack, die eerder met succes zijn verzonden, en de laatste twee fragmenten voor de video track, die eerder met succes werden verzonden, om ervoor te zorgen dat er geen verlies van gegevens. De encoder MOET een "voorwaartse" buffer van mediafragmenten handhaven, die het opnieuw verzendt wanneer het opnieuw verbindt.

## <a name="5-timescale"></a>5. Tijdschema
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) beschrijft het gebruik van de tijdschaal voor **SmoothStreamingMedia** (sectie 2.2.2.1), **StreamElement** (punt 2.2.2.3), **StreamFragmentElement** (punt 2.2.2.6) en **LiveSMIL** (punt 2.2.7.3.1). Als de tijdschaalwaarde niet aanwezig is, is de standaardwaarde 10.000.000 (10 MHz). Hoewel de specificatie voor vloeiende streaming-indeling het gebruik van andere tijdschaalwaarden niet blokkeert, gebruiken de meeste encoderimplementaties deze standaardwaarde (10 MHz) om vloeiende streaminggegevens te genereren. Vanwege de [Azure Media Dynamic Packaging-functie](media-services-dynamic-packaging-overview.md) raden we u aan een tijdschaal van 90 KHz te gebruiken voor videostreams en 44,1 KHz of 48,1 KHz voor audiostreams. Als verschillende tijdschaalwaarden worden gebruikt voor verschillende streams, moet de tijdschaal op streamniveau worden verzonden. Zie [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx)voor meer informatie.     

## <a name="6-definition-of-stream"></a>6. Definitie van "stream"
Stream is de basiseenheid van de werking in live-opname voor het samenstellen van live presentaties, het verwerken van streaming failover, en redundantie scenario's. Stream wordt gedefinieerd als een unieke, gefragmenteerde MP4-bitstream die mogelijk één track of meerdere tracks bevat. Een volledige live presentatie kan een of meer streams bevatten, afhankelijk van de configuratie van de live-encoders. De volgende voorbeelden illustreren verschillende opties van het gebruik van streams om een volledige live presentatie samen te stellen.

**Voorbeeld:** 

Een klant wil een live streaming presentatie maken met de volgende audio/video bitrates:

Video – 3000 kbps, 1500 kbps, 750 kbps

Audio – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Optie 1: Alle tracks in één stream
In deze optie genereert een enkele encoder alle audio/videotracks en bundelt deze vervolgens in één gefragmenteerde MP4-bitstream. De gefragmenteerde MP4 bitstream wordt vervolgens verzonden via een enkele HTTP POST-verbinding. In dit voorbeeld is er slechts één stream voor deze live presentatie.

![Streams-one track][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Optie 2: Elk nummer in een aparte stream
In deze optie plaatst de encoder één track in elk fragment MP4 bitstream en plaatst vervolgens alle streams via afzonderlijke HTTP-verbindingen. Dit kan met één encoder of met meerdere encoders. De live-opname ziet deze live presentatie als samengesteld uit vier streams.

![Streams-aparte tracks][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Optie 3: Bundel audiotrack met de laagste bitrate videotrack in één stream
In deze optie kiest de klant ervoor om de audiotrack te bundelen met de laagste bitrate videotrack in een fragment MP4 bitstream, en laat de andere twee videotracks als afzonderlijke streams. 

![Streams-audio- en videotracks][image4]

### <a name="summary"></a>Samenvatting
Dit is geen uitputtende lijst van alle mogelijke opnameopties voor dit voorbeeld. In feite wordt elke groepering van tracks in streams ondersteund door live inname. Klanten en encoderleveranciers kunnen hun eigen implementaties kiezen op basis van technische complexiteit, encodercapaciteit en redundantie en failoveroverwegingen. In de meeste gevallen is er echter slechts één audiotrack voor de hele live presentatie. Het is dus belangrijk om de gezondheid van de innamestream die de audiotrack bevat, te waarborgen. Deze overweging resulteert vaak in het zetten van de audio track in zijn eigen stream (zoals in optie 2) of bundeling met de laagste bitrate video track (zoals in optie 3). Ook voor een betere redundantie en fouttolerantie, het verzenden van dezelfde audio track in twee verschillende streams (Optie 2 met redundante audio tracks) of het bundelen van de audio track met ten minste twee van de laagste bitrate video tracks (Optie 3 met audio gebundeld in ten minste twee videostreams) wordt ten zeerste aanbevolen voor live inname in Media Services.

## <a name="7-service-failover"></a>7. Servicefailover
Gezien de aard van live streaming, goede failover ondersteuning is van cruciaal belang voor het waarborgen van de beschikbaarheid van de dienst. Media Services is ontworpen om verschillende soorten storingen te verwerken, waaronder netwerkfouten, serverfouten en opslagproblemen. Bij gebruik in combinatie met de juiste failoverlogica van de live encoderkant, kunnen klanten een zeer betrouwbare live streamingservice vanuit de cloud bereiken.

In deze sectie bespreken we failoverscenario's voor service. In dit geval gebeurt de fout ergens binnen de service en manifesteert het zich als een netwerkfout. Hier volgen enkele aanbevelingen voor de encoder-implementatie voor het afhandelen van servicefailover:

1. Gebruik een time-out van 10 seconden voor het tot stand brengen van de TCP-verbinding. Als een poging om de verbinding tot stand te brengen langer dan 10 seconden duurt, afgebroken de bewerking en probeer opnieuw. 
1. Gebruik een korte time-out voor het verzenden van de http-aanvraagberichtbrokken. Als de mp4-fragmentduur van het doel N-seconden is, gebruikt u een verzendtime-out tussen N en 2 N seconden; Als de mp4-fragmentduur bijvoorbeeld 6 seconden is, gebruikt u een time-out van 6 tot 12 seconden. Als er een time-out optreedt, stelt u de verbinding opnieuw in, opent u een nieuwe verbinding en hervat u de streaming op de nieuwe verbinding. 
1. Onderhoud een rollende buffer met de laatste twee fragmenten voor elk nummer die met succes en volledig naar de service zijn verzonden.  Als het HTTP POST-verzoek om een stream wordt beëindigd of een time-out uitloopt voorafgaand aan het einde van de stream, opent u een nieuwe verbinding en begint u een ander HTTP POST-verzoek, verzendt u de streamkoppen opnieuw, verzendt u de laatste twee fragmenten voor elke track opnieuw en hervat u de stream zonder een discontinuïteit in de mediatijdlijn. Dit vermindert de kans op gegevensverlies.
1. We raden de encoder aan het aantal nieuwe pogingen om een verbinding tot stand te brengen of streaming te hervatten na een TCP-fout niet te beperken.
1. Na een TCP-fout:
  
    a. De huidige verbinding moet worden gesloten en er moet een nieuwe verbinding worden gemaakt voor een nieuwe HTTP POST-aanvraag.

    b. De nieuwe HTTP POST URL MOET hetzelfde zijn als de oorspronkelijke URL van POST.
  
    c. De nieuwe HTTP POST MOET streamheaders **(ftyp,** **Live Server Manifest Box**en **moov** boxes) bevatten die identiek zijn aan de streamheaders in de eerste POST.
  
    d. De laatste twee fragmenten die voor elk nummer worden verzonden, moeten worden verweten en de streaming moet worden hervat zonder een discontinuïteit in de mediatijdlijn in te voeren. De MP4-fragmenttijdstempels moeten voortdurend toenemen, zelfs voor HTTP POST-aanvragen.
1. De encoder MOET de HTTP POST-aanvraag beëindigen als gegevens niet worden verzonden tegen een snelheid die in verhouding staat tot de mp4-fragmentduur.  Een HTTP POST-verzoek dat geen gegevens verzendt, kan voorkomen dat Media Services snel de verbinding met de encoder verbreekt in het geval van een service-update. Om deze reden moet de HTTP POST voor schaarse (advertentiesignaal) tracks van korte duur zijn, eindigend zodra het schaarse fragment wordt verzonden.

## <a name="8-encoder-failover"></a>8. Encoder failover
Encoder failover is het tweede type failover scenario dat moet worden aangepakt voor end-to-end live streaming levering. In dit scenario treedt de foutvoorwaarde op aan de encoderzijde. 

![encoder failover][image5]

De volgende verwachtingen gelden vanaf het eindpunt voor live inname wanneer encoderfailover plaatsvindt:

1. Een nieuwe encoder instantie MOET worden gemaakt om te blijven streamen, zoals geïllustreerd in het diagram (Stream voor 3000k video, met stippellijn).
1. De nieuwe encoder MOET dezelfde URL gebruiken voor HTTP POST-aanvragen als de mislukte instantie.
1. De post-aanvraag van de nieuwe encoder moet dezelfde gefragmenteerde MP4-koptekstvakken bevatten als de mislukte instantie.
1. De nieuwe encoder moet goed worden gesynchroniseerd met alle andere lopende encoders voor dezelfde live presentatie om gesynchroniseerde audio / video samples met uitgelijnde fragment grenzen te genereren.
1. De nieuwe stream moet semantisch gelijkwaardig zijn met de vorige stream en uitwisselbaar op de kop- en fragmentniveaus.
1. De nieuwe encoder moet proberen om gegevensverlies te minimaliseren. De `fragment_absolute_time` `fragment_index` en van de media fragmenten moeten toenemen vanaf het punt waar de encoder voor het laatst gestopt. De `fragment_absolute_time` `fragment_index` en moet toenemen op een continue manier, maar het is toegestaan om een discontinuïteit in te voeren, indien nodig. Media Services negeert fragmenten die het al heeft ontvangen en verwerkt, dus het is beter om te vergissen aan de kant van het opnieuw verzenden van fragmenten dan om discontinuïteiten in de media tijdlijn te introduceren. 

## <a name="9-encoder-redundancy"></a>9. Ontslag van encoder
Voor bepaalde kritieke live-gebeurtenissen die een nog hogere beschikbaarheid en kwaliteit van ervaring vereisen, raden we u aan actieve actieve redundante encoders te gebruiken om een naadloze failover te bereiken zonder gegevensverlies.

![encoder redundantie][image6]

Zoals geïllustreerd in dit diagram, twee groepen van encoders duwen twee exemplaren van elke stream tegelijk in de live service. Deze instelling wordt ondersteund omdat Media Services dubbele fragmenten kan filteren op basis van stream-ID en fragmenttijdstempel. De resulterende live stream en archief is een enkele kopie van alle streams die de best mogelijke aggregatie van de twee bronnen. Bijvoorbeeld, in een hypothetisch e-end, zolang er een encoder (het hoeft niet dezelfde te zijn) draait op een bepaald punt in de tijd voor elke stream, de resulterende live stream van de dienst is continu zonder verlies van gegevens. 

De vereisten voor dit scenario zijn bijna hetzelfde als de vereisten in de case "Encoder failover", met uitzondering van het feit dat de tweede set encoders tegelijkertijd met de primaire encoders wordt uitgevoerd.

## <a name="10-service-redundancy"></a>10. Serviceredundantie
Voor zeer redundante wereldwijde distributie, soms moet je cross-region back-up om regionale rampen te behandelen. In de uitbreiding van de topologie van de "Encoder redundantie" kunnen klanten ervoor kiezen om een redundante service-implementatie te hebben in een andere regio die is verbonden met de tweede set encoders. Klanten kunnen ook samenwerken met een content delivery network-provider om een Global Traffic Manager te implementeren voor de twee service-implementaties om het clientverkeer naadloos te routeren. De vereisten voor de encoders zijn hetzelfde als de "Encoder redundantie" geval. De enige uitzondering is dat de tweede set encoders moet worden gewezen op een ander live endpoint. In het volgende diagram ziet u de volgende instelling:

![serviceredundantie][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Speciale soorten innameformaten
In dit gedeelte worden speciale typen live-opnameformaten besproken die zijn ontworpen om specifieke scenario's te verwerken.

### <a name="sparse-track"></a>Schaars spoor
Bij het leveren van een live streaming presentatie met een rijke klantervaring, is het vaak noodzakelijk om tijdgesynchroniseerde gebeurtenissen of signalen in-band met de belangrijkste mediagegevens te verzenden. Een voorbeeld hiervan is dynamische live advertentie-invoeging. Dit type gebeurtenissignalering is anders dan reguliere audio/video streaming vanwege het schaarse karakter. Met andere woorden, de signaleringsgegevens gebeuren meestal niet continu en het interval kan moeilijk te voorspellen zijn. Het concept van spaarzame track is ontworpen om in-band signaleringsgegevens in te nemen en uit te zenden.

De volgende stappen zijn een aanbevolen implementatie voor het innemen van schaarse track:

1. Maak een afzonderlijke gefragmenteerde MP4-bitstream die alleen schaarse tracks bevat, zonder audio/videotracks.
1. Gebruik in het **manifestvak Live Server** zoals gedefinieerd in sectie 6 in [1], de parameter *parentTrackName* om de naam van het bovenliggende spoor op te geven. Zie punt 4.2.1.2.1.2 voor meer informatie in [1].
1. In het **manifestvak live server**moet **manifestOutput** worden ingesteld op **true**.
1. Gezien de schaarse aard van de signaleringsgebeurtenis, hebben we het volgende aanbevolen:
   
    a. Aan het begin van het live-evenement stuurt de encoder de eerste koptekstvakken naar de service, waardoor de service het schaarse spoor in het clientmanifest kan registreren.
   
    b. De encoder MOET het HTTP POST-verzoek beëindigen wanneer er geen gegevens worden verzonden. Een langlopende HTTP-POST die geen gegevens verzendt, kan voorkomen dat Media Services snel de verbinding met de encoder verbreekt in het geval van een service-update of het opnieuw opstarten van de server. In deze gevallen wordt de mediaserver tijdelijk geblokkeerd in een ontvangstbewerking op de socket.
   
    c. Gedurende het moment dat er geen signaalgegevens beschikbaar zijn, moet de encoder de HTTP POST-aanvraag sluiten. Terwijl de POST-aanvraag actief is, moet de encoder gegevens verzenden.

    d. Bij het verzenden van schaarse fragmenten kan de encoder een expliciete inhoudslengte-header instellen, als deze beschikbaar is.

    e. Bij het verzenden van schaarse fragmenten met een nieuwe verbinding, moet de encoder beginnen met verzenden vanuit de kopvakken, gevolgd door de nieuwe fragmenten. Dit is voor gevallen waarin failover gebeurt in-between, en de nieuwe schaarse verbinding wordt vastgesteld op een nieuwe server die niet heeft gezien de schaarse track voor.

    f. Het schaarse spoorfragment wordt beschikbaar voor de client wanneer het bijbehorende bovenliggende spoorfragment met een gelijke of grotere tijdstempelwaarde beschikbaar wordt gesteld aan de client. Als het schaarse fragment bijvoorbeeld een tijdstempel van t=1000 heeft, wordt verwacht dat nadat de client "video" (ervan uitgaande dat de naam van de bovenliggende track "video" is) fragmenttijdstempel 1000 of verder, het schaarse fragment t=1000 kan downloaden. Houd er rekening mee dat het werkelijke signaal kan worden gebruikt voor een andere positie in de presentatietijdlijn voor het aangewezen doel. In dit voorbeeld is het mogelijk dat het schaarse fragment van t=1000 een XML-payload heeft, wat is voor het invoegen van een advertentie in een positie die een paar seconden later is.

    g. De payload van schaarse spoorfragmenten kan in verschillende indelingen (zoals XML, tekst of binair) zijn, afhankelijk van het scenario.

### <a name="redundant-audio-track"></a>Redundante audiotrack
In een typisch HTTP-adaptief streamingscenario (bijvoorbeeld Smooth Streaming of DASH) is er vaak slechts één audiotrack in de hele presentatie. In tegenstelling tot videotracks, die meerdere kwaliteitsniveaus hebben waar de client uit kan kiezen in foutomstandigheden, kan de audiotrack een single point of failure zijn als de opname van de stream die de audiotrack bevat, is verbroken. 

Om dit probleem op te lossen, ondersteunt Media Services live opname van redundante audiotracks. Het idee is dat dezelfde audiotrack meerdere keren in verschillende streams kan worden verzonden. Hoewel de service de audiotrack slechts eenmaal registreert in het clientmanifest, kan deze redundante audiotracks gebruiken als back-ups voor het ophalen van audiofragmenten als de primaire audiotrack problemen heeft. Om redundante audiotracks in te nemen, moet de encoder:

1. Maak dezelfde audiotrack in meerdere fragment-MP4-bitstreams. De redundante audiotracks moeten semantisch gelijkwaardig zijn, met dezelfde fragmenttijdstempels, en uitwisselbaar zijn op kop- en fragmentniveaus.
1. Controleer of de vermelding 'audio' in het Live Server Manifest (sectie 6 in [1]) hetzelfde is voor alle redundante audiotracks.

De volgende implementatie wordt aanbevolen voor redundante audiotracks:

1. Stuur elke unieke audiotrack in een stream zelf. Stuur ook een redundante stream voor elk van deze audiotrackstreams, waarbij de tweede stream alleen verschilt van de eerste door de id in de HTTP POST-URL: {protocol}://{server adres}/{publishing point path}/Streams({identifier}).
1. Gebruik afzonderlijke streams om de twee laagste videobitrates te verzenden. Elk van deze streams moet ook een kopie van elke unieke audio track bevatten. Wanneer bijvoorbeeld meerdere talen worden ondersteund, moeten deze streams audiotracks voor elke taal bevatten.
1. Gebruik afzonderlijke server -instanties (encoder) om de in (1) en 2 genoemde redundante streams te coderen en te verzenden). 

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
