---
title: Meerdere invoer bestanden en onderdeel eigenschappen met Premium encoder-Azure | Microsoft Docs
description: In dit onderwerp wordt uitgelegd hoe u setRuntimeProperties gebruikt om meerdere invoer bestanden te gebruiken en aangepaste gegevens door te geven aan de Media Encoder Premium Workflow-media processor.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 70acbb239df3806437127e41510bfebc1358532a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015965"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Meerdere invoer bestanden en onderdeel eigenschappen gebruiken met Premium encoder
## <a name="overview"></a>Overzicht
Er zijn scenario's waarin u mogelijk onderdeel eigenschappen kunt aanpassen, de XML-inhoud van een clip lijst kunt opgeven of meerdere invoer bestanden wilt verzenden wanneer u een taak verzendt met de **Media Encoder Premium workflow** -media processor. Een aantal voorbeelden:

* Tekst op video bedekken en de tekst waarde (bijvoorbeeld de huidige datum) in runtime instellen voor elke invoer video.
* De lijst-XML van de clip aanpassen (om een of meer bron bestanden op te geven, met of zonder te verkleinen, enzovoort).
* Een logo afbeelding bedekken op de invoer video terwijl de video is gecodeerd.
* Meerdere audio taal code ring.

Als u wilt dat de **Media Encoder Premium workflow** weet dat u sommige eigenschappen in de werk stroom wijzigt wanneer u de taak maakt of meerdere invoer bestanden verzendt, moet u een configuratie teken reeks gebruiken die **setRuntimeProperties** en/of **transcodeSource**bevat. In dit onderwerp wordt uitgelegd hoe u deze gebruikt.

## <a name="configuration-string-syntax"></a>Syntaxis van de configuratie teken reeks
De configuratie teken reeks die in de coderings taak moet worden ingesteld, maakt gebruik van een XML-document dat er als volgt uitziet:

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

Hieronder ziet u de C#-code die de XML-configuratie van een bestand leest, deze bijwerkt met de juiste video bestands naam en door gegeven aan de taak in een taak:

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

## <a name="customizing-component-properties"></a>Onderdeel eigenschappen aanpassen
### <a name="property-with-a-simple-value"></a>Eigenschap met een eenvoudige waarde
In sommige gevallen is het handig om een onderdeel eigenschap aan te passen met het werk stroom bestand dat wordt uitgevoerd door Media Encoder Premium Workflow.

Stel dat u een werk stroom hebt ontworpen voor het bedekken van tekst op uw Video's en dat de tekst (bijvoorbeeld de huidige datum) tijdens runtime moet worden ingesteld. U kunt dit doen door het verzenden van de tekst die moet worden ingesteld als de nieuwe waarde voor de eigenschap text van het overlay-onderdeel van de coderings taak. U kunt dit mechanisme gebruiken om andere eigenschappen van een onderdeel in de werk stroom te wijzigen (zoals de positie of kleur van de overlay, de bitsnelheid van de AVC-encoder, enzovoort).

**setRuntimeProperties** wordt gebruikt voor het overschrijven van een eigenschap in de onderdelen van de werk stroom.

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
Als u een eigenschap wilt instellen die een XML-waarde verwacht, inkapselen met behulp van `<![CDATA[ and ]]>` .

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
> Zorg ervoor dat u geen regel terugloop hoeft op te geven `<![CDATA[` .

### <a name="propertypath-value"></a>waarde propertyPath
In de vorige voor beelden was het propertyPath "/media file input/filename" of "/inactiveTimeout" of "clipListXml".
Dit is in het algemeen de naam van het onderdeel, gevolgd door de naam van de eigenschap. Het pad kan meer of minder niveaus hebben, zoals '/primarySourceFile ' (omdat de eigenschap zich in de hoofdmap van de werk stroom bevindt) of '/video processing/dekking/opaciteit ' (omdat de overlay zich in een groep bevindt).    

Als u het pad en de naam van de eigenschap wilt controleren, gebruikt u de actie knop die zich direct naast elke eigenschap bevindt. U kunt op deze actie knop klikken en vervolgens **bewerken**selecteren. Hiermee wordt de werkelijke naam van de eigenschap en de naam ruimte weer gegeven.

![Actie/bewerken](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Eigenschap](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Meerdere invoer bestanden
Voor elke taak die u naar de **Media Encoder Premium workflow** verzendt, zijn twee assets vereist:

* De eerste is een *werk stroom activum* dat een werk stroom bestand bevat. U kunt werk stroom bestanden ontwerpen met behulp van de [Workflow Designer](media-services-workflow-designer.md).
* De tweede is een *medium Asset* dat de media bestanden bevat die u wilt coderen.

Wanneer u meerdere media bestanden naar het coderings programma van **Media Encoder Premium workflow** stuurt, gelden de volgende beperkingen:

* Alle media bestanden moeten zich in hetzelfde media- *activum*bedienen. Het gebruik van meerdere media-assets wordt niet ondersteund.
* U moet het primaire bestand in deze media-asset instellen (in het ideale geval is dit het belangrijkste video bestand dat het coderings programma moet worden verwerkt).
* Het is nood zakelijk om configuratie gegevens met het **setRuntimeProperties** -en/of **transcodeSource** -element aan de processor door te geven.
  * **setRuntimeProperties** wordt gebruikt voor het overschrijven van de eigenschap filename of een andere eigenschap in de onderdelen van de werk stroom.
  * **transcodeSource** wordt gebruikt om de inhoud van de clip lijst-XML op te geven.

Verbindingen in de werk stroom:

* Als u een of meer media bestands invoer onderdelen gebruikt en **setRuntimeProperties** wilt gebruiken om de bestands naam op te geven, sluit u de primaire bestands onderdeel pincode niet aan. Zorg ervoor dat er geen verbinding is tussen het primaire bestands object en de invoer (n) van het Media bestand.
* Als u liever de clip list XML en één media bron onderdeel gebruikt, kunt u beide tegelijk verbinden.

![Geen verbinding van het primaire bron bestand naar de invoer van het Media bestand](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Er is geen verbinding van het primaire bestand naar het Media bestand invoer onderdeel (s) als u setRuntimeProperties gebruikt om de eigenschap filename in te stellen.*

![Verbinding vanuit de clip lijst-XML naar de bron van de clip lijst](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*U kunt de XML van de clip lijst koppelen aan de media bron en transcodeSource gebruiken.*

### <a name="clip-list-xml-customization"></a>Aanpassing van de clip lijst-XML
U kunt de clip lijst-XML in de werk stroom in runtime opgeven met behulp van **transcodeSource** in de configuratie teken reeks-XML. Hiervoor moet de pincode van de clip lijst-XML zijn gekoppeld aan het media bron onderdeel in de werk stroom.

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

Als u/primarySourceFile wilt opgeven om deze eigenschap te gebruiken om de uitvoer bestanden een naam te geven met behulp van ' expressies ', wordt u aangeraden de clip lijst-XML als een eigenschap *na* de eigenschap/primarySourceFile door te geven om te voor komen dat de clip lijst wordt overschreven door de instelling/primarySourceFile.

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

Met aanvullende frame-nauw keurige bijsnijden:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Voor beeld 1: een afbeelding bovenop de video bedekken

### <a name="presentation"></a>Stijl
Bekijk een voor beeld waarin u een logo afbeelding wilt bedekken op de invoer video terwijl de video is gecodeerd. In dit voor beeld heeft de invoer video de naam Microsoft_HoloLens_Possibilities_816p24.mp4 en het logo heeft de naam logo.png. Voer de volgende stappen uit:

* Maak een werk stroom-activum met het werk stroom bestand (Zie het volgende voor beeld).
* Maak een media-activum dat twee bestanden bevat: MyInputVideo.mp4 als het primaire bestand en MyLogo.png.
* Verzend een taak naar de Media Encoder Premium Workflow-media processor met de bovenstaande invoer assets en geef de volgende configuratie teken reeks op.

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

In het bovenstaande voor beeld wordt de naam van het video bestand verzonden naar het onderdeel invoer van Media bestand en de eigenschap primarySourceFile. De naam van het logo bestand wordt verzonden naar een andere invoer van het Media bestand die is verbonden met het grafische bedekkings onderdeel.

> [!NOTE]
> De naam van het video bestand wordt verzonden naar de eigenschap primarySourceFile. De reden hiervoor is het gebruik van deze eigenschap in de werk stroom voor het bouwen van de juiste naam voor het uitvoer bestand met behulp van expressies, bijvoorbeeld.

### <a name="step-by-step-workflow-creation"></a>Stapsgewijze werk stroom maken
Hier volgen de stappen voor het maken van een werk stroom die twee bestanden als invoer accepteert: een video en een afbeelding. De afbeelding wordt boven op de video bedekt.

Open **Workflow Designer** en selecteer **bestand**  >  **nieuwe werk ruimte**trans code trans  >  **blauw**.

In de nieuwe werk stroom worden drie elementen weer gegeven:

* Primair bron bestand
* Lijst met XML-fragmenten
* Uitvoer bestand/-activum  

![Nieuwe encoding-werk stroom](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nieuwe encoding-werk stroom*

Begin met het toevoegen van een invoer onderdeel voor media bestanden om het invoer Media-bestand te accepteren. Als u een component wilt toevoegen aan de werk stroom, zoekt u deze in het zoekvak van de opslag plaats en sleept u de gewenste vermelding naar het deel venster ontwerp.

Voeg vervolgens het video bestand toe dat moet worden gebruikt voor het ontwerpen van uw werk stroom. Als u dit wilt doen, klikt u op het achtergrond deel venster in Workflow Designer en zoekt u naar de eigenschap primair bron bestand in het rechterdeel venster van de rechter kant. Klik op het mappictogram en selecteer het juiste video bestand.

![Primaire bestands bron](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primaire bestands bron*

Geef vervolgens het video bestand op in het onderdeel voor het invoeren van media bestanden.   

![Invoer bron van Media bestand](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Invoer bron van Media bestand*

Zodra dit is gebeurd, wordt het bestand door het onderdeel invoer van media bestanden gecontroleerd en worden de uitvoer pincodes gevuld met het bestand dat het heeft geinspectet.

De volgende stap is het toevoegen van een ' video-gegevens type bijwerken ' om de kleur ruimte op te geven voor rec. 709. Voeg een ' video-indelings conversieprogramma ' toe die is ingesteld op gegevens indeling/indelings type = Configureer bare vlakke richting. Hiermee wordt de video stroom geconverteerd naar een indeling die kan worden gemaakt als bron van het bedekkings onderdeel.

![Updater en indelings programma voor video gegevens typen](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Updater en indelings programma voor video gegevens typen*

![Indelings type = Configureer bare vlakke richting](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Het indelings type is Configureer bare vlakke richting*

Voeg vervolgens een video-overlay-onderdeel toe en koppel de video pincode (niet-gecomprimeerd) aan de video pincode (niet-gecomprimeerd) van de invoer van het Media bestand.

Voeg nog een invoer van het Media bestand toe (om het logo bestand te laden), klik op dit onderdeel en wijzig de naam in logo voor invoer van Media bestand. Selecteer een installatie kopie (bijvoorbeeld een PNG-bestand) in de bestands eigenschap. Verbind de pincode van de niet-gecomprimeerde afbeelding aan de pincode van de niet-gecomprimeerde afbeelding van de overlay.

![Bron van overlay-onderdeel en afbeeldings bestand](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Bron van overlay-onderdeel en afbeeldings bestand*

Schakel het selectie vakje hand matige invoer uit als u de positie van het logo in de video wilt wijzigen (bijvoorbeeld als u het wilt positioneren op 10 procent van de linkerbovenhoek van de video). U kunt dit doen omdat u een invoer van media bestanden gebruikt om het logo bestand te voorzien van het bedekkings onderdeel.

![Positie van overlay](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Positie van overlay*

Als u de video stroom wilt coderen naar H. 264, voegt u de componenten van de AVC-Video Encoder en de AAC-Encoder toe aan het ontwerp oppervlak. Verbind de pincodes.
Stel het AAC-coderings programma in en selecteer conversie/voor instelling audio-indeling: 2,0 (L, R).

![Audio-en video encoders](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Audio-en video encoders*

Voeg nu de **ISO MPEG-4 multiplexer** en **Bestands uitvoer** onderdelen toe en verbind de pincodes zoals weer gegeven.

![MP4 multiplexer en bestands uitvoer](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer en bestands uitvoer*

U moet de naam van het uitvoer bestand instellen. Klik op het onderdeel **Bestands uitvoer** en bewerk de expressie voor het bestand:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4`

![Bestands uitvoer naam](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Bestands uitvoer naam*

U kunt de werk stroom lokaal uitvoeren om te controleren of deze correct wordt uitgevoerd.

Nadat deze is voltooid, kunt u deze uitvoeren in Azure Media Services.

Maak eerst een asset in Azure Media Services met twee bestanden: het video bestand en het logo. U kunt dit doen met behulp van .NET of REST API. U kunt dit ook doen met behulp van de Azure Portal of [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Deze zelf studie laat zien hoe u assets beheert met AMSE. Er zijn twee manieren om bestanden toe te voegen aan een Asset:

* Maak een lokale map, kopieer de twee bestanden erin en sleep de map naar het tabblad **assets** .
* Upload het video bestand als een Asset, geef de Asset-informatie weer, ga naar het tabblad bestanden en upload een extra bestand (logo).

> [!NOTE]
> Zorg ervoor dat u een primair bestand instelt in de asset (het belangrijkste video bestand).

![Asset-bestanden in AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Asset-bestanden in AMSE*

Selecteer de Asset en kies deze code ring met Premium encoder. Upload de werk stroom en selecteer deze.

Klik op de knop om gegevens door te geven aan de processor en voeg de volgende XML toe om de runtime-eigenschappen in te stellen:

![Premium encoder in AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium encoder in AMSE*

Plak vervolgens de volgende XML-gegevens. U moet de naam van het video bestand opgeven voor de invoer van het Media bestand en de primarySourceFile. Geef ook de naam van de bestands naam voor het logo op.

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

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Als u de .NET SDK gebruikt om de taak te maken en uit te voeren, moeten deze XML-gegevens worden door gegeven als de configuratie teken reeks.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Nadat de taak is voltooid, wordt in het bestand MP4 in het uitvoer element de overlay weer gegeven.

![Overlay op de video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Overlay op de video*

U kunt de voorbeeld werk stroom downloaden vanuit [github](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Voor beeld 2: meerdere audio taal codering

Er is een voor beeld van een workflow voor het coderen van meerdere audio talen beschikbaar in [github](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Deze map bevat een voor beeld van een werk stroom die kan worden gebruikt om een MXF-bestand te coderen in een multi MP4 bestanden-Asset met meerdere audio sporen.

Deze werk stroom gaat ervan uit dat het MXF-bestand één audio track bevat. de extra audio tracks moeten worden door gegeven als afzonderlijke audio bestanden (WAV of MP4...).

Voer de volgende stappen uit om te coderen:

* Maak een Media Services-Asset met het MXF-bestand en de audio bestanden (0 tot 18 audio bestanden).
* Controleer of het MXF-bestand is ingesteld als een primair bestand.
* Maak een taak en een taak met behulp van de Premium workflow encoder-processor. Gebruik de meegeleverde werk stroom (MultiMP4-1080p-19audio-v1. workflow).
* Geef de setruntime.xml gegevens door aan de taak (als u Azure Media Services Explorer gebruikt, gebruikt u de knop XML-gegevens door geven aan de werk stroom).
  * Werk de XML-gegevens bij om de juiste bestands namen en talen Tags op te geven.
  * De werk stroom bevat audio-onderdelen met de naam audio 1 op audio 18.
  * RFC5646 wordt ondersteund voor de taal code.

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

* De gecodeerde Asset bevat audio tracks met meerdere talen en deze sporen moeten worden geselecteerd in Azure Media Player.

## <a name="see-also"></a>Zie ook
* [Introductie van Premium-code ring in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Premium-code ring gebruiken in Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Inhoud op aanvraag coderen met Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow indelingen en codecs](media-services-premium-workflow-encoder-formats.md)
* [Voor beeld van werk stroom bestanden](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer-hulpprogramma](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
