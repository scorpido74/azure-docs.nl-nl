---
title: 'Zelf studie: sensor gegevens samen voegen met weer prognose gegevens met behulp van Azure Notebooks (python) | Microsoft Docs'
description: 'Zelf studie: deze zelf studie laat zien hoe u met behulp van Azure Notebooks (python) sensor gegevens kunt samen voegen met de weers verwachting van Azure Maps weer service.'
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f641640ff6cf4174e2e1374404d47fc0760f79f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979901"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Zelf studie: sensor gegevens samen voegen met weer prognose gegevens met behulp van Azure Notebooks (python)

Wind kracht is een alternatieve energie bron voor fossiele brand stoffen om te bestrijden tegen klimaat verandering. Omdat de wind zelf niet consistent is, moeten de voedings exploitanten van de wind ML (machine learning) modellen bouwen om te voors pellen wat de wind kracht is om te voldoen aan de elektriciteits vraag en de stabiliteit van het raster te garanderen. In deze zelf studie laten we zien hoe de Azure Maps weers verwachtingen gegevens kunnen worden gecombineerd met de demo-gegevensset van sensor locaties met weers-Lees functies. Er worden gegevens over de weers verwachting aangevraagd door de Azure Maps weer service aan te roepen.

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

1. Maak een abonnement Azure Maps account in de prijs categorie S0 door de instructies in [uw Azure Maps-account beheren](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)te volgen.
2. Down load de primaire abonnements sleutel voor uw account en volg de instructies in [de primaire sleutel voor uw account ophalen](./tutorial-search-location.md#getkey).

Volg de instructies voor het [maken van een Azure-notebook](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)om vertrouwd te raken met Azure-notebooks en te weten hoe u aan de slag kunt gaan.

> [!Note]
> Het Jupyter-notebook bestand voor dit project kan worden gedownload van de [weer kaarten Jupyter notebook-opslag plaats](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>De vereiste modules en frameworks laden

Als u alle vereiste modules en frameworks wilt laden, voert u het volgende script uit:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Weer gegevens importeren

Voor het omwille van deze zelf studie gebruiken we weer gegevens leesingen van Sens oren die zijn geïnstalleerd in vier verschillende wind turbines. De voorbeeld gegevens bestaan uit 30 dagen van weers omstandigheden die worden verzameld van de weers-data centers in de buurt van elke turbine-locatie. De voorbeeld gegevens bevatten gegevens voor de Tempe ratuur, de wind snelheid en de richting. U kunt de demo gegevens [hier](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)downloaden. In het onderstaande script worden demo gegevens in azure notebook geïmporteerd.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Dagelijkse prognose gegevens aanvragen

In ons voorbeeld scenario willen we dagelijks een prognose aanvragen voor elke sensor locatie. Met het volgende script wordt de [dagelijkse prognose-API](https://aka.ms/AzureMapsWeatherDailyForecast) van de Azure Maps-weer service aangeroepen om dagelijks weers verwachtingen voor elke wind turbine te krijgen, voor de komende vijf tien dagen vanaf de huidige datum.


```python
subscription_key = "Your Azure Maps primary subscription key"

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


Als u de voorbeeld gegevens wilt uitbreiden met de prognose gegevens, zullen we de prognose gegevens groeperen met de demo gegevens op basis van de Station-ID voor het weer Data Center.

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

Om te zien hoe de wind snelheid en de richting van de volgende 15 dagen veranderen, zullen we de geraamde waarden uitzetten op de dagen dat ze worden geforecastd.

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

De onderstaande grafieken visualiseren de prognose gegevens voor de wijziging van de wind snelheid (linker grafiek) en richting (rechter grafiek) in de komende 15 dagen vanaf de huidige dag.

<center>

![](./media/weather-service-tutorial/speed-date-plot.png) van de wind-snelheid ![tekening](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Azure Maps REST-Api's aanroept om weer prognose gegevens op te halen en de gegevens in grafieken te visualiseren.

Zie voor meer informatie over het aanroepen van Azure Maps REST-Api's in Azure Notebooks [EV-route ring met behulp van Azure notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Zie voor het verkennen van de Azure Maps Api's die in deze zelf studie worden gebruikt:

* [Dagelijkse prognose](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Afbeelding van de kaart ophalen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Zie [Azure Maps rest api's](https://docs.microsoft.com/azure/azure-maps/#reference)voor een volledige lijst met Azure Maps rest api's.

Zie [Azure notebooks](https://docs.microsoft.com/azure/notebooks)voor meer informatie over Azure notebooks.
