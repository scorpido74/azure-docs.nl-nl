---
title: Stijl functionaliteiten voor kaart | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over de stijl functies die beschikbaar zijn in Microsoft Azure Maps Web SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cabf39f017afe440c883a63db57643c5c5367128
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189742"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Kies een kaart stijl in Azure Maps

Veel van de [ondersteunde kaart stijlen in azure Maps](./supported-map-styles.md) zijn beschikbaar in de Web-SDK. In dit artikel wordt beschreven hoe u de functionaliteit voor stijl kunt gebruiken. Meer informatie over het instellen van een stijl bij het laden van een kaart en informatie over het instellen van een nieuwe kaart stijl met het besturings element stijl kiezer.

## <a name="set-style-on-map-load"></a>Stijl instellen voor toewijzings belasting

In de volgende code wordt de optie `style` van de kaart ingesteld op `grayscale_dark` bij de initialisatie.

<br/>

<iframe height='500' scrolling='no' title='Stijl instellen voor toewijzings belasting' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>instellen van de stijl in de map load</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>De stijl bijwerken

Na het laden van een kaart exemplaar in de volgende code wordt de kaart stijl bijgewerkt van `road` naar `satellite` met behulp van de functie [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) .

<br/>

<iframe height='500' scrolling='no' title='De stijl bijwerken' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen voor het <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>bijwerken van de stijl</a> op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>De stijl kiezer toevoegen

Met de volgende code wordt een [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) toegevoegd aan de kaart, zodat de gebruiker eenvoudig tussen de verschillende kaart stijlen kan scha kelen. De kaart stijl in-of uitschakelen met het besturings element kaart stijl in de rechter bovenhoek.

<br/>

<iframe height='500' scrolling='no' title='De stijl kiezer toevoegen' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>het toevoegen van stijl kiezer</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Wanneer u de prijs categorie S0 van Azure Maps gebruikt, wordt in het besturings element stijl kiezer standaard alle beschik bare stijlen vermeld. Als u het aantal stijlen in deze lijst wilt beperken, geeft u een matrix van de stijlen die u wilt weer geven in de lijst in de optie `mapStyle` van de stijl kiezer. Als u S1 gebruikt en alle beschik bare stijlen wilt weer geven, stelt u de optie `mapStyles` van de stijl kiezer in op `"all"`.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Diagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Besturings elementen toevoegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Kaart besturings elementen toevoegen](map-add-controls.md)

> [!div class="nextstepaction"]
> [Een pincode toevoegen](map-add-pin.md)
