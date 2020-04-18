---
title: Live streaming met Azure Media Services om multibitrate streams te maken | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe u een kanaal instelt dat een enkele bitrate-livestream ontvangt van een on-premises encoder en vervolgens live codering uitvoert naar adaptieve bitratestream met Media Services.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6210d6ee4877c6ba84178340cf0a6610e402da31
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641107"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Live streamen met Azure Media Services om multi-bitrate streams te maken

> [!NOTE]
> Vanaf 12 mei 2018 ondersteunen live kanalen niet langer het RTP/MPEG-2 transportstream ingest protocol. Migreer van RTP/MPEG-2 naar RTMP of gefragmenteerde MP4 -protocollen (Smooth Streaming).

## <a name="overview"></a>Overzicht
In Azure Media Services (AMS) vertegenwoordigt een **kanaal** een pijplijn voor het verwerken van live streaming-inhoud. Een **kanaal** ontvangt op twee manieren live-invoerstreams:

* Een on-premises live-encoder stuurt een stream met één bitrate naar het kanaal die is ingeschakeld om live codering met Media Services uit te voeren in een van de volgende indelingen: RTMP of Smooth Streaming (Fragmented MP4). Het kanaal codeert de inkomende single-bitrate stream vervolgens live naar een (adaptieve) multi-bitrate videostream. Desgevraagd levert Media Services de stream aan klanten.
* Een on-premises live encoder stuurt een multi-bitrate **RTMP** of **Smooth Streaming** (Fragmented MP4) naar het Kanaal dat niet is ingeschakeld om live codering met AMS uit te voeren. De ingenomen streams passeren **Kanaal**s zonder verdere verwerking. Deze methode wordt **pass-through**genoemd. U de volgende live-encoders gebruiken die multi-bitrate Smooth Streaming uitvoeren: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco en Elemental. De volgende live encoders output RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek encoders.  Een live coderingsprogramma kan ook een stream met één bitsnelheid verzenden naar een kanaal dat niet is ingeschakeld voor Live Encoding, maar dit wordt niet aanbevolen. Desgevraagd levert Media Services de stream aan klanten.

  > [!NOTE]
  > Het gebruik van een pass-through methode is de meest economische manier om live streaming te doen.
  > 
  > 

Vanaf de release mediaservices 2.10 u bij het maken van een kanaal opgeven op welke manier u wilt dat uw kanaal de invoerstream ontvangt en of u wilt dat het kanaal live codering van uw stream uitvoert. U hebt hiervoor twee opties:

* **Geen** : als u van plan bent een on-premises live-encoder te gebruiken die multibitrate stream (een pass-through stream) zal produceren. In dit geval is de binnenkomende stroom doorgegeven aan de uitgang zonder codering. Dit is het gedrag van een kanaal voorafgaand aan 2.10 release.  Zie [Live streaming met on-premises encoders die multibitrate streams maken](media-services-live-streaming-with-onprem-encoders.md)voor meer gedetailleerde informatie over het werken met kanalen van dit type.
* **Standaard** : kies deze waarde als u van plan bent Media Services te gebruiken om uw livestream te coderen om een stream met meerdere bitrate's te streamen. Houd er rekening mee dat er een factureringseffect is voor live codering en u moet niet vergeten dat het verlaten van een live coderingskanaal in de status 'Uitvoeren' rekening zal houden met facturering.  Het wordt aanbevolen dat u onmiddellijk stopt met uw hardloopkanalen nadat uw live streaming-evenement is voltooid om extra uurkosten te voorkomen.

> [!NOTE]
> In dit onderwerp worden kenmerken van kanalen besproken die**Standard** zijn ingeschakeld om live codering uit te voeren ( Standaardcoderingstype). Zie [Live streaming met on-premises coderingen die multibitrate streams maken](media-services-live-streaming-with-onprem-encoders.md)voor informatie over het werken met kanalen die niet zijn ingeschakeld om live codering uit te voeren.
> 
> Controleer de sectie [Overwegingen.](media-services-manage-live-encoder-enabled-channels.md#Considerations)
> 
> 

## <a name="billing-implications"></a>Implicaties voor facturering
Een live coderingskanaal begint met factureren zodra de status wordt overgezet naar 'Uitvoeren' via de API.   U de status ook weergeven in de Azure-portalhttps://aka.ms/amse)of in het Azure Media Services Explorer-hulpprogramma (.

In de volgende tabel ziet u hoe kanaalstaten toewijzen aan factureringsstatussen in de API- en Azure-portal. De toestanden zijn iets anders tussen de API en Portal UX. Zodra een kanaal zich in de status 'Actief' bevindt via de API of in de status 'Gereed' of 'Streaming' in de Azure-portal, is facturering actief.
Als u wilt voorkomen dat het Kanaal u verder factureert, moet u het kanaal stoppen via de API of in de Azure-portal.
U bent verantwoordelijk voor het stoppen van uw kanalen wanneer u klaar bent met de live codering kanaal.  Als u een coderingskanaal niet stopt, wordt de facturering voortgezet.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Kanaalstatussen en hoe ze worden toegewezen aan de factureringsmodus
De huidige toestand van een kanaal. Mogelijke waarden zijn:

* **Gestopt.** Dit is de oorspronkelijke status van het Kanaal na de creatie ervan (tenzij autostart is geselecteerd in de portal.) Er vindt geen facturering plaats in deze status. In deze status kunnen de kanaaleigenschappen worden bijgewerkt, maar is streamen niet toegestaan.
* **Vanaf**. Het kanaal wordt gestart. Er vindt geen facturering plaats in deze status. Tijdens deze status is geen updates of streaming toegestaan. Als er een fout optreedt, keert het kanaal terug naar de status Gestopt.
* **Hardlopen**. Het Kanaal is in staat om live streams te verwerken. Het is nu facturering gebruik. U moet het kanaal stoppen om verdere facturering te voorkomen. 
* **Stoppen**. Het kanaal wordt tegengehouden. Er vindt geen facturering plaats in deze tijdelijke status. Tijdens deze status is geen updates of streaming toegestaan.
* **Verwijderen**. Het kanaal wordt verwijderd. Er vindt geen facturering plaats in deze tijdelijke status. Tijdens deze status is geen updates of streaming toegestaan.

In de volgende tabel ziet u hoe kanaaltoestanden worden toegewezen aan de factureringsmodus. 

| Kanaalstaat | Portal-gebruikersinterface-indicatoren | Is het Billing? |
| --- | --- | --- |
| Starten |Starten |Geen (tijdelijke status) |
| In uitvoering |Klaar (geen lopende programma's)<br/>of<br/>Streaming (ten minste één lopend programma) |JA |
| Stoppen |Stoppen |Geen (tijdelijke status) |
| Gestopt |Gestopt |Nee |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatisch uitschakelen voor ongebruikte kanalen
Vanaf 25 januari 2016 heeft Media Services een update uitgerold die automatisch een kanaal stopt (met live codering ingeschakeld) nadat deze lange tijd in een ongebruikte status is uitgevoerd. Dit geldt voor kanalen die geen actieve programma's hebben en die gedurende een langere periode geen invoerbijdragefeed hebben ontvangen.

De drempel voor een ongebruikte periode is nominaal 12 uur, maar is onderhevig aan verandering.

## <a name="live-encoding-workflow"></a>Live coderingswerkstroom
Het volgende diagram vertegenwoordigt een live streaming workflow waarbij een kanaal een enkele bitrate stream ontvangt in een van de volgende protocollen: RTMP of Smooth Streaming; het codeert vervolgens de stroom naar een multi-bitrate stream. 

![Live-werkstroom][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Gemeenschappelijk scenario voor live streaming
Hieronder volgen de algemene stappen voor het maken van veelvoorkomende toepassingen voor live streamen.

> [!NOTE]
> De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur.
>
> Er is een factureringseffect voor live codering en u moet niet vergeten dat het verlaten van een live coderingskanaal in de status 'Lopend' per uur rekening houdt met facturering. Het wordt aanbevolen dat u onmiddellijk stopt met uw hardloopkanalen nadat uw live streaming-evenement is voltooid om extra uurkosten te voorkomen. 

1. Sluit een videocamera aan op een computer. Start en configureer een on-premises live encoder die een **enkele** bitrate stream kan uitvoeren in een van de volgende protocollen: RTMP of Smooth Streaming. 

    Deze stap kan ook worden uitgevoerd nadat u uw kanaal hebt gemaakt.
2. Maak en start een kanaal. 
3. Haal de URL voor opnemen voor het kanaal op. 

    De URL voor opnemen wordt gebruikt door het live coderingsprogramma om de stream naar het kanaal te verzenden.
4. Haal de voorbeeld-URL voor het kanaal op. 

    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.
5. Maak een programma. 

    Wanneer u de Azure-portal gebruikt, maakt het maken van een programma ook een asset. 

    Wanneer u .NET SDK of REST gebruikt, moet u een asset maken en opgeven om dit element te gebruiken bij het maken van een programma. 
6. Publiceer de asset die aan het programma is gekoppeld.   

    >[!NOTE]
    >Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Het streaming-eindpunt van waar u inhoud wilt streamen, moet de status **Wordt uitgevoerd** hebben. 

7. Start het programma wanneer u klaar bent om te streamen en te archiveren.
8. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.
9. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.
10. Verwijder het programma (en verwijder desgewenst de asset).   

> [!NOTE]
> Het is erg belangrijk om niet te vergeten om een Live Encoding Channel te stoppen. Houd er rekening mee dat er een factureringseffect per uur is voor live codering en u moet niet vergeten dat het verlaten van een live coderingskanaal in de status 'Uitvoeren' rekening zal worden gebracht met facturering.  Het wordt aanbevolen dat u onmiddellijk stopt met uw hardloopkanalen nadat uw live streaming-evenement is voltooid om extra uurkosten te voorkomen. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>De invoerconfiguraties (inname) van het kanaal
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Inname streaming protocol
Als het **encodertype** is ingesteld op **Standaard,** zijn geldige opties:

* Enkele bitrate **RTMP**
* Single bitrate **Fragmented MP4** (Smooth Streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>Single-bitrate RTMP;
Overwegingen:

* De binnenkomende stream kan geen multibitrate video bevatten
* De videostream moet een gemiddelde bitrate hebben van minder dan 15 Mbps
* De audiostream moet een gemiddelde bitrate van minder dan 1 Mbps hebben
* Hieronder volgen de ondersteunde codecs:
* MPEG-4 AVC / H.264 Video
* Basislijn, Hoofd- en hoogprofiel (8-bits 4:2:0)
* Hoog 10-profiel (10-bits 4:2:0)
* Hoog 422-profiel (10-bits 4:2:2)
* MPEG-2 AAC-LC Audio
* Mono, Stereo, Surround (5.1, 7.1)
* 44,1 kHz bemonsteringssnelheid
* MPEG-2 stijl ADTS verpakking
* Aanbevolen encoders zijn:
* [Telestream-wirecast](media-services-configure-wirecast-live-encoder.md)
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Single-bitrate Fragmented MP4 (Smooth Streaming);
Typische use case:

Gebruik on-premises live encoders van leveranciers zoals Elemental Technologies, Ericsson, Ateme, Envivio om de invoerstroom via het open internet naar een nabijgelegen Azure-datacenter te verzenden.

Overwegingen:

Hetzelfde als voor [enkele bitrate RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Andere overwegingen
* U kunt het invoerprotocol niet wijzigen terwijl het kanaal of de gekoppelde programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* De maximale resolutie voor de inkomende videostream is 1920x1080 en maximaal 60 velden per seconde als deze doorweven zijn, of 30 frames per seconde als deze progressief zijn.

### <a name="ingest-urls-endpoints"></a>Inname van URL's (eindpunten)
Een kanaal biedt een invoereindpunt (inname VAN URL) dat u opgeeft in de live-encoder, zodat de encoder streams naar uw kanalen kan pushen.

Je de inname URL's krijgen zodra je een kanaal hebt gemaakt. Om deze URL's te krijgen, hoeft het kanaal niet in de **status Actief** te zijn. Wanneer u klaar bent om gegevens in het kanaal te duwen, moet deze in de **status Actief zijn.** Zodra het kanaal gegevens begint in te nemen, u een voorbeeld van uw stream bekijken via de url van het voorbeeld.

Je hebt een optie om gefragmenteerde MP4 (Smooth Streaming) live stream in te nemen via een TLS-verbinding. Als u TLS wilt innemen, moet u de inname van DE URL bijwerken naar HTTPS. Op dit moment ondersteunt AMS TLS niet met aangepaste domeinen.  

### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U de IP-adressen definiëren die video op dit kanaal mogen publiceren. Toegestane IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld '10.0.0.1'), een IP-bereik met behulp van een IP-adres en een CIDR-subnetmasker (bijvoorbeeld '10.0.0.1/22'), of een IP-bereik met een IP-adres en een gestippeld subnetmasker (bijvoorbeeld '10.0.0.1(255.255.252.0)).

Als geen IP-adressen zijn opgegeven en er geen regeldefinitie bestaat, zijn er geen IP-adressen toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.

## <a name="channel-preview"></a>Kanaalvoorbeeld
### <a name="preview-urls"></a>Url's bekijken
Kanalen bieden een voorbeeldeindpunt (preview-URL) dat u gebruikt om uw stream te bekijken en te valideren voordat u verder wordt verwerkt en geleverd.

U de url van de preview ophalen wanneer u het kanaal maakt. Om de URL te krijgen, hoeft het kanaal niet in de **status Actief** te zijn.

Zodra het kanaal gegevens begint in te nemen, kun je een voorbeeld van je stream bekijken.

> [!NOTE]
> Momenteel kan de previewstream alleen worden geleverd in de indeling Fragmented MP4 (Smooth Streaming), ongeacht het opgegeven invoertype.  U een speler die in de Azure-portal wordt gehost, gebruiken om uw stream te bekijken.
> 
> 

### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U de IP-adressen definiëren die verbinding mogen maken met het voorbeeldeindpunt. Als er geen IP-adressen zijn opgegeven, is een IP-adres toegestaan. Toegestane IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld '10.0.0.1'), een IP-bereik met behulp van een IP-adres en een CIDR-subnetmasker (bijvoorbeeld '10.0.0.1/22'), of een IP-bereik met een IP-adres en een gestippeld subnetmasker (bijvoorbeeld '10.0.0.1(255.255.252.0)).

## <a name="live-encoding-settings"></a>Live coderingsinstellingen
In deze sectie wordt beschreven hoe de instellingen voor de live-encoder in het kanaal kunnen worden aangepast, wanneer het **coderingstype** van een kanaal is ingesteld op **Standaard.**

> [!NOTE]
> Uw bijdragefeed kan slechts één audiotrack bevatten: het innemen van meerdere audiotracks wordt momenteel niet ondersteund. Wanneer u live codering met [on-premises live codeert,](media-services-live-streaming-with-onprem-encoders.md)u een bijdragefeed verzenden in het Smooth Streaming-protocol met meerdere audiotracks.
> 
> 

### <a name="ad-marker-source"></a>Bron van advertentiemarkeertekens
U de bron voor signalen van advertentiemarkeertekens opgeven. Standaardwaarde is **Api**, wat aangeeft dat de levende encoder in het kanaal moet luisteren naar een asynchrone **Ad Marker API**.

### <a name="cea-708-closed-captions"></a>CEA 708 ondertiteling
Een optionele vlag die de levende encoder vertelt om cea 708-bijschriften gegevens te negeren die zijn ingesloten in de binnenkomende video. Wanneer de vlag is ingesteld op false (default), detecteert en voegt de encoder CEA 708-gegevens in de uitvoervideostreams.

#### <a name="index"></a>Index
Het wordt aanbevolen om één programma transportstream (SPTS) in te sturen. Als de invoerstream meerdere programma's bevat, onteert de live-encoder binnen het Kanaal de Program Map Table (PMT) in de invoer, identificeert de ingangen met een streamtypenaam van MPEG-2 AAC ADTS of AC-3 System-A of AC-3 System-B of MPEG-2 Private PES of MPEG-1 Audio of MPEG-2 Audio en rangschikt deze in de volgorde die in de PMT is opgegeven. De nul-gebaseerde index wordt vervolgens gebruikt om de n-th entry in die regeling op te halen.

#### <a name="language"></a>Taal
De taal-id van de audiostream, die overeenkomt met ISO 639-2, zoals ENG. Als deze niet aanwezig is, is de standaardinstelling UND (niet gedefinieerd).

### <a name="system-preset"></a><a id="preset"></a>Systeemvoorinstelling
Hiermee geeft u de voorinstelling op die moet worden gebruikt door de levende encoder in dit kanaal. Momenteel is de enige toegestane waarde **Default720p** (standaard).

**Default720p** codeert de video in de volgende 6 lagen.

#### <a name="output-video-stream"></a>Videostream uitvoeren

| Bitrate | Breedte | Height | MaxFPS (MaxFPS) | Profiel | Naam uitvoerstroom |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hoog |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hoog |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hoog |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hoog |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hoog |Video_384x216_550kbps |
| 200 |340 |192 |30 |Hoog |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Audiostream uitvoeren

Audio is gecodeerd naar stereo OC-LC bij 128 kbps, sampling rate van 48 kHz.

## <a name="signaling-advertisements"></a>Signalering Advertenties
Wanneer je kanaal Live Encoding heeft ingeschakeld, heb je een component in je pijplijn die video verwerkt en deze manipuleren. U het kanaal signaleren om leien en/of advertenties in de uitgaande adaptieve bitratestream in te voegen. Leien zijn stilstaande beelden die u gebruiken om de invoer live feed te verdoezelen in bepaalde gevallen (bijvoorbeeld tijdens een commerciële pauze). Reclamesignalen, zijn tijdsgesynchroniseerde signalen die u inde inde uitgaande stream insluit om de videospeler te vertellen om speciale actie te ondernemen - bijvoorbeeld om op het juiste moment over te schakelen naar een advertentie. Zie deze [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) voor een overzicht van het SCTE-35 signaleringsmechanisme dat hiervoor wordt gebruikt. Hieronder vindt u een typisch scenario dat u implementeren in uw live-evenement.

1. Laat je kijkers een PRE-EVENT-afbeelding krijgen voordat het evenement begint.
2. Laat je kijkers een post-event-afbeelding krijgen nadat de gebeurtenis is afgelopen.
3. Laat uw kijkers een ERROR-EVENT-afbeelding krijgen als er een probleem is tijdens het evenement (bijvoorbeeld stroomuitval in het stadion).
4. Stuur een AD-BREAK-afbeelding om de live-gebeurtenisfeed tijdens een commerciële onderbreking te verbergen.

De volgende zijn de eigenschappen die u instellen bij het signaleren van advertenties. 

### <a name="duration"></a>Duur
De duur, in seconden, van de commerciële pauze. Dit moet een niet-nul positieve waarde zijn om de commerciële pauze te starten. Wanneer een commerciële onderbreking aan de gang is en de duur is ingesteld op nul met de CueId die overeenkomt met de doorlopende commerciële pauze, wordt die pauze geannuleerd.

### <a name="cueid"></a>CueId (CueId)
Een unieke id voor de commerciële onderbreking, die door downstream-toepassing moet worden gebruikt om passende actie(en) te ondernemen. Moet een positief geheel getal zijn. U deze waarde instellen op een willekeurig positief geheel getal of een upstream-systeem gebruiken om de Cue Id's te volgen. Zorg ervoor dat u id's normaliseert tot positieve gehele getallen voordat u deze indient via de API.

### <a name="show-slate"></a>Lei weergeven
Optioneel. Signaleert de live-encoder om tijdens een commerciële pauze over te schakelen naar het [standaardleibeeld](media-services-manage-live-encoder-enabled-channels.md#default_slate) en verbergt de binnenkomende videofeed. Audio wordt ook gedempt tijdens leisteen. Standaard is **false**. 

De gebruikte afbeelding is de afbeelding die is opgegeven via de eigenschap default slate asset Id op het moment van het maken van het kanaal. De lei wordt uitgerekt om de grootte van de weergaveafbeelding te passen. 

## <a name="insert-slate--images"></a>Leiafbeeldingen invoegen
De levende encoder binnen het Kanaal kan worden gesignaleerd om over te schakelen naar een leibeeld. Het kan ook worden gesignaleerd om een lopende leisteen te beëindigen. 

De live encoder kan worden geconfigureerd om over te schakelen naar een leibeeld en het binnenkomende videosignaal in bepaalde situaties te verbergen , bijvoorbeeld tijdens een advertentieonderbreking. Als een dergelijke lei niet is geconfigureerd, wordt invoervideo niet gemaskeerd tijdens dat advertentieeinde.

### <a name="duration"></a>Duur
De duur van de leisteen in seconden. Dit moet een niet-nul positieve waarde zijn om de lei te starten. Als er een lopende leisteen is en een duur van nul wordt opgegeven, wordt die lopende lei beëindigd.

### <a name="insert-slate-on-ad-marker"></a>Lei invoegen op advertentiemarkering
Wanneer deze instelling is ingesteld op true, configureert deze instelling de live-encoder om een leiafbeelding in te voegen tijdens een advertentieonderbreking. De standaardwaarde is waar. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Standaard-bedrijfsnaam

Optioneel. Hiermee geeft u de asset-id op van het mediaservices-element dat de leiafbeelding bevat. Standaard is null. 


> [!NOTE] 
> Voordat u het kanaal maakt, moet de leiafbeelding met de volgende beperkingen worden geüpload als een specifiek actief (er mogen geen andere bestanden in dit actief staan). Deze afbeelding wordt alleen gebruikt wanneer de levende encoder een lei invoegt als gevolg van een advertentieonderbreking, of expliciet is gesignaleerd om een lei in te voegen. Er is momenteel geen optie om een aangepaste afbeelding te gebruiken wanneer de levende encoder zo'n 'invoersignaal verloren' staat invoert. U stemmen voor deze functie [hier](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* Hoogstens 1920x1080 in resolutie.
* Hooguit 3 Mbytes groot.
* De bestandsnaam moet een extensie *.jpg hebben.
* De afbeelding moet worden geüpload naar een asset als de enige AssetFile in dat actief en dit AssetFile moet worden gemarkeerd als het primaire bestand. Het item kan niet versleuteld worden opgeslagen.

Als de **standaard-lei Asset Id** niet is opgegeven en **de lei invoegen op de advertentiemarkering** is ingesteld op **true,** wordt een standaard Azure Media Services-afbeelding gebruikt om de invoervideostream te verbergen. Audio wordt ook gedempt tijdens leisteen. 

## <a name="channels-programs"></a>Programma's van het kanaal
Een kanaal is gekoppeld aan programma's waarmee u het publiceren en opslaan van segmenten in een live stream kunt beheren. Kanalen beheren programma's. De kanaal- en programmarelatie is zeer vergelijkbaar met traditionele media waar een kanaal een constante stroom van inhoud heeft en een programma is ondergebracht bij een gebeurtenis met een bepaalde tijd op dat kanaal.

U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de lengte voor **Archiefvenster** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur. Archiefvenster lengte bepaalt ook het maximale aantal tijd dat klanten terug kunnen zoeken in de tijd van de huidige live positie. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elk programma is gekoppeld aan een asset die de gestreamde inhoud opslaat. Een asset wordt toegewezen aan een blokblobcontainer in het Azure Storage-account en de bestanden in het asset worden opgeslagen als blobs in die container. Als u het programma wilt publiceren zodat uw klanten de stream kunnen bekijken, moet u een OnDemand-locator voor het bijbehorende item maken. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig lopende programma's, zodat u meerdere archieven van dezelfde binnenkomende stream maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

Gebruik bestaande programma's niet voor nieuwe gebeurtenissen. Maak en start in plaats daarvan een nieuw programma voor elke gebeurtenis zoals beschreven in de sectie Live Streaming Applications programmeren.

Start het programma wanneer u klaar bent om te streamen en te archiveren. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren. 

Als u de gearchiveerde inhoud wilt verwijderen, stopt en verwijdert u het programma en verwijdert u vervolgens de gekoppelde asset. Een asset kan niet worden verwijderd als deze wordt gebruikt door een programma. U moet eerst het programma verwijderen. 

Zelfs na het stoppen en verwijderen van het programma kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, mits u de asset niet hebt verwijderd.

Als u de gearchiveerde inhoud wilt behouden maar deze niet langer voor streaming beschikbaar mag zijn, verwijdert u de streaming-locator.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Een miniatuurvoorbeeld van een live feed krijgen
Wanneer Live Encoding is ingeschakeld, u nu een voorbeeld van de live-feed krijgen zodra deze het Kanaal bereikt. Dit kan een waardevol hulpmiddel zijn om te controleren of je live feed daadwerkelijk het Kanaal bereikt. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Kanaaltoestanden en hoe staten worden toegewezen aan de factureringsmodus
De huidige toestand van een kanaal. Mogelijke waarden zijn:

* **Gestopt.** Dit is de eerste staat van het Kanaal na de oprichting ervan. In deze status kunnen de kanaaleigenschappen worden bijgewerkt, maar is streamen niet toegestaan.
* **Vanaf**. Het kanaal wordt gestart. Tijdens deze status is geen updates of streaming toegestaan. Als er een fout optreedt, keert het kanaal terug naar de status Gestopt.
* **Hardlopen**. Het Kanaal is in staat om live streams te verwerken.
* **Stoppen**. Het kanaal wordt tegengehouden. Tijdens deze status is geen updates of streaming toegestaan.
* **Verwijderen**. Het kanaal wordt verwijderd. Tijdens deze status is geen updates of streaming toegestaan.

In de volgende tabel ziet u hoe kanaaltoestanden worden toegewezen aan de factureringsmodus. 

| Kanaalstaat | Portal-gebruikersinterface-indicatoren | Gefactureerd? |
| --- | --- | --- |
| Starten |Starten |Geen (tijdelijke status) |
| In uitvoering |Klaar (geen lopende programma's)<br/>of<br/>Streaming (ten minste één lopend programma) |Ja |
| Stoppen |Stoppen |Geen (tijdelijke status) |
| Gestopt |Gestopt |Nee |

> [!NOTE]
> Momenteel is het kanaalbegingemiddelde ongeveer 2 minuten, maar soms kan het tot 20 + minuten duren. Kanaalresets kunnen tot 5 minuten duren.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Overwegingen
* Wanneer een kanaal van **standaard** codering type ervaart een verlies van input bron / bijdrage feed, het compenseert voor het door het vervangen van de bron video / audio met een fout lei en stilte. Het Kanaal blijft een lei uitzenden totdat de invoer/bijdragefeed wordt hervat. We raden aan om een live kanaal niet langer dan 2 uur in een dergelijke staat te laten. Buiten dat punt is het gedrag van het Kanaal bij invoeropnieuwverbinding niet gegarandeerd, noch is het gedrag ervan in reactie op een opdracht Opnieuw instellen. Je moet het kanaal stoppen, verwijderen en een nieuw kanaal maken.
* U kunt het invoerprotocol niet wijzigen terwijl het kanaal of de gekoppelde programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* Elke keer dat u de live encoder opnieuw configureert, roept u de **methode Opnieuw instellen** op het kanaal aan. Voordat u het kanaal reset, moet u het programma stoppen. Nadat u het kanaal hebt gereset, start u het programma opnieuw op.
* Een kanaal kan alleen worden gestopt als het actief is en alle programma's op het kanaal zijn gestopt.
* Standaard u slechts 5 kanalen toevoegen aan uw Media Services-account. Dit is een zacht quotum voor alle nieuwe accounts. Zie [Quota en beperkingen voor](media-services-quotas-and-limitations.md)meer informatie .
* U kunt het invoerprotocol niet wijzigen terwijl het kanaal of de gekoppelde programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* Je wordt alleen gefactureerd als je kanaal in de **status Actief** is. Zie voor meer informatie [deze](media-services-manage-live-encoder-enabled-channels.md#states) sectie.
* De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. 
* Zorg ervoor dat het streamingeindpunt is waarvan u inhoud wilt streamen in de **status Actief.**
* De codering preset maakt gebruik van de notie van "max frame rate" van 30 fps. Dus als de ingang 60fps/59.94i is, worden de invoerframes gedropt/ontvlechtd tot 30/29,97 fps. Als de invoer 50fps/50i is, worden de invoerframes gedropt/ontvlechtd tot 25 fps. Als de input 25 fps is, blijft de output op 25 fps.
* Vergeet niet om uw kanalen te stoppen wanneer u klaar bent. Als u dit niet doet, wordt de facturering voortgezet.

## <a name="known-issues"></a>Bekende problemen
* De opstarttijd van het kanaal is verbeterd tot gemiddeld 2 minuten, maar in tijden van toegenomen vraag kan het nog steeds 20+ minuten duren.
* Slate beelden moeten voldoen aan de [beperkingen die hier](media-services-manage-live-encoder-enabled-channels.md#default_slate)worden beschreven . Als u probeert een kanaal te maken met een standaardlei die groter is dan 1920x1080, wordt de aanvraag uiteindelijk foutloos.
* Nog een keer.... vergeet niet om je kanalen te stoppen als je klaar bent met streamen. Als u dit niet doet, wordt de facturering voortgezet.

## <a name="need-help"></a>Hebt u hulp nodig?

U een ondersteuningsticket openen door te navigeren naar [nieuw ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Volgende stap

Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Live Streaming-gebeurtenissen met Azure Media Services leveren](media-services-overview.md)

[Kanalen maken die live codering uitvoeren van een singe bitrate tot adaptieve bitrate stream met Portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Kanalen maken die live codering uitvoeren van een singe bitrate tot adaptieve bitrate stream met .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Kanalen beheren met REST API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Concepten voor Media Services](media-services-concepts.md)

[Azure Media Services gefragmenteerde MP4 Live Inest-specificatie](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

