---
title: Schematoewijzing in kopieeractiviteit
description: Meer informatie over hoe kopieeractiviteit in Azure Data Factory schema's en gegevenstypen uit brongegevens in kaart brengt om gegevens te laten zinken bij het kopiëren van gegevens.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 9f04955fb910a6159dc09ac40a87a398e67d59d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414119"
---
# <a name="schema-mapping-in-copy-activity"></a>Schematoewijzing in kopieeractiviteit
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe de activiteit azure data factory-kopieeractiviteit schematoewijzing en gegevenstypetoewijzing van brongegevens uitvoert om gegevens te laten zinken bij het uitvoeren van de gegevenskopie.

## <a name="schema-mapping"></a>Schematoewijzing

Kolomtoewijzing is van toepassing bij het kopiëren van gegevens van bron naar gootsteen. Kopieer standaard **brongegevens van activiteitenmap om te zinken op kolomnamen**. U [expliciete toewijzing](#explicit-mapping) opgeven om de kolomtoewijzing aan te passen op basis van uw behoefte. Meer in het bijzonder, kopieer activiteit:

1. Lees de gegevens van de bron en bepaal het bronschema
2. Gebruik standaardkolomtoewijzing om kolommen op naam toe te voegen of expliciete kolomtoewijzing toe te passen als deze is opgegeven.
3. Schrijf de gegevens te zinken

### <a name="explicit-mapping"></a>Expliciete toewijzing

U de kolommen opgeven die `translator`  ->  `mappings` u wilt toewijzen in de eigenschap > kopiëren. In het volgende voorbeeld wordt een kopieeractiviteit in een pijplijn gedefinieerd om gegevens van afgebakende tekst naar Azure SQL Database te kopiëren.

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

De volgende eigenschappen worden `translator`  ->  `mappings` ondersteund onder `source` `sink`> object met en:

| Eigenschap | Beschrijving                                                  | Vereist |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Naam van de bron- of gootsteenkolom.                           | Ja      |
| Ordinale  | Kolomindex. Begin met 1. <br>Toepassen en vereist bij het gebruik van afgebakende tekst zonder koptekstregel. | Nee       |
| path     | JSON-padexpressie voor elk veld dat moet worden geëxtraheerd of toegewezen. Solliciteer voor hiërarchische gegevens zoals MongoDB/REST.<br>Voor velden onder het hoofdobject begint het JSON-pad met root $; voor velden binnen de `collectionReference` array die door eigenschap zijn gekozen, gaat JSON-pad uit van het array-element. | Nee       |
| type     | Data Factory interim-gegevenstype van de bron- of sinkkolom. | Nee       |
| Cultuur  | Cultuur van de bron of gootsteenkolom. <br>Toepassen wanneer `Datetime` type `Datetimeoffset`is of . De standaardwaarde is `en-us`. | Nee       |
| formaat   | Tekenreeks opmaken die moet `Datetime` `Datetimeoffset`worden gebruikt wanneer tekst is of . Raadpleeg [tekenreeksen voor aangepaste datum- en tijdnotatie](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) voor het opmaken van datumtijd. | Nee       |

De volgende eigenschappen worden `translator`  ->  `mappings` ondersteund onder `source` in `sink`aanvulling op object met en :

| Eigenschap            | Beschrijving                                                  | Vereist |
| ------------------- | ------------------------------------------------------------ | -------- |
| verzamelingReferentie | Alleen ondersteund wanneer hiërarchische gegevens zoals MongoDB/REST bron zijn.<br>Als u gegevens wilt herhalen en extraheren uit de objecten **in een arrayveld** met hetzelfde patroon en wilt converteren naar per rij per object, geeft u het JSON-pad van die array op om cross-apply te doen. | Nee       |

### <a name="alternative-column-mapping"></a>Alternatieve kolomtoewijzing

U kopieeractiviteit `translator`  ->  `columnMappings` -> opgeven om tussen tabelvormige gegevens in kaart te brengen. In dit geval is de sectie "structuur" vereist voor zowel invoer- als uitvoergegevenssets. Kolomtoewijzing ondersteunt **het toewijzen van alle of subset van kolommen in de 'structuur structuur' van de brongegevensset naar alle kolommen in de 'structuur structuur' van de sink-gegevensset.** De volgende fouten zijn foutvoorwaarden die resulteren in een uitzondering:

* Queryresultaten voor brongegevensopslag hebben geen kolomnaam die is opgegeven in de sectie 'structuurstructuur' van de invoergegevensset.
* Sink-gegevensarchief (als met vooraf gedefinieerd schema) heeft geen kolomnaam die is opgegeven in de sectie 'structuurstructuur' van uitvoergegevensset.
* Minder kolommen of meer kolommen in de 'structuur' van de sinkset dan opgegeven in de toewijzing.
* Dubbele toewijzing.

In het volgende voorbeeld heeft de invoergegevensset een structuur en wijst deze naar een tabel in een on-premises Oracle-database.

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

In dit voorbeeld heeft de uitvoergegevensset een structuur en wijst deze naar een tabel in Salesfoce.

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

De volgende JSON definieert een kopieeractiviteit in een pijplijn. De kolommen van bron toegewezen aan kolommen in gootsteen met behulp van de **eigenschap vertaler** -> **columnMappings.**

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

Als u de syntaxis gebruikt om `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` kolomtoewijzing op te geven, wordt deze nog steeds ondersteund als is.

### <a name="alternative-schema-mapping"></a>Alternatieve schematoewijzing

U kopieeractiviteit `translator`  ->  `schemaMapping` -> toewijzen tussen hiërarchischgevormde gegevens en tabelvormige gegevens, bijvoorbeeld kopiëren van MongoDB/REST naar tekstbestand en kopiëren van Oracle naar Azure Cosmos DB's API voor MongoDB. De volgende eigenschappen worden ondersteund `translator` in de sectie kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de vertaler voor kopieeractiviteit moet worden ingesteld op: **TabularTranslator** | Ja |
| schemaToewijzing | Een verzameling sleutelwaardeparen, die de mappingrelatie **van bronzijde tot gootsteenzijde**vertegenwoordigt.<br/>- **Sleutel:** vertegenwoordigt bron. Geef **voor tabelbron**de kolomnaam op zoals gedefinieerd in de structuur van de gegevensset. voor **hiërarchische bron**geeft u de JSON-padexpressie op voor elk veld dat moet worden geëxtraheerd en toegewezen.<br>- **Waarde:** vertegenwoordigt gootsteen. Geef **voor tabelgooteens**de kolomnaam op zoals gedefinieerd in de structuur van de gegevensset. voor **hiërarchische gootsteen**geeft u de JSON-padexpressie op voor elk veld dat moet worden geëxtraheerd en toegewezen. <br>In het geval van hiërarchische gegevens begint JSON-pad voor velden onder hoofdobject met root $; voor velden binnen de `collectionReference` array die door eigenschap zijn gekozen, gaat JSON-pad uit van het array-element.  | Ja |
| verzamelingReferentie | Als u gegevens wilt herhalen en extraheren uit de objecten **in een arrayveld** met hetzelfde patroon en wilt converteren naar per rij per object, geeft u het JSON-pad van die array op om cross-apply te doen. Deze eigenschap wordt alleen ondersteund wanneer hiërarchische gegevens bron zijn. | Nee |

**Voorbeeld: kopiëren van MongoDB naar Oracle:**

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

en u wilt deze kopiëren naar een Azure SQL-tabel in de volgende indeling, door de gegevens in de array af te vlakken *(order_pd en order_price)* en samen te voegen met de algemene hoofdinfo *(nummer, datum en plaats):*

| orderAantal | Orderdatum | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configureer de regel voor schematoewijzing als het volgende JSON-voorbeeld voor kopieeractiviteit:

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
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Toewijzing van gegevenstype

Met kopieeractiviteit worden brontypen uitgevoerd om typen te laten zinken met de volgende aanpak in twee stappen:

1. Converteer van native source-typen naar tijdelijke gegevenstypen van Azure Data Factory
2. Converteer van tijdelijke gegevenstypen azure data factory naar native sinktype

Kopieeractiviteit ondersteunt de volgende tussentijdse gegevenstypen: 

* Byte
* Booleaans
* Datum/tijd
* Datumtijdverschuiving
* Decimal
* Double
* GUID
* Int16
* Int32
* Int64
* Enkel
* Tekenreeks
* Periode

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen Copy Activity:

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
