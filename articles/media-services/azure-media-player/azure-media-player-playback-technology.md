---
title: Afspeel technologie Azure Media Player
description: Meer informatie over de afspeel technologie die wordt gebruikt om de video of audio af te spelen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 85eaa04836774b838da67e073017f4af3d2fe179
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726485"
---
# <a name="playback-technology-tech"></a>Afspeel technologie ("tech") #

Afspeel technologie verwijst naar de specifieke browser-of invoeg toepassingen technologie die wordt gebruikt om de video of audio af te spelen.

- **azureHtml5JS**: gebruikt MSE-en EME-standaarden in combi natie met het video-element voor het afspelen van inhoud op basis van een invoeg toepassing met ondersteuning voor AES-128 bits envelop versleutelde inhoud of DRM common encrypted content (via PlayReady en Widevine wanneer de browser deze ondersteunt) van Azure Media Services
- **knipperend**: Hiermee wordt gebruikgemaakt van Flash Player-technologie voor het afspelen van vloeiende inhoud met ondersteuning voor AES-128 bits-envelop codering van Azure Media Services-vereist een Flash-versie van 11,4 of hoger
- **html5FairPlayHLS**: u gebruikt Safari specifiek in op browser gebaseerde afspeel technologie via HLS met het video-element. Deze technische mede werker vereist het afspelen van FairPlay beveiligde inhoud van Azure Media Services en is toegevoegd aan de techOrder vanaf 10/19/16
- **Silverlight**: maakt gebruik van Silverlight-technologie om vloeiende inhoud af te spelen met ondersteuning voor met PlayReady beveiligde inhoud van Azure Media Services.
- **HTML5**: maakt gebruik van een op browser gebaseerde afspeel technologie met het video-element.  Wanneer u op een Apple iOS-of Android-apparaat, kunt u met deze technische HLS-streams afspelen met een basis ondersteuning voor AES-128 bits-envelop versleuteling of DRM-inhoud (via FairPlay wanneer de browser deze ondersteunt).

## <a name="tech-order"></a>Technische bestelling ##

Om ervoor te zorgen dat uw asset kan worden afgespeeld op een groot aantal verschillende apparaten, wordt de volgende technische volg orde aanbevolen en is de standaard `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` waarde als: en kan rechtstreeks worden `<video>` ingesteld op de of meerdere manieren in de opties:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

of

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Compatibiliteits matrix ##

Gezien de aanbevolen technische volg orde met het streamen van inhoud van Azure Media Services, wordt de volgende compatibiliteits matrix verwacht

| Browser        | OS                                                       | Verwachte technische (heldere)  | Verwachte technische (AES)  | Verwachte technische (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10, Windows 8,1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | knipperend                | knipperend              | Silverlight (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| Edge           | Xbox One<sup>1</sup> (update van nov 2015)                   | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| Chrome 37 +     | Windows 10, Windows 8,1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 +    | Windows 10, Windows 8,1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8,1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | Silverlight (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8,1                                  | knipperend                | knipperend              | Silverlight (PlayReady)    |
| Safari         | iOS 6+                                                   | HTML5                  | HTML5 (geen token) 3    | niet ondersteund                |
| Safari 8 +      | OS X Yosemite +                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X berg Lion<sup>1</sup>                           | knipperend                | knipperend              | Silverlight (PlayReady)    |
| Chrome 37 +     | Android 4.4.4 +<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 +     | Android 4,02                                             | HTML5                  | HTML5 (geen token)<sup>3</sup>    | niet ondersteund                |
| Firefox 42 +    | Android 5.0 +<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| IE 8           | Windows                                                  | niet ondersteund          | niet ondersteund        | niet ondersteund                |

<sup>1</sup> configuratie wordt niet ondersteund of getest. wordt weer gegeven als referentie voor volt ooien.

<sup>2</sup> als u het afspelen op Android-apparaten hebt voltooid, hebt u een combi natie van mogelijkheden voor apparaten, grafische ondersteuning, codec-rendering, ondersteuning voor besturings systemen en meer nodig. Omdat Android een open-source platform is waarmee telefoon fabrikanten het vanille Android-besturings systeem kunnen wijzigen dat door Google wordt geboden, leidt dit tot enige fragmentatie in de Android-ruimte en sommige apparaten worden mogelijk niet ondersteund vanwege een gebrek aan functies. Sommige Android-apparaten bieden ook geen ondersteuning voor alle codecs.  

<sup>3</sup> in de gevallen waarin het token niet wordt ondersteund, kan een proxy worden gebruikt om deze functionaliteit toe te voegen. Bekijk deze [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) voor meer informatie over deze oplossing.

> [!NOTE]
> Als de verwachte technische keuze vereist dat een invoeg toepassing wordt geïnstalleerd, zoals Flash, en die niet is geïnstalleerd op de computer van de gebruiker, blijft de mogelijkheden van de volgende technische, in combi natie met bron typen en beveiligings gegevens, in de technische lijst staan. Als er bijvoorbeeld een niet-beveiligde stroom op aanvraag wordt weer gegeven in Safari 8 op OS X Yosemite, en Flash en Silverlight niet zijn geïnstalleerd, selecteert AMP de systeem eigen Html5-Tech voor afspelen.<br/><br/>Nieuwe browser technologieën worden dagelijks opkomende en kunnen op deze matrix van invloed zijn.

## <a name="next-steps"></a>Volgende stappen ##

- [Snelstartgids Azure Media Player](azure-media-player-quickstart.md)