---
title: 'Zelfstudie: Sensorgegevens combineren met weersvoorspellingsgegevens met Azure Notebooks(Python) | Microsoft Azure Maps'
description: In deze zelfstudie ziet u hoe u sensorgegevens combineren met weersvoorspellingsgegevens van Microsoft Azure Maps Weather Service met Azure Notebooks (Python).
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e5292f5166e739264e9cf969480b70f415fcc75a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333498"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Zelfstudie: Sensorgegevens combineren met weersvoorspellingsgegevens met Azure Notebooks (Python)

Windenergie is een alternatieve energiebron voor fossiele brandstoffen om klimaatverandering tegen te gaan. Omdat wind van nature niet consistent is, moeten exploitanten van windenergie machine learning (ML)-modellen bouwen om de windenergiecapaciteit te voorspellen. Deze voorspelling is nodig om aan de vraag naar elektriciteit te voldoen en de stabiliteit van het net te waarborgen. In deze zelfstudie bekijken we hoe weersvoorspellingsgegevens van Azure Maps worden gecombineerd met demogegevens voor weermetingen. Weersvoorspellingsgegevens worden opgevraagd door azure maps weerservice te bellen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Werk met gegevensbestanden in [Azure-notitieblokken](https://docs.microsoft.com/azure/notebooks) in de cloud.
> * Demogegevens uit het bestand laden.
> * Bel Azure Maps REST API's in Python.
> * Locatiegegevens op de kaart renderen.
> * Verrijk de demogegevens met azure maps daily forecast-weersgegevens. [Daily Forecast](https://aka.ms/AzureMapsWeatherDailyForecast)
> * Plot prognosegegevens in grafieken.


## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, moet u eerst het:

1. Maak een Azure Maps-accountabonnement in de prijscategorie S0 door instructies te volgen in [Een account maken.](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. Download de primaire abonnementssleutel voor uw account, volg de instructies in [de primaire sleutel op te halen.](quick-demo-map-app.md#get-the-primary-key-for-your-account)


Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

Volg de instructies [Een Azure-notitieblok maken](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)om vertrouwd te raken met Azure-notitieblokken en om te weten hoe u aan de slag.

> [!Note]
> Het Jupyter-notitieblokbestand voor dit project kan worden gedownload van de [Weather Maps Jupyter-notebookrepository.](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)

## <a name="load-the-required-modules-and-frameworks"></a>Laad de benodigde modules en frameworks

Voer het volgende script uit om alle vereiste modules en frameworks te laden:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Weergegevens importeren

Omwille van deze tutorial, zullen we gebruik maken van weersgegevens lezingen van sensoren geïnstalleerd op vier verschillende windturbines. De monstergegevens bestaan uit 30 dagen weermetingen. Deze metingen worden verzameld van weer datacenters in de buurt van elke turbine locatie. De demogegevens bevatten gegevensmetingen voor temperatuur, windsnelheid en richting. U de demo gegevens downloaden vanaf [hier](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Het onderstaande script importeert demogegevens naar het Azure Notebook.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Dagelijkse prognosegegevens opvragen

In ons scenario willen we voor elke sensorlocatie dagelijks een prognose aanvragen. In het volgende script wordt de [Dagelijkse prognose-API](https://aka.ms/AzureMapsWeatherDailyForecast) van de weerservice Azure Maps aanroept. Deze API retourneert de weersvoorspelling voor elke windturbine, voor de komende 15 dagen vanaf de huidige datum.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Het onderstaande script geeft de turbinelocaties op de kaart weer door de Azure Maps [Get Map Image-service](https://docs.microsoft.com/rest/api/maps/render/getmapimage)aan te roepen.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Turbinelocaties](./media/weather-service-tutorial/location-map.png)


We groeperen de prognosegegevens met de demogegevens op basis van de station-ID. De station-ID is voor het weerdatacenter. Deze groepering breidt de demogegevens uit met de prognosegegevens.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

In de volgende tabel worden de gecombineerde historische en prognosegegevens voor een van de turbinelocaties weergegeven.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Gegroepeerde gegevens](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Prognosegegevens voor plotten

We zetten de voorspelde waarden in kaart ten opzichte van de dagen waarvoor ze worden voorspeld. Dit perceel stelt ons in staat om de snelheid en richting veranderingen van de wind te zien voor de komende 15 dagen.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

De onderstaande grafieken visualiseren de prognosegegevens. Voor de verandering van windsnelheid, zie de linkergrafiek. Zie de juiste grafiek voor verandering in windrichting. Deze gegevens zijn voorspelling voor de komende 15 dagen vanaf de dag dat de gegevens worden opgevraagd.

<center>

![Windsnelheid](./media/weather-service-tutorial/speed-date-plot.png) ![plot Wind richting plot](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heb je geleerd hoe je Azure Maps REST API's aanroepen om weersvoorspellingsgegevens op te halen. Je hebt ook geleerd hoe je de gegevens op grafieken visualiseren.

Zie [EV-routering met Azure-notitieblokken](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing)voor meer informatie over het aanroepen van Azure Maps REST API's in Azure-notitieblokken.

Zie het alsvolgt in de apuisten van Azure Maps die in deze zelfstudie worden gebruikt:

* [Dagelijkse weersverwachting](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render - Kaartafbeelding krijgen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Zie Azure Maps REST API's voor een volledige lijst met AZURE Maps REST [API's.](https://docs.microsoft.com/azure/azure-maps/consumption-model)

Zie [Azure-notitieblokken](https://docs.microsoft.com/azure/notebooks)voor meer informatie over Azure-notitieblokken.
