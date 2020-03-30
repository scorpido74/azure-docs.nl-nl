---
title: Een voorinstelling voor inhoudsbewuste codering - Azure Media Services
description: In dit artikel wordt inhoudsbewuste codering besproken in Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772103"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Gebruik de voorinstelling voor inhoudsbewuste codering om de optimale bitratewaarde voor een bepaalde resolutie te vinden

Om content voor te bereiden op levering door [adaptieve bitrate streaming,](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)moet video worden gecodeerd met meerdere bitrates (hoog tot laag). Dit zorgt voor sierlijke degradatie van kwaliteit, als de bitrate wordt verlaagd zo is de resolutie van de video. Dergelijke meervoudige bit-rate codering maakt gebruik van een zogenaamde codering ladder - een tabel van resoluties en bitrates, zie de Media Services [ingebouwde codering presets](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

U moet zich bewust zijn van de inhoud die u verwerkt, en aanpassen / afstemmen van de codering ladder om de complexiteit van de individuele video. Bij elke resolutie is er een bitrate waarboven elke verhoging van de kwaliteit niet scherpzinnig is – de encoder werkt op deze optimale bitrate waarde. Het volgende niveau van optimalisatie is het selecteren van de resoluties op basis van de inhoud - bijvoorbeeld, een video van een PowerPoint-presentatie profiteert niet van het gaan onder 720p. Verder gaan, kan de encoder worden belast met het optimaliseren van de instellingen voor elke opname in de video. 

Microsoft's [Adaptive Streaming](autogen-bitrate-ladder.md) preset pakt gedeeltelijk het probleem van de variabiliteit in de kwaliteit en resolutie van de bronvideo's. Onze klanten hebben een wisselende mix van inhoud, sommige op 1080p, anderen op 720p, en een paar op SD en lagere resoluties. Bovendien is niet alle broninhoud hoogwaardige mezzanines van film- of tv-studio's. De preset Adaptive Streaming pakt deze problemen aan door ervoor te zorgen dat de bitrate-ladder nooit hoger is dan de resolutie of de gemiddelde bitrate van de invoermezzanine. Deze voorinstelling onderzoekt echter geen andere broneigenschappen dan resolutie en bitrate.

## <a name="the-content-aware-encoding"></a>De inhoudsbewuste codering 

De inhoud-bewuste codering preset breidt de "adaptieve bitrate streaming" mechanisme, door het opnemen van aangepaste logica waarmee de encoder zoeken naar de optimale bitrate waarde voor een bepaalde resolutie, maar zonder uitgebreide computationele analyse. Deze voorinstelling produceert een set mp4's met GOP-uitgelijnd. Gezien alle invoerinhoud voert de service een eerste lichtgewicht analyse van de invoerinhoud uit en gebruikt de resultaten om het optimale aantal lagen, de juiste bitrate- en resolutie-instellingen voor levering door adaptieve streaming te bepalen. Deze preset is bijzonder effectief voor video's met lage en gemiddelde complexiteit, waarbij de uitvoerbestanden lager zijn dan de Adaptive Streaming-voorinstelling, maar met een kwaliteit die kijkers nog steeds een goede ervaring biedt. De uitvoer bevat MP4-bestanden met video en audio interleaved

De volgende voorbeeldgrafieken tonen de vergelijking met behulp van kwaliteitsstatistieken zoals [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) en [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). De bron werd gemaakt door het concatenating korte clips van hoge complexiteit shots van films en tv-programma's, bedoeld om de encoder te benadrukken. Per definitie produceert deze voorinstelling resultaten die variëren van inhoud tot inhoud - het betekent ook dat er voor sommige inhoud mogelijk geen significante vermindering van bitrate of verbetering van de kwaliteit is.

![Curve (RD) van tariefvervorming met PSNR](media/content-aware-encoding/msrv1.png)

**Figuur 1: Rd-curve (Rate distortion) met PSNR-statistiek voor bron met hoge complexiteit**

![Rd-curve (Rate-distortion) met VMAF](media/content-aware-encoding/msrv2.png)

**Figuur 2: Rd-curve (Rate distortion) met VMAF-statistiek voor bron met hoge complexiteit**

Hieronder staan de resultaten voor een andere categorie broninhoud, waarbij de encoder kon bepalen dat de invoer van slechte kwaliteit was (veel compressieartefacten vanwege de lage bitrate). Merk op dat met de inhoud-aware preset, de encoder besloten om slechts een uitvoerlaag te produceren - op een laag genoeg bitrate, zodat de meeste klanten in staat zou zijn om de stroom af te spelen zonder te vertragen.

![RD-curve met PSNR](media/content-aware-encoding/msrv3.png)

**Figuur 3: RD-curve met PSNR voor invoer van lage kwaliteit (op 1080p)**

![RD-curve met VMAF](media/content-aware-encoding/msrv4.png)

**Figuur 4: RD-curve met VMAF voor invoer van lage kwaliteit (op 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>De voorinstelling voor inhoudsbewuste codering gebruiken 

U transformaties maken die deze voorinstelling als volgt gebruiken. 

> [!TIP]
> Zie de sectie [Volgende stappen](#next-steps) voor zelfstudies die transvormuitgangen gebruiken. De uitvoerasset kan worden geleverd vanuit streamingeindpunten van Media Services in protocollen zoals MPEG-DASH en HLS (zoals weergegeven in de zelfstudies).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Coderingstaken met `ContentAwareEncoding` behulp van de voorinstelling worden gefactureerd op basis van de uitvoerminuten. 

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Video's uploaden, coderen en streamen met Media Services v3](stream-files-tutorial-with-api.md)
* [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - REST](stream-files-tutorial-with-rest.md)
* [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - CLI](stream-files-cli-quickstart.md)
* [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - .NET](stream-files-dotnet-quickstart.md)
* [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen - Node.js](stream-files-nodejs-quickstart.md)
