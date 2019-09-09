---
title: JSON-indeling in Azure Data Factory | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de JSON-indeling in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 7ff164b378ac6981fcb9686d6264b0bcf50cfafb
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814809"
---
# <a name="json-format-in-azure-data-factory"></a>JSON-indeling in Azure Data Factory

Volg dit artikel als u **de json-bestanden wilt parseren of de gegevens naar de JSON-indeling wilt schrijven**. 

De JSON-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die door de JSON-gegevensset worden ondersteund.

| Eigenschap         | Description                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| Type             | De eigenschap type van de gegevensset moet worden ingesteld op **JSON**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen `location`onder. **Zie de sectie Details in connector artikel-> Eigenschappen van gegevensset**. | Ja      |
| encodingName     | Het coderings type dat wordt gebruikt voor het lezen/schrijven van test bestanden. <br>Toegestane waarden zijn als volgt: ' UTF-8 ', ' UTF-16 ', ' UTF-16BE ', ' UTF-32 ', ' UTF-32BE ', ' US-ASCII ', ' UTF-7 ', ' BIG5 "," EUC-JP "," EUC-KR "," GB2312 "," GB18030 "," JOHAB "," SHIFT-JIS "," CP875 "," CP866 "," IBM00858 "," IBM037 "," IBM273 "," IBM437 "," IBM500 "," IBM737 "," IBM775 "," IBM850 "," IBM852 "," IBM855 "," IBM857 "," IBM860 "," IBM861 ', ' IBM863 ', ' IBM864 ', ' IBM865 ', ' IBM869 ', ' IBM870 ', ' IBM01140 ', ' IBM01141 ', ' IBM01142 ', ' IBM01143 ', ' IBM01144 ', ' ', ' ', ' ', ' IBM01145 ', ' IBM01146 ', ' IBM01147 ', ' IBM01148 ', ' IBM01149 ', ' ISO-2022-2022 "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 "," ISO-8859-15 "," WINDOWS-874 "," WINDOWS-1250 "," WINDOWS-1251 "," WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 ', ' WINDOWS-1255 ', ' WINDOWS-1256 ', ' WINDOWS-1257 ', ' WINDOWS-1258 '.| Nee       |
| compressionCodec | De compressie-codec die wordt gebruikt om tekst bestanden te lezen/schrijven. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **Snappy**of **LZ4**. te gebruiken bij het opslaan van het bestand. <br>Houd er rekening mee dat de activiteit die momenteel wordt gekopieerd, geen ondersteuning biedt voor "Snappy" & "LZ4". | Nee       |
| compressionLevel | De compressie ratio. <br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **Snelste** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: De compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie voor meer informatie, [compressieniveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) onderwerp. | Nee       |

Hieronder ziet u een voor beeld van JSON-gegevensset op Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
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
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de JSON-bron en Sink.

### <a name="json-as-source"></a>JSON als bron

De volgende eigenschappen worden ondersteund in de sectie ***\*bron\**** van de Kopieer activiteit.

| Eigenschap      | Description                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **JSONSource**. | Ja      |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees `storeSettings`instellingen onder. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

### <a name="json-as-sink"></a>JSON als Sink

De volgende eigenschappen worden ondersteund in het gedeelte ***\*Sink\**** van de Kopieer activiteit.

| Eigenschap      | Description                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| Type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **JSONSink**. | Ja      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor **JSON-schrijf instellingen** . | Nee       |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings`. **Zie de sectie Details in connector artikel-> Eigenschappen van de Kopieer activiteit**. | Nee       |

Ondersteunde **instellingen voor json-schrijf bewerkingen** onder `formatSettings`:

| Eigenschap      | Description                                                  | Vereist                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| Type          | Het type formatSettings moet zijn ingesteld op **JsonWriteSetting**. | Ja                                                   |
| filePattern |Hiermee geeft u het patroon aan van gegevens die zijn opgeslagen in elk JSON-bestand. Toegestane waarden zijn **setOfObjects** en **arrayOfObjects**. De **standaardwaarde** is **setOfObjects**. Zie het gedeelte [JSON-bestandpatronen](#json-file-patterns) voor meer informatie over deze patronen. |Nee |

### <a name="json-file-patterns"></a>JSON-bestandpatronen

Met de Kopieer activiteit kan de volgende patronen van JSON-bestanden automatisch worden gedetecteerd en geparseerd. 

- **Type I: setOfObjects**

    Elk bestand bevat één object of meerdere door regels gescheiden/samengevoegde objecten. 
    Wanneer deze optie is gekozen in Sink kopiëren-activiteit, produceert Kopieer activiteit één JSON-bestand met elk object per regel (regel scheidings teken).

    * **voorbeeld van JSON-bestand met één object**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **voorbeeld van JSON-bestand dat door regels is gescheiden**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **voorbeeld van JSON-bestand met samengevoegde objecten**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Type II: arrayOfObjects**

    Elk bestand bevat een matrix met objecten.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht kopieeractiviteit](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
