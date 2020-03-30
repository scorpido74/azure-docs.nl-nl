---
title: 'Gegevenstransformatie: gegevens & transformeren '
description: Meer informatie over het transformeren van gegevens of het verwerken van gegevens in Azure Data Factory met Hadoop, Machine Learning of Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b3e2db9b9769dee7599a2446b272e04cc0bedf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703393"
---
# <a name="transform-data-in-azure-data-factory"></a>Gegevens transformeren in Azure Data Factory
> [!div class="op_single_selector"]
> * [Component](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop-streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Opgeslagen procedure](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET aangepast](data-factory-use-custom-activities.md)

## <a name="overview"></a>Overzicht
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [gegevenstransformatieactiviteiten in Data Factory](../transform-data.md).

In dit artikel worden gegevenstransformatieactiviteiten in Azure Data Factory uitgelegd die u gebruiken om uw ruwe gegevens om te zetten en te verwerken tot voorspellingen en inzichten. Een transformatieactiviteit wordt uitgevoerd in een computeromgeving, zoals Azure HDInsight-cluster of een Azure Batch. Het biedt links naar artikelen met gedetailleerde informatie over elke transformatieactiviteit.

Data Factory ondersteunt de volgende gegevenstransformatieactiviteiten die afzonderlijk of geketend kunnen worden aan [pijplijnen.](data-factory-create-pipelines.md)

> [!NOTE]
> Zie Een pijplijn maken met een artikel [over de transformatie van Hive](data-factory-build-your-first-pipeline.md) voor een walkthrough met stapsgewijze instructies.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-activiteit
De HDInsight Hive-activiteit in een Data Factory-pijplijn voert Hive-query's uit op uw eigen of on-demand HDInsight-cluster op basis van Windows/Linux. Zie het artikel [Hive Activity](data-factory-hive-activity.md) voor meer informatie over deze activiteit. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig activiteit
De HDInsight Pig-activiteit in een Data Factory-pijplijn voert Pig-query's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster. Zie het artikel [Varkensactiviteit](data-factory-pig-activity.md) voor meer informatie over deze activiteit. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapActiviteit verminderen
De HDInsight MapReduce activiteit in een Data Factory-pijplijn voert MapReduce-programma's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster. Zie [Artikel Activiteit verkleinen Voor](data-factory-map-reduce.md) meer informatie over deze activiteit.

## <a name="hdinsight-streaming-activity"></a>HDInsight-streamingactiviteit
De HDInsight Streaming Activity in een Data Factory-pijplijn voert Hadoop Streaming-programma's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster. Zie [HDInsight Streaming-activiteit](data-factory-hadoop-streaming-activity.md) voor meer informatie over deze activiteit.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-activiteit
De HDInsight Spark-activiteit in een Data Factory-pijplijn voert Spark-programma's uit op uw eigen HDInsight-cluster. Zie [Spark-programma's van Azure Data Factory voor](data-factory-spark.md)meer informatie aanroepen. 

## <a name="machine-learning-activities"></a>Machine Learning-activiteiten
Met Azure Data Factory u eenvoudig pijplijnen maken die gebruikmaken van een gepubliceerde Azure Machine Learning-webservice voor voorspellende analyses. Met behulp van de [batchuitvoeringsactiviteit](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) in een Azure Data Factory-pijplijn u een Machine Learning-webservice aanroepen om voorspellingen te doen over de gegevens in batch.

Na verloop van tijd moeten de voorspellende modellen in de Machine Learning-scoringsexperimenten worden omgeschoold met behulp van nieuwe invoergegevenssets. Nadat u klaar bent met omscholing, wilt u de scorewebservice bijwerken met het omgeschoold Machine Learning-model. U de [resourceactiviteit bijwerken](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) gebruiken om de webservice bij te werken met het nieuw getrainde model.  

Zie [Machine Learning-activiteiten gebruiken](data-factory-azure-ml-batch-execution-activity.md) voor meer informatie over deze Machine Learning-activiteiten. 

## <a name="stored-procedure-activity"></a>Opgeslagen procedureactiviteit
U de sql-server-activiteit opgeslagen procedure in een Data Factory-pijplijn gebruiken om een opgeslagen procedure aan te roepen in een van de volgende gegevensopslag: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database in uw onderneming of een Azure VM. Zie [Artikel Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md) voor meer informatie.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
Data Lake Analytics U-SQL Activity voert een U-SQL-script uit op een Azure Data Lake Analytics-cluster. Zie [artikel Data Analytics U-SQL Activity](data-factory-usql-activity.md) voor meer informatie. 

## <a name="net-custom-activity"></a>Aangepaste .NET-activiteit
Als u gegevens moet transformeren op een manier die niet wordt ondersteund door Data Factory, u een aangepaste activiteit maken met uw eigen gegevensverwerkingslogica en de activiteit in de pijplijn gebruiken. U de aangepaste .NET-activiteit zo configureren dat deze wordt uitgevoerd met een Azure Batch-service of een Azure HDInsight-cluster. Zie [Artikel Aangepaste activiteiten gebruiken](data-factory-use-custom-activities.md) voor meer informatie. 

U kunt een aangepaste activiteit maken om R-scripts uit te voeren op uw HDInsight-cluster waarop R is geïnstalleerd. Zie [R-script uitvoeren met behulp van Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Compute-omgevingen
U maakt een gekoppelde service voor de compute-omgeving en gebruikt vervolgens de gekoppelde service bij het definiëren van een transformatieactiviteit. Er zijn twee typen compute-omgevingen die worden ondersteund door Data Factory. 

1. **On-demand**: In dit geval wordt de computeromgeving volledig beheerd door Data Factory. Het wordt automatisch gemaakt door de Service Gegevensfabriek voordat een taak wordt ingediend om gegevens te verwerken en wordt verwijderd wanneer de taak is voltooid. U gedetailleerde instellingen configureren en beheren van de on-demand compute-omgeving voor taakuitvoering, clusterbeheer en bootstrapping-acties. 
2. **Bring Your Own**: In dit geval u uw eigen computeromgeving (bijvoorbeeld HDInsight cluster) registreren als gekoppelde service in Data Factory. De computeromgeving wordt door u beheerd en de Data Factory-service gebruikt deze om de activiteiten uit te voeren. 

Zie [artikel Compute Linked Services](data-factory-compute-linked-services.md) voor meer informatie over compute services die worden ondersteund door Data Factory. 

## <a name="summary"></a>Samenvatting
Azure Data Factory ondersteunt de volgende gegevenstransformatieactiviteiten en de compute-omgevingen voor de activiteiten. De transformatieactiviteiten kunnen afzonderlijk aan pijplijnen worden toegevoegd of met een andere activiteit worden geketend.

| Activiteiten voor gegevenstransformatie | Compute-omgeving |
|:--- |:--- |
| [Component](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop-streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning-activiteiten: batchuitvoering en resources bijwerken](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Opgeslagen procedure](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse of SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] of Azure Batch |

