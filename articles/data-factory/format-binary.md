---
title: Binaire indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met binaire indeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416326"
---
# <a name="binary-format-in-azure-data-factory"></a>Binaire indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Binaire indeling wordt ondersteund voor de volgende connectors [: Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

U kunt binaire gegevensset gebruiken in [Kopieer activiteit](copy-activity-overview.md), [GetMetadata-activiteit](control-flow-get-metadata-activity.md)of [Delete-activiteit](delete-activity.md). Wanneer u een binaire gegevensset gebruikt, parseert ADF geen bestands inhoud, maar behandelt deze niet. 

>[!NOTE]
>Wanneer u een binaire gegevensset in een Kopieer activiteit gebruikt, kunt u alleen kopiëren van een binaire gegevensset naar een binaire gegevensset.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de binaire gegevensset worden ondersteund.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **binary**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen `location`onder. **Zie de sectie Details in connector artikel-> eigenschappen van gegevensset**. | Ja      |
| compressie | Groep eigenschappen voor het configureren van bestands compressie. Configureer deze sectie als u compressie/decompressie wilt uitvoeren tijdens de uitvoering van de activiteit. | Nee |
| type | De compressie-codec die wordt gebruikt om binaire bestanden te lezen/schrijven. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**. te gebruiken bij het opslaan van het bestand.<br>Opmerking Wanneer u Kopieer activiteit gebruikt om ZipDeflate-bestand (en) te decomprimeren en te schrijven naar Sink-gegevens archief op basis van een bestand, worden `<path specified in dataset>/<folder named as source zip file>/`bestanden uitgepakt naar de map:. | Nee       |
| niveau | De compressie ratio. Toep assen wanneer gegevensset wordt gebruikt in Sink voor kopieer activiteit.<br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie het onderwerp [compressie niveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie. | Nee       |

Hieronder ziet u een voor beeld van een binaire gegevensset op Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de binaire bron en Sink.

>[!NOTE]
>Wanneer u een binaire gegevensset in een Kopieer activiteit gebruikt, kunt u alleen kopiëren van een binaire gegevensset naar een binaire gegevensset.

### <a name="binary-as-source"></a>Binair als bron

De volgende eigenschappen worden ondersteund in de sectie *** \*bron\* *** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **BinarySource**. | Ja      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees `storeSettings`instellingen onder. **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | Nee       |

### <a name="binary-as-sink"></a>Binair als Sink

De volgende eigenschappen worden ondersteund in het gedeelte *** \*Sink\* *** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **BinarySink**. | Ja      |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings`. **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | Nee       |

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)
