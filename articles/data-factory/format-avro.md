---
title: De indeling Avro in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de Avro-indeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 1717969aeb24a153f986c70ef60db1aac5c840fb
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267776"
---
# <a name="avro-format-in-azure-data-factory"></a>De indeling Avro in Azure Data Factory

Volg dit artikel als u **de Avro-bestanden wilt parseren of de gegevens wilt schrijven naar de Avro-indeling**. 

De Avro-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Avro-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **Avro**. | Ja      |
| locatie         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location`. **Zie de sectie Details in connector artikel-> Eigenschappen van gegevensset**. | Ja      |
| avroCompressionCodec | De compressie-codec die moet worden gebruikt bij het schrijven naar Avro-bestanden. Bij het lezen van Avro-bestanden, Data Factory de compressie-codec automatisch bepalen op basis van de meta gegevens van het bestand.<br>Ondersteunde typen zijn '**none**' (standaard), '**Deflate**', '**Snappy**'. Houd er rekening mee dat de activiteit die momenteel wordt gekopieerd, geen ondersteuning biedt voor Snappy wanneer Avro bestanden lezen/schrijven. | Nee       |

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

### <a name="copy-activity"></a>Kopieeractiviteit
Avro [complexe gegevens typen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, matrices, kaarten, vakbonden en vast) in de Kopieer activiteit.

### <a name="data-flows"></a>Gegevensstromen
Wanneer u werkt met Avro-bestanden in gegevens stromen, kunt u complexe gegevens typen lezen en schrijven, maar u moet het fysieke schema eerst wissen uit de gegevensset. In gegevens stromen kunt u uw logische projectie instellen en kolommen afleiden die complexe structuren zijn en die velden vervolgens automatisch toewijzen aan een AVRO-bestand.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
