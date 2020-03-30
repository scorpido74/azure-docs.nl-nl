---
title: Ondersteunde kaartstijlen | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over verschillende stijlen voor het maken van kaarten die worden ondersteund door Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334042"
---
# <a name="azure-maps-supported-map-styles"></a>Ondersteunde kaartstijlen voor Azure Maps
Azure Maps ondersteunt verschillende ingebouwde kaartstijlen zoals hieronder beschreven.

## <a name="road"></a>Weg
Een **routekaart** is een standaardkaart met wegen, natuurlijke en kunstmatige kenmerken, samen met de labels voor die functies.

![kaartstijl](./media/supported-map-styles/road.png)

**Toepasselijke API's:**
* [Afbeelding van kaart](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kaarttegel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-kaartbesturingselement
* Android-kaartbediening

## <a name="blank-and-blank_accessible"></a>leeg en blank_accessible

De **lege** en **blank_accessible** kaartstijlen bieden een leeg canvas waarop gegevens kunnen worden gevisualiseerd. De **blank_accessible-stijl** blijft schermlezer-updates bieden met de locatiegegevens van de kaart, ook al wordt de basiskaart niet weergegeven.

> [!Note]
> In de web SDK u de achtergrondkleur `background-color` van de kaart wijzigen door de CSS-stijl van map DIV-element in te stellen.

**Toepasselijke API's:**
* Web SDK-kaartbesturingselement

## <a name="satellite"></a>satellite 
De **satellietstijl** is een combinatie van satelliet- en luchtfoto's.

![satelliettegelkaartstijl](./media/supported-map-styles/satellite.png)

**Toepasselijke API's:**
* [Satelliettegel](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK-kaartbesturingselement
* Android-kaartbediening

## <a name="satellite_road_labels"></a>satellite_road_labels
Deze kaart stijl is een hybride van wegen en labels bedekt op de top van satelliet-en luchtfoto's.

![satellite_road_labels kaartstijl](./media/supported-map-styles/satellite-road-labels.png)

**Toepasselijke API's:**
* Web SDK-kaartbesturingselement
* Android-kaartbediening

## <a name="grayscale_dark"></a>grayscale_dark
**grijstinten donker** is een donkere versie van de routekaart stijl.

![gray_scale kaartstijl](./media/supported-map-styles/grayscale-dark.png)

**Toepasselijke API's:**
* [Afbeelding van kaart](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kaarttegel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-kaartbesturingselement 
* Android-kaartbediening


## <a name="grayscale_light"></a>grayscale_light
**grijswaardenlicht** is een lichte versie van de routekaartstijl.

![grijswaardenlichtkaartstijl](./media/supported-map-styles/grayscale-light.png)

**Toepasselijke API's:**
* Web SDK-kaartbesturingselement
* Android-kaartbediening


## <a name="night"></a>nacht
**nacht** is een donkere versie van de routekaart stijl met gekleurde wegen en symbolen.

![nachtkaartstijl](./media/supported-map-styles/night.png)

**Toepasselijke API's:**
* Web SDK-kaartbesturingselement
* Android-kaartbediening

## <a name="road_shaded_relief"></a>road_shaded_relief
**weg gearceerde reliëf** is een Azure Maps belangrijkste stijl aangevuld met contouren van de aarde.

![gearceerde reliëfkaartstijl](./media/supported-map-styles/shaded-relief.png)

**Toepasselijke API's:**
* [Kaarttegel](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-kaartbesturingselement
* Android-kaartbediening

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** is een donkere kaartstijl met een hoger contrast dan de andere stijlen.

![donkere kaartstijl met hoog contrast](./media/supported-map-styles/high-contrast-dark.png)

**Toepasselijke API's:**
* Web SDK-kaartbesturingselement

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een kaartstijl in Azure Maps:

> [!div class="nextstepaction"]
> [Een kaartstijl kiezen](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
