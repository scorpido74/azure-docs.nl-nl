---
title: Integreren met Apache Spark - Azure Event Hubs | Microsoft Documenten
description: In dit artikel ziet u hoe u integreren met Apache Spark om gestructureerde streaming met gebeurtenishubs mogelijk te maken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311679"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Apache Spark integreren met Azure Event Hubs

Azure Event Hubs integreert naadloos met [Apache Spark](https://spark.apache.org/) om gedistribueerde streamingtoepassingen te kunnen bouwen. Deze integratie ondersteunt [Spark Core,](https://spark.apache.org/docs/latest/rdd-programming-guide.html) [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)en [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). De Event Hubs-connector voor Apache Spark is beschikbaar op [GitHub.](https://github.com/Azure/azure-event-hubs-spark) Deze bibliotheek is ook beschikbaar voor gebruik in Maven projecten van de [Maven Central Repository.](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)

In dit artikel wordt beschreven hoe u een doorlopende toepassing maakt in [Azure Databricks.](https://azure.microsoft.com/services/databricks/) Hoewel dit artikel Azure Databricks gebruikt, zijn Spark-clusters ook beschikbaar met [HDInsight.](../hdinsight/spark/apache-spark-overview.md)

In het voorbeeld in dit artikel worden twee Scala-notitieblokken gebruikt: een voor het streamen van gebeurtenissen vanuit een gebeurtenishub en een andere voor het terugsturen van gebeurtenissen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als je nog geen account hebt, [maak dan een gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)aan.
* Een instantie Gebeurtenishubs. Als u er geen hebt, [maakt u er een.](event-hubs-create.md)
* Een [azure databricks-exemplaar.](https://azure.microsoft.com/services/databricks/) Als u er geen hebt, [maakt u er een.](../azure-databricks/quickstart-create-databricks-workspace-portal.md)
* [Maak een bibliotheek met mavencoördinaten:](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package) `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Gebeurtenissen streamen vanuit uw gebeurtenishub met de volgende code:

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
De volgende code stuurt gebeurtenissen naar uw gebeurtenishub met de Spark-batch-API's. U ook een streamingquery schrijven om gebeurtenissen naar de gebeurtenishub te verzenden:

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

Nu weet u hoe u een schaalbare, fouttolerante stream instellen met behulp van de Event Hubs Connector voor Apache Spark. Meer informatie over het gebruik van gebeurtenishubs met gestructureerde streaming en Spark-streaming door deze links te volgen:

* [Handleiding voor gestructureerde streaming + Azure Event Hubs-integratie](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + Integratiegids voor gebeurtenishubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
