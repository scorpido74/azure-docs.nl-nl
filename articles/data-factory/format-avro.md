---
title: De indeling Avro in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de Avro-indeling in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: a40e1b717a2ac791cdf7583afb9a7649a5b00af1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674896"
---
# <a name="avro-format-in-azure-data-factory"></a>De indeling Avro in Azure Data Factory

Volg dit artikel als u **de Avro-bestanden wilt parseren of de gegevens wilt schrijven naar de Avro-indeling**. 

De Avro-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Avro-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **Avro**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location`. **Zie de sectie Details in connector artikel-> Eigenschappen van gegevensset**. | Ja      |
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

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Avro-bron en Sink.

### <a name="avro-as-source"></a>AVRO als bron

De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit ***\*bron\**** .

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AvroSource**. | Ja      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings`. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

### <a name="avro-as-sink"></a>AVRO als Sink

De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit ***\*sink\**** .

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **AvroSink**. | Ja      |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings`. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

Avro [complexe gegevens typen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, matrices, kaarten, vakbonden en vast).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
