---
title: De stijl van de kaart in Azure Maps wijzigen | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over de stijl functionaliteit die beschikbaar is in Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335693"
---
# <a name="change-the-style-of-the-map"></a>Stijl van de kaart wijzigen

De kaart ondersteunt diverse verschillende [stijl opties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) die kunnen worden ingesteld wanneer de kaart wordt geïnitialiseerd of later met de `setStyle` functie Maps. In dit artikel wordt uitgelegd hoe u deze stijl opties kunt gebruiken om de weer gave van kaarten aan te passen. Meer informatie over het instellen van een stijl bij het laden van een kaart en informatie over het instellen van een nieuwe kaart stijl met het besturings element stijl kiezer.

## <a name="set-the-style-options"></a>De stijl opties instellen 

Stijl opties kunnen worden door gegeven aan de kaart wanneer deze later wordt geïnitialiseerd of bijgewerkt met de `setStyle` functie Maps.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

Het volgende hulp programma laat zien hoe de verschillende stijl opties wijzigen hoe de kaart wordt weer gegeven. Als u de 3D-gebouwen wilt bekijken, zoomt u in op dicht bij een primaire plaats. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor kaart stijl" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de opties voor de <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>kaart stijl</a> van de Pen op Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Een Basiskaart stijl kiezen

Een van de meest gang bare kaart stijl opties wordt gebruikt voor het wijzigen van de stijl van de basis kaart die is opgemaakt. Veel van de [ondersteunde kaart stijlen in azure Maps](supported-map-styles.md) zijn beschikbaar in de Web-SDK. 

### <a name="set-base-map-style-on-map-load"></a>Stijl van basis kaart instellen voor toewijzen van kaart


De kaart stijl kan worden opgegeven bij het initialiseren van de kaart door de optie in te stellen `style` . In de volgende code is de `style` optie van de kaart ingesteld op `grayscale_dark` bij initialisatie.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Stijl instellen voor toewijzings belasting' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>instellen van de stijl van de kaart belasting</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>De stijl van het basis overzicht bijwerken

 De kaart stijl kan worden bijgewerkt met behulp van de `setStyle` functie en de `style` optie voor de gewenste kaart stijl instellen.

```javascript
map.setStyle({ style: 'satellite' });
```

In de volgende code, nadat een kaart exemplaar is geladen, wordt de kaart stijl bijgewerkt van `road` naar `satellite` met behulp van de functie [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='De stijl bijwerken' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>het profiel bijwerken</a> met Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>De stijl kiezer toevoegen

Het besturings element stijl kiezer biedt een gemakkelijk te gebruiken knop met het deel venster flyout dat door de eind gebruiker kan worden gebruikt om de kaart stijl te wijzigen. De stijl kiezer heeft twee verschillende indelings opties. De stijl kiezer maakt standaard gebruik van de `icons` indeling en geeft alle kaart stijlen weer als een horizontale rij pictogrammen. 

<center>

![Pictogram indeling stijl kiezer](media/choose-map-style/style-picker-icon-layout.png)</center>

De tweede indelings optie wordt opgeroepen `list` en geeft een lijst weer met kaart stijlen.  

<center>

![Indeling van lijst met stijl kiezer](media/choose-map-style/style-picker-list-layout.png)</center>


De volgende code laat zien hoe u een instantie van het besturings element stijl kiezer maakt en hoe u deze toevoegt aan de rechter bovenhoek van de kaart. De stijl kiezer is ingesteld op een donkere stijl en toont een aantal geselecteerde paar kaart stijlen met behulp van de laag lijst.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Met de volgende code wordt een besturings element stijl kiezer met de standaard instellingen aan de kaart toegevoegd, zodat de gebruiker eenvoudig tussen de verschillende kaart stijlen kan scha kelen. De kaart stijl in-of uitschakelen met het besturings element kaart stijl in de rechter bovenhoek.

<br/>

<iframe height='500' scrolling='no' title='De stijl kiezer toevoegen' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>de stijl kiezer toevoegen</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wanneer u de prijs categorie S0 van Azure Maps gebruikt, wordt in het besturings element stijl kiezer standaard alle beschik bare stijlen vermeld. Als u het aantal stijlen in deze lijst wilt beperken, geeft u een matrix van de stijlen die u wilt weer geven in de lijst in de `mapStyle` optie van de stijl kiezer. Als u S1 gebruikt en alle beschik bare stijlen wilt weer geven, stelt u de `mapStyles` optie van de stijl kiezer in op `"all"` .

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Besturings elementen toevoegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Kaartbesturingselementen toevoegen](map-add-controls.md)

> [!div class="nextstepaction"]
> [Een speld toevoegen](map-add-pin.md)
