---
title: Een experimentele voor instelling voor Content-Aware encoding-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u met inhoud compatibele code ring in Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9389466b6291542563c068706479bf981c5880da
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896145"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Experimentele voor instelling voor code ring met Content-Aware

Video moet worden gecodeerd met meerdere bitsnelheden (hoog naar laag) om inhoud voor te bereiden voor levering door [Adaptive bitrate streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). Om te zorgen voor een goede vermindering van de kwaliteit, naarmate de bitsnelheid lager is, is dit de resolutie van de video. Dit resulteert in een zogenaamde coderings ladder: een tabel met resoluties en bitsnelheden; Zie de Media Services [ingebouwde coderings definities](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Overzicht

Het is belang dat u verder gaat dan een vooraf ingestelde methode die geschikt is voor alle Video's nadat Netflix zijn [blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) in december 2015 heeft gepubliceerd. Sinds dat moment zijn er meerdere oplossingen voor content-bewuste code ring uitgebracht op Marketplace; Raadpleeg [dit artikel](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) voor een overzicht. Het is de bedoeling dat u rekening houdt met de inhoud: om de coderings ladder aan te passen of af te stemmen op de complexiteit van de afzonderlijke video. Bij elke oplossing is er sprake van een bitsnelheid waarboven een toename van de kwaliteit niet perceptive is. het coderings programma werkt op deze optimale bitrate waarde. Het volgende optimalisatie niveau is het selecteren van de oplossingen op basis van de inhoud. een video van een Power Point-presentatie biedt bijvoorbeeld geen voor deel uit van de volgende 720p. Het coderings programma kan ook worden getaakd om de instellingen voor elke foto in de video te optimaliseren. Netflix heeft [een dergelijke benadering](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) beschreven in 2018.

In vroege 2017 heeft micro soft de [adaptieve streaming](autogen-bitrate-ladder.md) -voor instelling uitgebracht om het probleem van de variabiliteit in de kwaliteit en resolutie van de bron Video's te verhelpen. Onze klanten hadden een gevarieerde combi natie van inhoud, een aantal van 1080p, anderen op 720p en enkele van de SD en lagere resoluties. Bovendien was niet alle bron inhoud van hoge kwaliteit mezzanine van film of TV Studios. Met de vooraf ingestelde adaptieve streaming worden deze problemen opgelost door ervoor te zorgen dat de videoladder nooit de resolutie of de gemiddelde bitsnelheid van de invoer-mezzanine overschrijdt.

De voor keuren voor de experimentele inhoud van de coderings functie breidt dat mechanisme uit door aangepaste logica te integreren waarmee de encoder de optimale bitrate waarde voor een bepaalde oplossing kan zoeken, maar zonder dat er uitgebreide reken kundige analyse nodig is. Het resultaat is dat deze nieuwe standaard instelling een uitvoer produceert die een lagere bitsnelheid heeft dan de vooraf ingestelde adaptieve streaming, maar met een hogere kwaliteit. Bekijk de volgende voorbeeld grafieken die de vergelijking weer geven met behulp van kwaliteits metingen, zoals [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) en [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). De bron is gemaakt door het samen voegen van korte clips met zeer complexe opnamen van films en TV-Program ma's, die zijn bedoeld om het coderings programma te stressren. Deze standaard instelling produceert per definitie resultaten die variëren van inhoud tot inhoud. het betekent ook dat er voor sommige inhoud geen aanzienlijke vermindering van de bitrate of verbetering van de bitsnelheid kan optreden.

![Distorsie (RD)-curve met behulp van PSNR](media/cae-experimental/msrv1.png)

**Afbeelding 1: een bocht-curve (distorsie) met behulp van PSNR-metrische gegevens voor een hoge complexiteits bron**

![Distorsie (RD)-curve met behulp van VMAF](media/cae-experimental/msrv2.png)

**Afbeelding 2: een bocht-curve (distorsie) met behulp van VMAF-metrische gegevens voor een hoge complexiteits bron**

De voor instelling is momenteel afgesteld voor hoge complexiteit, bron Video's van hoge kwaliteit (films, TV-Program ma's). Het werk wordt uitgevoerd om aan te passen aan inhoud met weinig complexiteit (bijvoorbeeld Power Point-presentaties), en Video's met een slechte kwaliteit. Deze vooraf ingestelde gebruikt ook dezelfde set resoluties als de vooraf ingestelde voor instelling van adaptief streamen. Micro soft werkt aan methoden om de minimale set resoluties te selecteren op basis van de inhoud. Dit zijn de resultaten voor een andere categorie bron inhoud, waarbij het coderings programma kan bepalen dat de invoer van slechte kwaliteit was (veel compressie artefacten vanwege de lage bitrate). Houd er rekening mee dat met de voor instelling voor experimenteren het coderings programma heeft besloten om slechts één uitvoer laag te maken: met een lage bitrate, zodat de meeste clients de stroom kunnen afspelen zonder te hoeven aflopen.

![RD-curve met PSNR](media/cae-experimental/msrv3.png)

**Afbeelding 3: RD-curve met PSNR voor invoer van lage kwaliteit (in 1080p)**

![RD-curve met VMAF](media/cae-experimental/msrv4.png)

**Afbeelding 4: RD-curve met VMAF voor invoer van lage kwaliteit (in 1080p)**

## <a name="use-the-experimental-preset"></a>De voor instelling experimenteel gebruiken

U kunt als volgt trans formaties maken die gebruikmaken van deze voor instelling. [Als u een zelf studie](stream-files-tutorial-with-api.md)wilt gebruiken, kunt u de code als volgt bijwerken:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> Het voor voegsel "experimenteel" wordt hier gebruikt om te Signa leren dat de onderliggende algoritmen nog steeds in ontwikkeling zijn. Er kunnen wijzigingen in de loop van de tijd worden aangebracht aan de logica die wordt gebruikt voor het genereren van bitsnelheid ladders, met het doel van het convergeren van een algoritme dat robuust is, en wordt aangepast aan een groot aantal invoer voorwaarden. Voor het coderen van taken die gebruikmaken van deze voor instelling worden nog steeds kosten in rekening gebracht op basis van uitvoer minuten en het uitvoer activum kan worden geleverd vanuit onze streaming-eind punten in protocollen zoals streepje en HLS.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over deze nieuwe optie voor het optimaliseren van uw Video's, nodigen we u uit om het uit te proberen. U kunt ons feedback sturen via de koppelingen aan het einde van dit artikel of ons meer rechtstreeks op <amsved@microsoft.com>.
