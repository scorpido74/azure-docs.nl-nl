---
title: Muis gebeurtenissen afhandelen met Azure Maps | Microsoft Docs
description: Een interactieve Java script-kaart maken met toewijzings gebeurtenissen
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da6b183155de0fbc370751254a6842343d280874
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638964"
---
# <a name="interact-with-the-map---mouse-events"></a>Communiceren met de muis gebeurtenissen van de kaart

In dit artikel wordt beschreven hoe u [de eigenschap eigenschappen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) van de [kaart klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) gebruikt om gebeurtenissen te markeren op de kaart en op verschillende lagen van de kaart. U ziet ook hoe u de eigenschap gebeurtenis klasse gebeurtenissen kunt gebruiken om gebeurtenissen te markeren wanneer u met een HTML-markering communiceert.

## <a name="interact-with-the-map"></a>Interactie met de kaart

<iframe height='600' scrolling='no' title='Interactie met de kaart – muis gebeurtenissen' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interactie met de kaart – muis gebeurtenissen</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Speel met de bovenstaande kaart en Bekijk de bijbehorende muis gebeurtenissen die aan de rechter kant zijn gemarkeerd. U kunt op het **tabblad js** klikken om de Java script-code weer te geven en te bewerken. U kunt ook klikken op de knop **bewerken op CodePen** en de code bewerken op CodePen.

## <a name="interact-with-map-layers"></a>Communiceren met kaart lagen

<iframe height='600' scrolling='no' title='Interactie met de gebeurtenissen van de kaart-laag' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interactie met de gebeurtenissen van de kaart-laag</a> door Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De code hierboven markeert de naam van de gebeurtenissen die worden geactiveerd tijdens de interactie met de symbool-laag. De laag symbool, bellen, lijn en veelhoek bieden ondersteuning voor dezelfde set gebeurtenissen. De laag van de tegel ondersteunt geen van deze gebeurtenissen.

## <a name="interact-with-html-marker"></a>Interactie met HTML-markering

<iframe height='500' scrolling='no' title='Interactie met de kaart-HTML-markerings gebeurtenissen' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interactie met de markerings gebeurtenissen van de kaart-HTML</a> per<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

De bovenstaande code voegt java script-toewijzings gebeurtenissen toe aan een HTML-markering. Het markeert ook de naam van de gebeurtenissen die worden geactiveerd tijdens de interactie met de HTML-markering.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Diagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [De Azure Maps Services-module gebruiken](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Voorbeeld pagina voor code](https://aka.ms/AzureMapsSamples)
