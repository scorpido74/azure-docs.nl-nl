---
title: Gegevens van en naar de SFTP-server kopiëren
description: Meer informatie over het kopiëren van gegevens van en naar de SFTP-server met Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: 06428d4a9c4a4178212d16d42b8b3adffb5c9718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250292"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Gegevens van en naar de SFTP-server kopiëren met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-sftp-connector.md)
> * [Huidige versie](connector-sftp.md)

In dit artikel wordt beschreven hoe u gegevens van en naar de SFTP-server kopiëren. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SFTP-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit Metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)

Met name deze SFTP-connector ondersteunt:

- Bestanden kopiëren van/naar SFTP met **basis-** of **SshPublicKey-verificatie.**
- Bestanden kopiëren als is of ontleedt/genereert bestanden met de [ondersteunde bestandsindelingen en compressiecodecs.](supported-file-formats-and-compression-codecs.md)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek die specifiek zijn voor SFTP te definiëren.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor SFTP-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Sftp**. |Ja |
| host | Naam of IP-adres van de SFTP-server. |Ja |
| poort | Poort waarop de SFTP-server luistert.<br/>Toegestane waarden zijn: geheel getal, standaardwaarde is **22**. |Nee |
| skipHostKeyValidation | Geef op of u de validatie van hostsleutels wilt overslaan.<br/>Toegestane waarden zijn: **waar,** **onwaar** (standaard).  | Nee |
| hostKeyFingerprint | Geef de vingerafdruk van de hosttoets op. | Ja als de "skipHostKeyValidation" is ingesteld op false.  |
| authenticationType | Geef verificatietype op.<br/>Toegestane waarden zijn: **Basic**, **SshPublicKey**. Raadpleeg het [gebruik van basisverificatie](#using-basic-authentication) [en het gebruik van ssh-verificatiesecties voor openbare sleutels](#using-ssh-public-key-authentication) op respectievelijk meer eigenschappen en JSON-voorbeelden. |Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

### <a name="using-basic-authentication"></a>Basisverificatie gebruiken

Als u basisverificatie wilt gebruiken, stelt u de eigenschap 'authenticationType' in op **Basic**en geeft u de volgende eigenschappen op naast de algemene Eigenschappen van de SFTP-connector die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | Gebruiker die toegang heeft tot de SFTP-server. |Ja |
| wachtwoord | Wachtwoord voor de gebruiker (userName). Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |

**Voorbeeld:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>Verificatie van SSH-public key gebruiken

Als u SSH-verificatie met openbare sleutels wilt gebruiken, stelt u de eigenschap 'authenticationType' in als **SshPublicKey**en geeft u de volgende eigenschappen op naast de algemene SFTP-connector die in de laatste sectie zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | Gebruiker die toegang heeft tot de SFTP-server |Ja |
| privateKeyPath | Geef het absolute pad op naar het privésleutelbestand waarToe Integratie Runtime toegang heeft. Geldt alleen wanneer zelf gehost type Integratieruntime is opgegeven in 'connectVia'. | Geef de `privateKeyPath` `privateKeyContent`or op .  |
| privateKeyContent | Base64 gecodeerde SSH private key content. SSH private key moet OpenSSH-formaat zijn. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Geef de `privateKeyPath` `privateKeyContent`or op . |
| Passphrase | Geef de wachtwoordzin/het wachtwoord op om de privésleutel te decoderen als het sleutelbestand wordt beschermd door een wachtwoordzin. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja als het privésleutelbestand wordt beschermd door een wachtwoordzin. |

> [!NOTE]
> SFTP-connector ondersteunt RSA/DSA OpenSSH-toets. Zorg ervoor dat de inhoud van uw sleutelbestand begint met '-----BEGIN [RSA/DSA] PRIVATE KEY-----". Als het bestand met de privésleutel een ppk-formaatbestand is, gebruikt u het gereedschap Putty om te converteren van .ppk naar OpenSSH-indeling. 

**Voorbeeld 1: SshPublicKey-verificatie met behulp van private key filePath**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld 2: SshPublicKey-verificatie met behulp van inhoud met privésleutels**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `location` voor SFTP onder instellingen in op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap `location` type onder in de gegevensset moet worden ingesteld op **SftpLocatie**. | Ja      |
| folderPath | Het pad naar de map. Als u wildcard wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |
| fileName   | De bestandsnaam onder de opgegeven mapPath. Als u wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de SFTP-bron.

### <a name="sftp-as-source"></a>SFTP als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `storeSettings` voor SFTP onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **SftpReadSettings**. | Ja                                           |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de gootsteen een op bestanden gebaseerd e-bestand is, een lege map of submap niet wordt gekopieerd of gemaakt aan de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                            |
| wildcardFolderPath       | Het mappad met jokertekens om bronmappen te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De bestandsnaam met jokertekens onder de opgegeven mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft.  Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als deze niet is opgegeven in de gegevensset |
| gewijzigdDatumtimeStart    | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd. | Nee                                            |
| gewijzigdDatumtimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee                                            |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sftp-as-sink"></a>SFTP als gootsteen

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `storeSettings` voor SFTP onder instellingen in op indeling gebaseerde copy sink:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **SftpWriteSettings**. | Ja      |
| copyBehavior             | Hiermee definieert u het kopieergedrag wanneer de bron bestanden is uit een gegevensarchief op basis van bestanden.<br/><br/>Toegestane waarden zijn:<br/><b>- BehoudenHiërarchie (standaard)</b>: Hiermee behoudt u de bestandshiërarchie in de doelmap. Het relatieve pad van het bronbestand naar de bronmap is identiek aan het relatieve pad van het doelbestand naar de doelmap.<br/><b>- FlattenHierarchy:</b>Alle bestanden uit de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>- MergeFiles</b>: Hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestandsnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders is het een automatisch gegenereerde bestandsnaam. | Nee       |
| maxConcurrentVerbindingen | Het aantal verbindingen om gelijktijdig verbinding te maken met het gegevensarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee       |
| gebruik tempfilerename | Geef aan of u moet uploaden naar tijdelijke bestanden en de naam wijzigt, of direct naar de doelmap/bestandslocatie wilt schrijven. Standaard, ADF eerst schrijven naar tijdelijke bestand (s) dan doen bestandshernoemen bij het uploaden voltooiing, om te voorkomen dat conflict schrijven resulteert in beschadigde bestand als u andere proces schrijven naar hetzelfde bestand, en 2) ervoor zorgen dat de oorspronkelijke versie van het bestand bestaat tijdens hele overdracht. Als uw SFTP-server geen hernoemingsbewerking ondersteunt, schakelt u deze optie uit en controleert u of u geen gelijktijdige schrijfbewerking naar het doelbestand hebt. Zie tip voor het oplossen van problemen onder deze tabel. | Nee. De standaardwaarde is waar. |
| operationTime-out | De wachttijd voordat elk schrijfverzoek naar SFTP-server een time-out heeft. De standaardwaarde is 60 min (01:00:00).|Nee |

>[!TIP]
>Als u een fout indenaam "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" of "SftpOperationFail" bij het schrijven van gegevens in SFTP, en de SFTP-gebruiker die u gebruikt wel`useTempFileRename`de juiste toestemming heeft, controleert u of uw SFTP-serverondersteuningsbestands de naam van de bewerking wijzigt - zo niet, schakelt u de optie Uploaden met temp-bestand uit en probeert u het opnieuw. Meer informatie over deze eigenschap van bovenstaande tabel. Als u zelf gehoste integratieruntime gebruikt voor kopiëren, moet u versie 4.6 of hoger gebruiken.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voorbeelden van mappen en bestandsfilters

In deze sectie wordt het resulterende gedrag van het mappad en de bestandsnaam met wildcardfilters beschreven.

| folderPath | fileName | Recursieve | Structuur en filterresultaat voor bronmappen **(vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (leeg, standaard gebruiken) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (leeg, standaard gebruiken) | waar | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | waar | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Activiteitseigenschappen getMetadata

Voor meer informatie over de eigenschappen, controleert [GetMetadata-activiteit](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Activiteitseigenschappen verwijderen

Voor meer informatie over de eigenschappen schakelt u [Activiteit verwijderen](delete-activity.md)

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund als-is voor achterwaartse compatibiliteit. U wordt voorgesteld om het nieuwe model te gebruiken dat in bovengenoemde secties wordt vermeld die vooruit gaan, en ADF authoring UI is op het produceren van het nieuwe model overgestapt.

### <a name="legacy-dataset-model"></a>Verouderd gegevenssetmodel

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **FileShare** |Ja |
| folderPath | Pad naar de map. Wildcard filter wordt ondersteund, toegestane wildcards zijn: `*` (komt `?` overeen met nul of meer tekens) en (komt overeen met nul of een enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke bestandsnaam wildcard of deze ontsnapping char binnen heeft. <br/><br/>Voorbeelden: rootmap/submap/, zie meer voorbeelden in [voorbeelden van mappen en bestandsfilters](#folder-and-file-filter-examples). |Ja |
| fileName |  **Naam- of wildcardfilter** voor het bestand(en) onder de opgegeven 'mapPath'. Als u geen waarde voor deze eigenschap opgeeft, verwijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor filter zijn toegestane jokertekens: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of één teken).<br/>- Voorbeeld 1:`"fileName": "*.csv"`<br/>- Voorbeeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. |Nee |
| gewijzigdDatumtimeStart | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, zodat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| gewijzigdDatumtimeEnd | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, zodat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| formaat | Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), Avro [Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **Optimaal** en **snelste**. |Nee |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **alleen mapPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam.**<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam** met wildcardfilter.

>[!NOTE]
>Als u de eigenschap "fileFilter" gebruikt voor bestandsfilter, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe filtermogelijkheid te gebruiken die wordt toegevoegd aan "fileName" in de toekomst.

**Voorbeeld:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Bronmodel voor verouderde kopieeractiviteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **FileSystemSource** |Ja |
| Recursieve | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Opmerking wanneer recursieve is ingesteld op true en sink is file-based store, lege map / sub-map zal niet worden gekopieerd / gemaakt op gootsteen.<br/>Toegestane waarden zijn: **true** (default), **false** | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
