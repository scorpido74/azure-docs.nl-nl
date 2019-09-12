---
title: Gebeurtenissen verwerken met Azure Maps | Microsoft Docs
description: Een interactieve Web-SDK-kaart maken met toewijzings gebeurtenissen
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899432"
---
# <a name="interact-with-the-map"></a>Interactie met de kaart

In dit artikel wordt beschreven hoe u de eigenschap eigenschappen van de [kaart klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) gebruikt om gebeurtenissen te markeren op de kaart en op verschillende lagen van de kaart. U ziet ook hoe u de eigenschap gebeurtenis klasse gebeurtenissen kunt gebruiken om gebeurtenissen te markeren wanneer u met een HTML-markering communiceert.

## <a name="interact-with-the-map"></a>Interactie met de kaart

Speel met de onderstaande kaart en Bekijk de bijbehorende muis gebeurtenissen die aan de rechter kant zijn gemarkeerd. U kunt op het **tabblad js** klikken om de Java script-code weer te geven en te bewerken. U kunt ook klikken op de knop **bewerken op CodePen** en de code bewerken op CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interactie met de kaart – muis gebeurtenissen' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interactie met de kaart – muis gebeurtenissen</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Communiceren met kaart lagen

De volgende code markeert de naam van de gebeurtenissen die worden geactiveerd tijdens de interactie met de symbool-laag. De laag symbool, bellen, lijn en veelhoek bieden ondersteuning voor dezelfde set gebeurtenissen. De heatmap en tegel lagen bieden geen ondersteuning voor een van deze gebeurtenissen.

<br/>

<iframe height='600' scrolling='no' title='Interactie met de gebeurtenissen van de kaart-laag' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interactie met de gebeurtenissen van de kaart-laag</a> door Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interactie met HTML-markering

Met de volgende code worden java script-toewijzings gebeurtenissen toegevoegd aan een HTML-markering. Het markeert ook de naam van de gebeurtenissen die worden geactiveerd tijdens de interactie met de HTML-markering.

<br/>

<iframe height='500' scrolling='no' title='Interactie met de kaart-HTML-markerings gebeurtenissen' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interactie met de markerings gebeurtenissen van de kaart-HTML</a> per<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De volgende tabel geeft een overzicht van alle ondersteunde gebeurtenissen van de kaart klasse.

| Gebeurtenis             | Description |
|-------------------|-------------|
| boxzoomend        | Deze gebeurtenis wordt geactiveerd wanneer een ' zoom functie ' in vak wordt beëindigd.|
| boxzoomstart      | Deze gebeurtenis wordt geactiveerd wanneer een ' zoom functie ' in vak wordt gestart.|
| Schakel             | Deze gebeurtenis wordt gestart wanneer een aanwijs apparaat op hetzelfde punt op de kaart wordt ingedrukt en losgelaten.|
| sluiten             | Deze gebeurtenis wordt gestart wanneer de pop-up hand matig of programmatisch wordt gesloten.|
| context       | Deze gebeurtenis wordt gestart wanneer op de rechter knop van de muis wordt geklikt.|
| dataadded         | Wordt geactiveerd wanneer aan de gegevens bron shapes worden toegevoegd.|
| dataremoved       | Deze gebeurtenis wordt gestart wanneer vormen uit de gegevens bron worden verwijderd.|
| datasourceupdated | Deze gebeurtenis wordt gestart wanneer het data source-object wordt bijgewerkt.|
| DblClick          | Deze gebeurtenis wordt gestart wanneer twee keer op een aanwijs apparaat wordt geklikt op hetzelfde punt op de kaart.|
| Schuif              | Wordt herhaaldelijk geactiveerd tijdens een interactie ' slepen naar pan ' op de markering kaart, pop-up of HTML.|
| dragend           | Wordt geactiveerd wanneer de interactie ' slepen naar pan ' op de kaart, pop-up of HTML-markering eindigt.|
| dragstart         | Deze gebeurtenis wordt gestart wanneer de interactie ' slepen naar pan ' wordt gestart op de kaart, pop-up of HTML-markering.|
| fout             | Wordt geactiveerd wanneer er een fout optreedt.|
| KeyDown           | Deze gebeurtenis wordt gestart wanneer een toets wordt ingedrukt.|
| ToetsIndrukken          | Deze gebeurtenis wordt gestart wanneer een sleutel die een typable-teken (een ANSI-sleutel) produceert, wordt ingedrukt.|
| omhoog             | Deze gebeurtenis wordt gestart wanneer een sleutel wordt losgelaten.|
| layeradded        | Deze gebeurtenis wordt gestart wanneer een laag wordt toegevoegd aan de kaart.|
| load              | Wordt onmiddellijk geactiveerd nadat alle benodigde resources zijn gedownload en de eerste visueel volledige rendering van de kaart heeft plaatsgevonden.|
| muis         | Deze gebeurtenis wordt gestart wanneer een aanwijs apparaat wordt ingedrukt binnen de kaart.|
| gebeurtenis         | Deze gebeurtenis wordt gestart wanneer een aanwijs apparaat binnen de kaart wordt verplaatst.|
| mouseout          | Deze gebeurtenis wordt gestart wanneer een punt apparaat het canvas van de kaart verlaat.|
| mouseover         | Deze gebeurtenis wordt gestart wanneer een aanwijs apparaat binnen de kaart wordt verplaatst.|
| muis           | Deze gebeurtenis wordt gestart wanneer een aanwijs apparaat wordt vrijgegeven binnen de kaart.|
| Ga              | Wordt herhaaldelijk geactiveerd tijdens een bewegende overgang van de ene weer gave naar de andere, als gevolg van de interactie of methoden van een gebruiker.|
| moveend           | Net nadat de kaart is geactiveerd, wordt de overgang van de ene weer gave naar de andere door gegeven als gevolg van de gebruikers interactie of-methoden.|
| movestart         | Wordt geactiveerd vlak voordat de kaart een overgang van de ene naar de andere weer gave start, als gevolg van de interactie of methoden van de gebruiker.|
| open              | Deze gebeurtenis wordt gestart wanneer de pop-up hand matig of programmatisch wordt geopend.|
| afstand             | Wordt geactiveerd wanneer de hoogte van de kaart (kanteling) wordt gewijzigd als gevolg van de interactie of methoden van een gebruiker.|
| pitchend          | Wordt geactiveerd direct nadat de kaart is gewijzigd (kanteling) als gevolg van een gebruikers interactie of-methode.|
| pitchstart        | Wordt geactiveerd wanneer de hoogte van de kaart (kanteling) een wijziging aangaat als gevolg van een gebruikers interactie of-methode.|
| gereed             | Wordt geactiveerd wanneer de mini maal vereiste toewijzings bronnen worden geladen voordat de kaart gereed is om programmatisch te worden gecommuniceerd met.|
| waardoor            | <p> Wordt geactiveerd wanneer de kaart naar het scherm wordt getekend als gevolg van:<ul><li>Een wijziging in de positie van de kaart, in-of uitzoomen, verkopen of plaatsen.</li><li>Een wijziging in de stijl van de kaart.</li><li>Een wijziging in een gegevens bron.</li><li>Het laden van een vector tegel, geojson-bestand, Glyph of sprite.</li></ul></p>|
| Klein            | Wordt geactiveerd direct nadat het formaat van de kaart is gewijzigd.|
| rechtsom            | Wordt herhaaldelijk geactiveerd tijdens de interactie ' slepen om te draaien '.|
| rotateend         | Wordt geactiveerd wanneer de interactie ' slepen om te draaien ' eindigt.|
| rotatestart       | Wordt geactiveerd wanneer de interactie ' slepen om draaien ' wordt gestart.|
| ShapeChanged      | Deze gebeurtenis wordt gestart wanneer een eigenschap van een shape-object wordt gewijzigd.|
| sourceadded       | Deze gebeurtenis wordt gestart wanneer een gegevens bron of VectorTileSource wordt toegevoegd aan de kaart.|
| sourceremoved     | Deze gebeurtenis wordt gestart wanneer een gegevens bron of VectorTileSource wordt verwijderd uit de kaart.|
| styledata         | Deze gebeurtenis wordt gestart wanneer de stijl van de kaart wordt geladen of gewijzigd.|
| tokenacquired     | Wordt geactiveerd wanneer een AAD-toegangs token wordt verkregen.|
| touchcancel       | Wordt geactiveerd wanneer een touchcancel-gebeurtenis plaatsvindt binnen de kaart.|
| touchend          | Wordt geactiveerd wanneer een touchend-gebeurtenis plaatsvindt binnen de kaart.|
| touchmove         | Wordt geactiveerd wanneer een touchmove-gebeurtenis plaatsvindt binnen de kaart.|
| touchstart        | Wordt geactiveerd wanneer een touchstart-gebeurtenis plaatsvindt binnen de kaart.|
| schijf             | Deze gebeurtenis wordt gestart wanneer een muis wiel wordt weer gegeven in de kaart.|
| Zoom              | Wordt herhaaldelijk geactiveerd tijdens een bewegende overgang van het ene zoom niveau naar het andere, als gevolg van de interactie of methoden van de gebruiker.|
| zoomend           | Wordt geactiveerd vlak nadat de kaart een overgang van het ene zoom niveau naar het andere heeft uitgevoerd, als gevolg van de interactie of methoden van de gebruiker.|
| zoomstart         | Gestart vlak voordat de kaart een overgang van het ene zoom niveau naar het andere start, als gevolg van de interactie of methoden van de gebruiker.|


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [De Azure Maps Services-module gebruiken](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
