---
title: JSON-indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u omgaat met de JSON-indeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: c18d767afd3721bd6f6250058b9fbe66990133e4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417739"
---
# <a name="json-format-in-azure-data-factory"></a>JSON-indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel wanneer u **de JSON-bestanden wilt ontleden of de gegevens in JSON-indeling**wilt schrijven. 

Json-indeling wordt ondersteund voor de volgende connectors: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de JSON-gegevensset.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **Json**. | Ja      |
| location         | Locatie-instellingen van het bestand(en). Elke op bestanden gebaseerde connector heeft zijn `location`eigen locatietype en ondersteunde eigenschappen onder . **Zie details in de sectie -> gegevensseteigenschappen**van de connector . | Ja      |
| encodingName     | Het coderingstype dat wordt gebruikt om testbestanden te lezen/schrijven. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nee       |
| compressie | Groep eigenschappen om bestandscompressie te configureren. Configureer deze sectie wanneer u compressie/decompressie wilt uitvoeren tijdens de uitvoering van de activiteit. | Nee |
| type | De compressiecodec die wordt gebruikt voor het lezen/schrijven van JSON-bestanden. <br>Toegestane waarden zijn **bzip2,** **gzip,** **leeglopen,** **ZipDeflate**, **pittig**of **lz4**. te gebruiken bij het opslaan van het bestand. Standaard wordt niet gecomprimeerd.<br>**Opmerking** op dit moment Kopieeractiviteit ondersteunt geen "pittige" & "lz4", en het toewijzen van gegevensstroom ondersteunt geen "ZipDeflate".<br>**Wanneer u** kopieeractiviteit gebruikt om zipdeflate-bestand(en) te decomprimeren en naar het opslagarchief `<path specified in dataset>/<folder named as source zip file>/`voor bestanden te schrijven, worden bestanden naar de map geëxtraheerd: . | Nee.  |
| niveau | De compressieverhouding. <br>Toegestane waarden zijn **optimaal** of **snelst.**<br>- **Snelste:** De compressiebewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: De compressiebewerking moet optimaal worden gecomprimeerd, zelfs als de bewerking langer duurt. Zie Onderwerp [compressieniveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie. | Nee       |

Hieronder vindt u een voorbeeld van de JSON-gegevensset over Azure Blob Storage:

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
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de JSON-bron en -gootsteen.

### <a name="json-as-source"></a>JSON als bron

De volgende eigenschappen worden ondersteund in de *** \*sectie\* *** bron van kopieeractiviteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **JSONSource**. | Ja      |
| storeInstellingen | Een groep eigenschappen voor het lezen van gegevens uit een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde leesinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

### <a name="json-as-sink"></a>JSON als gootsteen

De volgende eigenschappen worden ondersteund in de sectie copy activity *** \*sink.\* ***

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op **JSONSink**. | Ja      |
| opmaakInstellingen | Een groep eigenschappen. Raadpleeg hieronder de tabel **met schrijfinstellingen van JSON.** | Nee       |
| storeInstellingen | Een groep eigenschappen voor het schrijven van gegevens naar een gegevensarchief. Elke op bestanden gebaseerde connector heeft `storeSettings`zijn eigen ondersteunde schrijfinstellingen onder . **Zie details in de sectie connectorartikel -> Activiteitseigenschappen kopiëren**. | Nee       |

Ondersteunde **JSON-schrijfinstellingen** onder `formatSettings`:

| Eigenschap      | Beschrijving                                                  | Vereist                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Het type indelingInstellingen moet worden ingesteld op **JsonWriteSettings**. | Ja                                                   |
| filePattern |Hiermee geeft u het patroon aan van gegevens die zijn opgeslagen in elk JSON-bestand. Toegestane waarden zijn **setOfObjects** en **arrayOfObjects**. De **standaardwaarde** is **setOfObjects**. Zie het gedeelte [JSON-bestandpatronen](#json-file-patterns) voor meer informatie over deze patronen. |Nee |

### <a name="json-file-patterns"></a>JSON-bestandpatronen

Kopieeractiviteit kan automatisch de volgende patronen van JSON-bestanden detecteren en ontleden. 

- **Type I: setOfObjects**

    Elk bestand bevat één object of meerdere door regels gescheiden/samengevoegde objecten. 
    Wanneer deze optie wordt gekozen in de inhoudslaag van de kopieeractiviteit, produceert kopieeractiviteit één JSON-bestand met elk object per regel (regel-afgebakend).

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

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

JSON-bestandstypen kunnen worden gebruikt als zowel een gootsteen als een bron in de kaartgegevensstroom.

### <a name="creating-json-structures-in-a-derived-column"></a>Json-structuren maken in een afgeleide kolom

U een complexe kolom toevoegen aan uw gegevensstroom via de afgeleide kolomexpressiebouwer. Voeg in de afgeleide kolomtransformatie een nieuwe kolom toe en open de expressiebouwer door op het blauwe vak te klikken. Als u een kolom complex wilt maken, u de JSON-structuur handmatig invoeren of de UX gebruiken om subkolommen interactief toe te voegen.

#### <a name="using-the-expression-builder-ux"></a>De UX met expressiebouwer gebruiken

Plaats in het zijvenster van het uitvoerschema de muisaanwijzer op een kolom en klik op het pluspictogram. Selecteer **Subkolom toevoegen** om van de kolom een complex type te maken.

![Subkolom toevoegen](media/data-flow/addsubcolumn.png "Subkolom toevoegen")

U op dezelfde manier extra kolommen en subkolommen toevoegen. Voor elk niet-complex veld kan een expressie worden toegevoegd in de expressieeditor aan de rechterkant.

![Complexe kolom](media/data-flow/complexcolumn.png "Complexe kolom")

#### <a name="entering-the-json-structure-manually"></a>Handmatig de JSON-structuur invoeren

Als u handmatig een JSON-structuur wilt toevoegen, voegt u een nieuwe kolom toe en voert u de expressie in de editor in. De expressie volgt de volgende algemene notatie:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Als deze expressie is ingevoerd voor een kolom met de naam 'complexColumn', wordt deze naar de gootsteen geschreven als de volgende JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Voorbeeldvan handmatig script voor volledige hiërarchische definitie
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Opties voor bronindeling

Met behulp van een JSON-gegevensset als bron in uw gegevensstroom u vijf extra instellingen instellen. Deze instellingen zijn te vinden onder de **JSON-instellingen** accordeon in het tabblad **Bronopties.**  

![JSON-instellingen](media/data-flow/json-settings.png "JSON-instellingen")

#### <a name="default"></a>Standaard

Standaard worden JSON-gegevens in de volgende indeling gelezen.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Eén document

Als **Enkel document** is geselecteerd, worden in kaartgegevensstromen één JSON-document uit elk bestand gelezen. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Niet-geciteerde kolomnamen

Als **niet-geciteerde kolomnamen** zijn geselecteerd, worden json-kolommen in kaart brengen voor JSON-kolommen die niet zijn omgeven door aanhalingstekens. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Heeft opmerkingen

Selecteer **Opmerkingen heeft** als de JSON-gegevens opmerkingen in de Stijl C of C++ hebben.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Enkel geciteerd

Selecteer **Aanhalingsteken** als de velden en waarden VAN JSON afzonderlijke aanhalingstekens gebruiken in plaats van dubbele aanhalingstekens.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Backslash ontsnapt

Selecteer **Single quoted** if backslashes are used to escape characters in the JSON data.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
