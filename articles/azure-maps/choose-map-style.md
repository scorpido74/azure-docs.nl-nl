---
title: De stijl van de kaart wijzigen in Azure Maps | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over stijlgerelateerde functionaliteiten die beschikbaar zijn in Microsoft Azure Maps web SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335693"
---
# <a name="change-the-style-of-the-map"></a>Stijl van de kaart wijzigen

De kaart ondersteunt verschillende [stijlopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) die kunnen worden ingesteld wanneer de `setStyle` kaart wordt geïnitialiseerd of later met behulp van de functie kaarten. In dit artikel ziet u hoe u deze stijlopties gebruiken om het uiterlijk van de kaarten aan te passen. Leer een stijl in te stellen bij het laden van een kaart en leer een nieuwe kaartstijl in te stellen met behulp van het besturingselement stijlkiezer.

## <a name="set-the-style-options"></a>De stijlopties instellen 

Stijlopties kunnen worden doorgegeven aan de kaart wanneer deze `setStyle` later wordt geïnitialiseerd of bijgewerkt met behulp van de functie Kaarten.

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

Met het volgende gereedschap ziet u hoe de verschillende stijlopties de weergave van de kaart wijzigen. Om de 3D-gebouwen te zien, zoom je in in de buurt van een grote stad. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor kaartstijl" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>stijlopties penkaart</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>van Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Een basiskaartstijl kiezen

Een van de meest voorkomende kaartstijlopties wordt gebruikt om de stijl van de gestylede basiskaart te wijzigen. Veel van de [ondersteunde kaartstijlen in Azure Maps](supported-map-styles.md) zijn beschikbaar in de Web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Basiskaartstijl instellen op kaartbelasting


De kaartstijl kan worden opgegeven bij het `style` initialiseren van de kaart door de optie in te stellen. In de volgende `style` code is de optie `grayscale_dark` van de kaart ingesteld op initialisatie.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='De stijl instellen op kaartbelasting' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>peninstelling voor de stijl op kaartbelasting</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>De basiskaartstijl bijwerken

 De kaartstijl kan worden `setStyle` bijgewerkt met `style` behulp van de functie en de optie instellen op de gewenste kaartstijl.

```javascript
map.setStyle({ style: 'satellite' });
```

In de volgende code wordt de kaartstijl bijgewerkt van `road` naar `satellite` de functie [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) nadat een kaartinstantie is geladen.

<br/>

<iframe height='500' scrolling='no' title='De stijl bijwerken' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen Bijwerken van<a href='https://codepen.io/azuremaps'>@azuremaps</a>de <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stijl</a> door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>De stijlkiezer toevoegen

De stijlkiezer besturingselement biedt een eenvoudig te gebruiken knop met flyout paneel dat kan worden gebruikt om door de eindgebruiker om de kaart stijl te veranderen. De stijlkiezer heeft twee verschillende indelingsopties. Standaard gebruikt de stijlkiezer `icons` de indeling en geeft alle kaartstijl weer als een horizontale rij pictogrammen. 

<center>

![Indeling voor pictogram stijlkiezer](media/choose-map-style/style-picker-icon-layout.png)</center>

De tweede indelingsoptie wordt aangeroepen `list` en geeft een schuifbare lijst met kaartstijlen weer.  

<center>

![Indeling voor lijst met stijlkiezer](media/choose-map-style/style-picker-list-layout.png)</center>


In de volgende code ziet u hoe u een instantie van het besturingselement voor stijlkiezer maakt en deze toevoegt aan de rechterbovenhoek van de kaart. De stijlkiezer is ingesteld op een donkere stijl en toont een geselecteerde paar kaartstijlen met behulp van de lijstlaag.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

De volgende code voegt een stijlkiezerbesturingselement met de standaardinstellingen toe aan de kaart, zodat de gebruiker eenvoudig kan schakelen tussen de verschillende kaartstijlen. Schakel de kaartstijl in met het besturingselement kaartstijl in de rechterbovenhoek.

<br/>

<iframe height='500' scrolling='no' title='De stijlkiezer toevoegen' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>De stijlkiezer</a> toevoegen<a href='https://codepen.io/azuremaps'>@azuremaps</a>door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wanneer u standaard de s0-prijslaag van Azure Maps gebruikt, geeft het besturingselement voor stijlkiezer standaard alle beschikbare stijlen weer. Als u het aantal stijlen in deze lijst wilt verminderen, geeft u een array `mapStyle` van de stijlen die u in de lijst wilt weergeven door naar de optie van de stijlkiezer. Als u S1 gebruikt en alle beschikbare stijlen `mapStyles` wilt weergeven, stelt `"all"`u de optie van de stijlkiezer in op .

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Stijlopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl (StyleControl)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [Opties voor StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Besturingselementen toevoegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Kaartbesturingselementen toevoegen](map-add-controls.md)

> [!div class="nextstepaction"]
> [Een speld toevoegen](map-add-pin.md)
