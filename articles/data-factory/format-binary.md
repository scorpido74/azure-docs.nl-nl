---
title: Binaire indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u met de binaire indeling omgaan in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416326"
---
# <a name="binary-format-in-azure-data-factory"></a>Binaire indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Binaire indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).

U binaire gegevensset gebruiken in [Activiteit kopiëren](copy-activity-overview.md), [Activiteit GetMetadata](control-flow-get-metadata-activity.md)of [Activiteit Verwijderen](delete-activity.md). Bij het gebruik van binaire gegevensset ontleden ADF de bestandsinhoud niet, maar behandelt deze niet als is. 

>[!NOTE]
>Wanneer u binaire gegevensset in kopieeractiviteit gebruikt, u alleen kopiëren van binaire gegevensset naar binaire gegevensset.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de binaire gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **Binair**. | Ja      |
| location         | Locatie-instellingen van het bestand(en). Elke op bestanden gebaseerde connector heeft zijn `location`eigen locatietype en ondersteunde eigenschappen onder . **Zie details in de sectie -> gegevensseteigenschappen**van de connector . | Ja      |
| compressie | Groep eigenschappen om bestandscompressie te configureren. Configureer deze sectie wanneer u compressie/decompressie wilt uitvoeren tijdens de uitvoering van de activiteit. | Nee |
| type | De compressiecodec die wordt gebruikt voor het lezen/schrijven van binaire bestanden. <br>Toegestane waarden zijn **bzip2,** **gzip**, **leeglopen**, **ZipDeflate**. te gebruiken bij het opslaan van het bestand.<br>Wanneer u kopieeractiviteit gebruikt om zipdeflate-bestand(en) te decomprimeren en naar het opslagarchief `<path specified in dataset>/<folder named as source zip file>/`voor bestanden te schrijven, worden bestanden naar de map geëxtraheerd: . | Nee       |
| niveau | De compressieverhouding. Toepassen wanneer de gegevensset wordt gebruikt in De activiteitssink kopiëren.<br>Toegestane waarden zijn **optimaal** of **snelst.**<br>- **Snelste:** De compressiebewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: De compressiebewerking moet optimaal worden gecomprimeerd, zelfs als de bewerking langer duurt. Zie Onderwerp [compressieniveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie. | Nee       |

Hieronder vindt u een voorbeeld van binaire gegevensset op Azure Blob Storage:

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de binaire bron en gootsteen.

>[!NOTE]
>Wanneer u binaire gegevensset in kopieeractiviteit gebruikt, u alleen kopiëren van binaire gegevensset naar binaire gegevensset.

### <a name="binary-as-source"></a>Binair als bron

De volgende eigenschappen worden ondersteund in de *** \*sectie\* *** bron van kopieeractiviteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **BinarySource**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het lezen van gegevens uit een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde leesinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

### <a name="binary-as-sink"></a>Binair als gootsteen

De volgende eigenschappen worden ondersteund in de sectie copy activity *** \*sink.\* ***

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **BinarySink**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het schrijven van gegevens naar een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde schrijfinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)
