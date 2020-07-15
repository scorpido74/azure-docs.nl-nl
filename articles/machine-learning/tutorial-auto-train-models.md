---
title: 'Regressiezelfstudie: Geautomatiseerde machine learning'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie leert u hoe u een model voor machine learning genereert met behulp van geautomatiseerde machine learning. Azure Machine Learning kan voorbewerking van gegevens, selectie van algoritmes en selectie van hyperparameters automatisch voor u uitvoeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.custom: tracking-python
ms.openlocfilehash: 595440dc727f3faf1fa475266825a671f00d9153
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143608"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Zelfstudie: Geautomatiseerde machine learning gebruiken om taxitarieven te voorspellen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie gebruikt u geautomatiseerde machine learning in Azure Machine Learning om een regressiemodel te maken om de taxikosten van een NYC taxi te voorspellen. Dit proces gebruikt trainingsgegevens en configuratie-instellingen en doorloopt automatisch combinaties van verschillende methoden voor het normaliseren/standaardiseren van functies, modellen en instellingen van hyperparameters om het beste model te bepalen.

![Stroomdiagram](./media/tutorial-auto-train-models/flow2.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens downloaden, transformeren en opschonen met behulp van Azure Open Datasets
> * Een regressiemodel trainen met geautomatiseerde machine learning
> * De nauwkeurigheid van een model berekenen

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Vereisten

* Doorloop de [installatiezelfstudie](tutorial-1st-experiment-sdk-setup.md) als u nog geen Azure Machine Learning-werkruimte of virtuele notebook-machine hebt.
* Wanneer u de installatiezelfstudie hebt voltooid, opent u de notebook *tutorials/regression-automl-nyc-taxi-data/regression-automated-ml.ipynb* met dezelfde notebookserver.

Deze zelf studie is ook beschikbaar op [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) als u deze wilt uitvoeren in uw eigen [lokale omgeving](how-to-configure-environment.md#local). Voer `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` uit om de vereiste pakketten te downloaden.

## <a name="download-and-prepare-data"></a>Gegevens downloaden en voorbereiden

Importeer de benodigde pakketten. Het Open Datasets-pakket bevat een klasse voor elke gegevensbron (`NycTlcGreen` bijvoorbeeld) om eenvoudig op datumparameters te filteren voordat u downloadt.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Maak eerst een dataframe om de taxigegevens op te slaan. Als u in een niet-Spark-omgeving werkt, kunt u met Azure Open Datasets slechts gegevens voor één maand tegelijk downloaden met bepaalde klassen om `MemoryError` met grote gegevenssets te voorkomen.

Voor het downloaden van taxigegevens worden iteratief de gegevens voor één maand tegelijk opgehaald en voordat deze worden toegevoegd aan `green_taxi_df`, worden willekeurig 2000 records uit elke maand gehaald om bloating van het dataframe te voorkomen. Bekijk daarna een voorbeeld van de gegevens.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; } .dataframe tbody tr th { vertical-align: top; } .dataframe thead th { text-align: right; } </style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
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
      <th>extra</th>
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
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>15,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>16,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>2</td>
      <td>4,50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>4,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>5,00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>2</td>
      <td>6,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>7,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>2</td>
      <td>6,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>1</td>
      <td>7,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>nan</td>
      <td>9,55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>2</td>
      <td>5,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 rijen × 23 kolommen</p>
</div>


Nu de initiële gegevens zijn geladen, definieert u een functie om verschillende tijdkenmerken te maken op basis van het uit het veld Datum/tijd. Hiermee maakt u nieuwe velden voor het maandnummer, de dag van de maand, de dag van de week en het uur van de dag, waarna het model op tijd gebaseerde seizoensgebondenheid kan meenemen als factor. Gebruik de functie `apply()` voor het dataframe om de functie `build_time_features()` iteratief toe te passen op elke rij in de taxigegevens.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; } .dataframe tbody tr th { vertical-align: top; } .dataframe thead th { text-align: right; } </style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
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
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,88</td>
      <td>40,84</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>16,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,96</td>
      <td>40,81</td>
      <td>-73,96</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>4,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,81</td>
      <td>40,70</td>
      <td>-73,82</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,92</td>
      <td>40,76</td>
      <td>-73,92</td>
      <td>...</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>5,00</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>7,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,88</td>
      <td>40,76</td>
      <td>-73,87</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,96</td>
      <td>40,72</td>
      <td>-73,91</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,95</td>
      <td>...</td>
      <td>0,3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>nan</td>
      <td>9,55</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Geen</td>
      <td>Geen</td>
      <td>-73,94</td>
      <td>40,71</td>
      <td>-73,94</td>
      <td>...</td>
      <td>0,3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>nan</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 rijen × 27 kolommen</p>
</div>

Verwijder enkele kolommen die u niet nodig hebt voor de training of het maken van extra kenmerken.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Gegevens opschonen

Voer de functie `describe()` uit op het nieuwe dataframe om samenvattingsstatistieken voor elk veld weer te geven.

```python
green_taxi_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; } .dataframe tbody tr th { vertical-align: top; } .dataframe thead th { text-align: right; } </style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1,78</td>
      <td>1,37</td>
      <td>2,87</td>
      <td>-73,83</td>
      <td>40,69</td>
      <td>-73,84</td>
      <td>40,70</td>
      <td>14,75</td>
      <td>6,50</td>
      <td>15,13</td>
      <td>3,27</td>
      <td>13,52</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0,41</td>
      <td>1,04</td>
      <td>2,93</td>
      <td>2,76</td>
      <td>1,52</td>
      <td>2,61</td>
      <td>1,44</td>
      <td>12,08</td>
      <td>3,45</td>
      <td>8,45</td>
      <td>1,95</td>
      <td>6,83</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>-74,66</td>
      <td>0,00</td>
      <td>-74,66</td>
      <td>0,00</td>
      <td>-300,00</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1,06</td>
      <td>-73,96</td>
      <td>40,70</td>
      <td>-73,97</td>
      <td>40,70</td>
      <td>7,80</td>
      <td>3,75</td>
      <td>8,00</td>
      <td>2,00</td>
      <td>9,00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1,90</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>-73,94</td>
      <td>40,75</td>
      <td>11,30</td>
      <td>6,50</td>
      <td>15,00</td>
      <td>3,00</td>
      <td>15,00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>3,60</td>
      <td>-73,92</td>
      <td>40,80</td>
      <td>-73,91</td>
      <td>40,79</td>
      <td>17,80</td>
      <td>9,25</td>
      <td>22,00</td>
      <td>5,00</td>
      <td>19,00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,00</td>
      <td>9,00</td>
      <td>97,57</td>
      <td>0,00</td>
      <td>41,93</td>
      <td>0,00</td>
      <td>41,94</td>
      <td>450,00</td>
      <td>12,00</td>
      <td>30,00</td>
      <td>6,00</td>
      <td>23,00</td>
    </tr>
  </tbody>
</table>
</div>


In de samenvattingsstatistieken ziet u dat er verschillende velden zijn met uitbijters of waarden die de nauwkeurigheid van het model doen afnemen. Filter eerst de velden met lengte-/breedtegraden zodat deze binnen de grenzen van het gebied Manhattan vallen. Zo kunt u de langere taxiritten of ritten die uitbijters zijn ten opzichte van andere kenmerken, uitfilteren.

Stel vervolgens een filter in om het veld `tripDistance` in te stellen op groter dan nul, maar kleiner dan 31 mijl (de haversine-afstand tussen de twee lengte-/breedtegraadparen). Dit elimineert lange uitbijters met inconsistente ritkosten.

En tot slot ziet u dat het veld `totalAmount` negatieve waarden voor de taxitarieven bevat. Dit heeft weinig zin in de context van ons model en het veld `passengerCount` bevat ongeldige gegevens waar de minimumwaarden nul zijn.

Filter ook deze afwijkingen eruit met behulp van queryfuncties en verwijder vervolgens de laatste paar kolommen die niet nodig zijn voor de training.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Roep `describe()` opnieuw aan voor de gegevens om te controleren of ze goed zijn opgeschoond. U hebt nu een voorbereide en opgeschoonde set taxi-, feestdagen- en weersgegevens die u voor de training van een machine learning-model kunt gebruiken.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Werkruimte configureren

Maak een werkruimte-object van de bestaande werkruimte. Een [Werkruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) is een klasse die uw Azure-abonnement en resourcegegevens accepteert. Hier wordt ook een cloudresource gemaakt om de uitvoeringen van uw model te controleren en bij te houden. `Workspace.from_config()` leest het bestand **config.json** en laadt de verificatiegegevens in een object met de naam `ws`. `ws` wordt gebruikt in de rest van de code in deze zelfstudie.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Gegevens splitsen in sets voor trainen en voor testen

Splitst de gegevens in trainings- en testsets met behulp van de functie `train_test_split` in de `scikit-learn`-bibliotheek. Met deze functie worden de gegevens verdeeld in de gegevensset x (**kenmerken**) voor het trainen van het model, en de gegevensset y (**te voorspellen waarden**) voor testen.

Met de parameter `test_size` wordt het percentage gegevens bepaald dat moet worden toegewezen aan testen. Met de parameter `random_state` wordt de willekeurige generator ingesteld, zodat de verdeling tussen trainen en testen deterministisch is.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Het doel van deze stap is om gegevenspunten voor het testen van het voltooide model te verkrijgen die nog niet zijn gebruikt voor het trainen van het model. Zo kan de ware nauwkeurigheid van het model worden gemeten.

Met andere woorden, een goed getraind model moet in staat zijn nauwkeurige voorspellingen te doen voor gegevens die nog niet eerder de revue hebben gepasseerd. U hebt nu gegevens voorbereid voor het automatisch trainen van een machine learning-model.

## <a name="automatically-train-a-model"></a>Automatisch een model trainen

Als u automatisch een model wilt trainen, voert u de volgende stappen uit:
1. Definieer de instellingen voor het uitvoeren van het experiment. Koppel uw trainingsgegevens aan de configuratie en wijzig de instellingen voor het trainingsproces.
1. Verzend het experiment om het model af te stemmen. Nadat u het experiment hebt verzonden, doorloopt het proces verschillende machine learning-algoritmen en hyperparameter-instellingen conform de gedefinieerde beperkingen. Het optimale model worden gekozen door een metrische nauwkeurigheidswaarde te optimaliseren.

### <a name="define-training-settings"></a>Trainingsinstellingen definiëren

Definieer de experimentparameter en modelinstellingen voor training. Bekijk de volledige lijst met [instellingen](how-to-configure-auto-train.md). Het verzenden van het experiment met deze standaardinstellingen duurt ongeveer 5-20 minuten. Als u een kortere uitvoeringstijd wilt, verkleint u de waarde van parameter `experiment_timeout_minutes`.

|Eigenschap| Waarde in deze zelfstudie |Beschrijving|
|----|----|---|
|**iteration_timeout_minutes**|2|Tijdslimiet in minuten voor elke iteratie. Verklein deze waarde als u de totale uitvoeringstijd wilt verminderen.|
|**experiment_timeout_minutes**|20|Maximale tijdsduur in minuten dat de combinatie van alle iteraties voordat het experiment wordt beëindigd, kan duren.|
|**enable_early_stopping**|True|Vlag om vroegtijdige beëindiging in te schakelen als de score niet op korte termijn verbetert.|
|**primary_metric**| spearman_correlation | De metrische gegevens die u wilt optimaliseren. Het optimale model wordt gekozen op basis van deze metrische waarde.|
|**featurization**| auto | Door **auto** te gebruiken, kunnen de invoergegevens (afhandeling van ontbrekende gegevens, conversie van tekst naar numerieke waarden, enzovoort) voor het experiment worden voorverwerkt.|
|**uitgebreidheid**| logging.INFO | Hiermee bepaalt u het niveau van logboekregistratie.|
|**n_cross_validations**|5|Aantal kruisvalidaties dat moet worden uitgevoerd wanneer er geen validatiegegevens worden opgegeven.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Gebruik de gedefinieerde trainingsinstellingen als `**kwargs`-parameter voor een `AutoMLConfig`-object. Geef uw trainingsgegevens en het type model op. In dat geval is dat `regression`.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> Geautomatiseerde machine learning-voorverwerkingsstappen (kenmerknormalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarden, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model voor voorspellingen gebruikt, worden dezelfde voorverwerkingsstappen die tijdens de training zijn toegepast, automatisch toegepast op uw invoergegevens.

### <a name="train-the-automatic-regression-model"></a>Het automatische regressiemodel trainen

Maak een experimentobject in uw werkruimte. Een experiment fungeert als een container voor uw afzonderlijke uitvoeringen. Geef het gedefinieerde `automl_config`-object door aan het experiment, en stel de uitvoer in op `True` om de voortgang weer te geven tijdens de uitvoering.

Nadat het experiment is gestart, wordt de weergegeven uitvoer live bijgewerkt terwijl het experiment wordt uitgevoerd. Voor elke iteratie ziet u het modeltype, de uitvoeringsduur en de nauwkeurigheid van de training. In het veld `BEST` wordt de beste trainingsscore op basis van uw type metrische waarde bijgehouden.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>De resultaten verkennen

Bekijk de resultaten van de automatische training met een [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Met de widget kunt u een grafiek en tabel van alle afzonderlijke uitvoeringsiteraties bekijken, samen met metrische nauwkeurigheidsgegevens en metagegevens van de training. Daarnaast kunt u met behulp van de vervolgkeuzelijst filteren op andere metrische nauwkeurigheidsgegevens dan de eerste metrische gegevens.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Uitvoeringsdetails van Jupyter-widget](./media/tutorial-auto-train-models/automl-dash-output.png)
![Tekengebied Jupyter-widget](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Het beste model ophalen

Selecteer het beste model in uw iteraties. De methode `get_output` retourneert de beste uitvoering en het aangepaste model voor de laatste aangepaste aanroep. U kunt de overloads op `get_output` gebruiken om de beste uitvoering en het aangepaste model op te halen voor alle geregistreerde metrische gegevens of voor een bepaalde interatie.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Nauwkeurigheid van het beste model testen

Gebruik het beste model om voorspellingen uit te voeren op de testgegevensset om taxitarieven te voorspellen. De functie `predict` maakt gebruik van het beste model en voorspelt de waarden van y (**ritkosten**) uit de gegevensset `x_test` voorspeld. Druk de eerste 10 voorspelde kostenwaarden uit `y_predict` af.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Bereken de `root mean squared error` van de resultaten. Converteer het gegevensframe `y_test` in een lijst voor vergelijking met de voorspelde waarden. Met de functie `mean_squared_error` wordt de gemiddelde gekwadrateerde fout berekend tussen twee matrices met waarden. De vierkantswortel van het resultaat veroorzaakt een fout in dezelfde eenheden als de variabele y (**kosten**). Deze waarde geeft aan in welke mate de voorspelde taxitarieven ongeveer afwijken van de werkelijke tarieven.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Voer de volgende code uit om MAPE (het gemiddelde absolute foutpercentage) te berekenen met behulp van de volledige gegevenssets `y_actual` en `y_predict`. Met deze statistiek wordt een absoluut verschil tussen elke voorspelde en werkelijke waarde berekent en worden alle verschillen opgeteld. Vervolgens wordt die som weergegeven als een percentage van het totaal van de werkelijke waarden.

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

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


In de twee metrische nauwkeurigheidsgegevens van de voorspellingen kunt u zien dat het model redelijk goed is in het voorspellen van taxitarieven op basis van de functies van de gegevensset; gewoonlijk $4,00 te veel of te weinig en een foutmarge van circa 15%.

In het traditionele ontwikkelingsproces voor een Machine Learning-model vergt het veel resourcecapaciteit, domeinkennis en tijd om verschillende modellen uit te voeren en de resultaten daarvan met elkaar te vergelijken. Geautomatiseerde machine learning is een uitstekende manier om in korte tijd veel verschillende modellen voor uw scenario te testen.

## <a name="clean-up-resources"></a>Resources opschonen

Voer deze sectie niet uit als u van plan bent andere Azure Machine Learning-zelfstudies uit te voeren.

### <a name="stop-the-compute-instance"></a>Het rekenproces stoppen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om gebruik te maken van de resources die u hebt gemaakt, kunt u ze verwijderen zodat er geen kosten voor in rekening worden gebracht.

1. Selecteer **Resourcegroepen** links in Azure Portal.
1. Selecteer de resourcegroep die u eerder hebt gemaakt uit de lijst.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Bekijk de eigenschappen van de werkruimte en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie over geautomatiseerde machine learning hebt u het volgende gedaan:

> [!div class="checklist"]
> * U hebt een werkruimte geconfigureerd en gegevens voorbereid voor een experiment.
> * U hebt getraind met behulp van een lokaal geautomatiseerd regressiemodel met aangepaste parameters.
> * U hebt trainingsresultaten verkend en gecontroleerd.

[Uw model implementeren](tutorial-deploy-models-with-aml.md) met Azure Machine Learning.
