---
title: 'Zelf studie: elektrische Voer tuigen routeren met behulp van Azure Notebooks (python)'
description: "Zelf studie: elektrische Voer tuigen routeren met behulp van Azure Maps routerings-Api's en Azure Notebooks."
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 0f5964472b738bd3868bbf361b85fa4ad1f13b5c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109600"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Zelf studie: elektrische Voer tuigen routeren met behulp van Azure Notebooks (python)

Azure Maps is een port Folio met georuimtelijke service-Api's die systeem eigen zijn geïntegreerd in Azure. Met deze Api's kunnen ontwikkel aars, ondernemingen en Isv's locatie-bewuste apps en IoT-, mobiliteits-, logistiek-en Asset Tracking-oplossingen maken. 

De Azure Maps REST-Api's kunnen worden aangeroepen vanuit talen als python en R voor het inschakelen van georuimtelijke gegevens analyse en machine learning scenario's. Azure Maps biedt een robuuste set [routerings-api's](https://docs.microsoft.com/rest/api/maps/route) waarmee gebruikers routes tussen verschillende gegevens punten kunnen berekenen. De berekeningen zijn gebaseerd op verschillende voor waarden, zoals het type van het Voer tuig of het bereik bare gebied. 

In deze zelf studie gaat u een scenario door lopen om een stuur programma te helpen waarvan de energie kosten van het elektrische Voer tuig laag zijn om het dichtstbijzijnde mogelijke oplaad station te vinden, op basis van de schijf tijd van de locatie van het Voer tuig.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Jupyter-notebook maken en uitvoeren op [Azure notebooks](https://docs.microsoft.com/azure/notebooks) in de Cloud.
> * Roep Azure Maps REST-Api's in python.
> * Zoek een bereik bare reeks op basis van het verbruiks model van het elektriciteits medium.
> * Zoek naar elektrische laad stations voor Voer tuigen binnen het bereik dat bereikbaar is of isochrone.
> * De bereik grens en de laad stations van een kaart weer geven.
> * Zoek en visualiseer een route naar het dichtstbijzijnde elektrische Voer tuig op basis van de schijf tijd.


## <a name="prerequisites"></a>Vereisten 

Als u deze zelf studie wilt volt ooien, moet u eerst een Azure Maps-account maken en uw primaire sleutel (abonnements sleutel) ophalen. 

Als u een abonnement voor een Azure Maps account wilt maken in de prijs categorie S1, volgt u de instructies in [uw Azure Maps-account beheren](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account). 

Als u de primaire abonnements sleutel voor uw account wilt ophalen, volgt u de instructies in [een account maken en uw sleutel ophalen](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-and-get-your-key).

## <a name="create-an-azure-notebook"></a>Een Azure-notebook maken

Als u deze zelf studie wilt volgen, moet u een Azure notebook-project maken en het Jupyter-notebook bestand downloaden en uitvoeren. Het notitieblok bestand bevat de python-code, waarmee het scenario in deze zelf studie wordt geïmplementeerd. Ga als volgt te werk om een Azure notebook-project te maken en het Jupyter-notebook document ernaar te uploaden:

1. Ga naar [Azure notitieblokken](https://notebooks.azure.com) en meld u aan. Zie [Quick Start: Meld u aan en stel een gebruikers-id in](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)voor meer informatie.
1. Selecteer aan de bovenkant van de pagina met het open bare profiel **Mijn projecten**.

    ![De knop mijn projecten](./media/tutorial-ev-routing/myproject.png)

1. Op de pagina **Mijn projecten** selecteert u **Nieuw project**.
 
   ![De knop Nieuw project](./media/tutorial-ev-routing/create-project.png)

1. Voer in het deel venster **Nieuw project maken** een project naam en project-id in.
 
    ![Het deel venster Nieuw project maken](./media/tutorial-ev-routing/create-project-window.png)

1. Selecteer **Maken**.

1. Nadat het project is gemaakt, downloadt u het [document bestand Jupyter notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) van de [Azure Maps Jupyter-notebook opslagplaats](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

1. Selecteer in de lijst projecten op de pagina **Mijn projecten** het project en selecteer vervolgens **uploaden** om het Jupyter notebook-document bestand te uploaden. 

    ![notitie blok uploaden](./media/tutorial-ev-routing/upload-notebook.png)

1. Upload het bestand van uw computer en selecteer vervolgens **gereed**.

1. Nadat het uploaden is voltooid, wordt het bestand weer gegeven op de pagina van uw project. Selecteer het bestand om het te openen als een Jupyter-notebook.

Om u te helpen beter inzicht te krijgen in de functionaliteit die in het notitieblok bestand is geïmplementeerd, raden we u aan om de code in het notitie blok één cel tegelijk uit te voeren. U kunt de code in elke cel uitvoeren door de knop **uitvoeren** boven aan de app notebook te selecteren.

  ![De knop uitvoeren](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Project-niveau pakketten installeren

Als u de code in het notitie blok wilt uitvoeren, installeert u pakketten op project niveau door het volgende te doen:

1. Down load het bestand [*Requirements. txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) van de [Azure Maps Jupyter notebook repository](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)en upload het vervolgens naar uw project.
1. Selecteer in het projectdashboard **projectinstellingen**. 
1. Selecteer in het deel venster **project instellingen** het tabblad **omgeving** en selecteer vervolgens **toevoegen**.
1. Ga als volgt te werk onder **omgevings instellingen**:   
    a. Selecteer in de eerste vervolg keuzelijst **vereisten. txt**.  
    b. Selecteer in de tweede vervolg keuzelijst het bestand *Requirements. txt* .  
    c. Selecteer **python versie 3,6** als uw versie in de derde vervolg keuzelijst.
1. Selecteer **Opslaan**.

    ![Pakketten installeren](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>De vereiste modules en frameworks laden

Als u alle vereiste modules en frameworks wilt laden, voert u het volgende script uit:

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>De bereik grens opvragen

In ons scenario heeft een bedrijf voor levering van pakketten enkele elektrische Voer tuigen in zijn vloot. Gedurende de dag moeten elektrische Voer tuigen worden gefactureerd zonder terug te hoeven naar het magazijn. Telkens wanneer de huidige resterende kosten minder dan een uur duren (dat wil zeggen, als de accu weinig kosten heeft), zoekt u naar een set laad stations die zich binnen een bereik bevinden en haalt u de grens gegevens voor dat bereik op. 

Omdat het bedrijf de voor keur geeft aan het gebruik van routes waarvoor een evenwicht tussen de economie en de snelheid is vereist, is de aangevraagde routeType *Eco*. Met het volgende script wordt de [Get route Range-API](https://docs.microsoft.com/rest/api/maps/route/getrouterange) van de Azure Maps Routing-service aangeroepen met behulp van para meters voor het verbruiks model van het Voer tuig. Het script parseert vervolgens het antwoord voor het maken van een veelhoek object van de geojson-indeling, die het maximum bereik van de auto aangeeft.

Als u de grenzen voor het bereik van het elektrische Voer tuig wilt bepalen, voert u het script uit in de volgende cel:

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


# Get boundaries for the electric vehicle's reachable range.
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

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Zoeken naar elektrische laad stations voor Voer tuigen binnen het bereik

Nadat u het bereikable (isochrone) voor het elektrische medium hebt vastgesteld, kunt u zoeken naar laad stations binnen dat bereik. 

Met het volgende script wordt de Azure Maps [post Search aangeroepen in de geometrie-API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry). Er wordt gezocht naar elektrische laad zenders binnen de grenzen van het maximum bereik van de auto en vervolgens wordt de reactie geparseerd naar een matrix van bereik bare locaties.

Voer het volgende script uit om te zoeken naar elektrische laad stations voor Voer tuigen binnen het bereik dat bereikbaar is:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>De bereik bare bereik-en laad punten uploaden naar Azure Maps-gegevens service

U wilt visualiseren op een kaart met de oplaad stations en grens waarden voor het maximale bereik van het elektrische medium. Als u dit wilt doen, uploadt u de grens gegevens en laadt u gegevens van stations als geojson-objecten naar Azure Maps-gegevens service met behulp van de [Data upload-API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Als u de grens-en oplaad punt gegevens wilt uploaden naar Azure Maps-gegevens service, voert u de volgende twee cellen uit:

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

# Upload the range data to Azure Maps Data Service.
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

# Upload the electric vehicle charging station data to Azure Maps Data Service.
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

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>De laad stations en het bereik van bereiken op een kaart renderen

Nadat u de gegevens naar de gegevens service hebt geüpload, roept u de Azure Maps [kaart installatie kopie service ophalen](https://docs.microsoft.com/rest/api/maps/render/getmapimage) aan om de verwerkings punten en de maximale bereik bare grens op de statische kaart afbeelding te genereren door het volgende script uit te voeren:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
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

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Een kaart met het locatie bereik](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Het optimale oplaad station zoeken

Nadat u hebt vastgesteld dat alle mogelijke laad zenders binnen het bereikbaar zijn, weet u zeker dat deze in een minimum tijd kunnen worden bereikt. 

Met het volgende script wordt de Azure Maps [matrix routerings-API](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)aangeroepen, die wordt geretourneerd voor de locatie van het opgegeven Voer tuig, de reis tijd en afstand tot elk oplaad station. Het script in de volgende cel parseert het antwoord voor het vinden van het dichtstbijzijnde bereik bare opheffings station met betrekking tot tijd.

Als u wilt zoeken naar het dichtstbijzijnde bereik dat kan worden bereikt, moet u het script uitvoeren in de volgende cel:

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

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>De route naar het dichtstbijzijnde oplaad station berekenen

Nu u het dichtstbijzijnde oplaad station hebt gevonden, kunt u de [API route richtingen ophalen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) aanroepen om de gedetailleerde route van de huidige locatie van het elektrische medium naar het oplaad station aan te vragen.

Als u de route naar het oplaad station wilt ophalen en het antwoord wilt parseren om een geojson-object te maken dat de route vertegenwoordigt, voert u het script uit in de volgende cel:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
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

Als u de route wilt visualiseren, uploadt u de route gegevens eerst als een geojson-object naar Azure Maps data-service met behulp van de Azure Maps [Data upload-API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Vervolgens roept u de rendering-service aan, de [afbeeldings-API voor kaarten ophalen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)om de route op de kaart weer te geven en te visualiseren.

Als u een afbeelding wilt ophalen voor de gerenderde route op de kaart, voert u het volgende script uit:

```python
# Upload the route data to Azure Maps Data Service.
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


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Een kaart met de route](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Azure Maps REST-Api's rechtstreeks aanroept en Azure Maps gegevens visualert met behulp van python.

Zie voor het verkennen van de Azure Maps Api's die in deze zelf studie worden gebruikt:

* [Route bereik ophalen](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Zoeken in geometrie plaatsen](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Gegevens uploaden](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Afbeelding van de kaart ophalen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Route matrix plaatsen](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Route beschrijving ophalen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Zie [Azure Maps rest api's](https://docs.microsoft.com/azure/azure-maps/#reference)voor een volledige lijst met Azure Maps rest api's.

Zie [Azure notebooks](https://docs.microsoft.com/azure/notebooks)voor meer informatie over Azure notebooks.
