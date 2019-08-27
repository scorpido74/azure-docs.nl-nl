---
title: 'Trans formaties: gegevens prep python SDK'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het transformeren van nettolading en opschonen van gegevens met Azure Machine Learning Data Prep SDK. Transformatie-methoden gebruiken voor kolommen toevoegen, filteren ongewenste rijen of kolommen en rekenen ontbrekende waarden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 339a4f93d45b3d3b3e242aa735ce4b737a9292f0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035963"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Gegevens transformeren met de Azure Machine Learning Data Prep SDK

In dit artikel leert u verschillende methoden voor het transformeren van gegevens met behulp `azureml-dataprep` van het pakket. Het pakket biedt functies die het eenvoudig maken om kolommen toe te voegen, ongewenste rijen of kolommen uit te filteren en ontbrekende waarden te wijzigen. Zie de volledige referentie documentatie voor het [pakket met de azureml-dataprep](https://aka.ms/data-prep-sdk).

> [!Important]
> Als u een nieuwe oplossing bouwt, probeert u de [Azure machine learning gegevens sets](how-to-explore-prepare-data.md) (preview) om uw gegevens te transformeren, momentopname gegevens op te slaan en gegevensset-definities op basis van een archief te bewaren. Gegevens sets is de volgende versie van de data prep SDK, die uitgebreide functionaliteit biedt voor het beheren van gegevens sets in AI-oplossingen.
> Als u het `azureml-dataprep` pakket gebruikt om een gegevensstroom te maken met uw trans formaties in plaats `azureml-datasets` van het pakket te gebruiken om een gegevensset te maken, kunt u later geen moment opnamen of versie gegevens sets gebruiken.

In deze procedure worden voor beelden weer gegeven voor de volgende taken:

- Met behulp van een expressie kolom toevoegen
- [Ontbrekende waarden worden toegerekend](#impute-missing-values)
- [Kolom afleiden per voorbeeld](#derive-column-by-example)
- [Filteren](#filtering)
- [Aangepaste Python-transformaties](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Met behulp van een expressie kolom toevoegen

De Azure Machine Learning Data Prep SDK bevat `substring` expressies die u kunt gebruiken om een waarde uit bestaande kolommen te berekenen en vervolgens samengesteld die waarde in een nieuwe kolom. In dit voorbeeld moet u gegevens laden en probeer kolommen toevoegen aan die invoergegevens.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||Id|Nummer van de aanvraag|Date|Blokkeren|IUCR|Het primaire Type|Description|Beschrijving van locatie|Aanhoudingsbevel|Binnenlandse|...|Ward|Community-gebied|Code van de FBI|X-coördinaat|Y-coördinaat|Jaar|Bijgewerkt op|Breedtegraad|Lengtegraad|Locatie|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07-05-2015 23:50:00 UUR|050XX N NEWLAND OPSLAAN|0820|DIEFSTAL|$500 EN KLIKT U ONDER|ADRES|false|false|...|41|10|06|1129230|1933315|2015|07-12-2015 12:42:46 UUR|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|07-05-2015 23:30:00 UUR|011XX W MORSE OPSLAAN|0460|ACCU|EENVOUDIGE|ADRES|false|true|...|49|1|08B|1167370|1946271|2015|07-12-2015 12:42:46 UUR|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|07-05-2015 23:20:00 UUR|121XX S FRONT AVE|0486|ACCU|EENVOUDIGE VAN BINNENLANDSE ACCU|ADRES|false|true|...|9|53|08B|||2015|07-12-2015 12:42:46 UUR|


Gebruik de `substring(start, length)` expressie voor het extraheren van het voorvoegsel van de kolom aanvraag en die tekenreeks in een nieuwe kolom geplaatst `Case Category`. Doorgeven van de `substring_expression` variabele de `expression` parameter maakt u een nieuwe berekende kolom die de expressie op elke record wordt uitgevoerd.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||Id|Nummer van de aanvraag|Aanvraagcategorie|Date|Blokkeren|IUCR|Het primaire Type|Description|Beschrijving van locatie|Aanhoudingsbevel|Binnenlandse|...|Ward|Community-gebied|Code van de FBI|X-coördinaat|Y-coördinaat|Jaar|Bijgewerkt op|Breedtegraad|Lengtegraad|Locatie|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|AAROM|07-05-2015 23:50:00 UUR|050XX N NEWLAND OPSLAAN|0820|DIEFSTAL|$500 EN KLIKT U ONDER|ADRES|false|false|...|41|10|06|1129230|1933315|2015|07-12-2015 12:42:46 UUR|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|AAROM|07-05-2015 23:30:00 UUR|011XX W MORSE OPSLAAN|0460|ACCU|EENVOUDIGE|ADRES|false|true|...|49|1|08B|1167370|1946271|2015|07-12-2015 12:42:46 UUR|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|AAROM|07-05-2015 23:20:00 UUR|121XX S FRONT AVE|0486|ACCU|EENVOUDIGE VAN BINNENLANDSE ACCU|ADRES|false|true|...|9|53|08B|||2015|07-12-2015 12:42:46 UUR|


Gebruik de `substring(start)` expressie voor het extraheren van alleen het nummer van de kolom aanvraag en maak een nieuwe kolom. Converteren naar een numerieke gegevens met behulp van de `to_number()` functioneren en de naam van de kolom als een parameter doorgeven.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Ontbrekende waarden worden toegerekend

De SDK kunt rekenen ontbrekende waarden in de opgegeven kolommen. In dit voorbeeld u waarden voor breedtegraad en lengtegraad laden en vervolgens probeert te rekenen ontbrekende waarden in de ingevoerde gegevens.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||Id|Aanhoudingsbevel|Breedtegraad|Lengtegraad|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

De derde record ontbreken waarden voor breedtegraad en lengtegraad. Als u deze ontbrekende waarden wilt toegerekend, [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) gebruikt u voor het leren van een vaste expressie. Deze kolommen met een van beide een berekende kunt rekenen `MIN`, `MAX`, `MEAN` waarde, of een `CUSTOM` waarde. Wanneer `group_by_columns` is opgegeven, worden ontbrekende waarden worden toegeschreven per groep met `MIN`, `MAX`, en `MEAN` berekend per groep.

Controleer de `MEAN` waarde van de kolom breedte met behulp van de [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) functie. Deze functie accepteert een matrix van kolommen in de `group_by_columns` parameter opgeven voor het aggregatieniveau van. De `summary_columns` parameter accepteert een `SummaryColumnsValue` aanroepen. Deze aanroep van de functie geeft de huidige kolomnaam, de nieuwe naam voor het berekende veld en de `SummaryFunction` om uit te voeren.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                             summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                        summary_column_name='Latitude_MEAN',
                                                                        summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Aanhoudingsbevel|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

De `MEAN` waarde van Latitude nauwkeurige uiterlijk, gebruikt u de `ImputeColumnArguments` functie voor het rekenen. Deze functie accepteert een `column_id` tekenreeks, en een `ReplaceValueFunction` om op te geven van het type impute. Voor de ontbrekende Lengtegraadwaarde, moet u het rekenen met 42 op basis van externe kennis.

Rekenen stappen kunnen een keten worden samengevoegd in een `ImputeMissingValuesBuilder` object met behulp van de functie builder `impute_missing_values()`. De `impute_columns` parameter accepteert een matrix van `ImputeColumnArguments` objecten. Roep de `learn()` functie voor het opslaan van de stappen impute, en vervolgens toepassen op een gegevensstroom object met `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                      group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||Id|Aanhoudingsbevel|Breedtegraad|Lengtegraad|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

Zoals weergegeven in het bovenstaande resultaat, de ontbrekende breedtegraad is toegerekende met de `MEAN` waarde van `Arrest=='false'` groep. De ontbrekende lengtegraad is toegerekende met 42.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Kolom afleiden per voorbeeld

Een van de meer geavanceerde hulpprogramma's in de SDK van Azure Machine Learning Data Prep is de mogelijkheid voor het afleiden van kolommen met behulp van voorbeelden van de gewenste resultaten. Hiermee kunt u de SDK een voorbeeld geven, zodat deze code voor het bereiken van de gewenste transformatie kunt genereren.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00 uur|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

Wordt ervan uitgegaan dat u wilt deelnemen aan dit bestand met een gegevensset waarin datum en tijd in een indeling zijn ' 10 maart 2018 | 2 AM - 4 AM'.

```python
builder = dflow.builders.derive_column_by_example(
    source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(
    source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5)
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Vanaf 1 januari 2015 12 AM - 2 AM|
|1|1/1/2015 1:00 uur|Vanaf 1 januari 2015 12 AM - 2 AM|
|2|1/1/2015 1:54|Vanaf 1 januari 2015 12 AM - 2 AM|
|3|1/1/2015 2:54|Vanaf 1 januari 2015 2 AM - 4 AM|
|4|1/1/2015 3:54|Vanaf 1 januari 2015 2 AM - 4 AM|

De bovenstaande code maakt eerst een opbouwfunctie voor de afgeleide kolom. Bieden van een matrix van kolommen in de gegevensbron te houden (`DATE`), en een naam voor de nieuwe kolom die moet worden toegevoegd. Als het eerste voorbeeld doorgeven in de tweede rij (index 1) en geef een verwachte waarde voor de afgeleide kolom.

Roep ten slotte `builder.preview(skip=30, count=5)` en de afgeleide kolom naast de bronkolom kunt zien. De indeling lijkt correct, maar u ziet alleen waarden voor dezelfde datum "1 januari 2015".

Nu in het aantal rijen dat u wilt doorgeven `skip` vanaf de bovenkant om te zien van rijen verder omlaag.

> [!NOTE]
> De functie Preview () slaat rijen over, maar de uitvoer index wordt niet opnieuw genummerd. In het onderstaande voor beeld komt de index 0 in de tabel overeen met index 30 in de gegevens stroom.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Vanaf 1 januari 2015 10 PM - 12 AM|
|1|1/1/2015 23:54|Vanaf 1 januari 2015 10 PM - 12 AM|
|2|1/1/2015 23:59|Vanaf 1 januari 2015 10 PM - 12 AM|
|3|1/2/2015 0:54|Vanaf 1 februari 2015 12 AM - 2 AM|
|4|1/2/2015 1:00 uur|Vanaf 1 februari 2015 12 AM - 2 AM|

Hier ziet u een probleem met de gegenereerde programma. Uitsluitend zijn gebaseerd op de een voorbeeld dat u hierboven hebt opgegeven, het programma afleiden hebt gekozen voor het parseren van de datum als 'Dag/maand/jaar', die niet wat u wilt dat in dit geval is. U kunt dit probleem oplossen door een specifieke index van een record te maken en een `add_example()` ander voor beeld `builder` te gebruiken met behulp van de functie voor de variabele.

```python
builder.add_example(
    source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Vanaf 1 januari 2015 10 PM - 12 AM|
|1|1/1/2015 23:54|Vanaf 1 januari 2015 10 PM - 12 AM|
|2|1/1/2015 23:59|Vanaf 1 januari 2015 10 PM - 12 AM|
|3|1/2/2015 0:54|Jan 2, 2015 12 AM - 2 AM|
|4|1/2/2015 1:00 uur|Jan 2, 2015 12 AM - 2 AM|

Op de juiste manier wordt ' 1/2/2015 ' als ' Jan 2, 2015 ' verwerkt. Als u echter meer dan index 76 van de afgeleide kolom ziet, ziet u dat de waarden aan het einde niets in de afgeleide kolom hebben.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 januari 2015 06.00-8 A.M.|
|1|1/3/2015 7:54|3 januari 2015 06.00-8 A.M.|
|2|1/29/2015 6:54|Geen|
|3|1/29/2015 7:00|Geen|
|4|1/29/2015 7:54|Geen|

```python
builder.add_example(
    source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 januari 2015 06.00-8 A.M.|
|1|1/3/2015 7:54|3 januari 2015 06.00-8 A.M.|
|2|1/29/2015 6:54|29 januari 2015 06.00-8 A.M.|
|3|1/29/2015 7:00|29 januari 2015 06.00-8 A.M.|
|4|1/29/2015 7:54|29 januari 2015 06.00-8 A.M.|

 Voor een overzicht van de huidige aanroep `list_examples()` van afgeleide voor beelden voor het object Builder.

```python
examples = builder.list_examples()
```

| |DATE|Voorbeeld|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00 uur|Vanaf 1 januari 2015 12 AM - 2 AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12 AM - 2 AM|-2|
|2|1/29/2015 20:54|29 januari 2015 8 PM - 10 PM|-3|


In bepaalde gevallen, als u voor beelden wilt verwijderen die onjuist zijn, kunt u een `example_row` van de Panda data frame of `example_id` -waarde door geven. Als u bijvoorbeeld uitvoert `builder.delete_example(example_id=-1)`, wordt het eerste transformatie voorbeeld verwijderd.


Aanroep `to_dataflow()` op de opbouw functie die een gegevens stroom retourneert waarbij de gewenste afgeleide kolommen worden toegevoegd.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filteren

De SDK bevat de methoden [`drop_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) en [`filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) u kunt kolommen of rijen filteren.

### <a name="initial-setup"></a>Eerste installatie

> [!Note]
> De URL in dit voor beeld is geen volledige URL. In plaats daarvan verwijst deze naar de demo-map in de blob. De volledige URL naar de gegevens is https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

In de zelf studie worden alle bestanden in de map geladen en wordt het resultaat geaggregeerd in green_df_raw en yellow_df_raw.

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(
    path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|
|1|2013-08-01 08:14:37|2013-08-01-09:09:06|N|1|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01-09:13:00 uur|2013-08-01-11:38:00 uur|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01-09:48:00 uur|2013-08-01-09:49:00 uur|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01-10:38:35|2013-08-01-10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Kolommen filteren

Kolommen wilt filteren, gebruikt u `drop_columns()`. Deze methode heeft een lijst met kolommen die moeten worden verwijderd of een complexere [`ColumnSelector`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py)argument met de naam.

#### <a name="filtering-columns-with-list-of-strings"></a>Filteren van kolommen met een lijst met tekenreeksen

In dit voorbeeld `drop_columns()` neemt een lijst met tekenreeksen. Elke tekenreeks moet exact overeenkomen met de gewenste kolom te verwijderen.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|Geen|
|1|2013-08-01 08:14:37|2013-08-01-09:09:06|0|0|0|0|1|.00|0|0|21,25|

#### <a name="filtering-columns-with-regex"></a>Filteren van kolommen met een reguliere expressie

U kunt ook de `ColumnSelector` expressie om te verwijderen van kolommen die overeenkomen met een bepaalde reguliere expressie. In dit voorbeeld u de kolommen die overeenkomen met de expressie neerzetten `Column*|.*longitude|.*latitude`.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector(
    'Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Geen|Geen|Geen|Geen|Geen|Geen|Geen|
|1|2013-08-01 08:14:37|2013-08-01-09:09:06|1|.00|0|0|21,25|

## <a name="filtering-rows"></a>Rijen filteren

Rijen filteren, gebruikt u `filter()`. Deze methode een Azure Machine Learning Data Prep SDK-expressie als een argument neemt en een nieuwe gegevensstroom met de rijen die de expressie wordt geëvalueerd als waar retourneert. Expressies zijn gebouwd met behulp van de expressie builders (`col`, `f_not`, `f_and`, `f_or`) en reguliere operators (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Rijen met eenvoudige expressies filteren

De opbouwfunctie voor expressies gebruiken `col`, geef de naam van de kolom als een tekenreeksargument `col('column_name')`. Deze expressie gebruiken in combinatie met een van de volgende standaardoperators >, <>, =, < =, ==,! = voor het bouwen van een expressie zoals `col('Tip_amount') > 0`. Tot slot geven de samengestelde expressie in de `filter()` functie.

In dit voorbeeld `dflow.filter(col('Tip_amount') > 0)` retourneert een nieuwe gegevensstroom met de rijen waarin de waarde van `Tip_amount` is groter dan 0.

> [!NOTE]
> `Tip_amount` eerst wordt geconverteerd naar een numerieke waarde, waarmee je een expressie die wordt vergeleken met andere numerieke waarden maken.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0,08|0|4,58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24 uur per dag|1|.00|0,30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Rijen met complexe expressies filteren

Combineren om te filteren met behulp van complexe expressies, een of meer eenvoudige expressies met de expressie-opbouwfuncties `f_not`, `f_and`, of `f_or`.

In dit voorbeeld `dflow.filter()` retourneert een nieuwe gegevensstroom met de rijen waar `'Passenger_count'` is kleiner dan 5 en `'Tolls_amount'` is groter dan 0.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(
    dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08-12:16:00 uur|2013-08-08-12:16:00 uur|1.0|.00|2.25|5,00|19.75|
|1|2013-08-12-14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

Het is ook mogelijk om te filteren van rijen die meer dan één opbouwfunctie voor het maken van een geneste expressie combineren.

> [!NOTE]
> `lpep_pickup_datetime` en `Lpep_dropoff_datetime` eerst worden geconverteerd naar datum/tijd, waarmee je een expressie die wordt vergeleken met andere datum / tijdwaarden maken.

```python
dflow = dflow.to_datetime(
    ['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013, 7, 1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|13-08-2013 06:11:06 + 00:00 uur|13-08-2013 06:30:28 + 00:00 uur|1.0|9,57|7.47|5.33|44.80|
|1|23-08-2013 12:28:20 + 00:00 uur|23-08-2013 12:50:28 + 00:00 uur|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Aangepaste Python-transformaties

Er wordt altijd zijn scenario's wanneer de eenvoudigste manier voor het maken van een transformatie is uw eigen script schrijven. De SDK biedt drie Extensiepunten die u voor aangepaste Python-scripts gebruiken kunt.

- Script van de nieuwe kolom
- Nieuw script filter
- Partitie transformeren

Elk van de extensies wordt ondersteund in de runtime scale-up en scale-out. Een groot voordeel van het gebruik van deze extensie is dat u niet nodig voor het ophalen van alle gegevens om te kunnen maken van een gegevensframe. Uw aangepaste Python code wordt uitgevoerd, net zoals andere transformaties, op schaal door partitie en meestal parallel.

### <a name="initial-data-preparation"></a>Initiële gegevens voor te bereiden

Begin met het laden van gegevens uit Azure Blob.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |

De gegevensset verkleinen en enkele eenvoudige trans formaties uitvoeren, waaronder het verwijderen van kolommen, het vervangen van waarden en het omzetten van typen.

```python
dflow = dflow.keep_columns(
    ['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(
    columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1.017100e + 10|Geen|
|1|ALABAMA|Hale County|1.017100e + 10|Geen|

Zoek naar null-waarden met behulp van de volgende filters.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1.017100e + 10|Geen|
|1|ALABAMA|Hale County|1.017100e + 10|Geen|

### <a name="transform-partition"></a>Partitie transformeren

Gebruiken [`transform_partition()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) om alle Null-waarden te vervangen door 0. Deze code wordt uitgevoerd door partitie, niet op in één keer de volledige gegevensset. Dit betekent dat op een grote gegevensset met deze code kan parallel worden uitgevoerd als de runtime de gegevens, partitie door partitie verwerkt.

Het Python-script moet een aangeroepen functie definiëren `transform()` die neemt twee argumenten, `df` en `index`. De `df` argument is een pandas dataframe dat de gegevens voor de partitie bevat en de `index` argument is een unieke id van de partitie. De omzetfunctie het doorgegeven gegevensframe volledig kunt bewerken, maar een dataframe moet retourneren. Alle bibliotheken die het Python-script importeert, moeten zich in de omgeving waar de gegevensstroom wordt uitgevoerd.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1.017100e + 10|0.0|
|1|ALABAMA|Hale County|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Script van de nieuwe kolom

U kunt een python-script gebruiken om een nieuwe kolom te maken met de naam van de regio en de naam van de status en om de naam van de status te kapitaliseren. Gebruik hiervoor de [`new_script_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) methode voor de gegevens stroom.

Het Python-script moet een aangeroepen functie definiëren `newvalue()` die een één argument `row`. De `row` argument is een dict (`key`: naam van kolom, `val`: huidige waarde) en voor elke rij in de gegevensset worden doorgegeven aan deze functie. Deze functie moet een waarde die moet worden gebruikt in de nieuwe kolom retourneren. Alle bibliotheken die het Python-script importeert, moeten zich in de omgeving waar de gegevensstroom wordt uitgevoerd.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale regio, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale County|Hale regio, Alabama|1.017100e + 10|0.0|

### <a name="new-script-filter"></a>Nieuw Script Filter

Bouw een python-expressie [`new_script_filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) die wordt gebruikt om de gegevensset te filteren op alleen rijen waarvoor ' inademing ' `county_state` zich niet in de nieuwe kolom bevindt. De expressie retourneert `True` als we willen houden van de rij en `False` verwijderen van de rij.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson regio, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson County|Jefferson regio, Alabama|1.019200e + 10|0.0|

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [referentie documentatie](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#dataprep) voor de Azure machine learning data prep SDK voor meer informatie.
