---
title: Activiteit van meta gegevens in Azure Data Factory ophalen
description: Meer informatie over het gebruik van de activiteit meta gegevens ophalen in een Data Factory-pijp lijn.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417111"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Activiteit van meta gegevens in Azure Data Factory ophalen
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U kunt de activiteit meta gegevens ophalen gebruiken om de meta gegevens van alle gegevens in de Azure Data Factory op te halen. U kunt deze activiteit gebruiken in de volgende scenario's:

- Valideer de meta gegevens van alle gegevens.
- Een pijp lijn activeren wanneer de gegevens gereed/beschikbaar zijn.

De volgende functionaliteit is beschikbaar in de controle stroom:

- U kunt de uitvoer van de activiteit meta gegevens ophalen in voorwaardelijke expressies gebruiken om validatie uit te voeren.
- U kunt een pijp lijn activeren wanneer aan een voor waarde wordt voldaan via do until-lus.

## <a name="capabilities"></a>Functionaliteit

De activiteit meta gegevens ophalen neemt een gegevensset als invoer en retourneert meta gegevens als uitvoer. Op dit moment worden de volgende connectors en de bijbehorende opgehaalde meta gegevens ondersteund. De maximale grootte van de geretourneerde meta gegevens is 2 MB.

>[!NOTE]
>Als u de activiteit meta gegevens ophalen uitvoert op een zelf-hostende Integration runtime, worden de meest recente mogelijkheden ondersteund in versie 3,6 of hoger.

### <a name="supported-connectors"></a>Ondersteunde connectors

**Bestands opslag**

| Connector/meta gegevens | itemName<br>(bestand/map) | Item type<br>(bestand/map) | grootte<br>Profiler | toegevoegd<br>(bestand/map) | lastModified<br>(bestand/map) |childItems<br>map |contentMD5<br>Profiler | structuur<br/>Profiler | Aantal<br>Profiler | reeds<br>(bestand/map) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Bestands systeem](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Wanneer u activiteit voor het ophalen van meta gegevens voor een map gebruikt, moet u ervoor zorgen dat u beschikt over de machtiging lijst/uitvoeren voor de opgegeven map.
- Voor Amazon S3 en Google Cloud Storage `lastModified` is van toepassing op de Bucket en de sleutel, maar niet op de virtuele `exists` map, en is van toepassing op de Bucket en de sleutel, maar niet op het voor voegsel of de virtuele map.
- Voor Azure Blob Storage geldt `lastModified` voor de container en de blob, maar niet voor de virtuele map.
- `lastModified`filter is momenteel van toepassing op het filteren van onderliggende items, maar niet voor de opgegeven map/bestand zelf.
- Het Joker teken filter voor mappen/bestanden wordt niet ondersteund voor de activiteit meta gegevens ophalen.

**Relationele database**

| Connector/meta gegevens | structuur | Aantal | reeds |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Beheerde instantie Azure SQL Database](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opties voor meta gegevens

U kunt de volgende typen meta gegevens opgeven in de velden lijst activiteit meta gegevens ophalen om de bijbehorende gegevens op te halen:

| Meta gegevens type | Beschrijving |
|:--- |:--- |
| itemName | De naam van het bestand of de map. |
| Item type | Het type van het bestand of de map. Geretourneerde waarde `File` is `Folder`of. |
| grootte | Grootte van het bestand in bytes. Alleen van toepassing op bestanden. |
| toegevoegd | Er is een datum/tijd gemaakt van het bestand of de map. |
| lastModified | Datum/tijd waarop het bestand of de map voor het laatst is gewijzigd. |
| childItems | Lijst met submappen en bestanden in de opgegeven map. Alleen van toepassing op mappen. Geretourneerde waarde is een lijst met de naam en het type van elk onderliggend item. |
| contentMD5 | MD5 van het bestand. Alleen van toepassing op bestanden. |
| structuur | De gegevens structuur van het bestand of de relationele database tabel. Geretourneerde waarde is een lijst met kolom namen en kolom typen. |
| Aantal | Het aantal kolommen in het bestand of de tabel relationeel. |
| reeds| Hiermee wordt aangegeven of een bestand, map of tabel bestaat. Houd er rekening `exists` mee dat als is opgegeven in de lijst met velden voor meta gegevens ophalen, de activiteit niet kan worden uitgevoerd, zelfs niet als het bestand, de map of de tabel niet bestaat. In plaats `exists: false` daarvan wordt geretourneerd in de uitvoer. |

>[!TIP]
>Als u wilt controleren of een bestand, map of tabel bestaat, geeft `exists` u in de velden lijst activiteit van meta gegevens ophalen op. U kunt vervolgens het `exists: true/false` resultaat controleren in de uitvoer van de activiteit. Als `exists` niet wordt opgegeven in de lijst met velden, mislukt de activiteit meta gegevens ophalen als het object niet is gevonden.

>[!NOTE]
>Wanneer u meta gegevens ophaalt uit het `modifiedDatetimeStart` bestand `modifiedDatetimeEnd`archieven en `childItems` configureren of, bevat de uitvoer alleen bestanden in het opgegeven pad met een tijd die het laatst is gewijzigd binnen het opgegeven bereik. In zijn geen items in submappen inbegrepen.

## <a name="syntax"></a>Syntaxis

**Activiteit meta gegevens ophalen**

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

**Sets**

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

## <a name="type-properties"></a>Type-eigenschappen

Op dit moment kunnen met de activiteit meta gegevens ophalen de volgende typen meta gegevens worden geretourneerd:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
Velden | De typen meta gegevens die zijn vereist. Zie de sectie [meta gegevens opties](#metadata-options) in dit artikel voor meer informatie over ondersteunde meta gegevens. | Ja 
sets | De referentie gegevensset waarvan de meta gegevens moeten worden opgehaald door de activiteit meta gegevens ophalen. Zie de sectie [mogelijkheden](#capabilities) voor informatie over ondersteunde connectors. Raadpleeg de onderwerpen over de specifieke connector voor de syntaxis van de gegevensset. | Ja
formatSettings | Toep assen bij gebruik van gegevensset voor indelings type. | Nee
storeSettings | Toep assen bij gebruik van gegevensset voor indelings type. | Nee

## <a name="sample-output"></a>Voorbeelduitvoer

De resultaten van de meta gegevens ophalen worden weer gegeven in de uitvoer van de activiteit. Hieronder vindt u twee voor beelden van uitgebreide opties voor meta gegevens. Gebruik dit patroon om de resultaten in een volgende activiteit te gebruiken: `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Meta gegevens van een bestand ophalen

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

### <a name="get-a-folders-metadata"></a>Meta gegevens van een map ophalen

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
Meer informatie over andere controle stroom activiteiten die door Data Factory worden ondersteund:

- [Pijplijn activiteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Activiteit ForEach](control-flow-for-each-activity.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
