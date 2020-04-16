---
title: Metagegevensactiviteit opslaan in Azure Data Factory
description: Meer informatie over het gebruik van de activiteit Metagegevens opvragen in een datafabriekpijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 344ad8e106c119c1de59570d1ec4e3df5e1cc8af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417111"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Metagegevensactiviteit opslaan in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U de activiteit Metagegevens ophalen gebruiken om de metagegevens van alle gegevens in Azure Data Factory op te halen. U deze activiteit gebruiken in de volgende scenario's:

- Valideer de metagegevens van alle gegevens.
- Activeer een pijplijn wanneer de gegevens gereed/beschikbaar zijn.

De volgende functionaliteit is beschikbaar in de besturingsstroom:

- U de uitvoer van de activiteit Metagegevens ophalen in voorwaardelijke expressies gebruiken om validatie uit te voeren.
- U een pijplijn activeren wanneer een voorwaarde is voldaan via Do Until-lussen.

## <a name="capabilities"></a>Functionaliteit

De activiteit Metagegevens opvragen neemt een gegevensset als invoer en retourneert metagegevens als uitvoer. Momenteel worden de volgende connectors en bijbehorende opvraagbare metagegevens ondersteund. De maximale grootte van geretourneerde metagegevens is 2 MB.

>[!NOTE]
>Als u de activiteit Metagegevens opvragen op een zelf gehoste nawerking sertijd voor integratie uitvoert, worden de nieuwste mogelijkheden ondersteund in versie 3.6 of hoger.

### <a name="supported-connectors"></a>Ondersteunde connectors

**Bestandsopslag**

| Connector/metagegevens | ObjectNaam<br>(bestand/map) | itemType<br>(bestand/map) | grootte<br>(bestand) | Gemaakt<br>(bestand/map) | Lastmodified<br>(bestand/map) |onderliggendE items<br>(map) |contentMD5<br>(bestand) | Structuur<br/>(bestand) | Columncount<br>(bestand) | Bestaat<br>(bestand/map) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob-opslag](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure-bestanden](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Bestandssysteem](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Wanneer u activiteit Metagegevens opvoert tegen een map, controleert u of u de machtiging LIST/EXECUTE hebt voor de opgegeven map.
- Voor Amazon S3 en `lastModified` Google Cloud Storage geldt dit voor de bucket `exists` en de sleutel, maar niet voor de virtuele map, en is van toepassing op de bucket en de sleutel, maar niet op het voorvoegsel of virtuele map.
- Voor Azure Blob-opslag geldt dit voor de container en de blob, `lastModified` maar niet op de virtuele map.
- `lastModified`filter is momenteel van toepassing op onderliggende items filteren, maar niet op de opgegeven map/bestand zelf.
- Wildcardfilter op mappen/bestanden wordt niet ondersteund voor activiteit Metagegevens downloaden.

**Relationele database**

| Connector/metagegevens | Structuur | Columncount | Bestaat |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Database-beheerde instantie](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL-gegevensmagazijn](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metagegevensopties

U de volgende metagegevenstypen opgeven in de lijst Metagegevens activiteit ophalen om de bijbehorende informatie op te halen:

| Metagegevenstype | Beschrijving |
|:--- |:--- |
| ObjectNaam | Naam van het bestand of de map. |
| itemType | Type van het bestand of de map. Geretourneerde `File` waarde `Folder`is of . |
| grootte | Grootte van het bestand, in bytes. Alleen van toepassing op bestanden. |
| Gemaakt | Gemaakt datumtijd van het bestand of de map. |
| Lastmodified | Laatst gewijzigde datumtijd van het bestand of de map. |
| onderliggendE items | Lijst met submappen en bestanden in de opgegeven map. Alleen van toepassing op mappen. Geretourneerde waarde is een lijst met de naam en het type van elk onderliggend item. |
| contentMD5 | MD5 van het bestand. Alleen van toepassing op bestanden. |
| Structuur | Gegevensstructuur van het bestand of de relationele databasetabel. Geretourneerde waarde is een lijst met kolomnamen en kolomtypen. |
| Columncount | Aantal kolommen in het bestand of de relationele tabel. |
| Bestaat| Of er een bestand, map of tabel bestaat. Als deze `exists` is opgegeven in de veldlijst Metagegevens opvragen, mislukt de activiteit niet, zelfs niet als het bestand, de map of de tabel niet bestaat. In `exists: false` plaats daarvan wordt geretourneerd in de uitvoer. |

>[!TIP]
>Wanneer u wilt valideren dat er een bestand, `exists` map of tabel bestaat, geeft u dit op in de lijst Metagegevensactiviteit opbrengen. U vervolgens `exists: true/false` het resultaat in de activiteitsuitvoer controleren. Als `exists` deze niet is opgegeven in de lijst met velden, mislukt de activiteit Metagegevens opvragen als het object niet wordt gevonden.

>[!NOTE]
>Wanneer u metagegevens uit `modifiedDatetimeStart` `modifiedDatetimeEnd`bestandsstores `childItems` ontvangt en de in-uitvoer configureert of bevat, bevat de in-uitvoer alleen bestanden in het opgegeven pad die een laatste gewijzigde tijd binnen het opgegeven bereik hebben. In worden geen items in submappen opgenomen.

## <a name="syntax"></a>Syntaxis

**Metagegevensactiviteit opdoen**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Dataset**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Eigenschappen typen

Momenteel kan de activiteit Metagegevens metagegevens metagegevens verzamelen de volgende typen metagegevens retourneren:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
veldlijst | De benodigde soorten metagegevens. Zie het gedeelte [Metagegevens](#metadata-options) van dit artikel voor meer informatie over ondersteunde metagegevens. | Ja 
Dataset | De referentiegegevensset waarvan de metagegevens moeten worden opgehaald met de activiteit Metagegevens ophalen. Zie [de](#capabilities) sectie Mogelijkheden voor informatie over ondersteunde connectors. Raadpleeg de specifieke verbindingsonderwerpen voor syntaxisdetails van de gegevensset. | Ja
opmaakInstellingen | Toepassen bij het gebruik van de gegevensset voor het opmaaktype. | Nee
storeInstellingen | Toepassen bij het gebruik van de gegevensset voor het opmaaktype. | Nee

## <a name="sample-output"></a>Voorbeelduitvoer

De resultaten met metagegevens opbrengen worden weergegeven in de activiteitsuitvoer. Hieronder volgen twee voorbeelden met uitgebreide metadata-opties. Als u de resultaten in een volgende `@{activity('MyGetMetadataActivity').output.itemName}`activiteit wilt gebruiken, gebruikt u dit patroon: .

### <a name="get-a-files-metadata"></a>De metagegevens van een bestand opvragen

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>De metagegevens van een map oppakken

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over andere controlestroomactiviteiten die worden ondersteund door Data Factory:

- [Pijplijnactiviteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Activiteit ForEach](control-flow-for-each-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
