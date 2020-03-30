---
title: Een HTML-markering toevoegen aan kaart | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over het toevoegen van een HTML-markering aan een kaart met behulp van de Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2700d42c25d58911fb275ad9ce6c5610cd22624d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536765"
---
# <a name="add-html-markers-to-the-map"></a>HTML-markeringen toevoegen aan de kaart

In dit artikel ziet u hoe u een aangepaste HTML zoals een afbeeldingsbestand aan de kaart toevoegt als HTML-markering.

> [!NOTE]
> HTML-markeringen maken geen verbinding met gegevensbronnen. In plaats daarvan wordt positiegegevens direct aan de `markers` markering toegevoegd en wordt de markering toegevoegd aan de eigenschap kaarten die een [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)is.

> [!IMPORTANT]
> In tegenstelling tot de meeste lagen in het Azure Maps-webbesturingselement waarmee WebGL wordt gebruikt voor rendering, gebruiken HTML-markeringen traditionele DOM-elementen voor rendering. Als zodanig, hoe meer HTML-markers toegevoegd aan een pagina, hoe meer DOM-elementen er zijn. Prestaties kunnen verslechteren na het toevoegen van een paar honderd HTML-markeringen. Voor grotere gegevenssets u overwegen uw gegevens te clusteren of een laag Symbool of Bel te gebruiken.

## <a name="add-an-html-marker"></a>Een HTML-markering toevoegen

De klasse [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) heeft een standaardstijl. U de markering aanpassen door de kleur- en tekstopties van de markering in te stellen. De standaardstijl van de klasse HTML-markering is `{color}` `{text}` een SVG-sjabloon met een tijdelijke aanduiding en tijdelijke aanduiding. Stel de kleur- en teksteigenschappen in de HTML-markeringsopties in voor een snelle aanpassing. 

Met de volgende code wordt een HTML-markering gemaakt en wordt de eigenschap kleur ingesteld op 'DodgerBlue' en de eigenschap tekst op '10'. Er is een pop-up `click` aan de markering gekoppeld en gebeurtenis wordt gebruikt om de zichtbaarheid van de pop-up in te schakelen.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een HTML-markering toevoegen aan een kaart' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Een HTML-markering toevoegen aan een kaart</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Html-markering met SVG-sjabloon maken

De `htmlContent` standaardmarkering van een Html-markering is `{color}` een `{text}` SVG-sjabloon met plaatsmappen en daarin. U aangepaste SVG-tekenreeksen maken en dezelfde tijdelijke aanduidingen toevoegen aan uw SVG, zodat de `color` instelling en `text` opties van de markeringsupdate deze tijdelijke aanduidingen in uw SVG instellen.

<br/>

<iframe height='500' scrolling='no' title='HTML-markering met aangepaste SVG-sjabloon' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML-markering pen met aangepaste SVG-sjabloon</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> De Azure Maps web SDK biedt verschillende SVG-afbeeldingssjablonen die kunnen worden gebruikt met HTML-markeringen. Zie het document [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

## <a name="add-a-css-styled-html-marker"></a>Een HTML-markering met CSS-stijl toevoegen

Een van de voordelen van HTML-markers is dat er veel grote aanpassingen zijn die kunnen worden bereikt met CSS. In dit voorbeeld bestaat de inhoud van de HtmlMarker uit HTML en CSS die een geanimeerde pin maken die op zijn plaats valt en pulsen.

<br/>

<iframe height='500' scrolling='no' title='HTML-gegevensbron' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> by Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Sleepbare HTML-markeringen

In dit voorbeeld ziet u hoe u een HTML-markering draggable maakt. Ondersteuning voor `drag` `dragstart`HTML-markeringen `dragend` en gebeurtenissen.

<br/>

<iframe height='500' scrolling='no' title='Sleepbare HTML-markering' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>HTML-markering pen</a> slepen<a href='https://codepen.io/azuremaps'>@azuremaps</a>door Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Muisgebeurtenissen toevoegen aan HTML-markeringen

In deze voorbeelden wordt uitgelegd hoe u muis toevoegen en gebeurtenissen slepen naar een HTML-markering.

<br/>

<iframe height='500' scrolling='no' title='Muisgebeurtenissen toevoegen aan HTML-markeringen' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>die muisgebeurtenissen toevoegt aan HTML-markeringen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Htmlmarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten wilt toevoegen:

> [!div class="nextstepaction"]
> [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Een bubbellaag toevoegen](./map-add-bubble-layer.md)
