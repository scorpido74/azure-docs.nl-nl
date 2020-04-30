---
title: Snelstartgids Azure Media Player
description: Meer informatie over de basis stappen voor het instellen van de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726464"
---
# <a name="azure-media-player-quickstart"></a>Quickstart voor Azure Media Player
Azure Media Player eenvoudig in te stellen. Het duurt slechts enkele minuten om het eenvoudig afspelen van media-inhoud van uw Azure Media Services-account te verkrijgen. In deze sectie worden de basisstappen beschreven zonder op de details in te gaan. De volgende secties geven u specifieke informatie over het instellen en configureren van Azure Media Player.  Voeg de volgende bijlagen toe aan de `<head>` van uw document:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> U moet **NOT** de `latest` versie niet gebruiken in productie, omdat dit kan worden gewijzigd op aanvraag. Vervangen `latest` door een versie van Azure Media Player; Vervang bijvoorbeeld door `latest` `1.0.0`. Van Azure Media Player-versies kan [hier](azure-media-player-changelog.md)een query worden uitgevoerd.

## <a name="use-the-video-element"></a>Het video-element gebruiken

Gebruik vervolgens gewoon het `<video>` element zoals u dat normaal zou doen, maar met een `data-setup` extra kenmerk met opties. Deze opties kunnen een wille keurige Azure Media Services-optie bevatten in een geldig JSON-object.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Als u geen automatische installatie wilt gebruiken, kunt u het `data-setup` kenmerk weglaten en een video-element hand matig initialiseren.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Volgende stappen ##

- [Snelstartgids Azure Media Player](azure-media-player-quickstart.md)