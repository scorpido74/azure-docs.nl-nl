---
title: De indeling Parquet in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de Parquet-indeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 340f91fc926c155f95449f7cc49c214f46d1ff35
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423654"
---
# <a name="parquet-format-in-azure-data-factory"></a>De indeling Parquet in Azure Data Factory

Volg dit artikel als u **de Parquet-bestanden wilt parseren of de gegevens wilt schrijven naar de Parquet-indeling**. 

De Parquet-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Parquet-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **Parquet**. | Ja      |
| locatie         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location`. **Zie de sectie Details in connector artikel-> Eigenschappen van gegevensset**. | Ja      |
| compressionCodec | De compressie-codec die moet worden gebruikt bij het schrijven naar Parquet-bestanden. Bij het lezen van Parquet-bestanden, Data Factory de compressie-codec automatisch bepalen op basis van de meta gegevens van het bestand.<br>Ondersteunde typen zijn '**none**', '**gzip**', '**Snappy**' (standaard) en '**lzo**'. Houd er rekening mee dat de activiteit die momenteel wordt gekopieerd, geen ondersteuning biedt voor LZO wanneer Parquet bestanden lezen/schrijven. | Nee       |

> [!NOTE]
> Spaties in kolom naam wordt niet ondersteund voor Parquet-bestanden.

Hieronder ziet u een voor beeld van een Parquet-gegevensset op Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Parquet-bron en Sink.

### <a name="parquet-as-source"></a>Parquet als bron

De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit ***\*bron\**** .

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **ParquetSource**. | Ja      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings`. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

### <a name="parquet-as-sink"></a>Parquet als Sink

De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit ***\*sink\**** .

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **ParquetSink**. | Ja      |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings`. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over de [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) in de toewijzing van gegevens stroom.

## <a name="data-type-support"></a>Ondersteuning voor gegevens typen

Parquet complexe gegevens typen worden momenteel niet ondersteund (bijvoorbeeld kaart, lijst, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime gebruiken

> [!IMPORTANT]
> Als u de kopie wilt voorzien van zelf-hostende Integration Runtime bijvoorbeeld tussen on-premises en gegevens opslag in de Cloud, moet u de **64-bits jre 8 (Java Runtime Environment) of openjdk** op uw IR-computer **installeren, als**u geen Parquet-bestanden kopieert. Raadpleeg de volgende alinea met meer informatie.

Voor kopieën die worden uitgevoerd op zelf-hostende IR met Parquet-serialisatie/deserialisatie, zoekt ADF de Java-runtime door de register *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* voor jre te controleren, indien niet gevonden, ter controle van de systeem variabele *`JAVA_HOME`* voor openjdk.

- **Jre gebruiken**: de 64-bits IR vereist een 64-bits jre. U kunt deze [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Als u openjdk wilt gebruiken**, wordt dit ondersteund sinds IR-versie 3,13. Verdeel het JVM. dll-bestand met alle andere vereiste assembly's van OpenJDK in een zelf-hostende IR-computer en stel de systeem omgevings variabele in JAVA_HOME dienovereenkomstig in.

> [!TIP]
> Als u gegevens kopieert naar/van Parquet-indeling met behulp van zelf-hostende Integration Runtime en de fout melding ' er is een fout opgetreden bij het aanroepen van Java, bericht: **Java. lang. OutOfMemoryError: Java-heapruimte**', kunt u een omgevings variabele `_JAVA_OPTIONS` toevoegen aan de computer die als host fungeert voor de zelf-hostende IR om een dergelijke kopie te maken,

![JVM-Heap-grootte instellen op zelf-hostende IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Voor beeld: Stel variabele `_JAVA_OPTIONS` in met de waarde `-Xms256m -Xmx16g`. Met de vlag `Xms` wordt de eerste geheugen toewijzings groep opgegeven voor een Java Virtual Machine (JVM), terwijl `Xmx` de maximale geheugen toewijzings groep opgeeft. Dit betekent dat JVM wordt gestart met `Xms` hoeveelheid geheugen en dat er Maxi maal `Xmx` hoeveelheid geheugen kan worden gebruikt. ADF gebruikt standaard min 64 MB en Max 1G.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
