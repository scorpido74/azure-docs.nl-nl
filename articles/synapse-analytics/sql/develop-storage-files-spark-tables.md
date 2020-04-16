---
title: Spark-tabellen query's met SQL on-demand (voorbeeld)
description: Overzicht van hoe spark-tabellen kunnen worden opgevraagd met SQL on-demand (voorbeeld)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424046"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Spark-tabellen met Azure Synapse Analytics opvragen met SQL on-demand (voorbeeld)

De SQL on-demand (preview) kan metagegevens van Spark-pools automatisch synchroniseren binnen synapsewerkruimte (preview). Er wordt een SQL on-demand database gemaakt voor elke database die bestaat in Spark-pools (preview). Voor elke Spark-tabel op basis van parket of CSV wordt een externe tabel gemaakt in de SQL on-demand database. Als zodanig u uw Spark-pools afsluiten en nog steeds Spark-tabellen van SQL on-demand opvragen.

Wanneer een tabel is verdeeld in Spark, worden bestanden in opslag geordend op mappen. SQL on-demand maakt gebruik van partitiemetagegevens en target alleen relevante mappen en bestanden voor uw query.

Synchronisatie met ametagegevens wordt automatisch geconfigureerd voor elke Spark-groep die is ingericht in de Azure Synapse-werkruimte. U spark-tabellen direct beginnen op te vragen.

Elke Spark-tabel wordt weergegeven met een externe tabel in een dbo-schema dat overeenkomt met een SQL on-demand database. Voer voor Query's in Spark een query uit die is gericht op een externe [spark_table]. Voordat u het onderstaande voorbeeld uitvoert, moet u de juiste toegang hebben [tot het opslagaccount](develop-storage-files-storage-access-control.md) waar de bestanden zich bevinden.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark-gegevenstypen voor toewijzing van SQL-gegevenstypen

| Spark-gegevenstype | SQL-gegevenstype               |
| --------------- | --------------------------- |
| ByteType ByteType        | smallint                    |
| ShortType (ShortType)       | smallint                    |
| IntegerType     | int                         |
| LongType (LongType)        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| Tijdstempeltype   | datetime2                   |
| DateType        | date                        |
| StringType (StringType)      | varchar(max.)*               |
| BinaryType (BinaryType)      | varbinary varbinary                   |
| BooleanType     | bit                         |
| ArrayType (ArrayType)       | varchar(max)* (in JSON)** |
| MapType         | varchar(max)* (in JSON)** |
| StructType      | varchar(max)* (in JSON)** |

\*Collatie gebruikt is Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType en StructType worden weergegeven als JSONs.



## <a name="next-steps"></a>Volgende stappen

Ga naar het artikel [Opslagtoegangsbeheer](develop-storage-files-storage-access-control.md) voor meer informatie over toegangsbeheer voor opslag.
