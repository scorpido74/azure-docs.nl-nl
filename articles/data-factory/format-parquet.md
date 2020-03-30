---
title: Parketindeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u met de parketindeling omgaan in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597623"
---
# <a name="parquet-format-in-azure-data-factory"></a>Parketindeling in Azure Data Factory

Volg dit artikel wanneer u de parketbestanden wilt **ontken of de gegevens in parketformaat**wilt schrijven. 

Parket-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Parket.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld **op Parket**. | Ja      |
| location         | Locatie-instellingen van het bestand(en). Elke op bestanden gebaseerde connector heeft zijn `location`eigen locatietype en ondersteunde eigenschappen onder . **Zie details in de sectie -> gegevensseteigenschappen**van de connector . | Ja      |
| compressieCodec | De compressie codec te gebruiken bij het schrijven naar parketbestanden. Bij het lezen van parketbestanden bepalen Data Factories automatisch de compressiecodec op basis van de bestandsmetagegevens.<br>Ondersteunde types zijn "**none**", "**gzip**", "**snappy**" (standaard), en "**lzo**". Opmerking momenteel Kopieeractiviteit biedt geen ondersteuning voor LZO bij het lezen/schrijven van parketbestanden. | Nee       |

> [!NOTE]
> Witte ruimte in kolomnaam wordt niet ondersteund voor parketbestanden.

Hieronder vindt u een voorbeeld van de gegevensset Parket over Azure Blob Storage:

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst van eigenschappen die worden ondersteund door de parketbron en gootsteen.

### <a name="parquet-as-source"></a>Parket als bron

De volgende eigenschappen worden ondersteund in de *** \*sectie\* *** bron van kopieeractiviteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld **op ParquetSource**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het lezen van gegevens uit een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde leesinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

### <a name="parquet-as-sink"></a>Parket als gootsteen

De volgende eigenschappen worden ondersteund in de sectie copy activity *** \*sink.\* ***

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **ParquetSink**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het schrijven van gegevens naar een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde schrijfinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Leer meer over [brontransformatie](data-flow-source.md) en [sinktransformatie](data-flow-sink.md) in de kaartgegevensstroom.

## <a name="data-type-support"></a>Ondersteuning voor gegevenstype

Parketcomplexe gegevenstypen worden momenteel niet ondersteund (bijvoorbeeld MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Runtime voor zelfgehoste integratie gebruiken

> [!IMPORTANT]
> Voor copy empowered van Self-hosted Integration Runtime bijvoorbeeld tussen on-premises en cloud data stores, als u geen Parketbestanden kopieert **zoals het is,** moet u het **64-bits JRE 8 (Java Runtime Environment) of OpenJDK** en **Microsoft Visual C++ 2010 Redistributable Package** op uw IR-machine installeren. Controleer de volgende alinea met meer details.

Voor copy running op Self-hosted IR met Parquet file serialization/deserialization, adf lokaliseert de Java runtime door eerst het register *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* te controleren op JRE, indien niet gevonden, ten tweede het controleren van systeem variabele *`JAVA_HOME`* voor OpenJDK.

- **Om JRE te gebruiken:** De 64-bits IR vereist 64-bits JRE. U het vanaf [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Om OpenJDK te gebruiken**: Het wordt ondersteund sinds IR-versie 3.13. Verpak de jvm.dll met alle andere vereiste samenstellingen van OpenJDK in Self-hosted IR machine, en stel systeemomgevingvariabele JAVA_HOME dienovereenkomstig in.
- **Voor het installeren van Visual C++ 2010 Redistributable Package:** Visual C++ 2010 Redistributable Package is niet geïnstalleerd met zelf gehoste IR-installaties. U het vanaf [hier](https://www.microsoft.com/download/details.aspx?id=14632)vinden.

> [!TIP]
> Als u gegevens kopieert naar/van het Parket-formaat met behulp van Self-hosted Integration Runtime en hiterror met de tekst Er is een `_JAVA_OPTIONS` fout opgetreden bij het aanroepen van java, bericht: **java.lang.OutOfMemoryError:Java heap space**, u een omgevingsvariabele toevoegen in de machine die de zelfgehoste IR host om de min/max heapgrootte voor JVM aan te passen om een dergelijke kopie te machtigen en vervolgens de pijplijn opnieuw te gebruiken.

![Stel de grootte van de JVM-heap in op zelfgehoste IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Voorbeeld: variabele `_JAVA_OPTIONS` instellen `-Xms256m -Xmx16g`met waarde . De `Xms` vlag geeft de initiële groep geheugentoewijzing voor `Xmx` een Java Virtual Machine (JVM) op, terwijl de maximale geheugentoewijzingsgroep wordt opgegeven. Dit betekent dat JVM `Xms` zal worden gestart met de hoeveelheid `Xmx` geheugen en in staat zal zijn om een maximale hoeveelheid geheugen te gebruiken. AdF gebruikt standaard min 64 MB en max 1G.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
