---
title: Snel start azure mediaspeler
description: Meer informatie over de basisstappen voor het instellen van de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726464"
---
# <a name="azure-media-player-quickstart"></a>Snel start van Azure Media Player
Azure Media Player is eenvoudig in te stellen. Het duurt slechts een paar minuten om basisweergave van media-inhoud van uw Azure Media Services-account te krijgen. In deze sectie worden de basisstappen beschreven zonder op de details in te gaan. De volgende secties geven u details over het instellen en configureren van Azure Media Player.  Voeg de volgende bijlagen toe aan de `<head>` van uw document:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> U **NOT** moet de `latest` versie NIET in productie gebruiken, omdat dit op aanvraag kan worden gewijzigd. Vervangen `latest` door een versie van Azure Media Player; bijvoorbeeld vervangen `latest` `1.0.0`door . Azure Media Player-versies kunnen vanaf [hier](azure-media-player-changelog.md)worden opgevraagd.

## <a name="use-the-video-element"></a>Het video-element gebruiken

Vervolgens gewoon gebruik `<video>` maken van het element zoals `data-setup` je normaal zou doen, maar met een extra attribuut met alle opties. Deze opties kunnen een Azure Media Services-optie in een geldig JSON-object bevatten.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Als u geen automatische installatie wilt gebruiken, `data-setup` u het kenmerk weglaten en een video-element handmatig initialiseren.

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

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)