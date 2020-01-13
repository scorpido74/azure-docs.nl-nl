---
title: Een pop-upvenster toevoegen aan een punt op een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een pop-upvenster kunt toevoegen aan een punt met behulp van de Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 79bafb331cb7ad38ea7cad9e510b22886b647764
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911149"
---
# <a name="add-a-popup-to-the-map"></a>Een pop-upvenster toevoegen aan de kaart

In dit artikel wordt uitgelegd hoe u een pop-upvenster kunt toevoegen aan een punt op een kaart.

## <a name="understand-the-code"></a>De code begrijpen

Met de volgende code wordt een punt functie met `name`-en `description` eigenschappen aan de kaart toegevoegd met behulp van een Symbol-laag. Er wordt een exemplaar van de [pop-upklasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) gemaakt, maar dit wordt niet weer gegeven. Muis gebeurtenissen worden toegevoegd aan de symbool-laag om te activeren en sluiten van het pop-upvenster wanneer de muis aanwijzer over en op de symbool markering wordt gehouden. Wanneer het markerings symbool wordt geplaatst, wordt de eigenschap `position` van het pop-upvenster bijgewerkt met de positie van de markering en de `content` optie wordt bijgewerkt met een aantal HTML-code waarmee de `name` en `description` eigenschappen van de punt functie worden gepositioneerd. De pop-up wordt weer gegeven op de kaart met behulp van de functie `open`.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een pop-up toevoegen met Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>een pop-up toevoegen met behulp van Azure Maps</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Een pop-upvenster met meerdere punten opnieuw gebruiken

Wanneer u een groot aantal punten hebt en slechts één pop-up tegelijk wilt weer geven, is het raadzaam om één pop-up te maken en deze opnieuw te gebruiken in plaats van een pop-upvenster voor elke punt functie te maken. Door de pop-up opnieuw te gebruiken, wordt het aantal DOM-elementen dat door de toepassing is gemaakt, aanzienlijk verminderd, waardoor betere prestaties kunnen worden geboden. In het volgende voor beeld worden drie punt functies gemaakt. Als u op een van deze functies klikt, wordt er een pop-upvenster weer gegeven met de inhoud voor die punt functie.

<br/>

<iframe height='500' scrolling='no' title='Opnieuw gebruiken van pop-up met meerdere pincodes' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen opnieuw <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>gebruiken met meerdere pincodes</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Een pop-upvenster aanpassen

Standaard heeft de pop-up een witte achtergrond, een aanwijzer pijl aan de onderkant en een knop Sluiten in de rechter bovenhoek. In het volgende voor beeld wordt de achtergrond kleur gewijzigd in zwart met de optie `fillColor` van de pop-up. De knop Sluiten wordt verwijderd door de `shoCloseButton` optie in te stellen op ONWAAR. De HTML-inhoud van het pop-upvenster gebruikt 10 pixels van de randen van het pop-upvenster en de tekst wordt wit weer gegeven, zodat deze fraai op de zwarte achtergrond verschijnt.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aangepaste pop-up" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de aangepaste pen ( <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>pop-up</a> ) door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Pop-upgebeurtenisen

Pop-ups kunnen worden geopend, gesloten en gesleept. De pop-upklasse biedt gebeurtenissen voor de Help-ontwikkel aars die op deze acties reageren. In het volgende voor beeld ziet u welke gebeurtenissen worden geactiveerd wanneer u de pop-up opent, sluit of sleept. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pop-upgebeurtenisen" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>pop-up gebeurtenissen</a> van de Pen per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Raadpleeg de volgende fantastische artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Een HTML-markering toevoegen](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Een line laag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoon laag toevoegen](map-add-shape.md)