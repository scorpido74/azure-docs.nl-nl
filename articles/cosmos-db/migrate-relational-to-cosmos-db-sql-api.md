---
title: Een-op-enkele relationele gegevens migreren naar Azure Cosmos DB SQL API
description: Meer informatie over het omgaan met complexe gegevensmigratie voor een-op-een-paar relaties in SQL API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896634"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Een-op-enkele relationele gegevens migreren naar Azure Cosmos DB SQL API-account

Om te migreren van een relationele database naar Azure Cosmos DB SQL API, kan het nodig zijn om wijzigingen aan te brengen in het gegevensmodel voor optimalisatie.

Een veelvoorkomende transformatie is het denormaliseren van gegevens door gerelateerde subitems in één JSON-document in te sluiten. Hier bekijken we een aantal opties hiervoor met Azure Data Factory of Azure Databricks. Voor algemene richtlijnen voor gegevensmodellering voor Cosmos DB raadpleegt u [Gegevensmodellering in Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Voorbeeldscenario

Stel dat we de volgende twee tabellen in onze SQL-database, Orders en OrderDetails hebben.


![Bestelgegevens](./media/migrate-relational-to-cosmos-sql-api/orders.png)

We willen deze een-op-enkele relatie combineren in één JSON-document tijdens de migratie. Om dit te doen, kunnen we een T-SQL-query maken met behulp van "VOOR JSON" zoals hieronder:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

De resultaten van deze query ziet er als volgt uit: 

![Bestelgegevens](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


Idealiter wilt u één AAzure Data Factory -kopieeractiviteit (ADF) gebruiken om SQL-gegevens als bron op te vragen en de uitvoer rechtstreeks naar Azure Cosmos DB-sink te schrijven als de juiste JSON-objecten. Momenteel is het niet mogelijk om de benodigde JSON-transformatie in één kopieeractiviteit uit te voeren. Als we proberen de resultaten van de bovenstaande query te kopiëren naar een Azure Cosmos DB SQL API-container, zien we het veld OrderDetails als een tekenreekseigenschap van ons document, in plaats van de verwachte JSON-array.

We kunnen deze huidige beperking op een van de volgende manieren omzeilen:

* **Gebruik Azure Data Factory met twee kopieeractiviteiten:** 
  1. Json-geformatteerde gegevens uit SQL ophalen naar een tekstbestand op een tussenliggende blob-opslaglocatie en 
  2. Gegevens uit het JSON-tekstbestand laden naar een container in Azure Cosmos DB.

* **Gebruik Azure Databricks om uit SQL te lezen en naar Azure Cosmos DB te schrijven** - we presenteren hier twee opties.


Laten we eens kijken naar deze benaderingen in meer detail:

## <a name="azure-data-factory"></a>Azure Data Factory

Hoewel we OrderDetails niet kunnen insluiten als een JSON-array in het db-document van bestemming Cosmos, kunnen we het probleem oplossen met behulp van twee afzonderlijke kopieeractiviteiten.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Activiteits#1 kopiëren: SqlJsonToBlobText

Voor de brongegevens gebruiken we een SQL-query om het resultaat als één kolom te krijgen met één JSON-object (die de volgorde vertegenwoordigt) per rij met behulp van de SQL Server OPENJSON- en JSON PATH-mogelijkheden:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF-kopie](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


Voor de gootsteen van de sqlJsonToBlobText-kopieeractiviteit kiezen we 'Delimited Text' en wijzen we deze naar een specifieke@concatmap in Azure Blob Storage met een dynamisch gegenereerde unieke bestandsnaam (bijvoorbeeld ' (pijplijn(). RunId,'.json').
Aangezien ons tekstbestand niet echt "afgebakend" is en we niet willen dat het wordt ontleed in afzonderlijke kolommen met komma's en de dubbele aanhalingstekens willen behouden ("), stellen we "Kolomscheidingbegrenzer" in op een tabblad ("\t") - of een ander teken dat niet in de gegevens voorkomt - en "Teken citaat" naar "Geen citaat karakter".

![ADF-kopie](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>Activiteits#2 kopiëren: BlobJsonToCosmos

Vervolgens wijzigen we onze ADF-pijplijn door de tweede kopieeractiviteit toe te voegen die er in Azure Blob Storage uitziet voor het tekstbestand dat is gemaakt door de eerste activiteit. Het verwerkt het als "JSON" bron om aan de gootsteen van de Kosmos DB als één document per JSON-rij in het tekstdossier in te voegen.

![ADF-kopie](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Optioneel voegen we ook een activiteit 'Verwijderen' toe aan de pijplijn, zodat alle vorige bestanden die nog in de map /Orders/map staan, worden verwijderd. Onze ADF-pijplijn ziet er nu ongeveer als volgt uit:

![ADF-kopie](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

Nadat we de bovenstaande pijplijn hebben geactiveerd, zien we een bestand dat is gemaakt in onze intermediaire Azure Blob Storage-locatie met één JSON-object per rij:

![ADF-kopie](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

We zien ook ordersdocumenten met goed ingesloten OrderDetails ingevoegd in onze Cosmos DB-collectie:

![ADF-kopie](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

We kunnen Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) ook gebruiken om de gegevens van onze SQL Database-bron naar de Azure Cosmos DB-bestemming te kopiëren zonder de intermediaire tekst/JSON-bestanden in Azure Blob Storage te maken. 

> [!NOTE]
> Voor de duidelijkheid en eenvoud bevatten de onderstaande codefragmenten expliciet dummy-databasewachtwoorden, maar u moet altijd azure databricks-geheimen gebruiken.
>

Ten eerste maken en koppelen we de vereiste [SQL-connector-](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) en [Azure Cosmos DB-connectorbibliotheken](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) aan ons Azure Databricks-cluster. Start het cluster opnieuw om ervoor te zorgen dat bibliotheken worden geladen.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Vervolgens presenteren we twee monsters, voor Scala en Python. 

### <a name="scala"></a>Scala
Hier krijgen we de resultaten van de SQL-query met "FOR JSON" uitvoer in een DataFrame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

Vervolgens maken we verbinding met onze Cosmos DB-database en -collectie:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Tot slot definiëren we ons schema en gebruiken we from_json om het DataFrame toe te passen voordat het wordt opgeslagen in de CosmosDB-verzameling.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

Als alternatieve benadering moet u mogelijk JSON-transformaties uitvoeren in Spark (als de brondatabase geen ondersteuning biedt voor "FOR JSON" of een vergelijkbare bewerking), of als u parallelle bewerkingen wilt gebruiken voor een zeer grote gegevensset. Hier presenteren we een PySpark monster. Begin met het configureren van de bron- en doeldatabaseverbindingen in de eerste cel:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Vervolgens zullen we de brondatabase (in dit geval SQL Server) opvragen voor zowel de order- als orderdetailrecords, waardoor de resultaten in Spark Dataframes worden opgenomen. We maken ook een lijst met alle order--geïdentificeerde gegevens en een threadpool voor parallelle bewerkingen:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Maak vervolgens een functie voor het schrijven van orders in de doelSQL-API-verzameling. Deze functie filtert alle orderdetails voor de opgegeven order-id, zet ze om in een JSON-array en voegt de array in een JSON-document in dat we voor die volgorde in de doelSQL-API-verzameling zullen schrijven:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Ten slotte zullen we het bovenstaande met behulp van een kaart functie op de thread pool, uit te voeren in parallel, passeren in de lijst van orde-iD's die we eerder gemaakt:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
In beide benaderingen, aan het eind, moeten we goed opgeslagen embedded OrderDetails binnen elke Order document in Cosmos DB collectie:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gegevensmodellering in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* Meer informatie over [het modelleren en beheren van gegevens op Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
