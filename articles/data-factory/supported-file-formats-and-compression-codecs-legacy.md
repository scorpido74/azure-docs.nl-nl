---
title: Ondersteunde bestandsindelingen in Azure Data Factory (legacy)
description: In dit onderwerp worden de bestandsindelingen en compressiecodes beschreven die worden ondersteund door op bestanden gebaseerde connectors in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: b1f11a1ff25117c07e61475e7e83fc0c170cd552
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414656"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Ondersteunde bestandsindelingen en compressiecodecs in Azure Data Factory (legacy)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Dit artikel is van toepassing op de volgende connectors: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory introduceerde een nieuw op formaat gebaseerd gegevenssetmodel, zie overeenkomstig formaatartikel met details: <br>- [Avro-formaat](format-avro.md)<br>- [Binaire indeling](format-binary.md)<br>- [Afgebakende tekstnotatie](format-delimited-text.md)<br>- [JSON-indeling](format-json.md)<br>- [ORC-indeling](format-orc.md)<br>- [Parketformaat](format-parquet.md)<br>De rest configuraties vermeld in dit artikel worden nog steeds ondersteund as-is voor achterwaartse compabitility. U wordt voorgesteld om het nieuwe model in de toekomst te gebruiken. 

## <a name="text-format-legacy"></a><a name="text-format"></a>Tekstnotatie (legacy)

>[!NOTE]
>Meer informatie over het nieuwe model uit [artikel Delimited text format.](format-delimited-text.md) De volgende configuraties op bestandsgebaseerde gegevensarchiefgegevensset worden nog steeds ondersteund als dit is voor achterwaartse compabitility. U wordt voorgesteld om het nieuwe model in de toekomst te gebruiken.

Als u wilt lezen uit een tekstbestand of naar `type` een `format` tekstbestand wilt schrijven, stelt u de eigenschap in de sectie van de gegevensset in op **TextFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Raadpleeg het gedeelte [TextFormat-voorbeeld](#textformat-example) voor configuratie-instructies.

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| columnDelimiter |Het teken dat wordt gebruikt voor het scheiden van kolommen in een bestand. U overwegen om een zeldzaam onafdrukbaar teken te gebruiken dat mogelijk niet in uw gegevens voorkomt. Geef bijvoorbeeld '\u0001' op, wat het begin van kop (SOH) vertegenwoordigt. |Er is slechts één teken toegestaan. De **standaardwaarde** is een **komma (',')**. <br/><br/>Als u een Unicode-teken wilt gebruiken, raadpleegt u [Unicode-tekens](https://en.wikipedia.org/wiki/List_of_Unicode_characters) om de bijbehorende code ervoor te krijgen. |Nee |
| rowDelimiter |Het teken dat wordt gebruikt voor het scheiden van rijen in een bestand. |Er is slechts één teken toegestaan. De **standaardwaarde** is een van de volgende leeswaarden **['\r\n', '\r', '\n']** en de schrijfwaarde **'\r\n'**. |Nee |
| escapeChar |Dit speciale teken wordt gebruikt om een scheidingsteken voor kolommen van de inhoud van het invoerbestand om te zetten. <br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voorbeeld: als u komma (',') hebt als de kolomscheidingsteken, maar u wilt het kommateken in de tekst hebben (bijvoorbeeld 'Hallo, wereld'), u '$' definiëren als het escape-teken en de tekenreeks 'Hallo$, wereld' in de bron gebruiken. |Nee |
| quoteChar |Het teken dat wordt gebruikt om een tekenreekswaarde te citeren. De scheidingstekens voor kolommen en rijen binnen de aanhalingstekens worden beschouwd als onderdeel van de tekenreekswaarde. Deze eigenschap is van toepassing op gegevenssets voor invoer en uitvoer.<br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voorbeeld: als u kolommen scheidt met komma's (', '), maar u het kommateken in een tekst wilt gebruiken (voorbeeld: <Hallo, wereld>), kunt u " (dubbel aanhalingsteken) als het aanhalingsteken opgeven en de tekenreeks "Hallo, wereld" in de bron gebruiken. |Nee |
| nullValue |Een of meer tekens die worden gebruikt om een null-waarde te vertegenwoordigen. |Een of meer tekens. De **standaardwaarden** zijn **'\N' en 'NULL'** voor lezen en **'\N'** voor schrijven. |Nee |
| encodingName |Geef de coderingsnaam op. |Een geldige coderingsnaam. Zie [De eigenschap Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Voorbeeld: windows 1250 of shift_jis. De **standaardwaarde** is **UTF-8**. |Nee |
| firstRowAsHeader |Hiermee geeft u op of de eerste rij als een header moet worden gezien. Bij een gegevensset voor invoer leest Data Factory de eerste rij als een header. Bij een gegevensset voor uitvoer schrijft Data Factory de eerste rij als een header. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |True<br/><b>False (standaard)</b> |Nee |
| skipLineCount |Geeft het aantal **niet-lege** rijen aan dat moet worden overgeslagen bij het lezen van gegevens uit invoerbestanden. Als zowel skipLineCount als firstRowAsHeader is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |Geheel getal |Nee |
| treatEmptyAsNull |Hiermee geeft u aan of null of lege tekenreeks moeten worden behandeld als een null-waarde bij het lezen van gegevens uit een invoerbestand. |**True (standaard)**<br/>False |Nee |

### <a name="textformat-example"></a>Voorbeeld van TextFormat

In de volgende JSON-definitie voor een gegevensset worden enkele optionele eigenschappen opgegeven.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Gebruik een `escapeChar` in plaats van `quoteChar`, vervang de regel door `quoteChar` met het volgende escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenario's voor het gebruik van firstRowAsHeader en skipLineCount

* U kopieert vanuit een bron die geen bestand is, naar een tekstbestand en wilt een headerregel toevoegen die de metagegevens van het schema bevat (bijvoorbeeld: SQL-schema). Geef voor `firstRowAsHeader` 'True' op in de uitvoergegevensset voor dit scenario.
* U wilt kopiëren vanuit een tekstbestand met een headerregel naar een sink die geen bestand is en wilt die regel verwijderen. Geef voor `firstRowAsHeader` 'True' op in de invoergegevensset.
* U wilt kopiëren uit een tekstbestand en wilt een paar regels aan het begin overslaan die geen gegevens of headerinformatie bevatten. Geef `skipLineCount` op om aan te geven hoeveel regels er moeten worden overgeslagen. Als de rest van het bestand een headerregel bevat, kunt u ook `firstRowAsHeader` opgeven. Als zowel `skipLineCount` als `firstRowAsHeader` is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand

## <a name="json-format-legacy"></a><a name="json-format"></a>JSON-indeling (legacy)

>[!NOTE]
>Meer informatie over het nieuwe model uit [json-formaat](format-json.md) artikel. De volgende configuraties op bestandsgebaseerde gegevensarchiefgegevensset worden nog steeds ondersteund als dit is voor achterwaartse compabitility. U wordt voorgesteld om het nieuwe model in de toekomst te gebruiken.

Zie Json-documenten sectie importeren/exporteren in het artikel [Gegevens verplaatsen naar/van Azure Cosmos DB importeren/exporteren](connector-azure-cosmos-db.md) als **u een JSON-bestand as-is in/vanuit Azure Cosmos DB wilt importeren/exporteren.**

Als u de JSON-bestanden wilt ontleden of de gegevens `type` in `format` JSON-indeling wilt schrijven, stelt u de eigenschap in de sectie in op **JsonFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| filePattern |Hiermee geeft u het patroon aan van gegevens die zijn opgeslagen in elk JSON-bestand. Toegestane waarden zijn **setOfObjects** en **arrayOfObjects**. De **standaardwaarde** is **setOfObjects**. Zie het gedeelte [JSON-bestandpatronen](#json-file-patterns) voor meer informatie over deze patronen. |Nee |
| jsonNodeReference | Als u wilt bladeren en gegevens wilt ophalen uit de objecten in een matrixveld met hetzelfde patroon, geeft u het JSON-pad van die matrix op. Deze eigenschap wordt alleen ondersteund bij het kopiëren van gegevens **uit** JSON-bestanden. | Nee |
| jsonPathDefinition | Hiermee geeft u de JSON-padexpressie aan voor elke kolomtoewijzing met een aangepaste kolomnaam (begin met een kleine letter). Deze eigenschap wordt alleen ondersteund bij het kopiëren van gegevens **uit** JSON-bestanden en u gegevens uit object of array extraheren. <br/><br/> Voor velden onder het hoofdobject begint u met root $; voor velden binnen de matrix die is gekozen door de eigenschap `jsonNodeReference`, begint u vanaf het element van de matrix. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies. | Nee |
| encodingName |Geef de coderingsnaam op. Zie voor de lijst met geldige namen voor versleuteling [De eigenschap Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Bijvoorbeeld: windows 1250 of shift_jis. De **standaardwaarde** is: **UTF-8**. |Nee |
| nestingSeparator |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. De standaardwaarde is '.' (punt). |Nee |

>[!NOTE]
>Voor het geval van gegevens met meerdere gegevens in array in meerdere rijen (voorbeeld 1 -> voorbeeld 2 `jsonNodeReference`in [JsonFormat-voorbeelden),](#jsonformat-example)u er alleen voor kiezen om één array uit te vouwen met behulp van eigenschap.

### <a name="json-file-patterns"></a>JSON-bestandpatronen

Kopieeractiviteit kan de volgende patronen van JSON-bestanden ontleden:

- **Type I: setOfObjects**

    Elk bestand bevat één object of meerdere door regels gescheiden/samengevoegde objecten. Wanneer deze optie is geselecteerd in een uitvoergegevensset, produceert de kopieerbewerking een enkel JSON-bestand met één object per regel (door regels gescheiden).

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

### <a name="jsonformat-example"></a>Voorbeeld van JsonFormat

**Voorbeeld 1: gegevens uit JSON-bestanden kopiëren**

**Voorbeeld 1: gegevens ophalen uit object en matrix**

In dit voorbeeld kunt u verwachten dat één JSON-hoofdobject wordt toegewezen aan één record in het tabelresultaat. Als u een JSON-bestand hebt met de volgende inhoud:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling door gegevens te extraheren uit de objecten en matrix:

| Id | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | Pc | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 13/1/2017 11:24:37 uur |

De invoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Met name:

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie [Kolommen van gegevenssetgegevensmap toewijzen aan kolommen met doelgegevensset](copy-activity-schema-and-type-mapping.md)voor meer informatie .
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. Als u gegevens uit array `array[x].property` wilt kopiëren, u `xth` de waarde van `array[*].property` de gegeven eigenschap uit het object extraheren of u de waarde vinden van een object dat een dergelijke eigenschap bevat.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Voorbeeld 2: meerdere objecten met hetzelfde patroon uit een matrix toepassen**

In dit voorbeeld probeert u een JSON-hoofdobject te transformeren naar meerdere records in een tabelresultaat. Als u een JSON-bestand hebt met de volgende inhoud:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

en u wilt het kopiëren naar een Azure SQL-tabel in de volgende indeling, door de gegevens binnen de matrix af te vlakken en samen te voegen met de algemene root-gegevens:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


De invoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Met name:

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie [Kolommen van gegevenssetgegevensmap toewijzen aan kolommen met doelgegevensset](copy-activity-schema-and-type-mapping.md)voor meer informatie .
- `jsonNodeReference`geeft aan gegevens te herhalen en te extraheren uit de objecten met hetzelfde patroon onder **array** `orderlines`.
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. In dit `ordernumber`voorbeeld `orderdate`, `city` en zijn onder hoofdobject `$.`met `order_pd` JSON pad te beginnen met , terwijl en `order_price` worden gedefinieerd met pad afgeleid van de array element zonder `$.`.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Houd rekening met de volgende punten:**

* Als `structure` en `jsonPathDefinition` niet zijn gedefinieerd in de gegevensset van Data Factory, detecteert de kopieerbewerking het schema van het eerste object en wordt het hele object afgevlakt.
* Als de JSON-invoer een matrix heeft, zet de kopieerbewerking de volledige matrix-waarde standaard om in een tekenreeks. U kunt ervoor kiezen om er gegevens uit op te halen met behulp van `jsonNodeReference` en/of `jsonPathDefinition`, of deze stap over te slaan door deze niet op te geven in `jsonPathDefinition`.
* Als er dubbele namen op hetzelfde niveau voorkomen, gebruikt de kopieerbewerking de laatste.
* Eigenschapnamen zijn hoofdlettergevoelig. Twee eigenschappen met dezelfde naam maar met een verschil in hoofdletters en kleine letters worden behandeld als twee afzonderlijke eigenschappen.

**Voorbeeld 2: gegevens schrijven naar een JSON-bestand**

Als u de volgende tabel in SQL-database hebt:

| Id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

en voor elke record verwacht u naar een JSON-object in de volgende indeling te schrijven:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

De uitvoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Meer specifiek `structure` definieert sectie de aangepaste `nestingSeparator` eigenschapsnamen in het doelbestand (standaard is '.") worden gebruikt om de nestlaag van de naam te identificeren. Dit gedeelte is **optioneel**, tenzij u de naam van de eigenschap wilt wijzigen ten opzichte van de naam van de bronkolom of sommige eigenschappen wilt nesten.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a>Parketformaat (legacy)

>[!NOTE]
>Leer het nieuwe model uit [parket formaat](format-parquet.md) artikel. De volgende configuraties op bestandsgebaseerde gegevensarchiefgegevensset worden nog steeds ondersteund als dit is voor achterwaartse compabitility. U wordt voorgesteld om het nieuwe model in de toekomst te gebruiken.

Als u de Parquet-bestanden wilt parseren of de gegevens in Parquet-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **ParquetFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Houd rekening met de volgende punten:

* Complexe gegevenstypen worden niet ondersteund (MAP, LIST).
* Witte ruimte in kolomnaam wordt niet ondersteund.
* Parquet-bestanden hebben de volgende opties voor compressie: NONE, SNAPPY, GZIP en LZO. Data Factory ondersteunt het lezen van gegevens uit het parketbestand in een van deze gecomprimeerde formaten, behalve LZO - het gebruikt de compressiecodec in de metagegevens om de gegevens te lezen. Bij het schrijven naar een Parquet-bestand kiest Data Factory echter SNAPPY, de standaardinstelling voor Parquet. Er is momenteel geen optie om dit gedrag te overschrijven.

> [!IMPORTANT]
> Voor copy empowered van Self-hosted Integration Runtime bijvoorbeeld tussen on-premises en cloud data stores, als u geen Parketbestanden kopieert zoals het is, moet u de **64-bits JRE 8 (Java Runtime Environment) of OpenJDK** op uw **IR-machine**installeren. Zie de volgende paragraaf met meer details.

Voor copy running op Self-hosted IR met Parquet file serialization/deserialization, adf lokaliseert de Java runtime door eerst het register *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* te controleren op JRE, indien niet gevonden, ten tweede het controleren van systeem variabele *`JAVA_HOME`* voor OpenJDK.

- **Om JRE te gebruiken:** De 64-bits IR vereist 64-bits JRE. U het vanaf [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Om OpenJDK te gebruiken:** het wordt ondersteund sinds IR-versie 3.13. Verpak de jvm.dll met alle andere vereiste samenstellingen van OpenJDK in Self-hosted IR machine, en stel systeemomgevingvariabele JAVA_HOME dienovereenkomstig in.

>[!TIP]
>Als u gegevens kopieert naar/van het Parket-formaat met behulp van Self-hosted Integration Runtime en hiterror met de tekst Er is een `_JAVA_OPTIONS` fout opgetreden bij het aanroepen van java, bericht: **java.lang.OutOfMemoryError:Java heap space**, u een omgevingsvariabele toevoegen in de machine die de zelfgehoste IR host om de min/max heapgrootte voor JVM aan te passen om een dergelijke kopie te machtigen en vervolgens de pijplijn opnieuw te gebruiken.

![Stel de grootte van de JVM-heap in op zelfgehoste IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Voorbeeld: variabele `_JAVA_OPTIONS` instellen `-Xms256m -Xmx16g`met waarde . De `Xms` vlag geeft de initiële groep geheugentoewijzing voor `Xmx` een Java Virtual Machine (JVM) op, terwijl de maximale geheugentoewijzingsgroep wordt opgegeven. Dit betekent dat JVM `Xms` zal worden gestart met de hoeveelheid `Xmx` geheugen en in staat zal zijn om een maximale hoeveelheid geheugen te gebruiken. Standaard gebruikt ADF min 64MB en max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Gegevenstypetoewijzing voor parketbestanden

| Tussentijds gegevenstype gegevensfabriek | Parket Primitief type | Parquet Original Type (Deserialiseren) | Parquet Original Type (Serialiseren) |
|:--- |:--- |:--- |:--- |
| Booleaans | Booleaans | N.v.t. | N.v.t. |
| SByte SByte | Int32 | Int8 Int8 | Int8 Int8 |
| Byte | Int32 | UInt8 UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/Binair | UInt64 | Decimal |
| Enkel | Drijvend | N.v.t. | N.v.t. |
| Double | Double | N.v.t. | N.v.t. |
| Decimal | Binair | Decimal | Decimal |
| Tekenreeks | Binair | Utf8 Utf8 | Utf8 Utf8 |
| DateTime | Int96 | N.v.t. | N.v.t. |
| TimeSpan | Int96 | N.v.t. | N.v.t. |
| Datumtijdverschuiving | Int96 | N.v.t. | N.v.t. |
| Bytearray | Binair | N.v.t. | N.v.t. |
| GUID | Binair | Utf8 Utf8 | Utf8 Utf8 |
| Char | Binair | Utf8 Utf8 | Utf8 Utf8 |
| CharArray (CharArray) | Niet ondersteund | N.v.t. | N.v.t. |

## <a name="orc-format-legacy"></a><a name="orc-format"></a>ORC-indeling (legacy)

>[!NOTE]
>Meer informatie over het nieuwe model uit het artikel [ORC-indeling.](format-orc.md) De volgende configuraties op bestandsgebaseerde gegevensarchiefgegevensset worden nog steeds ondersteund als dit is voor achterwaartse compabitility. U wordt voorgesteld om het nieuwe model in de toekomst te gebruiken.

Als u de ORC-bestanden wilt parseren of de gegevens in ORC-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **OrcFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "OrcFormat"
}
```

Houd rekening met de volgende punten:

* Complexe gegevenstypen worden niet ondersteund (STRUCT, MAP, LIST, UNION).
* Witte ruimte in kolomnaam wordt niet ondersteund.
* Een ORC-bestand heeft drie [opties voor compressie](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory ondersteunt het lezen van gegevens uit ORC-bestanden in een van deze gecomprimeerde indelingen. Hierbij wordt de compressiecodec in de metagegevens gebruikt om de gegevens te lezen. Bij het schrijven naar een ORC-bestand kiest Data Factory echter ZLIB, de standaardinstelling voor ORC. Er is momenteel geen optie om dit gedrag te overschrijven.

> [!IMPORTANT]
> Voor copy empowered van Self-hosted Integration Runtime bijvoorbeeld tussen on-premises en cloud data stores, als u orc-bestanden niet **kopieert zoals het is,** moet u de **64-bits JRE 8 (Java Runtime Environment) of OpenJDK** op uw IR-machine installeren. Zie de volgende paragraaf met meer details.

Voor copy running op Self-hosted IR met ORC-bestandsserialisatie/deserialisatie, lokaliseert ADF de Java-runtime door eerst het register *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* te controleren op JRE, als deze niet is gevonden, ten tweede systeemvariabele *`JAVA_HOME`* voor OpenJDK te controleren.

- **Om JRE te gebruiken:** De 64-bits IR vereist 64-bits JRE. U het vanaf [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Om OpenJDK te gebruiken:** het wordt ondersteund sinds IR-versie 3.13. Verpak de jvm.dll met alle andere vereiste samenstellingen van OpenJDK in Self-hosted IR machine, en stel systeemomgevingvariabele JAVA_HOME dienovereenkomstig in.

### <a name="data-type-mapping-for-orc-files"></a>Toewijzing van gegevenstype voor ORC-bestanden

| Tussentijds gegevenstype gegevensfabriek | ORC-typen |
|:--- |:--- |
| Booleaans | Booleaans |
| SByte SByte | Byte |
| Byte | Korte |
| Int16 | Korte |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Lange |
| Int64 | Lange |
| UInt64 | Tekenreeks |
| Enkel | Drijvend |
| Double | Double |
| Decimal | Decimal |
| Tekenreeks | Tekenreeks |
| DateTime | Tijdstempel |
| Datumtijdverschuiving | Tijdstempel |
| TimeSpan | Tijdstempel |
| Bytearray | Binair |
| GUID | Tekenreeks |
| Char | Char(1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a>AVRO-formaat (legacy)

>[!NOTE]
>Leer het nieuwe model uit [avro formaat](format-avro.md) artikel. De volgende configuraties op bestandsgebaseerde gegevensarchiefgegevensset worden nog steeds ondersteund als dit is voor achterwaartse compabitility. U wordt voorgesteld om het nieuwe model in de toekomst te gebruiken.

Als u de Avro-bestanden wilt parseren of de gegevens in Avro-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **AvroFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "AvroFormat",
}
```

Als u het Avro-formaat in een Hive-tabel wilt gebruiken, u verwijzen naar [de zelfstudie van Apache Hive.](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)

Houd rekening met de volgende punten:

* [Complexe gegevenstypen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, arrays, kaarten, vakbonden en vaste bestanden).

## <a name="compression-support-legacy"></a><a name="compression-support"></a>Compressieondersteuning (legacy)

Azure Data Factory ondersteunt het comprimeren/decomprimeren van gegevens tijdens het kopiëren. Wanneer u `compression` de eigenschap opgeeft in een invoergegevensset, leest de kopieeractiviteit de gecomprimeerde gegevens van de bron en decomprimeert deze. en wanneer u de eigenschap in een uitvoergegevensset opgeeft, schrijft het comprimeren van kopieeractiviteit vervolgens gegevens naar de gootsteen. Hier volgen een paar voorbeeldscenario's:

* Lees GZIP-gecomprimeerde gegevens uit een Azure-blob, decomprimeer deze en schrijf resultaatgegevens naar een Azure SQL-database. U definieert de invoer-Azure Blob-gegevensset met de `compression` `type` eigenschap als GZIP.
* Lees gegevens uit een bestand met platte tekst van on-premises bestandssysteem, comprimeer het met de GZip-indeling en schrijf de gecomprimeerde gegevens naar een Azure-blob. U definieert een uitvoerazure `compression` `type` blob-gegevensset met de eigenschap als GZip.
* Lees .zip-bestand van ftp-server, decomprimeer het om de bestanden binnen te krijgen en land deze bestanden in Azure Data Lake Store. U definieert een FTP-invoerset met de `compression` `type` eigenschap als ZipDeflate.
* Lees een GZIP-gecomprimeerde gegevens uit een Azure-blob, decomprimeer deze, comprimeer deze met BZIP2 en schrijf resultaatgegevens naar een Azure-blob. U definieert de invoer-Azure Blob-gegevensset `compression` `type` met `compression` `type` ingesteld op GZIP en de uitvoergegevensset met ingesteld op BZIP2.

Als u compressie voor een gegevensset wilt opgeven, gebruikt u de **compressieeigenschap** in de gegevensset JSON zoals in het volgende voorbeeld:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

De **compressiesectie** heeft twee eigenschappen:

* **Type:** de compressiecodec, die **GZIP,** **Deflate,** **BZIP2**of **ZipDeflate**kan zijn. Wanneer u kopieeractiviteit gebruikt om zipdeflate-bestand(en) te decomprimeren en naar het opslagarchief `<path specified in dataset>/<folder named as source zip file>/`voor bestanden te schrijven, worden bestanden naar de map geëxtraheerd: .
* **Niveau:** de compressieverhouding, die **optimaal** of **snelste**kan zijn.

  * **Snelste:** De compressiebewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.
  * **Optimaal**: De compressiebewerking moet optimaal worden gecomprimeerd, zelfs als de bewerking langer duurt.

    Zie Onderwerp [compressieniveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie.

> [!NOTE]
> Compressie-instellingen worden niet ondersteund voor gegevens in het **AvroFormat**, **OrcFormat**of **Parketformaat**. Bij het lezen van bestanden in deze indelingen detecteert en gebruikt Data Factory de compressiecodec in de metagegevens. Bij het schrijven naar bestanden in deze indelingen kiest Data Factory de standaardcompressiecodec voor die indeling. Bijvoorbeeld ZLIB voor OrcFormat en SNAPPY voor ParketFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Niet-ondersteunde bestandstypen en compressie-indelingen

U de uitbreidbaarheidsfuncties van Azure Data Factory gebruiken om bestanden te transformeren die niet worden ondersteund.
Twee opties zijn Azure-functies en aangepaste taken met Azure Batch.

U een voorbeeld zien dat een Azure-functie gebruikt om [de inhoud van een teerbestand](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)te extraheren. Zie [Azure-functiesactiviteit](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)voor meer informatie .

U deze functionaliteit ook bouwen met behulp van een aangepaste dotnetactiviteit. Meer informatie is [hier](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) beschikbaar

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwste ondersteunde bestandsindelingen en compressies van [ondersteunde bestandsindelingen en compressies.](supported-file-formats-and-compression-codecs.md)
