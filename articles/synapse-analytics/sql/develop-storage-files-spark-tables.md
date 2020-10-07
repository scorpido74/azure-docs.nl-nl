---
title: Apache Spark synchroniseren voor externe tabeldefinities in SQL on demand (preview)
description: Overzicht van hoe u query’s kunt uitvoeren op Spark-tabellen met behulp van SQL on-demand (preview)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3e9f688a31d2847505e974ab6a1557aa6a7b2047
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87046842"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Apache Spark synchroniseren voor externe Azure Synapse-tabeldefinities in SQL on-demand (preview)

Met SQL on-demand (preview) kunnen automatisch metagegevens worden gesynchroniseerd van Apache Spark voor Azure Synapse-pools. Er wordt een SQL on-demand-database gemaakt voor elke database in Spark-pools (preview). 

Voor elke externe Spark-tabel die is gebaseerd op Parquet en zich in Azure Storage bevindt, wordt een externe tabel gemaakt in de SQL on-demand-database. Op deze manier kunt u uw Spark-pools afsluiten en nog steeds query’s uitvoeren op externe Spark-tabellen vanuit SQL on-demand.

Wanneer een tabel is gepartitioneerd in Spark, worden de bestanden in de opslag geordend in mappen. SQL on demand maakt gebruik van partitiemetagegevens en werkt alleen met relevante mappen en bestanden voor uw query.

Synchronisatie van metagegevens wordt automatisch geconfigureerd voor elke Spark-pool die is ingericht in de Azure Synapse-werkruimte. U kunt direct beginnen met het uitvoeren van query’s op externe Spark-tabellen.

Elke externe tabel van Spark Parquet die zich in Azure Storage bevindt, wordt vertegenwoordigd door een externe tabel in een dbo-schema dat overeenkomt met een SQL on-demand-database. 

Voer voor externe Spark-tabelquery’s een query uit die is gericht op een externe [spark_table]. Voordat u het onderstaande voorbeeld uitvoert, moet u controleren of u de juiste [toegang tot het opslagaccount](develop-storage-files-storage-access-control.md) hebt waar de bestanden zich bevinden.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Toevoegen, verwijderen of wijzigen van externe Spark-tabelopdrachten voor een kolom wordt niet weerspiegeld de in externe tabel in SQL on demand.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark-gegevenstypen toewijzen aan SQL-gegevenstypen

| Spark-gegevenstype | SQL-gegevenstype               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | werkelijk                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (into JSON)** |
| MapType         | varchar(max)* (into JSON)** |
| StructType      | varchar(max)* (into JSON)** |

\* De gebruikte sortering is Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType en StructType worden weergegeven als JSON’s.



## <a name="next-steps"></a>Volgende stappen

Ga naar het artikel [Storage-toegangsbeheer](develop-storage-files-storage-access-control.md) voor meer informatie over opslagtoegangsbeheer.
