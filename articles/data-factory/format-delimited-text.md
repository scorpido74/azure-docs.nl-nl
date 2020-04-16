---
title: Afgebakende tekstnotatie in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u omgaat met afgebakende tekstindeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 696c175f55ecdb09b1b88f9766c4c678afe2c4ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417091"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Afgebakende tekstnotatie in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel wanneer u **de afgebakende tekstbestanden wilt ontlopen of de gegevens wilt schrijven in een afgebakende tekstindeling.** 

De beperkte tekstindeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset afgebakende tekst.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **DelimitedText**. | Ja      |
| location         | Locatie-instellingen van het bestand(en). Elke op bestanden gebaseerde connector heeft zijn `location`eigen locatietype en ondersteunde eigenschappen onder .  | Ja      |
| columnDelimiter  | Het teken(en) dat wordt gebruikt om kolommen in een bestand te scheiden. <br>De standaardwaarde is **komma `,` **. Wanneer de kolomscheidingwordt gedefinieerd als lege tekenreeks, wat betekent dat er geen scheidingsteken is, wordt de hele regel als één kolom genomen.<br>Momenteel wordt kolomscheiding als lege tekenreeks of multi-char alleen ondersteund voor het toewijzen van gegevensstroom, maar niet voor kopieeractiviteit.  | Nee       |
| rowDelimiter     | Het enige teken of '\r\n' dat wordt gebruikt om rijen in een bestand te scheiden. <br>De standaardwaarde is een van de volgende waarden **op gelezen waarden: ["\r\n", "\r", "\n"]** en **"\n" of "\r\n" bij schrijven** door respectievelijk gegevensstroom en Kopieeractiviteit. <br>Wanneer de scheidingsteken van de rij is ingesteld op geen scheidingsteken (lege tekenreeks), moet de kolomscheidinger ook worden ingesteld als geen scheidingsteken (lege tekenreeks), wat betekent dat de volledige inhoud als één waarde moet worden behandeld.<br>Momenteel wordt rijscheiding als lege tekenreeks alleen ondersteund voor het toewijzen van gegevensstroom, maar niet voor kopieeractiviteit. | Nee       |
| quoteChar        | Het enige teken dat kolomwaarden moet citeren als het kolomscheidingstekens bevat. <br>De standaardwaarde is **dubbele aanhalingstekens** `"`. <br>Voor het toewijzen `quoteChar` van gegevensstroom kan geen lege tekenreeks zijn. <br>Voor Kopieeractiviteit, `quoteChar` wanneer deze wordt gedefinieerd als lege tekenreeks, betekent dit dat er `escapeChar` geen koerschar is en dat kolomwaarde niet wordt geciteerd en wordt gebruikt om te ontsnappen aan de kolomscheidingsteken en zichzelf. | Nee       |
| escapeChar       | Het enige teken om aan aan citaten in een geciteerde waarde te ontsnappen.<br>De standaardwaarde is **backslash `\` **. <br>Voor het toewijzen `escapeChar` van gegevensstroom kan geen lege tekenreeks zijn. <br/>Bij Activiteit kopiëren `escapeChar` moet de `quoteChar` tekenreeks ook worden ingesteld als lege tekenreeks, in welk geval ervoor moet worden gezorgd dat alle kolomwaarden geen scheidingstekens bevatten. | Nee       |
| firstRowAsHeader | Hiermee geeft u op of u de eerste rij moet behandelen/maken als een kopregel met namen van kolommen.<br>Toegestane waarden zijn **waar** en **onwaar** (standaard). | Nee       |
| nullValue        | Hiermee geeft u de tekenreeksweergave van null-waarde op. <br>De standaardwaarde is **een lege tekenreeks**. | Nee       |
| encodingName     | Het coderingstype dat wordt gebruikt om testbestanden te lezen/schrijven. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>De gegevensstroom van notities biedt geen ondersteuning voor UTF-7-codering. | Nee       |
| compressieCodec | De compressiecodec die wordt gebruikt voor het lezen/schrijven van tekstbestanden. <br>Toegestane waarden zijn **bzip2,** **gzip,** **leeglopen,** **ZipDeflate**, **pittig**of **lz4**. Standaard wordt niet gecomprimeerd. <br>**Opmerking** op dit moment Kopieeractiviteit ondersteunt geen "pittige" & "lz4", en het toewijzen van gegevensstroom ondersteunt geen "ZipDeflate". <br>**Wanneer u** kopieeractiviteit gebruikt om zipdeflate-bestand(en) te decomprimeren en naar het opslagarchief `<path specified in dataset>/<folder named as source zip file>/`voor bestanden te schrijven, worden bestanden naar de map geëxtraheerd: . | Nee       |
| compressieNiveau | De compressieverhouding. <br>Toegestane waarden zijn **optimaal** of **snelst.**<br>- **Snelste:** De compressiebewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: De compressiebewerking moet optimaal worden gecomprimeerd, zelfs als de bewerking langer duurt. Zie Onderwerp [compressieniveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie. | Nee       |

Hieronder vindt u een voorbeeld van de beperkte tekstgegevensset op Azure Blob Storage:

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
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de afgebakende tekstbron en -sink.

### <a name="delimited-text-as-source"></a>Afgebakende tekst als bron 

De volgende eigenschappen worden ondersteund in de *** \*sectie\* *** bron van kopieeractiviteit.

| Eigenschap       | Beschrijving                                                  | Vereist |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **DelimitedTextSource**. | Ja      |
| opmaakInstellingen | Een groep eigenschappen. Raadpleeg hieronder de tabel **Met de beperkte tekstleesinstellingen.** | Nee       |
| storeInstellingen  | Een groep eigenschappen voor het lezen van gegevens uit een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde leesinstellingen onder . | Nee       |

Ondersteunde **instellingen voor het lezen van verwijderde tekst** onder `formatSettings`:

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Het type opmaakInstellingen moet worden ingesteld op **DelimitedTextReadSettings**. | Ja      |
| skipLineCount | Geeft het aantal **niet-lege** rijen aan dat moet worden overgeslagen bij het lezen van gegevens uit invoerbestanden. <br>Als zowel skipLineCount als firstRowAsHeader is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand. | Nee       |

### <a name="delimited-text-as-sink"></a>Afgebakende tekst als gootsteen

De volgende eigenschappen worden ondersteund in de sectie copy activity *** \*sink.\* ***

| Eigenschap       | Beschrijving                                                  | Vereist |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **DelimitedTextSink**. | Ja      |
| opmaakInstellingen | Een groep eigenschappen. Raadpleeg hieronder de tabel **Met de instellingen voor het schrijven van de beperkte tekst.** |          |
| storeInstellingen  | Een groep eigenschappen voor het schrijven van gegevens naar een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde schrijfinstellingen onder .  | Nee       |

Ondersteunde **instellingen voor het schrijven van tijdelijke tekst** onder `formatSettings`:

| Eigenschap      | Beschrijving                                                  | Vereist                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Het type opmaakInstellingen moet worden ingesteld op **DelimitedTextWriteSettings**. | Ja                                                   |
| bestandsextensie | De bestandsextensie die wordt gebruikt om de `.csv` `.txt`uitvoerbestanden een naam te geven, bijvoorbeeld , . Dit moet worden `fileName` opgegeven wanneer de gegevensset Uitvoer DelimitedText niet is opgegeven. Wanneer de bestandsnaam is geconfigureerd in de uitvoergegevensset, wordt deze gebruikt als de naam van het sink-bestand en wordt de bestandsextensie-instelling genegeerd.  | Ja wanneer bestandsnaam niet is opgegeven in uitvoergegevensset |

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Leer meer over [brontransformatie](data-flow-source.md) en [sinktransformatie](data-flow-sink.md) in de kaartgegevensstroom.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
