---
title: ORC-indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u met orc-indeling omgaan in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597487"
---
# <a name="orc-format-in-azure-data-factory"></a>ORC-indeling in Azure Data Factory

Volg dit artikel wanneer u **de ORC-bestanden wilt ontlopen of de gegevens in ORC-indeling**wilt schrijven. 

ORC-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de ORC-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **Orc**. | Ja      |
| location         | Locatie-instellingen van het bestand(en). Elke op bestanden gebaseerde connector heeft zijn `location`eigen locatietype en ondersteunde eigenschappen onder . **Zie details in de sectie -> gegevensseteigenschappen**van de connector . | Ja      |

Hieronder vindt u een voorbeeld van orc-gegevensset over Azure Blob Storage:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Houd rekening met de volgende punten:

* Complexe gegevenstypen worden niet ondersteund (STRUCT, MAP, LIST, UNION).
* Witte ruimte in kolomnaam wordt niet ondersteund.
* Een ORC-bestand heeft drie [opties voor compressie](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory ondersteunt het lezen van gegevens uit ORC-bestanden in een van deze gecomprimeerde indelingen. Hierbij wordt de compressiecodec in de metagegevens gebruikt om de gegevens te lezen. Bij het schrijven naar een ORC-bestand kiest Data Factory echter ZLIB, de standaardinstelling voor ORC. Er is momenteel geen optie om dit gedrag te overschrijven.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de ORC-bron en -gootsteen.

### <a name="orc-as-source"></a>ORC als bron

De volgende eigenschappen worden ondersteund in de *** \*sectie\* *** bron van kopieeractiviteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **OrcSource**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het lezen van gegevens uit een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde leesinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

### <a name="orc-as-sink"></a>ORC als gootsteen

De volgende eigenschappen worden ondersteund in de sectie copy activity *** \*sink.\* ***

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **OrcSink**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het schrijven van gegevens naar een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde schrijfinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

## <a name="using-self-hosted-integration-runtime"></a>Runtime voor zelfgehoste integratie gebruiken

> [!IMPORTANT]
> Voor kopieën die worden ondersteund door Self-hosted Integration Runtime, bijvoorbeeld tussen on-premises en clouddatastores, moet u, als u ORC-bestanden niet **kopieert zoals deze is,** het **64-bits JRE 8 (Java Runtime Environment) of OpenJDK** en **Microsoft Visual C++ 2010 Redistributable Package** op uw IR-machine installeren. Controleer de volgende alinea met meer details.

Voor copy running op Self-hosted IR met ORC-bestandsserialisatie/deserialisatie, lokaliseert ADF de Java-runtime door eerst het register *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* te controleren op JRE, als deze niet is gevonden, ten tweede systeemvariabele *`JAVA_HOME`* voor OpenJDK te controleren.

- **Om JRE te gebruiken:** De 64-bits IR vereist 64-bits JRE. U het vanaf [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Om OpenJDK te gebruiken**: Het wordt ondersteund sinds IR-versie 3.13. Verpak de jvm.dll met alle andere vereiste samenstellingen van OpenJDK in Self-hosted IR machine, en stel systeemomgevingvariabele JAVA_HOME dienovereenkomstig in.
- **Voor het installeren van Visual C++ 2010 Redistributable Package:** Visual C++ 2010 Redistributable Package is niet geïnstalleerd met zelf gehoste IR-installaties. U het vanaf [hier](https://www.microsoft.com/download/details.aspx?id=14632)vinden.

> [!TIP]
> Als u gegevens kopieert naar/van ORC-indeling met behulp van Self-hosted Integration Runtime en hiterror met de tekst Er is een `_JAVA_OPTIONS` fout opgetreden bij het aanroepen van java, bericht: **java.lang.OutOfMemoryError:Java heap space**, u een omgevingsvariabele toevoegen in de machine die de zelfgehoste IR host om de min/max heapgrootte voor JVM aan te passen om een dergelijke kopie te machtigen en vervolgens de pijplijn opnieuw te gebruiken.

![Stel de grootte van de JVM-heap in op zelfgehoste IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Voorbeeld: variabele `_JAVA_OPTIONS` instellen `-Xms256m -Xmx16g`met waarde . De `Xms` vlag geeft de initiële groep geheugentoewijzing voor `Xmx` een Java Virtual Machine (JVM) op, terwijl de maximale geheugentoewijzingsgroep wordt opgegeven. Dit betekent dat JVM `Xms` zal worden gestart met de hoeveelheid `Xmx` geheugen en in staat zal zijn om een maximale hoeveelheid geheugen te gebruiken. AdF gebruikt standaard min 64 MB en max 1G.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
