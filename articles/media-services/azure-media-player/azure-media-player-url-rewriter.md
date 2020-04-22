---
title: URL-rewriter van Azure Media Player
description: Azure Media Player herschrijft een bepaalde URL van Azure Media Services om streams te bieden voor SMOOTH, DASH, HLS v3 en HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726457"
---
# <a name="url-rewriter"></a>URL-rewriter #

Azure Media Player herschrijft standaard een bepaalde URL van Azure Media Services om streams te bieden voor SMOOTH, DASH, HLS v3 en HLS v4. Als de bron bijvoorbeeld als volgt wordt gegeven, zorgt Azure Media Player ervoor dat alle bovenstaande protocollen worden afgespeeld:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Als u de URL-rewriter echter niet wilt gebruiken, `disableUrlRewriter` kunt u dit doen door de eigenschap aan de parameter toe te voegen. Dit betekent dat alle informatie die wordt doorgegeven aan de bronnen direct worden doorgegeven aan de speler zonder wijziging.  Hier is een voorbeeld van het toevoegen van twee bronnen aan de speler, op DASH en een soepele streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

of

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Als u dat wilt, u ook de specifieke streaming-indelingen opgeven `streamingFormats` die u wilt dat Azure Media Player opnieuw schrijft naar het gebruik van de parameter. Opties `DASH`zijn `SMOOTH` `HLSv3`, `HLSv4` `HLS`, , , . Het verschil tussen HLS en HLSv3 & v4 is dat het HLS-formaat het afspelen van FairPlay-content ondersteunt. v3 en v4 ondersteunen FairPlay niet. Dit is handig als u geen leveringsbeleid hebt voor een bepaald protocol dat beschikbaar is.  Hier is een voorbeeld van wanneer een DASH-protocol niet is ingeschakeld met uw asset.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

of

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

De bovenstaande twee kunnen worden gebruikt in combinatie met elkaar voor meerdere omstandigheden op basis van uw specifieke actief.

> [!NOTE]
> Widevine-beveiligingsinformatie blijft alleen bestaan op het DASH-protocol.

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)