---
title: Apache Spark machine learning pipeline maken - Azure HDInsight
description: Gebruik de Apache Spark-machine learning-bibliotheek om gegevenspijplijnen te maken in Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494662"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Een machine learning-pijplijn in Apache Spark maken

De schaalbare machine learning-bibliotheek (MLlib) van Apache Spark brengt modelleringsmogelijkheden naar een gedistribueerde omgeving. Het Spark-pakket [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) is een set API's op hoog niveau die zijn gebouwd op DataFrames. Deze API's helpen u bij het maken en afstemmen van praktische machine learning-pijplijnen.  *Spark machine learning* verwijst naar deze MLlib DataFrame-gebaseerde API, niet naar de oudere RDD-gebaseerde pipeline API.

Een ml-pijplijn (machine learning) is een complete workflow die meerdere machine learning-algoritmen combineert. Er kunnen veel stappen nodig zijn om gegevens te verwerken en te leren, waarvoor een reeks algoritmen nodig is. Pijplijnen definiëren de fasen en het bestellen van een machine learning-proces. In MLlib worden fasen van een pijplijn weergegeven door een specifieke reeks PipelineStages, waarbij een transformator en een schatter elk taken uitvoeren.

Een transformator is een algoritme dat het ene `transform()` DataFrame naar het andere transformeert met behulp van de methode. Een functietransformator kan bijvoorbeeld een kolom van een DataFrame lezen, deze toewijzen aan een andere kolom en een nieuw Gegevensframe uitzetten met de toegewezen kolom eraan toegevoegd.

Een Estimator is een abstractie van leeralgoritmen en is verantwoordelijk voor het aanbrengen of trainen op een dataset om een Transformer te produceren. Een schatter implementeert `fit()`een methode met de naam , die een DataFrame accepteert en een DataFrame produceert, dat een transformator is.

Elke stateloze instantie van een transformatieof een chatter heeft zijn eigen unieke id, die wordt gebruikt bij het opgeven van parameters. Beide gebruiken een uniforme API voor het opgeven van deze parameters.

## <a name="pipeline-example"></a>Voorbeeld van pijplijn

Om een praktisch gebruik van een ML-pijplijn `HVAC.csv` aan te tonen, wordt in dit voorbeeld het voorbeeldgegevensbestand gebruikt dat vooraf is geladen op de standaardopslag voor uw HDInsight-cluster, azure storage of Data Lake Storage. Als u de inhoud van het `/HdiSamples/HdiSamples/SensorSampleData/hvac` bestand wilt weergeven, navigeert u naar de map. `HVAC.csv`bevat een aantal tijden met zowel doel- als werkelijke temperaturen voor HVAC *(verwarming, ventilatie en airconditioning)* systemen in verschillende gebouwen. Het doel is om het model te trainen op de gegevens, en produceren een voorspelde temperatuur voor een bepaald gebouw.

De volgende code:

1. Hiermee `LabeledDocument`definieert u `BuildingID` `SystemInfo` een , die de , (de id en leeftijd van een systeem) en een `label` (1.0 als het gebouw te warm is, 0,0 anders) opslaat.
2. Hiermee maakt u een `parseDocument` aangepaste parserfunctie die een lijn (rij) met gegevens neemt en bepaalt of het gebouw "heet" is door de doeltemperatuur te vergelijken met de werkelijke temperatuur.
3. Hiermee past u de parser toe bij het extraheren van de brongegevens.
4. Hiermee maakt u trainingsgegevens.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Deze voorbeeldpijplijn heeft `Tokenizer` drie `HashingTF` fasen: en `Logistic Regression` (zowel Transformers) en (een Schatter).  De geëxtraheerde en ontleedde gegevens in het `training` DataFrame stromen door de pijplijn wanneer `pipeline.fit(training)` wordt aangeroepen.

1. De eerste `Tokenizer`fase splitst `SystemInfo` de invoerkolom (bestaande uit de systeem-id `words` en leeftijdswaarden) op in een uitvoerkolom. Deze `words` nieuwe kolom wordt toegevoegd aan het DataFrame. 
2. De tweede `HashingTF`fase zet `words` de nieuwe kolom om in functievectoren. Deze `features` nieuwe kolom wordt toegevoegd aan het DataFrame. Deze eerste twee fasen zijn Transformers. 
3. De derde `LogisticRegression`fase, , is een Schatter, `LogisticRegression.fit()` en dus `LogisticRegressionModel`de pijpleiding roept de methode om een te produceren . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Als u `words` de `features` nieuwe en `Tokenizer` `HashingTF` kolommen wilt zien die `LogisticRegression` door de `PipelineModel.transform()` transformatoren en een voorbeeld van de schatter zijn toegevoegd, voert u een methode uit op het oorspronkelijke Gegevensframe. In productiecode zou de volgende stap zijn om in een test DataFrame te slagen om de training te valideren.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

Het `model` object kan nu worden gebruikt om voorspellingen te doen. Zie [Apache Spark-machine learning-toepassingen bouwen op Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md)voor het volledige voorbeeld van deze machine learning-toepassing en stapsgewijze instructies voor het uitvoeren ervan.

## <a name="see-also"></a>Zie ook

* [Data Science met Scala en Apache Spark op Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
