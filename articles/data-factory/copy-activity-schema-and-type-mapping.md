---
title: Schematoewijzing in de kopieeractiviteit | Microsoft Docs
description: Meer informatie over hoe de kopieeractiviteit in Azure Data Factory schema's en gegevenstypen van de brongegevens sink-gegevens bij het kopiëren van gegevens wordt toegewezen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875800"
---
# <a name="schema-mapping-in-copy-activity"></a>Schematoewijzing in kopieeractiviteit

Dit artikel wordt beschreven hoe copy activity in Azure Data Factory biedt schematoewijzing en gegevenstypetoewijzing van de brongegevens met sink-gegevens wanneer het kopiëren van gegevens uitvoert.

## <a name="schema-mapping"></a>Schematoewijzing

In de kolomtoewijzing is van toepassing bij het kopiëren van gegevens van bron naar een sink. Standaard kopieeractiviteit **brongegevens opvangen door de namen van kolommen toewijzen**. U kunt opgeven [expliciete toewijzing](#explicit-mapping) om aan te passen de kolomtoewijzing op basis van uw behoeften. Meer specifiek, kopieeractiviteit:

1. De gegevens niet lezen vanuit de bron en het schema van de gegevensbron bepalen
2. Gebruik standaard kolomtoewijzing om toe te wijzen kolommen met de naam, of expliciete kolomtoewijzing van toepassing als u opgeeft.
3. Schrijven van de gegevens om op te vangen

### <a name="explicit-mapping"></a>Expliciete toewijzing

U kunt opgeven met de kolommen om toe te wijzen in de kopieeractiviteit -> `translator`  ->  `mappings` eigenschap. Het volgende voorbeeld definieert een kopieeractiviteit in een pijplijn om gegevens te kopiëren uit een door tekens gescheiden tekst naar Azure SQL Database.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

De volgende eigenschappen worden ondersteund onder `translator`  ->  `mappings` object met -> `source` en `sink`:

| Eigenschap | Description                                                  | Vereist |
| -------- | ------------------------------------------------------------ | -------- |
| nam     | Naam van de bron- of -sink-kolom.                           | Ja      |
| ordinal  | De kolomindex. Beginnen met 1. <br>Toepassen en vereist wanneer met behulp van tekst zonder kopregel gescheiden. | Nee       |
| path     | JSON-padexpressie voor elk veld op te halen of toewijzen. Van toepassing op hiërarchische gegevens bijvoorbeeld MongoDB/REST.<br>Voor velden onder het hoofdobject begint JSON-pad met root $; voor velden binnen de matrix die is gekozen door `collectionReference` eigenschap, JSON-pad begint met het matrixelement. | Nee       |
| type     | Data Factory tussentijdse gegevenstype van de bron- of -sink-kolom. | Nee       |
| culture  | De cultuur van de bron- of -sink-kolom. <br>Van toepassing wanneer het type is `Datetime` of `Datetimeoffset`. De standaardwaarde is `en-us`. | Nee       |
| format   | Indeling van tekenreeks die moet worden gebruikt als type `Datetime` of `Datetimeoffset`. Raadpleeg [aangepaste datum en tijd opmaaktekenreeksen](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) op datum/tijd opmaken. | Nee       |

De volgende eigenschappen worden ondersteund onder `translator`  ->  `mappings` naast het object met `source` en `sink`:

| Eigenschap            | Description                                                  | Vereist |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Ondersteund alleen als hiërarchische gegevens bijvoorbeeld MongoDB/REST bron is.<br>Als u wilt herhalen en gegevens ophalen uit de objecten **in een matrixveld** met hetzelfde patroon en converteren naar per rij per object geeft u het JSON-pad van die matrix voor cross-pas. | Nee       |

### <a name="alternative-column-mapping"></a>Alternatieve kolomtoewijzing

Kunt u copy activity -> `translator`  ->  `columnMappings` om toe te wijzen tussen gegevens in tabelvorm vormgegeven. In dit geval is "structuur" sectie vereist voor zowel invoer- en uitvoergegevenssets. Kolom toewijzing ondersteunt **toewijzing van alle of een subset van kolommen in de brongegevensset "structuur" voor alle kolommen in de sink-gegevensset "structuur"** . Hier volgen foutcondities die leiden tot een uitzondering:

* Brongegevens opgeslagen resultaat heeft geen naam van een kolom die is opgegeven in de invoergegevensset "structuur" sectie query.
* Sink-gegevensopslag (als met vooraf gedefinieerd schema) heeft geen naam van een kolom die is opgegeven in de sectie uitvoer gegevensset "structuur".
* Minder kolommen of meer kolommen in de 'structuur"van de sink-gegevensset dan opgegeven in de toewijzing.
* Dubbele toewijzing.

In het volgende voorbeeld wordt de invoergegevensset is een structuur en of deze verwijst naar een tabel in een on-premises Oracle-database.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

In dit voorbeeld heeft een structuur van de uitvoergegevensset en of deze verwijst naar een tabel in Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

De volgende JSON definieert een kopieeractiviteit in een pijplijn. De kolommen uit de bron die zijn toegewezen aan kolommen in sink met behulp van de **translator** -> **columnMappings** eigenschap.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Als u de syntaxis van `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` om op te geven in de kolomtoewijzing, wordt nog steeds ondersteund als-is.

### <a name="alternative-schema-mapping"></a>Alternatieve schematoewijzing

Kunt u copy activity -> `translator`  ->  `schemaMapping` om toe te wijzen tussen hiërarchische gevormde gegevens en gegevens in tabelvorm vorm, bijvoorbeeld kopiëren van MongoDB/REST-bestand te kopiëren van Oracle naar Azure Cosmos DB-API voor MongoDB. De volgende eigenschappen worden ondersteund in de kopieeractiviteit `translator` sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de activiteit kopiëren translator moet worden ingesteld op: **TabularTranslator** | Ja |
| schemaMapping | Een verzameling van sleutel / waarde-paren, die staat voor de relatie toewijzing **van de bronkant naar het sink-zijde**.<br/>- **Sleutel:** bron vertegenwoordigt. Voor **in tabelvorm bron**, geef de naam van de kolom zoals gedefinieerd in de gegevenssetstructuur; voor **hiërarchische bron**, geef de JSON-padexpressie voor elk veld om te worden toegewezen.<br>- **Waarde:** vertegenwoordigt sink. Voor **in tabelvorm sink**, geef de naam van de kolom zoals gedefinieerd in de gegevenssetstructuur; voor **hiërarchische sink**, geef de JSON-padexpressie voor elk veld om te worden toegewezen. <br>In het geval van hiërarchische gegevens, voor velden onder het hoofdobject, begint JSON-pad met root $; voor velden binnen de matrix die is gekozen door `collectionReference` eigenschap, JSON-pad begint met het matrixelement.  | Ja |
| collectionReference | Als u wilt herhalen en gegevens ophalen uit de objecten **in een matrixveld** met hetzelfde patroon en converteren naar per rij per object geeft u het JSON-pad van die matrix voor cross-pas. Deze eigenschap wordt alleen ondersteund als hiërarchische gegevens bron is. | Nee |

**Voorbeeld: kopieer van MongoDB naar Oracle:**

Bijvoorbeeld, hebt u MongoDB-document met de volgende inhoud:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

en u wilt kopiëren naar een Azure SQL-tabel in de volgende indeling, door te maken van de gegevens in de matrix *(order_pd en order_price)* en cross join met de algemene root-gegevens *(getal, datum en plaats)* :

| orderNumber | Orderdatum | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

De schema-toewijzingsregel configureren als de volgende kopie activiteit JSON-voorbeeld:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Toewijzing van het type gegevens

Kopieeractiviteit voert typen gegevensbronnen sink-type toewijzing met de volgende stappen 2-benadering:

1. Systeemeigen brontypen converteren naar tussentijdse gegevenstypen voor Azure Data Factory
2. Azure Data Factory tussentijdse gegevenstypen converteren naar systeemeigen sink-type

Hier vindt u de toewijzing tussen de systeemeigen naar tussentijdse type in de sectie "Data type toewijzing" in het onderwerp van elke connector.

### <a name="supported-data-types"></a>Ondersteunde gegevenstypen

Data Factory ondersteunt de volgende tijdelijke gegevenstypen: U kunt onderstaande waarden opgeven bij het configureren van type informatie in [gegevenssetstructuur](concepts-datasets-linked-services.md#dataset-structure-or-schema) configuratie:

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen van de Kopieeractiviteit:

- [Overzicht kopieeractiviteit](copy-activity-overview.md)
