---
title: Opgeslagen procedure van Azure Data Factory Copy Activity aanroepen
description: Meer informatie over het aanroepen van een opgeslagen procedure in Azure SQL Database of SQL Server vanuit een kopieeractiviteit van Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924085"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Opgeslagen procedure aanroepen vanuit kopieeractiviteit in Azure Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [gegevens transformeren met behulp van opgeslagen procedureactiviteit in Gegevensfabriek](../transform-data-using-stored-procedure.md).


Wanneer u gegevens kopieert naar [SQL Server](data-factory-sqlserver-connector.md) of Azure [SQL Database,](data-factory-azure-sql-connector.md)u de **SqlSink** configureren in kopieeractiviteit om een opgeslagen procedure aan te roepen. U de opgeslagen procedure gebruiken om extra verwerkingen uit te voeren (kolommen samenvoegen, waarden opzoeken, invoegen in meerdere tabellen, enz.) voordat u gegevens in de doeltabel invoegt. Deze functie maakt gebruik van [tabelwaardeparameters](https://msdn.microsoft.com/library/bb675163.aspx). 

In het volgende voorbeeld ziet u hoe u een opgeslagen procedure in een SQL Server-database aanroept vanuit een Pijplijn Gegevensfabriek (kopieeractiviteit):  

## <a name="output-dataset-json"></a>Uitvoergegevensset JSON
Stel in de uitvoergegevensset JSON het **type** in op: **SqlServerTable**. Stel deze in op **AzureSqlTable** om te gebruiken met een Azure SQL-database. De waarde voor de eigenschap **tabelNaam** moet overeenkomen met de naam van de eerste parameter van de opgeslagen procedure.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>SqlSink sectie in kopieeractiviteit JSON
Definieer de sectie **SqlSink** in de kopieeractiviteit JSON als volgt. Als u een opgeslagen procedure wilt aanroepen terwijl u gegevens in de sink/destination-database invoegt, geeft u waarden op voor zowel **sqlwriterstoredprocedurenaam** als **sqlwritertabletype-eigenschappen.** Zie [SqlSink-sectie in het SQL Server-connectorartikel](data-factory-sqlserver-connector.md#sqlsink)voor beschrijvingen van deze eigenschappen.

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definitie van opgeslagen procedures 
Definieer in uw database de opgeslagen procedure met dezelfde naam als **SqlWriterStoredProcedureName.** De opgeslagen procedure verwerkt invoergegevens uit het brongegevensarchief en voegt gegevens in een tabel in de doeldatabase in. De naam van de eerste parameter van de opgeslagen procedure moet overeenkomen met de tabelNaam gedefinieerd in de gegevensset JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Tabeltypedefinitie
Definieer in uw database het tabeltype met dezelfde naam als **SqlWriterTableType.** Het schema van het tabeltype moet overeenkomen met het schema van de invoergegevensset.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende connectorartikelen die voor volledige JSON-voorbeelden: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
