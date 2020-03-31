---
title: Browsers met webSDK-ondersteuning | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over ondersteunde browsers voor Microsoft Azure Maps Web SDK en hoe u controleren of een browser een ondersteunde browser is.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988784"
---
# <a name="web-sdk-supported-browsers"></a>Browsers die Web-SDK ondersteunen

De Azure Maps Web SDK biedt een helperfunctie genaamd [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Deze functie detecteert of een webbrowser de minimale set WebGL-functies heeft die nodig zijn om het laden en renderen van het kaartbesturingselement te ondersteunen. Hier is een voorbeeld van hoe u de functie gebruikt:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Bureaublad

De Azure Maps Web SDK ondersteunt de volgende desktopbrowsers:

- Microsoft Edge (huidige en vorige versie)
- Google Chrome (huidige en vorige versie)
- Mozilla Firefox (huidige en vorige versie)
- Apple Safari (Mac OS X) (huidige en vorige versie)

Zie ook [Verouderde browsers targeten](#Target-Legacy-Browsers) later in dit artikel.

## <a name="mobile"></a>Mobiele telefoon

De Azure Maps Web SDK ondersteunt de volgende mobiele browsers:

- Android
  - Huidige versie van Chrome op Android 6.0 en hoger
  - Chrome WebView op Android 6.0 en hoger
- iOS
  - Mobile Safari op de huidige en vorige grote versie van iOS
  - UIWebView en WKWebView op de huidige en vorige hoofdversie van iOS
  - Huidige versie van Chrome voor iOS

> [!TIP]
> Als u een kaart ineensluit in een mobiele toepassing met behulp van een WebView-besturingselement, gebruikt u mogelijk liever het [npm-pakket van de Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) in plaats van te verwijzen naar de versie van de SDK die wordt gehost op Azure Content Delivery Network. Deze aanpak vermindert de laadtijd omdat de SDK zich al op het apparaat van de gebruiker bevindt en niet hoeft te worden gedownload tijdens het uitvoeren van de tijd.

## <a name="nodejs"></a>Node.js

De volgende Web SDK-modules worden ook ondersteund in Node.js:

- Services module[(documentatie](how-to-use-services-module.md) | [npm module)](https://www.npmjs.com/package/azure-maps-rest)

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Verouderde browsers targeten

Misschien wilt u oudere browsers targeten die WebGL niet ondersteunen of die er slechts beperkte ondersteuning voor hebben. In dergelijke gevallen raden we u aan Azure Maps-services te gebruiken, samen met een open-source kaartbesturingselement zoals [Leaflet.](https://leafletjs.com/) Hier volgt een voorbeeld:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + bijsluiter" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de Pen Azure Maps +<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Folder</a> by Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Kaartbesturingselement](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Services-module](how-to-use-services-module.md)
