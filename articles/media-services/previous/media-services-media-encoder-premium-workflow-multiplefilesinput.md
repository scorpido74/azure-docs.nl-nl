---
title: Meerdere invoerbestanden en componenteigenschappen met Premium Encoder - Azure | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u setRuntimeProperties gebruikt om meerdere invoerbestanden te gebruiken en aangepaste gegevens door te geven aan de mediaprocessor Media Encoder Premium Workflow.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.openlocfilehash: 27bdf82d4515678e28eadf07fe325860fe5df063
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250996"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Meerdere invoerbestanden en componenteigenschappen gebruiken met Premium Encoder
## <a name="overview"></a>Overzicht
Er zijn scenario's waarin u mogelijk componenteigenschappen moet aanpassen, XML-inhoud voor cliplijst moet opgeven of meerdere invoerbestanden moet verzenden wanneer u een taak verzendt met de mediaprocessor **Media Encoder Premium Workflow.** Een aantal voorbeelden:

* Tekst op video te overlopen en de tekstwaarde (bijvoorbeeld de huidige datum) in te stellen bij runtime voor elke invoervideo.
* Het aanpassen van de XML voor cliplijst (om een of meer bronbestanden op te geven, met of zonder bijsnijden, enz.).
* Een logoafbeelding op de invoervideo overheig maken terwijl de video is gecodeerd.
* Meerdere audiotaalcodering.

Als u de **Media Encoder Premium-werkstroom** wilt laten weten dat u bepaalde eigenschappen in de werkstroom wijzigt wanneer u de taak maakt of meerdere invoerbestanden verzendt, moet u een configuratietekenreeks gebruiken die **setRuntimeProperties** en/of **transcodeSource**bevat. In dit onderwerp wordt uitgelegd hoe u ze gebruiken.

## <a name="configuration-string-syntax"></a>Syntaxis van de configuratietekenreeks
De configuratietekenreeks die u in de coderingstaak wilt instellen, gebruikt een XML-document dat er als volgt uitziet:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Het volgende is de C#-code die de XML-configuratie uit een bestand leest, deze bijwerkt met de juiste videobestandsnaam en deze doorgeeft aan de taak in een taak:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Eigenschappen van onderdelen aanpassen
### <a name="property-with-a-simple-value"></a>Woning met een eenvoudige waarde
In sommige gevallen is het handig om een componenteigenschap aan te passen samen met het werkstroombestand dat wordt uitgevoerd door Media Encoder Premium Workflow.

Stel dat u een werkstroom hebt ontworpen die tekst op uw video's bedekt en de tekst (bijvoorbeeld de huidige datum) moet worden ingesteld bij uitvoering. U dit doen door de tekst te verzenden die moet worden ingesteld als de nieuwe waarde voor de eigenschap tekst van de overlaycomponent van de coderingstaak. U dit mechanisme gebruiken om andere eigenschappen van een component in de werkstroom te wijzigen (zoals de positie of kleur van de overlay, de bitrate van de AVC-encoder, enz.).

**setRuntimeProperties** wordt gebruikt om een eigenschap in de onderdelen van de werkstroom te overschrijven.

Voorbeeld:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Eigenschap met een XML-waarde
Als u een eigenschap wilt instellen die `<![CDATA[ and ]]>`een XML-waarde verwacht, moet u inkapselen met behulp van .

Voorbeeld:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Zorg ervoor dat een koets `<![CDATA[`niet terug te zetten net na .

### <a name="propertypath-value"></a>waarde propertyPath
In de vorige voorbeelden was de eigenschapPath "/Media File Input/filename" of "/inactiveTimeout" of "clipListXml".
Dit is, in het algemeen, de naam van de component, dan is de naam van de eigenschap. Het pad kan meer of minder niveaus hebben, zoals '/primarySourceFile' (omdat de eigenschap aan de basis van de werkstroom ligt) of '/Videoverwerking/Grafische overlay/Dekking' (omdat de Overlay zich in een groep bevindt).    

Als u het pad en de naam van de eigenschap wilt controleren, gebruikt u de actieknop die direct naast elke eigenschap staat. U op deze actieknop klikken en **bewerken selecteren.** Dit toont u de werkelijke naam van de eigenschap, en direct erboven, de naamruimte.

![Actie/Bewerken](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Eigenschap](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Meerdere invoerbestanden
Elke taak die u indient bij de **Media Encoder Premium-werkstroom** vereist twee elementen:

* De eerste is een *werkstroomitem* dat een werkstroombestand bevat. U werkstroombestanden ontwerpen met behulp van de [Werkstroomontwerper](media-services-workflow-designer.md).
* De tweede is een *media-element* dat het mediabestand(en) bevat dat u wilt coderen.

Wanneer u meerdere mediabestanden naar de encoder van media **encoder Premium-werkstroom** verzendt, gelden de volgende beperkingen:

* Alle mediabestanden moeten zich in dezelfde *Media Asset begeven.* Het gebruik van meerdere media-elementen wordt niet ondersteund.
* U moet het primaire bestand instellen in deze mediaasset (idealiter is dit het belangrijkste videobestand dat de encoder moet verwerken).
* Het is noodzakelijk om configuratiegegevens die het **element setRuntimeProperties** en/of **transcodeSource** bevatten, door te geven aan de processor.
  * **setRuntimeProperties** wordt gebruikt om de eigenschap bestandsnaam of een andere eigenschap in de onderdelen van de werkstroom te overschrijven.
  * **transcodeSource** wordt gebruikt om de XML-inhoud van de cliplijst op te geven.

Verbindingen in de werkstroom:

* Als u een of meer mediabestandsinvoercomponenten gebruikt en van plan bent **setRuntimeProperties** te gebruiken om de bestandsnaam op te geven, verbindt u er vervolgens de primaire bestandscomponentpin niet mee. Zorg ervoor dat er geen verbinding is tussen het primaire bestandsobject en de invoer(en) van het mediabestand.
* Als u xml met cliplijst en één component Mediabron liever gebruikt, u beide met elkaar verbinden.

![Geen verbinding van primair bronbestand met mediabestandsinvoer](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Er is geen verbinding van het primaire bestand met de component Mediabestandsinvoer(s) als u setRuntimeProperties gebruikt om de eigenschap bestandsnaam in te stellen.*

![Verbinding van XML met cliplijst naar bron van cliplijst](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*U XML voor cliplijst verbinden met mediabron en transcodeSource gebruiken.*

### <a name="clip-list-xml-customization"></a>XML-aanpassing van cliplijst
U de XML voor cliplijst in de werkstroom op runtime opgeven met **transcodeSource** in de configuratietekenreeks XML. Hiervoor moet de XML-koppeling van de cliplijst worden aangesloten op de component Mediabron in de werkstroom.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Als u /primarySourceFile wilt opgeven om deze eigenschap te gebruiken om de uitvoerbestanden een naam te geven met 'Expressies', raden we u aan de XML voor cliplijst als eigenschap door te geven *na* de eigenschap /primarySourceFile, om te voorkomen dat de cliplijst wordt overschreven door de instelling /primarySourceFile.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Met extra frame-nauwkeurige trimmen:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Voorbeeld 1 : Een afbeelding boven op de video bedekken

### <a name="presentation"></a>Stijl
Overweeg een voorbeeld waarin u een logoafbeelding op de invoervideo wilt bedekken terwijl de video is gecodeerd. In dit voorbeeld wordt de invoervideo "Microsoft_HoloLens_Possibilities_816p24.mp4" genoemd en wordt het logo "logo.png" genoemd. U moet de volgende stappen uitvoeren:

* Maak een werkstroomasset met het werkstroombestand (zie het volgende voorbeeld).
* Maak een media-asset, die twee bestanden bevat: MyInputVideo.mp4 als het primaire bestand en MyLogo.png.
* Stuur een taak naar de mediaprocessor Media Encoder Premium Workflow met de bovenstaande invoerelementen en geef de volgende configuratietekenreeks op.

Configuratie:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

In het bovenstaande voorbeeld wordt de naam van het videobestand verzonden naar de component Mediabestandsinvoer en de eigenschap primarySourceFile. De naam van het logobestand wordt verzonden naar een andere mediabestandsinvoer die is verbonden met de component grafische overlay.

> [!NOTE]
> De naam van het videobestand wordt verzonden naar de eigenschap primarySourceFile. De reden hiervoor is om deze eigenschap te gebruiken in de werkstroom voor het bouwen van de juiste uitvoerbestandsnaam met behulp van expressies, bijvoorbeeld.

### <a name="step-by-step-workflow-creation"></a>Stapsgewijze werkstroomcreatie
Dit zijn de stappen om een werkstroom te maken die twee bestanden als invoer nodig heeft: een video en een afbeelding. Het zal overlay het beeld op de top van de video.

Open **Workflow Designer** en selecteer Blueprint voor **nieuwe** > **werkruimtebestand** > **.**

De nieuwe werkstroom toont drie elementen:

* Primaire bronbestand
* XML voor cliplijst
* Uitvoerbestand/-actief  

![Nieuwe coderingswerkstroom](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nieuwe coderingswerkstroom*

Als u het invoermediabestand wilt accepteren, begint u met het toevoegen van een component Mediabestandsinvoer. Als u een component aan de werkstroom wilt toevoegen, zoekt u deze in het zoekvak Repository en sleept u het gewenste item naar het ontwerpdeelvenster.

Voeg vervolgens het videobestand toe dat moet worden gebruikt voor het ontwerpen van uw werkstroom. Klik hiervoor op het achtergrondvenster in Workflow Designer en zoek naar de eigenschap Primair bronbestand in het deelvenster eigenschap rechts. Klik op het mappictogram en selecteer het juiste videobestand.

![Primaire bestandsbron](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primaire bestandsbron*

Geef vervolgens het videobestand op in de component Mediabestandsinvoer.   

![Bron voor mediabestandinvoer](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Bron voor mediabestandinvoer*

Zodra dit is gebeurd, inspecteert de component Mediabestandsinvoer het bestand en vult de uitvoerpins de uitvoerpinnen in om het bestand weer te geven dat het heeft geïnspecteerd.

De volgende stap is het toevoegen van een "Video Data Type Updater" om de kleurruimte op te geven aan Rec.709. Voeg een 'Video Format Converter' toe die is ingesteld op gegevensindeling/indelingstype = Configureerbare planar. Hiermee wordt de videostream omgezet naar een indeling die kan worden opgevat als een bron van de overlaycomponent.

![Updater van het type video en converter voor opmaak](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Updater van videogegevenstype en converter voor opmaak*

![Indelingstype = Configureerbare planar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Indelingstype is Configureerbare planar*

Voeg vervolgens een component Video Overlay toe en sluit de (niet-gecomprimeerde) videopin aan op de (niet-gecomprimeerde) videopin van de invoer van het mediabestand.

Voeg nog een mediabestandsinvoer toe (om het logobestand te laden), klik op deze component en wijzig deze in 'MediaFile Input Logo' en selecteer een afbeelding (bijvoorbeeld een PNG-bestand) in de bestandseigenschap. Sluit de niet-gecomprimeerde afbeeldingspin aan op de niet-gecomprimeerde afbeeldingspin van de overlay.

![Overlay-component- en afbeeldingsbestandsbron](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Overlay-component- en afbeeldingsbestandsbron*

Als u de positie van het logo op de video wilt wijzigen (u deze bijvoorbeeld op 10 procent van de linkerbovenhoek van de video plaatsen), schakelt u het selectievakje Handmatige invoer uit. U dit doen omdat u een mediabestandsinvoer gebruikt om het logobestand aan de overlaycomponent te verstrekken.

![Overlaypositie](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Overlaypositie*

Als u de videostream naar H.264 wilt coderen, voegt u de avc-video-encoder-encodercomponenten toe aan het ontwerpoppervlak. Sluit de pinnen aan.
Stel de OC-encoder in en selecteer Conversie/voorinstelling audioformaat: 2.0 (L, R).

![Audio- en video-encoders](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Audio- en video-encoders*

Voeg nu de **ISO Mpeg-4 Multiplexer-** en **Bestandsuitvoercomponenten** toe en sluit de pinnen zoals afgebeeld.

![MP4-multiplexer en bestandsuitvoer](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4-multiplexer en bestandsuitvoer*

U moet de naam voor het uitvoerbestand instellen. Klik op de component **Bestandsuitvoer** en bewerk de expressie voor het bestand:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Naam bestandsuitvoer](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Naam bestandsuitvoer*

U de werkstroom lokaal uitvoeren om te controleren of deze correct wordt uitgevoerd.

Nadat deze is voltooid, u het uitvoeren in Azure Media Services.

Bereid eerst een asset voor in Azure Media Services met twee bestanden erin: het videobestand en het logo. U dit doen met behulp van de .NET of REST API. U dit ook doen met de Azure-portal of [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

In deze zelfstudie ziet u hoe u assets beheren met AMSE. Er zijn twee manieren om bestanden toe te voegen aan een asset:

* Maak een lokale map, kopieer de twee bestanden erin en sleep en zet de map naar het tabblad **Asset.**
* Upload het videobestand als een asset, geef de asset-informatie weer, ga naar het tabblad bestanden en upload een extra bestand (logo).

> [!NOTE]
> Zorg ervoor dat u een primair bestand instelt in het element (het hoofdvideobestand).

![Assetbestanden in AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Assetbestanden in AMSE*

Selecteer het item en kies ervoor om het te coderen met Premium Encoder. Upload de werkstroom en selecteer deze.

Klik op de knop om gegevens door te geven aan de processor en voeg de volgende XML toe om de runtime-eigenschappen in te stellen:

![Premium Encoder in AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium Encoder in AMSE*

Plak vervolgens de volgende XML-gegevens. U moet de naam van het videobestand opgeven voor zowel de mediabestandsinvoer als primarySourceFile. Geef ook de naam van de bestandsnaam voor het logo op.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![SetRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*SetRuntimeProperties*

Als u de .NET SDK gebruikt om de taak te maken en uit te voeren, moeten deze XML-gegevens worden doorgegeven als de configuratietekenreeks.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Nadat de taak is voltooid, geeft het MP4-bestand in de uitvoerasset de overlay weer!

![Overlay op de video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Overlay op de video*

U de voorbeeldworkflow downloaden van [GitHub.](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/)

## <a name="example-2--multiple-audio-language-encoding"></a>Voorbeeld 2 : Meervoudige codering van audiotalen

Een voorbeeld van meerdere werkstroom voor het coderen van audiotalen is beschikbaar in [GitHub.](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding)

Deze map bevat een voorbeeldwerkstroom die kan worden gebruikt om een MXF-bestand te coderen voor een multi MP4-bestandselement met meerdere audiotracks.

Deze werkstroom gaat ervan uit dat het MXF-bestand één audiotrack bevat; de extra audiotracks moeten worden doorgegeven als afzonderlijke audiobestanden (WAV of MP4...).

Volg de volgende stappen om te coderen:

* Maak een Media Services-asset met het MXF-bestand en de audiobestanden (0 tot 18 audiobestanden).
* Zorg ervoor dat het MXF-bestand is ingesteld als een primair bestand.
* Maak een taak en een taak met behulp van de Premium Workflow Encoder-processor. Gebruik de geleverde werkstroom (MultiMP4-1080p-19audio-v1.workflow).
* Geef de gegevens van setruntime.xml door aan de taak (als u Azure Media Services Explorer gebruikt, gebruikt u de knop Xml-gegevens doorgeven aan de werkstroom).
  * Werk de XML-gegevens bij om de juiste bestandsnamen en talentags op te geven.
  * De workflow heeft audiocomponenten met de naam Audio 1 tot Audio 18.
  * RFC5646 wordt ondersteund voor de taaltag.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Het gecodeerde item bevat audiotracks in meerdere talen en deze tracks moeten kunnen worden geselecteerd in Azure Media Player.

## <a name="see-also"></a>Zie ook
* [Premium-codering introduceren in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Premium-codering gebruiken in Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [On-demand content coderen met Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Indelingen en codecs voor Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)
* [Voorbeeldwerkstroombestanden](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer-hulpprogramma](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
