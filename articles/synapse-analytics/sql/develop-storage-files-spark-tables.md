---
title: Apache Spark synchroniseren voor externe Azure Synapse-tabeldefinities in SQL on-demand (preview)
description: Overzicht van hoe u query’s kunt uitvoeren op Spark-tabellen met behulp van SQL on-demand (preview)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: e36f98a20d1fc6392aef1aebf5fc86e18085cc10
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204895"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Apache Spark synchroniseren voor externe Azure Synapse-tabeldefinities in SQL on-demand (preview)

Met SQL on-demand (preview) kunnen automatisch metagegevens worden gesynchroniseerd van Apache Spark voor Azure Synapse-pools. Er wordt een SQL on-demand-database gemaakt voor elke database in Spark-pools (preview). 

Voor elke externe Spark-tabel die is gebaseerd op Parquet en zich in Azure Storage bevindt, wordt een externe tabel gemaakt in de SQL on-demand-database. Op deze manier kunt u uw Spark-pools afsluiten en nog steeds query’s uitvoeren op externe Spark-tabellen vanuit SQL on-demand.

Wanneer een tabel is gepartitioneerd in Spark, worden de bestanden in de opslag geordend in mappen. SQL on-demand maakt gebruik van partitiemetagegevens en is alleen gericht op relevante mappen en bestanden voor uw query.

Synchronisatie van metagegevens wordt automatisch geconfigureerd voor elke Spark-pool die is ingericht in de Azure Synapse-werkruimte. U kunt direct beginnen met het uitvoeren van query’s op externe Spark-tabellen.

Elke externe tabel van Spark Parquet die zich in Azure Storage bevindt, wordt vertegenwoordigd door een externe tabel in een dbo-schema dat overeenkomt met een SQL on-demand-database. 

Voer voor externe Spark-tabelquery’s een query uit die is gericht op een externe [spark_table]. Voordat u het onderstaande voorbeeld uitvoert, moet u controleren of u de juiste [toegang tot het opslagaccount](develop-storage-files-storage-access-control.md) hebt waar de bestanden zich bevinden.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark-gegevenstypen toewijzen aan SQL-gegevenstypen

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
