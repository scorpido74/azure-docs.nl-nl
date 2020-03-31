---
title: Een kaartlaag van het Open Geospatial Consortium (OGC) toevoegen | Microsoft Azure Maps
description: Meer informatie over hoe u een OGC-kaartlaag op de kaart bedekken en hoe u de verschillende opties in de klasse OgcMapLayer gebruiken.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334287"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Een kaartlaag van het Open Geospatial Consortium (OGC) toevoegen

De `atlas.layer.OgcMapLayer` klasse kan WMS-afbeeldingen (Web Map Services) en WMTS-afbeeldingen (Web Map Tile Services) op de kaart plaatsen. WMS is een standaard protocol ontwikkeld door OGC voor het serveren van georeferenced kaartbeelden via het internet. Beeldgeoreferencing is de processen van het koppelen van een afbeelding aan een geografische locatie. WMTS is ook een standaard protocol ontwikkeld door OGC. Het is ontworpen voor het serveren van vooraf gerenderde en georeferenced kaarttegels.

In de volgende secties worden de functies van `OgcMapLayer` de webkaartservice beschreven die door de klasse worden ondersteund.

**Webmapservice (WMS)**

- Ondersteunde versies: `1.0.0` `1.1.0`, `1.1.1`, en`1.3.0`
- De service moet `EPSG:3857` het projectiesysteem ondersteunen of herprojecties verwerken.
- GetFeatureInfo vereist dat `EPSG:4326` de service herprojecties ondersteunt of verwerkt. 
- Ondersteunde bewerkingen:

    | | |
    | :-- | :-- |
    | GetCapabilities GetCapabilities GetCapabilities GetCapabilit | Haalt metagegevens over de service op met de ondersteunde mogelijkheden |
    | GetMap (GetMap) | Hiermee haalt u een kaartafbeelding op voor een bepaald gebied |
    | GetFeatureInfo | Ophalen `feature_info`, die onderliggende gegevens over de functie bevat |

**Webmaptegelservice (WMTS)**

- Ondersteunde versies:`1.0.0`
- Tegels moeten vierkant zijn, zodanig dat `TileWidth == TileHeight`.
- CRS ondersteund: `EPSG:3857` of`GoogleMapsCompatible` 
- De TileMatrix-id moet een gehele waarde zijn die overeenkomt met een zoomniveau op de kaart. Op een azure-kaart is het `"0"` zoomniveau een waarde tussen en `"22"`. Dus, `"0"` wordt ondersteund, `"00"` maar wordt niet ondersteund.
- Ondersteunde bewerkingen:

    | | |
    | :-- | :-- |
    | GetCapabilities GetCapabilities GetCapabilities GetCapabilit | Hiermee haalt u de ondersteunde bewerkingen en functies op |
    | GetTile (GetTile) | Hiermee haalt u afbeeldingen op voor een bepaalde tegel |

## <a name="overlay-an-ogc-map-layer"></a>Een OGC-kaartlaag bedekken

Het `url` kan de basis-URL voor de service of een volledige URL met de query voor het verkrijgen van de mogelijkheden van de service. Afhankelijk van de verstrekte details kan de WFS-client verschillende standaard URL-indelingen proberen om te bepalen hoe u in eerste instantie toegang tot de service krijgen.

In de volgende code ziet u hoe u een OGC-kaartlaag op de kaart overlayt.

<br/>

<iframe height='700' scrolling='no' title='Voorbeeld van de laag OGC Map' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie het voorbeeld van de pen<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC-kaartlaag</a> van Azure Maps op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opties voor OGC-kaartlagen

Het onderstaande voorbeeld toont de verschillende OGC-kaartlaagopties. U op de codepenknop in de rechterbovenhoek klikken om de codepen te bewerken.

<br/>

<iframe height='700' scrolling='no' title='Opties voor OGC-kaartlagen' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de opties voor pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC-kaartlagen</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web Map Service Explorer

Met het volgende gereedschap worden afbeeldingen uit de Web Map Services (WMS) en Web Map Tile Services (WMTS) als lagen overlays. U selecteren welke lagen in de service op de kaart worden weergegeven. U ook de bijbehorende legenda's voor deze lagen weergeven.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web Map Service Explorer' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de Pen OGC Web Map<a href='https://codepen.io/azuremaps'>@azuremaps</a>Service <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>explorer</a> by Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U ook de kaartinstellingen opgeven om een proxyservice te gebruiken. Met de proxyservice u resources laden die worden gehost op domeinen die cors niet hebben ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Ogcmaplayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcmaplayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Zie de volgende artikelen, die codevoorbeelden bevatten die u aan uw kaarten zou kunnen toevoegen:

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Benut kernactiviteiten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)
