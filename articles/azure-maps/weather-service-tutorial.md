---
title: 'Zelf studie: sensor gegevens samen voegen met weer prognose gegevens met behulp van Azure Notebooks (python) | Microsoft Azure kaarten'
description: In deze zelf studie wordt uitgelegd hoe u met behulp van Azure Notebooks (python) sensor gegevens samenvoegt met weers verwachtingen van Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d6b82ec0662745fd9c9a05db28595ff84f57f330
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208026"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Zelf studie: sensor gegevens samen voegen met weer prognose gegevens met behulp van Azure Notebooks (python)

Wind kracht is een alternatieve energie bron voor fossiele brand stoffen om te bestrijden tegen klimaat verandering. Omdat wind niet consistent is met aard, moeten de voedings exploitanten van de wind machine learning (ML) modellen bouwen om de snelheid van de wind energie te voors pellen. Deze voor spelling is nodig om te voldoen aan de elektriciteits vraag en de stabiliteit van het raster te garanderen. In deze zelf studie wordt uitgelegd hoe Azure Maps weers prognose gegevens worden gecombineerd met voorbeeld gegevens voor weer lees-en achterlezen. Er worden gegevens over de weers verwachting aangevraagd door de Azure Maps weer service aan te roepen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Werk met gegevens bestanden in [Azure notebooks](https://docs.microsoft.com/azure/notebooks) in de Cloud.
> * De demo gegevens laden uit het bestand.
> * Roep Azure Maps REST-Api's in python.
> * De locatie gegevens op de kaart weer geven.
> * Verrijk de demo gegevens met Azure Maps dagelijkse gegevens over [prognoses](https://aka.ms/AzureMapsWeatherDailyForecast) .
> * De prognose gegevens in grafieken te tekenen.


## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u eerst het volgende doen:

1. Maak een abonnement Azure Maps account in de prijs categorie S0 door de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps)te volgen.
2. Down load de primaire abonnements sleutel voor uw account en volg de instructies in de [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

Volg de instructies voor het [maken van een Azure-notebook](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)om vertrouwd te raken met Azure-notebooks en te weten hoe u aan de slag kunt gaan.

> [!Note]
> Het Jupyter-notebook bestand voor dit project kan worden gedownload van de [weer kaarten Jupyter notebook-opslag plaats](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>De vereiste modules en frameworks laden

Als u alle vereiste modules en frameworks wilt laden, voert u het volgende script uit:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Weer gegevens importeren

Voor het omwille van deze zelf studie gebruiken we de weer gegeven gegevens van Sens oren die worden geïnstalleerd in vier verschillende wind turbines. De voorbeeld gegevens bestaan uit 30 dagen aan weers omstandigheden. Deze leesingen worden verzameld van weer data centers in de buurt van elke turbine-locatie. De voorbeeld gegevens bevatten gegevens voor de Tempe ratuur, de wind snelheid en de richting. U kunt de demo gegevens [hier](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)downloaden. In het onderstaande script worden demo gegevens in azure notebook geïmporteerd.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Dagelijkse prognose gegevens aanvragen

In ons scenario willen we dagelijks een prognose aanvragen voor elke sensor locatie. Met het volgende script wordt de [dagelijkse prognose-API](https://aka.ms/AzureMapsWeatherDailyForecast) van de Azure Maps weer service aangeroepen. Deze API retourneert weers verwachtingen voor elke wind turbine, voor de komende vijf tien dagen vanaf de huidige datum.


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

In het onderstaande script worden de turbine-locaties op de kaart weer gegeven door de Azure Maps [service toewijzings installatie kopie ophalen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)aan te roepen.

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

![Turbine-locaties](./media/weather-service-tutorial/location-map.png)


De prognose gegevens worden gegroepeerd met de demo gegevens op basis van de Station-ID. De Station-ID is voor het weer Data Center. Deze groepering breidt de demo gegevens uit met de prognose gegevens.

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

De volgende tabel bevat de gecombineerde historische en prognose gegevens voor een van de turbine-locaties.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![gegroepeerde gegevens](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Prognose gegevens tekenen

We zetten de geraamde waarden op tegen de dagen waarvoor ze worden geforecastd. In dit plot kunnen we de snelheid en richtings wijzigingen van de wind gedurende de volgende 15 dagen zien.

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

In de grafieken hieronder worden de prognose gegevens gevisualiseerd. Zie het linkerframe voor meer informatie over het wijzigen van wind snelheid. Zie het juiste diagram voor het wijzigen van de richting van de wind. Deze gegevens zijn voor spelling voor de volgende vijf tien dagen vanaf de dag dat de gegevens worden aangevraagd.

<center>

![](./media/weather-service-tutorial/speed-date-plot.png) van de wind-snelheid ![tekening](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Azure Maps REST-Api's aanroept om weer prognose gegevens op te halen. U hebt ook geleerd hoe u de gegevens in grafieken kunt visualiseren.

Zie voor meer informatie over het aanroepen van Azure Maps REST-Api's in Azure Notebooks [EV-route ring met behulp van Azure notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Zie voor het verkennen van de Azure Maps Api's die in deze zelf studie worden gebruikt:

* [Dagelijkse prognose](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Afbeelding van de kaart ophalen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Zie [Azure Maps rest api's](https://docs.microsoft.com/azure/azure-maps/consumption-model)voor een volledige lijst met Azure Maps rest api's.

Zie [Azure notebooks](https://docs.microsoft.com/azure/notebooks)voor meer informatie over Azure notebooks.
