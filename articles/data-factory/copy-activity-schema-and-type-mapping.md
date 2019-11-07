---
title: Schematoewijzing in kopieeractiviteit
description: Meer informatie over de manier waarop Kopieer activiteiten in Azure Data Factory kaarten en gegevens typen van bron gegevens worden toegewezen om gegevens te sinken bij het kopiëren van gegevens.
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
ms.openlocfilehash: ed0823930b819661baf384d51478547cb2e0eccf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678140"
---
# <a name="schema-mapping-in-copy-activity"></a>Schematoewijzing in kopieeractiviteit

In dit artikel wordt beschreven hoe de Azure Data Factory Kopieer activiteit de schema toewijzing en het gegevens type toewijzing van bron gegevens tot het opvangen van gegevens wanneer de gegevens kopie wordt uitgevoerd.

## <a name="schema-mapping"></a>Schema toewijzing

Kolom toewijzing wordt toegepast bij het kopiëren van gegevens van bron naar sink. Kopieer de bron gegevens van de activiteit **toewijzing standaard naar Sink op kolom namen**. U kunt een [expliciete toewijzing](#explicit-mapping) opgeven om de kolom toewijzing aan te passen op basis van uw behoeften. Meer specifiek, kopieer activiteit:

1. De gegevens van de bron lezen en het bron schema bepalen
2. Gebruik de standaard kolom toewijzing om kolommen op naam toe te wijzen of expliciete kolom toewijzing toe te passen als deze is opgegeven.
3. De gegevens naar sink schrijven

### <a name="explicit-mapping"></a>Expliciete toewijzing

U kunt opgeven welke kolommen u wilt toewijzen in de eigenschap Copy activity-> `translator` -> `mappings`. In het volgende voor beeld wordt een Kopieer activiteit in een pijp lijn gedefinieerd om gegevens van gescheiden tekst te kopiëren naar Azure SQL Database.

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

De volgende eigenschappen worden ondersteund onder `translator` -> object `mappings`-> met `source` en `sink`:

| Eigenschap | Beschrijving                                                  | Vereist |
| -------- | ------------------------------------------------------------ | -------- |
| naam     | De naam van de bron-of sink-kolom.                           | Ja      |
| rang telwoord  | Kolom index. Begin met 1. <br>Toep assen en vereist voor het gebruik van gescheiden tekst zonder header-lijn. | Nee       |
| programmapad     | De expressie JSON-pad voor elk veld dat moet worden uitgepakt of toegewezen. Toep assen op hiërarchische gegevens, bijvoorbeeld MongoDB/REST.<br>Voor velden onder het hoofd object begint het JSON-pad met root $; voor velden in de matrix die worden gekozen door `collectionReference` eigenschap, begint het JSON-pad vanuit het matrix element. | Nee       |
| type     | Data Factory tussentijds gegevens type van de kolom Source of sink. | Nee       |
| culturele  | Cultuur van de kolom Source of sink. <br>Toep assen wanneer het type `Datetime` of `Datetimeoffset`is. De standaardwaarde is `en-us`. | Nee       |
| Formatteer   | Indelings teken reeks die moet worden gebruikt als het type `Datetime` of `Datetimeoffset`is. Raadpleeg de [aangepaste datum-en tijd notatie teken reeksen](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) voor het opmaken van DateTime. | Nee       |

De volgende eigenschappen worden ondersteund onder `translator` -> `mappings` naast object met `source` en `sink`:

| Eigenschap            | Beschrijving                                                  | Vereist |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Wordt alleen ondersteund als hiërarchische gegevens, zoals MongoDB/REST, bron is.<br>Als u gegevens wilt sequentieel en wilt ophalen uit de objecten **in een matrix veld** met hetzelfde patroon en converteren naar per rij per object, geeft u het JSON-pad van die matrix op om meerdere aanvragen uit te voeren. | Nee       |

### <a name="alternative-column-mapping"></a>Alternatieve kolom toewijzing

U kunt een Kopieer activiteit-> `translator` -> `columnMappings` opgeven om te koppelen tussen gegevens in tabel vorm. In dit geval is de sectie ' Structure ' vereist voor de invoer-en uitvoer gegevens sets. Kolom toewijzing ondersteunt het **toewijzen van alle of subset kolommen in de bron gegevensset ' structuur ' op alle kolommen in de Sink-gegevensset ' Structure**'. Hieronder vindt u fout voorwaarden die resulteren in een uitzonde ring:

* Het query resultaat van de brongegevens opslag heeft geen kolom naam die is opgegeven in de sectie structuur van de invoer gegevensset.
* Het sink-gegevens archief (als met een vooraf gedefinieerd schema) heeft geen kolom naam die is opgegeven in de sectie ' Structure ' van de uitvoer gegevensset.
* Minder kolommen of meer kolommen in de structuur van de Sink-gegevensset dan is opgegeven in de toewijzing.
* Dubbele toewijzing.

In het volgende voor beeld heeft de gegevensset van de invoer een structuur en verwijst deze naar een tabel in een on-premises Oracle-data base.

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

In dit voor beeld heeft de uitvoer gegevensset een structuur en verwijst deze naar een tabel in Salesfoce.

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

De volgende JSON definieert een Kopieer activiteit in een pijp lijn. De kolommen van de bron die is toegewezen aan de kolommen in Sink met behulp van de eigenschap **translator** -> **columnMappings** .

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

Als u de syntaxis van `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` gebruikt om kolom toewijzing op te geven, wordt deze nog steeds ondersteund als-is.

### <a name="alternative-schema-mapping"></a>Alternatieve schema toewijzing

U kunt Kopieer activiteit-> `translator` -> `schemaMapping` in te stellen tussen hiërarchische gegevens en gegevens in tabel vorm, zoals kopiëren van MongoDB/REST naar tekst bestand en kopiëren van Oracle naar Azure Cosmos DB API voor MongoDB. De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit `translator`:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van het conversie programma Copy activity moet worden ingesteld op: **TabularTranslator** | Ja |
| schemaMapping | Een verzameling sleutel-waardeparen, die de toewijzings relatie vertegenwoordigt **van de bron aan de Sink-zijde**.<br/>- **sleutel:** vertegenwoordigt de bron. Voor **tabel bron**geeft u de naam van de kolom op zoals gedefinieerd in de structuur van de gegevensset. voor een **hiërarchische bron**geeft u de JSON-padexpressie op voor elk veld dat u wilt uitpakken en toewijzen.<br>- **waarde:** vertegenwoordigt sink. Voor de **tabellaire Sink**geeft u de naam van de kolom op zoals gedefinieerd in de structuur van de gegevensset. voor **hiërarchische Sink**geeft u de JSON-padexpressie op voor elk veld dat u wilt uitpakken en toewijzen. <br>In het geval van hiërarchische gegevens, voor velden onder hoofd object, begint JSON-pad met root $; voor velden in de matrix die worden gekozen door `collectionReference` eigenschap, begint het JSON-pad vanuit het matrix element.  | Ja |
| collectionReference | Als u gegevens wilt sequentieel en wilt ophalen uit de objecten **in een matrix veld** met hetzelfde patroon en converteren naar per rij per object, geeft u het JSON-pad van die matrix op om meerdere aanvragen uit te voeren. Deze eigenschap wordt alleen ondersteund wanneer hiërarchische gegevens bron is. | Nee |

**Voor beeld: kopiëren van MongoDB naar Oracle:**

Als u bijvoorbeeld een MongoDB-document hebt met de volgende inhoud:

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

en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling door de gegevens in de matrix *(order_pd en order_price)* samen te voegen en cross samen te voegen met de gemeen schappelijke basis informatie *(nummer, datum en plaats)* :

| orderNumber | Order | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configureer de schema toewijzings regel als de volgende JSON-voor beeld van een Kopieer activiteit:

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

## <a name="data-type-mapping"></a>Toewijzing van gegevens type

Met de Kopieer activiteit worden bron typen voor de toewijzing van Sink-typen uitgevoerd met de volgende methode voor twee stappen:

1. Converteren van systeem eigen bron typen naar Azure Data Factory tussenliggende gegevens typen
2. Converteren van Azure Data Factory tussenliggende gegevens typen naar systeem eigen Sink-type

U vindt de toewijzing tussen systeem eigen type en tussentijds type in de sectie gegevens type toewijzing in elk onderwerp van de connector.

### <a name="supported-data-types"></a>Ondersteunde gegevens typen

Data Factory ondersteunt de volgende tussenliggende gegevens typen: u kunt de onderstaande waarden opgeven bij het configureren van type-informatie in de configuratie van de [gegevensset-structuur](concepts-datasets-linked-services.md#dataset-structure-or-schema) :

* Byte []
* Booleaans
* Datetime
* Date time offset
* Komma
* Double-waarde
* GUID
* Int16
* Int32
* Int64
* Enkelvoudig
* Tekenreeks
* Periode

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
