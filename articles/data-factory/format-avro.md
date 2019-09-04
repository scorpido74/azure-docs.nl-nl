---
title: De indeling Avro in Azure Data Factory | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de Avro-indeling in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c1f55da2f1aada8c7e4a0d979b2e38e6782e564c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294228"
---
# <a name="avro-format-in-azure-data-factory"></a>De indeling Avro in Azure Data Factory

Volg dit artikel als u **de Avro-bestanden wilt parseren of de gegevens wilt schrijven naar de Avro-indeling**. 

De Avro-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Avro-gegevensset.

| Eigenschap         | Description                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| Type             | De eigenschap type van de DataSet moet worden ingesteld op **Avro**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen `location`onder. **Zie de sectie Details in connector artikel-> Eigenschappen van gegevensset**. | Ja      |
| avroCompressionCodec | De compressie-codec die moet worden gebruikt bij het schrijven naar Avro-bestanden. Bij het lezen van Avro-bestanden, Data Factory de compressie-codec automatisch bepalen op basis van de meta gegevens van het bestand.<br>Ondersteunde typen zijn '**none**' (standaard), '**Deflate**', '**Snappy**'. | Nee       |

> [!NOTE]
> Spaties in kolom naam wordt niet ondersteund voor Avro-bestanden.

Hieronder ziet u een voor beeld van een AVRO-gegevensset op Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Avro-bron en Sink.

### <a name="avro-as-source"></a>AVRO als bron

De volgende eigenschappen worden ondersteund in de sectie ***\*bron\**** van de Kopieer activiteit.

| Eigenschap      | Description                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AvroSource**. | Ja      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees `storeSettings`instellingen onder. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

### <a name="avro-as-sink"></a>AVRO als Sink

De volgende eigenschappen worden ondersteund in het gedeelte ***\*Sink\**** van de Kopieer activiteit.

| Eigenschap      | Description                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AvroSink**. | Ja      |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings`. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

Avro [complexe gegevens typen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, matrices, kaarten, vakbonden en vast).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht kopieeractiviteit](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
