---
title: Voorbeeld van machine learning met Spark MLlib op HDInsight - Azure
description: Meer informatie over het gebruik van Spark MLlib om een machine learning-app te maken die een gegevensset analyseert met behulp van classificatie via logistieke regressie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 26695df299ba5d0f50c8f271b5da99284a8d6764
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531130"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Apache Spark MLlib gebruiken om een machine learning-toepassing te bouwen en een gegevensset te analyseren

Meer informatie over het gebruik van Apache Spark [MLlib](https://spark.apache.org/mllib/) om een machine learning-toepassing te maken. De toepassing zal voorspellende analyse op een open gegevensset doen. Vanuit de ingebouwde machine learning-bibliotheken van Spark maakt dit voorbeeld gebruik van *classificatie* tot logistieke regressie.

MLlib is een kernsparkbibliotheek die veel hulpprogramma's biedt die nuttig zijn voor machine learning-taken, zoals:

* Classificatie
* Regressie
* Clustering
* Modelleren
* Enkelvoudwaardedecompositie (SVD) en hoofdcomponentanalyse (PCA)
* Hypothesetesten en het berekenen van steekproefstatistieken

## <a name="understand-classification-and-logistic-regression"></a>Begrijp classificatie en logistieke regressie

*Classificatie*, een populaire machine learning taak, is het proces van het sorteren van invoergegevens in categorieën. Het is de taak van een classificatiealgoritme om erachter te komen hoe u labels toewijzen aan invoergegevens die u verstrekt. U bijvoorbeeld een machine learning-algoritme gebruiken dat voorraadinformatie als invoer accepteert. Dan verdeelt de voorraad in twee categorieën: aandelen die je moet verkopen en de voorraden die je moet houden.

Logistieke regressie is het algoritme dat u gebruikt voor classificatie. Spark's logistieke regressie API is handig voor *binaire classificatie,* of classificeren van invoergegevens in een van de twee groepen. Zie [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)voor meer informatie over logistieke regressies.

Kortom, het proces van logistieke regressie levert een *logistieke functie*op. Gebruik de functie om de waarschijnlijkheid te voorspellen dat een invoervector in de ene groep of de andere thuishoort.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Voorbeeld van voorspellende analyse over voedselinspectiegegevens

In dit voorbeeld gebruikt u Spark om een aantal voorspellende analyses uit te voeren op voedselinspectiegegevens **(Food_Inspections1.csv).** Gegevens verkregen via de [City of Chicago data portal](https://data.cityofchicago.org/). Deze dataset bevat informatie over inspecties van voedselinstellingen die in Chicago zijn uitgevoerd. Inclusief informatie over elke vestiging, de geconstateerde overtredingen (indien van toepassing) en de resultaten van de inspectie. Het CSV-gegevensbestand is al beschikbaar in het opslagaccount dat is gekoppeld aan het cluster op **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

In de onderstaande stappen ontwikkel je een model om te zien wat er nodig is om een voedselinspectie door te voeren of te mislukken.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Een Apache Spark MLlib machine learning-app maken

1. Maak een Jupyter-notebook met behulp van de PySpark-kernel. Zie [Een Jupyter-notebook maken](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook) voor de instructies.

2. Importeer de typen die nodig zijn voor deze toepassing. Kopieer en plak de volgende code in een lege cel en druk op **Shift + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    Vanwege de PySpark-kernel hoeft u geen contexten expliciet te maken. De Spark- en Hive-contexten worden automatisch gemaakt wanneer u de eerste codecel uitvoert.

## <a name="construct-the-input-dataframe"></a>Het invoergegevensframe construeren

Gebruik de context Spark om de ruwe CSV-gegevens als ongestructureerde tekst in het geheugen te halen. Gebruik vervolgens de CSV-bibliotheek van Python om elke regel van de gegevens te ontleden.

1. Voer de volgende regels uit om een Robuuste Gedistribueerde gegevensset (RDD) te maken door de invoergegevens te importeren en te ontleden.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Voer de volgende code uit om één rij uit de RDD op te halen, zodat u het gegevensschema bekijken:

    ```PySpark
    inspections.take(1)
    ```

    Dit is de uitvoer:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    De uitvoer geeft u een idee van het schema van het invoerbestand. Het bevat de naam van elk etablissement, en het type inrichting. Ook het adres, de gegevens van de inspecties, en de locatie, onder andere dingen.

3. Voer de volgende code uit om een gegevensframe *(df)* en een tijdelijke tabel *(CountResults)* te maken met een paar kolommen die nuttig zijn voor de voorspellende analyse. `sqlContext`wordt gebruikt om transformaties op gestructureerde gegevens uit te brengen.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    De vier kolommen die van belang zijn in het dataframe zijn **ID**, **naam**, **resultaten**en **schendingen**.

4. Voer de volgende code uit om een klein voorbeeld van de gegevens te krijgen:

    ```PySpark
    df.show(5)
    ```

    Dit is de uitvoer:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>De gegevens begrijpen

Laten we beginnen om een idee te krijgen van wat de gegevensset bevat. 

1. Voer de volgende code uit om de verschillende waarden in de **resultatenkolom** weer te geven:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Dit is de uitvoer:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Voer de volgende code uit om de verdeling van deze resultaten te visualiseren:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    De `%%sql` magie `-o countResultsdf` die wordt gevolgd door zorgt ervoor dat de uitvoer van de query lokaal wordt gehandhaafd op de Jupyter-server (meestal de headnode van het cluster). De uitvoer wordt gehandhaafd als een [Pandas-gegevensframe](https://pandas.pydata.org/) met de opgegeven **naamcountResultsdf**. Zie `%%sql` [Kernels beschikbaar op Jupyter-laptops met Apache Spark HDInsight-clusters](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)voor meer informatie over de magie en andere magie die beschikbaar is met de PySpark-kernel.

    Dit is de uitvoer:

    ![SQL-queryuitvoer](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-queryuitvoer")

3. U [ook Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), een bibliotheek die wordt gebruikt om visualisatie van gegevens te construeren, gebruiken om een plot te maken. Omdat de plot moet worden gemaakt van het lokaal aanhoudende **countResultsdf-gegevensframe,** moet het codefragment beginnen met de `%%local` magie. Deze actie zorgt ervoor dat de code lokaal wordt uitgevoerd op de Jupyter-server.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Om een voedselinspectieresultaat te voorspellen, moet u een model ontwikkelen dat op de schendingen wordt gebaseerd. Omdat logistieke regressie een binaire classificatiemethode is, is het zinvol om de resultaatgegevens te groeperen in twee categorieën: **Fail** and **Pass:**

   - Pass
       - Pass
       - Pass w/ voorwaarden
   - Mislukt
       - Mislukt
   - Verwijderen
       - Bedrijf niet gevestigd
       - Out of Business

     Gegevens met de andere resultaten ('Business Not Located' of 'Out of Business') zijn niet nuttig en vormen sowieso een klein percentage van de resultaten.

4. Voer de volgende code uit om`df`het bestaande gegevensframe om te zetten in een nieuw gegevensframe waarbij elke inspectie wordt weergegeven als een label-violations-paar. In dit geval, `0.0` een label van vertegenwoordigt `1.0` een mislukking, een `-1.0` label van vertegenwoordigt een succes, en een label van vertegenwoordigt een aantal resultaten naast deze twee resultaten.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Voer de volgende code uit om één rij van de gelabelde gegevens weer te geven:

    ```PySpark
    labeledData.take(1)
    ```

    Dit is de uitvoer:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Een logistiek regressiemodel maken op basis van het invoergegevensframe

De uiteindelijke taak is het converteren van de gelabelde gegevens. Zet de gegevens om in een indeling die kan worden geanalyseerd door logistieke regressie. De invoer naar een logistiek regressiealgoritme heeft een set vectorparen met *labelfunctie*nodig. Waar de "functievector" een vector van getallen is die het invoerpunt vertegenwoordigen. Dus, je moet de kolom "schendingen" converteren, die semi-gestructureerd is en veel opmerkingen bevat in vrije tekst. Converteer de kolom naar een array met echte getallen die een machine gemakkelijk kan begrijpen.

Een standaard machine learning benadering voor het verwerken van natuurlijke taal is het toewijzen van elk afzonderlijk woord een "index". Geef vervolgens een vector door aan het machine learning-algoritme. Zodanig dat de waarde van elke index de relatieve frequentie van dat woord in de tekenreeks bevat.

MLlib biedt een eenvoudige manier om deze operatie te doen. Ten eerste, "tokenize" elke schendingen string om de individuele woorden in elke string te krijgen. Gebruik vervolgens `HashingTF` een om elke set tokens om te zetten in een functievector die vervolgens kan worden doorgegeven aan het logistieke regressiealgoritme om een model te construeren. U voert al deze stappen in volgorde uit met behulp van een "pijplijn".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Het model evalueren met een andere gegevensset

U het model dat u eerder hebt gemaakt gebruiken om te *voorspellen* wat de resultaten van nieuwe inspecties zullen zijn. De voorspellingen zijn gebaseerd op de overtredingen die werden waargenomen. U hebt dit model getraind op de gegevensset **Food_Inspections1.csv**. U een tweede gegevensset, **Food_Inspections2.csv,** gebruiken om de sterkte van dit model op de nieuwe gegevens te *evalueren.* Deze tweede gegevensset (**Food_Inspections2.csv**) bevindt zich in de standaardopslagcontainer die aan het cluster is gekoppeld.

1. Voer de volgende code uit om een nieuw gegevensframe te maken, **predictionsDf** dat de voorspelling bevat die door het model wordt gegenereerd. Het fragment maakt ook een tijdelijke tabel met de naam **Voorspellingen** op basis van het gegevensframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    U ziet een uitvoer zoals de volgende tekst:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Kijk naar een van de voorspellingen. Voer dit fragment uit:

    ```PySpark
    predictionsDf.take(1)
    ```

   Er is een voorspelling voor de eerste vermelding in de testgegevensset.

1. De `model.transform()` methode past dezelfde transformatie toe op nieuwe gegevens met hetzelfde schema en komt tot een voorspelling van het classificeren van de gegevens. U een aantal statistieken om een idee van hoe de voorspellingen waren te krijgen:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    De uitvoer ziet eruit als de volgende tekst:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Het gebruik van logistieke regressie met Spark geeft u een model van de relatie tussen schendingen beschrijvingen in het Engels. En of een bepaald bedrijf zou slagen of falen een voedselinspectie.

## <a name="create-a-visual-representation-of-the-prediction"></a>Een visuele weergave van de voorspelling maken

U nu een definitieve visualisatie maken om u te helpen redeneren over de resultaten van deze test.

1. U begint met het extraheren van de verschillende voorspellingen en resultaten uit de tijdelijke tabel **Voorspellingen** die eerder zijn gemaakt. De volgende query's scheiden de uitvoer als *true_positive*, *false_positive*, *true_negative*en *false_negative*. In de onderstaande query's schakelt `-q` u visualisatie uit door `-o`de uitvoer (met behulp van) te gebruiken als dataframes die vervolgens met de `%%local` magie kunnen worden gebruikt.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Gebruik ten slotte het volgende fragment om het plot te genereren met **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:

    ![Spark machine learning applicatie output - pie chart percentages van mislukte voedselinspecties.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark machine learning resultaatoutput")

    In deze grafiek verwijst een "positief" resultaat naar de mislukte voedselinspectie, terwijl een negatief resultaat verwijst naar een geslaagde inspectie.

## <a name="shut-down-the-notebook"></a>Het notitieblok afsluiten

Nadat u klaar bent met het uitvoeren van de toepassing, moet u het notitieblok afsluiten om de bronnen vrij te geven. Selecteer hiervoor **Sluiten en stoppen** in het menu **Bestand** van het notebook. Met deze actie wordt het notebook afgesloten en gesloten.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve data-analyse met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
