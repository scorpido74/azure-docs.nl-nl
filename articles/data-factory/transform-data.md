---
title: Gegevens transformeren
description: Meer informatie over het transformeren van gegevens of het verwerken van gegevens in Azure Data Factory met Hadoop, Machine Learning of Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 024d61c5bcc38c78dc5a2bd5e01cba43d7b5267e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418827"
---
# <a name="transform-data-in-azure-data-factory"></a>Gegevens transformeren in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector"]
> * [Gegevensstroom toewijzen](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Opgeslagen procedure](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks-notitieblok](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET aangepast](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
## <a name="overview"></a>Overzicht
In dit artikel worden gegevenstransformatieactiviteiten in Azure Data Factory uitgelegd die u gebruiken om uw ruwe gegevens om te zetten en te verwerken tot voorspellingen en inzichten op schaal. Een transformatieactiviteit wordt uitgevoerd in een computeromgeving zoals Azure Databricks of Azure HDInsight. Het biedt links naar artikelen met gedetailleerde informatie over elke transformatieactiviteit.

Data Factory ondersteunt de volgende gegevenstransformatieactiviteiten die afzonderlijk of geketend kunnen worden aan [pijplijnen.](concepts-pipelines-activities.md)

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Native transformeren in Azure Data Factory met gegevensstromen

### <a name="mapping-data-flows"></a>Toewijzing gegevensstromen

Gegevensstromen in kaart brengen zijn visueel ontworpen gegevenstransformaties in Azure Data Factory. Gegevensstromen stellen dataengineers in staat om grafische logica voor gegevenstransformatie te ontwikkelen zonder code te schrijven. De resulterende gegevensstromen worden uitgevoerd als activiteiten binnen Azure Data Factory-pijplijnen die geschaalde Spark-clusters gebruiken. Gegevensstroomactiviteiten kunnen worden geoperationaliseerd via bestaande plannings-, controle-, stroom- en bewakingsmogelijkheden van Data Factory. Zie [gegevensstromen toewijzen](concepts-data-flow-overview.md)voor meer informatie .

### <a name="wrangling-data-flows"></a>Getouwtrek gegevensstromen

Met de gegevensstromen in Azure Data Factory u codevrije gegevensvoorbereiding op cloudschaal iteratief doen. Wrangling data flows integreren met [Power Query Online](https://docs.microsoft.com/power-query/) en maakt Power Query M functies beschikbaar voor data wrangling op cloud schaal via spark uitvoering. Zie voor meer informatie [de gegevensstromen van het getouwtrek](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Externe transformaties

Optioneel u transformaties met de hand coderen en de externe compute-omgeving zelf beheren.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive-activiteit
De HDInsight Hive-activiteit in een Data Factory-pijplijn voert Hive-query's uit op uw eigen of on-demand HDInsight-cluster op basis van Windows/Linux. Zie [artikel over de activiteit van Hive](transform-data-using-hadoop-hive.md) voor meer informatie over deze activiteit. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig activiteit
De HDInsight Pig-activiteit in een Data Factory-pijplijn voert Pig-query's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster. Zie [Artikel Varkensactiviteit](transform-data-using-hadoop-pig.md) voor meer informatie over deze activiteit. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapActiviteit verminderen
De HDInsight MapReduce activiteit in een Data Factory-pijplijn voert MapReduce-programma's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster. Zie [MapReduce activity](transform-data-using-hadoop-map-reduce.md) article for details about this activity.

### <a name="hdinsight-streaming-activity"></a>HDInsight-streamingactiviteit
De HDInsight Streaming-activiteit in een Data Factory-pijplijn voert Hadoop Streaming-programma's uit op uw eigen of on-demand Windows/Linux-gebaseerde HDInsight-cluster. Zie [HDInsight Streaming-activiteit](transform-data-using-hadoop-streaming.md) voor meer informatie over deze activiteit.

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-activiteit
De HDInsight Spark-activiteit in een Data Factory-pijplijn voert Spark-programma's uit op uw eigen HDInsight-cluster. Zie [Spark-programma's van Azure Data Factory voor](transform-data-using-spark.md)meer informatie aanroepen. 

### <a name="machine-learning-activities"></a>Machine Learning-activiteiten
Met Azure Data Factory u eenvoudig pijplijnen maken die gebruikmaken van een gepubliceerde Azure Machine Learning-webservice voor voorspellende analyses. Met de [batchuitvoeringsactiviteit](transform-data-using-machine-learning.md) in een Azure Data Factory-pijplijn u een Machine Learning-webservice aanroepen om voorspellingen te doen over de gegevens in batch.

Na verloop van tijd moeten de voorspellende modellen in de Machine Learning-scoringsexperimenten worden omgeschoold met behulp van nieuwe invoergegevenssets. Nadat u klaar bent met omscholing, wilt u de scorewebservice bijwerken met het omgeschoold Machine Learning-model. U de [activiteit Resource bijwerken](update-machine-learning-models.md) gebruiken om de webservice bij te werken met het nieuw getrainde model.  

Zie [Machine Learning-activiteiten gebruiken](transform-data-using-machine-learning.md) voor meer informatie over deze Machine Learning-activiteiten. 

### <a name="stored-procedure-activity"></a>Opgeslagen procedureactiviteit
U de sql-server-activiteit opgeslagen procedure in een Data Factory-pijplijn gebruiken om een opgeslagen procedure aan te roepen in een van de volgende gegevensopslag: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database in uw onderneming of een Azure VM. Zie [Artikel Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md) voor meer informatie.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
Data Lake Analytics U-SQL-activiteit voert een U-SQL-script uit op een Azure Data Lake Analytics-cluster. Zie [Data Analytics U-SQL-activiteitenartikel](transform-data-using-data-lake-analytics.md) voor meer informatie. 

### <a name="databricks-notebook-activity"></a>Activiteit Databricks-notitieblok

Met de Azure Databricks-notitieblokactiviteit in een datafabriekpijplijn wordt een Databricks-notitieblok uitgevoerd in uw Azure Databricks-werkruimte. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark. Zie [Gegevens transformeren door een Databricks-notitieblok uit te voeren](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks Jar-activiteit

Met de Azure Databricks Jar-activiteit in een datafabriekwordt een Spark Jar uitgevoerd in uw Azure Databricks-cluster. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark. Zie [Gegevens transformeren door een Jar-activiteit uit te voeren in Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks Python-activiteit

Met de Azure Databricks Python-activiteit in een datafabriekwordt een Python-bestand uitgevoerd in uw Azure Databricks-cluster. Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark. Zie [Gegevens transformeren door een Python-activiteit uit te voeren in Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Aangepaste activiteit
Als u gegevens moet transformeren op een manier die niet wordt ondersteund door Data Factory, u een aangepaste activiteit maken met uw eigen gegevensverwerkingslogica en de activiteit in de pijplijn gebruiken. U de aangepaste .NET-activiteit zo configureren dat deze wordt uitgevoerd met een Azure Batch-service of een Azure HDInsight-cluster. Zie [Artikel Aangepaste activiteiten gebruiken](transform-data-using-dotnet-custom-activity.md) voor meer informatie. 

U kunt een aangepaste activiteit maken om R-scripts uit te voeren op uw HDInsight-cluster waarop R is geïnstalleerd. Zie [R-script uitvoeren met behulp van Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Compute-omgevingen
U maakt een gekoppelde service voor de compute-omgeving en gebruikt vervolgens de gekoppelde service bij het definiëren van een transformatieactiviteit. Er zijn twee typen compute-omgevingen die worden ondersteund door Data Factory. 

- **On-demand**: In dit geval wordt de computeromgeving volledig beheerd door Data Factory. Het wordt automatisch gemaakt door de Service Gegevensfabriek voordat een taak wordt ingediend om gegevens te verwerken en wordt verwijderd wanneer de taak is voltooid. U gedetailleerde instellingen configureren en beheren van de on-demand compute-omgeving voor taakuitvoering, clusterbeheer en bootstrapping-acties. 
- **Bring Your Own**: In dit geval u uw eigen computeromgeving (bijvoorbeeld HDInsight cluster) registreren als gekoppelde service in Data Factory. De computeromgeving wordt door u beheerd en de Data Factory-service gebruikt deze om de activiteiten uit te voeren. 

Zie [artikel Compute Linked Services](compute-linked-services.md) voor meer informatie over compute services die worden ondersteund door Data Factory. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudie voor een voorbeeld van het gebruik van een transformatieactiviteit: [Zelfstudie: gegevens transformeren met Spark](tutorial-transform-data-spark-powershell.md)
