---
title: Afbeeldingssjablonen in de Azure Maps Web SDK | Microsoft Azure Maps
description: In dit artikel leert u hoe u afbeeldingssjablonen met HTML-markeringen en verschillende lagen in de Microsoft Azure Maps Web SDK gebruikt.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804806"
---
# <a name="how-to-use-image-templates"></a>Afbeeldingssjablonen gebruiken

Afbeeldingen kunnen worden gebruikt met HTML-markeringen en verschillende lagen binnen de Azure Maps-webSDK:

 - Symboollagen kunnen punten op de kaart weergeven met een afbeeldingspictogram. Symbolen kunnen ook worden weergegeven langs een lijnenpad.
 - Veelhoeklagen kunnen worden weergegeven met een afbeelding van het vulpatroon. 
 - HTML-markeringen kunnen punten renderen met afbeeldingen en andere HTML-elementen.

Als u goede prestaties met lagen wilt garanderen, laadt u de afbeeldingen in de mapafbeeldingsprite-bron voordat u deze rendert. De [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), van de SymbolLayer, preloads een paar marker beelden in een handvol kleuren in de kaart afbeelding sprite, standaard. Deze markeringsafbeeldingen en meer zijn beschikbaar als SVG-sjablonen. Ze kunnen worden gebruikt om afbeeldingen te maken met aangepaste schalen, of gebruikt als primaire en secundaire kleur van de klant. In totaal zijn er 42 afbeeldingssjablonen aanwezig: 27 symboolpictogrammen en 15 veelhoekige vulpatronen.

Afbeeldingssjablonen kunnen met behulp van de `map.imageSprite.createFromTemplate` functie aan de sprite-bronnen van de kaartafbeelding worden toegevoegd. Met deze functie kunnen maximaal vijf parameters worden doorgegeven;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Het `id` is een unieke id die u maakt. De `id` afbeelding wordt toegewezen aan de afbeelding wanneer deze wordt toegevoegd aan de sprite van de kaartafbeelding. Gebruik deze id in de lagen om op te geven welke afbeeldingsbron moet worden weergegeven. Hiermee `templateName` geeft u aan welke afbeeldingssjabloon u wilt gebruiken. Met `color` de optie wordt de primaire `secondaryColor` kleur van de afbeelding ingesteld en de opties stelt de secundaire kleur van de afbeelding in. De `scale` optie schaalt de afbeeldingssjabloon voordat u deze toepast op de afbeeldingssprite. Wanneer de afbeelding wordt toegepast op de afbeeldingsprite, wordt deze omgezet in een PNG. Om een scherpe weergave te garanderen, is het beter om de afbeeldingssjabloon op te schalen voordat u deze aan de sprite toevoegt, dan deze in een laag op te schalen.

Deze functie laadt de afbeelding asynchroon in de afbeeldingsprite. Zo geeft het een belofte terug dat u wachten tot deze functie is voltooid.

In de volgende code ziet u hoe u een afbeelding maakt van een van de ingebouwde sjablonen en deze gebruikt met een symboollaag.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Een afbeeldingssjabloon met een symboollaag gebruiken

Zodra een afbeeldingssjabloon in de sprite van de kaartafbeelding is geladen, kan deze worden weergegeven als `image` symbool `iconOptions`in een symboollaag door te verwijzen naar de afbeeldingsbron-id in de optie van de .

In het volgende voorbeeld wordt `marker-flat` een symboollaag weergegeven met de afbeeldingssjabloon met een tealprimaire kleur en een witte secundaire kleur. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symboollaag met ingebouwde pictogramsjabloon" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>laag Pensymbolen met ingebouwde pictogramsjabloon</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Een afbeeldingssjabloon gebruiken langs een lijnenpad

Zodra een afbeeldingssjabloon in de sprite van de kaartafbeelding is geladen, kan deze langs het pad van een `lineSpacing`lijn worden weergegeven door een linestring toe `image` te `iconOptions`voegen aan een gegevensbron en een symboollaag met een optie te gebruiken en door te verwijzen naar de ID van de afbeeldingsbron in de optie th . 

In het volgende voorbeeld wordt een roze lijn op `car` de kaart weergegeven en wordt een symboollaag gebruikt met behulp van de afbeeldingssjabloon met een dodgerblauwe primaire kleur en een witte secundaire kleur. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lijnlaag met ingebouwde pictogramsjabloon" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>laag Penlijn met ingebouwde pictogramsjabloon</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Als de afbeeldingssjabloon wijst, stelt u de `rotation` pictogramoptie van de symboollaag in op 90 als u wilt dat deze in dezelfde richting wijst als de lijn.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Een afbeeldingssjabloon met een veelhoeklaag gebruiken

Zodra een afbeeldingssjabloon in de sprite van de kaartafbeelding is geladen, kan deze worden weergegeven als `fillPattern` een vulpatroon in een veelhoeklaag door te verwijzen naar de afbeeldingsbron-id in de optie van de laag.

In het volgende voorbeeld wordt `dot` een veelhoeklaag weergegeven met behulp van de afbeeldingssjabloon met een rode primaire kleur en een transparante secundaire kleur.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Veelhoek vullen met ingebouwde pictogramsjabloon" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>veelhoek Voor het vullen van pen met ingebouwde pictogramsjabloon</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Als u de secundaire kleur van vulpatronen instelt, wordt het gemakkelijker om de onderliggende kaart te zien en wordt nog steeds het primaire patroon weergegeven. 

## <a name="use-an-image-template-with-an-html-marker"></a>Een afbeeldingssjabloon gebruiken met een HTML-markering

Een afbeeldingssjabloon kan worden `altas.getImageTemplate` opgehaald met de functie en worden gebruikt als de inhoud van een HTML-markering. De sjabloon kan worden `htmlContent` doorgegeven aan de optie `color`van `secondaryColor`de `text` markering en vervolgens worden aangepast met behulp van de , en opties.

In het volgende `marker-arrow` voorbeeld wordt de sjabloon gebruikt met een rode primaire kleur, een roze secundaire kleur en een tekstwaarde van 00.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-markering met ingebouwde pictogramsjabloon" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML-markering pen met ingebouwde pictogramsjabloon</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Aangepaste herbruikbare sjablonen maken

Als uw toepassing hetzelfde pictogram met verschillende pictogrammen gebruikt of als u een module maakt die extra afbeeldingssjablonen toevoegt, u deze pictogrammen eenvoudig toevoegen en ophalen uit de Azure Maps-webSDK. Gebruik de volgende statische `atlas` functies op de naamruimte.

| Name | Retourtype | Beschrijving | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Hiermee voegt u een aangepaste SVG-afbeeldingssjabloon toe aan de naamruimte van de atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| tekenreeks | Hiermee haalt u een SVG-sjabloon op naam op. |
| `getAllImageTemplateNames()` | tekenreeks[] |  Hiermee haalt u een SVG-sjabloon op naam op. |

SVG-afbeeldingssjablonen ondersteunen de volgende tijdelijke aanduidingswaarden:

| Tijdelijke aanduiding | Beschrijving |
|-|-|
| `{color}` | De primaire kleur. | 
| `{secondaryColor}` | De secundaire kleur. | 
| `{scale}` | De SVG-afbeelding wordt geconverteerd naar een png-afbeelding wanneer deze wordt toegevoegd aan de sprite van de kaartafbeelding. Deze tijdelijke aanduiding kan worden gebruikt om een sjabloon te schalen voordat deze wordt geconverteerd om ervoor te zorgen dat deze duidelijk wordt weergegeven. | 
| `{text}` | De locatie om tekst weer te geven wanneer deze wordt gebruikt met een HTML-markering. |

In het volgende voorbeeld ziet u hoe u een SVG-sjabloon maakt en deze toevoegt aan de Azure Maps-webSDK als een herbruikbare pictogramsjabloon. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aangepaste pictogramsjabloon toevoegen aan atlasnaamruimte" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de sjabloon Pen <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Aangepaste pictogramtoevoegen aan atlasnaamruimte</a> door Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lijst met afbeeldingssjablonen

In deze tabel worden alle afbeeldingssjablonen weergegeven die momenteel beschikbaar zijn in de Azure Maps-webSDK. De naam van de sjabloon bevindt zich boven elke afbeelding. Standaard is de primaire kleur blauw en de secundaire kleur wit. Om de secundaire kleur op een witte achtergrond beter te zien, hebben de volgende afbeeldingen de secundaire kleur ingesteld op zwart.

**Sjablonen voor symboolpictogramssjablonen**

|||||
|:-:|:-:|:-:|:-:|
| Markering | marker-dik | markeringscirkel | markeringsvlak |
|![pictogram markering](./media/image-templates/marker.png)|![pictogram met markeringsdikte](./media/image-templates/marker-thick.png)|![pictogram markeringscirkel](./media/image-templates/marker-circle.png)|![pictogram markeert vlak](./media/image-templates/marker-flat.png)|
||||
| markeringsvierkant | markering-vierkant-cluster | markering-pijl | marker-bal-pin | 
|![pictogram markeringsvierkant](./media/image-templates/marker-square.png)|![pictogram marker-vierkant-cluster](./media/image-templates/marker-square-cluster.png)|![pictogram markering-pijl](./media/image-templates/marker-arrow.png)|![pictogram marker-ball-pin](./media/image-templates/marker-ball-pin.png)|
||||
| markering-vierkant afgerond | markering-vierkant-afgerond-cluster | flag | vlagdriehoek |
| ![pictogram met markeringsvierkant](./media/image-templates/marker-square-rounded.png) | ![pictogram met vierkant-afgerond cluster](./media/image-templates/marker-square-rounded-cluster.png) | ![pictogram vlag](./media/image-templates/flag.png) | ![pictogram vlagdriehoek](./media/image-templates/flag-triangle.png) |
||||
| driehoek | driehoek-dik | driehoek-pijl-omhoog | driehoek-pijl-links |
| ![pictogram driehoek](./media/image-templates/triangle.png) | ![pictogram driehoekd dik](./media/image-templates/triangle-thick.png) | ![pictogram driehoek-pijl-omhoog](./media/image-templates/triangle-arrow-up.png) | ![pictogram driehoek-pijl-links](./media/image-templates/triangle-arrow-left.png) |
||||
| Zeshoek | zeshoek-dik | zeshoek-afgerond | hexagon-afgerond-dik |
| ![pictogram hexagon](./media/image-templates/hexagon.png) | ![zeshoek-dik pictogram](./media/image-templates/hexagon-thick.png) | ![pictogram zeshoekafgerond](./media/image-templates/hexagon-rounded.png) | ![zeshoek-afgerond-dik pictogram](./media/image-templates/hexagon-rounded-thick.png) |
||||
| vastmaken | pin-ronde | afgerond vierkant | afgerond-vierkant-dik |
| ![Speldpictogram](./media/image-templates/pin.png) | ![pictogram pin-round](./media/image-templates/pin-round.png) | ![pictogram afgerond vierkant](./media/image-templates/rounded-square.png) | ![pictogram afgerond vierkant-dik](./media/image-templates/rounded-square-thick.png) |
||||
| pijl-omhoog | pijl-omhoog-dun | car ||
| ![pictogram pijl-omhoog](./media/image-templates/arrow-up.png) | ![pictogram pijl-omhoog-dun](./media/image-templates/arrow-up-thin.png) | ![autopictogram](./media/image-templates/car.png) | |

**Polygoonvulpatroonsjablonen**

|||||
|:-:|:-:|:-:|:-:|
| Checker | controle-gedraaid | Cirkels | cirkels-gespreid |
| ![pictogram controle](./media/image-templates/checker.png) | ![pictogram met gedraaide controle](./media/image-templates/checker-rotated.png) | ![pictogram cirkels](./media/image-templates/circles.png) | ![pictogram met cirkelsafstand](./media/image-templates/circles-spaced.png) |
|||||
| diagonale lijnen omhoog | diagonale lijnen naar beneden | diagonale strepen omhoog | diagonale strepen naar beneden |
| ![pictogram diagonale lijnen](./media/image-templates/diagonal-lines-up.png) | ![pictogram diagonaal-lijnen-omlaag](./media/image-templates/diagonal-lines-down.png) | ![pictogram diagonale strepen](./media/image-templates/diagonal-stripes-up.png) | ![pictogram diagonaal-strepen](./media/image-templates/diagonal-stripes-down.png) |
|||||
| rasterlijnen | geroteerde rasterlijnen | geroteerde rasterstrepen | x-vulling |
| ![pictogram rasterlijnen](./media/image-templates/grid-lines.png) | ![pictogram geroteerde rasterlijnen](./media/image-templates/rotated-grid-lines.png) | ![pictogram rotated-grid-stripes](./media/image-templates/rotated-grid-stripes.png) | ![pictogram x-vulling](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zigzag-verticaal | Punten |  |
| ![pictogram zig-zag](./media/image-templates/zig-zag.png) | ![pictogram zig-zag-verticaal](./media/image-templates/zig-zag-vertical.png) | ![pictogram punten](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Probeer het nu, gereedschap

Met het volgende gereedschap u de verschillende ingebouwde afbeeldingssjablonen op verschillende manieren weergeven en de primaire en secundaire kleuren en schaal aanpassen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opties voor pictogramsjabloon" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>sjabloonopties voor penpictogramsopties</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlasnaamruimte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Zie de volgende artikelen voor meer codevoorbeelden waarin afbeeldingssjablonen kunnen worden gebruikt:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML-makers toevoegen](map-add-bubble-layer.md)
