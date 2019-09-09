---
title: Ondersteunde bestandsindelingen in Azure Data Factory | Microsoft Docs
description: Dit onderwerp beschrijft de bestandsindelingen en compressie-codes die worden ondersteund door de connectors op basis van bestanden in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 2c8983b5d6a44834d0c9659877c857fd73805ce6
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812303"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Ondersteunde indelingen en codecs voor de compressie in Azure Data Factory

*Dit artikel is van toepassing op de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).*

Als u wilt **bestanden als kopiëren-is** overslaan tussen op basis van bestanden (binaire kopie), het gedeelte indeling in beide definities van de gegevensset voor invoer en uitvoer. Als u wilt **parseren of bestanden met een specifieke indeling genereren**, Azure Data Factory ondersteunt de volgende indeling bestandstypen:

* [Tekstindeling](#text-format)
* [JSON-indeling](#json-format)
* [Parquet-indeling](#parquet-format)
* [ORC-indeling](#orc-format)
* [Avro-indeling](#avro-format)
* [Binaire indeling](#binary-format)

> [!TIP]
> Meer informatie over hoe kopieer activiteiten uw bron gegevens koppelt aan de [schema toewijzing in de Kopieer activiteit](copy-activity-schema-and-type-mapping.md).

## <a name="text-format"></a>Tekstindeling

>[!NOTE]
>Data Factory nieuwe gegevensset met scheidings tekens voor tekst opmaak hebt geïntroduceerd, raadpleegt u artikel met [scheidings](format-delimited-text.md) tekens voor tekst met details. De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u wilt lezen uit een tekstbestand of schrijven naar een tekstbestand, stelt u de `type` eigenschap in de `format` sectie van de gegevensset in **TextFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Raadpleeg het gedeelte [TextFormat-voorbeeld](#textformat-example) voor configuratie-instructies.

| Eigenschap | Description | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| columnDelimiter |Het teken dat wordt gebruikt voor het scheiden van kolommen in een bestand. U kunt overwegen een zeldzaam niet-afdrukbaar teken die mogelijk niet aanwezig zijn in uw gegevens gebruiken. Geef bijvoorbeeld '\u0001', waarmee de Start van de kop (SOH). |Er is slechts één teken toegestaan. De **standaardwaarde** is een **komma (',')** . <br/><br/>Raadpleeg voor het gebruik van een Unicode-teken, [Unicode-tekens](https://en.wikipedia.org/wiki/List_of_Unicode_characters) om op te halen van de bijbehorende code voor deze. |Nee |
| rowDelimiter |Het teken dat wordt gebruikt voor het scheiden van rijen in een bestand. |Er is slechts één teken toegestaan. De **standaardwaarde** is een van de volgende leeswaarden **['\r\n', '\r', '\n']** en de schrijfwaarde **'\r\n'** . |Nee |
| escapeChar |Dit speciale teken wordt gebruikt om een scheidingsteken voor kolommen van de inhoud van het invoerbestand om te zetten. <br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voor beeld: als u een komma (', ') hebt als kolom scheidings teken, maar u wilt het punt komma in de tekst hebben (bijvoorbeeld: "Hallo, wereld"), u kunt $ definiëren als escape teken en de teken reeks "Hallo $, wereld" in de bron gebruiken. |Nee |
| quoteChar |Het teken dat wordt gebruikt om een tekenreekswaarde te citeren. De scheidingstekens voor kolommen en rijen binnen de aanhalingstekens worden beschouwd als onderdeel van de tekenreekswaarde. Deze eigenschap is van toepassing op gegevenssets voor invoer en uitvoer.<br/><br/>Het is niet mogelijk om zowel escapeChar als quoteChar voor een tabel op te geven. |Er is slechts één teken toegestaan. Er is geen standaardwaarde. <br/><br/>Voorbeeld: als u kolommen scheidt met komma's (', '), maar u het kommateken in een tekst wilt gebruiken (voorbeeld: <Hallo, wereld>), kunt u " (dubbel aanhalingsteken) als het aanhalingsteken opgeven en de tekenreeks "Hallo, wereld" in de bron gebruiken. |Nee |
| nullValue |Een of meer tekens die worden gebruikt om een null-waarde te vertegenwoordigen. |Een of meer tekens. De **standaardwaarden** zijn **'\N' en 'NULL'** voor lezen en **'\N'** voor schrijven. |Nee |
| encodingName |Geef de coderingsnaam op. |Een geldige coderingsnaam. Zie [De eigenschap Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Voorbeeld: windows 1250 of shift_jis. De **standaardwaarde** is **UTF-8**. |Nee |
| firstRowAsHeader |Hiermee geeft u op of de eerste rij als een header moet worden gezien. Bij een gegevensset voor invoer leest Data Factory de eerste rij als een header. Bij een gegevensset voor uitvoer schrijft Data Factory de eerste rij als een header. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount` ](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |True<br/><b>False (standaard)</b> |Nee |
| skipLineCount |Geeft het aantal **niet-lege** rijen dat moet worden overgeslagen bij het lezen van gegevens in invoerbestanden. Als zowel skipLineCount als firstRowAsHeader is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand. <br/><br/>Zie [Gebruiksscenario's`firstRowAsHeader` en `skipLineCount` ](#scenarios-for-using-firstrowasheader-and-skiplinecount) voor voorbeeldscenario's. |Geheel getal |Nee |
| treatEmptyAsNull |Hiermee geeft u aan of null of lege tekenreeks moeten worden behandeld als een null-waarde bij het lezen van gegevens uit een invoerbestand. |**True (standaard)**<br/>False |Nee |

### <a name="textformat-example"></a>Voorbeeld van TextFormat

In de volgende JSON-definitie voor een gegevensset, worden enkele van de optionele eigenschappen opgegeven.

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

* U kopieert vanuit een niet-bestands bron naar een tekst bestand en wilt een header regel toevoegen met de meta gegevens van het schema (bijvoorbeeld: SQL-schema). Geef voor `firstRowAsHeader` 'True' op in de uitvoergegevensset voor dit scenario.
* U wilt kopiëren vanuit een tekstbestand met een headerregel naar een sink die geen bestand is en wilt die regel verwijderen. Geef voor `firstRowAsHeader` 'True' op in de invoergegevensset.
* U wilt kopiëren uit een tekstbestand en wilt een paar regels aan het begin overslaan die geen gegevens of headerinformatie bevatten. Geef `skipLineCount` op om aan te geven hoeveel regels er moeten worden overgeslagen. Als de rest van het bestand een headerregel bevat, kunt u ook `firstRowAsHeader` opgeven. Als zowel `skipLineCount` als `firstRowAsHeader` is opgegeven, worden de regels eerst overgeslagen en wordt de headerinformatie gelezen uit het invoerbestand

## <a name="json-format"></a>JSON-indeling

>[!NOTE]
>Zie [JSON](format-json.md) article with Details (engelstalig) Data Factory nieuwe GEGEVENSSET voor json-indeling hebt geïntroduceerd. De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Naar **importeren/exporteren als een JSON-bestand-in / uit Azure Cosmos DB**, Zie de sectie van de Import/export-JSON-documenten in [gegevens verplaatsen naar/van Azure Cosmos DB](connector-azure-cosmos-db.md) artikel.

Als u wilt voor het parseren van JSON-bestanden of de gegevens in JSON-indeling wilt schrijven, stelt u de `type` eigenschap in de `format` gedeelte **JsonFormat**. U kunt ook de volgende **optionele** eigenschappen opgeven in het gedeelte `format`. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies.

| Eigenschap | Description | Vereist |
| --- | --- | --- |
| filePattern |Hiermee geeft u het patroon aan van gegevens die zijn opgeslagen in elk JSON-bestand. Toegestane waarden zijn **setOfObjects** en **arrayOfObjects**. De **standaardwaarde** is **setOfObjects**. Zie het gedeelte [JSON-bestandpatronen](#json-file-patterns) voor meer informatie over deze patronen. |Nee |
| jsonNodeReference | Als u wilt bladeren en gegevens wilt ophalen uit de objecten in een matrixveld met hetzelfde patroon, geeft u het JSON-pad van die matrix op. Deze eigenschap wordt alleen ondersteund bij het kopiëren **van gegevens uit** json-bestanden. | Nee |
| jsonPathDefinition | Hiermee geeft u de JSON-padexpressie aan voor elke kolomtoewijzing met een aangepaste kolomnaam (begin met een kleine letter). Deze eigenschap wordt alleen ondersteund bij het kopiëren **van gegevens uit** json-bestanden en u kunt gegevens uit een object of matrix ophalen. <br/><br/> Voor velden onder het hoofdobject begint u met root $; voor velden binnen de matrix die is gekozen door de eigenschap `jsonNodeReference`, begint u vanaf het element van de matrix. Zie het gedeelte [JsonFormat-voorbeeld](#jsonformat-example) voor configuratie-instructies. | Nee |
| encodingName |Geef de coderingsnaam op. Zie voor de lijst met geldige coderings namen: Eigenschap [Encoding. coderings](https://msdn.microsoft.com/library/system.text.encoding.aspx) naam. Bijvoorbeeld: windows 1250 of shift_jis. De **standaard** waarde is: **UTF-8**. |Nee |
| nestingSeparator |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. De standaardwaarde is '.' (punt). |Nee |

>[!NOTE]
>In het geval van cross-apply gegevens in een matrix in meerdere rijen (hoofdletter 1-> voor beeld 2 in [JsonFormat-voor beelden](#jsonformat-example)), kunt u er alleen voor kiezen `jsonNodeReference`om één matrix uit te breiden met behulp van eigenschap.

### <a name="json-file-patterns"></a>JSON-bestandpatronen

Kopieeractiviteit kan de volgende patronen van JSON-bestanden parseren:

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

**Voor beeld 1: Gegevens uit JSON-bestanden kopiëren**

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

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie voor meer informatie, [bronkolommen gegevensset worden toegewezen aan de gegevensset doelkolommen](copy-activity-schema-and-type-mapping.md).
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. U kunt gebruiken om gegevens te kopiëren van de matrix, `array[x].property` om op te halen van de waarde van de opgegeven eigenschap van de `xth` object, of u kunt gebruiken `array[*].property` gezocht naar de waarde van een object met deze eigenschap.

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

- Het gedeelte `structure` definieert de aangepaste kolomnamen en het bijbehorende gegevenstype tijdens het converteren van gegevens in tabelvorm. Dit gedeelte is **optioneel**, tenzij u kolommen moet toewijzen. Zie voor meer informatie, [bronkolommen gegevensset worden toegewezen aan de gegevensset doelkolommen](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` Hiermee wordt aangegeven en dat er gegevens ophalen uit de objecten met hetzelfde patroon onder **matrix** `orderlines`.
- Met `jsonPathDefinition` geeft u het JSON-pad op voor elke kolom die aangeeft waar de gegevens moeten worden opgehaald. In dit voorbeeld `ordernumber`, `orderdate`, en `city` zijn onder het hoofdobject met JSON-pad begint met `$.`, terwijl `order_pd` en `order_price` zijn gedefinieerd met het pad dat is afgeleid van het matrixelement zonder `$.` .

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

**Voor beeld 2: Gegevens schrijven naar JSON-bestand**

Als u de volgende tabel in SQL-Database hebt:

| Id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

en voor elke record die u verwacht te schrijven naar een JSON-object in de volgende indeling:

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

De uitvoergegevensset met het type **JsonFormat** wordt als volgt gedefinieerd: (gedeeltelijke definitie met alleen belangrijke onderdelen). Meer specifiek, `structure` gedeelte definieert de namen van aangepaste eigenschappen in het doelbestand, `nestingSeparator` (de standaardwaarde is '. ') worden gebruikt voor identificatie van de nestlaag van de naam. Dit gedeelte is **optioneel**, tenzij u de naam van de eigenschap wilt wijzigen ten opzichte van de naam van de bronkolom of sommige eigenschappen wilt nesten.

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

## <a name="parquet-format"></a>Parquet-indeling

>[!NOTE]
>Data Factory nieuwe gegevensset voor Parquet-indeling hebt geïntroduceerd, raadpleegt u het artikel [Parquet Format](format-parquet.md) met details. De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u de Parquet-bestanden wilt parseren of de gegevens in Parquet-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **ParquetFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Houd rekening met de volgende punten:

* Complexe gegevens typen worden niet ondersteund (kaart, lijst).
* Spaties in kolom naam wordt niet ondersteund.
* Het Parquet-bestand heeft de volgende opties voor compressie: GEEN, SNAPPY, GZIP en LZO. Data Factory ondersteunt het lezen van gegevens uit het Parquet-bestand in een van deze gecomprimeerde indelingen, met uitzonde ring van LZO, gebruikt de compressie-codec in de meta gegevens om de gegevens te lezen. Bij het schrijven naar een Parquet-bestand kiest Data Factory echter SNAPPY, de standaardinstelling voor Parquet. Er is momenteel geen optie om dit gedrag te overschrijven.

> [!IMPORTANT]
> Als u de kopie wilt voorzien van zelf-hostende Integration Runtime bijvoorbeeld tussen on-premises en gegevens opslag in de Cloud, moet u de **64-bits jre 8 (Java Runtime Environment) of openjdk** op uw IR-computer **installeren, als**u geen Parquet-bestanden kopieert. Raadpleeg de volgende alinea met meer informatie.

Voor een kopie die wordt uitgevoerd op een zelf-hostende IR met Parquet-serialisatie/deserialisatie, zoekt ADF de Java-runtime *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* door het REGI ster voor jre te controleren, indien niet *`JAVA_HOME`* gevonden, ter controle van de systeem variabele voor openjdk.

- **Jre gebruiken**: De 64-bits IR vereist een 64-bits JRE. U kunt deze [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Als u openjdk wilt gebruiken**, wordt dit ondersteund sinds IR-versie 3,13. Verdeel het JVM. dll-bestand met alle andere vereiste assembly's van OpenJDK in een zelf-hostende IR-computer en stel de systeem omgevings variabele JAVA_HOME dienovereenkomstig in.

>[!TIP]
>Als u gegevens kopieert naar/van Parquet-indeling met zelf-hostende Integration runtime en de fout melding ' er is een fout opgetreden bij het aanroepen van Java, bericht: **Java. lang. OutOfMemoryError: Java-heap**- `_JAVA_OPTIONS` ruimte ', kunt u een omgevings variabele toevoegen in de machine die de zelf-hostende IR host om de minimale/maximale Heap-grootte voor JVM aan te passen om een dergelijke kopie te kunnen uitvoeren, en vervolgens de pijp lijn opnieuw uit te voeren.

![JVM-Heap-grootte instellen op zelf-hostende IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Voor beeld: Stel `_JAVA_OPTIONS` variabele met `-Xms256m -Xmx16g`waarde in. Met de `Xms` vlag wordt de eerste geheugen toewijzings groep opgegeven voor een Java Virtual Machine (JVM `Xmx` ), terwijl de maximale geheugen toewijzings groep wordt opgegeven. Dit betekent dat JVM wordt gestart met `Xms` de hoeveelheid geheugen en dat er een `Xmx` maximum hoeveelheid geheugen kan worden gebruikt. ADF gebruikt standaard min 64 MB en Max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Gegevenstype toewijzing voor Parquet-bestanden

| Data factory tussentijdse gegevenstype | Parquet primitief Type | Parquet oorspronkelijke Type (deserialiseren) | Parquet oorspronkelijke Type (serialiseren) |
|:--- |:--- |:--- |:--- |
| Booleaans | Booleaans | N/A | N/A |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64 binaire | UInt64 | decimaal |
| Enkelvoudig | Float | N/A | N/A |
| Double-waarde | Double-waarde | N/A | N/A |
| decimaal | Binair bestand | decimaal | decimaal |
| Reeks | Binair bestand | Utf8 | Utf8 |
| DateTime | Int96 | N/A | N/A |
| TimeSpan | Int96 | N/A | N/A |
| DateTimeOffset | Int96 | N/A | N/A |
| ByteArray | Binair bestand | N/A | N/A |
| GUID | Binair bestand | Utf8 | Utf8 |
| CHAR | Binair bestand | Utf8 | Utf8 |
| CharArray | Niet ondersteund | N/A | N/A |

## <a name="orc-format"></a>ORC-indeling

Als u de ORC-bestanden wilt parseren of de gegevens in ORC-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **OrcFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "OrcFormat"
}
```

Houd rekening met de volgende punten:

* Complexe gegevens typen worden niet ondersteund (STRUCT, kaart, lijst, samen VOEGing).
* Spaties in kolom naam wordt niet ondersteund.
* Het ORC-bestand heeft drie [opties met betrekking tot compressie](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): GEEN, ZLIB, SNAPPY. Data Factory ondersteunt het lezen van gegevens uit ORC-bestanden in een van deze gecomprimeerde indelingen. Hierbij wordt de compressiecodec in de metagegevens gebruikt om de gegevens te lezen. Bij het schrijven naar een ORC-bestand kiest Data Factory echter ZLIB, de standaardinstelling voor ORC. Er is momenteel geen optie om dit gedrag te overschrijven.

> [!IMPORTANT]
> Als u de kopie wilt voorzien van zelf-hostende Integration Runtime bijvoorbeeld tussen on-premises en gegevens opslag in de Cloud, moet u de **64-bits jre 8 (Java Runtime Environment) of openjdk** op uw IR-computer **installeren, als**u geen Orc-bestanden kopieert. Raadpleeg de volgende alinea met meer informatie.

Voor een kopie die wordt uitgevoerd op een zelf-hostende IR met Orc-serialisatie/deserialisatie, zoekt ADF de Java-runtime *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* door het REGI ster voor jre te controleren, indien niet *`JAVA_HOME`* gevonden, ter controle van de systeem variabele voor openjdk.

- **Jre gebruiken**: De 64-bits IR vereist een 64-bits JRE. U kunt deze [hier](https://go.microsoft.com/fwlink/?LinkId=808605)vinden.
- **Als u openjdk wilt gebruiken**, wordt dit ondersteund sinds IR-versie 3,13. Verdeel het JVM. dll-bestand met alle andere vereiste assembly's van OpenJDK in een zelf-hostende IR-computer en stel de systeem omgevings variabele JAVA_HOME dienovereenkomstig in.

### <a name="data-type-mapping-for-orc-files"></a>Gegevenstype toewijzing voor ORC-bestanden

| Data factory tussentijdse gegevenstype | ORC-typen |
|:--- |:--- |
| Booleaans | Booleaans |
| SByte | Byte |
| Byte | Kort |
| Int16 | Kort |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Lang |
| Int64 | Lang |
| UInt64 | Reeks |
| Enkelvoudig | Float |
| Double-waarde | Double-waarde |
| decimaal | decimaal |
| Reeks | Reeks |
| DateTime | Tijdstempel |
| DateTimeOffset | Tijdstempel |
| TimeSpan | Tijdstempel |
| ByteArray | Binair bestand |
| GUID | Reeks |
| CHAR | CHAR(1) |

## <a name="avro-format"></a>AVRO-indeling

>[!NOTE]
>Data Factory nieuwe gegevensset voor Avro-indeling hebt geïntroduceerd, raadpleegt u het artikel [AVRI Format](format-avro.md) met details. De volgende configuraties voor gegevens opslag gegevensset op basis van een bestand worden nog steeds ondersteund als-is voor achterwaartse compabitility. U wordt aangeraden het nieuwe model verder te gebruiken.

Als u de Avro-bestanden wilt parseren of de gegevens in Avro-indeling wilt schrijven, stelt u de eigenschap `format` `type` in op **AvroFormat**. U hoeft geen eigenschappen op te geven in het gedeelte Indeling binnen het gedeelte typeProperties. Voorbeeld:

```json
"format":
{
    "type": "AvroFormat",
}
```

Als u de Avro-indeling wilt gebruiken in een Hive-tabel, kunt u de [Zelfstudie voor Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) raadplegen.

Houd rekening met de volgende punten:

* [Complexe gegevenstypen](https://avro.apache.org/docs/current/spec.html#schema_complex) worden niet ondersteund (records, enums, matrices, kaarten, samenvoegingen, en vaste bestanden).

## <a name="binary-format"></a>Binaire indeling

Raadpleeg het artikel over [binaire indeling](format-binary.md) voor meer informatie.

## <a name="compression-support"></a>Compressieondersteuning voor

Azure Data Factory ondersteunt comprimeren/decomprimeren van gegevens tijdens het kopiëren. Wanneer u opgeeft `compression` eigenschap in een invoergegevensset, de kopieerbewerking de gecomprimeerde gegevens lezen van de bron- en decomprimeren; en wanneer u de eigenschap opgeeft in een uitvoergegevensset, de copy-activiteit gecomprimeerd en vervolgens gegevens schrijven naar de sink. Hier volgen enkele eenvoudige voorbeeldscenario's:

* Lezen GZIP gecomprimeerde gegevens uit een Azure-blob, het decomprimeren en resultaatgegevens schrijven naar een Azure SQL database. U definieert de Azure Blob-invoergegevensset met het `compression` `type` eigenschap zoals GZIP.
* Gegevens lezen uit een bestand met tekst zonder opmaak van on-premises bestandssysteem, comprimeren met behulp van GZip-indeling en de gecomprimeerde gegevens schrijven naar een Azure-blob. Definieert u een Azure Blob-uitvoergegevensset met de `compression` `type` eigenschap zoals GZip.
* ZIP-bestand lezen van FTP-server, decomprimeren voor het ophalen van de bestanden in, en deze bestanden worden neergezet in Azure Data Lake Store. U definieert een invoergegevensset FTP met de `compression` `type` eigenschap ZipDeflate.
* Een gecomprimeerde GZIP-gegevens lezen uit een Azure-blob, het decomprimeren, comprimeren met behulp van BZIP2 en resultaatgegevens schrijven naar een Azure-blob. U definieert de Azure Blob-invoergegevensset met `compression` `type` ingesteld op GZIP- en de uitvoergegevensset met `compression` `type` ingesteld op BZIP2.

Als u compressie voor een gegevensset, gebruikt u de **compressie** eigenschap in de gegevensset JSON zoals in het volgende voorbeeld:

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

De **compressie** sectie heeft twee eigenschappen:

* **Type:** de compressiecodec, dit kan **GZIP**, **Deflate**, **BZIP2**, of **ZipDeflate**.
* **Niveau:** de compressieverhouding, dit kan **optimale** of **snelst**.

  * **Snelste** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.
  * **Optimaal**: De compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt.

    Zie voor meer informatie, [compressieniveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) onderwerp.

> [!NOTE]
> Compressie-instellingen worden niet ondersteund voor gegevens in de **AvroFormat**, **OrcFormat**, of **ParquetFormat**. Bij het lezen van bestanden in de volgende indelingen, wordt in Data Factory wordt gedetecteerd en wordt de compressiecodec in de metagegevens. Bij het schrijven naar bestanden in de volgende indelingen, kiest Data Factory de compressiecodec standaard voor deze indeling. Bijvoorbeeld, ZLIB voor OrcFormat en SNAPPY voor ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Niet-ondersteunde bestands typen en compressie-indelingen

U kunt de uitbreidings functies van Azure Data Factory gebruiken om bestanden te transformeren die niet worden ondersteund.
Er zijn twee opties Azure Functions en aangepaste taken met behulp van Azure Batch.

U kunt een voor beeld zien waarin een Azure-functie wordt gebruikt om [de inhoud van een tar-bestand te extra heren](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Zie [Azure functions-activiteit](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)voor meer informatie.

U kunt deze functionaliteit ook bouwen met behulp van een aangepaste DotNet-activiteit. Meer informatie is [hier](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) beschikbaar

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor gegevensarchieven die op bestanden gebaseerde ondersteund door Azure Data Factory:

- [Azure Blob Storage-connector](connector-azure-blob-storage.md)
- [Azure Data Lake Store-connector](connector-azure-data-lake-store.md)
- [Amazon S3-connector](connector-amazon-simple-storage-service.md)
- [Bestandssysteemconnector](connector-file-system.md)
- [FTP-connector](connector-ftp.md)
- [SFTP-connector](connector-sftp.md)
- [HDFS-connector](connector-hdfs.md)
- [HTTP-connector](connector-http.md)
