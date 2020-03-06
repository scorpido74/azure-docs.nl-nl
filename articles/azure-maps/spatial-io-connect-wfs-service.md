---
title: Verbinding maken met een WFS-service (Web Feature Service) | Microsoft Azure kaarten
description: Meer informatie over hoe u verbinding maakt met een WFS-service en hoe u vervolgens een query kunt uitvoeren op de WFS-service met de Azure Maps Web SDK en de ruimtelijke IO-module.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0f50fe51f9c1cdef3c3f07c91640f5b9b9616229
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370958"
---
# <a name="connect-to-a-wfs-service"></a>Verbinding maken met een WFS-service

Een Web Feature Service (WFS) is een webservice voor het opvragen van ruimtelijke gegevens met een gestandaardiseerde API, die is gedefinieerd door de Open Geospatial Consortium (OGC). Met de klasse `WfsClient` in de ruimtelijke IO-module kunnen ontwikkel aars verbinding maken met een WFS-service en query's uitvoeren op gegevens van de service.

De volgende functies worden ondersteund door de klasse `WfsClient`:

- Ondersteunde versies: `1.0.0`, `1.1.0`en `2.0.0`
- Ondersteunde filter operators: binaire vergelijkingen, logica, wiskunde, waarde en `bbox`.
- Aanvragen worden alleen gemaakt met behulp van `HTTP GET`.
- Ondersteunde bewerkingen:

    | | |
    | :-- | :-- |
    | GetCapabilities | Hiermee wordt een meta gegevens document gegenereerd met geldige WFS-bewerkingen en-para meters |
    | GetFeature | Hiermee wordt een selectie van functies uit een gegevens bron geretourneerd |
    | DescribeFeatureType | Retourneert de ondersteunde functie typen |

## <a name="using-the-wfs-client"></a>De WFS-client gebruiken

Met de klasse `atlas.io.ogc.WfsClient` in de ruimtelijke IO-module kunt u eenvoudig een WFS-service opvragen en de antwoorden converteren naar geojson-objecten. Dit geojson-object kan vervolgens worden gebruikt voor andere toewijzings doeleinden.

Met de volgende code wordt een query uitgevoerd op een WFS-service en worden de geretourneerde functies op de kaart weer gegeven.

<br/>

<iframe height='700' scrolling='no' title='Eenvoudig WFS-voor beeld' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie het <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>eenvoudige WFS-voor beeld</a> Azure Maps van Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Ondersteunde filters

De specificatie voor de WFS-standaard maakt gebruik van OGC-filters. De onderstaande filters worden ondersteund door de WFS-client, ervan uitgaande dat de service die wordt aangeroepen ook deze filters ondersteunt. Aangepaste filter teken reeksen kunnen worden door gegeven aan de klasse `CustomFilter`.

**Logische operators**

- `And`
- `Or`
- `Not`

**Operator waarde**

- `GmlObjectId`
- `ResourceId`

**Reken kundige Opera tors**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Vergelijkings operators**

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

<iframe height='500' scrolling='no' title= 'Voor beelden van WFS-filter' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Raadpleeg de pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS filter voorbeelden</a> op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS service Explorer

De volgende code maakt gebruik van de WFS-client om WFS services te verkennen. Selecteer een laag met het type eigenschap in de service en Bekijk de bijbehorende legenda.

<br/>

<iframe height='700' scrolling='no' title= 'WFS service Explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS service Explorer</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

U kunt ook een proxy service gebruiken om resources te laden die worden gehost op domeinen waarvoor geen CORs is ingeschakeld. U definieert eerst een variabele voor de proxy service-URL en stelt de `proxyService` optie in voor de WFS-client. Als u een proxy service optie voor de gebruiker wilt weer geven, voegt u een gebruikers invoer toe aan de gebruikers interface. Laad de service-URL wanneer op de invoer wordt geklikt. De volgende code fragmenten laten zien hoe u de proxy service kunt gebruiken.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

Het onderstaande HTML-code fragment komt overeen met de bovenstaande java script-code:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Kern bewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevens indeling](spatial-io-supported-data-format-details.md)
