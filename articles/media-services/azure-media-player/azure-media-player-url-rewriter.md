---
title: Azure Media Player URL ReWriter
description: Azure Media Player herschrijft een gegeven URL van Azure Media Services om stromen te bieden voor SMOOTH, DASH, HLS v3 en HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726457"
---
# <a name="url-rewriter"></a>URL-herschrijver #

Azure Media Player herschrijft een gegeven URL standaard van Azure Media Services om stromen te bieden voor SMOOTH, DASH, HLS v3 en HLS v4. Als de bron bijvoorbeeld als volgt wordt gegeven, zorgt Azure Media Player ervoor dat deze alle bovenstaande protocollen probeert af te spelen:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Als u echter geen gebruik wilt maken van de URL ReWriter, kunt u dit doen door de `disableUrlRewriter` eigenschap toe te voegen aan de para meter. Dit betekent dat alle informatie die wordt door gegeven aan de bronnen rechtstreeks zonder aanpassing aan de speler wordt door gegeven.  Hier volgt een voor beeld van het toevoegen van twee bronnen aan de speler, op streepje en één SMOOTH streaming.

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

U kunt ook de specifieke streaming-indelingen opgeven die u wilt Azure Media Player herschrijven naar met behulp van de `streamingFormats` para meter. Opties zijn `DASH`onder `SMOOTH`andere `HLSv3`, `HLSv4`, `HLS`,,. Het verschil tussen HLS en HLSv3 & v4 is dat de HLS-indeling het afspelen van FairPlay-inhoud ondersteunt. v3 en v4 bieden geen ondersteuning voor FairPlay. Dit is handig als u geen leverings beleid hebt voor een bepaald protocol dat beschikbaar is.  Hier volgt een voor beeld van wanneer een streepje-protocol niet is ingeschakeld met uw asset.

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

De bovenstaande twee kunnen worden gebruikt in combi natie met elkaar voor meerdere omstandigheden op basis van uw specifieke activa.

> [!NOTE]
> Widevine-beschermings informatie blijft alleen behouden in het DASH-protocol.

## <a name="next-steps"></a>Volgende stappen ##

- [Snelstartgids Azure Media Player](azure-media-player-quickstart.md)