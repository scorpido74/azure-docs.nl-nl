---
title: Afbeeldings sjablonen in de Azure Maps Web-SDK | Microsoft Azure kaarten
description: In dit artikel leert u hoe u afbeeldings sjablonen kunt gebruiken met HTML-markeringen en verschillende lagen in de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: f3b1141ea3c3c8e33b8a2ae12c22b6962a90d32b
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77198221"
---
# <a name="how-to-use-image-templates"></a>Afbeeldingssjablonen gebruiken

Afbeeldingen kunnen worden gebruikt met HTML-markeringen en verschillende lagen in de Azure Maps Web-SDK:

 - Met symbool lagen kunnen punten op de kaart worden weer gegeven met een afbeeldings pictogram. Symbolen kunnen ook worden weer gegeven langs een pad naar een regel.
 - Veelhoek lagen kunnen worden weer gegeven met een opvul patroon afbeelding. 
 - HTML-markeringen kunnen punten weer geven met behulp van afbeeldingen en andere HTML-elementen.

Om te zorgen voor goede prestaties met lagen, laadt u de afbeeldingen in de kaart afbeelding sprite resource vóór rendering. De [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), van de SymbolLayer, laadt standaard een aantal markerings afbeeldingen in een aantal kleuren in de kaart afbeelding sprite. Deze markerings afbeeldingen en meer zijn beschikbaar als SVG-sjablonen. Ze kunnen worden gebruikt voor het maken van installatie kopieën met aangepaste schalen of voor het gebruik van de primaire en secundaire kleur van klanten. In totaal zijn er 42 afbeeldings sjablonen beschikbaar: 27 symbool pictogrammen en 15 veelhoek patronen.

Afbeeldings sjablonen kunnen worden toegevoegd aan de kaart installatie kopie sprite-resources met behulp van de functie `map.imageSprite.createFromTemplate`. Met deze functie kunnen Maxi maal vijf para meters worden door gegeven in;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

De `id` is een unieke id die u maakt. De `id` wordt toegewezen aan de installatie kopie wanneer deze wordt toegevoegd aan de toewijzings afbeelding sprite. Gebruik deze id in de lagen om op te geven welke afbeeldings bron moet worden weer gegeven. De `templateName` geeft aan welke afbeeldings sjabloon moet worden gebruikt. Met de optie `color` stelt u de primaire kleur van de afbeelding in en de `secondaryColor` opties stelt de secundaire kleur van de afbeelding in. Met de optie `scale` wordt de afbeeldings sjabloon geschaald voordat deze op de afbeelding Sprite wordt toegepast. Wanneer de installatie kopie wordt toegepast op de afbeelding sprite, wordt deze omgezet in een PNG-bestand. Voor een scherpe rendering is het beter om de afbeeldings sjabloon omhoog te schalen voordat u deze toevoegt aan de sprite, dan om deze te schalen in een laag.

Met deze functie wordt de afbeelding asynchroon geladen in de afbeelding sprite. Daarom wordt er een Promise geretourneerd die u kunt wachten tot deze functie is voltooid.

De volgende code laat zien hoe u een installatie kopie maakt van een van de ingebouwde sjablonen en deze kunt gebruiken met een symbool laag.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>Een afbeeldings sjabloon met een symbool laag gebruiken

Zodra een afbeeldings sjabloon is geladen in de kaart afbeelding sprite, kan deze worden weer gegeven als een symbool in een symbool-laag door te verwijzen naar de bron-ID van de installatie kopie in de `image` optie van de `iconOptions`.

In het volgende voor beeld wordt een symbool laag weer gegeven met behulp van de sjabloon `marker-flat` afbeelding met een primaire groen kleur en een witte secundaire kleur. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbool laag met ingebouwde pictogram sjabloon" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de laag met het pictogram voor de pen door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>( <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>ingebouwde pictogrammen sjabloon</a> ).
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Een afbeeldings sjabloon gebruiken langs een pad naar een regel

Zodra een afbeeldings sjabloon is geladen in de kaart afbeelding sprite, kan deze worden weer gegeven langs het pad van een regel door een Lines Tring toe te voegen aan een gegevens bron en een Symbol-laag te gebruiken met een `lineSpacing`optie en door te verwijzen naar de ID van de afbeeldings bron in de `image` optie van de `iconOptions`. 

In het volgende voor beeld wordt een roze lijn op de kaart weer gegeven en wordt een symbool laag gebruikt met behulp van de `car` afbeeldings sjabloon met een blauw primaire kleur van Dodger en een witte secundaire kleur. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lijn laag met ingebouwd pictogram sjabloon" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>laag met ingebouwde pictogram sjablonen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Als de afbeeldings sjabloon omhoog wijst, stelt u de optie `rotation` pictogram van de laag Symbol in op 90 als u wilt dat deze in dezelfde richting als de lijn moet wijzen.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Een afbeeldings sjabloon met een polygoon laag gebruiken

Zodra een afbeeldings sjabloon is geladen in de kaart afbeelding sprite, kan deze worden weer gegeven als een opvul patroon in een polygoon laag door te verwijzen naar de bron-ID van de installatie kopie in de `fillPattern` optie van de laag.

In het volgende voor beeld wordt een polygoon laag met behulp van de `dot`-afbeeldings sjabloon met een rode primaire kleur en een transparante secundaire kleur weer gegeven.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Veelhoek vullen met ingebouwd pictogram sjabloon" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>-opvul veelhoek met de ingebouwde pictogram sjabloon</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Het instellen van de secundaire kleur van opvul patronen maakt het gemakkelijker om te zien hoe de onderliggende kaart het primaire patroon blijft leveren. 

## <a name="use-an-image-template-with-an-html-marker"></a>Een afbeeldings sjabloon gebruiken met een HTML-markering

Een afbeeldings sjabloon kan worden opgehaald met behulp van de functie `altas.getImageTemplate` en worden gebruikt als de inhoud van een HTML-markering. De sjabloon kan worden door gegeven aan de `htmlContent` optie van de markering en vervolgens worden aangepast met de opties `color`, `secondaryColor`en `text`.

In het volgende voor beeld wordt gebruikgemaakt van de `marker-arrow` sjabloon met een rode primaire kleur, een roze secundaire kleur en een tekst waarde van "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-markering met ingebouwde pictogram sjabloon" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML-markering van de pen met ingebouwde pictogram sjabloon</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Aangepaste herbruikbare sjablonen maken

Als uw toepassing gebruikmaakt van hetzelfde pictogram met verschillende pictogrammen of als u een module maakt waarmee extra afbeeldings sjablonen worden toegevoegd, kunt u deze pictogrammen eenvoudig toevoegen en ophalen uit de Azure Maps Web-SDK. Gebruik de volgende statische functies voor de `atlas` naam ruimte.

| Name | Retour type | Beschrijving | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Hiermee voegt u een aangepaste SVG-afbeeldings sjabloon toe aan de Atlas-naam ruimte. |
|  `getImageTemplate(templateName: string, scale?: number)`| tekenreeks | Hiermee wordt een SVG-sjabloon met de naam opgehaald. |
| `getAllImageTemplateNames()` | string[] |  Hiermee wordt een SVG-sjabloon met de naam opgehaald. |

SVG-afbeeldings sjablonen ondersteunen de volgende waarden voor de tijdelijke aanduiding:

| Tijdelijke aanduiding | Beschrijving |
|-|-|
| `{color}` | De primaire kleur. | 
| `{secondaryColor}` | De secundaire kleur. | 
| `{scale}` | De SVG-afbeelding wordt geconverteerd naar een PNG-afbeelding wanneer deze wordt toegevoegd aan de kaart afbeelding sprite. Deze tijdelijke aanduiding kan worden gebruikt om een sjabloon te schalen voordat deze wordt geconverteerd om ervoor te zorgen dat deze duidelijk wordt weer gegeven. | 
| `{text}` | De locatie voor het weer geven van tekst wanneer deze wordt gebruikt in combi natie met een HTML-markering. |

In het volgende voor beeld ziet u hoe u een SVG-sjabloon maakt en hoe u deze toevoegt aan de Azure Maps Web-SDK als een pictogram sjabloon die opnieuw kan worden gebruikt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aangepaste pictogram sjabloon toevoegen aan de Atlas-naam ruimte" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>aangepaste pictogram sjabloon pen toevoegen aan de naam ruimte</a> van de Atlas Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lijst met afbeeldings sjablonen

Deze tabel bevat alle afbeeldings sjablonen die momenteel beschikbaar zijn in de websdk van Azure Maps. De naam van de sjabloon bevindt zich boven elke afbeelding. Standaard is de primaire kleur blauw en de secundaire kleur wit. Om ervoor te zorgen dat de secundaire kleur gemakkelijker te zien is op een witte achtergrond, hebben de volgende afbeeldingen de secundaire kleur ingesteld op zwart.

**Symbool pictogram sjablonen**

|||||
|:-:|:-:|:-:|:-:|
| tijdlijnmarkeerteken | markering-dik | markering-cirkel | markering-plat |
|![markerings pictogram](./media/image-templates/marker.png)|![markering-dik pictogram](./media/image-templates/marker-thick.png)|![markering: cirkel pictogram](./media/image-templates/marker-circle.png)|![markering-vlak pictogram](./media/image-templates/marker-flat.png)|
||||
| markering-vier kant | markering-vier kant-cluster | markering-pijl | markering-bal-PIN | 
|![pictogram markering-vier kant](./media/image-templates/marker-square.png)|![markering-vier kant-cluster pictogram](./media/image-templates/marker-square-cluster.png)|![pictogram voor markering-pijl](./media/image-templates/marker-arrow.png)|![markering-bal-PIN-pictogram](./media/image-templates/marker-ball-pin.png)|
||||
| markering-vier kant rond | markering-vier kant-afgerond-cluster | Markeringen | vlag: drie hoek |
| ![markering: vier kant pictogram](./media/image-templates/marker-square-rounded.png) | ![markering-vier kant-afgerond-cluster pictogram](./media/image-templates/marker-square-rounded-cluster.png) | ![vlag pictogram](./media/image-templates/flag.png) | ![pictogram voor de vlag-drie hoek](./media/image-templates/flag-triangle.png) |
||||
| drie | drie hoek: dik | drie hoek omlaag | drie hoekje-pijl-links |
| ![pictogram drie hoek](./media/image-templates/triangle.png) | ![drie hoek-dik pictogram](./media/image-templates/triangle-thick.png) | ![pictogram met drie hoek omlaag](./media/image-templates/triangle-arrow-up.png) | ![pictogram met drie hoekje-pijl-links](./media/image-templates/triangle-arrow-left.png) |
||||
| zeshoek | zeshoek-dik | zeshoek-afgerond | zeshoek-afgerond-dik |
| ![pictogram van zeshoek](./media/image-templates/hexagon.png) | ![pictogram van zeshoek-dik](./media/image-templates/hexagon-thick.png) | ![pictogram met zeshoek afgerond](./media/image-templates/hexagon-rounded.png) | ![pictogram van zeshoek-afgerond-dik](./media/image-templates/hexagon-rounded-thick.png) |
||||
| pin | vastmaken en afronden | afgerond vier kant | afgerond-vier kant-dik |
| ![speld pictogram](./media/image-templates/pin.png) | ![pictogram voor een afronding](./media/image-templates/pin-round.png) | ![pictogram afgerond op vier kant](./media/image-templates/rounded-square.png) | ![pictogram rond afgerond-vier kant](./media/image-templates/rounded-square-thick.png) |
||||
| pijl-omhoog | pijl-omhoog-dun | zou ||
| ![pictogram pijl omhoog](./media/image-templates/arrow-up.png) | ![pijl-omhoog-dun pictogram](./media/image-templates/arrow-up-thin.png) | ![pictogram auto](./media/image-templates/car.png) | |

**Opvul patroon Sjablonen voor veelhoek**

|||||
|:-:|:-:|:-:|:-:|
| blok | controle-gedraaid | kring | cirkels-gespatieerd |
| ![pictogram van de controle](./media/image-templates/checker.png) | ![pictogram van de controle functie](./media/image-templates/checker-rotated.png) | ![pictogram cirkels](./media/image-templates/circles.png) | ![cirkels pictogram](./media/image-templates/circles-spaced.png) |
|||||
| diagonaal omhoog | diagonale lijnen-omlaag | diagonale strepen-omhoog | diagonale strepen-omlaag |
| ![pictogram voor diagonale lijnen](./media/image-templates/diagonal-lines-up.png) | ![pictogram diagonale lijnen/omlaag](./media/image-templates/diagonal-lines-down.png) | ![pictogram met diagonale strepen naar boven](./media/image-templates/diagonal-stripes-up.png) | ![pictogram met diagonale strepen-omlaag](./media/image-templates/diagonal-stripes-down.png) |
|||||
| raster lijnen | gedraaid-raster-lijnen | gedraaid-raster-stroken | x-Fill |
| ![pictogram raster lijnen](./media/image-templates/grid-lines.png) | ![pictogram voor gedraaid raster-lijnen](./media/image-templates/rotated-grid-lines.png) | ![pictogram voor gedraaid raster-strepen](./media/image-templates/rotated-grid-stripes.png) | ![pictogram x-opvul](./media/image-templates/x-fill.png) |
|||||
| zigzag | zigzag-zigzag-Vertical | wijzen |  |
| ![zigzag-/-pictogram](./media/image-templates/zig-zag.png) | ![het pictogram zigzag-zigzag-Vertical](./media/image-templates/zig-zag-vertical.png) | ![pictogram punten](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Hulp programma nu uitproberen

Met het volgende hulp programma kunt u de verschillende ingebouwde afbeeldings sjablonen op verschillende manieren weer geven en de primaire en secundaire kleuren en schaal aanpassen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opties voor pictogram sjablonen" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>sjabloon opties</a> voor het pictogram van de Pen op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Atlas-naam ruimte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Raadpleeg de volgende artikelen voor meer code voorbeelden waarbij afbeeldings sjablonen kunnen worden gebruikt:

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een line laag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoon laag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML-makers toevoegen](map-add-bubble-layer.md)
