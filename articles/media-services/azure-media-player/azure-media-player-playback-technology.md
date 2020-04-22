---
title: Azure Media Player-afspeeltechnologie
description: Meer informatie over de afspeeltechnologie die wordt gebruikt om de video of audio af te spelen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 85eaa04836774b838da67e073017f4af3d2fe179
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726485"
---
# <a name="playback-technology-tech"></a>Afspeeltechnologie ("tech") #

Playback Technology verwijst naar de specifieke browser- of plug-intechnologie die wordt gebruikt om de video of audio af te spelen.

- **azureHtml5JS**: maakt gebruik van MSE- en EME-standaarden in combinatie met het video-element voor het op plug-in-gebaseerdafspelen van DASH-inhoud met ondersteuning voor gecodeerde inhoud met AES-128-bits envelop of DRM-gemeenschappelijke versleutelde inhoud (via PlayReady en Widevine wanneer de browser dit ondersteunt) van Azure Media Services
- **flashSS**: maakt gebruik van flashplayer-technologie om soepele inhoud af te spelen met ondersteuning voor AES-128-bits envelopdecryptie van Azure Media Services - vereist Flash-versie van 11,4 of hoger
- **html5FairPlayHLS**: maakt gebruik van Safari-specifieke in browser-gebaseerde afspeeltechnologie via HLS met het video-element. Deze technologie is vereist om fairplay-beveiligde inhoud van Azure Media Services af te spelen en is vanaf 10/19/16 aan de techOrder toegevoegd
- **silverlightSS:** maakt gebruik van silverlight-technologie om vloeiende inhoud af te spelen met ondersteuning voor playReady-beveiligde inhoud van Azure Media Services.
- **html5**: maakt gebruik van browsergebaseerde afspeeltechnologie met het video-element.  Wanneer op een Apple iOS- of Android-apparaat, maakt deze technologie het afspelen van HLS-streams mogelijk met een basisondersteuning voor AES-128-bits envelopversleuteling of DRM-inhoud (via FairPlay wanneer de browser het ondersteunt).

## <a name="tech-order"></a>Technische bestelling ##

Om ervoor te zorgen dat uw asset speelbaar is op een breed scala van apparaten, `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` de volgende tech `<video>` order wordt aanbevolen en is de standaard als: en kan direct worden ingesteld op de of programmatisch in de opties:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

of

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Compatibiliteitsmatrix ##

Gezien de aanbevolen technische bestelling met streaming-inhoud van Azure Media Services, wordt de volgende compatibiliteitsweergave verwacht

| Browser        | OS                                                       | Verwachte Tech (Clear)  | Verwachte Tech (AES)  | Verwachte Tech (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| Edgeie 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| Edge           | Xbox One<sup>1</sup> (update van november 2015)                   | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5 (geen token)3    | niet ondersteund                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5fairplayHLS (fairplay)  |
| Safari 6       | OS X Bergleeuw<sup>1</sup>                           | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (geen token)<sup>3</sup>    | niet ondersteund                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| IE 8           | Windows                                                  | niet ondersteund          | niet ondersteund        | niet ondersteund                |

<sup>1</sup> Configuratie niet ondersteund of getest; vermeld als referentie voor voltooiing.

<sup>2</sup> Succesvol afspelen op Android-apparaten vereist een combinatie van apparaatmogelijkheden, grafische ondersteuning, codec-rendering, os-ondersteuning en meer. Aangezien Android is een open-source platform waarmee telefoon fabrikanten om de Vanilla Android OS geleverd door Google te veranderen, dit veroorzaken enige fragmentatie in de Android-ruimte, en sommige apparaten kunnen niet worden ondersteund vanwege gebrek aan functies. Ook hebben sommige Android-apparaten geen ondersteuning voor alle codecs.  

<sup>3</sup> In de gevallen waarin er geen ondersteuning voor token is, kan een proxy worden gebruikt om deze functionaliteit toe te voegen. Bekijk deze [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) voor meer informatie over deze oplossing.

> [!NOTE]
> Als de verwachte tech gekozen vereist een plugin worden geïnstalleerd, zoals Flash, en dat is niet geïnstalleerd op de machine van de gebruiker, AMP zal doorgaan met het controleren van de mogelijkheden van de volgende tech, in combinatie met brontypen en bescherming info, in de tech lijst. Als bijvoorbeeld een onbeveiligde on-demand stream in Safari 8 op OS X Yosemite wordt weergegeven en zowel Flash als Silverlight niet zijn geïnstalleerd, selecteert AMP de native Html5-technologie voor afspelen.<br/><br/>Nieuwe browsertechnologieën komen dagelijks in opkomst en kunnen deze matrix als zodanig beïnvloeden.

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)