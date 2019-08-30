---
title: Opzoek activiteit in Azure Data Factory | Microsoft Docs
description: Leer hoe u de opzoek activiteit kunt gebruiken om een waarde te zoeken vanuit een externe bron. Naar deze uitvoer kan worden verwezen door geslaagde activiteiten.
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
ms.openlocfilehash: 9658987092027b38ab0cab1feb3df4be0a91e350
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141651"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Opzoek activiteit in Azure Data Factory

Met opzoek activiteit kan een gegevensset worden opgehaald uit een van de Azure Data Factory ondersteunde gegevens bronnen. Gebruik het in het volgende scenario:
- Bepaal dynamisch welke objecten in een volgende activiteit moeten worden gebruikt, in plaats van de naam van het object vast te schrijven. Enkele voor beelden van objecten zijn bestanden en tabellen.

Met opzoek activiteit wordt de inhoud van een configuratie bestand of-tabel gelezen en geretourneerd. Het resultaat van het uitvoeren van een query of opgeslagen procedure wordt ook geretourneerd. De uitvoer van de opzoek activiteit kan worden gebruikt in een volgende Kopieer-of transformatie activiteit als dit een singleton waarde is. De uitvoer kan worden gebruikt in een ForEach-activiteit als dit een matrix met kenmerken is.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De volgende gegevens bronnen worden ondersteund voor opzoek activiteiten. Het grootste aantal rijen dat kan worden geretourneerd door de opzoek activiteit is 5.000, Maxi maal 2 MB groot. Momenteel is de langste duur voor de opzoek activiteit vóór een time-out van één uur.

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

## <a name="type-properties"></a>Type-eigenschappen

Name | Description | type | Vereist?
---- | ----------- | ---- | --------
sets | Bevat de referentie voor de gegevensset voor de zoek actie. Details ophalen uit de sectie **Eigenschappen van gegevensset** in elk bijbehorend connector artikel. | Sleutel/waarde-paar | Ja
source | Bevat eigenschappen van een gegevensset, hetzelfde als de bron van de Kopieer activiteit. Details ophalen uit de sectie **Eigenschappen van Kopieer activiteit** in elk bijbehorende connector-artikel. | Sleutel/waarde-paar | Ja
firstRowOnly | Geeft aan of alleen de eerste rij of alle rijen worden geretourneerd. | Boolean-waarde | Nee. De standaardwaarde is `true`.

> [!NOTE]
> 
> * Bron kolommen met het type byteas worden niet ondersteund.
> * De **structuur** wordt niet ondersteund in de definitie van de gegevensset. Gebruik voor tekstindelings bestanden de rij met koppen om de kolom naam op te geven.
> * Als uw lookup-bron een JSON-bestand is `jsonPathDefinition` , wordt de instelling voor het vorm geven van het JSON-object niet ondersteund. De volledige objecten worden opgehaald.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Het resultaat van de opzoek activiteit gebruiken in een volgende activiteit

Het Zoek resultaat wordt geretourneerd in de `output` sectie van het resultaat van de uitvoering van de activiteit.

* **Wanneer`firstRowOnly` is ingesteld op `true` (standaard)** , is de uitvoer indeling zoals wordt weer gegeven in de volgende code. Het Zoek resultaat bevindt zich `firstRow` onder een vaste sleutel. Gebruik het patroon van `@{activity('MyLookupActivity').output.firstRow.TableName}`om het resultaat in de volgende activiteit te gebruiken.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Wanneer`firstRowOnly` is ingesteld op `false`** , is de uitvoer indeling zoals in de volgende code wordt weer gegeven. Een `count` veld geeft aan hoeveel records er worden geretourneerd. Gedetailleerde waarden worden weer gegeven onder een `value` vaste matrix. In een dergelijk geval wordt de opzoek activiteit gevolgd door een [foreach-activiteit](control-flow-for-each-activity.md). U geeft de `value` matrix door aan het veld `items` foreach-activiteit met behulp `@activity('MyLookupActivity').output.value`van het patroon van. Als u elementen in de `value` matrix wilt openen, gebruikt u de `@{activity('lookupActivity').output.value[zero based index].propertyname}`volgende syntaxis:. Een voorbeeld is `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Voor beeld van Kopieer activiteit
In dit voor beeld kopieert u met de Kopieer activiteit gegevens uit een SQL-tabel in uw Azure SQL Database-exemplaar naar Azure Blob-opslag. De naam van de SQL-tabel wordt opgeslagen in een JSON-bestand in Blob Storage. De opzoek activiteit zoekt de tabel naam tijdens runtime. JSON wordt dynamisch gewijzigd met behulp van deze methode. U hoeft geen pijp lijnen of gegevens sets opnieuw te implementeren. 

In dit voor beeld wordt alleen de zoek functie voor de eerste rij gedemonstreerd. Zie de voor beelden in [meerdere tabellen tegelijk kopiëren](tutorial-bulk-copy.md)met behulp van Azure Data Factory voor het opzoeken van alle rijen en het koppelen van de resultaten met de foreach-activiteit.

### <a name="pipeline"></a>Pijplijn
Deze pijp lijn bevat twee activiteiten: Opzoeken en kopiëren. 

- De opzoek activiteit is geconfigureerd voor het gebruik van **LookupDataset**, die verwijst naar een locatie in Azure Blob Storage. De opzoek activiteit leest de naam van de SQL-tabel vanuit een JSON-bestand op deze locatie. 
- De Kopieer activiteit maakt gebruik van de uitvoer van de opzoek activiteit. Dit is de naam van de SQL-tabel. De eigenschap **TableName** in de **Source dataset** is geconfigureerd voor het gebruik van de uitvoer van de opzoek activiteit. Met de Kopieer activiteit worden gegevens uit de SQL-tabel gekopieerd naar een locatie in Azure Blob Storage. De locatie wordt opgegeven door de eigenschap **SinkDataset** . 

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

### <a name="lookup-dataset"></a>Zoek gegevensset
De **lookup** -gegevensset is het bestand **SourceTable. json** in de Azure Storage lookup-map die is opgegeven in het **AzureStorageLinkedService** -type. 

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

### <a name="source-dataset-for-copy-activity"></a>**Bron** gegevensset voor kopieer activiteit
De **bron** -gegevensset gebruikt de uitvoer van de opzoek activiteit, die de naam van de SQL-tabel is. Met de Kopieer activiteit worden gegevens uit deze SQL-tabel gekopieerd naar een locatie in Azure Blob Storage. De locatie wordt opgegeven door de **sink** -gegevensset. 

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

### <a name="sink-dataset-for-copy-activity"></a>**Sink** -gegevensset voor kopieer activiteit
Met de Kopieer activiteit worden gegevens uit de SQL-tabel gekopieerd naar het **filebylookup. CSV** -bestand in de **CSV** -map in azure Storage. Het bestand wordt opgegeven door de eigenschap **AzureStorageLinkedService** . 

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
Dit opslag account bevat het JSON-bestand met de namen van de SQL-tabellen. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service
Dit Azure SQL Database-exemplaar bevat de gegevens die moeten worden gekopieerd naar de Blob-opslag. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Set met objecten

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

#### <a name="array-of-objects"></a>Matrix van objecten

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

Hier volgen enkele beperkingen van de opzoek activiteit en voorgestelde tijdelijke oplossingen.

| Beperking | Tijdelijke oplossing |
|---|---|
| De opzoek activiteit heeft Maxi maal 5.000 rijen en een maximale grootte van 2 MB. | Ontwerp een tweedelige pijp lijn waarbij de buitenste pijp lijn zich herhaalt over een interne pijp lijn, waarmee gegevens worden opgehaald die niet groter zijn dan de maximum rijen of-grootte. |
| | |

## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund: 

- [Pijplijn activiteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Activiteit ForEach](control-flow-for-each-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit Web](control-flow-web-activity.md)
