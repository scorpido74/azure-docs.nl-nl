---
title: Plug-ins schrijven voor Azure Media Player
description: Meer informatie over het schrijven van een plug-in met Azure Media Player met JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727110"
---
# <a name="writing-plugins-for-azure-media-player"></a>Plug-ins schrijven voor Azure Media Player #

Een plug-in is JavaScript geschreven om de speler uit te breiden of te verbeteren. U plug-ins schrijven die het uiterlijk, de functionaliteit van Azure Media Player of zelfs de interface met andere services wijzigen. U dit doen in twee eenvoudige stappen:

## <a name="step-1"></a>Stap 1 ##

Schrijf uw JavaScript in een functie als volgt:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

U uw code rechtstreeks op `<script>` uw HTML-pagina schrijven in tags of in een extern JavaScript-bestand. Als u dit laatste doet, moet u het `<head>` JavaScript-bestand *na* het AMP-script opnemen in het javascript van uw HTML-pagina.

Voorbeeld:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Stap 2 ##
Initialiseer de plug-in met JavaScript op twee manieren:

Methode 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Methode 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Plugin-opties zijn niet vereist, met inbegrip van hen alleen kunnen de ontwikkelaars met behulp van uw plugin om het gedrag ervan te configureren zonder de broncode te wijzigen.

Voor inspiratie en meer voorbeelden over het maken van een plugin neem een kijkje in onze [galerij](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Plugin code dynamisch verandert items in de DOM tijdens de levensduur van de speler van de kijker ervaring, het maakt nooit permanente wijzigingen in de broncode van de speler. Dit is waar een goed begrip van de ontwikkelaarstools van uw browser van pas komt. Als u bijvoorbeeld het uiterlijk van een element in de speler wilt wijzigen, u het HTML-element vinden op basis van de klassenaam en vervolgens van daaruit kenmerken toevoegen of wijzigen. Hier is een geweldige bron voor [het wijzigen van HTML-kenmerken.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Geïntegreerde plug-ins ###

 Er zijn momenteel twee plugins gebakken in AMP: de [time-tip](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) en [hotkeys](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Deze plugins werden oorspronkelijk ontwikkeld om modulaire plugins voor de speler te zijn maar zijn nu inbegrepen in de spelerbroncode.

### <a name="plugin-gallery"></a>Galerie voor voor plug-in ###

De [plugin galerij](http//:aka.ms/ampplugins) heeft verschillende plugins die de gemeenschap al heeft bijgedragen voor functies zoals tijdregelmarkeringen, zoom, analytics en meer. De pagina biedt toegang tot de plug-ins en instructies over hoe het in te stellen, evenals een demo die de plugin in actie toont. Als u een coole plugin die je denkt dat moet worden opgenomen in onze galerij, voel je vrij om het in te dienen, zodat we het kunnen controleren.

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)