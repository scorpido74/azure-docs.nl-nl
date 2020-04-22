---
title: Azure Media Player-opties
description: De insluitcode van Azure Media Player is gewoon een HTML5-videotag, dus voor veel van de opties u de standaardtagkenmerken gebruiken om de opties in te stellen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727162"
---
# <a name="options"></a>Opties #

## <a name="setting-options"></a>Instellingsopties ##

De insluitcode van Azure Media Player is gewoon een HTML5-videotag, dus voor veel van de opties u de standaardtagkenmerken gebruiken om de opties in te stellen.

`<video controls autoplay ...>`

U ook het kenmerk gegevensinstellingen gebruiken om opties in de [JSON-indeling](http://json.org/example.html) te bieden. Dit is ook hoe u opties zou instellen die niet standaard zijn voor de videotag.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Tot slot u, als u het kenmerk voor het instellen van gegevens niet gebruikt om de installatie van de speler te activeren, een object met de spelersopties doorgeven als het tweede argument in de JavaScript-installatiefunctie.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Opties in de constructor worden alleen ingesteld op de eerste initialisatie voordat de bron is ingesteld.  Als u de opties wilt wijzigen op hetzelfde geïnitialiseerde Azure Media Player-element, moet u de opties bijwerken voordat u de bron wijzigt. U de opties in `myPlayer.options({/*updated options*/});`JavaScript bijwerken met behulp van. Houd er rekening mee dat alleen gewijzigde opties worden beïnvloed, alle andere eerder ingestelde opties blijven bestaan.

## <a name="individual-options"></a>Individuele opties ##

> [!NOTE]
>Videotagkenmerken kunnen alleen waar of onwaar zijn (booleaans), u neemt gewoon het kenmerk (geen gelijkteken) toe om het in te schakelen of uit te sluiten om het uit te schakelen. Bijvoorbeeld, om besturingselementen `<video controls="true" ...>` in `<video controls ...>` te schakelen: VERKEERD RECHTS Het grootste probleem waar mensen tegenaan lopen, is proberen deze waarden op false in te stellen als de waarde (bijvoorbeeld controls="false") die eigenlijk het tegenovergestelde doet en de waarde instelt op true omdat het attribuut nog steeds is opgenomen.

### <a name="controls"></a>besturingselementen ###

De besturingselementenoptie bepaalt of de speler besturingselementen heeft waarmee de gebruiker kan communiceren. Zonder besturingselementen is de enige manier om het afspelen van de video te starten met het kenmerk autoplay of via de API.

`<video controls ...>` of `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Als automatisch afspelen waar is, wordt de video afgespeeld zodra de pagina is geladen (zonder enige interactie van de gebruiker).

> [!NOTE]
> Deze optie wordt niet ondersteund door mobiele apparaten zoals Windows Phone, Apple iOS en Android. Mobiele apparaten blokkeren de autoplay-functionaliteit om te voorkomen dat de maandelijkse dataplannen van de consument worden gebruikt (vaak duur). Een gebruiker touch / klik is vereist om de video te starten in dit geval.

`<video autoplay ...>`Of`{ "autoplay": true }`

### <a name="poster"></a>Poster ###
Het posterkenmerk stelt de afbeelding in die wordt weergegeven voordat de video wordt afgespeeld. Dit is vaak een frame van de video of een aangepast titelscherm. Zodra de gebruiker klikt op spelen van de afbeelding zal verdwijnen.

`<video poster="myPoster.jpg" ...>` of `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>breedte ###

Met het kenmerk Breedte wordt de weergavebreedte van de video ingesteld.

`<video width="640" ...>` of `{ "width": 640 }`

### <a name="height"></a>hoogte ###

Met het kenmerk Hoogte stelt u de weergavehoogte van de video in.

`<video height="480" ...>` of `{ "height": 480 }`

### <a name="plugins"></a>Plugins ###

Met de plug-ins json bepaalt u welke plug-ins worden geladen met dat exemplaar van AMP, zodat u alle opties configureren die plug-in kan hebben.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Zie [het schrijven van plug-ins](azure-media-player-writing-plugins.md) voor meer informatie over de ontwikkeling en het gebruik van plug-ins

### <a name="other-options"></a>andere opties ###

Andere opties kunnen op `<video>` de tag `data-setup` worden ingesteld met behulp van de parameter die een JSON neemt.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Dit is expliciet ingesteld op vals. Door false in te stellen, kan de Azure Media Player-skin op verschillende platforms hetzelfde worden weergegeven.  Bovendien zal, in tegenstelling tot de naam, touch nog steeds worden ingeschakeld.

### <a name="fluid"></a>Vloeistof ###

Door deze optie in te stellen op het echte video-element, wordt de volledige breedte van de bovenliggende container ingenomen en wordt de hoogte aangepast aan een video met een standaard beeldverhouding van 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid`de optie `width` expliciet `height` en instellingen overschrijft. Deze optie is alleen beschikbaar `2.0.0` in de Azure Media Player-versie en hoger.

### <a name="playbackspeed"></a>afspeelsnelheid ###

`playbackSpeed`optie regelt het afspelenSnelheid controle en set van afspeelsnelheid instellingen beschikbaar voor de gebruiker. `playbackSpeed`neemt een object. Om de besturingselement voor afspeelsnelheid `enabled` op de bedieningsbalk mogelijk te maken, moet de eigenschap van het object op true worden ingesteld. Een voorbeeld van het inschakelen van afspeelsnelheid in de markering:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Andere eigenschappen `playbackSpeed` van de instelling worden gegeven door het object [PlaybackSpeedOptions.](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)

Voorbeeld van het instellen van opties voor afspeelsnelheid in JavaScript:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Deze optie is alleen beschikbaar in Azure Media Player-versie 2.0.0 en hoger.

### <a name="staledatatimelimitinsec"></a>verouderdeDataTimeLimitInSec ###

De `staleDataTimeLimitInSec` optie is een optimalisatie waarmee u configureren hoeveel seconden aan verouderde gegevens u wilt bewaren in de mediaSource-buffers. Deze optie is standaard uitgeschakeld.

### <a name="cea708captionssettings"></a>cea708Instellingen voor bijschriften ###

Als u de true-instellingen inschakelt, u live CEA-ondertiteling weergeven in uw livestreams en live-archieven. Het labelattribuut is niet vereist, als niet inbegrepen is, valt de speler terug naar een standaardlabel.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Deze optie is alleen beschikbaar in Azure Media Player-versie 2.1.1 en hoger.

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)