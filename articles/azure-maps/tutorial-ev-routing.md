---
title: Elektrische voertuig routering met behulp van Azure Notebooks (python) | Microsoft Docs
description: EV-route ring met Azure Maps routerings-Api's en Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 30751bebd397b378924453987462c9e2b3b55ebf
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803927"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Elektriciteits wagen routering met behulp van Azure Notebooks (python)

Azure Maps is een port Folio met georuimtelijke service-Api's die systeem eigen integreren in azure, waarmee ontwikkel aars, ondernemingen en Isv's locatie bewuste apps en IoT-, mobiliteits-, logistiek-en Asset Tracking-oplossingen kunnen maken. De Azure Maps REST-Api's kunnen worden aangeroepen vanuit talen als python en R voor het inschakelen van georuimtelijke gegevens analyse en machine learning scenario's. Azure Maps biedt een robuuste set [routerings-api's]([https://docs.microsoft.com/rest/api/maps/route) waarmee gebruikers routes tussen verschillende gegevens punten kunnen berekenen op basis van verschillende voor waarden, zoals een type Voer tuig of bereik bare ruimte. In deze zelf studie gaan we een scenario door lopen om een stuur programma voor elektrische Voer tuigen te helpen waarvan het Voer tuig weinig batterij kosten heeft, om het dichtstbijzijnde mogelijke laad station te vinden met betrekking tot drive tijd.

In deze zelf studie doet u het volgende:

> [!div class="checklist"]
> * Een Jupyter Notebook maken en uitvoeren op [Azure notebooks](https://docs.microsoft.com/azure/notebooks) in de Cloud
> * Azure Maps REST-Api's aanroepen in python
> * Zoek een bereik bare reeks op basis van het verbruiks model van het elektriciteits medium.
> * Zoek naar elektrische laad stations voor Voer tuigen binnen het bereik dat kan worden bereikt (of isochrone).
> * De bereik grens en de laad stations van een kaart weer geven.
> * Zoek en visualiseer route naar het dichtstbijzijnde elektrische Voer tuig op basis van de tijd.


## <a name="prerequisites"></a>Vereisten 

Als u de stappen in deze zelf studie wilt volt ooien, moet u eerst een Azure Maps account maken en uw primaire sleutel (abonnements sleutel) ophalen. Volg de instructies in [account beheren](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) om een abonnement voor een Azure Maps account te maken met de prijs categorie S1 en volg de stappen in [primaire sleutel ophalen](./tutorial-search-location.md#getkey) om de primaire abonnements sleutel voor uw account op te halen.

## <a name="create-an-azure-notebook"></a>Een Azure-notebook maken

Als u deze zelf studie wilt volgen, moet u een Azure notebook-project maken en het Jupyter-notebook bestand downloaden en uitvoeren. Het notitieblok bestand bevat de python-code, waarmee het scenario in deze zelf studie wordt geïmplementeerd. Volg de onderstaande stappen voor het maken van een Azure notebook-project en upload het Jupyter-notebook document hierin.

1. Ga naar [Azure notitieblokken](https://notebooks.azure.com) en meld u aan. Zie [Quick](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)start voor meer informatie.
2. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina.

    ![mijn project](./media/tutorial-ev-routing/myproject.png)

3. Op de pagina **Mijn projecten** selecteert u **Nieuw project**.
 
   ![Nieuw project](./media/tutorial-ev-routing/create-project.png)

4. Voer in de pop-up **Nieuw project maken** die wordt weer gegeven de volgende informatie in en klik op **maken**:
    * Projectnaam
    * Project-id
 
    ![Project maken](./media/tutorial-ev-routing/create-project-window.png)

5. Nadat het project is gemaakt, downloadt u het [document bestand Jupyter notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) van de [Azure Maps Jupyter notebook opslag plaats](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Selecteer uw project in de lijst projecten op de pagina **Mijn projecten** en klik op **uploaden** om het document bestand van de Jupyter-notebook te uploaden. Upload het bestand van uw computer en klik op **gereed**.

    ![notitie blok uploaden](./media/tutorial-ev-routing/upload-notebook.png)

7. Wanneer het uploaden is voltooid, ziet u het bestand op de pagina van uw project. Klik op het notitieblok bestand om het te openen als Jupyter Notebook.

Voor een beter begrip van de functionaliteit die in het notitieblok bestand is geïmplementeerd, raden we u aan om de code in het notitie blok één cel tegelijk uit te voeren. U kunt de code in elke cel uitvoeren door te klikken op de knop **uitvoeren** bovenaan in de app notebook.

  ![Uitvoeren](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Project-niveau pakketten installeren

Als u de code in de notebook wilt uitvoeren, moet u pakketten op project niveau installeren. Volg de onderstaande stappen om de vereiste pakketten te installeren:

1. Down load het bestand [Requirements. txt](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) in de [Azure Maps Jupyter notebook opslagplaats](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) en upload het naar uw project.
2. Selecteer in het projectdashboard **projectinstellingen**. 
3. Selecteer in het pop-upvenster dat wordt weer gegeven het **tabblad omgeving**en selecteer vervolgens **toevoegen**.
4. Onder **installatie stappen**voor de omgeving 
    * Kies in de eerste vervolg keuzelijst de optie **Requirements. txt**.
    * Kies in het tweede vervolg keuzemenu het bestand requirements. txt.
    * In de derde vervolg keuzelijst kiest u python versie 3,6 als Python-versie.
7. Selecteer **Opslaan**.

    ![Pakketten installeren](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Vereiste modules en frameworks laden

Voer het volgende script uit om alle vereiste modules en Frameworks te laden.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Aanvraag voor bereik grens bereiken

In ons scenario heeft een bedrijf voor levering van pakketten enkele elektrische Voer tuigen in hun vloot. Gedurende de dag moeten elektrische Voer tuigen worden gefactureerd zonder terug te hoeven naar het magazijn. Elke keer dat de lopende resterende kosten voor het elektriciteits medium minder dan een uur duren (het elektrische Voer tuig heeft weinig kosten), moeten we zoeken naar een set laad stations die binnen het bereik vallen en de grens informatie voor dat bereik ophalen. Omdat het bedrijf liever gebruikmaakt van routes die worden verdeeld door de economie en de snelheid, is de aangevraagde routeType ' Eco '. Met het volgende script wordt de [Get route Range-API](https://docs.microsoft.com/rest/api/maps/route/getrouterange) van de Azure Maps Routing-service met para meters voor het verbruiks model van het Voer tuig aangeroepen en wordt het antwoord geparseerd om een veelhoek object te maken van de geojson-indeling die het maximum bereik van de auto aanduidt .

Voer het script in de onderstaande cel uit om grenzen te krijgen voor het bereik van het elektrische Voer tuig.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Elektrische laad zenders voor het Voer tuig zoeken binnen bereikbaar bereik

Zodra we de bereik bare Range (isochrone) voor het elektrische Voer tuig hebben, kunnen we zoeken naar de oplaad stations in dat bereik. Met het volgende script wordt de Azure Maps [na het zoeken in de geometrie-API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) aangeroepen om te zoeken naar elektrische laad zenders in de grenzen van het maximum bereik van de auto. vervolgens wordt de reactie geparseerd naar een matrix van bereik bare locaties.

Voer het volgende script uit om te zoeken naar elektrische laad zenders binnen het bereik dat kan worden bereikt.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Bereik bare bereiken en laad punten uploaden naar Azure Maps-gegevens service

Om de laad stations en grens waarden voor het maximale bereik van het elektrische medium op de kaart te visualiseren, moeten we de grens gegevens uploaden en gegevens van stations als geojson-objecten naar de Azure Maps-gegevens service laden met behulp van de [Data upload-API ](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Voer de volgende twee cellen uit om de grens-en oplaad punt gegevens te uploaden naar de Azure Maps-gegevens service.

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Opheffings stations en bereik baarheid weer geven op kaart

Zodra de gegevens zijn geüpload naar de gegevens service, voert u nu het volgende script uit om de service voor het Azure Maps [ophalen van toewijzingen](https://docs.microsoft.com/rest/api/maps/render/getmapimage) aan te roepen om de verwerkings punten en de Maxi maal bereik bare grens op de statische kaart afbeelding weer te geven.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![locatie bereik](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Het optimale oplaad station vinden dat u wilt stoppen

Nadat alle mogelijke opheffings stations binnen het bereikbaar zijn, willen we weten dat een van de stations in de minimale tijd kan worden bereikt. Met het volgende script wordt de Azure Maps [matrix routerings-API](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) geretourneerd voor de opgegeven voertuig locatie de reis tijd en de afstand tot elke gegeven locatie van het oplaad station. Het script in de volgende cel, parseert het antwoord op de Get-locatie voor het dichtstbijzijnde bereik dat kan worden bereikt met betrekking tot tijd.

Voer de volgende cel uit om het dichtstbijzijnde bereik bare opheffings station te vinden dat binnen de minimale hoeveelheid tijd kan worden bereikt.

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Route berekenen naar het dichtstbijzijnde oplaad station

Nu we het dichtstbijzijnde oplaad station hebben gevonden, noemen we de [API route richtingen ophalen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) om de gedetailleerde route van de huidige locatie van het elektrische medium naar het oplaad station aan te vragen.

Voer het script uit in de volgende cel om de route op te halen en parser het antwoord om een geojson-object te maken dat de route vertegenwoordigt.

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>De route visualiseren

Om de route te visualiseren, zullen we de route gegevens eerst uploaden als een geojson-object in de Azure Maps-gegevens service met behulp van de Azure Maps [Data upload-API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Daarna roept u de service voor het genereren van [kaarten](https://docs.microsoft.com/rest/api/maps/render/getmapimage) aan om de route op de kaart weer te geven en te visualiseren.

Voer het volgende script uit om een installatie kopie op te halen voor de gerenderde route op de kaart.

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![route](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Azure Maps REST-Api's rechtstreeks aanroept en Azure Maps gegevens visualert met behulp van python.

Zie voor meer informatie Azure Maps Api's die in deze zelf studie worden gebruikt:

* [Route bereik ophalen](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Zoeken in geometrie plaatsen](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Gegevens uploaden](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Afbeelding van de kaart ophalen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Route matrix plaatsen](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Route beschrijving ophalen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Zie voor een volledige lijst met Azure Maps REST-Api's:

* [Azure Maps REST-Api's](https://docs.microsoft.com/azure/azure-maps/#reference)

Voor meer informatie over Azure Notebooks raadpleegt u:

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)