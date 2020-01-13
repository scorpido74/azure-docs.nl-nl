---
title: Aan de slag met het besturings element web map | Microsoft Azure kaarten
description: Meer informatie over het gebruik van de Microsoft Azure Maps map-beheer de Java script-bibliotheek aan de client zijde om kaarten en Inge sloten Azure Maps functionaliteit in uw web-of mobiele toepassing weer te geven.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: d50b97ad47f4b09ae4fec363e7d0a2c7f6b841df
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911344"
---
# <a name="use-the-azure-maps-map-control"></a>Het Azure Maps kaart besturings element gebruiken

Met de Map Control java script-bibliotheek aan de client zijde kunt u kaarten en Inge sloten Azure Maps functionaliteit weer geven in uw web-of mobiele toepassing.

## <a name="create-a-new-map-in-a-web-page"></a>Een nieuwe kaart maken op een webpagina

U kunt een kaart insluiten in een webpagina met behulp van de Map Control java script-bibliotheek aan de client zijde.

1. Maak een nieuw HTML-bestand.

2. Laden in de Azure Maps Web-SDK. U kunt dit doen met een van de twee opties;

    a. Gebruik de wereld wijd gehoste CDN-versie van de Azure Maps Web-SDK door de URL-eind punten toe te voegen aan het opmaak model en de script verwijzingen in het `<head>` element van het bestand:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. U kunt ook de Azure Maps Web SDK-bron code lokaal laden met behulp van het [Azure-Maps-Control NPM-](https://www.npmjs.com/package/azure-maps-control) pakket en deze hosten met uw app. Dit pakket bevat ook type script definities.

    > **NPM Azure-Maps-beheer installeren**

    Voeg vervolgens verwijzingen naar het Azure Maps opmaak model en script bron verwijzingen toe naar het `<head>` element van het bestand:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
    ```

    >[!Note]
    > Type script definities kunnen worden geïmporteerd in uw toepassing door het volgende toe te voegen:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Als u de kaart zo wilt genereren dat de volledige hoofd tekst van de pagina wordt gevuld, voegt u het volgende `<style>`-element toe aan het element `<head>`.

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. Voeg in de hoofd tekst van de pagina een `<div>` element toe en geef het een `id` van **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Als u het kaart besturings element wilt initialiseren, definieert u een nieuwe sectie in de HTML-hoofd tekst en maakt u een script. Geef de `id` van de kaart `<div>` of een `HTMLElement` (bijvoorbeeld `document.getElementById('myMap')`) als de eerste para meter bij het maken van een instantie van de `Map`-klasse. Gebruik uw eigen Azure Maps account sleutel of certificerings Azure Active Directory (AAD) om de kaart te verifiëren met behulp van [verificatie opties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Als u een account wilt maken of uw sleutel wilt vinden, volgt u de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) voor het maken van een abonnement op een Azure Maps-account en volgt u de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account op te halen. De **taal** optie geeft u de taal op die moet worden gebruikt voor labels en besturings elementen toewijzen. Zie [ondersteunde talen](supported-languages.md)voor meer informatie over ondersteunde talen. Als u een abonnements sleutel gebruikt voor verificatie.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Als u Azure Active Directory (AAD) gebruikt voor verificatie:

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
    ```

    [Hier](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)vindt u een lijst met voor beelden van de integratie van Azure Active Directory (Aad) met Azure Maps. 
    
    Zie voor meer informatie de [verificatie met Azure Maps](azure-maps-authentication.md) document en ook de voor [beelden van de Azure Maps voor Azure AD-verificatie](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. U kunt eventueel de volgende metatag elementen toevoegen aan het hoofd van de pagina:

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Het samen stellen van uw HTML-bestand moet er ongeveer als volgt uitzien:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Open het bestand in uw webbrowser en Bekijk de weer gegeven kaart. Het moet er ongeveer uitzien als de volgende code:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Het kaart besturings element gebruiken" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Zie de pen <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>How to use the map control</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>voor meer informatie.
    </iframe>

## <a name="localizing-the-map"></a>De kaart lokaliseren

Azure Maps biedt twee verschillende manieren om de taal en de regionale weer gave van de kaart in te stellen. De eerste optie is om deze informatie toe te voegen aan de naam ruimte van de globale `atlas`, wat resulteert in alle instanties van de kaart besturing in uw app die standaard worden ingesteld op deze instellingen. Hiermee stelt u de taal in op Frans ("fr-FR") en de regionale weer gave op ' auto ':

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

De tweede optie is om deze informatie door te geven aan de kaart opties bij het laden van de kaart zoals:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Met de Web-SDK is het mogelijk meerdere toewijzings exemplaren op dezelfde pagina te laden met andere taal-en regio-instellingen. Deze instellingen kunnen bovendien worden bijgewerkt nadat de kaart is geladen met behulp van de `setStyle` functie van de kaart. 

Hier volgt een voor beeld van Azure Maps waarbij de taal is ingesteld op "fr-FR" en de regionale weer gave ingesteld op ' auto '.

![Kaart afbeelding met labels in het Frans](./media/how-to-use-map-control/websdk-localization.png)

Een volledige lijst met ondersteunde talen en regionale weer gaven wordt [hier](supported-languages.md)beschreven.

## <a name="azure-government-cloud-support"></a>Cloud ondersteuning Azure Government

De Azure Maps Web-SDK ondersteunt de Azure Government Cloud. Alle Java script-en CSS-Url's die worden gebruikt voor toegang tot de Azure Maps Web-SDK blijven hetzelfde, maar de volgende taken moeten worden uitgevoerd om verbinding te maken met de Azure Government Cloud versie van het Azure Maps platform.

Wanneer u het besturings element interactieve map gebruikt, voegt u de volgende regel code toe voordat u een instantie van de klasse `Map` maakt. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Zorg ervoor dat u een Azure Maps verificatie gegevens van het Azure Government Cloud platform gebruikt wanneer u de kaart en services verifieert.

Wanneer u de Services-module gebruikt, moet het domein voor de services worden ingesteld bij het maken van een exemplaar van een API-URL-eind punt. Met de volgende code wordt bijvoorbeeld een instantie van de klasse `SearchURL` gemaakt en wordt het domein naar de Azure Government Cloud gewijst.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Als u rechtstreeks toegang hebt tot de Azure Maps REST-services, wijzigt u het URL-domein in `atlas.azure.us`. Als u bijvoorbeeld de Search API-service gebruikt, wijzigt u het URL-domein van `https://atlas.microsoft.com/search/` naar `https://atlas.azure.us/search/`.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken en gebruiken van een kaart:

> [!div class="nextstepaction"]
> [Een kaart maken](map-create.md)

Meer informatie over het opmaken van een kaart:

> [!div class="nextstepaction"]
> [Een kaart stijl kiezen](choose-map-style.md)

Meer gegevens toevoegen aan uw kaart:

> [!div class="nextstepaction"]
> [Een kaart maken](map-create.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Zie voor een lijst met voor beelden die laten zien hoe u Azure Active Directory (AAD) integreert met Azure Maps:

> [!div class="nextstepaction"]
> [Voor beelden van Azure AD-verificatie](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)