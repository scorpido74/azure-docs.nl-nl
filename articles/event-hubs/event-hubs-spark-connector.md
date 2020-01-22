---
title: Integreren met Apache Spark - Azure Eventhubs | Microsoft Docs
description: In dit artikel wordt beschreven hoe u kunt integreren met Apache Spark om Structured streamen met Event Hubs in te scha kelen.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311679"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Apache Spark integreren met Azure Eventhubs

Azure Event Hubs wordt naadloos geïntegreerd met [Apache Spark](https://spark.apache.org/) gedistribueerd streamingtoepassingen bouwen inschakelen. Deze integratie ondersteunt [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html), en [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). De Event Hubs-connector voor Apache Spark is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-spark). Deze bibliotheek is ook beschikbaar voor gebruik in Maven-projecten uit de [Maven Central Repository](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Dit artikel wordt beschreven hoe u een continue toepassing in maakt [Azure Databricks](https://azure.microsoft.com/services/databricks/). Hoewel dit artikel wordt gebruikgemaakt van Azure Databricks, Spark-clusters zijn ook beschikbaar met [HDInsight](../hdinsight/spark/apache-spark-overview.md).

Het voorbeeld in dit artikel maakt gebruik van twee Scala-notebooks: één voor het streaming-gebeurtenissen vanaf een event hub en andere voor het verzenden van gebeurtenissen naar deze.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u niet hebt, [Maak een gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Een Event Hubs-instantie. Als u niet hebt, [maakt u er een](event-hubs-create.md).
* Een [Azure Databricks](https://azure.microsoft.com/services/databricks/) exemplaar. Als u niet hebt, [maakt u er een](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Maken van een bibliotheek met behulp van maven-coördinaten](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Gebeurtenissen van de Stream van uw event hub met behulp van de volgende code:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
De volgende code verzendt gebeurtenissen naar uw event hub met de batch-API's van Spark. U kunt ook een streaming-query voor het verzenden van gebeurtenissen naar de event hub schrijven:

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Volgende stappen

Nu u hoe u weet voor het instellen van een schaalbare en fouttolerante stream met behulp van de Event Hubs-Connector voor Apache Spark. Meer informatie over het gebruik van Event Hubs met Structured Streaming en Spark Streaming door deze koppelingen volgen:

* [Structured Streaming + Azure Event Hubs-Integratiehandleiding](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark-Streaming + Event Hubs-Integratiehandleiding](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
