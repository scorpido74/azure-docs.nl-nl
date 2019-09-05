---
title: 'Zelfstudie: Een geautomatiseerd machine learning model verrijken'
titleSuffix: Azure Open Datasets
description: Meer informatie over hoe u het gemak van Azure open gegevens sets kunt gebruiken in combi natie met de kracht van Azure Machine Learning-service om een regressie model te maken om NYCe prijzen voor de taxi te voors pellen.
services: open-datasets
ms.service: open-datasets
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: 0218221426351b0b508021fc74693557f200b4e8
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309267"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Zelfstudie: Een regressie model bouwen met automatische machine learning en gegevens sets openen

In deze zelf studie maakt u gebruik van het gemak van Azure open gegevens sets, samen met de kracht van Azure Machine Learning-service om een regressie model te maken om NYCe prijzen voor het taxi tarief te voors pellen. U kunt eenvoudig openbaar beschik bare taxi-, feestdag-en weers gegevens downloaden en een geautomatiseerd machine learning experiment configureren met behulp van Azure Machine Learning-service. Dit proces accepteert trainings gegevens en configuratie-instellingen en herhaalt automatisch combi Naties van verschillende methoden voor het normaliseren/standaardiseren van functies, modellen en afstemming-instellingen om het beste model te ontvangen.

In deze zelf studie leert u de volgende taken:

- Een werkruimte configureren in de Azure Machine Learning-service
- Een lokale python-omgeving instellen
- Gegevens openen, transformeren en eraan koppelen met behulp van Azure open gegevens sets
- Een geautomatiseerd machine learning regressie model trainen
- Nauw keurigheid van model berekenen

## <a name="prerequisites"></a>Vereisten

Deze zelf studie vereist de volgende vereisten.

* Een Azure Machine Learning Services-werk ruimte
* Een python 3,6-omgeving

### <a name="create-a-workspace"></a>Een werkruimte maken

Volg de [instructies](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) voor het maken van een werk ruimte via de Azure Portal, als u er nog geen hebt. Na het maken noteert u de naam van de werk ruimte, de naam van de resource groep en de abonnements-ID.

### <a name="create-a-python-environment"></a>Een python-omgeving maken

In dit voor beeld wordt een Anaconda-omgeving met Jupyter-notebooks gebruikt, maar u kunt deze code uitvoeren in een 3.6. x-omgeving en met een wille keurige tekst editor of IDE. Gebruik de volgende stappen om een nieuwe ontwikkel omgeving te maken.

1. Als u dit nog niet hebt, [downloadt](https://www.anaconda.com/distribution/) en installeert u Anaconda en kiest u **python 3,7-versie**.
1. Open een Anaconda-prompt en maak een nieuwe omgeving. Het duurt enkele minuten om de omgeving te maken terwijl onderdelen en pakketten worden gedownload.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Activeer de omgeving.
    ```
    conda activate tutorialenv
    ```
1. Omgevings specifieke IPython-kernels in te scha kelen.
    ```
    conda install notebook ipykernel
    ```
1. Maak de kernel.
    ```
    ipython kernel install --user
    ```
1. Installeer de pakketten die u nodig hebt voor deze zelf studie. Deze pakketten zijn groot en het duurt 5-10 minuten om de installatie uit te voeren.
    ```
    pip install azureml-sdk[automl] azureml-opendatasets
    ```
1. Start een notebook-kernel vanuit uw omgeving.
    ```
    jupyter notebook
    ```

Nadat u deze stappen hebt voltooid, kloont u de opslag plaats van het [Open gegevens sets notebook](https://github.com/Azure/OpenDatasetsNotebooks) en opent u de notebook **zelf studies/taxi-automl/01-tutorial-opendatasets-automl. ipynb** om het uit te voeren.

## <a name="download-and-prepare-data"></a>Gegevens downloaden en voorbereiden

Importeer de benodigde pakketten. Het open gegevens sets pakket bevat een klasse die elke gegevens bron`NycTlcGreen` vertegenwoordigt (bijvoorbeeld) om eenvoudig datum parameters te filteren voordat u downloadt.


```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Maak eerst een data frame om de taxi gegevens te bewaren. Als u in een niet-Spark-omgeving werkt, kunt u met data sets alleen één maand aan gegevens tegelijk downloaden met bepaalde klassen `MemoryError` om te voor komen dat er grote gegevens sets zijn. Als u een jaar aan taxi gegevens wilt downloaden, kunt u één maand per keer ophalen en voordat u deze toevoegt aan `green_taxi_df` wille keurige 2000 records van elke maand om te voor komen dat de data frame wordt gepipetteerd. Bekijk een voor beeld van de gegevens.

>[!NOTE]
> Open gegevens sets hebben spiegel klassen voor het werken in Spark-omgevingen waarin de gegevens grootte en het geheugen niet van belang zijn.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Leverancier</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>kent</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.921715</td>
      <td>40,766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>2.0</td>
      <td>11,5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0,50</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.830894</td>
      <td>40,759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1,93</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.881195</td>
      <td>40,741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.954376</td>
      <td>40,805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,845200</td>
      <td>40,722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0,3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 rijen × 23 kolommen</p>
</div>



Nu de initiële gegevens zijn geladen, definieert u een functie voor het maken van verschillende op tijd gebaseerde functies uit het veld Datum/tijd ophalen. Hiermee maakt u nieuwe velden voor het maand nummer, de dag van de maand, de dag van de week en het uur van de dag, waarna het model kan worden gefactord op basis van de op tijd gebaseerde seizoensgebondenheid. De functie voegt ook een statische functie voor de land code toe om gegevens van de feest dagen samen te voegen. Gebruik de `apply()` functie op de data frame om iteratieve `build_time_features()` functie toe te passen op elke rij in de taxi gegevens.


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))


green_taxi_df[["month_num", "day_of_month", "day_of_week", "hour_of_day", "country_code"]
              ] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Leverancier</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.921715</td>
      <td>40,766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0,50</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.830894</td>
      <td>40,759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4</td>
      <td>17</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1,93</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.881195</td>
      <td>40,741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73.954376</td>
      <td>40,805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,845200</td>
      <td>40,722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>VS</td>
    </tr>
  </tbody>
</table>
<p>10 rijen × 28 kolommen</p>
</div>

Verwijder enkele van de kolommen die u niet nodig hebt voor het maken van modellen of extra functies. Wijzig de naam van het tijd veld voor de ophaal tijd en converteer de tijd `pandas.Series.dt.normalize`naar middernacht met. U kunt dit doen met alle tijd functies zodat het onderdeel datetime later als een sleutel kan worden gebruikt bij het samen voegen van gegevens sets op een dagelijks niveau van granulariteit.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                     ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(
    columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Leverancier</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73.921715</td>
      <td>40,766682</td>
      <td>-73.916908</td>
      <td>40,761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>VS</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0,50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>VS</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>Verrijken met gegevens van de feest dagen

Nu u de taxi-gegevens hebt gedownload en ruwweg hebt voor bereid, kunt u in de feest dagen extra functies toevoegen. Met de opties voor de feestdag worden nauw keurigheid van modellen ondersteund, omdat er grote feest dagen zijn waarbij de levering van de taxi aanzienlijk toeneemt. De gegevensset van de feest dagen is relatief klein. Haal daarom de volledige set `PublicHolidays` op met behulp van de klasse-constructor zonder para meters voor filteren. Bekijk een voor beeld van de gegevens om de indeling te controleren.

```python
from azureml.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>countryOrRegion</th>
      <th>holidayName</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>Albanië</td>
      <td>Nieuwjaarsdag</td>
      <td>Geen</td>
      <td>AL</td>
      <td>Nieuwjaarsdag</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Algerije</td>
      <td>Nieuwjaarsdag</td>
      <td>Geen</td>
      <td>DZ</td>
      <td>Nieuwjaarsdag</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Andorra</td>
      <td>Nieuwjaarsdag</td>
      <td>Geen</td>
      <td>AD</td>
      <td>Nieuwjaarsdag</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Angola</td>
      <td>Nieuwjaarsdag</td>
      <td>Geen</td>
      <td>AO</td>
      <td>Nieuwjaarsdag</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Argentinië</td>
      <td>Nieuwjaarsdag</td>
      <td>Geen</td>
      <td>AR</td>
      <td>Nieuwjaarsdag</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



Wijzig de `countryRegionCode` namen `date` van de kolommen en en de kolom namen van de gegevens van de taxi en de tijd zodat deze kan worden gebruikt als sleutel. Voeg vervolgens de gegevens van de feestdag samen met de gegevens van de taxi door een left-koppeling te `merge()` gebruiken met de functie Pandas. Hiermee behoudt u alle records van `green_taxi_df`, maar voegt u in de gegevens van de feest dagen toe `datetime` , waar deze zich bevindt voor `"US"`de bijbehorende en `country_code`, in dit geval altijd. Bekijk een voor beeld van de gegevens om te controleren of ze correct zijn samengevoegd.

```python
holidays_df = holidays_df.rename(
    columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=[
                            "datetime", "country_code"])
taxi_holidays_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Leverancier</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73.921715</td>
      <td>40,766682</td>
      <td>-73.916908</td>
      <td>40,761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>VS</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>VS</td>
      <td>Waar</td>
      <td>Nieuwjaarsdag</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>VS</td>
      <td>Waar</td>
      <td>Nieuwjaarsdag</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>VS</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0,50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>VS</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>Verrijken met weer gegevens

Nu voegt u NOAA-Opper vlak gegevens toe aan de gegevens uit de taxi en de feestdag. Gebruik een vergelijk bare methode om de weer gegevens op te halen door één maand per keer te downloaden. Daarnaast geeft u de `cols` para meter op met een matrix met teken reeksen om de kolommen te filteren die u wilt downloaden. Dit is een zeer grote gegevensset met weers informatie over de hele wereld, dus voordat u elke maand toevoegt, filtert u de velden lat/long in bijna NYC met `query()` behulp van de functie op de data frame. Dit zorgt ervoor dat `weather_df` het niet te groot is.

```python
from azureml.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>temperatuur</th>
      <th>Breedtegraad</th>
      <th>precipDepth</th>
      <th>Lengtegraad</th>
      <th>datetime</th>
      <th>usaf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7.2</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24,0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>5.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>NaN</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Roep `pandas.Series.dt.normalize` opnieuw op het `datetime` veld in de weer gegevens zodat dit overeenkomt met de tijd `taxi_holidays_df`sleutel in. Verwijder de overbodige kolommen en filter de records waar de Tempe ratuur zich bevindt `NaN`.

Groepeer vervolgens de weer gegeven gegevens zodat u dagelijks geaggregeerde weer waarden hebt. Definieer een dict met `aggregations` de naam om te definiëren hoe elk veld op een dagelijks niveau moet worden geaggregeerd. Voor `snowDepth` `precipTime` `precipDepth` en `temperature` nemen het gemiddelde en voor en de dagelijkse maximum waarde. Gebruik de `groupby()` functie samen met de aggregaties om de gegevens te groeperen. Bekijk een voor beeld van de gegevens om ervoor te zorgen dat er één record per dag is.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max",
                "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperatuur</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5,197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2,567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3.846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0,123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-7,206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-0,896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3,180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4.384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6.710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24,0</td>
      <td>10,943655</td>
      <td>254,0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> In de voor beelden in deze zelf studie worden gegevens samengevoegd met behulp van Panda functies en aangepaste aggregaties, maar de SDK voor open gegevens sets bevat klassen die zijn ontworpen om eenvoudig gegevens sets samen te voegen en te verrijken. Bekijk het [notitie blok](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) voor code voorbeelden van deze ontwerp patronen.

### <a name="cleanse-data"></a>Gegevens opschonen

Voeg de gegevens over de taxi-en feestdag die u hebt voor bereid met de nieuwe weer gegevens toe. Deze keer hebt u alleen de `datetime` sleutel nodig en voert u opnieuw een left-koppeling van de gegevens uit. Voer de `describe()` functie uit op de nieuwe data frame om samenvattings statistieken voor elk veld weer te geven.

```python
taxi_holidays_weather_df = pd.merge(
    taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Leverancier</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperatuur</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>1671,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
    </tr>
    <tr>
      <th>gemiddelde</th>
      <td>1.786583</td>
      <td>6,576208</td>
      <td>1,582588</td>
      <td>20,505491</td>
      <td>84.936413</td>
      <td>-36,232825</td>
      <td>21,723144</td>
      <td>7.863018</td>
      <td>6,500000</td>
      <td>15,113708</td>
      <td>3,240250</td>
      <td>13,664125</td>
      <td>11,764141</td>
      <td>13,258875</td>
      <td>13,903524</td>
      <td>1056,644458</td>
    </tr>
    <tr>
      <th>Std</th>
      <td>0,409728</td>
      <td>9,086857</td>
      <td>2,418177</td>
      <td>108,847821</td>
      <td>70,678506</td>
      <td>37,650276</td>
      <td>19.104384</td>
      <td>10,648766</td>
      <td>3,452124</td>
      <td>8,485155</td>
      <td>1.956895</td>
      <td>6.650676</td>
      <td>15,651884</td>
      <td>10.339720</td>
      <td>9.474396</td>
      <td>2815.592754</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1,000000</td>
      <td>-60,000000</td>
      <td>-1,000000</td>
      <td>-74,179482</td>
      <td>0,000000</td>
      <td>-74,190704</td>
      <td>0,000000</td>
      <td>-52,800000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>0,330000</td>
      <td>-73.946680</td>
      <td>40,717712</td>
      <td>-73,945429</td>
      <td>1,770000</td>
      <td>1,000000</td>
      <td>3,750000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>6,620773</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>4,000000</td>
      <td>0,830000</td>
      <td>1,500000</td>
      <td>40,814129</td>
      <td>0,500000</td>
      <td>21,495000</td>
      <td>2,000000</td>
      <td>6,500000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,428571</td>
      <td>6,000000</td>
      <td>13,090753</td>
      <td>10,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>1,870000</td>
      <td>89,000000</td>
      <td>129,000000</td>
      <td>1,000000</td>
      <td>40,746146</td>
      <td>11,300000</td>
      <td>9,250000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>12,722222</td>
      <td>24,000000</td>
      <td>22,944737</td>
      <td>132,000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,000000</td>
      <td>460,000000</td>
      <td>51,950000</td>
      <td>265,000000</td>
      <td>265,000000</td>
      <td>6,000000</td>
      <td>58,600000</td>
      <td>498,000000</td>
      <td>12,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67.090909</td>
      <td>24,000000</td>
      <td>31,303665</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

Vanuit de samenvattings statistieken ziet u dat er verschillende velden zijn met uitbijters of waarden waardoor de nauw keurigheid van het model wordt verminderd. Filter eerst of de velden lat/long zich binnen dezelfde grenzen bevallen die u hebt gebruikt voor het filteren van weer gegevens. Het `tripDistance` veld bevat een aantal ongeldige gegevens, omdat de minimale waarde negatief is. Het `passengerCount` veld heeft ook ongeldige gegevens, waarbij de maximum waarde 210 reizigers is. Ten slotte heeft `totalAmount` het veld negatieve waarden, wat niet zinvol is in de context van ons model.

Filter deze afwijkingen met behulp van query functies en verwijder vervolgens de laatste paar kolommen die niet nodig zijn voor de training.

```python
final_df = taxi_holidays_weather_df.query(
    "pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query(
    "pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude",
                                  "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Roep `describe()` opnieuw aan met de gegevens om te controleren of het goed is gereinigd. U hebt nu een voor bereide en gereinigde set met gegevens over de taxi, feestdag en weers Taan die u voor machine learning model training kunt gebruiken.

```python
final_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Leverancier</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>temperatuur</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>1490,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
    </tr>
    <tr>
      <th>gemiddelde</th>
      <td>1.786910</td>
      <td>1,343476</td>
      <td>2,848488</td>
      <td>14,689039</td>
      <td>3.499788</td>
      <td>14,948916</td>
      <td>3.234254</td>
      <td>13,647344</td>
      <td>12,508581</td>
      <td>11,855929</td>
      <td>10,301433</td>
      <td>208.432384</td>
    </tr>
    <tr>
      <th>Std</th>
      <td>0,409508</td>
      <td>1,001232</td>
      <td>2,895960</td>
      <td>10,289832</td>
      <td>1.707865</td>
      <td>8,442438</td>
      <td>1,958477</td>
      <td>6,640280</td>
      <td>16.203195</td>
      <td>10,125701</td>
      <td>8,553512</td>
      <td>1284.892832</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,010000</td>
      <td>3,300000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,070000</td>
      <td>8,160000</td>
      <td>2,000000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>3.504580</td>
      <td>0,000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,900000</td>
      <td>11,300000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,250000</td>
      <td>6,000000</td>
      <td>10,168182</td>
      <td>3,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>3,550000</td>
      <td>17,800000</td>
      <td>5,000000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>15,647059</td>
      <td>24,000000</td>
      <td>16,966923</td>
      <td>41,000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,000000</td>
      <td>6,000000</td>
      <td>51,950000</td>
      <td>150,300000</td>
      <td>6,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67.090909</td>
      <td>24,000000</td>
      <td>26,524107</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Een model trainen

Nu gebruikt u de voor bereide gegevens om een geautomatiseerd machine learning model te trainen. Begin met het `final_df` splitsen van functies (X-waarden) en labels (y-waarde), wat voor dit model geldt voor de kosten van het taxi tarief.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Nu splitst u de gegevens in trainings- en testsets met behulp van de functie `train_test_split()` in de `scikit-learn`-bibliotheek. Met de parameter `test_size` wordt het percentage gegevens bepaald dat moet worden toegewezen aan testen. Met `random_state` de para meter wordt een Seed ingesteld op de generator voor wille keurige getallen, zodat de splitsingen van uw Train tests deterministisch zijn.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Werk ruimte laden en experiment configureren

Laad uw Azure machine learning service werkruimte met behulp van de `get()` functie met uw abonnement en werkruimte gegevens. Maak een experiment in uw werk ruimte om uw model uitvoeringen op te slaan en te controleren.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>",
                          name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Maak een configuratie object voor het experiment met behulp van de `AutoMLConfig` -klasse. U koppelt uw trainings gegevens en geeft daarnaast instellingen en para meters op die het trainings proces regelen. De para meters hebben de volgende doel einden:

* `task`: het type experiment dat moet worden uitgevoerd.
* `X`: trainings functies.
* `y`: Labels voor trainingen.
* `iterations`: het aantal iteraties dat moet worden uitgevoerd. Elke herhaling probeert combi Naties van verschillende functie normalisatie/standaardisering-methoden en verschillende modellen die gebruikmaken van meerdere afstemming-instellingen.
* `primary_metric`: primaire metriek voor optimalisatie tijdens model training. Het beste passend model wordt gekozen op basis van deze metrische gegevens.
* `preprocess`: Hiermee bepaalt u of het experiment de invoer gegevens kan voorverwerken (waarbij ontbrekende gegevens worden verwerkt, tekst naar numerieke waarde kan worden geconverteerd, enzovoort).
* `n_cross_validations`: Aantal kruisvalidaties dat moet worden uitgevoerd wanneer er geen validatiegegevens worden opgegeven.


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                             )
```

### <a name="submit-experiment"></a>Experiment verzenden

Dien het experiment in voor de training. Nadat u het experiment hebt verzonden, doorloopt het proces verschillende machine learning-algoritmen en hyperparameter-instellingen conform de gedefinieerde beperkingen. Hiermee wordt het beste model gekozen door de gedefinieerde nauwkeurigheids metriek te optimaliseren. Geef het `automl_config` object door aan het experiment. Stel de uitvoer in `True` op om de voortgang tijdens het experiment weer te geven.

Na het verzenden van het experiment ziet u live output voor het trainings proces. Voor elke herhaling ziet u het model type en de methode voor normalisatie/standaardisering, de uitvoerings duur en de nauw keurigheid van de training. In het veld `BEST` wordt de beste trainingsscore op basis van uw type metrische waarde bijgehouden.

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>Het aanwezige model ophalen

Aan het einde van alle trainingens iteraties maakt het geautomatiseerde machine learning proces een ensemble-algoritme van alle afzonderlijke uitvoeringen, hetzij met behulp van de bewerking of het stapelen. Haal de ingebouwde ensemble op in de `fitted_model`variabele en de best mogelijke afzonderlijke uitvoering in de `best_run`variabele.

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Nauw keurigheid van test model

Gebruik het model van de aanwezige ensemble om voor spellingen uit te voeren op de test gegevensset om te voors pellen van de taxi tarieven. De functie `predict()` maakt gebruik van het gepaste model en voor spelt de waarden van y, kosten voor `X_test` het taxi tarief voor de gegevensset.


```python
y_predict = fitted_model.predict(X_test.values)
```

Bereken het basis gemiddelde van de resultaten. Gebruik de `y_test` data frame en converteer deze naar een lijst `y_actual` om te vergelijken met de voorspelde waarden. Met de functie `mean_squared_error` wordt de gemiddelde gekwadrateerde fout berekend tussen twee matrices met waarden. Als u de vierkantswortel van het resultaat oplevert, treedt er een fout op in dezelfde eenheid als de variabele y, kosten. Het duidt ongeveer op hoe ver de voor spellingen van het taxi-ritbedrag uit de werkelijke tarieven afkomstig zijn, terwijl grote hoeveel heden zwaar worden verkleind.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Voer de volgende code uit om MAPE (het gemiddelde absolute foutpercentage) te berekenen met behulp van de volledige gegevenssets `y_actual` en `y_predict`. Met deze statistiek wordt een absoluut verschil tussen elke voorspelde en werkelijke waarde berekent en worden alle verschillen opgeteld. Vervolgens wordt deze som als een percentage van het totaal van de werkelijke waarden.


```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

Als u een redelijk klein voor beeld van gegevens hebt gebruikt ten opzichte van de volledige gegevensset (n = 11748), is de nauw keurigheid van het model redelijk hoog op 85%, met RMSE bij circa +-$4,00 fout bij het voors pellen van de prijs van een taxi. Als een mogelijke volgende stap om de nauw keurigheid te verbeteren, gaat u terug naar de tweede cel van dit notitie blok en verhoogt u de voorbeeld grootte van 2.000 records per maand en voert u het volledige experiment opnieuw uit om het model opnieuw te trainen met meer gegevens.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om de resources te gebruiken die u hebt gemaakt, verwijdert u deze, zodat er geen kosten in rekening worden gebracht.

1. Selecteer **Resourcegroepen** links in Azure Portal.
1. Selecteer de resourcegroep die u eerder hebt gemaakt uit de lijst.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Zie de Azure open data sets- [notitie blokken](https://github.com/Azure/OpenDatasetsNotebooks) voor meer code voorbeelden.
* Volg de [instructies](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) voor meer informatie over automatische machine learning in azure machine learning-service.
