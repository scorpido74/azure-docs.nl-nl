---
title: Gegevenssetstoewijzen in Azure Data Factory
description: Meer informatie over het toewijzen van bronkolommen aan doelkolommen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6eb7012e28319ee6cc86de5ee56090743d681068
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923873"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Kolommen met gegevenssetgegevensset van kaart naar kolommen met doelgegevensset
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

Kolomtoewijzing kan worden gebruikt om op te geven hoe kolommen die zijn opgegeven in de "structuur" van de brontabelkaart naar kolommen die zijn opgegeven in de "structuur" van de gootsteentabel. De eigenschap **columnMapping** is beschikbaar in de sectie **tekstEigenschappen** van de activiteit Kopiëren.

Kolomtoewijzing ondersteunt de volgende scenario's:

* Alle kolommen in de structuur van de brongegevensset worden toegewezen aan alle kolommen in de structuur van de sinkdataset.
* Een subset van de kolommen in de brongegevenssetstructuur wordt toegewezen aan alle kolommen in de structuur van de sinkdataset.

De volgende fouten zijn foutvoorwaarden die resulteren in een uitzondering:

* Minder kolommen of meer kolommen in de 'structuur' van de gootsteentabel dan opgegeven in de toewijzing.
* Dubbele toewijzing.
* Sql-queryresultaat heeft geen kolomnaam die is opgegeven in de toewijzing.

> [!NOTE]
> De volgende voorbeelden zijn voor Azure SQL en Azure Blob, maar zijn van toepassing op alle gegevensarchiefdat rechthoekige gegevenssets ondersteunt. Pas gegevensset- en gekoppelde servicedefinities aan in voorbeelden om naar gegevens in de relevante gegevensbron te wijzen.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Voorbeeld 1 – kolomtoewijzing van Azure SQL naar Azure-blob
In dit voorbeeld heeft de invoertabel een structuur en wijst deze naar een SQL-tabel in een Azure SQL-database.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

In dit voorbeeld heeft de uitvoertabel een structuur en wijst deze naar een blob in een Azure blob-opslag.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

De volgende JSON definieert een kopieeractiviteit in een pijplijn. De kolommen van bron toegewezen aan kolommen in gootsteen **(kolomMappings)** met behulp van de eigenschap **Vertaler.**

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Kolomtoewijzingsstroom:**

![Kolomtoewijzingsstroom](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Voorbeeld 2 – kolomtoewijzing met SQL-query van Azure SQL naar Azure blob
In dit voorbeeld wordt een SQL-query gebruikt om gegevens uit Azure SQL te extraheren in plaats van simpelweg de tabelnaam en de kolomnamen op te geven in de sectie 'structuur'. 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
In dit geval worden de queryresultaten eerst toegewezen aan kolommen die zijn opgegeven in 'structuur' van de bron. Vervolgens worden de kolommen van bronstructuur toegewezen aan kolommen in gootsteenstructuur met regels die zijn opgegeven in kolomToewijzingen.  Stel dat de query 5 kolommen retourneert, twee kolommen meer dan die welke zijn opgegeven in de 'structuur' van de bron.

**Kolomtoewijzingsstroom**

![Stroom van kolomtoewijzing-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Volgende stappen
Zie het artikel voor een zelfstudie over het gebruik van Kopieeractiviteit: 

- [Gegevens kopiëren van Blob-opslag naar SQL-database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
