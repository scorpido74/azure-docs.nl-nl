---
title: Verbinding maken met een WFS-service (Web Feature Service) | Microsoft Azure Maps
description: Meer informatie over hoe u verbinding maken met een WFS-service en vervolgens de WFS-service opvraagt met de Azure Maps-webSDK en de module Ruimtelijke IO.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334207"
---
# <a name="connect-to-a-wfs-service"></a>Verbinding maken met een WFS-service

Een Web Feature Service (WFS) is een webservice voor het opvragen van ruimtelijke gegevens met een gestandaardiseerde API die wordt gedefinieerd door het Open Geospatial Consortium (OGC). Met `WfsClient` de klasse in de ruimtelijke IO-module kunnen ontwikkelaars verbinding maken met een WFS-service en querygegevens van de service.

De volgende functies worden `WfsClient` ondersteund door de klasse:

- Ondersteunde versies: `1.0.0` `1.1.0`, en`2.0.0`
- Ondersteunde filteroperatoren: binaire vergelijkingen, logica, `bbox`wiskunde, waarde en .
- Verzoeken worden `HTTP GET` alleen met behulp van.
- Ondersteunde bewerkingen:

    | | |
    | :-- | :-- |
    | GetCapabilities GetCapabilities GetCapabilities GetCapabilit | Hiermee genereert u een metagegevensdocument met geldige WFS-bewerkingen en parameters |
    | GetFeature (GetFeature) | Geeft als resultaat een selectie functies uit een gegevensbron |
    | DescribeFeatureType | Geeft als resultaat de ondersteunde functietypen |

## <a name="using-the-wfs-client"></a>De WFS-client gebruiken

De `atlas.io.ogc.WfsClient` klasse in de ruimtelijke IO-module maakt het eenvoudig om een WFS-service op te vragen en de antwoorden om te zetten in GeoJSON-objecten. Dit GeoJSON-object kan vervolgens voor andere toewijzingsdoeleinden worden gebruikt.

Met de volgende code wordt een WFS-service opgevraagd en worden de geretourneerde functies op de kaart weergegeven.

<br/>

<iframe height='700' scrolling='no' title='Eenvoudig WFS-voorbeeld' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie het <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>voorbeeld pen eenvoudig WFS</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Ondersteunde filters

De specificatie voor de WFS-standaard maakt gebruik van OGC-filters. De onderstaande filters worden ondersteund door de WFS-client, ervan uitgaande dat de service die wordt aangeroepen ook deze filters ondersteunt. Aangepaste filtertekenreeksen kunnen `CustomFilter` in de klasse worden doorgegeven.

**Logische operators**

- `And`
- `Or`
- `Not`

**Waardebeheerders**

- `GmlObjectId`
- `ResourceId`

**Wiskundige operatoren**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Vergelijkingsoperatoren**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

De volgende code toont het gebruik van verschillende filters met de WFS-client.

<br/>

<iframe height='500' scrolling='no' title= 'Voorbeelden van WFS-filters' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de voorbeelden van Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS-filteren</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS-serviceverkenner

De volgende code gebruikt de WFS-client om WFS-services te verkennen. Selecteer een eigenschapstekstlaag in de service en zie de bijbehorende legenda.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS-serviceverkenner' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS-serviceexplorer</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Als u toegang wilt krijgen tot WFS-services die worden gehost op niet-CORS-enabled-eindpunten, kan een cors-proxyservice worden doorgegeven aan de `proxyService` optie van de WFS-client, zoals hieronder wordt weergegeven. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Benut kernactiviteiten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)
