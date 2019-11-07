---
title: 'Gegevens transformeren met behulp van Azure Data Factory '
description: Meer informatie over het transformeren van gegevens of het verwerken van gegevens in Azure Data Factory met Hadoop, Machine Learning of Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 97077b9f72de8792e6aba1d72ff34b9185b2d998
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683810"
---
# <a name="transform-data-in-azure-data-factory"></a>Gegevens in Azure Data Factory transformeren
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight-streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Opgeslagen procedure](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks-notebook](transform-data-databricks-notebook.md)
> * [Databricks jar](transform-data-databricks-jar.md)
> * [Databricks python](transform-data-databricks-python.md)
> * [.NET aangepast](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Overzicht
In dit artikel wordt uitgelegd hoe u activiteiten voor gegevens transformatie in Azure Data Factory kunt gebruiken om uw onbewerkte gegevens te transformeren en te verwerken in voor spellingen en inzichten. Een transformatie activiteit wordt uitgevoerd in een computer omgeving zoals Azure HDInsight-cluster of een Azure Batch. Het bevat koppelingen naar artikelen met gedetailleerde informatie over elke transformatie activiteit.

Data Factory ondersteunt de volgende activiteiten voor gegevens transformatie die afzonderlijk kunnen worden toegevoegd aan [pijp lijnen](concepts-pipelines-activities.md) of moeten worden gekoppeld aan een andere activiteit.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Systeem eigen transformeren in Azure Data Factory met gegevens stromen

### <a name="mapping-data-flows"></a>Toewijzing gegevensstromen

Het toewijzen van gegevens stromen zijn visueel ontworpen gegevens transformaties in Azure Data Factory. Met gegevens stromen kunnen data engineers grafische gegevens transformatie logica ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten binnen Azure Data Factory pijp lijnen die gebruikmaken van geschaalde Spark-clusters. Gegevens stroom activiteiten kunnen worden uitgevoerd via bestaande Data Factory plannings-, beheer-, stroom-en bewakings mogelijkheden. Zie [gegevens stromen toewijzen](concepts-data-flow-overview.md)voor meer informatie.

### <a name="wrangling-data-flows"></a>Wrangling-gegevens stromen

Met Wrangling-gegevens stromen in Azure Data Factory kunt u de gegevens voorbereiding voor code gratis uitvoeren op de Cloud schaal. Wrangling-gegevens stromen kunnen worden geïntegreerd met [Power query online](https://docs.microsoft.com/power-query/) en maken Power query M-functies beschikbaar voor gegevens Wrangling op Cloud schaal via Spark-uitvoering. Zie [wrangling Data Flows](wrangling-data-flow-overview.md)(Engelstalig) voor meer informatie.

## <a name="external-transformations"></a>Externe transformaties

### <a name="hdinsight-hive-activity"></a>HDInsight Hive-activiteit
De HDInsight Hive-activiteit in een Data Factory pijp lijn voert Hive-query's uit op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie het artikel [Hive-activiteit](transform-data-using-hadoop-hive.md) voor meer informatie over deze activiteit. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig-activiteit
Met de HDInsight Pig-activiteit in een Data Factory pijp lijn worden Pig-query's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie het artikel [Pig-activiteit](transform-data-using-hadoop-pig.md) voor meer informatie over deze activiteit. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-activiteit
Met de HDInsight MapReduce-activiteit in een Data Factory pijp lijn worden MapReduce-Program ma's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie [MapReduce-activiteiten](transform-data-using-hadoop-map-reduce.md) artikel voor meer informatie over deze activiteit.

### <a name="hdinsight-streaming-activity"></a>HDInsight streaming-activiteit
Met de activiteit HDInsight streaming in een Data Factory pijp lijn worden Hadoop-streaming-Program ma's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie [HDInsight streaming-activiteit](transform-data-using-hadoop-streaming.md) voor meer informatie over deze activiteit.

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-activiteit
Met de HDInsight Spark-activiteit in een Data Factory pijp lijn worden Spark-Program ma's uitgevoerd op uw eigen HDInsight-cluster. Zie voor meer informatie [aanroepen Spark-Program ma's van Azure Data Factory](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Machine Learning activiteiten
Met Azure Data Factory kunt u eenvoudig pijp lijnen maken die gebruikmaken van een gepubliceerde Azure Machine Learning-webservice voor predictive analytics. U kunt met behulp van de [batch Execution-activiteit](transform-data-using-machine-learning.md) in een Azure Data Factory-pijp lijn een machine learning-webservice aanroepen om voor spellingen te doen op de gegevens in batch.

De voorspellende modellen in de Machine Learning Score experimenten moeten na verloop van tijd opnieuw worden getraind met nieuwe invoer gegevens sets. Wanneer u klaar bent met opnieuw trainen, wilt u de Score-webservice bijwerken met het opnieuw getrainde Machine Learning model. U kunt de [activiteit resource bijwerken](update-machine-learning-models.md) gebruiken om de webservice bij te werken met het nieuwe getrainde model.  

Zie [machine learning activiteiten gebruiken](transform-data-using-machine-learning.md) voor meer informatie over deze machine learning activiteiten. 

### <a name="stored-procedure-activity"></a>Opgeslagen procedure activiteit
U kunt de SQL Server opgeslagen procedure-activiteit in een Data Factory-pijp lijn gebruiken voor het aanroepen van een opgeslagen procedure in een van de volgende gegevens archieven: Azure SQL Database, Azure SQL Data Warehouse, SQL Server data base in uw onderneming of een Azure-VM. Zie het artikel [opgeslagen procedure-activiteit](transform-data-using-stored-procedure.md) voor meer informatie.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
Met Data Lake Analytics U-SQL-activiteit wordt een U-SQL-script op een Azure Data Lake Analytics cluster uitgevoerd. Zie [het artikel Data Analytics U-SQL-activiteit](transform-data-using-data-lake-analytics.md) voor meer informatie. 

### <a name="databricks-notebook-activity"></a>Databricks-notebook activiteit

Met de activiteit Azure Databricks notebook in een Data Factory pijp lijn wordt een Databricks-notebook in uw Azure Databricks-werk ruimte uitgevoerd. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark. Zie [gegevens transformeren door een Databricks-notebook uit te voeren](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks jar-activiteit

De Azure Databricks jar-activiteit in een Data Factory pijp lijn voert een Spark jar in uw Azure Databricks-cluster uit. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark. Zie [gegevens transformeren door een jar-activiteit uit te voeren in azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks python-activiteit

Met de activiteit python Azure Databricks in een Data Factory-pijp lijn wordt een python-bestand in uw Azure Databricks-cluster uitgevoerd. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark. Zie [gegevens transformeren door een python-activiteit uit te voeren in azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Aangepaste activiteit
Als u gegevens moet transformeren op een manier die niet wordt ondersteund door Data Factory, kunt u een aangepaste activiteit maken met uw eigen gegevens verwerkings logica en de activiteit in de pijp lijn gebruiken. U kunt de aangepaste .NET-activiteit zodanig configureren dat deze wordt uitgevoerd met behulp van een Azure Batch-service of een Azure HDInsight-cluster. Zie het artikel [aangepaste activiteiten gebruiken](transform-data-using-dotnet-custom-activity.md) voor meer informatie. 

U kunt een aangepaste activiteit maken om R-scripts uit te voeren op uw HDInsight-cluster waarop R is geïnstalleerd. Zie [R-script uitvoeren met behulp van Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Reken omgevingen
U maakt een gekoppelde service voor de compute-omgeving en gebruikt vervolgens de gekoppelde service wanneer u een trans formatie-activiteit definieert. Er zijn twee soorten reken omgevingen die door Data Factory worden ondersteund. 

- **Op aanvraag**: in dit geval wordt de computer omgeving volledig beheerd door Data Factory. Het wordt automatisch gemaakt door de Data Factory-service voordat een taak wordt verzonden om gegevens te verwerken en te worden verwijderd wanneer de taak is voltooid. U kunt gedetailleerde instellingen van de compute-omgeving op aanvraag voor taak uitvoering, Cluster beheer en acties voor Boots traping configureren en beheren. 
- **Neem uw eigen**werk: in dit geval kunt u uw eigen computer omgeving (bijvoorbeeld HDInsight-cluster) registreren als een gekoppelde service in Data Factory. De computer omgeving wordt beheerd door u en de Data Factory-service gebruikt deze om de activiteiten uit te voeren. 

Zie het artikel [Compute linked Services](compute-linked-services.md) voor meer informatie over Compute-services die door Data Factory worden ondersteund. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende zelf studie voor een voor beeld van het gebruik van een trans formatie-activiteit: [zelf studie: gegevens transformeren met Spark](tutorial-transform-data-spark-powershell.md)
