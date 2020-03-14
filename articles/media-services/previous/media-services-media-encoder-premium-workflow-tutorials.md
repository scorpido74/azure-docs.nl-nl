---
title: Geavanceerde Media Encoder Premium Workflow zelf studies
description: Dit document bevat scenario's waarin wordt getoond hoe u geavanceerde taken uitvoert met Media Encoder Premium Workflow en hoe u met Workflow Designer complexe werk stromen kunt maken.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251009"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Geavanceerde Media Encoder Premium Workflow zelf studies
## <a name="overview"></a>Overzicht
Dit document bevat scenario's waarin wordt getoond hoe u werk stromen kunt aanpassen met **Workflow Designer**. U vindt [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)de daad werkelijke werk stroom bestanden.  

## <a name="toc"></a>INHOUDS OPGAVE
De volgende onderwerpen komen aan bod:

* [MXF coderen in een enkele bitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Een nieuwe werk stroom starten](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [De invoer van media bestanden gebruiken](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Streamen van media controleren](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Een video encoder toevoegen voor. MP4-bestand genereren](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [De audio stroom coderen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Audio-en video-streams multiplexen in een MP4-container](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Het MP4-bestand wordt geschreven](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Een Media Services-activum maken op basis van het uitvoer bestand](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [De voltooide werk stroom lokaal testen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [MXF versleutelen in een multibitrate Mp4's-dynamische verpakking ingeschakeld](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Een of meer extra MP4-uitvoer toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [De namen van de bestands uitvoer configureren](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Een afzonderlijk audio nummer toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Het SMIL-bestand ' ISM ' toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [MXF coderen in multibitrate MP4-Enhanced blauw druk](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Werk stroom overzicht om te verbeteren
  * [Naamgevings regels voor bestanden](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Onderdeel eigenschappen publiceren naar de hoofdwerk stroom](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [De namen van de uitvoer bestanden hebben gegenereerd, is afhankelijk van de waarden van gepubliceerde eigenschappen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Miniaturen toevoegen aan de multibitrate-MP4-uitvoer](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Werk stroom overzicht om miniaturen toe te voegen aan
  * [JPG-code ring toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Omgaan met de conversie van kleur ruimte](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [De miniaturen worden geschreven](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Fouten in een werk stroom detecteren](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Voltooide werk stroom](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Op tijd gebaseerde verkleining van multibitrate MP4-uitvoer](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Werk stroom overzicht om bijsnijden toe te voegen aan](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [De stream Trimmer gebruiken](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Voltooide werk stroom](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introductie van het script onderdeel](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Scripts uitvoeren in een werk stroom: Hallo wereld](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Op frames gebaseerd knippen van multibitrate MP4-uitvoer](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Overzicht van blauw druk voor het toevoegen van bijsnijden aan](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [De clip lijst-XML gebruiken](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [De lijst met clips wijzigen vanuit een script onderdeel](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Een eigenschap van het gemak ClippingEnabled toevoegen](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>MXF coderen in een enkele bitrate MP4
In deze sectie wordt gedemonstreerd hoe u een enkele bitrate maakt. MP4-bestand met AAC-HE-gecodeerde audio van een. MXF-invoer bestand.

### <a id="MXF_to_MP4_start_new"></a>Een nieuwe werk stroom starten
Open Workflow Designer en selecteer Bestand > nieuwe werk ruimte > trans code blauw druk

In de nieuwe werk stroom worden drie elementen weer gegeven:

* Primair bron bestand
* Lijst met XML-fragmenten
* Uitvoer bestand/-activum  

![Nieuwe encoding-werk stroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nieuwe encoding-werk stroom*

### <a id="MXF_to_MP4_with_file_input"></a>De invoer van media bestanden gebruiken
Om het invoer Media-bestand te accepteren, begint één met het toevoegen van een onderdeel voor het invoeren van media bestanden. Als u een component wilt toevoegen aan de werk stroom, zoekt u deze in het zoekvak van de opslag plaats en sleept u de gewenste vermelding naar het deel venster ontwerp. Herhaal de actie voor de invoer van het Media bestand en verbind het primaire bron bestand onderdeel met de pincode invoerpin van de invoer van het Media bestand.

![Bestands invoer verbonden media](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Bestands invoer verbonden media*

Bepaal in eerste instantie een geschikt voorbeeld bestand dat moet worden gebruikt bij het ontwerpen van een aangepaste werk stroom. Als u dit wilt doen, klikt u op de achtergrond van het ontwerp venster en zoekt u naar de eigenschap primair bron bestand in het rechterdeel venster van de rechter kant. Klik op het mappictogram en selecteer het gewenste bestand voor het testen van de werk stroom. Het onderdeel invoer van media bestanden inspecteert het bestand en vult de uitvoer pincodes in om de details te bekijken van het voorbeeld bestand dat het heeft geïnspecteerd.

![Invoer van Media bestand gevuld](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Invoer van Media bestand gevuld*

Nu de invoer is ingevuld, is de volgende stap het instellen van uitvoer coderings instellingen. Net als de manier waarop het primaire bron bestand is geconfigureerd, configureert u nu de variabele eigenschap uitvoermap, net eronder.

![Eigenschappen voor invoer en uitvoer geconfigureerd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Eigenschappen voor invoer en uitvoer geconfigureerd*

### <a id="MXF_to_MP4_streams"></a>Streamen van media controleren
Het is vaak gewenst om te weten hoe de stroom eruit ziet wanneer deze via de werk stroom loopt. Als u een stroom op een wille keurig punt in de werk stroom wilt controleren, klikt u op een uitvoer-of invoerpin op een van de onderdelen. In dit geval klikt u op de niet-gecomprimeerde video-uitvoer pincode van de invoer van het Media bestand. Er wordt een dialoog venster geopend waarmee de uitgaande video kan worden geïnspecteerd.

![De niet-gecomprimeerde video-uitvoer pincode controleren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*De niet-gecomprimeerde video-uitvoer pincode controleren*

In dit geval ziet u dat de video een 1920-invoer bevat met 24 frames per seconde in 4:2:2 steek proeven voor een video van bijna twee minuten.

### <a id="MXF_to_MP4_file_generation"></a>Een video encoder toevoegen voor. MP4-bestand genereren
Nu kunt u een niet-gecomprimeerde video en meerdere niet-gecomprimeerde audio-uitvoer pinnen gebruiken voor de invoer van het Media bestand. Als u de inkomende video wilt coderen, moet u een coderings onderdeel toevoegen aan de werk stroom, in dit geval voor het genereren van. MP4-bestanden.

Als u de video stroom wilt coderen naar H. 264, voegt u het component AVC Video Encoder toe aan het ontwerp oppervlak. Dit onderdeel gebruikt een gedecomprimeerde video stroom als invoer en levert een met AVC gecomprimeerde video stroom op de uitvoer pincode.

![Niet-verbonden AVC-encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Niet-verbonden AVC-encoder*

De eigenschappen bepalen hoe de code ring precies gebeurt. Laten we eens kijken naar een aantal van de belangrijkste instellingen:

* Uitvoer breedte en uitvoer hoogte: bepaalt de resolutie van de gecodeerde video. In dit geval is 640 x 360 een goede instelling.
* Frame frequentie: wanneer dit is ingesteld op passthrough, wordt alleen de bron frame frequentie toegepast. het is mogelijk om dit te negeren. Een dergelijke snelheids conversie is niet gecompenseerd.
* Profiel en niveau: bepaalt het AVC-profiel en-niveau. Als u meer informatie wilt krijgen over de verschillende niveaus en profielen, klikt u op het vraag teken op het pictogram van de AVC-Video Encoder en ziet u op de Help-pagina meer details over elk van de niveaus. Voor dit voor beeld gebruikt u hoofd profiel op niveau 3,2 (de standaard instelling).
* Snelheids controle modus en bitrate (kbps): in dit scenario kiest u voor een constante bitrate-uitvoer (CBR) bij 1200 kbps
* Video-indeling: bevat informatie over de VUI (informatie over de gebruiks vriendelijkheid) die wordt geschreven naar de H. 264-stroom (de gegevens die door een decoder kunnen worden gebruikt om de weer gave te verbeteren, maar niet essentieel voor het decoderen):
* NTSC (gebruikelijk voor US of Japan, met behulp van 30 fps)
* PAL (standaard voor Europa, met 25 fps)
* GOP terug formaat modus: Stel vaste GOP terug-grootte in voor onze doel einden met een sleutel interval van 2 seconden met een gesloten GOPs. De instelling van 2 seconden zorgt voor compatibiliteit met de dynamische verpakking Azure Media Services biedt.

Als u het AVC-coderings programma wilt invoeren, koppelt u de niet-gecomprimeerde video-uitvoer pincode van het onderdeel voor het invoeren van media bestanden aan de niet-gecomprimeerde video-invoer pincode van de AVC-encoder.

![Verbonden AVC-encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Verbonden AVC Main encoder*

### <a id="MXF_to_MP4_audio"></a>De audio stroom coderen
Op dit moment moet de oorspronkelijke ongecomprimeerde audio stroom nog steeds worden gecomprimeerd. Voor compressie van de audio stroom voegt u een onderdeel AAC-Encoder (Dolby) toe aan de werk stroom.

![Niet-verbonden AVC-encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Niet-verbonden AAC-Encoder*

Er is een incompatibiliteits probleem opgetreden: er is slechts één niet-gecomprimeerde audio-invoer pincode van de AAC encoder, terwijl er meer dan waarschijnlijk is dat de invoer van het Media bestand twee verschillende niet-gecomprimeerde audio gegevens bevat: één voor het audio kanaal links en een voor de rechter kant. (Als u wilt omgaan met surround sound, dat zijn zes kanalen.) Het is dus niet mogelijk om de audio rechtstreeks te verbinden van de invoer bron van het Media bestand in de AAC audio encoder. Het AAC-onderdeel verwacht een zogenaamde ' Interleaved ' audio stream: een enkele stroom met zowel de linker als de juiste kanalen die met elkaar zijn geleaveerd. Zodra we van ons bron media bestand weten dat audio sporen op welke positie in de bron staan, kunnen we een dergelijke gestroomde audiostream genereren met de correct toegewezen luidspreker posities voor links en rechts.

Eerst wil een Interleaved stream genereren op basis van de vereiste bron audio kanalen. Het onderdeel audio stream interleaver verwerkt dit voor ons. Voeg deze toe aan de werk stroom en verbind de audio-uitvoer van het Media bestand.

![Interleaver verbonden audio stroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Interleaver verbonden audio stroom*

Nu we een gestroomde audiostream hebben, hebben we nog steeds niet opgegeven waar de linker-of rechter luidspreker posities moeten worden toegewezen. Om dit op te geven, kunnen we gebruikmaken van de positie toewijzing van de spreker.

![De positie van een spreker toevoegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*De positie van een spreker toevoegen*

Configureer de toewijzings functie van de luidspreker positie voor gebruik met een stereo invoer stroom via een vooraf ingesteld filter voor het coderings programma met de naam "aangepast" en de kanaal voorinstelling "2,0 (L, R)." (Hiermee wordt de positie van de linker spreker toegewezen aan kanaal 1 en de juiste luidspreker positie naar kanaal 2.)

De uitvoer van de positie toewijzings functie van de spreker verbinden met de invoer van het AAC-coderings programma. Vervolgens geeft u aan dat het AAC-coderings programma moet werken met een ' 2,0 (L, R) ' kanaal vooraf ingesteld, zodat het duidelijk is dat het gaat om stereo audio als invoer.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Audio-en video-streams multiplexen in een MP4-container
Gezien onze met AVC gecodeerde video stroom en onze AAC-gecodeerde audio stroom kunnen we beide vastleggen in een. MP4-container. Het proces van het combi neren van verschillende stromen in één van beide wordt ' multiplexing ' (of ' muxing ' genoemd). In dit geval laten we het geluid en de video stromen in één samenhangend interheren. MP4-pakket. Het onderdeel dat dit coördineert voor een. MP4-container wordt de ISO MPEG-4-multiplexer genoemd. Voeg een toe aan het ontwerp oppervlak en verbind zowel de AVC-Video Encoder als het AAC-coderings programma naar de invoer.

![Verbonden MPEG4-multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Verbonden MPEG4-multiplexer*

### <a id="MXF_to_MP4_writing_mp4"></a>Het MP4-bestand wordt geschreven
Bij het schrijven van een uitvoer bestand wordt het onderdeel file output gebruikt. We kunnen dit verbinden met de uitvoer van de ISO MPEG-4-multiplexer, zodat de uitvoer naar de schijf wordt geschreven. U doet dit door de container (MPEG-4)-uitvoer pincode te koppelen aan de invoer pincode voor schrijven van de bestands uitvoer.

![Bestands uitvoer verbonden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Bestands uitvoer verbonden*

De bestands naam die wordt gebruikt, wordt bepaald door de bestands eigenschap. Hoewel deze eigenschap kan worden ingesteld op een bepaalde waarde, is het waarschijnlijk dat deze in plaats daarvan door een expressie moet worden geconfigureerd.

Als u wilt dat de werk stroom de naam eigenschap van het uitvoer bestand automatisch bepaalt vanuit een expressie, klikt u op de knop naast de bestands naam (naast het mappictogram). Selecteer in de vervolg keuzelijst expressie. Hiermee wordt de expressie-editor geopend. Wis eerst de inhoud van de editor.

![Lege expressie-editor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Lege expressie-editor*

Met de expressie-editor kunt u elke letterlijke waarde, gecombineerd met een of meer variabelen, invoeren. Variabelen beginnen met een dollar teken. Terwijl u op de $-toets drukt, wordt in de editor een vervolg keuzelijst weer gegeven met een keuze uit de beschik bare variabelen. In ons geval gebruiken we een combi natie van de uitvoer Directory-variabele en de variabele voor de naam van het basis invoer bestand:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Expressie-editor is ingevuld](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Expressie-editor is ingevuld*

> [!NOTE]
> Als u een uitvoer bestand van uw coderings taak in azure wilt zien, moet u een waarde opgeven in de expressie-editor.
>
>

Wanneer u de expressie bevestigt op OK, wordt in het eigenschappen venster een voor beeld weer gegeven van welke waarde de bestands eigenschap op dit moment wordt opgelost.

![File-expressie lost uitvoer dir op](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*File-expressie lost uitvoer dir op*

### <a id="MXF_to_MP4_asset_from_output"></a>Een Media Services-activum maken op basis van het uitvoer bestand
Hoewel we een MP4-uitvoer bestand hebben geschreven, moeten we nog steeds aangeven dat dit bestand hoort bij de uitvoer-Asset die door Media Services wordt gegenereerd als gevolg van het uitvoeren van deze werk stroom. Hiertoe wordt het knoop punt uitvoer bestand/activum op het canvas van de werk stroom gebruikt. Alle binnenkomende bestanden in dit knoop punt maken deel uit van de resulterende Azure Media Services Asset.

Verbind het onderdeel Bestands uitvoer met het onderdeel uitvoer bestand/activum om de werk stroom te volt ooien.

![Voltooide werk stroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Voltooide werk stroom*

### <a id="MXF_to_MP4_test"></a>De voltooide werk stroom lokaal testen
Als u de werk stroom lokaal wilt testen, klikt u op de knop afspelen in de werk balk aan de bovenkant. Wanneer de uitvoering van de werk stroom is voltooid, inspecteert u de uitvoer die is gegenereerd in de map met de geconfigureerde uitvoer. U ziet het voltooide MP4-uitvoer bestand dat is gecodeerd van het invoer bron bestand MXF.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>MXF coderen in MP4: dynamische verpakking met multibitrate ingeschakeld
In dit scenario maakt u een set met meerdere bitsnelheden MP4-bestanden met met AAC gecodeerde audio van één. MXF-invoer bestand.

Wanneer een multi-bitrate activa-uitvoer gewenst is voor gebruik in combi natie met de functies voor dynamische pakketten die worden aangeboden door Azure Media Services, moeten meerdere GOP terug-afgevulde MP4-bestanden van elk een andere bitrate en oplossing worden gegenereerd. Om dit te doen, biedt de [coderings MXF in een single bitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) -walkthrough een goed uitgangs punt.

![Werk stroom starten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Werk stroom starten*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Een of meer extra MP4-uitvoer toevoegen
Elk MP4-bestand in onze resulterende Azure Media Services Asset ondersteunt een andere bitsnelheid en oplossing. Laten we een of meer MP4-uitvoer bestanden toevoegen aan de werk stroom.

Om ervoor te zorgen dat we al onze video encoders met dezelfde instellingen hebben gemaakt, is het handig om de bestaande AVC-Video Encoder te dupliceren en een andere combi natie van resolutie en bitrate te configureren (we gaan een van 960 x 540 op 25 frames per seconde toevoegen bij 2,5 Mbps ). Als u de bestaande Encoder wilt dupliceren, kopieert u deze op het ontwerp oppervlak.

Verbind de niet-gecomprimeerde video-uitvoer pincode van de invoer van het Media bestand in het nieuwe AVC-onderdeel.

![Tweede AVC-encoder verbonden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Tweede AVC-encoder verbonden*

Pas nu de configuratie voor onze nieuwe AVC-encoder aan voor uitvoer 960x540 bij 2,5 Mbps. (Gebruik de eigenschappen "uitvoer breedte", "uitvoer hoogte" en "bitrate (kbps)" voor dit.)

Omdat we de resulterende activa in combi natie met Azure Media Services dynamische verpakkingen willen gebruiken, moet het streaming-eind punt kunnen genereren van deze MP4-bestanden HLS/gefragmenteerde MP4/DASH-fragmenten die exact op elkaar zijn afgestemd op een manier die clients die wisselen tussen verschillende bitsnelheden, een continue video-en audio-ervaring krijgen. Om dit te doen, moeten we ervoor zorgen dat in de eigenschappen van beide AVC-encoders de grootte van de GOP terug ("groep van afbeeldingen") voor beide MP4-bestanden is ingesteld op 2 seconden. Dit kan worden gedaan door:

* de grootte modus GOP terug instellen op vaste GOP terug-grootte en
* het sleutel frame interval tot twee seconden.
* Stel ook het GOP terug IDR-besturings element in op closed GOP terug om ervoor te zorgen dat alle GOPs op hun eigen positie zonder afhankelijkheden

Als u deze werk stroom eenvoudiger wilt begrijpen, wijzigt u de naam van het eerste AVC-coderings programma in ' AVC Video Encoder 640 x 360 1200 kbps ' en de tweede AVC-encoder ' AVC Video Encoder 960x540 2500 kbps '.

Voeg nu een tweede ISO MPEG-4-multiplexer en een tweede uitvoer bestand toe. Verbind de multiplexer met de nieuwe AVC-encoder en zorg ervoor dat de uitvoer wordt omgeleid naar de bestands uitvoer. Daarna kunt u ook de uitvoer van de AAC audio encoder verbinden met de invoer van de nieuwe multiplexer. De bestands uitvoer op zijn beurt kan vervolgens worden gekoppeld aan het knoop punt uitvoer bestand/activum om deze toe te voegen aan het Media Services activum dat wordt gemaakt.

![Tweede muxer en bestands uitvoer verbonden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Tweede muxer en bestands uitvoer verbonden*

Voor compatibiliteit met Azure Media Services dynamische verpakking configureert u de segment modus van de multiplexer in GOP terug Count of duration en stelt u de GOPs per chunk in op 1. (Dit moet de standaard waarde zijn.)

![Segment modi muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Segment modi muxer*

Opmerking: mogelijk wilt u dit proces herhalen voor alle andere Combi Naties van bitsnelheid en resolutie die u wilt toevoegen aan de Asset-uitvoer.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>De namen van de bestands uitvoer configureren
Er zijn meer dan één bestand toegevoegd aan de uitvoer Asset. Dit biedt de nood zaak om ervoor te zorgen dat de bestands namen voor elk van de uitvoer bestanden verschillen van elkaar en kunnen zelfs een Conventie voor bestands namen Toep assen zodat deze duidelijk wordt uit de bestands naam waar u mee mee bezig bent.

Naamgeving van bestands uitvoer kan worden beheerd via expressies in de ontwerp functie. Open het eigenschappen venster voor een van de bestands uitvoer onderdelen en open de expressie-editor voor de bestands eigenschap. Het eerste uitvoer bestand is via de volgende expressie geconfigureerd (Zie de zelf studie voor het [uitvoeren van MXF naar een enkele bitrate MP4-uitvoer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Dit betekent dat de bestands naam wordt bepaald door twee variabelen: de uitvoermap waarnaar moet worden geschreven en de basis naam van het bron bestand. De eerste wordt weer gegeven als een eigenschap op de hoofdmap van de werk stroom en de laatste wordt bepaald door het inkomende bestand. De uitvoermap is wat u gebruikt voor lokale tests; Deze eigenschap wordt overschreven door de werk stroom engine wanneer de werk stroom wordt uitgevoerd door de cloud-gebaseerde media processor in Azure Media Services.
Als u beide uitvoer bestanden een consistente uitvoer naam wilt geven, wijzigt u de eerste expressie voor de naamgeving van bestanden in:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

en de tweede tot:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Voer een tussenliggende test uit om ervoor te zorgen dat beide MP4-uitvoer bestanden correct worden gegenereerd.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Een afzonderlijk audio nummer toevoegen
Zoals we later zien wanneer we een ISM-bestand genereren om te gaan met de MP4-uitvoer bestanden, moeten we ook een MP4-bestand met audio-indeling vereisen als het audio spoor voor onze adaptieve streaming. Als u dit bestand wilt maken, voegt u een extra muxer toe aan de werk stroom (ISO-MPEG-4 multiplexer) en verbindt u de uitvoer pincode van het AAC-coderings programma met de invoer pincode voor track 1.

![Audio-muxer toegevoegd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio-muxer toegevoegd*

Maak een derde file output-component om de uitgaande stroom van de muxer uit te voeren en configureer de naamgevings expressie voor bestanden als:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio-muxer voor het maken van bestands uitvoer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio-muxer voor het maken van bestands uitvoer*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>De toevoegen. ISM SMIL-bestand
Als u wilt dat de dynamische verpakking in combi natie met zowel MP4-bestanden (en de alleen-audio-MP4) in onze Media Services-Asset werkt, hebt u ook een manifest bestand nodig (ook wel een ' SMIL-bestand: Synchronized Multi Media Integration Language ' genoemd). Dit bestand geeft aan Azure Media Services welke MP4-bestanden beschikbaar zijn voor dynamische pakketten en welke van die voor het streamen van audio. Een typisch manifest bestand voor een set MP4's met één audio stroom ziet er als volgt uit:

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

Het ISM-bestand bevinden zich in een instructie switch, een verwijzing naar elk van de afzonderlijke MP4-video bestanden en daarnaast ook een (of meer) audio bestand verwijst naar een MP4 die alleen de audio bevat.

Het genereren van het manifest bestand voor onze set MP4's kan worden uitgevoerd via een onderdeel dat de ' AMS-manifest schrijver ' wordt genoemd. Als u deze wilt gebruiken, sleept u deze naar het Opper vlak en verbindt u de uitvoer pincodes voor ' Schrijf voltooid ' van de drie onderdelen voor bestands uitvoer naar de AMS manifest Writer-invoer. Zorg er vervolgens voor dat u de uitvoer van de AMS-manifest schrijver verbindt met het uitvoer bestand/-activum.

Net als bij onze andere onderdelen voor bestands uitvoer configureert u de bestands uitvoer naam. ism met een expressie:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Onze voltooide werk stroom ziet er als volgt uit:

![MXF is voltooid voor de multibitrate-MP4-werk stroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF is voltooid voor de multibitrate-MP4-werk stroom*

## <a id="MXF_to__multibitrate_MP4"></a>MXF coderen in multibitrate MP4-Enhanced blauw druk
In het [vorige werk stroom scenario](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) hebben we gezien hoe een enkele MXF-invoer Asset kan worden omgezet in een uitvoer-Asset met multi-bitrate MP4-bestanden, een alleen-audio-MP4-bestand en een manifest bestand dat kan worden gebruikt in combi natie met Azure Media Services dynamische verpakking.

In dit scenario ziet u hoe een deel van de aspecten kan worden verbeterd en gemakkelijker kan worden gemaakt.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Werk stroom overzicht om te verbeteren
![MP4-werk stroom met multibitrate om te verbeteren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*MP4-werk stroom met multibitrate om te verbeteren*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Naamgevings regels voor bestanden
In de vorige werk stroom hebben we een eenvoudige expressie opgegeven als basis voor het genereren van uitvoer bestands namen. We hebben een paar duplicatie als: alle afzonderlijke onderdelen van het uitvoer bestand hebben deze expressie opgegeven.

Bijvoorbeeld, het onderdeel voor bestands uitvoer voor het eerste video bestand is geconfigureerd met deze expressie:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Voor de tweede uitvoer video hebben we een expressie als:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Zou het niet duidelijk zijn, minder fout gevoelig en handiger zijn als we een deel van deze duplicatie kunnen verwijderen en in plaats daarvan meer configureerbaar moeten maken? Gelukkig we kunnen: de Expression-functies in combi natie met de mogelijkheid om aangepaste eigenschappen te maken op basis van de werk stroom, bieden een extra laag voor het gemak.

We gaan ervan uit dat de bestands namen van de afzonderlijke MP4-bestanden worden geconfigureerd. Deze bitrates zijn bedoeld om te worden geconfigureerd op één centrale locatie (in de hoofdmap van onze grafiek), vanaf waar ze toegang hebben tot het configureren en het genereren van de bestands naam. Hiervoor publiceert u eerst de eigenschap bitrate van beide AVC-encoders naar de hoofdmap van onze werk stroom, zodat deze toegankelijk is vanuit zowel de hoofdmap als de AVC-encoders. (Zelfs als dit wordt weer gegeven in twee verschillende vlekken, is er slechts één onderliggende waarde.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Onderdeel eigenschappen publiceren naar de hoofdwerk stroom
Open de eerste AVC-encoder, ga naar de eigenschap bitrate (kbps) en kies publiceren in de vervolg keuzelijst.

![De eigenschap bitrate publiceren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*De eigenschap bitrate publiceren*

Configureer het dialoog venster publiceren om te publiceren naar de hoofdmap van onze werk stroom grafiek, met een gepubliceerde naam "video1bitrate" en een lees bare weergave naam van "video 1 bitrate". Configureer de naam ' streaming bitrate ' in een aangepaste groep en klik op publiceren.

![De eigenschap bitrate publiceren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Dialoog venster voor het publiceren van de eigenschap bitrate*

Herhaal hetzelfde voor de eigenschap bitrate van het tweede AVC-coderings programma en geef het de naam ' video2bitrate ' met de weergave naam ' video 2 bitrate ' in dezelfde aangepaste groep ' streaming bitrates '.

Als we nu de hoofd eigenschappen van de werk stroom controleren, zien we onze aangepaste groep met de twee gepubliceerde eigenschappen worden weer gegeven. Beide geven een weer spie gelen van de waarde van hun respectieve AVC encoder-bitsnelheid.

![Uitgegeven bitrate-eigenschappen op hoofdwerk stroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Wanneer we toegang willen krijgen tot deze eigenschappen vanuit code of vanuit een expressie, kunnen we dit als volgt doen:

* van inline code van een onderdeel, rechts onder de hoofdmap: node. getPropertyAsString ('.. /video1bitrate ', null)
* binnen een expressie: $ {ROOT_video1bitrate}

We gaan de groep ' streaming bitrates ' nu volt ooien door onze audio track-bitsnelheid ook te publiceren. In de eigenschappen van het AAC-coderings programma zoekt u naar de instelling bitrate en selecteert u publiceren in de vervolg keuzelijst ernaast. Publiceer naar de hoofdmap van de grafiek met de naam ' audio1bitrate ' en de weergave naam ' Audio 1 bitrate ' in onze aangepaste groep ' streaming bitrates '.

![Dialoog venster publiceren voor bitsnelheid audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialoog venster publiceren voor bitsnelheid audio*

![Resulterende video-en audio-eigenschappen in de hoofdmap](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Resulterende video-en audio-eigenschappen in de hoofdmap*

Als u een van deze drie waarden wijzigt, worden de waarden ook opnieuw geconfigureerd en gewijzigd in de respectieve onderdelen waaraan ze zijn gekoppeld (en waar deze vanuit zijn gepubliceerd).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>De namen van de uitvoer bestanden hebben gegenereerd, is afhankelijk van de waarden van gepubliceerde eigenschappen
In plaats van de namen van de gegenereerde bestanden te hardcoding, kunnen we nu de bestandsnaam expressie voor elk van de onderdelen van de bestands uitvoer wijzigen zodat deze afhankelijk is van de eigenschappen van de bitsnelheid die we hebben gepubliceerd op de basis van de grafiek. Ga vanaf onze eerste bestands uitvoer naar de bestands eigenschap en bewerk de expressie als volgt:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

De verschillende para meters in deze expressie zijn toegankelijk en ingevoerd door het dollar teken op het toetsen bord te gebruiken terwijl u in het expressie venster ziet. Een van de beschik bare para meters is onze eigenschap video1bitrate die we eerder hebben gepubliceerd.

![Toegang tot para meters in een expressie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Toegang tot para meters in een expressie*

Doe hetzelfde voor de bestands uitvoer voor onze tweede video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

en voor de bestands uitvoer met alleen audio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Als we de bitsnelheid voor een van de video-of audio bestanden nu wijzigen, wordt de bijbehorende Encoder opnieuw geconfigureerd en wordt de bestands naam Conventie op basis van bitsnelheid alle automatisch uitgevoerd.

## <a id="thumbnails_to__multibitrate_MP4"></a>Miniaturen toevoegen aan de multibitrate-MP4-uitvoer
Vanaf een werk stroom die [een multibitrate-MP4 genereert vanuit een MXF-invoer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), zullen we nu miniaturen toevoegen aan de uitvoer.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Werk stroom overzicht om miniaturen toe te voegen aan
![MP4-werk stroom voor multibitrate om te starten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*MP4-werk stroom voor multibitrate om te starten*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG-code ring toevoegen
De kern van de generatie van de miniaturen is het JPG-coderings onderdeel, waarmee JPG-bestanden kunnen worden uitgevoerd.

![JPG-coderings programma](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG-coderings programma*

We kunnen onze niet-gecomprimeerde video stroom echter niet rechtstreeks verbinden vanuit het Media bestand invoer in het JPG-coderings programma. In plaats daarvan verwacht de afzonderlijke frames aan de hand. Dit kunt u doen via het onderdeel video frame poort.

![Een frame poort koppelen aan het JPG-coderings programma](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Een frame poort koppelen aan het JPG-coderings programma*

De frame-Gate keer zoveel seconden of frames staat toe dat een video frame wordt door gegeven. Het interval en de tijds duur waarmee dit gebeurt, kunnen worden geconfigureerd in de eigenschappen.

![Eigenschappen van video frame poort](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Eigenschappen van video frame poort*

We gaan elke minuut een miniatuur maken door de modus in te stellen op tijd (seconden) en het interval op 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Omgaan met de conversie van kleur ruimte
Hoewel het logisch zou zijn dat niet-gecomprimeerde video pincodes van de frame poort en de invoer van het Media bestand nu kunnen worden verbonden, wordt een waarschuwing weer gegeven als dit wel het geval is.

![Fout in de invoer kleur ruimte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Fout in de invoer kleur ruimte*

Dit komt doordat de manier waarop kleur informatie wordt weer gegeven in onze oorspronkelijke onbewerkte, ongecomprimeerde video stroom, afkomstig van onze MXF, afwijkt van wat het JPG-coderings programma verwacht. In het bijzonder, een zogenaamde ' kleur ruimte ' van ' RGB ' of ' grijs waarde ', wordt naar verwachting stromen. Dit betekent dat er voor de inkomende video stroom van de video frame poort een conversie moet worden toegepast met betrekking tot de kleur ruimte eerst.

Sleep naar de werk stroom het conversie programma voor kleur ruimte-Intel en verbind deze met onze frame poort.

![Een conversie van een kleur ruimte maken](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Een conversie van een kleur ruimte maken*

Kies in het venster Eigenschappen de vermelding BGR 24 in de lijst voor instelling.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>De miniaturen worden geschreven
Het JPG-coderings onderdeel maakt niet uit van onze MP4 video meer dan een bestand. Als u dit wilt aanpakken, kunt u een scène-onderdeel voor het maken van scènes in het bestand Writer gebruiken: het maakt deel uit van de binnenkomende JPG-miniaturen en schrijft deze, waarbij elke bestands naam door een ander nummer wordt gebruikt. (Het getal geeft meestal het aantal seconden/eenheden in de stroom op waaruit de miniatuur is opgehaald.)

![Introductie van de schrijver van het JPG-bestand van scène zoeken](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introductie van de schrijver van het JPG-bestand van scène zoeken*

Configureer de eigenschap pad naar de map uitvoermap met de expressie: $ {ROOT_outputWriteDirectory}

en de eigenschap bestands naam voor voegsel met:

    ${ROOT_sourceFileBaseName}_thumb_

Het voor voegsel bepaalt hoe de miniatuur bestanden worden genoemd. Ze zijn een achtervoegsel met een getal dat de positie van de duim in de stroom aangeeft.

![Eigenschappen van bestands schrijver voor scène zoeken in JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Eigenschappen van bestands schrijver voor scène zoeken in JPG*

Verbind het JPG-bestand van scène zoeken naar het knoop punt uitvoer bestand/activum.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Fouten in een werk stroom detecteren
Verbind de invoer van de kleur ruimte-Converter met de onbewerkte, niet-gecomprimeerde video-uitvoer. Voer nu een lokale test uit voor de werk stroom. Er is een goede kans dat de werk stroom plotseling stopt met uitvoeren en wordt aangeduid met een rood overzicht van het onderdeel waarin een fout is opgetreden:

![Conversie fout in kleur ruimte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Conversie fout in kleur ruimte*

Klik op het pictogram kleine rode E in de rechter bovenhoek van het onderdeel kleur ruimte-Converter om te zien wat de reden is waarom de coderings poging is mislukt.

![Fout in dialoog venster conversie programma kleur ruimte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Fout in dialoog venster conversie programma kleur ruimte*

Het wordt weer geven, zoals u kunt zien, dat de standaard waarde voor de standaardkleur ruimte voor het conversie programma voor kleur ruimte moet worden rec601 voor de aangevraagde conversie van YUV naar RGB. Blijkbaar duidt onze stroom niet op de rec601. (REC 601 is een standaard voor het coderen van geïnterlinieerde analoge video signalen in een digitaal video formulier. Hiermee geeft u een actieve regio met 720 luminantie-voor beelden en 360 Chrominance-voor beelden per regel op. Het kleur coderings systeem staat bekend als YCbCr 4:2:2.)

Om dit probleem op te lossen, geven we de meta gegevens aan van de stroom die we verwerken met rec601-inhoud. Hiervoor gebruiken we een Updater onderdeel video gegevens type, dat we plaatsen tussen onze RAW-bron en het conversie onderdeel voor kleur ruimte. Met deze Updater voor gegevens typen kan bepaalde eigenschappen van het video gegevens type hand matig worden bijgewerkt. Configureer deze om aan te geven dat de kleur ruimte standaard is ingesteld op ' Rec 601 '. Dit leidt ertoe dat de video gegevens type-Updater de stroom labelt met de kleur ruimte ' Rec 601 ' als er nog geen kleur ruimte is gedefinieerd. (Alle bestaande meta gegevens worden niet overschreven, tenzij het selectie vakje voor onderdrukking is ingeschakeld.)

![De kleur ruimte standaard bijwerken voor het gegevens type bijwerken](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*De kleur ruimte standaard bijwerken voor het gegevens type bijwerken*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Voltooide werk stroom
Nu de werk stroom is voltooid, voert u een andere test uit om te zien hoe deze wordt door gegeven.

![Voltooide werk stroom voor multi-MP4-uitvoer met miniaturen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Voltooide werk stroom voor multi-MP4-uitvoer met miniaturen*

## <a id="time_based_trim"></a>Op tijd gebaseerde verkleining van multibitrate MP4-uitvoer
Vanaf een werk stroom die [een multibitrate-MP4 genereert vanuit een MXF-invoer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), zullen we nu de bron video op basis van tijds tempels bekijken.

### <a id="time_based_trim_start"></a>Werk stroom overzicht om bijsnijden toe te voegen aan
![Werk stroom starten om toe te voegen aan bijsnijden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Werk stroom starten om toe te voegen aan bijsnijden*

### <a id="time_based_trim_use_stream_trimmer"></a>De stream Trimmer gebruiken
Met het onderdeel stream trimmer kunt u het begin en einde van een invoer stroom baseren op timing gegevens (seconden, minuten,...). De trimmer biedt geen ondersteuning voor het verkleinen van frames.

![Gegevens stroom bijsnijden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Gegevens stroom bijsnijden*

In plaats van de AVC-encoders en de toewijzing van de luidspreker positie rechtstreeks aan de media bestands invoer toe te voegen, worden de gegevens in de stream afgekapt. (Een voor het video signaal en één voor het Interleaved audio signaal.)

![Plaats de stream trimmer tussen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Plaats de stream trimmer tussen*

We gaan de trimmer zo configureren dat er alleen video en audio tussen 15 seconden en 60 seconden in de video worden verwerkt.

Ga naar de eigenschappen van de video stream trimmer en configureer de eigenschappen start tijd (15 en) en eind tijd (60 s). Om ervoor te zorgen dat de audio-en video trimmer altijd worden geconfigureerd voor dezelfde begin-en eind waarden, publiceren we die in de hoofdmap van de werk stroom.

![De eigenschap begin tijd publiceren vanuit de stream-trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*De eigenschap begin tijd publiceren vanuit de stream-trimmer*

![Dialoog venster voor publicatie-eigenschappen voor start tijd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Dialoog venster voor publicatie-eigenschappen voor start tijd*

![Dialoog venster voor publicatie-eigenschappen voor eind tijd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Dialoog venster voor publicatie-eigenschappen voor eind tijd*

Als we nu de hoofdmap van onze werk stroom controleren, worden beide eigenschappen netjes weer gegeven en kunnen deze van daaruit worden geconfigureerd.

![Gepubliceerde eigenschappen die beschikbaar zijn in de hoofdmap](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Gepubliceerde eigenschappen die beschikbaar zijn in de hoofdmap*

Open nu de eigenschappen voor het verkleinen van de audio trimmer en configureer begin-en eind tijden met een expressie die verwijst naar de gepubliceerde eigenschappen in de hoofdmap van onze werk stroom.

Voor de begin tijd van de audio-bijsnijden:

    ${ROOT_TrimmingStartTime}

en voor de eind tijd:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Voltooide werk stroom
![Voltooide werk stroom](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Voltooide werk stroom*

## <a id="scripting"></a>Introductie van het script onderdeel
Script onderdelen kunnen wille keurige scripts uitvoeren tijdens de uitvoerings fasen van onze werk stroom. Er zijn vier verschillende scripts die kunnen worden uitgevoerd, elk met specifieke kenmerken en hun eigen plaats in de levens cyclus van de werk stroom:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

De documentatie van het script onderdeel gaat meer Details voor elk van de bovenstaande onderdelen. In [de volgende sectie](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)wordt het script onderdeel **realizeScript** gebruikt voor het maken van een cliplist-XML wanneer de werk stroom wordt gestart. Dit script wordt aangeroepen tijdens de configuratie van het onderdeel, dat slechts eenmaal in de levens cyclus plaatsvindt.

### <a id="scripting_hello_world"></a>Scripts uitvoeren in een werk stroom: Hallo wereld
Sleep een script onderdeel naar het ontwerp oppervlak en wijzig de naam ervan (bijvoorbeeld ' SetClipListXML ').

![Een script onderdeel toevoegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Een script onderdeel toevoegen*

Wanneer u de eigenschappen van het script onderdeel inspecteert, worden de vier verschillende script typen weer gegeven, die elk kunnen worden geconfigureerd in een ander script.

![Eigenschappen van script onderdeel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Eigenschappen van script onderdeel*

Wis de processInputScript en open de editor voor de realizeScript. Nu kunnen we beginnen met het uitvoeren van scripts.

Scripts worden geschreven in groovy, een dynamisch gecompileerde script taal voor het Java-platform dat compatibiliteit met Java behoudt. Eigenlijk is de meeste Java-code geldige groovy-code.

Laten we een eenvoudig Hello World Groovy-script schrijven in de context van onze realizeScript. Voer het volgende in de editor in:

    node.log("hello world");

Voer nu een lokale test uit. Na deze uitvoering inspecteert u (via het tabblad systeem van het onderdeel script) de logboeken-eigenschap.

![Hello World-logboek uitvoer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello World-logboek uitvoer*

Het knooppunt object roept de methode log aan bij, verwijst naar het huidige knoop punt of het onderdeel waarin het script wordt uitgevoerd. Elk onderdeel als zodanig heeft de mogelijkheid om logboek registratie gegevens uit te voeren die beschikbaar zijn via het tabblad systeem. In dit geval wordt de letterlijke teken reeks "Hallo wereld" uitgevoerd. Het is belang rijk te weten dat dit een onwaardevol hulp programma voor fout opsporing kan zijn, zodat u inzicht krijgt in de manier waarop het script daad werkelijk wordt uitgevoerd.

Vanuit onze script omgeving hebben we ook toegang tot eigenschappen van andere onderdelen. Probeer dit:

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

In ons logboek venster ziet u het volgende:

![Logboek uitvoer voor toegang tot knooppunt paden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Logboek uitvoer voor toegang tot knooppunt paden*

## <a id="frame_based_trim"></a>Op frames gebaseerd knippen van multibitrate MP4-uitvoer
Vanaf een werk stroom die [een multibitrate-MP4 genereert vanuit een MXF-invoer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), zullen we nu de bron video op basis van het aantal frames bekijken.

### <a id="frame_based_trim_start"></a>Overzicht van blauw druk voor het toevoegen van bijsnijden aan
![Werk stroom voor het toevoegen van bijsnijden aan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Werk stroom voor het toevoegen van bijsnijden aan*

### <a id="frame_based_trim_clip_list"></a>De clip lijst-XML gebruiken
In alle vorige werk stroom zelf studies hebben we het onderdeel Media bestands invoer gebruikt als video-invoer bron. Voor dit specifieke scenario wordt in plaats daarvan het bron onderdeel clip List gebruikt. Dit mag niet de voorkeurs manier zijn van werken. Gebruik de lijst Bron clip alleen wanneer dit een echte reden is (zoals in het volgende geval, waarbij we het gebruik van de clip lijst beperken).

Als u wilt overschakelen van de invoer van het Media bestand naar de lijst Bron van de clip, sleept u het bron onderdeel clip List naar het ontwerp oppervlak en verbindt u de clip lijst XML-pincode met het knoop punt clip lijst van de werk stroom ontwerper. Hiermee wordt de clip lijst Bron gevuld met uitvoer pinnen volgens onze invoer video. Verbind nu de niet-gecomprimeerde video en niet-gecomprimeerde geluids pincodes uit de lijst Bron van de clip naar de respectieve AVC-encoders en de interleaver van de audio stroom. Verwijder nu de invoer van het Media bestand.

![De invoer van het Media bestand vervangen door de lijst Bron van de clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*De invoer van het Media bestand vervangen door de lijst Bron van de clip*

Het bron onderdeel van de clip lijst neemt de invoer van een ' clip list XML ' in beslag. Wanneer u het bron bestand selecteert dat u lokaal wilt testen, wordt deze clip list XML automatisch ingevuld.

![Automatisch gevulde clip list XML-eigenschap](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatisch gevulde clip list XML-eigenschap*

Als u een beetje dichter bij het XML-bestand zoekt, ziet het er als volgt uit:

![Dialoog venster clip lijst bewerken](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Dialoog venster clip lijst bewerken*

Dit weerspiegelt echter niet de mogelijkheden van de clip lijst-XML. U kunt het beste een ' Trim '-element toevoegen onder de video-en audio bron, zoals:

![Een knip element toevoegen aan de lijst met clips](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Een knip element toevoegen aan de lijst met clips*

Als u de clip lijst-XML zoals hierboven wijzigt en een lokale test uitvoert, ziet u dat de video op de juiste manier is bijgesneden tussen 10 en 20 seconden in de video.

In tegens telling tot wat er gebeurt wanneer u een lokaal run uitvoert, heeft dezelfde cliplist XML niet hetzelfde effect als het wordt toegepast in een werk stroom die wordt uitgevoerd in Azure Media Services. Wanneer Azure Premium Encoder wordt gestart, wordt de cliplist-XML elke keer opnieuw gegenereerd, op basis van het invoer bestand waarin de coderings taak is opgegeven. Dit betekent dat alle wijzigingen die in het XML-bestand worden aangebracht, worden genegeerd.

Als u de cliplist XML wilt wissen die wordt gewist wanneer een coderings taak wordt gestart, kunnen we deze later opnieuw genereren na het begin van de werk stroom. Dergelijke aangepaste acties kunnen worden uitgevoerd via wat een ' scripted onderdeel ' wordt genoemd. Zie [Inleiding tot het script onderdeel](media-services-media-encoder-premium-workflow-tutorials.md#scripting)voor meer informatie.

Sleep een script onderdeel naar het ontwerp oppervlak en wijzig de naam in ' SetClipListXML '.

![Een script onderdeel toevoegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Een script onderdeel toevoegen*

Wanneer u de eigenschappen van het script onderdeel inspecteert, worden de vier verschillende script typen weer gegeven, die elk kunnen worden geconfigureerd in een ander script.

![Eigenschappen van script onderdeel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Eigenschappen van script onderdeel*

### <a id="frame_based_trim_modify_clip_list"></a>De lijst met clips wijzigen vanuit een script onderdeel
Voordat we de cliplist-XML kunnen herschrijven die tijdens het opstarten van de werk stroom is gegenereerd, moeten we toegang hebben tot de cliplist XML-eigenschappen en-inhoud. We kunnen dit als volgt doen:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Lijst met inkomende clips geregistreerd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lijst met inkomende clips geregistreerd*

Eerst moeten we bepalen op basis van welk punt de video moet worden bijgesneden. Om dit handig te maken voor de minder technische gebruiker van de werk stroom, publiceert u twee eigenschappen naar de hoofdmap van de grafiek. Als u dit wilt doen, klikt u met de rechter muisknop op het ontwerp oppervlak en selecteert u eigenschap toevoegen:

* Eerste eigenschap: "ClippingTimeStart" van het type: "tijd code"
* Tweede eigenschap: "ClippingTimeEnd" van het type: "tijd code"

![Dialoog venster Eigenschappen voor begin tijd van knippen toevoegen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dialoog venster Eigenschappen voor begin tijd van knippen toevoegen*

![Gepubliceerde moment opnamen van de knip tijd op de werk stroom basis](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Gepubliceerde moment opnamen van de knip tijd op de werk stroom basis*

Configureer beide eigenschappen naar een geschikte waarde:

![De eigenschappen voor begin en einde van knippen configureren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*De eigenschappen voor begin en einde van knippen configureren*

Vanuit ons script hebben we nu toegang tot beide eigenschappen, zoals:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Logboek venster met start en einde van knippen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Logboek venster met start en einde van knippen*

We gaan de tijd code-teken reeksen parseren in een gebruiks vriendelijke formulier, met een eenvoudige reguliere expressie:

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

![Logboek venster met uitvoer van geparseerde tijd code](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Logboek venster met uitvoer van geparseerde tijd code*

Aan de hand van deze informatie kunnen we de cliplist-XML nu aanpassen aan de begin-en eind tijden van het gewenste frame nauw keurig knippen van de film.

![Script code voor het toevoegen van trim elementen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Script code voor het toevoegen van trim elementen*

Dit is gedaan door middel van normale bewerkingen voor teken reeks manipulatie. De resulterende gewijzigde clip lijst-XML wordt teruggeschreven naar de eigenschap clipListXML in de hoofdmap van de werk stroom met behulp van de methode setProperty. Het logboek venster nadat een andere test is uitgevoerd, ziet er als volgt uit:

![De resulterende lijst met clips registreren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*De resulterende lijst met clips registreren*

Voer een test uit om te zien hoe de video-en audio gegevensstromen zijn afgekapt. Als u meer dan één test uitvoert met verschillende waarden voor het beperkende punt, zult u merken dat deze niet meer in aanmerking komen. De reden hiervoor is dat de ontwerper, in tegens telling tot de Azure runtime, de cliplist XML niet voor elke uitvoering overschrijft. Dit betekent dat alleen de eerste keer dat u de in-en uitpunten hebt ingesteld, ervoor zorgt dat het XML-bestand, alle andere keren, onze Guard-component (als (`clipListXML.indexOf("<trim>") == -1`)) de werk stroom niet kan toevoegen aan een ander trim element wanneer het al aanwezig is.

Om ervoor te zorgen dat onze werk stroom handig kan worden getest, kunt u het beste een code voor het bewaren van een huis houden om te controleren of een knip element al aanwezig is. Als dit het geval is, kunt u deze verwijderen voordat u doorgaat door de XML te wijzigen met de nieuwe waarden. In plaats van gebruik te maken van een teken reeks manipulatie, is het waarschijnlijk veiliger om dit te doen via real XML object model parsering.

Voordat we dergelijke code kunnen toevoegen, moeten we eerst een aantal import instructies aan het begin van het script toevoegen:

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

Daarna kunt u de vereiste reinigings code toevoegen:

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

Deze code verloopt net boven het punt waarop we de knip elementen toevoegen aan de cliplist XML.

Op dit moment kunnen we onze werk stroom uitvoeren en aanpassen, net zoals we willen, terwijl de wijzigingen ooit worden toegepast.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Een eigenschap van het gemak ClippingEnabled toevoegen
Zoals u mogelijk niet altijd wilt bijsnijden, kunt u de werk stroom eerst volt ooien door een handige Booleaanse vlag toe te voegen die aangeeft of het knippen/knippen moet worden ingeschakeld.

Publiceer net als voorheen een nieuwe eigenschap naar de hoofdmap van de werk stroom met de naam "ClippingEnabled" van het type "BOOLEAN".

![Er is een eigenschap gepubliceerd voor het inschakelen van knippen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Er is een eigenschap gepubliceerd voor het inschakelen van knippen*

Met de onderstaande component Simple Guard kunnen we controleren of het bijsnijden is vereist en besluiten of de lijst met clips als zodanig moet worden gewijzigd of niet.

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

### <a id="code"></a>Volledige code

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
[Introductie van Premium-code ring in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Premium-code ring gebruiken in Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Inhoud op aanvraag coderen met de Azure media-service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Indelingen en codecs voor Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)

[Voor beeld van werk stroom bestanden](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer-hulpprogramma](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
