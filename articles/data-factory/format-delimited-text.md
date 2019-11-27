---
title: Tekst indeling met scheidings tekens in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met gescheiden tekst indeling in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 9a1a019e7dea2f0c71b40530baaffff79d003774
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548289"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Tekst indeling met scheidings tekens in Azure Data Factory

Volg dit artikel als u **de gescheiden tekst bestanden wilt parseren of de gegevens naar tekst indeling met scheidings tekens wilt schrijven**. 

Tekst met scheidings tekens wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset met scheidings tekens.

| Eigenschap         | Beschrijving                                                  | Verplicht |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de DataSet moet worden ingesteld op **DelimitedText**. | Ja      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location`.  | Ja      |
| columnDelimiter  | De teken (s) die worden gebruikt voor het scheiden van kolommen in een bestand. Op dit moment wordt het scheidings teken voor meerdere tekens alleen ondersteund voor de toewijzing van gegevens stroom, maar niet voor het kopiëren van de activiteit. <br>De standaard waarde is een **komma `,`** , wanneer het kolom scheidings teken is gedefinieerd als lege reeks, wat geen scheidings teken is, wordt de hele regel als één kolom beschouwd. | Nee       |
| rowDelimiter     | Het afzonderlijke teken of ' \r\n ' die wordt gebruikt om rijen in een bestand te scheiden.<br>De standaard waarde is een van de volgende waarden **voor lezen: ["\r\n", "\r", "\n"]** en **"\n" of "\r\n" bij** het toewijzen van gegevens stroom en de Kopieer activiteit respectievelijk. <br>Als `rowDelimiter` is ingesteld op geen (een lege teken reeks), moet de `columnDelimiter` worden ingesteld als geen scheidings teken (lege string). Dit betekent dat u de gehele inhoud als één waarde kunt behandelen. | Nee       |
| quoteChar        | De kolom waarden voor één teken om te citeren als deze kolom scheidings tekens bevat. <br>De standaard waarde is **dubbele aanhalings tekens** `"`. <br>`quoteChar` kan geen lege teken reeks zijn voor het toewijzen van gegevens stroom. <br>Als `quoteChar` als lege teken reeks is gedefinieerd, betekent dit dat er geen aanhalings tekens en kolom waarden worden vermeld, en `escapeChar` wordt gebruikt om het kolom scheidings teken en zelf te escapeel. | Nee       |
| escapeChar       | Het enkele teken om aanhalings tekens binnen een waarde in de offerte op te lossen.<br>De standaard waarde is **back slash `\`** . <br>`escapeChar` kan geen lege teken reeks zijn voor het toewijzen van gegevens stroom. <br/>Als `escapeChar` als lege teken reeks is gedefinieerd, moet de `quoteChar` ook als lege teken reeks worden ingesteld, in dat geval dat alle kolom waarden geen scheidings tekens bevatten. | Nee       |
| firstRowAsHeader | Hiermee wordt aangegeven of de eerste rij moet worden behandeld/gemaakt als een kopregel met namen van kolommen.<br>Toegestane waarden zijn **True** en **False** (standaard). | Nee       |
| nullValue        | Hiermee wordt de teken reeks representatie van een null-waarde opgegeven. <br>De standaard waarde is een **lege teken reeks**. | Nee       |
| encodingName     | Het coderings type dat wordt gebruikt voor het lezen/schrijven van test bestanden. <br>Toegestane waarden zijn als volgt: ' UTF-8 ', ' UTF-16 ', ' UTF-16BE ', ' UTF-32 ', ' UTF-32BE ', ' US-ASCII "," UTF-7 "," BIG5 "," EUC-JP "," EUC-KR "," GB2312 "," GB18030 "," JOHAB "," SHIFT-JIS "," CP875 "," CP866 "," IBM00858 "," IBM037 "," IBM273 "," IBM437 "," IBM500 "," IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 ', ' WINDOWS-1253 ', ' WINDOWS-1254 ', ' WINDOWS-1255 ', ' WINDOWS-1256 ', ' WINDOWS-1257 ', ' WINDOWS-1258 '.<br>Opmerking toewijzing gegevens stroom ondersteunt geen UTF-7-code ring. | Nee       |
| compressionCodec | De compressie-codec die wordt gebruikt om tekst bestanden te lezen/schrijven. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **Snappy**of **LZ4**. te gebruiken bij het opslaan van het bestand. <br>Houd er rekening mee dat het kopiëren van gegevens op dit moment geen ondersteuning biedt voor ' Snappy ' & ' LZ4 ' en dat de toewijzing van data stroom niet wordt ondersteund ' ZipDeflate '. <br>Opmerking Wanneer u Kopieer activiteit gebruikt om ZipDeflate-bestand (en) te decomprimeren en te schrijven naar op bestanden gebaseerde Sink-gegevens opslag, worden bestanden uitgepakt naar de map: `<path specified in dataset>/<folder named as source zip file>/`. | Nee       |
| compressionLevel | De compressie ratio. <br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **snelst:** de compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie het onderwerp [compressie niveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie. | Nee       |

Hieronder ziet u een voor beeld van een gegevensset met scheidings tekens op Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gescheiden tekst bron en Sink.

### <a name="delimited-text-as-source"></a>Tekst met scheidings tekens als bron 

De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit ***\*bron\**** .

| Eigenschap       | Beschrijving                                                  | Verplicht |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **DelimitedTextSource**. | Ja      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor **Lees instellingen voor tekst met scheidings tekens** . | Nee       |
| storeSettings  | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings`. | Nee       |

Ondersteunde **instellingen voor het lezen van tekst met scheidings tekens** onder `formatSettings`:

| Eigenschap      | Beschrijving                                                  | Verplicht |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Het type formatSettings moet zijn ingesteld op **DelimitedTextReadSetting**. | Ja      |
| skipLineCount | Hiermee wordt het aantal **niet-lege** rijen aangegeven dat moet worden overgeslagen bij het lezen van gegevens van invoer bestanden. <br>Als zowel skipLineCount als firstRowAsHeader is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand. | Nee       |

### <a name="delimited-text-as-sink"></a>Tekst met scheidings tekens als Sink

De volgende eigenschappen worden ondersteund in de sectie Kopieer activiteit ***\*sink\**** .

| Eigenschap       | Beschrijving                                                  | Verplicht |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **DelimitedTextSink**. | Ja      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor het **schrijven van instellingen voor tekst met scheidings tekens** hieronder. |          |
| storeSettings  | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings`.  | Nee       |

Ondersteunde **instellingen voor het schrijven van tekst met scheidings tekens** onder `formatSettings`:

| Eigenschap      | Beschrijving                                                  | Verplicht                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Het type formatSettings moet zijn ingesteld op **DelimitedTextWriteSetting**. | Ja                                                   |
| File Extension | De bestands extensie die wordt gebruikt voor het benoemen van de uitvoer bestanden, bijvoorbeeld `.csv`, `.txt`. De waarde moet worden opgegeven als de `fileName` niet is opgegeven in de DelimitedText-gegevensset. | Ja als er geen bestands naam is opgegeven in de uitvoer gegevensset |

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over de [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) in de toewijzing van gegevens stroom.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
