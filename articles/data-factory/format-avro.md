---
title: Avro-indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u met de Avro-indeling omgaan in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 1717969aeb24a153f986c70ef60db1aac5c840fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267776"
---
# <a name="avro-format-in-azure-data-factory"></a>Avro-indeling in Azure Data Factory

Volg dit artikel wanneer u **de Avro-bestanden wilt ontlopen of de gegevens in Avro-formaat**wilt schrijven. 

Het Avro-formaat wordt ondersteund voor de volgende connectors: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Avro-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **Avro**. | Ja      |
| location         | Locatie-instellingen van het bestand(en). Elke op bestanden gebaseerde connector heeft zijn `location`eigen locatietype en ondersteunde eigenschappen onder . **Zie details in de sectie -> gegevensseteigenschappen**van de connector . | Ja      |
| avroCompressionCodec | De compressie codec te gebruiken bij het schrijven naar Avro bestanden. Bij het lezen van Avro-bestanden bepaalt Data Factory automatisch de compressiecodec op basis van de bestandsmetagegevens.<br>Ondersteunde types zijn "**none**" (default), "**deflate**", "**snappy**". Opmerking op dit moment Kopieeractiviteit biedt geen ondersteuning voor Snappy bij het lezen/schrijven van Avro-bestanden. | Nee       |

> [!NOTE]
> Witte ruimte in kolomnaam wordt niet ondersteund voor Avro-bestanden.

Hieronder vindt u een voorbeeld van de Avro-gegevensset over Azure Blob Storage:

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst van eigenschappen ondersteund door de Avro bron en gootsteen.

### <a name="avro-as-source"></a>Avro als bron

De volgende eigenschappen worden ondersteund in de *** \*sectie\* *** bron van kopieeractiviteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **AvroSource**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het lezen van gegevens uit een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde leesinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

### <a name="avro-as-sink"></a>Avro als gootsteen

De volgende eigenschappen worden ondersteund in de sectie copy activity *** \*sink.\* ***

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **AvroSink**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het schrijven van gegevens naar een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde schrijfinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

## <a name="data-type-support"></a>Ondersteuning voor gegevenstype

### <a name="copy-activity"></a>Kopieeractiviteit
De [complexe gegevenstypen van](https://avro.apache.org/docs/current/spec.html#schema_complex) Avro worden niet ondersteund (records, enums, arrays, kaarten, vakbonden en vaste) in Kopieeractiviteit.

### <a name="data-flows"></a>Gegevensstromen
Wanneer u met Avro-bestanden in gegevensstromen werkt, u complexe gegevenstypen lezen en schrijven, maar zorg ervoor dat u eerst het fysieke schema uit de gegevensset wist. In gegevensstromen u uw logische projectie instellen en kolommen afleiden die complexe structuren zijn, en deze velden vervolgens automatisch toewijzen aan een Avro-bestand.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
