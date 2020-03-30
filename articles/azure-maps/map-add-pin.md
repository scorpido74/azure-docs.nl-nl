---
title: Een symboollaag toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u de laag Symbool gebruikt om een symbool aan te passen en symbolen op een kaart toe te voegen met de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209695"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Een symboollaag toevoegen aan een kaart

Sluit een symbool aan op een gegevensbron en gebruik het om een pictogram of een tekst op een bepaald punt weer te geven. 

Symboollagen worden weergegeven met WebGL. Gebruik een symboollaag om grote verzamelingen punten op de kaart weer te geven. In vergelijking met de HTML-markering geeft de symboollaag een groot aantal puntgegevens op de kaart weer, met betere prestaties. De symboollaag ondersteunt echter geen traditionele CSS- en HTML-elementen voor styling.  

> [!TIP]
> Symboollagen geven standaard de coördinaten van alle geometrieën weer in een gegevensbron. Als u de laag zodanig wilt beperken dat `filter` alleen puntgeometriefuncties worden weergegeven, stelt u de eigenschap van de laag in `['==', ['geometry-type'], 'Point']` of `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` als u dat wilt, u ook MultiPoint-functies opnemen.

De map image sprite manager laadt aangepaste afbeeldingen die worden gebruikt door de symboollaag. Het ondersteunt de volgende afbeeldingsindelingen:

- Jpeg
- PNG
- Svg
- BMP
- GIF (geen animaties)

## <a name="add-a-symbol-layer"></a>Een symboollaag toevoegen

Voordat u een symboollaag aan de kaart toevoegen, moet u een paar stappen uitvoeren. Maak eerst een gegevensbron en voeg deze toe aan de kaart. Maak een symboollaag. Geef vervolgens de gegevensbron door aan de symboollaag om de gegevens uit de gegevensbron op te halen. Voeg ten slotte gegevens toe aan de gegevensbron, zodat er iets moet worden weergegeven. 

De onderstaande code laat zien wat er na het laden aan de kaart moet worden toegevoegd. Dit voorbeeld maakt één punt op de kaart met behulp van een symboollaag. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Er zijn vier verschillende soorten puntgegevens die aan de kaart kunnen worden toegevoegd:

- GeoJSON Point geometrie - Dit object bevat alleen een coördinaat van een punt en niets anders. De `atlas.data.Point` helperklasse kan worden gebruikt om deze objecten eenvoudig te maken.
- GeoJSON MultiPoint-geometrie - Dit object bevat de coördinaten van meerdere punten en niets anders. De `atlas.data.MultiPoint` helperklasse kan worden gebruikt om deze objecten eenvoudig te maken.
- GeoJSON-functie - Dit object bestaat uit een GeoJSON-geometrie en een set eigenschappen die metagegevens bevatten die zijn gekoppeld aan de geometrie. De `atlas.data.Feature` helperklasse kan worden gebruikt om deze objecten eenvoudig te maken.
- `atlas.Shape`klasse is vergelijkbaar met de GeoJSON-functie. Beide bestaan uit een GeoJSON-geometrie en een set eigenschappen die metagegevens bevatten die zijn gekoppeld aan de geometrie. Als een GeoJSON-object aan een gegevensbron wordt toegevoegd, kan het eenvoudig in een laag worden weergegeven. Als de eigenschap coördinaten van dat GeoJSON-object echter wordt bijgewerkt, veranderen de gegevensbron en de kaart niet. Dat komt omdat er geen mechanisme in het JSON-object zit om een update te activeren. De shapeklasse biedt functies voor het bijwerken van de gegevens die deze bevat. Wanneer een wijziging wordt aangebracht, worden de gegevensbron en de kaart automatisch op de hoogte gebracht en bijgewerkt. 

Met het volgende codevoorbeeld wordt een GeoJSON-puntgeometrie gemaakt en deze in de `atlas.Shape` klasse ingevoerd om het eenvoudig bij te werken. Het midden van de kaart wordt in eerste instantie gebruikt om een symbool weer te geven. Een klikgebeurtenis wordt aan de kaart toegevoegd, zodat wanneer deze wordt geactiveerd, de coördinaten van de muis worden gebruikt met de shapesfunctie. `setCoordinates` De muiscoördinaten worden geregistreerd op het moment van de klikgebeurtenis. Vervolgens wordt `setCoordinates` de locatie van het symbool op de kaart bijgewerkt.

<br/>

<iframe height='500' scrolling='no' title='Pinlocatie wisselen' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>penschakelaar-pinlocatie</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Symboollagen optimaliseren standaard de weergave van symbolen door symbolen te verbergen die elkaar overlappen. Als u inzoomt, worden de verborgen symbolen zichtbaar. Als u deze functie wilt uitschakelen en `allowOverlap` alle symbolen `iconOptions` te `true`allen tijde wilt renderen, stelt u de eigenschap van de opties in op .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Een aangepast pictogram toevoegen aan een symboollaag

Symboollagen worden weergegeven met WebGL. Als zodanig moeten alle bronnen, zoals pictogramafbeeldingen, in de WebGL-context worden geladen. In dit voorbeeld ziet u hoe u een aangepast pictogram toevoegt aan de kaartbronnen. Dit pictogram wordt vervolgens gebruikt om puntgegevens weer te geven met een aangepast symbool op de kaart. De `textField` eigenschap van de symboollaag vereist dat een expressie wordt opgegeven. In dit geval willen we de temperatuur eigenschap te maken. Aangezien de temperatuur een getal is, moet deze worden omgezet in een tekenreeks. Daarnaast willen we er "°F" aan toevoegen. Een uitdrukking kan worden gebruikt om deze samenvoeging te doen; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Pictogram Aangepaste symboolafbeelding' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie het <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>pictogram penafbeeldingssymbool</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pen van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> De Azure Maps web SDK biedt verschillende aanpasbare afbeeldingssjablonen die u met de symboollaag gebruiken. Zie het document [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

## <a name="customize-a-symbol-layer"></a>Een symboollaag aanpassen 

De symboollaag heeft vele stylingopties beschikbaar. Hier is een hulpmiddel om uit te testen deze verschillende styling opties.

<br/>

<iframe height='700' scrolling='no' title='Opties voor symboollaag' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/PxVXje/'>pensymboollaagopties</a> van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Als u alleen tekst met een symboollaag wilt renderen, `image` u het `'none'`pictogram verbergen door de eigenschap van de pictogramopties in te stellen op.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Symboollaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbollayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pictogramopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Tekstopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [Een bubbellaag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML-makers toevoegen](map-add-bubble-layer.md)
