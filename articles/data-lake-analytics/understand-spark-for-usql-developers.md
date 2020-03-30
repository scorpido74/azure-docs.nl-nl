---
title: Maak gebruik van Apache Spark voor Azure Data Lake Analytics U-SQL-ontwikkelaars.
description: In dit artikel worden Apache Spark-concepten beschreven om u te helpen verschillen tussen U-SQL-ontwikkelaars.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648426"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Inzicht in Apache Spark voor U-SQL-ontwikkelaars

Microsoft ondersteunt verschillende Analytics-services, zoals [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) en [Azure HDInsight](../hdinsight/hdinsight-overview.md) en Azure Data Lake Analytics. We horen van ontwikkelaars dat ze een duidelijke voorkeur hebben voor open-source-oplossingen terwijl ze analytics-pijplijnen bouwen. Om U-SQL-ontwikkelaars inzicht te geven in Apache Spark en hoe u uw U-SQL-scripts transformeren naar Apache Spark, hebben we deze richtlijnen gemaakt.

Het bevat een aantal stappen die u nemen, en verschillende alternatieven.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Stappen om U-SQL om te zetten in Apache Spark

1. Transformeer uw taakorkestratiepijplijnen.

   Als u [Azure Data Factory](../data-factory/introduction.md) gebruikt om uw Azure Data Lake Analytics-scripts te orkestreren, moet u deze aanpassen om de nieuwe Spark-programma's te orkestreren.
2. Inzicht in de verschillen tussen hoe U-SQL en Spark gegevens beheren

   Als u uw gegevens wilt verplaatsen van [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) naar Azure Data Lake Storage [Gen2,](../storage/blobs/data-lake-storage-introduction.md)moet u zowel de bestandsgegevens als de bijgehouden catalogusgegevens kopiëren. Houd er rekening mee dat Azure Data Lake Analytics alleen Azure Data Lake Storage Gen1 ondersteunt. Zie [Spark-gegevensindelingen begrijpen](understand-spark-data-formats.md)
3. Uw U-SQL-scripts transformeren naar Spark

   Voordat u uw U-SQL-scripts transformeert, moet u een analyseservice kiezen. Enkele van de beschikbare compute services die beschikbaar zijn, zijn:
      - [Gegevensstroom van Azure Data Factory](../data-factory/concepts-data-flow-overview.md) Het toewijzen van gegevensstromen zijn visueel ontworpen gegevenstransformaties waarmee gegevenstechnici een grafische logica voor gegevenstransformatie kunnen ontwikkelen zonder code te schrijven. Hoewel ze niet geschikt zijn om complexe gebruikerscode uit te voeren, kunnen ze eenvoudig traditionele SQL-achtige gegevensstroomtransformaties weergeven
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive op HDInsight is geschikt voor ETL-bewerkingen (Extract, Transform en Load). Dit betekent dat u uw U-SQL-scripts gaat vertalen naar Apache Hive.
      - Apache Spark Engines zoals [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) of [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) Dit betekent dat u uw U-SQL-scripts gaat vertalen naar Spark. Zie [Spark-gegevensindelingen begrijpen voor](understand-spark-data-formats.md) meer informatie

> [!CAUTION]
> Zowel [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) als [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) zijn clusterservices en geen serverloze taken zoals Azure Data Lake Analytics. U moet overwegen hoe u de clusters inricht om de juiste kosten-prestatieverhouding te krijgen en hoe u hun levensduur beheren om uw kosten te minimaliseren. Deze services hebben verschillende prestatiekenmerken met gebruikerscode geschreven in .NET, dus u moet wikkels schrijven of uw code in een ondersteunde taal herschrijven. Zie [Spark-gegevensindelingen begrijpen](understand-spark-data-formats.md), [Apache Spark-codeconcepten voor U-SQL-ontwikkelaars](understand-spark-code-concepts.md), [.Net voor Apache Spark begrijpen voor](https://dotnet.microsoft.com/apps/data/spark) meer informatie

## <a name="next-steps"></a>Volgende stappen

- [Spark-gegevensindelingen voor U-SQL-ontwikkelaars begrijpen](understand-spark-data-formats.md)
- [Spark-codeconcepten voor U-SQL-ontwikkelaars begrijpen](understand-spark-code-concepts.md)
- [Upgrade uw oplossingen voor big data-analyse van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET voor Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Gegevens transformeren met Hadoop Hive-activiteit in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Gegevens transformeren met Spark-activiteit in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Wat is Apache Spark in Azure HDInsight?](../hdinsight/spark/apache-spark-overview.md)
