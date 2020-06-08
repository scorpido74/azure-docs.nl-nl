---
title: Query uitvoeren op de analytische opslag van Azure Cosmos DB (preview) met Apache Spark voor Azure Synapse Analytics
description: Een query uitvoeren op analytische opslag in Azure Cosmos DB met Apache Spark voor Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 5c19a3f808b85ba9e34d3304251fe8acb21204ce
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700204"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Query uitvoeren op de analytische opslag van Azure Cosmos DB (preview) met Apache Spark voor Azure Synapse Analytics

Dit artikel bevat enkele voorbeelden van de manier waarop u via Synapse-bewegingen kunt werken met de analytische opslag. Bewegingen worden weergegeven wanneer u met de rechtermuisknop op een container klikt. Met bewegingen kunt u snel code genereren en deze aanpassen aan uw behoeften. Bewegingen zijn ook ideaal om met één klik gegevens te ontdekken.

## <a name="load-to-dataframe"></a>Laden in Dataframe

In deze stap leest u gegevens uit de analytische opslag van Azure Cosmos DB naar een Spark-dataframe. Er worden tien rijen uit de dataframe weergegeven met de naam ***df***. Zodra uw gegevens zich in de dataframe bevinden, kunt u aanvullende analyse uitvoeren.

Deze bewerking heeft geen invloed op de transactionele opslag.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

De equivalente codebeweging in **Scala** is de volgende code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Spark-tabel maken

Met deze beweging maakt u een Spark-tabel die verwijst naar de container die u hebt geselecteerd. Deze bewerking veroorzaakt geen gegevensverplaatsing. Als u besluit deze tabel te verwijderen, worden de onderliggende container, en de bijbehorende analytische opslag, niet beïnvloed. 

Dit scenario is handig voor hergebruik van tabellen via hulpprogramma's van derden en om de gegevens voor de uitvoering toegankelijk te maken.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Dataframe naar een container schrijven

Met deze beweging schrijft u een dataframe naar een container. Deze bewerking heeft invloed op de transactionele prestaties en verbruikt aanvraageenheden. Het gebruik van transactionele prestaties van Azure Cosmos DB is ideaal voor schrijftransacties. Zorg ervoor dat u **UWDATAFRAME-** vervangt door de dataframe waarnaar u wilt terugschrijven.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

De equivalente codebeweging in **Scala** is de volgende code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Streaming-dataframe laden vanuit een container
Met deze beweging gebruikt u de mogelijkheid van Spark-streaming om gegevens uit een container in een dataframe te laden. De gegevens worden opgeslagen in het primaire Data Lake-account (en bestandssysteem) dat u hebt gekoppeld aan de werkruimte. 

Als de map */localReadCheckpointFolder* niet is gemaakt, gebeurt dit automatisch. Deze bewerking heeft invloed op de transactionele prestaties van Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

De equivalente codebeweging in **Scala** is de volgende code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Streaming-dataframe naar een container schrijven
Met deze beweging schrijft u een streaming-dataframe naar de Azure Cosmos DB-container die u hebt geselecteerd. Als de map */localReadCheckpointFolder* niet is gemaakt, gebeurt dit automatisch. Deze bewerking heeft invloed op de transactionele prestaties van Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

De equivalente codebeweging in **Scala** is de volgende code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Volgende stappen

* [Krijg inzicht in wat wordt ondersteund tussen Synapse en Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Verbinding maken met Synapse Link voor Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
