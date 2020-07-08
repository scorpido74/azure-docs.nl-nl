---
title: Excel-indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met Excel-indeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670920"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel-indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel als u **de Excel-bestanden wilt parseren**. Azure Data Factory ondersteunt zowel ". xls" als ". xlsx".

Excel-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md). Het wordt ondersteund als bron, maar niet op sink.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de Excel-gegevensset worden ondersteund.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **Excel**.   | Yes      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location` . | Yes      |
| Ingestelde SheetName        | De naam van het Excel-werk blad om gegevens te lezen.                       | Yes      |
| bereik            | Het cellen bereik in het opgegeven werk blad om de selectieve gegevens te zoeken, bijvoorbeeld `A3:H5` (een tabel van a3 naar H5), `A3` (een tabel beginnend bij cel A3), `A3:A3` (één cel). Als deze niet wordt opgegeven, wordt ADF vanuit het hele werk blad als een tabel gelezen. | No       |
| firstRowAsHeader | Hiermee wordt aangegeven of de eerste rij in het opgegeven werk blad of bereik moet worden behandeld als een header regel met de namen van kolommen.<br>Toegestane waarden zijn **True** en **False** (standaard). | No       |
| nullValue        | Hiermee wordt de teken reeks representatie van een null-waarde opgegeven. <br>De standaard waarde is een **lege teken reeks**. | No       |
| compressionCodec | De compressie-codec die wordt gebruikt om Excel-bestanden te lezen. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **Snappy**of **LZ4**. De standaard waarde is niet gecomprimeerd. <br>**Houd er rekening mee** dat het kopiëren van gegevens op dit moment geen ondersteuning biedt voor ' snappy ' & ' LZ4 ' en dat de toewijzing van data stroom niet wordt ondersteund ' ZipDeflate '. <br>**Opmerking** wanneer u Kopieer activiteit gebruikt om **ZipDeflate** -bestand (en) te decomprimeren en te schrijven naar Sink-gegevens archief op basis van een bestand, worden bestanden uitgepakt naar de map: `<path specified in dataset>/<folder named as source zip file>/` . | No       |
| compressionLevel | De compressie ratio. <br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie het onderwerp [compressie niveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie. | No       |

Hieronder ziet u een voor beeld van een Excel-gegevensset op Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door de Excel-bron worden ondersteund.

### <a name="excel-as-source"></a>Excel als bron 

De volgende eigenschappen worden ondersteund in de sectie *** \* bron \* *** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **ExcelSource**. | Yes      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings` . | Nee       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
