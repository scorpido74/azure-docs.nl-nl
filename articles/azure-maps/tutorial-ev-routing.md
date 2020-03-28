---
title: 'Zelfstudie: Elektrische voertuigen routeren met Azure Notebooks (Python) | Microsoft Azure Maps'
description: Route elektrische voertuigen met behulp van Microsoft Azure Maps routing API's en Azure Notebooks.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3118ca39ec0efd42c9f7b622c91f857034ef4b03
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333838"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Zelfstudie: Elektrische voertuigen routeren met Azure Notebooks (Python)

Azure Maps is een portfolio van GEOspatialservice-API's die native zijn geïntegreerd in Azure. Met deze API's kunnen ontwikkelaars, ondernemingen en ISV's locatiebewuste apps, IoT,mobility, logistiek en asset tracking-oplossingen ontwikkelen. 

De Azure Maps REST API's kunnen worden aangeroepen vanuit talen zoals Python en R om scenario's voor georuimtelijke gegevensanalyse en machine learning mogelijk te maken. Azure Maps biedt een robuuste set [routerings-API's](https://docs.microsoft.com/rest/api/maps/route) waarmee gebruikers routes tussen verschillende gegevenspunten kunnen berekenen. De berekeningen zijn gebaseerd op verschillende omstandigheden, zoals het type voertuig of bereikbaar gebied. 

In deze tutorial, je loopt helpen een bestuurder wiens elektrische voertuig batterij is laag. De bestuurder moet het dichtstbijzijnde laadstation vinden vanaf de locatie van het voertuig.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Jupyter-notitieblok maken en uitvoeren op [Azure-notitieblokken](https://docs.microsoft.com/azure/notebooks) in de cloud.
> * Bel Azure Maps REST API's in Python.
> * Zoek naar een bereik op basis van het verbruiksmodel van de elektrische auto.
> * Zoek naar oplaadpunten voor elektrische voertuigen binnen het bereik, of isochrone.
> * Maak de bereikgrens en laadstations weer op een kaart.
> * Vind en visualiseer een route naar het dichtstbijzijnde laadstation voor elektrische voertuigen op basis van de rijtijd.


## <a name="prerequisites"></a>Vereisten 

Als u deze zelfstudie wilt voltooien, moet u eerst een Azure Maps-account maken en uw primaire sleutel (abonnementssleutel) oppakken. 

Als u een Azure Maps-accountabonnement wilt maken, volgt u instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps). U hebt een Azure Maps-accountabonnement nodig met de s1-prijslaag. 

Als u de primaire abonnementssleutel voor uw account wilt opvragen, volgt u de instructies in [de primaire sleutel op te halen.](quick-demo-map-app.md#get-the-primary-key-for-your-account)

Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

## <a name="create-an-azure-notebook"></a>Een Azure-notitieblok maken

Als u deze zelfstudie wilt volgen, moet u een Azure-notitieblokproject maken en het Jupyter-notitieblokbestand downloaden en uitvoeren. Het notitieblokbestand bevat Python-code, waarmee het scenario in deze zelfstudie wordt geïmplementeerd. Ga als volgt te werk om een Azure-notitieblokproject te maken en het Jupyter-notitieblokdocument ernaar te uploaden:

1. Ga naar [Azure-notitieblokken](https://notebooks.azure.com) en meld u aan. Zie [Snelstart: Aanmelden en een gebruikersnaam instellen](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)voor meer informatie.
1. Selecteer boven aan uw openbare profielpagina **Mijn projecten**.

    ![De knop Mijn projecten](./media/tutorial-ev-routing/myproject.png)

1. Selecteer **op** de pagina Mijn projecten de optie **Nieuw project**.
 
   ![De knop Nieuw project](./media/tutorial-ev-routing/create-project.png)

1. Voer in het deelvenster **Nieuw project maken** een projectnaam en project-ID in.
 
    ![Het deelvenster Nieuw project maken](./media/tutorial-ev-routing/create-project-window.png)

1. Selecteer **Maken**.

1. Nadat uw project is gemaakt, downloadt u dit [Jupyter-notitieblokdocumentbestand](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) uit de [Azure Maps Jupyter-notitieblokopslagplaats.](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

1. Selecteer uw project in de lijst projecten op de pagina **Mijn projecten** en selecteer **Uploaden** om het documentbestand van Jupyter-notitieblok te uploaden. 

    ![notitieblok uploaden](./media/tutorial-ev-routing/upload-notebook.png)

1. Upload het bestand vanaf uw computer en selecteer **Gereed**.

1. Nadat de upload is voltooid, wordt uw bestand weergegeven op uw projectpagina. Dubbelklik op het bestand om het te openen als een Jupyter-notitieblok.

Probeer inzicht te krijgen in de functionaliteit die in het notitieblokbestand is geïmplementeerd. Voer de code, in het notitieblokbestand, één cel tegelijk uit. U de code in elke cel uitvoeren door de knop **Uitvoeren** boven aan de notitieblok-app te selecteren.

  ![De knop Uitvoeren](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Pakketten op projectniveau installeren

Als u de code in het notitieblok wilt uitvoeren, installeert u pakketten op projectniveau door de volgende stappen uit te voeren:

1. Download het [*bestand requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) uit de [Azure Maps Jupyter-laptopopslagplaats](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)en upload het vervolgens naar uw project.
1. Selecteer **projectinstellingen**in het projectdashboard . 
1. Selecteer in het deelvenster **Projectinstellingen** het tabblad **Omgeving** en selecteer **Vervolgens Toevoegen**.
1. Ga als volgt te werk onder **Stappen voor omgevingsinstellingen:**   
    a. Selecteer **Requirements.txt**in de eerste vervolgkeuzelijst .  
    b. Selecteer in de tweede vervolgkeuzelijst uw *requirements.txt-bestand.*  
    c. Selecteer **Python Versie 3.6** als versie in de derde vervolgkeuzelijst.
1. Selecteer **Opslaan**.

    ![Pakketten installeren](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Laad de benodigde modules en frameworks

Voer het volgende script uit om alle vereiste modules en frameworks te laden.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Vraag de bereikgrens aan

Een pakketbezorger heeft een aantal elektrische voertuigen in zijn vloot. Overdag moeten elektrische voertuigen worden opgeladen zonder terug te hoeven keren naar het magazijn. Elke keer dat de resterende lading daalt tot minder dan een uur, zoekt u naar een set van laadstations die binnen een bereik van bereik zijn. In wezen zoekt u naar een laadstation wanneer de batterij bijna leeg is. En, krijg je de grens informatie voor dat bereik van laadpalen. 

Omdat het bedrijf de voorkeur geeft aan routes die een balans van economie en snelheid vereisen, is de gevraagde routeType *eco.* In het volgende script wordt de [API Routebereik opvragen](https://docs.microsoft.com/rest/api/maps/route/getrouterange) van de routeringsservice azure maps. Het maakt gebruik van parameters voor het verbruik van het voertuig model. Het script ontleden vervolgens het antwoord om een veelhoekobject van de geojson-indeling te maken, dat het maximaal bereik van de auto vertegenwoordigt.

Voer het script uit in de volgende cel om de grenzen voor het bereik van het elektrische voertuig te bepalen:

```python
subscriptionKey = "Your Azure Maps key"
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

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Zoek naar oplaadpunten voor elektrische voertuigen binnen het bereik

Nadat u het bereik (isochrone) voor het elektrische voertuig hebt bepaald, u zoeken naar laadstations binnen dat bereik. 

In het volgende script wordt de API azure maps [post search inside geometry aanroept.](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) Het zoekt naar oplaadpunten voor elektrisch voertuig, binnen de grenzen van het maximaal bereik van de auto. Vervolgens onteinde het script het antwoord op een array van bereikbare locaties.

Voer het volgende script uit om te zoeken naar oplaadpunten voor elektrische voertuigen binnen het bereik van het volgende:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Het bereik en de laadpunten uploaden naar Azure Maps Data Service

Op een kaart wilt u de laadstations en de grens voor het maximaal bereik van het elektrische voertuig visualiseren. Upload hiervoor de grensgegevens en laadstations gegevens als geojson objecten naar Azure Maps Data Service. Gebruik de [API voor gegevensupload](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Als u de grens- en oplaadpuntgegevens wilt uploaden naar Azure Maps Data Service, voert u de volgende twee cellen uit:

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

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>De laadstations en het bereik op een kaart weergeven

Nadat u de gegevens naar de gegevensservice hebt geüpload, belt u de Azure Maps [Get Map Image-service](https://docs.microsoft.com/rest/api/maps/render/getmapimage). Deze service wordt gebruikt om de laadpunten en de maximaal bereikbare grens op de statische kaartafbeelding weer te geven door het volgende script uit te voeren:

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

![Een kaart met het locatiebereik](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Vind het optimale laadstation

Ten eerste wilt u alle potentiële laadstations binnen het bereik bepalen. Vervolgens wilt u weten welke van hen kan worden bereikt in een minimale hoeveelheid tijd. 

In het volgende script wordt de Azure Maps [Matrix Routing API aangeschreven.](https://docs.microsoft.com/rest/api/maps/route/postroutematrix) Het retourneert de opgegeven voertuiglocatie, de reistijd en de afstand tot elk laadstation. Het script in de volgende cel onteert het antwoord op het dichtstbijzijnde bereikbare laadstation met betrekking tot de tijd te vinden.

Voer het script uit in de volgende cel om het dichtstbijzijnde bereikbare laadstation te vinden dat in de minste tijd bereikbaar is:

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

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Bereken de route naar het dichtstbijzijnde laadstation

Nu u het dichtstbijzijnde laadstation hebt gevonden, u de [API Routeroutebeschrijvingen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) ophalen om de gedetailleerde route op te vragen van de huidige locatie van het elektrische voertuig naar het laadstation.

Voer het script uit in de volgende cel om de route naar het laadstation te krijgen en het antwoord te ontleden om een geojsonobject te maken dat de route vertegenwoordigt:

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

## <a name="visualize-the-route"></a>Visualiseer de route

Als u de route wilt visualiseren, uploadt u de routegegevens eerst als geojson-object naar Azure Maps Data Service. Gebruik hiervoor de Azure Maps [Data Upload API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Bel vervolgens de renderingservice, [Get Map Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage), om de route op de kaart weer te geven en te visualiseren.

Voer het volgende script uit om een afbeelding voor de gerenderde route op de kaart te krijgen:

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

In deze zelfstudie hebt u geleerd hoe u Azure Maps REST API's rechtstreeks aanroepen en Azure Maps-gegevens visualiseren met behulp van Python.

Zie het alsvolgt in de apuisten van Azure Maps die in deze zelfstudie worden gebruikt:

* [Routebereik krijgen](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Zoeken in de map plaatsen](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Gegevens uploaden](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render - Kaartafbeelding krijgen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Matrix voor postroutes](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Routeroutebeschrijving opvragen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Zie Azure Maps REST API's voor een volledige lijst met AZURE Maps REST [API's.](https://docs.microsoft.com/azure/azure-maps/consumption-model)

Zie [Azure-notitieblokken](https://docs.microsoft.com/azure/notebooks)voor meer informatie over Azure-notitieblokken.
