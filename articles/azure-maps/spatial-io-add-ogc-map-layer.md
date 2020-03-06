---
title: Een Open Geospatial Consortium-kaartLaag (OGC) toevoegen | Microsoft Azure kaarten
description: Meer informatie over het bedekken van een OGC-kaartLaag op de kaart en het gebruik van de verschillende opties in de klasse OgcMapLayer.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c41ee293c853572ec9e1f9dd3edf001c805924d3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402770"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Een kaartLaag toevoegen vanuit de Open Geospatial Consortium (OGC)

De `atlas.layer.OgcMapLayer`-klasse kan installatie kopie van Webmapping-Services (WMS) en WMTS (web mapping tile Services) op de kaart bedekken. WMS is een standaard protocol dat door OGC is ontwikkeld voor het leveren van geoverwijzings installatie kopieën via internet. Afbeelding georefererend is de processen voor het koppelen van een afbeelding aan een geografische locatie. WMTS is ook een standaard protocol dat is ontwikkeld door OGC. Het is ontworpen voor het leveren van vooraf gerenderde en geoverwijzinge kaart tegels.

De volgende secties bevatten een overzicht van de functies van de Webmapping-service die worden ondersteund door de `OgcMapLayer` klasse.

**Web mapping service (WMS)**

- Ondersteunde versies: `1.0.0`, `1.1.0`, `1.1.1`en `1.3.0`
- De service moet het `EPSG:3857` projectie systeem ondersteunen of de service moet in staat zijn om reprojecties te verwerken.
- GetFeatureInfo vereist dat de service ondersteuning biedt voor `EPSG:4326` of voor het verwerken van reprojecties. 
- Ondersteunde bewerkingen:

    | | |
    | :-- | :-- |
    | GetCapabilities | Hiermee worden meta gegevens van de service met de ondersteunde mogelijkheden opgehaald |
    | GetMap | Hiermee wordt een kaart afbeelding voor een opgegeven regio opgehaald |
    | GetFeatureInfo | Hiermee wordt `feature_info`opgehaald, die onderliggende gegevens bevat over de functie |

**Webmapping-tegel service (WMTS)**

- Ondersteunde versies: `1.0.0`
- Tegels moeten `TileWidth == TileHeight`zijn.
- CRS ondersteund: `EPSG:3857` of `GoogleMapsCompatible` 
- De TileMatrix-id moet een geheel getal zijn dat overeenkomt met een zoom niveau op de kaart. Op een Azure-kaart is het zoom niveau een waarde tussen `"0"` en `"22"`. Dit betekent dat `"0"` wordt ondersteund, maar `"00"` niet wordt ondersteund.
- Ondersteunde bewerkingen:

    | | |
    | :-- | :-- |
    | GetCapabilities | Hiermee worden de ondersteunde bewerkingen en functies opgehaald |
    | GetTile | Hiermee wordt de afbeelding voor een bepaalde tegel opgehaald |

## <a name="overlay-an-ogc-map-layer"></a>Een OGC-kaartLaag bedekken

Het `url` kan de basis-URL voor de service zijn of een volledige URL met de query voor het ophalen van de mogelijkheden van de service. Afhankelijk van de gegevens die u opgeeft, kan de WFS-client verschillende standaard-URL-indelingen proberen om te bepalen hoe de service voor het eerst wordt geopend.

De volgende code laat zien hoe u een OGC-kaartLaag kunt bedekken op de kaart.

<br/>

<iframe height='700' scrolling='no' title='Voor beeld van OGC-kaartLaag' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk het <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>voor beeld</a> Azure Maps van de OGC-kaartLaag van de Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opties voor OGC-kaartLaag

In het onderstaande voor beeld ziet u de verschillende opties voor de OGC-kaartLaag. U kunt in de rechter bovenhoek op de knop code-pen klikken om de code-pen te bewerken.

<br/>

<iframe height='700' scrolling='no' title='Opties voor OGC-kaartLaag' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de OGC voor de <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>kaart laag</a> van de pen Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web map service Explorer

Met het volgende hulp programma worden afbeeldingen van de web map Services (WMS) en WMTS (web map tile Services) als lagen bedekt. U kunt selecteren welke lagen in de service worden weer gegeven op de kaart. U kunt ook de bijbehorende legenda's voor deze lagen weer geven.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web map service Explorer' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web map service Explorer</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U kunt ook de kaart instellingen voor het gebruik van een proxy service opgeven. Met de proxy service kunt u bronnen laden die worden gehost op domeinen waarvoor CORs niet is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Raadpleeg de volgende artikelen, met code voorbeelden die u aan uw kaarten kunt toevoegen:

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Kern bewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevens indeling](spatial-io-supported-data-format-details.md)
