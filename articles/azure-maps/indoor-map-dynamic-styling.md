---
title: Dynamische stijlen implementeren voor de kaarten van Azure Maps Maker binnenshuis
description: Meer informatie over het implementeren van dynamische opmaak voor plattegronden in de maker
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6edc114a2d69dfe8f1e6e5d3c0a2d4af26dbad67
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598771"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Dynamische stijlen voor het maken van kaarten in de maker implementeren

Azure Maps- [functie status service](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate) maakt het mogelijk om stijlen toe te passen op basis van de dynamische eigenschappen van de functies van de gegevens van de binnenste kaart.  U kunt bijvoorbeeld faciliteit Vergader zalen met een specifieke kleur weer geven om de status van bezetting weer te geven. In dit artikel laten we u zien hoe u met behulp van de [functie status service](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate) en de [WEBMODULE](how-to-use-indoor-module.md)over een dynamisch overzicht kunt weer geven van de toewijzings functies.

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Verkrijg een primaire abonnements sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel de primaire sleutel of de abonnements sleutel genoemd.
3. [Een Creator-resource maken](how-to-manage-creator.md)
4. Down load het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Maak een kaart voor binnenste](tutorial-creator-indoor-maps.md) om een en te verkrijgen `tilesetId` `statesetId` .
6. Bouw een webtoepassing met behulp van de stappen in [de module voor de binnenste kaart gebruiken](how-to-use-indoor-module.md).

In deze zelf studie wordt gebruikgemaakt van de toepassing [postman](https://www.postman.com/) , maar u kunt een andere API-ontwikkel omgeving kiezen.

## <a name="implement-dynamic-styling"></a>Dynamische opmaak implementeren

Wanneer u de vereisten hebt voltooid, moet u een eenvoudige webtoepassing hebben die is geconfigureerd met uw abonnements sleutel, `tilesetId` en `statesetId` .

### <a name="select-features"></a>Kenmerken selecteren

Voor het implementeren van dynamische vormgeving moet er naar een functie, zoals een vergadering of Vergader Zaal, worden verwezen met de functie `id` . U gebruikt de functie `id` om de dynamische eigenschap of de *status* van de functie bij te werken. U kunt een van de volgende methoden gebruiken om de functies weer te geven die in een gegevensset zijn gedefinieerd:

* WFS-API (web functie Service). Gegevens sets kunnen worden opgevraagd met behulp van de WFS-API. WFS volgt de Open Geospatial Consortium API-functies. De WFS-API is handig voor het uitvoeren van query's in functies in een gegevensset. U kunt WFS bijvoorbeeld gebruiken om alle middel grote Vergader zalen van een bepaalde faciliteit en een basis niveau te vinden.

* Implementeer aangepaste code waarmee een gebruiker functies op een kaart kan selecteren met behulp van uw webtoepassing. In dit artikel gaan we gebruikmaken van deze optie.  

Met het volgende script wordt de gebeurtenis muis klikken geïmplementeerd. Met de code wordt de functie opgehaald op `id` basis van het geklikte punt. In uw toepassing kunt u de code onder uw manager-code blok invoegen. Voer de toepassing uit en controleer de-console om de functie `id` van het geklikte punt te verkrijgen.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor")

    var result = features.reduce(function (ids, feature) {
        if (feature.layer.id == "indoor_unit_office") {
            console.log(feature);
        }
    }, []);
});
```

In de zelf studie [een binnenste kaart maken](tutorial-creator-indoor-maps.md) hebt u de functie statusset geconfigureerd om status updates voor te accepteren `occupancy` .

In de volgende sectie wordt de bezettings *status* van Office ingesteld `UNIT26` op `true` . terwijl Office `UNIT27` wordt ingesteld op `false` .

### <a name="set-occupancy-status"></a>Status van bezetting instellen

 We gaan nu de status van de twee kant oren bijwerken `UNIT26` en `UNIT27` :

1. Selecteer in de toepassing postman de optie **Nieuw**. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer de **naam** van een aanvraag in en selecteer een verzameling. Klik op **Opslaan**

2. Gebruik de [API voor de functie-update status](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview) om de status bij te werken. Geef de statusset-ID en `UNIT26` een van de twee eenheden door. Voeg uw Azure Maps-abonnements sleutel toe. Dit is de URL van een **post** -aanvraag om de status bij te werken:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Stel in de **kopteksten** van de **post** -aanvraag in `Content-Type` op `application/json` . In de **hoofd tekst** van de **post** -aanvraag schrijft u de volgende JSON met de functie-updates. De update wordt alleen opgeslagen als de tijds tempel na het tijds tempel dat is gebruikt in de vorige functie status update aanvragen voor dezelfde functie is `ID` . Geef de ' bezette ' door `keyName` om de waarde bij te werken.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. Voer stap 2 en 3 opnieuw uit met behulp `UNIT27` van de volgende JSON.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Dynamische stijlen op een kaart visualiseren

De webtoepassing die u eerder hebt geopend in een browser, moet nu de bijgewerkte status van de kaart functies weer geven. `UNIT27`(151) moet groen worden weer gegeven en `UNIT26` (157) moet rood worden weer gegeven.

![Beschik bare ruimte in groen en bezette kamer rood](./media/indoor-map-dynamic-styling/room-state.png)

## <a name="next-steps"></a>Volgende stappen

Lees voor meer informatie:

> [!div class="nextstepaction"]
> [Koppeling voor binnenste toewijzing](creator-indoor-maps.md)

Zie de naslag informatie voor de Api's die in dit artikel worden vermeld:

> [!div class="nextstepaction"]
> [Gegevens uploaden](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Gegevens conversie](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Sets](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tegelset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Functie status ingesteld](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS-service](creator-indoor-maps.md#web-feature-service-api)

