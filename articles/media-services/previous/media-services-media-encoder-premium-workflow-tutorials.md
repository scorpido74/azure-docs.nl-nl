---
title: Zelfstudies voor geavanceerde Media Encoder Premium Workflow
description: Dit document bevat walkthroughs die laten zien hoe je geavanceerde taken uitvoert met Media Encoder Premium Workflow en ook hoe je complexe workflows maken met Workflow Designer.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 1ab70d56bd3def58d0e814035070cf027a88cd3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251009"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Zelfstudies voor geavanceerde Media Encoder Premium Workflow
## <a name="overview"></a>Overzicht
Dit document bevat walkthroughs die laten zien hoe werkstromen met **Workflow Designer**kunnen worden aangepast. U vindt de werkelijke workflow bestanden [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>Inhoudsopgave
De volgende onderwerpen komen aan bod:

* [MXF coderen in een enkele bitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Een nieuwe werkstroom starten](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [De invoer van mediabestand gebruiken](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Mediastreams inspecteren](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Een video-encoder toevoegen voor . MP4-bestandsgeneratie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [De audiostream coderen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexing Audio en Video streamt naar een MP4-container](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Het MP4-bestand schrijven](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Een Media Services-element maken vanuit het uitvoerbestand](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [De voltooide werkstroom lokaal testen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Encoding MXF into multibitrate MP4s - dynamic packaging enabled](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Een of meer extra MP4-uitvoer toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [De namen van de bestandsuitvoer configureren](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Een apart audiospoor toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Het toevoegen van het SMIL-bestand "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [MxF coderen in multibitrate MP4 - verbeterde blauwdruk](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Overzicht van werkstroom om te verbeteren
  * [Conventies voor bestandsnaamgeving](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Eigenschappen van onderdelen publiceren in de werkstroomroot](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Gegenereerde uitvoerbestandsnamen afhankelijk laten zijn van gepubliceerde eigenschapswaarden](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Miniaturen toevoegen aan mp4-uitvoer met meerdere bitrate](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Werkstroomoverzicht om miniaturen toe te voegen
  * [JPG-codering toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Omgaan met kleurruimteconversie](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [De miniaturen schrijven](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Fouten in een werkstroom detecteren](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Voltooide werkstroom](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Time-based trimmen van mp4-uitvoer met meerdere bitrate](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Werkstroomoverzicht om bijsnijden toe te voegen aan](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [De streamtrimmer gebruiken](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Voltooide werkstroom](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introductie van de scriptcomponent](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Scripting binnen een workflow: hallo wereld](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Frame-based trimmen van mp4-uitvoer met meerdere bitrate](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Overzicht van de blauwdruk om bijsnijden toe te voegen aan](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [XML voor de cliplijst gebruiken](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [De cliplijst wijzigen vanuit een gescripte component](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Een eigenschap ClippingEnabled gemak toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>MXF coderen in een enkele bitrate MP4
In deze sectie wordt uitgelegd hoe u één bitrate maakt. MP4-bestand met AAC-HE gecodeerdaudio van een . MXF-invoerbestand.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Een nieuwe werkstroom starten
Open Werkstroomontwerper en selecteer Bestand > nieuwe werkruimte > Transcode Blueprint

De nieuwe werkstroom toont drie elementen:

* Primaire bronbestand
* XML voor cliplijst
* Uitvoerbestand/-actief  

![Nieuwe coderingswerkstroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nieuwe coderingswerkstroom*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>De invoer van mediabestand gebruiken
Om het invoermediabestand te accepteren, begint men met het toevoegen van een component MediaFile Input. Als u een component aan de werkstroom wilt toevoegen, zoekt u deze in het zoekvak Repository en sleept u het gewenste item naar het ontwerpdeelvenster. Herhaal de actie voor de invoer van mediabestand en verbind de component Primaire bronbestand met de bestandsnaaminvoerpin van de invoerbestandsinvoer media.

![Invoer van verbonden mediabestanden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Invoer van verbonden mediabestanden*

Identificeer in eerste instantie een geschikt voorbeeldbestand dat u wilt gebruiken bij het ontwerpen van een aangepaste werkstroom. Klik hiervoor op de achtergrond van het ontwerpdeelvenster en zoek naar de eigenschap Primaire bronbestand in het deelvenster Eigenschap rechts. Klik op het mappictogram en selecteer het gewenste bestand voor het testen van de werkstroom. De component Mediabestandsinvoer inspecteert het bestand en vult de uitvoerpins in om de details van het voorbeeldbestand dat het heeft geïnspecteerd weer te geven.

![Ingevulde mediabestandsinvoer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Ingevulde mediabestandsinvoer*

Nu de invoer is ingevuld, is de volgende stap het instellen van uitvoercoderingsinstellingen. Net als bij de manier waarop het primaire bronbestand is geconfigureerd, configureert u nu de eigenschap Variabele uitvoermap, net eronder.

![Eigenschappen voor geconfigureerde invoer en uitvoer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Eigenschappen voor geconfigureerde invoer en uitvoer*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Mediastreams inspecteren
Vaak is het gewenst om te weten hoe de stream eruit ziet als het stroomt door de workflow. Als u een stream op elk punt in de werkstroom wilt inspecteren, klikt u op een uitvoer- of invoerpin op een van de onderdelen. Klik in dit geval op de uitvoerpin Niet-gecomprimeerde video van de invoer van mediabestanden. Er wordt een dialoogvenster geopend waarmee u de uitgaande video inspecteren.

![De uitvoerpin voor niet-gecomprimeerde video inspecteren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*De uitvoerpin voor niet-gecomprimeerde video inspecteren*

In dit geval laat het zien dat de video een 1920x1080-ingang bevat bij 24 frames per seconde in 4:2:2-sampling voor een video van bijna 2 minuten.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Een video-encoder toevoegen voor . MP4-bestandsgeneratie
Nu zijn een niet-gecomprimeerde video en meerdere niet-gecomprimeerde audio-uitvoerpins beschikbaar voor gebruik op de mediabestandsinvoer. Om de binnenkomende video te coderen, moet een coderingscomponent aan de werkstroom worden toegevoegd - in dit geval voor het genereren van . MP4-bestanden.

Als u de videostream naar H.264 wilt coderen, voegt u de component AVC Video Encoder toe aan het oppervlak van de ontwerper. Deze component neemt een videostream ontcomprimeerals invoer en levert een AVC gecomprimeerde videostream op de uitvoerpin.

![Niet-verbonden AVC-encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Niet-verbonden AVC-encoder*

De eigenschappen bepalen hoe de codering precies gebeurt. Laten we eens kijken naar enkele van de belangrijkste instellingen:

* Uitvoerbreedte en Uitvoerhoogte: bepaalt de resolutie van de gecodeerde video. In dit geval is 640x360 een goede setting.
* Frame rate: wanneer ingesteld op passthrough zal het gewoon de bron frame rate vast te stellen, is het mogelijk om dit wel overschrijven. Een dergelijke framerate conversie wordt niet motion-gecompenseerd.
* Profiel en niveau: bepaalt het AVC profiel en niveau. Als u gemakkelijk meer informatie wilt krijgen over de verschillende niveaus en profielen, klikt u op het vraagtekenpictogram op de component AVC Video Encoder en wordt op de Help-pagina meer details over elk van de niveaus weergegeven. Gebruik in dit voorbeeld Hoofdprofiel op niveau 3.2 (de standaardinstelling).
* Rate Control Mode en Bitrate (kbps): kies in dit scenario voor een constante bitrate (CBR) output bij 1200 kbps
* Videoformaat: geeft informatie over de VUI (Video Usability Information) die in de H.264-stream wordt geschreven (neveninformatie die door een decoder kan worden gebruikt om het scherm te verbeteren, maar niet essentieel om correct te decoderen):
* NTSC (typisch voor de VS of Japan, met behulp van 30 fps)
* PAL (typisch voor Europa, met 25 fps)
* GOP-groottemodus: stel de vaste GOP-grootte in voor onze doeleinden met een sleutelinterval van 2 seconden met gesloten GoPs. De instelling van 2 seconden zorgt voor compatibiliteit met de dynamische verpakking die Azure Media Services biedt.

Als u de AVC-encoder wilt voeden, sluit u de uitvoerpin Ongecomprimeerde video van de component Mediabestandsinvoer aan op de invoerpin Ongecomprimeerde video van de AVC-encoder.

![Verbonden AVC-encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Aangesloten AVC-hoofdcoder*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>De audiostream coderen
Op dit punt moet de oorspronkelijke niet-gecomprimeerde audiostream nog worden gecomprimeerd. Voeg voor compressie van de audiostream een AAC Encoder (Dolby) component toe aan de workflow.

![Niet-verbonden AVC-encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Niet-verbonden OC-encoder*

Nu is er een incompatibiliteit: er is slechts één niet-gecomprimeerde audio-invoerpin van de OC-encoder, terwijl de mediabestandsinvoer meer dan waarschijnlijk twee verschillende niet-gecomprimeerde audiostreams beschikbaar zal hebben: een voor het linkeraudiokanaal en een voor rechts. (Als je te maken hebt met surround sound, dat zijn zes kanalen.) Het is dus niet mogelijk om de audio van de Media File Input bron rechtstreeks aan te sluiten op de OC-audio-encoder. De OC-component verwacht een zogenaamde "interleaved" audiostream: een enkele stream die zowel de linker- als de rechterkanalen met elkaar heeft verbonden. Zodra we uit ons bronmediabestand weten dat audiotracks op welke positie in de bron staan, kunnen we een dergelijke interleaved audiostream genereren met de correct toegewezen luidsprekerposities voor links en rechts.

Ten eerste wil men een interleaved stream genereren van de vereiste bronaudiokanalen. De Audio Stream Interleaver component regelt dit voor ons. Voeg deze toe aan de werkstroom en verbind de audio-uitgangen van de mediabestandsinvoer erop.

![Verbonden audiostream-interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Verbonden audiostream-interleaver*

Nu we een interleaved audiostream hebben, hebben we nog steeds niet aangegeven waar we de linker- of rechterluidsprekerposities aan moeten toewijzen. Om dit te specificeren, kunnen we gebruik maken van de Speaker Position Assigner.

![Een toewijzingsfunctie voor luidsprekerpositie toevoegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Een toewijzingsfunctie voor luidsprekerpositie toevoegen*

Configureer de luidsprekerpositieassigner voor gebruik met een stereo-ingangsstream via een voorinstellingsfilter van Encoder van 'Aangepast' en de kanaalvoorinstelling genaamd "2.0 (L,R)." (Hiermee wordt de linkerluidsprekerpositie naar kanaal 1 en de rechterluidsprekerpositie naar kanaal 2 toegelaten.)

Sluit de uitgang van de Speaker Position Assigner aan op de ingang van de OC-encoder. Vertel vervolgens de OC-encoder om te werken met een "2.0 (L,R)" Channel Preset, zodat het weet om te gaan met stereoaudio als input.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing Audio en Video streamt naar een MP4-container
Gezien onze AVC gecodeerde video stream en onze OC gecodeerde audiostream, kunnen we beide vast te leggen in een . MP4 container. Het proces van het mengen van verschillende stromen in een enkele heet "multiplexing" (of "muxing"). In dit geval, we zijn interleaving de audio en de video streams in een samenhangend . MP4-pakket. De component die dit coördineert voor een . MP4 container heet de ISO MPEG-4 Multiplexer. Voeg er een toe aan het designoppervlak en sluit zowel de AVC Video Encoder als de OC Encoder aan op de ingangen.

![Verbonden MPEG4 Multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Verbonden MPEG4 Multiplexer*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Het MP4-bestand schrijven
Bij het schrijven van een uitvoerbestand wordt de component Bestandsuitvoer gebruikt. We kunnen dit aansluiten op de output van de ISO MPEG-4 Multiplexer, zodat de output wordt geschreven naar de schijf. Sluit hiervoor de uitvoerpin Van De Container (MPEG-4) aan op de invoerpin Van Schrijven van de bestandsuitvoer.

![Verbonden bestandsuitvoer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Verbonden bestandsuitvoer*

De bestandsnaam die wordt gebruikt, wordt bepaald door de eigenschap File. Hoewel die eigenschap kan worden hardcoded tot een bepaalde waarde, waarschijnlijk wil men deze instellen via een expressie in plaats daarvan.

Als u wilt dat de werkstroom automatisch de eigenschap uitvoerbestandsnaam van een expressie bepaalt, klikt u op de knop naast de bestandsnaam (naast het mappictogram). Selecteer in de vervolgkeuzelijst 'Expressie' en selecteer 'Expressie'. Dit brengt de uitdrukkingsredacteur ter wereld. Duidelijk de inhoud van de editor eerst.

![Functie-expressieeditor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Functie-expressieeditor*

Met de expressieeditor u elke letterlijke waarde invoeren, gemengd met een of meer variabelen. Variabelen beginnen met een dollarteken. Als je op de $ toets, de editor toont een drop-down box met een keuze van de beschikbare variabelen. In ons geval gebruiken we een combinatie van de variabele uitvoermap en de variabele van de basisinvoerbestandsnaam:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Expressieeditor ingevuld](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Expressieeditor ingevuld*

> [!NOTE]
> Als u een uitvoerbestand van uw coderingstaak in Azure wilt zien, moet u een waarde opgeven in de expressieeditor.
>
>

Wanneer u de expressie bevestigt door op ok te drukken, geeft het eigenschapvenster een voorbeeld van de waarde waarop de eigenschap Bestand op dit moment wordt opgelost.

![Bestandsexpressie lost uitvoerdir op](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Bestandsexpressie lost uitvoerdir op*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Een Media Services-element maken vanuit het uitvoerbestand
Hoewel we een MP4-uitvoerbestand hebben geschreven, moeten we nog steeds aangeven dat dit bestand behoort tot het uitvoerelement dat mediaservices genereert als gevolg van het uitvoeren van deze werkstroom. Hiervoor wordt het knooppunt Uitvoerbestand/Asset op het werkstroomcanvas gebruikt. Alle binnenkomende bestanden in dit knooppunt maken deel uit van de resulterende Azure Media Services-asset.

Verbind de component Bestandsuitvoer met de component Uitvoerbestand/Asset om de werkstroom te voltooien.

![Voltooide werkstroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Voltooide werkstroom*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>De voltooide werkstroom lokaal testen
Als u de werkstroom lokaal wilt testen, drukt u op de afspeelknop op de werkbalk bovenaan. Wanneer de werkstroom is voltooid, controleert u de uitvoer die is gegenereerd in de geconfigureerde uitvoermap. U ziet het voltooide MP4-uitvoerbestand dat is gecodeerd uit het MXF-invoerbronbestand.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Encoding MXF into MP4 - multibitrate dynamic packaging enabled
Deze walkthrough maakt een set van meerdere bitrate MP4-bestanden met OC gecodeerde audio van een enkele . MXF-invoerbestand.

Wanneer een multibitrate asset output gewenst is voor gebruik in combinatie met de Dynamic Packaging-functies aangeboden door Azure Media Services, moeten meerdere GOP-aligned MP4-bestanden van elk een andere bitrate en resolutie worden gegenereerd. Om dit te doen, de [Encoding MXF in een enkele bitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) walkthrough biedt ons een goed uitgangspunt.

![Werkstroom starten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Werkstroom starten*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Een of meer extra MP4-uitvoer toevoegen
Elk MP4-bestand in onze resulterende Azure Media Services-asset ondersteunt een andere bitrate en resolutie. Laten we een of meer MP4-uitvoerbestanden toevoegen aan de werkstroom.

Om ervoor te zorgen dat we al onze video-encoders gemaakt met dezelfde instellingen, is het het handigst om de reeds bestaande AVC Video Encoder te dupliceren en een andere combinatie van resolutie en bitrate te configureren (laten we een van 960 x 540 toevoegen met 25 frames per seconde op 2,5 Mbps ). Als u de bestaande encoder wilt dupliceren, kopieert u de plak op het oppervlak van de ontwerper.

Sluit de uitvoerpin Ongecomprimeerde video van de mediabestandsinvoer aan op onze nieuwe AVC-component.

![Tweede AVC-encoder aangesloten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Tweede AVC-encoder aangesloten*

Pas nu de configuratie voor onze nieuwe AVC-encoder aan om 960x540 met 2,5 Mbps uit te brengen. (Gebruik hiervoor de eigenschappen 'Uitvoerbreedte', 'Uitvoerhoogte' en 'Bitrate (kbps)".)

Aangezien we de resulterende asset samen met de dynamische verpakking van Azure Media Services willen gebruiken, moet het streaming-eindpunt in staat zijn om uit deze MP4-bestanden HLS/Fragmentarische MP4/DASH-fragmenten te genereren die precies op elkaar zijn afgestemd op een manier dat clients die schakelen tussen verschillende bitrates één vloeiende continue video- en audio-ervaring krijgen. Om dat mogelijk te maken, moeten we ervoor zorgen dat, in de eigenschappen van zowel AVC encoders de GOP ("groep van foto's") grootte voor beide MP4-bestanden is ingesteld op 2 seconden, die kan worden gedaan door:

* de GOP-groottemodus instellen op vaste GOP-grootte en
* het hoofdframeinterval tot twee seconden.
* stel ook de GOP IDR Control in op Gesloten GOP om ervoor te zorgen dat alle GoPs op zichzelf staan zonder afhankelijkheden

Om deze workflow begrijpelijker te maken, wijzigt u de naam van de eerste AVC-encoder naar "AVC Video Encoder 640x360 1200 kbps" en de tweede AVC-encoder "AVC Video Encoder 960x540 2500 kbps."

Voeg nu een tweede ISO MPEG-4 Multiplexer en een tweede Bestandsuitvoer toe. Sluit de multiplexer aan op de nieuwe AVC-encoder en zorg ervoor dat de uitvoer ervan wordt geleid naar de bestandsuitvoer. Sluit vervolgens ook de OC-audio-encoder-uitvoer aan op de ingang van de nieuwe multiplexer. De bestandsuitvoer kan vervolgens worden gekoppeld aan het uitvoerbestand/het assetknooppunt om het toe te voegen aan het mediaservices-element dat wordt gemaakt.

![Tweede muxer en bestandsuitvoer verbonden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Tweede muxer en bestandsuitvoer verbonden*

Voor compatibiliteit met de dynamische verpakking van Azure Media Services configureert u de chunk-modus van de multiplexer op GOP-telling of -duur en stelt u de GoP's per stuk in op 1. (Dit moet de standaard.)

![Muxer Chunk-modi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer Chunk-modi*

Opmerking: u dit proces herhalen voor alle andere bitrate- en resolutiecombinaties die u aan de uitvoer van de activa wilt hebben toegevoegd.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>De namen van de bestandsuitvoer configureren
We hebben meer dan één bestand toegevoegd aan het uitvoeritem. Dit biedt een noodzaak om ervoor te zorgen dat de bestandsnamen voor elk van de uitvoerbestanden van elkaar verschillen en misschien zelfs een bestandsnaamgevingsconventie toepassen, zodat uit de bestandsnaam duidelijk wordt waar u mee te maken hebt.

Bestandsuitvoernaamgeving kan worden beheerd via expressies in de ontwerper. Open het eigenschappenvenster voor een van de onderdelen Bestandsuitvoer en open de expressie-editor voor de eigenschap Bestand. Ons eerste uitvoerbestand is geconfigureerd via de volgende expressie (zie de zelfstudie om van [MXF naar een enkele bitrate MP4-uitvoer te](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)gaan):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Dit betekent dat onze bestandsnaam wordt bepaald door twee variabelen: de uitvoermap om in te schrijven en de naam van de bronbestandsbasis. De eerste wordt blootgesteld als een eigenschap op de werkstroomroot en de laatste wordt bepaald door het binnenkomende bestand. De uitvoermap is wat u gebruikt voor lokale tests; deze eigenschap wordt overschreven door de werkstroomengine wanneer de werkstroom wordt uitgevoerd door de cloudgebaseerde mediaprocessor in Azure Media Services.
Als u onze beide uitvoerbestanden een consistente uitvoernaamgeving wilt geven, wijzigt u de eerste bestandsnaamgevingsexpressie in:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

en de tweede op:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Voer een tussentijdse testrun uit om ervoor te zorgen dat beide MP4-uitvoerbestanden correct zijn gegenereerd.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Een apart audiospoor toevoegen
Zoals we later zullen zien wanneer we een .ism-bestand genereren dat bij onze MP4-uitvoerbestanden hoort, hebben we ook een mp4-audiobestand nodig als audiotrack voor onze adaptieve streaming. Als u dit bestand wilt maken, voegt u een extra muxer toe aan de workflow (ISO-MPEG-4 Multiplexer) en sluit u de uitvoerpin van de OCC-encoder aan met de invoerpin voor Track 1.

![Audio Muxer toegevoegd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer toegevoegd*

Maak een derde component Bestandsuitvoer om de uitgaande stream van de muxer uit te schakelen en de bestandsnaamgevingsexpressie te configureren als:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio Muxer maakt bestandsuitvoer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio Muxer maakt bestandsuitvoer*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Het toevoegen van de . ISM SMIL-bestand
Om de dynamische verpakking te laten werken in combinatie met zowel MP4-bestanden (en de audio-only MP4) in onze Media Services-asset, hebben we ook een manifestbestand nodig (ook wel een "SMIL"-bestand genoemd: Gesynchroniseerde multimedia-integratietaal). Dit bestand geeft Azure Media Services aan welke MP4-bestanden beschikbaar zijn voor dynamische verpakkingen en welke van die moeten worden overwogen voor de audiostreaming. Een typisch manifest bestand voor een set mp4's met een enkele audiostream ziet er als volgt uit:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

Het .ism-bestand bevat in een switch-instructie, een verwijzing naar elk van de afzonderlijke MP4-videobestanden en daarnaast ook één (of meer) audiobestandsverwijzingen naar een MP4 die alleen de audio bevat.

Het genereren van het manifest bestand voor onze set van MP4's kan worden gedaan door middel van een component genaamd de "AMS Manifest Writer." Als u het wilt gebruiken, sleept u het naar het oppervlak en sluit u de uitvoerpennen 'Schrijf voltooid' van de drie bestandsuitvoercomponenten aan op de AMS Manifest Writer-invoer. Zorg er vervolgens voor dat u de uitvoer van de AMS Manifest Writer aansluit op het uitvoerbestand/actief.

Configureer, net als bij onze andere bestandsuitvoercomponenten, de naam van het .ism-bestand met een expressie:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Onze voltooide workflow ziet er als volgt uit:

![Voltooide MXF-werkstroom met meerdere bitrate MP4-werkstroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Voltooide MXF-werkstroom met meerdere bitrate MP4-werkstroom*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>MxF coderen in multibitrate MP4 - verbeterde blauwdruk
In de [vorige workflow-walkthrough](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) hebben we gezien hoe een enkel MXF-invoerelement kan worden omgezet in een uitvoerasset met multibitrate MP4-bestanden, een MP4-audiobestand en een manifestbestand voor gebruik in combinatie met dynamische azure mediaservices-verpakking.

Deze walkthrough laat zien hoe sommige aspecten kunnen worden verbeterd en handiger gemaakt.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Overzicht van werkstroom om te verbeteren
![Mp4-workflow met meerdere bitrates om te verbeteren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Mp4-workflow met meerdere bitrates om te verbeteren*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Conventies voor bestandsnaamgeving
In de vorige werkstroom hebben we een eenvoudige expressie opgegeven als basis voor het genereren van uitvoerbestandsnamen. We hebben echter een aantal duplicatie: alle afzonderlijke uitvoerbestandscomponenten hebben een dergelijke expressie gespecificeerd.

Onze component bestandsuitvoer voor het eerste videobestand is bijvoorbeeld geconfigureerd met deze expressie:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Terwijl voor de tweede uitvoervideo, hebben wij een uitdrukking als:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Zou het niet schoner, minder foutgevoelig, en handiger als we konden verwijderen sommige van deze duplicatie en dingen meer configureerbaar in plaats daarvan? Gelukkig kunnen we: de expressie mogelijkheden van de ontwerper in combinatie met de mogelijkheid om aangepaste eigenschappen te maken op onze workflow root zal een extra laag van gemak te bieden.

Laten we aannemen dat we de bestandsnaamconfiguratie stimuleren op de bitrates van de afzonderlijke MP4-bestanden. Deze bitrates zullen we streven naar configureren op een centrale plaats (op de wortel van onze grafiek), van waaruit ze zullen worden benaderd om te configureren en rijden bestandsnaam generatie. Om dit te doen, beginnen we met het publiceren van de bitrate eigenschap van zowel AVC encoders aan de wortel van onze workflow, zodat het toegankelijk wordt van zowel de wortel als van de AVC encoders. (Zelfs als deze op twee verschillende plekken wordt weergegeven, is er slechts één onderliggende waarde.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Eigenschappen van onderdelen publiceren in de werkstroomroot
Open de eerste AVC-encoder, ga naar de eigenschap Bitrate (kbps) en kies publiceren in de vervolgkeuzelijst Publiceren.

![De eigenschap bitrate publiceren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*De eigenschap bitrate publiceren*

Configureer het publicatiedialoogvenster om te publiceren naar de hoofdmap van onze werkstroomgrafiek, met een gepubliceerde naam van "video1bitrate" en een leesbare weergavenaam van "Video 1 Bitrate". Configureer een aangepaste groepsnaam genaamd 'Streaming Bitrates' en druk op Publiceren.

![De eigenschap bitrate publiceren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Dialoogvenster Publiceren voor bitrate- eigenschap*

Herhaal hetzelfde voor de bitrate eigenschap van de tweede AVC encoder en noem het "video2bitrate" met een weergavenaam van "Video 2 Bitrate", in dezelfde aangepaste groep "Streaming Bitrates".

Als we nu de hoofdeigenschappen van de werkstroom inspecteren, zien we onze aangepaste groep met de twee gepubliceerde eigenschappen weergeven. Beide weerspiegelen de waarde van hun respectieve AVC encoder bitrate.

![Gepubliceerde bitraterekwisieten op werkstroomroot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Wanneer we toegang willen krijgen tot deze eigenschappen vanuit code of vanuit een expressie, kunnen we dit als volgt doen:

* van inline code van een component onder de hoofdwortel: node.getPropertyAsString('.. /video1bitrate', null)
* binnen een expressie: ${ROOT_video1bitrate}

Laten we de groep "Streaming Bitrates" voltooien door onze audiotrackbitrate ook op te publiceren. Zoek in de eigenschappen van de OC-encoder naar de bitrate-instelling en selecteer Publiceren in de vervolgkeuzelijst ernaast. Publiceer naar de hoofdmap van de grafiek met de naam "audio1bitrate" en de weergavenaam "Audio 1 Bitrate" binnen onze aangepaste groep "Streaming Bitrates".

![Dialoogvenster Publiceren voor audiobitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialoogvenster Publiceren voor audiobitrate*

![Resulterende video- en audiorekwisieten op root](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Resulterende video- en audiorekwisieten op root*

Het wijzigen van een van deze drie waarden herconfigureert en wijzigt ook de waarden op de respectieve componenten waarmee ze zijn gekoppeld (en waar ze worden gepubliceerd).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Gegenereerde uitvoerbestandsnamen afhankelijk laten zijn van gepubliceerde eigenschapswaarden
In plaats van onze gegenereerde bestandsnamen te hardcoderen, kunnen we nu onze bestandsnaamexpressie op elk van de bestandsuitvoercomponenten wijzigen om te vertrouwen op de bitrate-eigenschappen die we op de grafiekwortel hebben gepubliceerd. Zoek de eigenschap Bestand en bewerk de expressie als volgt, te beginnen met onze eerste bestandsuitvoer:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

De verschillende parameters in deze expressie kunnen worden geopend en ingevoerd door in het expressievenster op het dollarteken op het toetsenbord te drukken. Een van de beschikbare parameters is onze video1bitrate eigenschap die we eerder gepubliceerd.

![Toegang tot parameters binnen een expressie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Toegang tot parameters binnen een expressie*

Doe hetzelfde voor de bestandsuitvoer voor onze tweede video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

en voor de bestandsuitvoer met alleen audio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Als we nu de bitrate voor een van de video- of audiobestanden wijzigen, wordt de betreffende encoder opnieuw geconfigureerd en wordt de op bitrate gebaseerde bestandsnaamconventie automatisch gehonoreerd.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Miniaturen toevoegen aan mp4-uitvoer met meerdere bitrate
Uitgaande van een werkstroom die [een mp4-uitvoer met meerdere bitrate's genereert van een MXF-ingang,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)zullen we nu zoeken naar het toevoegen van miniaturen aan de uitvoer.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Werkstroomoverzicht om miniaturen toe te voegen
![Mp4-werkstroom met meerdere bitrate om vanaf te starten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Mp4-werkstroom met meerdere bitrate om vanaf te starten*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG-codering toevoegen
Het hart van onze miniatuurgeneratie zal het JPG Encoder-onderdeel zijn, dat JPG-bestanden kan uitvoeren.

![JPG Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

We kunnen onze niet-gecomprimeerde videostream van de Media File Input echter niet rechtstreeks aansluiten op de JPG-encoder. In plaats daarvan verwacht het individuele frames te krijgen. Dit kunnen we doen via de Video Frame Gate component.

![Een framepoort aansluiten op de JPG-encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Een framepoort aansluiten op de JPG-encoder*

De framepoort eens in de zoveel seconden of frames maakt het mogelijk een videoframe te passeren. Het interval en de tijdverschuiving waarmee dit gebeurt, kunnen worden geconfigureerd in de eigenschappen.

![Eigenschappen videoframepoort](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Eigenschappen videoframepoort*

Laten we elke minuut een miniatuur maken door de modus in te stellen op Tijd (seconden) en het interval op 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Omgaan met kleurruimteconversie
Hoewel het logisch lijkt, zowel ongecomprimeerde videopinnen van de framepoort als de Media File Input kunnen nu worden aangesloten, zouden we een waarschuwing krijgen als we dat zouden doen.

![Fout in de invoerkleurruimte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Fout in de invoerkleurruimte*

Dit komt omdat de manier waarop kleurinformatie wordt weergegeven in onze originele ruwe ongecomprimeerde videostream, afkomstig van onze MXF, verschilt van wat de JPG Encoder verwacht. Meer in het bijzonder, een zogenaamde "kleurruimte" van "RGB" of "Grijswaarden" zal naar verwachting stromen inch Dit betekent dat de inkomende videostream van de Video Frame Gate eerst een conversie moet hebben toegepast met betrekking tot de kleurruimte.

Sleep op de workflow de Color Space Converter - Intel en sluit deze aan op onze framegate.

![Een convertor voor kleurruimte verbinden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Een convertor voor kleurruimte verbinden*

Kies in het venster Eigenschappen het BGR 24-item in de lijst Voorinstelling.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>De miniaturen schrijven
Anders dan onze MP4-video's, de JPG Encoder component uitgangen meer dan een bestand. Om hiermee om te gaan, kan een component Scene Search JPG File Writer worden gebruikt: het neemt de binnenkomende JPG-miniaturen en schrijft ze uit, waarbij elke bestandsnaam wordt achtergetekend door een ander nummer. (Het getal geeft doorgaans het aantal seconden/eenheden in de stream aan waaruit de miniatuur is getrokken.)

![Maak kennis met de scène zoeken JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Maak kennis met de scène zoeken JPG File Writer*

De eigenschap Pad uitvoermap configureren met de expressie: ${ROOT_outputWriteDirectory}

en de eigenschap Voorvoegsel Bestandsnaam met:

    ${ROOT_sourceFileBaseName}_thumb_

Het voorvoegsel bepaalt hoe de miniatuurbestanden worden benoemd. Ze zijn achtergezet met een getal dat de positie van de duim in de stroom aangeeft.

![Eigenschappen scènezoeken JPG-bestandsschrijver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Eigenschappen scènezoeken JPG-bestandsschrijver*

Verbind de scène zoek JPG-bestandsschrijver met het uitvoerbestand/het activumknooppunt.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Fouten in een werkstroom detecteren
Sluit de invoer van de kleurruimteconverter aan op de ruwe niet-gecomprimeerde video-uitvoer. Voer nu een lokale testrun uit voor de werkstroom. Er is een goede kans dat de werkstroom plotseling stopt met uitvoeren en met een rode omtrek op de component die een fout heeft ondervonden, wordt aangegeven:

![Converter van kleurruimte: fout](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Converter van kleurruimte: fout*

Klik op het kleine rode "E"-pictogram in de rechterbovenhoek van de component Kleurruimteconverter om te zien wat de reden is dat de coderingspoging is mislukt.

![Foutvan kleurruimteconverter](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Foutvan kleurruimteconverter*

Het blijkt, zoals u zien, dat de inkomende kleurruimte standaard voor de kleurruimte converter moet worden rec601 voor onze gevraagde conversie van YUV naar RGB. Blijkbaar is onze stroom niet te wijzen op de rec601. (Rec 601 is een standaard voor het coderen van interlaced analoge videosignalen in digitale videovorm. Het specificeert een actief gebied dat 720 luminantiemonsters en 360 chrominantiemonsters per lijn omvat. Het kleurcoderingssysteem staat bekend als YCbCr 4:2:2.)

Om dit op te lossen, geven we op de metadata van onze stream aan dat we te maken hebben met rec601-inhoud. Hiervoor gebruiken we een component Video Data Type Updater, die we tussen onze ruwe bron en de conversiecomponent voor kleurruimte plaatsen. Deze updater van het gegevenstype maakt het mogelijk om bepaalde eigenschappen van het type videogegevens handmatig bij te werken. Configureer het om een kleurruimtestandaard van "Rec 601" aan te geven. Dit zorgt ervoor dat de Video Data Type Updater om de stroom te taggen met de "Rec 601" kleurruimte als er nog geen kleurruimte gedefinieerd. (Bestaande metagegevens worden niet overschreven, tenzij het selectievakje Overschrijven is ingeschakeld.)

![Kleurruimtestandaard bijwerken op de updater van het gegevenstype](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Kleurruimtestandaard bijwerken op de updater van het gegevenstype*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Voltooide werkstroom
Nu onze workflow is voltooid, doet u nog een testom deze te zien passeren.

![Voltooide werkstroom voor multi-mp4-uitvoer met miniaturen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Voltooide werkstroom voor multi-mp4-uitvoer met miniaturen*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Time-based trimmen van mp4-uitvoer met meerdere bitrate
Uitgaande van een werkstroom die [een multibitrate MP4-uitvoer genereert van een MXF-ingang,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)zullen we nu kijken naar het bijsnijden van de bronvideo op basis van tijdstempels.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Werkstroomoverzicht om bijsnijden toe te voegen aan
![Werkstroom starten om bijsnijden toe te voegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Werkstroom starten om bijsnijden toe te voegen*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>De streamtrimmer gebruiken
Met de component Stream Trimmer u het begin en het einde van een invoerstreambasis bijsnijden op tijdsinformatie (seconden, minuten, ...). De trimmer ondersteunt geen frame-based trimmen.

![StreamTrimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*StreamTrimmer*

In plaats van de AVC-encoders en speakerpositieassigner rechtstreeks te koppelen aan de Media File Input, zetten we daar tussen die de streamtrimmer in. (Een voor het videosignaal en een voor het interleaved audiosignaal.)

![Zet Stream Trimmer ertussen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Zet Stream Trimmer ertussen*

Laten we de trimmer zo configureren dat we alleen video en audio verwerken tussen 15 seconden en 60 seconden in de video.

Ga naar de eigenschappen van de VideoStreamTrimmer en configureer zowel de begintijd (15 s) als de eigenschappen Eindtijd (60 s). Om ervoor te zorgen dat zowel onze audio- als videotrimmer altijd zijn geconfigureerd volgens dezelfde begin- en eindwaarden, publiceren we deze naar de hoofdmap van de workflow.

![De eigenschap Starttijd publiceren vanuit Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*De eigenschap Starttijd publiceren vanuit Stream Trimmer*

![Dialoogvenster Eigenschap publiceren voor begintijd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Dialoogvenster Eigenschap publiceren voor begintijd*

![Dialoogvenster Eigenschap publiceren voor eindtijd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Dialoogvenster Eigenschap publiceren voor eindtijd*

Als we nu de wortel van onze workflow inspecteren, worden beide eigenschappen vanaf daar netjes weergegeven en configureerbaar.

![Gepubliceerde eigenschappen beschikbaar op root](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Gepubliceerde eigenschappen beschikbaar op root*

Open nu de bijsnijeigenschappen van de audiotrimmer en configureer zowel begin- als eindtijden met een expressie die verwijst naar de gepubliceerde eigenschappen op de wortel van onze workflow.

Voor de begintijd van het bijsnijden van audio:

    ${ROOT_TrimmingStartTime}

en voor de eindtijd:

    ${ROOT_TrimmingEndTime}

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Voltooide werkstroom
![Voltooide werkstroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Voltooide werkstroom*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Introductie van de scriptcomponent
Gescripte componenten kunnen willekeurige scripts uitvoeren tijdens de uitvoeringsfasen van onze workflow. Er zijn vier verschillende scripts die kunnen worden uitgevoerd, elk met specifieke kenmerken, en hun eigen plaats in de workflow levenscyclus:

* **commandScript**
* **realizeScript**
* **procesInputScript**
* **lifeCycleScript**

De documentatie van de Scripted Component gaat in meer detail voor elk van de bovenstaande. In [de volgende sectie](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)wordt de component **realizeScript-scripting** gebruikt om een cliplist xml on the fly te construeren wanneer de werkstroom wordt gestart. Dit script wordt aangeroepen tijdens de componentinstelling, wat slechts één keer gebeurt in de levenscyclus.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Scripting binnen een workflow: hallo wereld
Sleep een scriptcomponent naar het ontwerpoppervlak en wijzig de naam ervan (bijvoorbeeld 'SetClipListXML').

![Een gescripte component toevoegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Een gescripte component toevoegen*

Wanneer u de eigenschappen van de scriptcomponent inspecteert, worden de vier verschillende scripttypen weergegeven, elk configureerbaar voor een ander script.

![Eigenschappen van gescripte component](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Eigenschappen van gescripte component*

Schakel het procesInputScript uit en open de editor voor het realizeScript. Nu zijn we klaar om te beginnen met scripting.

Scripts zijn geschreven in Groovy, een dynamisch gecompileerde scripttaal voor het Java-platform dat compatibiliteit met Java behoudt. Eigenlijk is de meeste Java-code geldig Groovy code.

Laten we een eenvoudige hello world groovy script schrijven in de context van onze realizeScript. Voer het volgende in de editor in:

    node.log("hello world");

Voer nu een lokale testrun uit. Controleer na deze run (via het tabblad Systeem op de scriptcomponent) de eigenschap Logboeken.

![Hello world log output Hello world log output Hello world log output Hello world](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello world log output Hello world log output Hello world log output Hello world*

Het knooppuntobject waar we de logmethode op aanroepen, verwijst naar ons huidige 'knooppunt' of de component waarin we zich aan het scripten zijn. Elk onderdeel als zodanig heeft de mogelijkheid om logboekregistratiegegevens uit te zetten, beschikbaar via het tabblad systeem. In dit geval, we output de string letterlijke "hallo wereld." Belangrijk om hier te begrijpen is dat dit kan blijken te zijn een onschatbare debugging tool, waardoor u inzicht over wat het script daadwerkelijk doet.

Vanuit onze scriptomgeving hebben we ook toegang tot eigenschappen op andere componenten. Probeer het volgende:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

Ons logboekvenster toont ons het volgende:

![Logboekuitvoer voor toegang tot knooppuntpaden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Logboekuitvoer voor toegang tot knooppuntpaden*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Frame-based trimmen van mp4-uitvoer met meerdere bitrate
Uitgaande van een werkstroom die [een multibitrate MP4-uitvoer genereert van een MXF-ingang,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)zullen we nu kijken naar het bijsnijden van de bronvideo op basis van frametellingen.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Overzicht van de blauwdruk om bijsnijden toe te voegen aan
![Werkstroom om bijsnijden toe te voegen aan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Werkstroom om bijsnijden toe te voegen aan*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>XML voor de cliplijst gebruiken
In alle vorige werkstroomzelfstudies gebruikten we de component Mediabestandsinvoer als onze video-invoerbron. Voor dit specifieke scenario gebruiken we echter in plaats daarvan de component Bron van de cliplijst. Dit mag niet de beste manier van werken zijn; Gebruik de bron van de cliplijst alleen als er een echte reden is om dit te doen (zoals in het volgende geval, waar we gebruik maken van de mogelijkheden voor het bijsnijden van de cliplijst).

Als u wilt overschakelen van onze mediabestandsinvoer naar de bron van de cliplijst, sleept u de component Cliplistbron naar het ontwerpoppervlak en sluit u de XML-cliplijst-koppeling aan op het XML-knooppunt cliplijst van de werkstroomontwerper. Hiermee wordt de bron van de cliplijst gevuld met uitvoerpinnen, volgens onze invoervideo. Sluit nu de niet-gecomprimeerde video- en niet-gecomprimeerde audiopins van de bron van de cliplijst aan op de respectievelijke AVC-coders en audiostream-interleaver. Verwijder nu de mediabestandsinvoer.

![De mediabestandsinvoer vervangen door de bron van de cliplijst](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*De mediabestandsinvoer vervangen door de bron van de cliplijst*

De component Cliplijstbron neemt als invoer een XML 'Cliplijst' in. Wanneer u het bronbestand selecteert waarmee u lokaal wilt testen, wordt deze cliplijst xml automatisch ingevuld.

![Xml, automatisch ingevulde cliplijst, eigenschap](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Xml, automatisch ingevulde cliplijst, eigenschap*

Op zoek naar een beetje dichter bij de xml, dit is hoe het eruit ziet:

![Dialoogvenster Cliplijst bewerken](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Dialoogvenster Cliplijst bewerken*

Dit weerspiegelt echter niet de mogelijkheden van de cliplijst xml. Een optie die we hebben is het toevoegen van een "Trim" element onder zowel de video-en audiobron, zoals deze:

![Een bijsnijdelement toevoegen aan de cliplijst](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Een bijsnijdelement toevoegen aan de cliplijst*

Als u de xml-cliplijst zoals deze hierboven wijzigt en een lokale testuitvoert, wordt de video correct bijgesneden tussen 10 en 20 seconden in de video.

In tegenstelling tot wat er gebeurt wanneer u een lokale uitvoering uitvoert, zou dezelfde cliplist xml niet hetzelfde effect hebben wanneer deze wordt toegepast in een werkstroom die wordt uitgevoerd in Azure Media Services. Wanneer Azure Premium Encoder wordt gestart, wordt de cliplist xml elke keer opnieuw gegenereerd, op basis van het invoerbestand dat de coderingstaak heeft gekregen. Dit betekent dat alle wijzigingen die we doen op de xml helaas zou worden overschreven.

Om de cliplist xml tegen te gaan die wordt gewist wanneer een coderingstaak wordt gestart, kunnen we deze net na het begin van onze workflow opnieuw genereren. Dergelijke aangepaste acties kunnen worden uitgevoerd door middel van wat wordt genoemd een "Scripted Component." Zie De component [Gescriptbestand introduceren](media-services-media-encoder-premium-workflow-tutorials.md#scripting)voor meer informatie .

Sleep een scriptcomponent naar het ontwerpoppervlak en wijzig de naam in SetClipListXML.

![Een gescripte component toevoegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Een gescripte component toevoegen*

Wanneer u de eigenschappen van de scriptcomponent inspecteert, worden de vier verschillende scripttypen weergegeven, elk configureerbaar voor een ander script.

![Eigenschappen van gescripte component](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Eigenschappen van gescripte component*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>De cliplijst wijzigen vanuit een gescripte component
Voordat we de cliplist xml kunnen herschrijven die wordt gegenereerd tijdens het opstarten van de werkstroom, moeten we toegang hebben tot de eigenschap en inhoud van de cliplist xml. We kunnen dit als volgt doen:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Inkomende cliplijst die wordt geregistreerd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Inkomende cliplijst die wordt geregistreerd*

Eerst hebben we een manier nodig om te bepalen vanaf welk punt tot welk punt we de video willen trimmen. Als u dit voor de minder technische gebruiker van de werkstroom gemakkelijk wilt maken, publiceert u twee eigenschappen naar de hoofdmap van de grafiek. Klik hiervoor met de rechtermuisknop op het ontwerpoppervlak en selecteer 'Eigenschap toevoegen':

* Eerste eigenschap: "ClippingTimeStart" van type: "TIMECODE"
* Tweede eigenschap: "ClippingTimeEnd" van type: "TIMECODE"

![Dialoogvenster Eigenschap toevoegen voor de begintijd van het knippen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dialoogvenster Eigenschap toevoegen voor de begintijd van het knippen*

![Gepubliceerde kniptijdrekwisieten op werkstroomroot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Gepubliceerde kniptijdrekwisieten op werkstroomroot*

Configureer beide eigenschappen op een geschikte waarde:

![De eigenschappen voor het knippen en beëindigen configureren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*De eigenschappen voor het knippen en beëindigen configureren*

Nu, vanuit ons script, hebben we toegang tot beide eigenschappen, zoals deze:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Logboekvenster met begin en einde van het knippen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Logboekvenster met begin en einde van het knippen*

Laten we de tijdcodetekenreeksen ontwenen in een handiger vorm, met behulp van een eenvoudige reguliere expressie:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Logboekvenster met uitvoer van ontleedtijdcode](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Logboekvenster met uitvoer van ontleedtijdcode*

Met deze informatie bij de hand, kunnen we nu de cliplist xml aanpassen aan de begin- en eindtijden voor de gewenste frame-accurate clipping van de film weer te geven.

![Scriptcode om bijsnijdelementen toe te voegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Scriptcode om bijsnijdelementen toe te voegen*

Dit werd gedaan door middel van normale snaar manipulatie operaties. De resulterende gewijzigde cliplijst xml wordt teruggeschreven naar de eigenschap clipListXML op de werkstroomroot via de methode 'setProperty'. Het logboekvenster na een andere testrun zou ons het volgende laten zien:

![De resulterende cliplijst registreren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*De resulterende cliplijst registreren*

Doe een test-run om te zien hoe de video- en audiostreams zijn geknipt. Aangezien u meer dan één test-run doet met verschillende waarden voor de trimpunten, zult u merken dat deze echter niet in aanmerking worden genomen! De reden hiervoor is dat de ontwerper, in tegenstelling tot de Azure-runtime, de cliplist xml NIET elke run overschrijft. Dit betekent dat alleen de eerste keer dat u de in- en outpunten hebt ingesteld, ervoor`clipListXML.indexOf("<trim>") == -1`zorgt dat de xml transformeert, alle andere keren, onze wachtclausule (als( )) voorkomt dat de workflow een ander trimelement toevoegt wanneer er al een aanwezig is.

Om onze workflow handig te maken om lokaal te testen, kunnen we het beste een huishoudcode toevoegen die controleert of er al een trimelement aanwezig was. Als dat het zo is, kunnen we het verwijderen voordat we doorgaan door de xml met de nieuwe waarden aan te passen. In plaats van het gebruik van plain string manipulaties, is het waarschijnlijk veiliger om dit te doen door middel van echte xml object model ontwijs.

Voordat we dergelijke code echter kunnen toevoegen, moeten we eerst een aantal importinstructies toevoegen aan het begin van ons script:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Hierna kunnen we de vereiste reinigingscode toevoegen:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Deze code gaat net boven het punt waarop we de trimelementen toevoegen aan de cliplist xml.

Op dit punt kunnen we onze workflow zoveel uitvoeren en wijzigen als we willen, terwijl we de wijzigingen ooit hebben toegepast.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Een eigenschap ClippingEnabled gemak toevoegen
Zoals u misschien niet altijd wilt trimmen gebeuren, laten we eindigen onze workflow door het toevoegen van een handige booleaanse vlag die aangeeft of we willen trimmen / knippen in te schakelen.

Net als voorheen publiceert u een nieuwe eigenschap naar de hoofdmap van onze werkstroom genaamd "ClippingEnabled" van het type "BOOLEAN".

![Een eigenschap gepubliceerd voor het inschakelen van knippen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Een eigenschap gepubliceerd voor het inschakelen van knippen*

Met de onderstaande eenvoudige bewakingsclausule kunnen we controleren of trimmen nodig is en beslissen of onze cliplijst als zodanig moet worden gewijzigd of niet.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a name="complete-code"></a><a id="code"></a>Volledige code

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Zie ook
[Premium-codering introduceren in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Premium-codering gebruiken in Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[On-demand content coderen met Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Media Encoder Premium Workflow-indelingen en -codecs](media-services-premium-workflow-encoder-formats.md)

[Voorbeeldwerkstroombestanden](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer-hulpprogramma](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
