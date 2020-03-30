---
title: De Azure Maps Services-module gebruiken | Microsoft Azure Maps
description: In dit artikel leert u hoe u de Microsoft Azure Maps REST-services gebruiken met de Azure Maps-servicesmodule.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988733"
---
# <a name="use-the-azure-maps-services-module"></a>De azure maps-servicesmodule gebruiken

De Azure Maps Web SDK biedt een *servicesmodule.* Deze module is een helperbibliotheek waarmee u de Azure Maps REST-services eenvoudig gebruiken in web- of Node.js-toepassingen met JavaScript of TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>De servicesmodule op een webpagina gebruiken

1. Een nieuw HTML-bestand maken.
1. Laad de Azure Maps-servicesmodule. U het op twee manieren laden:
    - Gebruik de wereldwijd gehoste Azure Content Delivery Network-versie van de Azure Maps-servicesmodule. Voeg een scriptverwijzing `<head>` toe aan het element van het bestand:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - U ook de servicesmodule voor de Azure Maps Web SDK-broncode lokaal laden met behulp van het [npm-pakket azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) en vervolgens hosten met uw app. Dit pakket bevat ook TypeScript-definities. Gebruik deze opdracht:
    
        > **npm install azure-maps-rest**
    
        Voeg vervolgens een scriptverwijzing `<head>` toe naar het element van het bestand:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Maak een verificatiepijplijn. De pijplijn moet worden gemaakt voordat u een eindpunt van de service-URL-client initialiseren. Gebruik uw eigen Azure Maps-accountsleutel of Azure Active Directory-referenties (Azure AD) om een Azure Maps Search-serviceclient te verifiëren. In dit voorbeeld wordt de URL-client zoekservice gemaakt. 

    Als u een abonnementssleutel gebruikt voor verificatie:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Als u Azure AD gebruikt voor verificatie:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Zie [Verificatie met Azure Maps](azure-maps-authentication.md)voor meer informatie.

1. De volgende code gebruikt de nieuw gemaakte URL-client azure maps-zoekservice om een adres te geocoderen: "1 Microsoft Way, Redmond, WA". De code `searchAddress` gebruikt de functie en geeft de resultaten weer als een tabel in de hoofdtekst van de pagina.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Hier is het volledige, lopende codevoorbeeld:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="De servicesmodule gebruiken" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de Pen Die de Services<a href='https://codepen.io/azuremaps'>@azuremaps</a>Module <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>by</a> Azure Maps () gebruikt op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Cloudondersteuning voor Azure Government

De Azure Maps Web SDK ondersteunt de Azure Government-cloud. Alle JavaScript- en CSS-URL's die worden gebruikt om toegang te krijgen tot de Azure Maps Web SDK blijven hetzelfde, maar de volgende taken moeten worden uitgevoerd om verbinding te maken met de Azure Government-cloudversie van het Azure Maps-platform.

Wanneer u het interactieve kaartbesturingselement gebruikt, voegt u `Map` de volgende coderegel toe voordat u een instantie van de klasse maakt. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Zorg ervoor dat u een Azure Maps-verificatiegegevens van het Azure Government-cloudplatform gebruikt wanneer u de kaart en services authenticeren.

Bij het gebruik van de servicesmodule moet het domein voor de services worden ingesteld bij het maken van een instantie van een API-URL-eindpunt. Met de volgende code wordt bijvoorbeeld `SearchURL` een instantie van de klasse gemaakt en wordt het domein naar de Azure Government-cloud getoverd.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Als u rechtstreeks toegang hebt tot de Azure `atlas.azure.us`Maps REST-services, wijzigt u het URL-domein in . Als u bijvoorbeeld de API-service voor zoeken `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`gebruikt, wijzigt u het URL-domein van .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [ZoekURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die de servicesmodule gebruiken:

> [!div class="nextstepaction"]
> [Zoekresultaten weergeven op de kaart](./map-search-location.md)

> [!div class="nextstepaction"]
> [Informatie ophalen uit een coördinaat](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Routebeschrijving van A naar B](./map-route.md)
