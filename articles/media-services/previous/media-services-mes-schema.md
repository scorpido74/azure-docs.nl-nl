---
title: Media Encoder Standard-schema | Microsoft Documenten
description: In dit artikel worden enkele elementen en typen van het XML-schema beschreven waarop media-encoderstandaardvoorinstellingen zijn gebaseerd.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 622f14beabb1f2f109dff5d28c1591ffdd5aa000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901438"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard-schema
In dit artikel worden enkele elementen en typen van het XML-schema beschreven waarop [media-encoderstandaardvoorinstellingen](media-services-mes-presets-overview.md) zijn gebaseerd. Het artikel geeft uitleg over elementen en hun geldige waarden.  

## <a name="preset-root-element"></a><a name="Preset"></a>Vooraf ingesteld (hoofdelement)
Hiermee definieert u een coderingsvoorinstelling.  

### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Encoding** |[Encoding](media-services-mes-schema.md#Encoding) |Hoofdelement geeft aan dat de invoerbronnen moeten worden gecodeerd. |
| **Uitvoer** |[Uitvoer](media-services-mes-schema.md#Output) |Verzameling van gewenste uitvoerbestanden. |
| **StretchMode StretchMode**<br/>minOccurs="0"<br/>default="AutoSize|xs:tekenreeks|Bedien de grootte van het uitvoervideoframe, de opvulling, de pixel of de weergaveverhouding. **StretchMode** kan een van de volgende waarden zijn: **Geen,** **AutoSize** (standaard) of **AutoFit**.<br/><br/>**Geen:** Volg strikt de uitvoerresolutie (bijvoorbeeld de **breedte** en **hoogte** in de voorinstelling) zonder rekening te houden met de pixelbeeldverhouding of de weergaveverhouding van de invoervideo. Aanbevolen in scenario's zoals [bijsnijden,](media-services-crop-video.md)waarbij de uitvoervideo een andere beeldverhouding heeft in vergelijking met de invoer. <br/><br/>**AutoSize:** de uitvoerresolutie past in het venster (Breedte * Hoogte) opgegeven op voorinstelling. De encoder produceert echter een uitvoervideo met een vierkante (1:1) pixelverhouding. Daarom kan de uitvoerbreedte of de uitvoerhoogte worden overschreven om de weergaveverhouding van de invoer te evenaren, zonder opvulling. Als de invoer bijvoorbeeld 1920x1080 is en de coderingsvoorinstelling 1280x1280 vraagt, wordt de hoogtewaarde in de voorinstelling overschreven en bevindt de uitvoer zich op 1280x720, waardoor de verhouding van de invoeraspect van 16:9 wordt behouden. <br/><br/>**AutoFit**: Indien nodig, pad de output video (met een brievenbus of pillarbox) om de gewenste uitgangsresolutie te eren, terwijl ervoor te zorgen dat de actieve video regio in de output heeft dezelfde aspect ratio als de input. Stel dat de invoer 1920x1080 is en de coderingsvoorinstelling vraagt om 1280x1280. Dan is de uitgang video zal worden op 1280x1280, maar het zal een binnenste 1280x720 rechthoek van 'actieve video' met aspect ratio van 16:9 bevatten, en letterbox regio's 280 pixels hoog aan de boven-en onderkant. Als de invoer bijvoorbeeld 1440x1080 is en de coderingsvoorinstelling 1280x720 vraagt, bevindt de uitvoer zich op 1280x720, die een binnenste rechthoek van 960x720 bevat bij een beeldverhouding van 4:3 en de kolomvakgebieden 160 pixels breed aan de linkerkant en rechts. 

### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Versie**<br/><br/> Vereist |**xs: decimale** |De vooraf ingestelde versie. De volgende beperkingen zijn van toepassing: xs:fractionDigits value="1" en xs:minInclusive value="1" Bijvoorbeeld **versie="1.0".** |

## <a name="encoding"></a><a name="Encoding"></a>Codering
Bevat een reeks van de volgende elementen:  

### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Instellingen voor H.264 codering van video. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Instellingen voor OC-codering van audio. |
| **BmpImage BmpImage** |[BmpImage BmpImage](media-services-mes-schema.md#BmpImage) |Instellingen voor Bmp-afbeelding. |
| **PngImage (PngImage)** |[PngImage (PngImage)](media-services-mes-schema.md#PngImage) |Instellingen voor png-afbeelding. |
| **JpgAfbeelding** |[JpgAfbeelding](media-services-mes-schema.md#JpgImage) |Instellingen voor de afbeelding van Jpg. |

## <a name="h264video"></a><a name="H264Video"></a>H264Video
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Momenteel wordt alleen one-pass codering ondersteund. |
| **HoofdframeInterval**<br/><br/> minOccurs="0"<br/><br/> **standaard="00:00:02"** |**xs:tijd** |Hiermee bepaalt u de vaste afstand tussen IDR-frames in secondeneenheden. Ook wel aangeduid als de GOP duur. Zie **SceneChangeDetection** voor het bepalen of de encoder van deze waarde kan afwijken. |
| **ScènewijzigingDetectie**<br/><br/> minOccurs="0"<br/><br/> default="false" |**xs: boolean** |Als het true is ingesteld, probeert encoder scèneverandering in de video te detecteren en voegt het een IDR-frame in. |
| **Complexiteit**<br/><br/> minOccurs="0"<br/><br/> default="Gebalanceerd" |**xs:tekenreeks** |Hiermee bepaalt u de afweging tussen de codesnelheid en de videokwaliteit. Kan een van de volgende waarden zijn: **Snelheid,** **Gebalanceerd**of **kwaliteit**<br/><br/> Standaard: **Gebalanceerd** |
| **SyncMode**<br/><br/> minOccurs="0" | |Functie zal worden blootgesteld in een toekomstige release. |
| **H264Lagen**<br/><br/> minOccurs="0" |[H264Lagen](media-services-mes-schema.md#H264Layers) |Verzameling van uitvoervideolagen. |

### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Voorwaarde** |**xs:tekenreeks** | Wanneer de invoer geen video heeft, u de encoder dwingen om een monochrome videotrack in te voegen. Gebruik hiervoor Condition="InsertBlackIfNoVideoBottomLayerOnly" (om een video in te voegen met alleen de laagste bitrate) of Condition="InsertBlackIfNoVideo" (om een video in te voegen bij alle uitvoerbitrates). Zie voor meer informatie [dit](media-services-advanced-encoding-with-mes.md#no_video) artikel.|

## <a name="h264layers"></a><a name="H264Layers"></a>H264Lagen

Als u standaard een invoer verzendt naar de encoder die alleen audio bevat en geen video, bevat het uitvoeritem alleen bestanden met audiogegevens. Sommige spelers kunnen dergelijke uitvoerstromen mogelijk niet verwerken. U de kenmerkinstelling **InsertBlackIfNoVideo** van de H264Video gebruiken om de encoder te dwingen een videotrack toe te voegen aan de uitvoer in dat scenario. Zie voor meer informatie [dit](media-services-advanced-encoding-with-mes.md#no_video) artikel.
              
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Een verzameling H264 lagen. |

## <a name="h264layer"></a><a name="H264Layer"></a>H264Layer
> [!NOTE]
> Videolimieten zijn gebaseerd op de waarden die worden beschreven in de tabel [H264-niveaus.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)  
> 
> 

### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Profiel**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: tekenreeks** |Kan een van de volgende **xs: string** waarden: **Auto**, **Baseline**, **Main**, **High**. |
| **Niveau**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: tekenreeks** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |De bitrate die wordt gebruikt voor deze videolaag, opgegeven in kbps. |
| **MaxBitrate MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |De maximale bitrate die wordt gebruikt voor deze videolaag, opgegeven in kbps. |
| **Buffervenster**<br/><br/> minOccurs="0"<br/><br/> standaard="00:00:05" |**xs: tijd** |Lengte van de videobuffer. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |Breedte van het uitvoervideoframe in pixels.<br/><br/> Momenteel moet u zowel breedte als hoogte opgeven. De breedte en hoogte moeten even getallen zijn. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |Hoogte van het uitvoervideoframe, in pixels.<br/><br/> Momenteel moet u zowel breedte als hoogte opgeven. De breedte en hoogte moeten even getallen zijn.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Aantal B-kaders tussen referentiekaders. |
| **Referentiekaders**<br/><br/> minOccurs="0"<br/><br/> default="3" |**xs:int** |Aantal referentiekaders in een GOP. |
| **EntropieMode**<br/><br/> minOccurs="0"<br/><br/> default="Cabac" |**xs: tekenreeks** |Kan een van de volgende waarden: **Cabac** en **Cavlc**. |
| **Framerate**<br/><br/> minOccurs="0" |rationeel getal |Hiermee bepaalt u de framesnelheid van de uitvoervideo. Gebruik standaard "0/1" om de encoder dezelfde framesnelheid te laten gebruiken als de invoervideo. Toegestane waarden zijn naar verwachting algemene videoframesnelheden. Echter, elke geldige rationele is toegestaan. 1/1 is bijvoorbeeld 1 fps en is geldig.<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29,97 fps) <br/> <br/>**OPMERKING** Als u een aangepaste voorinstelling maakt voor codering met meerdere bitrates, **moeten** alle lagen van de voorinstelling dezelfde waarde van FrameRate gebruiken.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Kopiëren van Azure-media-encoder |
| **Segmenten**<br/><br/> minOccurs="0"<br/><br/> standaard="0" |**xs:int** |Hiermee bepaalt u in hoeveel segmenten een frame is verdeeld. Beveel het gebruik van standaard. |

## <a name="aacaudio"></a><a name="AACAudio"></a>AACAudio
 Bevat een reeks van de volgende elementen en groepen.  

 Zie [OC voor](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)meer informatie over OC.  

### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Profiel**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: tekenreeks** |Kan een van de volgende waarden zijn: **AACLC,** **HEAACV1**, of **HEAACV2**. |

### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Voorwaarde** |**xs: tekenreeks** |Als u de encoder wilt dwingen een asset te produceren die een stille audiotrack bevat wanneer invoer geen audio heeft, geeft u de waarde 'InsertSilenceIfNoAudio' op.<br/><br/> Als u standaard een invoer verzendt naar de encoder die alleen video en geen audio bevat, bevat het uitvoeritem bestanden die alleen videogegevens bevatten. Sommige spelers kunnen dergelijke uitvoerstromen mogelijk niet verwerken. U deze instelling gebruiken om de encoder te dwingen een stille audiotrack toe te voegen aan de uitvoer in dat scenario. |

### <a name="groups"></a>Groepen

| Naslaginformatie | Beschrijving |
| --- | --- |
| [Audiogroep](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Zie beschrijving van [AudioGroup](media-services-mes-schema.md#AudioGroup) om het juiste aantal kanalen, samplingrate en bitsnelheid te kennen dat voor elk profiel kan worden ingesteld. |

## <a name="audiogroup"></a><a name="AudioGroup"></a>Audiogroep
Zie de tabel 'Audiocodec details' die volgt voor meer informatie over welke waarden geldig zijn voor elk profiel.  

### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Kanalen**<br/><br/> minOccurs="0" |**xs: int** |Het aantal gecodeerde audiokanalen. De volgende opties zijn geldig: 1, 2, 5, 6, 8.<br/><br/> Standaard: 2. |
| **Bemonsteringsfrequentie**<br/><br/> minOccurs="0" |**xs: int** |De audiosamplingsnelheid, opgegeven in Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |De bitrate die wordt gebruikt bij het coderen van de audio, opgegeven in kbps. |

### <a name="audio-codec-details"></a>Audio codec details

Audiocodec|Details  
-----------------|---  
**AACLC (AACLC)** |1:<br/><br/> - 11025: &lt;8 &lt; = bitrate 16<br/><br/> - 12000: &lt;8 &lt; = bitrate 16<br/><br/> - 16000: &lt;8 &lt;= bitrate 32<br/><br/>- 22050: &lt;24 &lt; = bitrate 32<br/><br/> - 24000: &lt;24 &lt; = bitrate 32<br/><br/> - 32000: &lt;32 &lt;= bitrate = 192<br/><br/> - 44100: &lt;56 &lt;= bitrate = 288<br/><br/> - 48000: &lt;56 &lt;= bitrate = 288<br/><br/> - 88200 : &lt;128 &lt;= bitrate = 288<br/><br/> - 96000 : &lt;128 &lt;= bitrate = 288<br/><br/> 2:<br/><br/> - 11025: &lt;16 &lt; = bitrate 24<br/><br/> - 12000: &lt;16 &lt; = bitrate 24<br/><br/> - 16000: &lt;16 &lt; = bitrate 40<br/><br/> - 22050: &lt;32 &lt; = bitrate 40<br/><br/> - 24000 : &lt;32 &lt; = bitrate 40<br/><br/> - 32000: &lt;40 &lt;= bitrate = 384<br/><br/> - 44100: &lt;96 &lt;= bitrate = 576<br/><br/> - 48000 : &lt;96 &lt;= bitrate = 576<br/><br/> - 88200: 256 &lt; &lt;= bitrate = 576<br/><br/> - 96000: 256 &lt; &lt;= bitrate = 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt; &lt;= bitrate = 896<br/><br/> - 44100: 240 &lt; &lt;= bitrate = 1024<br/><br/> - 48000: 240 &lt; &lt;= bitrate = 1024<br/><br/> - 88200: 640 &lt; &lt;= bitrate = 1024<br/><br/> - 96000: 640 &lt; &lt;= bitrate = 1024<br/><br/> 8:<br/><br/> - 32000 : &lt;224 &lt;= bitrate = 1024<br/><br/> - 44100 : &lt;384 &lt;= bitrate = 1024<br/><br/> - 48000: 384 &lt; &lt;= bitrate = 1024<br/><br/> - 88200: 896 &lt; &lt;= bitrate = 1024<br/><br/> - 96000: 896 &lt; &lt;= bitrate = 1024  
**HEAACV1** |1:<br/><br/> - 22050: bitrate = 8<br/><br/> - 24000: &lt;8 &lt;= bitrate = 10<br/><br/> - 32000: &lt;12 &lt;= bitrate = 64<br/><br/> - 44100: &lt;20 &lt;= bitrate = 64<br/><br/> - 48000: &lt;20 &lt;= bitrate = 64<br/><br/> - 88200: bitrate = 64<br/><br/> 2:<br/><br/> - 32000: &lt;16 &lt;= bitrate = 128<br/><br/> - 44100: &lt;16 &lt;= bitrate = 128<br/><br/> - 48000: &lt;16 &lt;= bitrate = 128<br/><br/> - 88200 : &lt;96 &lt;= bitrate = 128<br/><br/> - 96000: &lt;96 &lt;= bitrate = 128<br/><br/> 5/6:<br/><br/> - 32000 : &lt;64 &lt;= bitrate = 320<br/><br/> - 44100: &lt;64 &lt;= bitrate = 320<br/><br/> - 48000: &lt;64 &lt;= bitrate = 320<br/><br/> - 88200 : &lt;256 &lt;= bitrate = 320<br/><br/> - 96000: 256 &lt; &lt;= bitrate = 320<br/><br/> 8:<br/><br/> - 32000: &lt;96 &lt;= bitrate = 448<br/><br/> - 44100: &lt;96 &lt;= bitrate = 448<br/><br/> - 48000: &lt;96 &lt;= bitrate = 448<br/><br/> - 88200: 384 &lt; &lt;= bitrate = 448<br/><br/> - 96000: 384 &lt; &lt;= bitrate = 448  
**HEAACV2** |2:<br/><br/> - 22050: &lt;8 &lt;= bitrate = 10<br/><br/> - 24000: &lt;8 &lt;= bitrate = 10<br/><br/> - 32000: &lt;12 &lt;= bitrate = 64<br/><br/> - 44100: &lt;20 &lt;= bitrate = 64<br/><br/> - 48000: &lt;20 &lt;= bitrate = 64<br/><br/> - 88200: &lt;64 &lt;= bitrate = 64  
  
## <a name="clip"></a><a name="Clip"></a>Clip
### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Starttime** |**xs:duur** |Hiermee geeft u de begintijd van een presentatie op. De waarde van StartTime moet overeenkomen met de absolute tijdstempels van de invoervideo. Als het eerste frame van de invoervideo bijvoorbeeld een tijdstempel van 12:00:10.000 heeft, moet StartTime ten minste 12:00:10.000 of hoger zijn. |
| **Duur** |**xs:duur** |Hiermee geeft u de duur van een presentatie op (bijvoorbeeld het verschijnen van een overlay in de video). |

## <a name="output"></a><a name="Output"></a>Output
### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Bestandsnaam** |**xs:tekenreeks** |De naam van het uitvoerbestand.<br/><br/> U macro's gebruiken die in de volgende tabel worden beschreven om de namen van uitvoerbestanden te maken. Bijvoorbeeld:<br/><br/> **"Uitgangen": [ { "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4", "Format": { "Type": "MP4Format" } } ]** |

### <a name="macros"></a>Macro 's

| Macro | Beschrijving |
| --- | --- |
| **{Basisnaam}** |Als u VoD-codering doet, is de {Basename} de eerste 32 tekens van de eigenschap AssetFile.Name van het primaire bestand in het invoerelement.<br/><br/> Als het invoerelement een live-archief is, wordt de {Basename} afgeleid van de trackName-kenmerken in het servermanifest. Als u een subcliptaak indient met de TopBitrate,\>zoals in: "<\>VideoStream TopBitrate</VideoStream ", en het uitvoerbestand video bevat, dan is de {Basename} de eerste 32 tekens van de trackName van de videolaag met de hoogste bitrate.<br/><br/> Als u in plaats daarvan een subcliptaak indient met behulp van\>alle invoerbitrates, zoals '<VideoStream *</VideoStream\>'en het uitvoerbestand video bevat, dan is {Basename} de eerste 32 tekens van de trackName van de bijbehorende videolaag. |
| **{Codec}** |Kaarten naar "H264" voor video en "OC" voor audio. |
| **{Bitrate}** |De doelvideobitrate als het uitvoerbestand video en audio bevat, of als het uitvoerbestand alleen audio bevat. De gebruikte waarde is de bitrate in kbps. |
| **{Kanaal}** |Aantal audiokanalen als het bestand audio bevat. |
| **{Breedte}** |Breedte van de video, in pixels, in het uitvoerbestand, als het bestand video bevat. |
| **{Hoogte}** |Hoogte van de video, in pixels, in het uitvoerbestand, als het bestand video bevat. |
| **{Extensie}** |Overerfd van de eigenschap 'Type' voor het uitvoerbestand. De naam van het uitvoerbestand heeft een extensie die er een van is: "mp4", "ts", "jpg", "png" of "bmp". |
| **{Index}** |Verplicht voor miniatuur. Mag maar één keer aanwezig zijn. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a>Video (complex type overneemt van Codec)
### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Begin** |**xs:tekenreeks** | |
| **Stap** |**xs:tekenreeks** | |
| **Bereik** |**xs:tekenreeks** | |
| **Resolutienarotatie behouden** |**xs:boolean** |Zie voor meer informatie het volgende gedeelte: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a>Resolutienarotatie behouden
Het wordt aanbevolen om de vlag **PreserveResolutionAfterRotation** te gebruiken in combinatie met resolutiewaarden uitgedrukt in procentuele termen (Width="100%" , Height="100%").  

Standaard zijn de instellingen voor de resolutie (Breedte, Hoogte) in de voorinstellingen van de Media Encoder Standard (MES) gericht op video's met 0-gradenrotatie. Als uw invoervideo bijvoorbeeld 1280x720 is met nulgradenrotatie, zorgen de standaardvoorinstellingen ervoor dat de uitvoer dezelfde resolutie heeft.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Als de invoervideo is vastgelegd met niet-nulrotatie (bijvoorbeeld een verticaal gehouden smartphone of tablet), past MES standaard de instellingen voor de resolutie van de code (Breedte, Hoogte) toe op de invoervideo en compenseert u de rotatie. Zie bijvoorbeeld de afbeelding die volgt. De voorinstelling maakt gebruik van Width = "100%", Height = "100%", die MES interpreteert als de vereiste dat de uitvoer 1280 pixels breed en 720 pixels hoog is. Na het draaien van de video, het vervolgens krimpt het beeld te passen in dat venster, wat leidt tot pijler-box gebieden aan de linkerkant en rechts.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

U ook gebruik maken van de vlag **PreserveResolutionAfterRotation** en deze instellen op 'true' (standaard is 'false'). Dus als uw voorinstelling breedte = "100%", Hoogte = "100%" en PreserveResolutionAfterRotation ingesteld op "true", een input video, die is 1280 pixels breed en 720 pixels hoog met 90-graden rotatie produceert een output met nul-graden rotatie, maar 720 pixels breed en 1280 pixels hoog. Zie de volgende foto:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a>FormatGroep (groep)
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **BmpFormat BmpFormat** |**BmpFormat BmpFormat** | |
| **PngFormat (PngFormat)** |**PngFormat (PngFormat)** | |
| **JpgFormat JpgFormat** |**JpgFormat JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a>BmpLayer BmpLayer
### <a name="element"></a>Element

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Voorwaarde** |**xs:tekenreeks** | |

## <a name="pnglayer"></a><a name="PngLayer"></a>PngLayer (PngLayer)
### <a name="element"></a>Element

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Voorwaarde** |**xs:tekenreeks** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a>JpgLayer JpgLayer
### <a name="element"></a>Element

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Kwaliteit**<br/><br/> minOccurs="0" |**xs:int** |Geldige waarden: 1(slechtste)-100(beste) |

### <a name="attributes"></a>Kenmerken

| Name | Type | Beschrijving |
| --- | --- | --- |
| **Voorwaarde** |**xs:tekenreeks** | |

## <a name="pnglayers"></a><a name="PngLayers"></a>PngLagen
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **PngLayer (PngLayer)**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer (PngLayer)](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a>BmpLagen
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **BmpLayer BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a>JpgLagen
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **JpgLayer JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a>BmpImage (complex type erft van Video)
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **PngLagen**<br/><br/> minOccurs="0" |[PngLagen](media-services-mes-schema.md#PngLayers) |Png-lagen |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a>JpgImage (complex type overneemt van Video)
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **PngLagen**<br/><br/> minOccurs="0" |[PngLagen](media-services-mes-schema.md#PngLayers) |Png-lagen |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a>PngImage (complex type overneemt van Video)
### <a name="elements"></a>Elementen

| Name | Type | Beschrijving |
| --- | --- | --- |
| **PngLagen**<br/><br/> minOccurs="0" |[PngLagen](media-services-mes-schema.md#PngLayers) |Png-lagen |

## <a name="examples"></a>Voorbeelden
Zie voorbeelden van XML-voorinstellingen die op basis van dit schema zijn gebouwd, zie [Taakvoorinstellingen voor MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

