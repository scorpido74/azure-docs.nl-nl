---
title: Aangepaste gegevens weergeven op een rasterkaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u aangepaste gegevens op een rasterkaart weergeeft met behulp van Microsoft Azure Maps static image Service.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335518"
---
# <a name="render-custom-data-on-a-raster-map"></a>Aangepaste gegevens weergeven op een rasterkaart

In dit artikel wordt uitgelegd hoe u de [statische beeldservice](https://docs.microsoft.com/rest/api/maps/render/getmapimage), met functionaliteit voor beeldcompositie, gebruiken om overlays boven op een rasterkaart toe te staan. Afbeeldingssamenstelling omvat de mogelijkheid om een rastertegel terug te krijgen, met extra gegevens zoals aangepaste punaises, labels en geometrieoverlays.

Als u aangepaste punaises, labels en geometrieoverlays wilt renderen, u de toepassing Postman gebruiken. U [API's](https://docs.microsoft.com/rest/api/maps/data) van Azure Maps Data Service gebruiken om overlays op te slaan en weer te geven.

> [!Tip]
> Het is vaak veel rendabeler om de Azure Maps Web SDK te gebruiken om een eenvoudige kaart op een webpagina weer te geven dan om de statische beeldservice te gebruiken. De web SDK maakt gebruik van kaarttegels en tenzij de gebruiker de kaart pannen en zoomt, genereren ze vaak slechts een fractie van een transactie per kaartbelasting. Houd er rekening mee dat de Azure Maps web SDK opties heeft voor het uitschakelen van pannen en zoomen. Bovendien biedt de Azure Maps web SDK een uitgebreidere set gegevensvisualisatieopties dan een statische kaartwebservice.  

## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken

Als u de procedures in dit artikel wilt voltooien, moet u eerst een Azure Maps-account maken en uw kaartaccountsleutel oppakken. Volg instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-accountabonnement te maken en volg de stappen in de primaire [sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te krijgen. Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Punaises renderen met labels en een aangepaste afbeelding

> [!Note]
> De procedure in deze sectie vereist een Azure Maps-account in prijscategorie S0 of S1.

De s0-laag Azure Maps-account ondersteunt `pins` slechts één exemplaar van de parameter. Hiermee u maximaal vijf punaises, opgegeven in het URL-verzoek, weergeven met een aangepaste afbeelding.

Voer de volgende stappen uit om punaises weer te geven met labels en een aangepaste afbeelding:

1. Maak een verzameling waarin u de aanvragen opslaan. Selecteer in de Postman-app **Nieuw**. Selecteer **Verzameling** in het **Collection**venster Nieuw maken . Geef de verzameling een naam en selecteer de knop **Maken.** 

2. Als u de aanvraag wilt maken, selecteert u Opnieuw **nieuw.** Selecteer **Verzoek in**het venster **Nieuw maken** . Voer een **aanvraagnaam** in voor de punaises. Selecteer de verzameling die u in de vorige stap hebt gemaakt, als de locatie om de aanvraag op te slaan. Selecteer vervolgens **Opslaan**.
    
    ![Een aanvraag maken in Postbode](./media/how-to-render-custom-data/postman-new.png)

3. Selecteer de METHODE HTTP OPHALEN op het tabblad Bouwer en voer de volgende URL in om een GET-aanvraag te maken.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Hier is de resulterende afbeelding:

    ![Een aangepaste punaise met een label](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Gegevens ophalen uit azure maps-gegevensopslag

> [!Note]
> De procedure in deze sectie vereist een Azure Maps-account in prijscategorie S1.

U ook het pad verkrijgen en locatiegegevens vastmaken met behulp van de [API voor het uploaden](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)van gegevens. Volg de onderstaande stappen om de gegevens van het pad en de pinnen te uploaden.

1. Open in de Postman-app een nieuw tabblad in de verzameling die u in de vorige sectie hebt gemaakt. Selecteer de methode HTTP-bericht op het tabblad Bouwer en voer de volgende URL in om een POST-verzoek in te dienen:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Voer op het tabblad **Params** de volgende sleutel-/waardeparen in, die worden gebruikt voor de URL van de POST-aanvraag. Vervang `subscription-key` de waarde door uw Azure Maps-abonnementssleutel.
    
    ![Sleutel/waarde params in Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Selecteer op het tabblad **Hoofdtekst** de ruwe invoerindeling en kies JSON als invoerindeling in de vervolgkeuzelijst. Geef deze JSON als te uploaden gegevens:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Selecteer **Verzenden** en controleer de antwoordkop. Op een geslaagd verzoek bevat de koplocatie de status URI om de huidige status van de uploadaanvraag te controleren. De status URI zou van de volgende indeling zijn.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Kopieer uw status URI en sluit de parameter met de abonnementssleutel toe met de waarde van uw Azure Maps-accountabonnementssleutel. Gebruik dezelfde accountabonnementssleutel die u hebt gebruikt om de gegevens te uploaden. De status URI-indeling moet er uitzien als de onderstaande:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Open een nieuw tabblad in de Postman-app om de udId te krijgen. Selecteer DE METHODE HTTP DOWNLOADEN op het tabblad Bouwer. Een GET-aanvraag indienen bij de status URI. Als het uploaden van uw gegevens succesvol is, ontvangt u een udId in de reactieinstantie. Kopieer de udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Gebruik `udId` de waarde die is ontvangen van de API voor het uploaden van gegevens om functies op de kaart weer te geven. Open hiervoor een nieuw tabblad in de verzameling die u in de vorige sectie hebt gemaakt. Selecteer de METHODE HTTP OPHALEN op het tabblad Bouwer, vervang de {subscription-key} en {udId} door uw waarden en voer deze URL in om een GET-verzoek in te dienen:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Hier is de reactie afbeelding:

    ![Gegevens ophalen uit azure maps-gegevensopslag](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Een veelhoek met kleur en dekking weergeven

> [!Note]
> De procedure in deze sectie vereist een Azure Maps-account in prijscategorie S1.


U de weergave van een veelhoek wijzigen door stijlmodifiers te gebruiken met de [parameter pad](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Open in de Postman-app een nieuw tabblad in de verzameling die u eerder hebt gemaakt. Selecteer de METHODE HTTP OPHALEN op het tabblad Bouwer en voer de volgende URL in om een GET-aanvraag te configureren om een veelhoek met kleur en dekking weer te geven:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Hier is de reactie afbeelding:

    ![Een ondoorzichtige veelhoek weergeven](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Een cirkel en punaises weergeven met aangepaste labels

> [!Note]
> De procedure in deze sectie vereist een Azure Maps-account in prijscategorie S1.


U het uiterlijk van de pinnen wijzigen door stijlmodifiers toe te voegen. Als u bijvoorbeeld punaises en hun labels `sc` groter of kleiner wilt maken, gebruikt u de modifier 'schaalstijl'. Deze modifier heeft een waarde die groter is dan nul. Een waarde van 1 is de standaardschaal. Waarden groter dan 1 maken de pinnen groter en waarden kleiner dan 1 maken ze kleiner. Zie [statische beeldservicepadparameters](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)voor meer informatie over stijlmodifiers .


Volg deze stappen om een cirkel en punaises weer te geven met aangepaste labels:

1. Open in de Postman-app een nieuw tabblad in de verzameling die u eerder hebt gemaakt. Selecteer de METHODE HTTP OPHALEN op het tabblad Bouwer en voer deze URL in om een GET-verzoek in te dienen:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Hier is de reactie afbeelding:

    ![Een cirkel weergeven met aangepaste punaises](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Als u de kleur van de punaises van de laatste stap wilt wijzigen, wijzigt u de modifier 'co'-stijl. Kijk `pins=default|la15+50|al0.66|lc003C62|co002D62|`naar , de huidige kleur zou worden opgegeven als #002D62 in CSS. Stel dat je het wilt veranderen in #41d42a. Schrijf de nieuwe kleurwaarde na de "co" specificer, zoals deze: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Doe een nieuw GET-verzoek:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Hier is de reactieafbeelding na het wijzigen van de kleuren van de pinnen:

    ![Een cirkel weergeven met bijgewerkte punaises](./media/how-to-render-custom-data/circle-updated-pins.png)

U ook andere stijlmodifiers wijzigen, toevoegen en verwijderen.

## <a name="next-steps"></a>Volgende stappen


* Ontdek de [Azure Maps Get Map Image API-documentatie.](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* Zie de [servicedocumentatie](https://docs.microsoft.com/rest/api/maps/data)voor meer informatie over Azure Maps Data Service.

