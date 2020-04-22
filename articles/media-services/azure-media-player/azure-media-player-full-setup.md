---
title: Volledige installatie van Azure Media Player
description: Meer informatie over het instellen van de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727240"
---
# <a name="azure-media-player-full-setup"></a>Volledige installatie van Azure Media Player #

Azure Media Player is eenvoudig in te stellen. Het duurt slechts enkele ogenblikken om basisweergave van media-inhoud direct vanuit uw Azure Media Services-account te krijgen. [Monsters](https://github.com/Azure-Samples/azure-media-player-samples) zijn ook voorzien in de monsters directory van de release.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Stap 1: De JavaScript- en CSS-bestanden opnemen in het hoofd van uw pagina ##

Met Azure Media Player hebt u toegang tot de scripts van de door CDN gehoste versie. Het wordt nu vaak aanbevolen om JavaScript voor `<body>` de `<head>`end body tag te plaatsen in plaats van de , maar Azure Media Player bevat een 'HTML5 Shiv', die in het hoofd moet zitten voor oudere IE-versies om de videotag als geldig element te respecteren.

> [!NOTE]
> Als u al een HTML5-shiv zoals [Modernizr](http://modernizr.com/) gebruikt, u de Azure Media Player JavaScript overal opnemen. Zorg er echter voor dat uw versie van Modernizr de shiv voor video bevat.

### <a name="cdn-version"></a>CDN-versie ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> U **NOT** moet de `latest` versie NIET in productie gebruiken, omdat dit op aanvraag kan worden gewijzigd. Vervang `latest` door een versie van Azure Media Player. Vervang bijvoorbeeld `latest` door `2.1.1`. Azure Media Player-versies kunnen vanaf [hier](azure-media-player-changelog.md)worden opgevraagd.

> [!NOTE]
> Sinds `1.2.0` de release is het niet langer nodig om de locatie op te nemen in de fallback-techs (het zal automatisch de locatie ophalen van het relatieve pad van het azuremediaplayer.min.js-bestand). U de locatie van de fallback-technici wijzigen `<head>` door het volgende script toe te voegen in de na de bovenstaande scripts.

> [!NOTE]
> Vanwege de aard van Flash- en Silverlight-plug-ins moeten de swf- en xap-bestanden op een domein worden gehost zonder gevoelige informatie of gegevens - dit wordt automatisch voor u geregeld met de Azure CDN-gehoste versie.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Stap 2: Een HTML5-videotag toevoegen aan uw pagina ##

Met Azure Media Player u een HTML5-videotag gebruiken om een video in te sluiten. Azure Media Player leest de tag vervolgens en laat deze werken in alle browsers, niet alleen in browsers die HTML5-video ondersteunen. Naast de basismarkering heeft Azure Media Player een paar extra stukken nodig.

1. Het `<data-setup>` kenmerk `<video>` op de pagina vertelt Azure Media Player om de video automatisch in te stellen wanneer de pagina klaar is en een (in JSON-indeling) van het kenmerk te lezen.
1. Het `id` attribuut: Moet worden gebruikt en uniek voor elke video op dezelfde pagina.
1. Het `class` kenmerk bevat twee klassen:
    - `azuremediaplayer`past stijlen toe die vereist zijn voor de gebruikersinterfacefunctionaliteit van Azure Media Player
    - `amp-default-skin`past de standaardskin toe op de HTML5-besturingselementen
1. Het `<source>` bevat twee vereiste kenmerken
    - `src`kenmerk kan een **.ism/manifestbestand* van Azure Media Services bevatten, Azure Media Player voegt automatisch de URL's voor DASH, SMOOTH en HLS toe aan de speler
    - `type`kenmerk is het vereiste MIME-type van de stream. Het MIME-type dat is gekoppeld aan *".ism/manifest"* is *"application/vnd.ms-sstr+xml"*
1. Het *optionele* `<data-setup>` kenmerk `<source>` op de vertelt Azure Media Player als er een uniek leveringsbeleid voor de stream van Azure Media Services is, inclusief, maar niet beperkt tot, versleutelingstype (AES of PlayReady, Widevine of FairPlay) en token.

Kenmerken, instellingen, bronnen en tracks opnemen/uitsluiten, precies zoals u dat voor HTML5-video zou doen.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Standaard bevindt de grote afspeelknop zich in de linkerbovenhoek, zodat deze de interessante delen van de poster niet bedekt. Als u liever de grote afspeelknop wilt centreren, u een extra toevoegen `amp-big-play-centered` `class` aan uw `<video>` element.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternatieve installatie voor dynamisch geladen HTML ###

Als uw webpagina of toepassing de videotag dynamisch laadt (ajax, appendChild, enz.), zodat deze mogelijk niet bestaat wanneer de pagina wordt geladen, wilt u de speler handmatig instellen in plaats van te vertrouwen op het kenmerk van het gegevensinstellen. Verwijder hiervoor eerst het kenmerk van het instellen van gegevens uit de tag, zodat er geen verwarring is wanneer de speler wordt geïnitialiseerd. Voer vervolgens de volgende JavaScript uit enige tijd nadat de Azure Media Player JavaScript is geladen en nadat de videotag in de DOM is geladen.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
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
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Het eerste argument `amp` in de functie is de id van uw videotag. Vervang het door je eigen.

Het tweede argument is een optieobject. Hiermee u extra opties instellen zoals u met het kenmerk gegevensinstellingen.

Het derde argument is een 'ready' callback. Zodra Azure Media Player is geïnitialiseerd, wordt deze functie aangeroepen. In de ready callback verwijst 'dit' object naar de spelerinstance.

In plaats van een element-ID te gebruiken, u ook een verwijzing naar het element zelf doorgeven.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)