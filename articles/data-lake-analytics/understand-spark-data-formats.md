---
title: Informatie over Apache Spark-gegevensindelingen voor Azure Data Lake Analytics U-SQL-ontwikkelaars.
description: In dit artikel worden Apache Spark-concepten beschreven om U_SQL ontwikkelaars inzicht te geven in verschillen tussen U-SQL- en Spark-gegevensindelingen.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648439"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Verschillen tussen U-SQL- en Spark-gegevensindelingen begrijpen

Als u Azure [Databricks](../azure-databricks/what-is-azure-databricks.md) of [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)wilt gebruiken, raden we u aan uw gegevens te migreren van Azure Data Lake Storage [Gen1](../data-lake-store/data-lake-store-overview.md) naar Azure Data Lake Storage [Gen2.](../storage/blobs/data-lake-storage-introduction.md)

Naast het verplaatsen van uw bestanden, wilt u ook uw gegevens, opgeslagen in U-SQL-tabellen, toegankelijk maken voor Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Gegevens verplaatsen die zijn opgeslagen in Azure Data Lake Storage Gen1-bestanden

Gegevens die in bestanden zijn opgeslagen, kunnen op verschillende manieren worden verplaatst:

- Schrijf een [Azure Data Factory-pijplijn](../data-factory/introduction.md) om de gegevens van [azure Data Lake Storage Gen1-account](../data-lake-store/data-lake-store-overview.md) te kopiëren naar het [Azure Data Lake Storage Gen2-account.](../storage/blobs/data-lake-storage-introduction.md)
- Schrijf een Spark-taak die de gegevens van het [Azure Data Lake Storage Gen1-account](../data-lake-store/data-lake-store-overview.md) leest en deze naar het Azure Data Lake Storage [Gen2-account](../storage/blobs/data-lake-storage-introduction.md) schrijft. Op basis van uw use case u deze in een andere indeling schrijven, zoals Parket als u de oorspronkelijke bestandsindeling niet hoeft te behouden.

We raden u aan het artikel te bekijken [Upgrade uw big data analytics-oplossingen van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Gegevens verplaatsen die zijn opgeslagen in U-SQL-tabellen

U-SQL-tabellen worden niet begrepen door Spark. Als u gegevens hebt opgeslagen in U-SQL-tabellen, voert u een U-SQL-taak uit die de tabelgegevens extraheert en opslaat in een indeling die Spark begrijpt. De meest geschikte indeling is het maken van een set parketbestanden volgens de maplay-out van de Hive metastore.

De uitvoer kan worden bereikt in U-SQL met de ingebouwde Parquet outputter en met behulp van de dynamische uitvoerverdeling met bestandssets om de partitiemappen te maken. [Meer bestanden verwerken dan ooit en het gebruik van Parket](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) geeft een voorbeeld van hoe u dergelijke Spark-verbruiksgegevens maken.

Na deze transformatie kopieert u de gegevens zoals beschreven in het hoofdstuk [Move-gegevens die zijn opgeslagen in Azure Data Lake Storage Gen1-bestanden.](#move-data-stored-in-azure-data-lake-storage-gen1-files)

## <a name="caveats"></a>Waarschuwingen

- Gegevenssemantiek Bij het kopiëren van bestanden vindt de kopie plaats op byteniveau. Dezelfde gegevens moeten dus worden weergegeven in het [Azure Data Lake Storage Gen2-account.](../storage/blobs/data-lake-storage-introduction.md) Opmerking kan spark echter anders interpreteren. Het kan bijvoorbeeld een andere standaardgebruiken voor een rijscheidingsbekeerd in een CSV-bestand.
    Bovendien, als u getypte gegevens kopieert (uit tabellen), kunnen Parket en Spark verschillende precisie en schaal hebben voor sommige getypte waarden (bijvoorbeeld een vlotter) en kunnen null-waarden anders worden behandeld. U-SQL heeft bijvoorbeeld de C#-semantiek voor null-waarden, terwijl Spark een logica met drie waarden heeft voor null-waarden.

- U-SQL-tabellen voor gegevensorganisatie (partitionering) bieden twee niveaupartitionering. Het buitenste`PARTITIONED BY`niveau ( ) is door waarde en kaarten meestal in de Hive / Spark partitionering regeling met behulp van map hiërarchieën. U moet ervoor zorgen dat de null-waarden worden toegewezen aan de juiste map. Het binnenste`DISTRIBUTED BY`niveau ( ) in U-SQL biedt 4 distributieschema's: round robin, bereik, hash en directe hash.
    Hive/Spark-tabellen ondersteunen alleen waardepartitionering of hashpartitioning, met behulp van een andere hashfunctie dan U-SQL. Wanneer u uw U-SQL-tabelgegevens uitvoert, u waarschijnlijk alleen in kaart brengen in de waardepartitionering voor Spark en moet u mogelijk uw gegevensindeling verder afstemmen, afhankelijk van uw uiteindelijke Spark-query's.

## <a name="next-steps"></a>Volgende stappen

- [Spark-codeconcepten voor U-SQL-ontwikkelaars begrijpen](understand-spark-code-concepts.md)
- [Upgrade uw oplossingen voor big data-analyse van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET voor Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Gegevens transformeren met Spark-activiteit in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Gegevens transformeren met Hadoop Hive-activiteit in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Wat is Apache Spark in Azure HDInsight?](../hdinsight/spark/apache-spark-overview.md)
