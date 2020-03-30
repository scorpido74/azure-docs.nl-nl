---
title: Een pop-up toevoegen aan een punt op een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u een pop-up toevoegt aan een punt met de Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055956"
---
# <a name="add-a-popup-to-the-map"></a>Een pop-up toevoegen aan de kaart

In dit artikel ziet u hoe u een pop-up toevoegt aan een punt op een kaart.

## <a name="understand-the-code"></a>De code begrijpen

De volgende code voegt een `name` puntfunctie, die en `description` eigenschappen heeft, toe aan de kaart met behulp van een symboollaag. Er wordt een instantie van de [klasse Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) gemaakt, maar niet weergegeven. Muisgebeurtenissen worden toegevoegd aan de symboollaag om het openen en sluiten van de pop-up te activeren. Wanneer het markeringssymbool wordt gezweefd, `position` wordt de eigenschap van de `content` pop-up bijgewerkt met de `name` `description` positie van de markering en wordt de optie bijgewerkt met een aantal HTML die de eigenschappen en eigenschappen van de puntfunctie die wordt gezweefd, omwikkelt. De pop-up wordt vervolgens weergegeven `open` op de kaart met behulp van de functie.

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

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een pop-up toevoegen met Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Een pop-up toevoegen met Azure Maps</a> by Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Een pop-up opnieuw gebruiken met meerdere punten

Er zijn gevallen waarin de beste aanpak is om een pop-up te maken en te hergebruiken. U bijvoorbeeld een groot aantal punten hebben en slechts één pop-up tegelijk weergeven. Door de pop-up opnieuw te gebruiken, wordt het aantal DOM-elementen dat door de toepassing wordt gemaakt, sterk verminderd, wat betere prestaties kan bieden. In het volgende voorbeeld worden 3-puntsfuncties gemaakt. Als u op een van deze klikken klikt, wordt een pop-up weergegeven met de inhoud voor die puntfunctie.

<br/>

<iframe height='500' scrolling='no' title='Pop-up opnieuw gebruiken met meerdere pins' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>popup</a> pen opnieuw gebruiken met<a href='https://codepen.io/azuremaps'>@azuremaps</a>meerdere pins van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Een pop-up aanpassen

Standaard heeft de pop-up een witte achtergrond, een aanwijzerpijl aan de onderkant en een sluitknop in de rechterbovenhoek. In het volgende voorbeeld wordt de `fillColor` achtergrondkleur zwart gewijzigd met de optie pop-up. De sluitknop wordt verwijderd `CloseButton` door de optie in te stellen op false. De HTML-inhoud van de pop-up gebruikt gewatteerde van 10 pixels van de randen van de pop-up. De tekst is wit gemaakt, dus het verschijnt mooi op de zwarte achtergrond.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aangepaste pop-up" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>pop-up Pen op basis van aangepaste pop-up</a> van Azure Maps op<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Pop-upsjablonen toevoegen aan de kaart

Pop-upsjablonen maken het eenvoudig om gegevensgestuurde lay-outs voor pop-ups te maken. De onderstaande secties tonen het gebruik van verschillende pop-upsjablonen om opgemaakte inhoud te genereren met behulp van eigenschappen van functies.

> [!NOTE]
> Standaard wordt alle gerenderde inhoud die de pop-upsjabloon gebruikt, sandboxed in een iframe als beveiligingsfunctie. Er zijn echter beperkingen:
>
> - Alle scripts, formulieren, aanwijzervergrendeling en de bovenste navigatiefunctionaliteit zijn uitgeschakeld. Koppelingen mogen worden geopend in een nieuw tabblad wanneer er op wordt geklikt. 
> - Oudere browsers die de `srcdoc` parameter op iframes niet ondersteunen, worden beperkt tot het renderen van een kleine hoeveelheid inhoud.
> 
> Als u erop vertrouwt dat de gegevens die in de pop-ups worden geladen en deze scripts mogelijk in pop-ups worden geladen, toegang krijgen tot uw toepassing, u dit uitschakelen door de optie pop-upsjablonen `sandboxContent` in te stellen op false. 

### <a name="string-template"></a>Tekenreekssjabloon

De sjabloon Tekenreeks vervangt tijdelijke aanduidingen met waarden van de functie-eigenschappen. De eigenschappen van de functie hoeven geen waarde van type Tekenreeks te krijgen. Houdt bijvoorbeeld `value1` een geheel getal vast. Deze waarden worden vervolgens doorgegeven `popupTemplate`aan de eigenschap inhoud van de . 

De `numberFormat` optie geeft de notatie op van het te weergeven nummer. Als `numberFormat` de code niet is opgegeven, wordt in de code de datumnotatie pop-upsjablonen gebruikt. De `numberFormat` optie maakt getallen op met de functie [Getal.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Als u grote getallen `numberFormat` wilt opmaken, u de optie gebruiken met functies van [NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Het onderstaande codefragment `maximumFractionDigits` wordt bijvoorbeeld gebruikt om het aantal fractiecijfers te beperken tot twee.

> [!Note]
> Er is maar één manier waarop de sjabloon Tekenreeks afbeeldingen kan renderen. Eerst moet de sjabloon Tekenreeks een afbeeldingstag hebben. De waarde die wordt doorgegeven aan de afbeeldingstag moet een URL naar een afbeelding zijn. Vervolgens moet de sjabloon `isImage` Tekenreeks zijn ingesteld `HyperLinkFormatOptions`op true in de . De `isImage` optie geeft aan dat de hyperlink voor een afbeelding is en dat de hyperlink in een afbeeldingstag wordt geladen. Wanneer op de hyperlink wordt geklikt, wordt de afbeelding geopend.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>PropertyInfo-sjabloon

De sjabloon PropertyInfo geeft beschikbare eigenschappen van de functie weer. De `label` optie geeft de tekst op die aan de gebruiker moet worden weergegeven. Als `label` deze niet is opgegeven, wordt de hyperlink weergegeven. En als de hyperlink een afbeelding is, wordt de waarde die is toegewezen aan de 'alt'-tag weergegeven. Hiermee `dateFormat` wordt de notatie van de datum opgegeven en als de datumnotatie niet is opgegeven, wordt de datum weergegeven als een tekenreeks. De `hyperlinkFormat` optie maakt klikbare links, `email` op dezelfde manier, de optie kan worden gebruikt om klikbare e-mailadressen weer te geven.

Voordat de propertyinfo-sjabloon de eigenschappen aan de eindgebruiker weergeeft, wordt opnieuw gecontroleerd of de eigenschappen inderdaad voor die functie zijn gedefinieerd. Het negeert ook het weergeven van stijl- en titeleigenschappen. Het wordt bijvoorbeeld niet `color` `size`weergegeven, `anchor` `strokeOpacity`, `visibility`, en . Zodra de eigenschappenpadcontrole in de back-end is voltooid, wordt de inhoud in een tabelindeling weergegeven zodra de eigenschappenpadcontrole is voltooid.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Meerdere inhoudssjablonen

Een functie kan ook inhoud weergeven met behulp van een combinatie van de sjabloon Tekenreeks en de sjabloon PropertyInfo. In dit geval worden met de sjabloon Tekenreeks tijdelijke aanduidingen waarden op een witte achtergrond weergegeven.  En met de sjabloon PropertyInfo wordt een afbeelding met volledige breedte in een tabel weergegeven. De eigenschappen in dit voorbeeld zijn vergelijkbaar met de eigenschappen die we in de vorige monsters hebben uitgelegd.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Punten zonder gedefinieerde sjabloon

Wanneer de pop-upsjabloon niet is gedefinieerd als een tekenreekssjabloon, een PropertyInfo-sjabloon of een combinatie van beide, gebruikt deze de standaardinstellingen. Wanneer `title` de `description` en zijn de enige toegewezen eigenschappen, de pop-up sjabloon toont een witte achtergrond, een nauwe knop in de rechterbovenhoek. En op kleine en middelgrote schermen, het toont een pijl aan de onderkant. De standaardinstellingen worden in een tabel `title` weergegeven `description`voor alle andere eigenschappen dan de . Zelfs wanneer u terugvalt naar de standaardinstellingen, kan de pop-upsjabloon nog steeds programmatisch worden gemanipuleerd. Gebruikers kunnen bijvoorbeeld hyperlinkdetectie uitschakelen en de standaardinstellingen zijn nog steeds van toepassing op andere eigenschappen.

Klik op de punten op de kaart in de CodePen. Er is een punt op de kaart voor elk van de volgende pop-upsjablonen: tekenreekssjabloon, PropertyInfo-sjabloon en sjabloon voor meerdere inhoud. Er zijn ook drie punten om te laten zien hoe sjablonen renderen met behulp van de standaardinstellingen.

<br/>

<iframe height='500' scrolling='no' title='Pop-upSjablonen' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Popupsjabloon opnieuw gebruiken

Net als bij het opnieuw gebruiken van pop-up, u pop-upsjablonen opnieuw gebruiken. Deze benadering is handig als u slechts één pop-upsjabloon tegelijk wilt weergeven, voor meerdere punten. Door de pop-upsjabloon opnieuw te gebruiken, wordt het aantal DOM-elementen dat door de toepassing is gemaakt, verminderd, wat de prestaties van uw toepassing verbetert. In het volgende voorbeeld wordt dezelfde pop-upsjabloon voor drie punten gebruikt. Als u op een van deze klikken klikt, wordt een pop-up weergegeven met de inhoud voor die puntfunctie.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplate opnieuw gebruiken' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Pop-upevenementen

Pop-ups kunnen worden geopend, gesloten en gesleept. De pop-upklasse biedt evenementen om ontwikkelaars te helpen reageren op deze gebeurtenissen. In het volgende voorbeeld wordt benadrukt welke gebeurtenissen worden geopend, gesloten of sleept de pop-up. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pop-upevenementen" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>Pen Popup-gebeurtenissen</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [Pop-upopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [Pop-upSjabloon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Zie de volgende grote artikelen voor volledige code monsters:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Een HTML-markering toevoegen](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)
