---
title: Aan de slag met webkaartbesturingselement | Microsoft Azure Maps
description: Meer informatie over het gebruik van de JavaScript-bibliotheek aan de clientzijde van Microsoft Azure Maps-kaartbeheer om kaarten en ingesloten Azure Maps-functionaliteit weer te geven in uw web- of mobiele toepassing.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335235"
---
# <a name="use-the-azure-maps-map-control"></a>Het kaartbesturingselement voor Azure Maps gebruiken

Met de JavaScript-bibliotheek aan de clientzijde van Map Control u kaarten en ingesloten Azure Maps-functionaliteit weergeven in uw web- of mobiele toepassing.

## <a name="create-a-new-map-in-a-web-page"></a>Een nieuwe kaart maken op een webpagina

U een kaart insluiten in een webpagina met behulp van de JavaScript-bibliotheek aan de clientzijde van Map Control.

1. Een nieuw HTML-bestand maken.

2. Laden in de Azure Maps Web SDK. U een van de twee opties kiezen;

    * Gebruik de wereldwijd gehoste CDN-versie van de Azure Maps Web SDK `<head>` door verwijzingen toe te voegen naar het JavaScript en het stylesheet in het element van het HTML-bestand:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Laad de Azure Maps Web SDK-broncode lokaal met behulp van het [NPM-pakket Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) en host deze met uw app. Dit pakket bevat ook TypeScript-definities.

        > **npm installeert azure-maps-control**

       Voeg vervolgens verwijzingen toe naar het Azure Maps-stijlblad en verwijzingen naar de scriptbron naar het `<head>` element van het bestand:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Typescriptdefinities kunnen in uw toepassing worden geïmporteerd door de volgende code toe te voegen:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Als u de kaart zo wilt weergeven dat deze `<style>` de `<head>` volledige hoofdtekst van de pagina vult, voegt u het volgende element toe aan het element.

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

4. Voeg in de hoofdtekst `<div>` van de pagina `id` een element toe en geef het een **myMap.**

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Als u het kaartbesturingselement wilt initialiseren, definieert u een nieuwe scripttag in de html-hoofdtekst. Geef de `id` kaart `<div>` of `HTMLElement` een `document.getElementById('myMap')`(bijvoorbeeld) als eerste parameter door bij `Map` het maken van een instantie van de klasse. Gebruik uw eigen Azure Maps-accountsleutel of Aad-referenties (Azure Active Directory) om de kaart te verifiëren met behulp van [verificatieopties.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions) 

   Als u een account wilt aanmaken of uw sleutel wilt vinden, volgt u de instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) en krijgt u de primaire [sleutel.](quick-demo-map-app.md#get-the-primary-key-for-your-account) 

   De **taaloptie** geeft de taal op die moet worden gebruikt voor kaartlabels en besturingselementen. Zie ondersteunde talen voor meer informatie over ondersteunde [talen.](supported-languages.md) Als u een abonnementssleutel gebruikt voor verificatie, gebruikt u het volgende:

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

   Als u Azure Active Directory (AAD) gebruikt voor verificatie, gebruikt u het volgende:

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

   Hier vindt u [een](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)lijst met voorbeelden met informatie over het integreren van Azure Active Directory (AAD) met Azure Maps. 
    
   Zie het document [Verificatie met Azure Maps](azure-maps-authentication.md) en ook de [azure maps Azure AD-verificatievoorbeelden](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)voor meer informatie.

6. Optioneel u het toevoegen van de volgende metatag-elementen aan het hoofd van uw pagina nuttig vinden:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Als u alles in elkaar zet, moet uw HTML-bestand er ongeveer zo uitzien als de volgende code:

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

8. Open het bestand in uw webbrowser en bekijk de gerenderde kaart. Het moet eruit zien als de afbeelding hieronder:

   ![Afbeelding van kaart met weergegeven resultaat](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>De kaart lokaliseren

Azure Maps biedt twee verschillende manieren om de taal en de regionale weergave voor de weergegeven kaart in te stellen. De eerste optie is om deze `atlas` informatie toe te voegen aan de globale naamruimte, wat resulteert in alle instanties voor kaartbeheer in uw app die in gebreke blijven bij deze instellingen. In de volgende plaats de taal frans (fr-FR) en de regionale weergave op "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

De tweede optie is om deze informatie door te geven aan de kaartopties bij het laden van de kaart als volgt:

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
> Met de Web SDK is het mogelijk om meerdere kaartexemplaren op dezelfde pagina te laden met verschillende taal- en regio-instellingen. Bovendien kunnen deze instellingen worden bijgewerkt nadat `setStyle` de kaart wordt geladen met behulp van de functie van de kaart. 

Hier is een voorbeeld van Azure Maps met de taal ingesteld op "fr-FR" en de regionale weergave ingesteld op "Auto".

![Kaartafbeelding met labels in het Frans](./media/how-to-use-map-control/websdk-localization.png)

Een volledige lijst van ondersteunde talen en regionale weergaven wordt [hier](supported-languages.md)gedocumenteerd.

## <a name="azure-government-cloud-support"></a>Cloudondersteuning voor Azure Government

De Azure Maps Web SDK ondersteunt de Azure Government-cloud. Alle JavaScript- en CSS-URL's die worden gebruikt om toegang te krijgen tot de Azure Maps Web SDK blijven hetzelfde. De volgende taken moeten worden uitgevoerd om verbinding te maken met de Azure Government-cloudversie van het Azure Maps-platform.

Wanneer u het interactieve kaartbesturingselement gebruikt, voegt u `Map` de volgende coderegel toe voordat u een instantie van de klasse maakt. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Zorg ervoor dat u azure maps-verificatiegegevens van het Azure Government-cloudplatform gebruikt wanneer u de kaart en services authenticeren.

Bij het gebruik van de servicesmodule moet het domein voor de services worden ingesteld bij het maken van een instantie van een API-URL-eindpunt. Met de volgende code wordt bijvoorbeeld `SearchURL` een instantie van de klasse gemaakt en wordt het domein naar de Azure Government-cloud getoverd.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Als u rechtstreeks toegang hebt tot de Azure `atlas.azure.us`Maps REST-services, wijzigt u het URL-domein in . Als u bijvoorbeeld de API-service voor zoeken `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`gebruikt, wijzigt u het URL-domein van .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken en ermee omgaan met een kaart:

> [!div class="nextstepaction"]
> [Een kaart maken](map-create.md)

Meer informatie over het stylen van een kaart:

> [!div class="nextstepaction"]
> [Een kaartstijl kiezen](choose-map-style.md)

Ga als u meer gegevens aan uw kaart toevoegen:

> [!div class="nextstepaction"]
> [Een kaart maken](map-create.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Zie voor een lijst met voorbeelden die laten zien hoe azure Active Directory (AAD) worden geïntegreerd met Azure Maps:

> [!div class="nextstepaction"]
> [Azure AD-verificatievoorbeelden](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
