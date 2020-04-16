---
title: Opzoekactiviteit in Azure Data Factory
description: Meer informatie over het gebruik van opzoekactiviteit om een waarde op te zoeken vanuit een externe bron. Deze output kan verder worden verwezen door het opvolgen van activiteiten.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 02abdaf46ca2af6c96d3b5e8d4ce5876831bd415
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417995"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Opzoekactiviteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zoekactiviteit kan een gegevensset ophalen uit een van de door Azure Data Factory ondersteunde gegevensbronnen. Gebruik het in het volgende scenario:
- Bepaal dynamisch welke objecten moeten worden gebruikt in een volgende activiteit, in plaats van de objectnaam hard te coderen. Sommige objectvoorbeelden zijn bestanden en tabellen.

Opzoekactiviteit leest en retourneert de inhoud van een configuratiebestand of -tabel. Het retourneert ook het resultaat van het uitvoeren van een query of opgeslagen procedure. De uitvoer van opzoekactiviteit kan worden gebruikt in een volgende kopieer- of transformatieactiviteit als het een singleton-waarde is. De uitvoer kan worden gebruikt in een ForEach-activiteit als het een array met kenmerken is.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De volgende gegevensbronnen worden ondersteund voor opzoekactiviteit. Het grootste aantal rijen dat kan worden geretourneerd door opzoekactiviteit is 5.000, tot 2 MB groot. Momenteel is de langste duur voor opzoekactiviteit vóór een time-out een uur.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntaxis

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Eigenschappen typen

Naam | Beschrijving | Type | Vereist?
---- | ----------- | ---- | --------
Dataset | Bevat de gegevenssetverwijzing voor de opzoeking. Informatie in de sectie **Gegevensseteigenschappen** in elk corresponderend verbindingsartikel. | Sleutel-/waardepaar | Ja
source | Bevat gegevenssetspecifieke broneigenschappen, hetzelfde als de bron Activiteit kopiëren. Informatie in de sectie **Activiteitseigenschappen kopiëren** in elk corresponderend verbindingsartikel. | Sleutel-/waardepaar | Ja
firstRowOnly | Hiermee geeft u aan of u alleen de eerste rij of alle rijen wilt retourneren. | Booleaans | Nee. De standaardwaarde is `true`.

> [!NOTE]
> 
> * Bronkolommen met **bytearray-type** worden niet ondersteund.
> * **Structuur** wordt niet ondersteund in gegevenssetdefinities. Gebruik de koprij voor tekstnotatiebestanden om de kolomnaam op te geven.
> * Als uw opzoekbron een JSON-bestand is, wordt de `jsonPathDefinition` instelling voor het opnieuw vormgeven van het JSON-object niet ondersteund. De volledige objecten worden opgehaald.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Het resultaat van de opzoekactiviteit in een volgende activiteit gebruiken

Het opzoekresultaat wordt `output` geretourneerd in het gedeelte van het resultaat van de activiteitsrun.

* **Wanneer `firstRowOnly` is `true` ingesteld op (standaard)**, is de uitvoernotatie zoals weergegeven in de volgende code. Het opzoekresultaat bevindt `firstRow` zich onder een vaste sleutel. Als u het resultaat wilt gebruiken `@{activity('MyLookupActivity').output.firstRow.TableName}`in de volgende activiteit, gebruikt u het patroon van .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Wanneer `firstRowOnly` is `false`ingesteld op **, is de uitvoernotatie zoals weergegeven in de volgende code. Een `count` veld geeft aan hoeveel records worden geretourneerd. Gedetailleerde waarden worden weergegeven `value` onder een vaste array. In een dergelijk geval wordt de opzoekactiviteit gevolgd door een [Foreach-activiteit](control-flow-for-each-activity.md). U geeft `value` de array door `items` aan het activiteitsveld ForEach met behulp van het patroon van `@activity('MyLookupActivity').output.value`. Als u toegang `value` wilt krijgen tot `@{activity('lookupActivity').output.value[zero based index].propertyname}`elementen in de array, gebruikt u de volgende syntaxis: . Een voorbeeld is `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Voorbeeld van activiteit kopiëren
Kopieer in dit voorbeeld Activiteit kopieert gegevens uit een SQL-tabel in uw Azure SQL Database-exemplaar naar Azure Blob-opslag. De naam van de SQL-tabel wordt opgeslagen in een JSON-bestand in Blob-opslag. Met de activiteit Opzoeken wordt de tabelnaam bij runtime opzoekt. JSON wordt dynamisch aangepast door deze aanpak te gebruiken. U hoeft pijplijnen of gegevenssets niet opnieuw te implementeren. 

In dit voorbeeld wordt alleen voor de eerste rij opzoekingen getoond. Zie de voorbeelden in Meerdere tabellen kopiëren in bulk met [Azure Data Factory](tutorial-bulk-copy.md)voor het opzoeken van alle rijen en om de resultaten te ketenen met ForEach-activiteit.

### <a name="pipeline"></a>Pijplijn
Deze pijplijn bevat twee activiteiten: Opzoeken en kopiëren. 

- De opzoekactiviteit is geconfigureerd voor het gebruik **van LookupDataset,** wat verwijst naar een locatie in Azure Blob-opslag. De opzoekactiviteit leest de naam van de SQL-tabel uit een JSON-bestand op deze locatie. 
- Kopieeractiviteit gebruikt de uitvoer van de opzoekactiviteit, de naam van de SQL-tabel. De eigenschap **tableName** in de **SourceDataset** is geconfigureerd om de uitvoer van de opzoekactiviteit te gebruiken. Activiteit kopiëren kopieert gegevens uit de SQL-tabel naar een locatie in Azure Blob-opslag. De locatie wordt opgegeven door de eigenschap **SinkDataset.** 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Gegevensset Opzoeken
De **zoekset** is het **bestand sourcetable.json** in de azure storage-opzoekmap die is opgegeven door het **azureStorageLinkedService-type.** 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Brongegevensset** voor Kopieeractiviteit
De **brongegevensset** maakt gebruik van de uitvoer van de opzoekactiviteit, de naam van de SQL-tabel. Activiteit kopiëren kopieert gegevens uit deze SQL-tabel naar een locatie in Azure Blob-opslag. De locatie wordt **sink** opgegeven door de sink-gegevensset. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Gegevensset sink** voor Activiteit kopiëren
Activiteit kopiëren kopieert gegevens uit de SQL-tabel naar het **bestand filebylookup.csv** in de **csv-map** in Azure Storage. Het bestand wordt opgegeven door de eigenschap **AzureStorageLinkedService.** 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Dit opslagaccount bevat het JSON-bestand met de namen van de SQL-tabellen. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service
Deze instantie Azure SQL Database bevat de gegevens die naar Blob-opslag moeten worden gekopieerd. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Reeks objecten

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Array van objecten

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

Hier volgen enkele beperkingen van de opzoekactiviteit en voorgestelde tijdelijke oplossingen.

| Beperking | Tijdelijke oplossing |
|---|---|
| De opzoekactiviteit heeft een maximum van 5.000 rijen en een maximale grootte van 2 MB. | Ontwerp een pijplijn op twee niveaus waarin de buitenste pijplijn over een binnenpijplijn wordt gehesen, die gegevens ophaalt die de maximale rijen of grootte niet overschrijden. |
| | |

## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory: 

- [Pijplijnactiviteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Activiteit ForEach](control-flow-for-each-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
