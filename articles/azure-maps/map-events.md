---
title: Kaartgebeurtenissen afhandelen | Microsoft Azure Maps
description: In dit artikel leert u hoe u een interactieve Web SDK-kaart maakt met kaartgebeurtenissen met de Microsoft Azure Maps-webSDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534895"
---
# <a name="interact-with-the-map"></a>Interactie met de kaart

In dit artikel ziet u hoe u [de klasse kaartgebeurtenissen kunt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)gebruiken. De eigenschap markeren gebeurtenissen op de kaart en op verschillende lagen van de kaart. U ook gebeurtenissen markeren wanneer u een HTML-markering hebt.

## <a name="interact-with-the-map"></a>Interactie met de kaart

Speel met de kaart hieronder en bekijk de bijbehorende muisgebeurtenissen die aan de rechterkant zijn gemarkeerd. U op het **tabblad JS** klikken om de JavaScript-code weer te geven en te bewerken. U ook op **Bewerken op CodePen** klikken om de code op CodePen te wijzigen.

<br/>

<iframe height='600' scrolling='no' title='Interactie met de kaart – muisgebeurtenissen' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interactie met de kaart - muis gebeurtenissen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interactie met kaartlagen

Met de volgende code wordt de geactiveerde gebeurtenis markeert terwijl u met de symboollaag communiceert. De symbool-, bellen-, lijn- en veelhoeklaag ondersteunen allemaal dezelfde reeks gebeurtenissen. De heatmap en tegellagen ondersteunen geen van deze gebeurtenissen.

<br/>

<iframe height='600' scrolling='no' title='Interactie met de kaart – Laaggebeurtenissen' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>die interactie heeft met de kaart – Laaggebeurtenissen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interactie met HTML-markering

De volgende code voegt Javascript-kaartgebeurtenissen toe aan een HTML-markering. Het benadrukt ook de naam van de gebeurtenissen die worden geactiveerd als u interactie met de HTML-marker.

<br/>

<iframe height='500' scrolling='no' title='Interactie met de kaart - HTML-markeringsgebeurtenissen' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>die interactie heeft met de kaart - HTML-markeringsgebeurtenissen</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de volgende tabel worden alle ondersteunde kaartklassegebeurtenissen weergegeven.

| Gebeurtenis               | Beschrijving |
|---------------------|-------------|
| `boxzoomend`        | Ontslagen wanneer een interactie met 'box zoom' eindigt.|
| `boxzoomstart`      | Afgevuurd wanneer een interactie met 'box zoom' begint.|
| `click`             | Afgevuurd wanneer een aanwijsapparaat op hetzelfde punt op de kaart wordt ingedrukt en losgelaten.|
| `close`             | Ontslagen wanneer de pop-up handmatig of programmatisch wordt gesloten.|
| `contextmenu`       | Afgevuurd wanneer op de rechterknop van de muis wordt geklikt.|
| `data`              | Afgevuurd wanneer kaartgegevens worden geladen of gewijzigd. |
| `dataadded`         | Ontslagen wanneer vormen worden `DataSource`toegevoegd aan de .|
| `dataremoved`       | Ontslagen wanneer vormen uit `DataSource`de .|
| `datasourceupdated` | Ontslagen wanneer `DataSource` het object wordt bijgewerkt.|
| `dblclick`          | Ontslagen wanneer een aanwijsapparaat twee keer op hetzelfde punt op de kaart wordt geklikt.|
| `drag`              | Herhaaldelijk wordt afgevuurd tijdens een 'drag to pan'-interactie op de kaart-, pop-up- of HTML-markering.|
| `dragend`           | Wordt afgevuurd wanneer een interactie 'slepen naar pannen' eindigt op de kaart-, pop-up- of HTML-markering.|
| `dragstart`         | Wordt geactiveerd wanneer een interactie 'slepen naar pannen' begint op de kaart,pop-up of HTML-markering.|
| `error`             | Ontslagen wanneer er een fout optreedt.|
| `idle`              | <p>Ontslagen na het laatste frame dat is weergegeven voordat de kaart een "niet-actieve" status invoert:<ul><li>Er worden geen cameraovergangen uitgevoerd.</li><li>Alle momenteel aangevraagde tegels zijn geladen.</li><li>Alle fade/transition animaties zijn voltooid.</li></ul></p>|
| `keydown`           | Afgevuurd wanneer een toets wordt ingedrukt.|
| `keypress`          | Wordt afgevuurd wanneer een toets die een typable karakter (een ANSI-toets) produceert, wordt ingedrukt.|
| `keyup`             | Ontslagen als er een sleutel vrijkomt.|
| `layeradded`        | Wordt afgevuurd wanneer een laag aan de kaart wordt toegevoegd.|
| `layerremoved`      | Ontslagen wanneer een laag van de kaart wordt verwijderd.|
| `load`              | Ontslagen onmiddellijk nadat alle benodigde middelen zijn gedownload en de eerste visueel volledige weergave van de kaart heeft plaatsgevonden.|
| `mousedown`         | Afgevuurd wanneer een aanwijsapparaat op de kaart wordt gedrukt of wanneer deze bovenop een element wordt gedrukt.|
| `mouseenter`        | Ontslagen wanneer een aanwijsapparaat in eerste instantie over de kaart of een element wordt verplaatst. |
| `mouseleave`        | Ontslagen wanneer een aanwijsapparaat wordt verplaatst van de kaart of een element. |
| `mousemove`         | Ontslagen wanneer een aanwijsapparaat binnen de kaart of een element wordt verplaatst.|
| `mouseout`          | Ontslagen wanneer een punt apparaat laat de kaart doek onze laat een element.|
| `mouseover`         | Ontslagen wanneer een aanwijsapparaat over de kaart of een element wordt verplaatst.|
| `mouseup`           | Ontslagen wanneer een aanwijsapparaat wordt losgelaten op de kaart of wanneer op de top van een element.|
| `move`              | Herhaaldelijk afgevuurd tijdens een geanimeerde overgang van de ene weergave naar de andere, als gevolg van gebruikersinteractie of -methoden.|
| `moveend`           | Vuurvlak nadat de kaart een overgang van de ene weergave naar de andere heeft voltooid, als gevolg van gebruikersinteractie of -methoden.|
| `movestart`         | Vuurde vlak voordat de kaart begint met een overgang van de ene weergave naar de andere, als gevolg van gebruikersinteractie of -methoden.|
| `open`              | Ontslagen wanneer de pop-up handmatig of programmatisch wordt geopend.|
| `pitch`             | Afgevuurd wanneer de toonhoogte (kanteling) van de kaart verandert als gevolg van gebruikersinteractie of -methoden.|
| `pitchend`          | Ontslagen direct na de toonhoogte van de kaart (tilt) eindigt veranderen als gevolg van interactie van de gebruiker of methoden.|
| `pitchstart`        | Afgevuurd wanneer de toonhoogte (kanteling) van de kaart een verandering begint als gevolg van gebruikersinteractie of -methoden.|
| `ready`             | Ontslagen wanneer de minimaal vereiste kaartbronnen worden geladen voordat de kaart klaar is om programmatisch te worden samengewerkt.|
| `render`            | <p>Afgevuurd wanneer de kaart wordt aangetrokken tot het scherm, als gevolg van:<ul><li>Een wijziging in de positie, zoom, toonhoogte of lager van de kaart.</li><li>Een wijziging in de stijl van de kaart.</li><li>Een wijziging `DataSource` in een bron.</li><li>Het laden van een vectortegel, GeoJSON-bestand, glyph of sprite.</li></ul></p>|
| `resize`            | Ontslagen direct nadat de kaart is aangepast.|
| `rotate`            | Herhaaldelijk afgevuurd tijdens een interactie 'slepen om te draaien'.|
| `rotateend`         | Afgevuurd wanneer een interactie 'slepen om te draaien' eindigt.|
| `rotatestart`       | Afgevuurd wanneer een interactie 'slepen om te draaien' begint.|
| `shapechanged`      | Wordt geactiveerd wanneer de eigenschap van een vormobject wordt gewijzigd.|
| `sourcedata`        | Wordt afgevuurd wanneer een van de bronnen van de kaart wordt geladen of gewijzigd, inclusief als een tegel die tot een bron behoort, wordt geladen of gewijzigd. |
| `sourceadded`       | Ontslagen wanneer `DataSource` `VectorTileSource` een of wordt toegevoegd aan de kaart.|
| `sourceremoved`     | Ontslagen wanneer `DataSource` `VectorTileSource` een of van de kaart wordt verwijderd.|
| `styledata`         | Ontslagen wanneer de stijl van de kaart wordt geladen of gewijzigd.|
| `styleimagemissing` | Wordt afgevuurd wanneer een laag een afbeelding probeert te laden van de afbeeldingssprite die niet bestaat |
| `tokenacquired`     | Ontslagen wanneer een AAD-toegangstoken wordt verkregen.|
| `touchcancel`       | Ontslagen wanneer een touchcancel-gebeurtenis plaatsvindt binnen de kaart.|
| `touchend`          | Ontslagen wanneer een touchend-gebeurtenis plaatsvindt binnen de kaart.|
| `touchmove`         | Ontslagen wanneer een touchmove-gebeurtenis plaatsvindt binnen de kaart.|
| `touchstart`        | Ontslagen wanneer een touchstart-gebeurtenis plaatsvindt binnen de kaart.|
| `wheel`             | Afgevuurd wanneer een muiswielgebeurtenis plaatsvindt binnen de kaart.|
| `zoom`              | Herhaaldelijk afgevuurd tijdens een geanimeerde overgang van het ene zoomniveau naar het andere, als gevolg van gebruikersinteractie of -methoden.|
| `zoomend`           | Vuurvlak nadat de kaart een overgang van het ene zoomniveau naar het andere heeft voltooid, als gevolg van gebruikersinteractie of -methoden.|
| `zoomstart`         | Vuurde vlak voordat de kaart begint met een overgang van het ene zoomniveau naar het andere, als gevolg van gebruikersinteractie of -methoden.|


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor volledige code voorbeelden:

> [!div class="nextstepaction"]
> [De Azure Maps Services-module gebruiken](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
