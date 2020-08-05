---
title: 'Zelfstudie: aan de slag met het analyseren met behulp van Spark'
description: In deze zelfstudie leert u de basisstappen voor het instellen en gebruiken van Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5c6b35c1d9f00cae8fc688569e3a491679900995
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093479"
---
# <a name="analyze-with-apache-spark"></a>Analyseren met behulp van Apache Spark

In deze zelfstudie leert u de basisstappen voor het laden en analyseren van gegevens met Apache Spark voor Azure Synapse.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>De gegevens van NYC-taxi laden in de Apache Spark-database nyctaxi

Er zijn gegevens beschikbaar in een tabel in **SQLDB1**. Laad deze in een Apache Spark-database met de naam **nyctaxi**.

1. Ga in Synapse Studio naar de hub **Ontwikkelen**.
1. Selecteer **+**  > **Notebook**.
1. Stel bovenaan de notebook de waarde voor **Koppelen aan** in op **Spark1**.
1. Selecteer **Code toevoegen** om een notebookcodecel toe te voegen en plak de volgende tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Ga naar de hub **Data**, klik met de rechtermuisknop op **Databases** en selecteer **Vernieuwen**. U zou deze databases moeten zien:

    - **SQLDB1** (SQL-pool)
    - **nyctaxi** (Apache Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>De gegevens over de NYC-taxi met behulp van Apache Spark en notebooks analyseren

1. Ga terug naar uw notebook.
1. Maak een nieuwe codecel en voer de volgende tekst in. Voer vervolgens de cel uit om de NYC-taxigegevens weer te geven die zijn geladen in de Apache Spark-database **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Voer de volgende code uit om dezelfde analyse uit te voeren die we eerder met de SQL-pool **SQLDB1** hebben uitgevoerd. Met deze code worden ook de resultaten van de analyse opgeslagen in een tabel met de naam **nyctaxi.passengercountstats** en worden de resultaten gevisualiseerd.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Selecteer **Grafiek** in de celresultaten om de gevisualiseerde gegevens weer te geven.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Gegevensvisualisatie aanpassen met Apache Spark en notebooks

U kunt bepalen hoe grafieken worden weergegeven met behulp van notebooks. De volgende code toont een eenvoudig voorbeeld. De code maakt gebruik van de populaire bibliotheken **matplotlib** en **seaborn**. De code geeft hetzelfde soort lijndiagram weer als de SQL-query's die u eerder hebt uitgevoerd.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Gegevens uit een Apache Spark-tabel laden in een SQL-pooltabel

Eerder kopieerde u gegevens uit de tabel met SQL-pools **SQLDB1.dbo.Trip** naar de Apache Spark-tabel **nyctaxi.trip**. Vervolgens aggregeerde u de gegevens in de Apache Spark-tabel **nyctaxi.passengercountstats** met behulp van Apache Spark. Nu gaat u de gegevens uit **nyctaxi.passengercountstats** kopiëren naar een SQL-pooltabel met de naam **SQLDB1.dbo.PassengerCountStats**.

Voer de volgende cel uit in uw notebook. Hiermee wordt de geaggregeerde Apache Spark-tabel weer gekopieerd naar de SQL-pooltabel.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens analyseren in Storage](get-started-analyze-storage.md)


