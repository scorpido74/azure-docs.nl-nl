---
title: Query's uitvoeren Spark-tabellen met behulp van SQL op aanvraag (preview)
description: Overzicht van het opvragen van Spark-tabellen met behulp van SQL op aanvraag (preview)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424046"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Query Spark-tabellen met Azure Synapse Analytics met behulp van SQL on-demand (preview)

De SQL op aanvraag (preview) kan automatisch meta gegevens synchroniseren vanuit Spark-groepen in de Synapse-werk ruimte (preview). Er wordt een SQL-Data Base op aanvraag gemaakt voor elke data base in Spark-Pools (preview-versie). Voor elke Spark-tabel op basis van Parquet of CSV wordt een externe tabel gemaakt in de SQL-Data Base op aanvraag. Zo kunt u uw Spark-Pools afsluiten en toch query's uitvoeren op Spark-tabellen vanuit SQL op aanvraag.

Wanneer een tabel is gepartitioneerd in Spark, worden de bestanden in de opslag geordend op mappen. SQL on demand maakt gebruik van meta gegevens van de partitie en alleen relevante mappen en bestanden voor uw query.

Synchronisatie van meta gegevens wordt automatisch geconfigureerd voor elke Spark-groep die is ingericht in de Azure Synapse-werk ruimte. U kunt direct beginnen met het uitvoeren van query's op Spark-tabellen.

Elke Spark-tabel wordt weer gegeven met een externe tabel in een dbo-schema die overeenkomt met een SQL on-demand-data base. Voor Spark-tabel query's voert u een query uit die gericht is op een extern [spark_table]. Voordat u het onderstaande voor beeld uitvoert, moet u ervoor zorgen dat u de juiste [toegang hebt tot het opslag account](develop-storage-files-storage-access-control.md) waarin de bestanden zich bevinden.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark-gegevens typen aan toewijzing van SQL-gegevens typen

| Spark-gegevens type | SQL-gegevens type               |
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
| StringType      | varchar (max) *               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| Array       | varchar (max) * (in JSON) * * |
| MapType         | varchar (max) * (in JSON) * * |
| StructType      | varchar (max) * (in JSON) * * |

\*De gebruikte sortering is Latin1_General_100_BIN2_UTF8.

* * Array type, MapType en StructType worden weer gegeven als JSONs.



## <a name="next-steps"></a>Volgende stappen

Ga naar de [opslag Access Control](develop-storage-files-storage-access-control.md) artikel voor meer informatie over toegangs beheer voor opslag.
