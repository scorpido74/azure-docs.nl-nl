---
title: De Creator gebruiken om kaarten voor de binnenste toe te maken
description: Gebruik Azure Maps Maker om kaarten voor de binnenste toe te voegen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 93827e4d5f6bcf66191ae78c18adac71b5dd0a22
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255174"
---
# <a name="use-creator-to-create-indoor-maps"></a>De Creator gebruiken om kaarten voor de binnenste toe te maken

In deze zelf studie leert u hoe u kaarten kunt maken. In deze zelf studie leert u hoe u de API kunt gebruiken voor het volgende:

> [!div class="checklist"]
> * Het toewijzings pakket van de binnenste kaart uploaden
> * Uw tekening pakket converteren naar kaart gegevens
> * Een gegevensset maken op basis van uw kaart gegevens
> * Een tegelset maken op basis van de gegevens in uw gegevensset
> * Een query uitvoeren op de API van Azure Maps Web Feature Service (WFS) voor meer informatie over uw kaart functies
> * Een functie statusset maken met behulp van de kaart functies en de gegevens in uw gegevensset
> * Uw functie statusset bijwerken

## <a name="prerequisites"></a>Vereisten

Kaarten voor een binnenste maken:

1. [Een Azure Maps account maken](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Verkrijg een primaire abonnements sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel de primaire sleutel of de abonnements sleutel genoemd.
3. [Een Creator-resource maken](how-to-manage-creator.md)
4. Down load het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

In deze zelf studie wordt gebruikgemaakt van de toepassing [postman](https://www.postman.com/) , maar u kunt een andere API-ontwikkel omgeving kiezen.

>[!IMPORTANT]
> De API-url's in dit document moeten mogelijk worden aangepast op basis van de locatie van de bron van de maker. Zie [toegang tot Creator-Services](how-to-manage-creator.md#access-to-creator-services)voor meer informatie.

## <a name="upload-a-drawing-package"></a>Een teken pakket uploaden

Gebruik de [Data upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) om het teken pakket te uploaden naar Azure Maps-resources.

De API voor het uploaden van gegevens is een langlopende trans actie die het hier gedefinieerde patroon implementeert. Zodra de bewerking is voltooid, gebruiken we de `udid` om toegang te krijgen tot het geüploade pakket om het te converteren. Volg de onderstaande stappen om de te verkrijgen `udid` .

1. Open de Postman-app. Selecteer in de buurt van de Postman-app de optie **Nieuw**. Selecteer **verzameling**in het venster **Nieuw maken** .  Geef de verzameling een naam en selecteer de knop **maken** .

2. Als u de aanvraag wilt maken, selecteert u **Nieuw** opnieuw. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer een **aanvraag naam** in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer vervolgens **Opslaan**.

3. Selecteer de **post** HTTP-methode op het tabblad Builder en voer de volgende URL in om het teken pakket naar de Azure Maps-service te uploaden. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt `<Azure-Maps-Primary-Subscription-key>` u door de sleutel van uw primaire abonnement.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Geef op het tabblad **headers** een waarde op voor de `Content-Type` sleutel. Het teken pakket is een gezipte map, dus gebruikt u de `application/octet-stream` waarde. Selecteer **binaire bestanden**op het tabblad **hoofd tekst** . Klik op **bestand selecteren** en kies een teken pakket.

     ![gegevens beheer](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Klik op de knop voor de blauwe **verzen ding** en wacht totdat de aanvraag is verwerkt. Zodra de aanvraag is voltooid, gaat u naar het tabblad **headers** van het antwoord. Kopieer de waarde van de **locatie** sleutel, die de bevat `status URL` .

6. Als u de status van de API-aanroep wilt controleren, maakt u een **Get** HTTP-aanvraag op de `status URL` . U moet uw primaire abonnements sleutel toevoegen aan de URL voor authenticatie. De **Get** -aanvraag moet eruitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/mapData/operations/{operationId}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Wanneer de **Get** HTTP-aanvraag is voltooid, wordt een geretourneerd `resourceLocation` . De `resourceLocation` bevat de unieke `udid` voor de geüploade inhoud. U kunt eventueel de `resourceLocation` URL gebruiken om meta gegevens op te halen uit deze resource in de volgende stap.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. Als u meta gegevens van inhoud wilt ophalen, maakt u een **Get** HTTP-aanvraag op de `resourceLocation` URL die is opgehaald in stap 7. Zorg ervoor dat u uw primaire abonnements sleutel aan de URL voor verificatie toevoegt. De **Get** -aanvraag moet eruitzien als de volgende URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Wanneer de **Get** HTTP-aanvraag is voltooid, bevat de antwoord tekst de `udid` opgegeven waarde in `resourceLocation` stap 7, de locatie voor het openen/downloaden van de inhoud in de toekomst en enkele andere meta gegevens over de inhoud zoals gemaakt/bijgewerkt op datum, grootte, enzovoort. Een voor beeld van het totale antwoord is:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Een teken pakket converteren

 Nu het teken pakket is geüpload, wordt het `udid` geüploade pakket gebruikt voor het converteren van het pakket naar kaart gegevens. De conversie-API gebruikt een langlopende trans actie die het [hier](creator-long-running-operation.md)gedefinieerde patroon implementeert. Zodra de bewerking is voltooid, gebruiken we de `conversionId` om toegang te krijgen tot de geconverteerde gegevens. Volg de onderstaande stappen om de te verkrijgen `conversionId` .

1. Selecteer **Nieuw**. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer de **naam** van een aanvraag in en selecteer een verzameling. Klik op **Opslaan**.

2. Selecteer de **post** HTTP-methode op het tabblad opbouw functie en voer de volgende URL in om uw geüploade tekening pakket te converteren naar kaart gegevens. Gebruik de `udid` voor het geüploade pakket.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

3. Klik op de knop **verzenden** en wacht totdat de aanvraag is verwerkt. Zodra de aanvraag is voltooid, gaat u naar het tabblad **headers** van het antwoord en zoekt u naar de **locatie** sleutel. Kopieer de waarde van de **locatie** sleutel, die de `status URL` voor de conversie aanvraag is.

4. Start een nieuwe HTTP-methode **ophalen** op het tabblad opbouw functie. voeg uw Azure Maps primaire abonnements sleutel toe aan de `status URL` . Maak een **Get** -aanvraag met de `status URL` van de vorige stap. Als het conversie proces nog niet is voltooid, ziet u mogelijk iets zoals het volgende JSON-antwoord:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Zodra de aanvraag is voltooid, ziet u een geslaagd status bericht in de hoofd tekst van het antwoord.  Kopieer de `conversionId` van de `resourceLocation` URL voor het geconverteerde pakket. De `conversionId` wordt gebruikt door de andere API om toegang te krijgen tot de geconverteerde kaart gegevens.

    ```json
   {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>De Postman-toepassing biedt geen systeem eigen ondersteuning voor langlopende HTTP-aanvragen. Als gevolg hiervan kan een lange vertraging optreden bij het maken van een **Get** -aanvraag op de status-URL.  Wacht ongeveer dertig seconden en probeer opnieuw te klikken op de knop **verzenden** totdat het antwoord slaagt of mislukt.

Het voorbeeld tekening pakket moet worden geconverteerd zonder fouten of waarschuwingen. Als u echter fouten of waarschuwingen van uw eigen teken pakket ontvangt, geeft het JSON-antwoord u een koppeling naar de [visualer](drawing-error-visualizer.md)voor het tekenen van fouten. Met de tekening fout visualer kunt u de details van fouten en waarschuwingen inspecteren. Als u aanbevelingen wilt ontvangen over het oplossen van conversie fouten en waarschuwingen, raadpleegt u de [conversie fouten en waarschuwingen](drawing-conversion-error-codes.md)voor de tekening.

```json
{
    "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Een gegevensset maken

De gegevensset is een verzameling kaart functies, zoals gebouwen, niveaus en ruimtes. Gebruik de [DataSet Create API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)om een gegevensset te maken. De DataSet Create API neemt het `conversionId` voor het geconverteerde teken pakket en retourneert een `datasetId` van de gemaakte gegevensset. De volgende stappen laten zien hoe u een gegevensset kunt maken.

1. Selecteer in de toepassing postman de optie **Nieuw**. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer de **naam** van een aanvraag in en selecteer een verzameling. Klik op **Opslaan**.

2. Maak een **post** -aanvraag naar de [gegevensset Create API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) om een nieuwe gegevensset te maken. Voordat u de aanvraag indient, voegt u zowel uw abonnements sleutel als de `conversionId` met de `conversionId` tijdens het conversie proces opgehaalde stap 5 toe.  De aanvraag moet er ongeveer uitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Verkrijg de `statusURL` in de **locatie** sleutel van de antwoord **headers**.

4. Maak een **Get** -aanvraag op de `statusURL` om de te verkrijgen `datasetId` . Voeg uw Azure Maps primaire abonnements sleutel toe voor authenticatie. De aanvraag moet er ongeveer uitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/dataset/operations/{operationId}?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

5. Wanneer de **Get** HTTP-aanvraag is voltooid, bevat de antwoord header de `datasetId` voor de gemaakte gegevensset. Kopieer de `datasetId` . U moet de gebruiken `datasetId` om een tegelset te maken.

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Een tegelset maken

Een tegelset is een set vector tegels die op de kaart worden weer gegeven. Tilesets worden gemaakt op basis van bestaande gegevens sets. Een tegelset is echter onafhankelijk van de gegevensset waaruit deze is gebrond. Als de gegevensset wordt verwijderd, blijft de tegelset bestaan. Volg de onderstaande stappen om een tegelset te maken:

1. Selecteer in de toepassing postman de optie **Nieuw**. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer de **naam** van een aanvraag in en selecteer een verzameling. Klik op **Opslaan**.

2. Maak een **post** -aanvraag op het tabblad opbouw functie. De aanvraag-URL moet eruitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Maak een **Get** -aanvraag op de `statusURL` voor de tegelset. Voeg uw Azure Maps primaire abonnements sleutel toe voor authenticatie. De aanvraag moet er ongeveer uitzien als de volgende URL:

   ```http
    https://atlas.microsoft.com/tileset/operations/{operationId}?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

4. Wanneer de **Get** HTTP-aanvraag is voltooid, bevat de antwoord header de `tilesetId` voor de gemaakte tegelset. Kopieer de `tilesetId` .

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Gegevens sets opvragen met WFS-API

 Gegevens sets kunnen worden opgevraagd met behulp van de [WFS-API](https://docs.microsoft.com/rest/api/maps/wfs). Met de WFS-API kunt u een query uitvoeren voor functie verzamelingen, een specifieke verzameling of een specifieke functie met een functie **-id**. De functie **-id** is een unieke aanduiding van de functie binnen de gegevensset. Het wordt gebruikt om bijvoorbeeld te identificeren welke onderdeel status in een bepaalde statusset moet worden bijgewerkt.

1. Selecteer in de toepassing postman de optie **Nieuw**. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer de **naam** van een aanvraag in en selecteer een verzameling. Klik op **Opslaan**.

2. Maak een **Get** -aanvraag om een lijst van de verzamelingen in uw gegevensset weer te geven. Vervang door `<dataset-id>` uw `datasetId` . Gebruik uw Azure Maps primaire sleutel in plaats van de tijdelijke aanduiding. De aanvraag moet er ongeveer uitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. De antwoord tekst wordt geleverd in geojson-indeling en bevat alle verzamelingen in de gegevensset. In dit voor beeld wordt alleen de verzameling weer gegeven `unit` . Zie WFS voor meer informatie over het [beschrijven van verzamelingen API](https://docs.microsoft.com/rest/api/maps/wfs/collectiondescriptionpreview)voor een voor beeld dat alle verzamelingen bevat. Als u meer wilt weten over een verzameling, kunt u klikken op een van de Url's in het `link` element.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Een **Get** -aanvraag voor de `unit` functie verzamelingen maken.  Vervang door `{datasetId}` uw `datasetId` . Gebruik uw Azure Maps primaire sleutel in plaats van de tijdelijke aanduiding. De antwoord tekst bevat alle functies van de `unit` verzameling. De aanvraag moet er ongeveer uitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Kopieer de functie `id` van een eenheids functie met stijl eigenschappen die dynamisch kunnen worden gewijzigd.  Omdat de status van de bezetting van de eenheid en de Tempe ratuur dynamisch kunnen worden bijgewerkt, gebruiken we deze functie `id` in de volgende sectie. In het volgende voor beeld is de functie `id` ' UNIT26 '. We verwijzen naar de stijl eigenschappen van deze functie als status en we gebruiken de functie om een statusset te maken.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Een functie statusset maken

1. Selecteer in de toepassing postman de optie **Nieuw**. Selecteer **aanvraag**in het venster **Nieuw maken** . Voer de **naam** van een aanvraag in en selecteer een verzameling. Klik op **Opslaan**.

2. Maak een **post** -aanvraag naar de API voor het maken van de [statusset](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview). Gebruik de `datasetId` van de gegevensset die de status bevat die u wilt wijzigen. De aanvraag moet er ongeveer uitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Stel in de **kopteksten** van de **post** -aanvraag in `Content-Type` op `application/json` . Geef in de **hoofd tekst**de onderstaande stijlen op om wijzigingen aan te brengen in de `occupied` `temperature` *statussen*en. Wanneer u klaar bent, klikt u op **verzenden**.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Kopieer de `statesetId` van de hoofd tekst van het antwoord.

5. Maak een **post** -aanvraag om de status bij te werken: Geef de statesetId en de functie door `ID` met uw Azure Maps-abonnements sleutel. De aanvraag moet er ongeveer uitzien als de volgende URL:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Stel in de **kopteksten** van de **post** -aanvraag in `Content-Type` op `application/json` . In de **hoofd tekst** van de aanvraag **post** kopieert en plakt u de json in het onderstaande voor beeld.

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

    >[!NOTE]
    > De update wordt alleen opgeslagen als de stempel na het tijds tempel van de vorige aanvraag is geplaatst. We kunnen alle namen door geven die we eerder hebben geconfigureerd tijdens het maken.

7. Wanneer een update is voltooid, ontvangt u een `200 OK` HTTP-status code. Als u [dynamische opmaak hebt geïmplementeerd](indoor-map-dynamic-styling.md) voor een binnenste kaart, wordt de update weer gegeven in de gerenderde kaart op de opgegeven tijds tempel.

Met de [functie Get statuss van API](https://docs.microsoft.com/rest/api/maps/featurestate/getstatespreview) kunt u de status van een functie ophalen met behulp van de functie `ID` . U kunt ook de statusset en de bijbehorende resources verwijderen met behulp van de API voor het verwijderen van de [functie status](https://docs.microsoft.com/rest/api/maps/featurestate/deletestatesetpreview).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Het toewijzings pakket van de binnenste kaart uploaden
> * Uw tekening pakket converteren naar kaart gegevens
> * Een gegevensset maken op basis van uw kaart gegevens
> * Een tegelset maken op basis van de gegevens in uw gegevensset
> * Query's uitvoeren op de Azure Maps WFS-service voor meer informatie over uw kaart functies
> * Een functie statusset maken met behulp van de kaart functies en de gegevens in uw gegevensset
> * Uw functie statusset bijwerken

U bent nu voorzien van de vaardig heden die u nodig hebt om door te gaan naar de volgende hand leidingen:

> [!div class="nextstepaction"]
> [De module binnenste kaarten gebruiken](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Dynamische opmaak voor kaarten in de binnenste implementeren](indoor-map-dynamic-styling.md)

Meer informatie over de verschillende Azure Maps services die in dit artikel worden besproken:

> [!div class="nextstepaction"]
> [Gegevens uploaden](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Gegevens conversie](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Gegevensset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tegelset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Functie status ingesteld](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS-service](creator-indoor-maps.md#web-feature-service-api)
