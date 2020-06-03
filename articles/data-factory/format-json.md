---
title: JSON-indeling in Azure Data Factory
description: In dit onderwerp wordt beschreven hoe u kunt omgaan met de JSON-indeling in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: jingwang
ms.openlocfilehash: c488c57f8c755bfc062dc81a242fbfbb605406e0
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298564"
---
# <a name="json-format-in-azure-data-factory"></a>JSON-indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Volg dit artikel als u **de json-bestanden wilt parseren of de gegevens naar de JSON-indeling wilt schrijven**. 

JSON-indeling wordt ondersteund voor de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de JSON-gegevensset worden ondersteund.

| Eigenschap         | Beschrijving                                                  | Vereist |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | De eigenschap type van de gegevensset moet worden ingesteld op **JSON**. | Yes      |
| location         | Locatie-instellingen van bestand (en). Elke connector op basis van bestanden heeft een eigen locatie type en ondersteunde eigenschappen onder `location` . **Zie de sectie Details in connector artikel-> eigenschappen van gegevensset**. | Yes      |
| encodingName     | Het coderings type dat wordt gebruikt voor het lezen/schrijven van test bestanden. <br>Toegestane waarden zijn als volgt: ' UTF-8 ', ' UTF-16 ', ' UTF-16BE ', ' UTF-32 ', ' UTF-32BE ', ' VS-ASCII ', ' UTF-7 ', ' BIG5 ', ' EUC-JP ', ' EUC-KR ', ' GB2312 "," GB18030 "," JOHAB "," SHIFT-JIS "," CP875 "," CP866 "," IBM00858 "," IBM037 "," IBM273 "," IBM437 "," IBM500 "," IBM737 "," IBM775 "," IBM850 "," IBM852 "," IBM855 "," IBM857 "," IBM860 "," IBM861 ', ' IBM863 ', ' IBM864 ', ' IBM865 ', ' IBM869 ', ' IBM870 ', ' IBM01140 ', ' IBM01141 ', ' IBM01142 ', ' IBM01143 ', ' IBM01144 ', IBM01145 "," IBM01146 "," IBM01147 "," IBM01148 "," IBM01149 "," ISO-2022-JP "," ISO-2022-KR "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 " , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| No       |
| compressie | Groep eigenschappen voor het configureren van bestands compressie. Configureer deze sectie als u compressie/decompressie wilt uitvoeren tijdens de uitvoering van de activiteit. | No |
| type | De compressie-codec die wordt gebruikt voor het lezen/schrijven van JSON-bestanden. <br>Toegestane waarden zijn **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **Snappy**of **LZ4**. te gebruiken bij het opslaan van het bestand. De standaard waarde is niet gecomprimeerd.<br>**Houd er rekening mee** dat het kopiëren van gegevens op dit moment geen ondersteuning biedt voor ' snappy ' & ' LZ4 ' en dat de toewijzing van data stroom niet wordt ondersteund ' ZipDeflate '.<br>**Opmerking** wanneer u de Kopieer activiteit gebruikt om **ZipDeflate** -bestand (en) te decomprimeren en te schrijven naar Sink-gegevens archief op basis van een bestand, worden standaard bestanden uitgepakt naar de map:. `<path specified in dataset>/<folder named as source zip file>/` Gebruik de `preserveZipFileNameAsFolder` bron van de [Kopieer activiteit](#json-as-source) om te bepalen of de zip-bestands naam moet worden behouden als mapstructuur. | Nee.  |
| niveau | De compressie ratio. <br>Toegestane waarden zijn **optimaal** of **snelst**.<br>- **Snelst:** De compressie bewerking moet zo snel mogelijk worden voltooid, zelfs als het resulterende bestand niet optimaal is gecomprimeerd.<br>- **Optimaal**: de compressie bewerking moet optimaal worden gecomprimeerd, zelfs als het volt ooien van de bewerking langer duurt. Zie het onderwerp [compressie niveau](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) voor meer informatie. | No       |

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
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de JSON-bron en Sink.

### <a name="json-as-source"></a>JSON als bron

De volgende eigenschappen worden ondersteund in de sectie *** \* bron \* *** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **JSONSource**. | Yes      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor **JSON-Lees instellingen** . | No       |
| storeSettings | Een groep eigenschappen voor het lezen van gegevens uit een gegevens archief. Elke connector op basis van een bestand heeft zijn eigen ondersteunde Lees instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | No       |

Ondersteunde **instellingen voor json lezen** onder `formatSettings` :

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Het type formatSettings moet zijn ingesteld op **JsonReadSettings**. | Yes      |
| compressionProperties | Een groep eigenschappen voor het decomprimeren van gegevens voor een bepaalde compressie-codec. | No       |
| preserveZipFileNameAsFolder<br>(*onder `compressionProperties` *) | Van toepassing wanneer de invoer-gegevensset is geconfigureerd met **ZipDeflate** -compressie. Hiermee wordt aangegeven of de naam van het bron-zip-bestand tijdens het kopiëren moet worden bewaard als mapstructuur. Als deze eigenschap is ingesteld op True (standaard) Data Factory worden uitgepakte bestanden naar; genoteerd `<path specified in dataset>/<folder named as source zip file>/` . Als u deze instelt op ONWAAR Data Factory, worden uitgepakte bestanden rechtstreeks naar opgeslagen `<path specified in dataset>` .  | No |

### <a name="json-as-sink"></a>JSON als Sink

De volgende eigenschappen worden ondersteund in het gedeelte *** \* sink \* *** van de Kopieer activiteit.

| Eigenschap      | Beschrijving                                                  | Vereist |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **JSONSink**. | Yes      |
| formatSettings | Een groep eigenschappen. Raadpleeg de onderstaande tabel voor **JSON-schrijf instellingen** . | No       |
| storeSettings | Een groep eigenschappen voor het schrijven van gegevens naar een gegevens archief. Elke connector op basis van bestanden heeft eigen ondersteunde schrijf instellingen onder `storeSettings` . **Zie de sectie Details in connector artikel-> eigenschappen van de Kopieer activiteit**. | No       |

Ondersteunde **instellingen voor json-schrijf bewerkingen** onder `formatSettings` :

| Eigenschap      | Beschrijving                                                  | Vereist                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Het type formatSettings moet zijn ingesteld op **JsonWriteSettings**. | Yes                                                   |
| filePattern |Hiermee geeft u het patroon aan van gegevens die zijn opgeslagen in elk JSON-bestand. Toegestane waarden zijn: **setOfObjects** (JSON Lines) en **arrayOfObjects**. De **standaardwaarde** is **setOfObjects**. Zie het gedeelte [JSON-bestandpatronen](#json-file-patterns) voor meer informatie over deze patronen. |No |

### <a name="json-file-patterns"></a>JSON-bestandpatronen

Bij het kopiëren van gegevens uit JSON-bestanden kan de Kopieer activiteit de volgende patronen van JSON-bestanden automatisch detecteren en parseren. Wanneer u gegevens naar JSON-bestanden schrijft, kunt u het bestands patroon configureren op Sink voor kopieer activiteit.

- **Type I: setOfObjects**

    Elk bestand bevat één object, JSON-lijnen of aaneengeschakelde objecten.

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

    * **JSON-lijnen (standaard voor Sink)**

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

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

JSON-bestands typen kunnen worden gebruikt als Sink en als bron in de toewijzing van gegevens stroom.

### <a name="creating-json-structures-in-a-derived-column"></a>JSON-structuren maken in een afgeleide kolom

U kunt een complexe kolom toevoegen aan uw gegevens stroom via de afgeleide column Expression Builder. Voeg in de afgeleide kolom transformatie een nieuwe kolom toe en open de opbouw functie voor expressies door te klikken op het blauwe vak. Als u een kolom complex wilt maken, kunt u de JSON-structuur hand matig invoeren of de UX gebruiken om subkoloms interactief toe te voegen.

#### <a name="using-the-expression-builder-ux"></a>De Expression Builder UX gebruiken

Beweeg de muis aanwijzer over een kolom in het deel venster uitvoer schema en klik op het plus pictogram. Selecteer **subkolom toevoegen** om de kolom een complex type te maken.

![Subkolom toevoegen](media/data-flow/addsubcolumn.png "Subkolom toevoegen")

U kunt op dezelfde manier extra kolommen en subkolomlen toevoegen. Voor elk niet-complex veld kan een expressie in de expressie-editor aan de rechter kant worden toegevoegd.

![Complexe kolom](media/data-flow/complexcolumn.png "Complexe kolom")

#### <a name="entering-the-json-structure-manually"></a>De JSON-structuur hand matig invoeren

Als u hand matig een JSON-structuur wilt toevoegen, voegt u een nieuwe kolom toe en voert u de expressie in de editor in. De expressie volgt de volgende algemene notatie:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Als deze expressie is ingevoerd voor een kolom met de naam ' complexColumn ', wordt deze als de volgende JSON naar de Sink geschreven:

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Voor beeld hand matig script voor de volledige hiërarchische definitie
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

### <a name="source-format-options"></a>Opties voor bron indeling

U kunt met behulp van een JSON-gegevensset als bron in uw gegevens stroom vijf extra instellingen instellen. Deze instellingen kunt u vinden onder de accordeon van de **JSON-instellingen** op het tabblad **bron opties** .  

![JSON-instellingen](media/data-flow/json-settings.png "JSON-instellingen")

#### <a name="default"></a>Standaard

JSON-gegevens worden standaard in de volgende indeling gelezen.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Eén document

Als er **één document** is geselecteerd, stroomt de toewijzing van gegevens een JSON-document uit elk bestand. 

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
> [!NOTE]
> Als gegevens stromen een fout gegenereerd met de melding ' corrupt_record ' bij het weer geven van een voor beeld van de JSON-gegevens, bevat uw gegevens waarschijnlijk één document in uw JSON-bestand. Bij het instellen van ' Eén document ' moet deze fout worden gewist.

#### <a name="unquoted-column-names"></a>Niet-geciteerde kolom namen

Als **kolom namen die geen aanhalings tekens** zijn geselecteerd, worden door het toewijzen van gegevens stromen JSON-kolommen gelezen die niet tussen aanhalings tekens staan. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Bevat opmerkingen

Selecteer **opmerkingen bevat** als de JSON-gegevens C of C++-stijl opmerkingen hebben.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Enkel aanhalings teken

**Eén aanhalings** teken selecteren als de JSON-velden en-waarden enkele aanhalings tekens gebruiken in plaats van dubbele aanhalings tekens.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Back slash met escape-teken

**Eén aanhalings** teken selecteren als backslashes worden gebruikt om tekens in de JSON-gegevens te escapepen.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)
