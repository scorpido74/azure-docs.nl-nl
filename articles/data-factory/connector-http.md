---
title: Gegevens uit een HTTP-bron kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit een cloud of on-premises HTTP-bron naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 4a05d955be88f68b3c0db1f4a29b3f6e1155aa0d
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992178"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Gegevens uit een HTTP-eindpunt kopiëren met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-http-connector.md)
> * [Huidige versie](connector-http.md)

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van een HTTP-eindpunt te kopiëren. Het artikel bouwt voort op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), dat een algemeen overzicht van Kopieeractiviteit weergeeft.

Het verschil tussen deze HTTP-connector, de [REST-connector](connector-rest.md) en de [webtafelconnector](connector-web-table.md) zijn:

- **REST-connector** ondersteunt specifiek het kopiëren van gegevens van RESTful API's; 
- **HTTP-connector** is algemeen om gegevens op te halen uit elk HTTP-eindpunt, bijvoorbeeld om bestand te downloaden. Voordat restconnector beschikbaar komt, u de HTTP-connector gebruiken om gegevens te kopiëren van DE RESTful API, die wordt ondersteund, maar minder functioneel in vergelijking met REST-connector.
- **In de webtabelconnector** wordt tabelinhoud uit een HTML-webpagina geëxtraheerd.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze HTTP-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit een HTTP-bron kopiëren naar een ondersteund sinkdataarchief. Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die Activiteit kopiëren als bronnen en sinks ondersteunt.

U deze HTTP-connector gebruiken om:

- Haal gegevens op uit een HTTP/S-eindpunt met behulp van de HTTP **GET-** of **POST-methoden.**
- Gegevens ophalen met behulp van een van de volgende verificaties: **Anoniem,** **Basic**, **Digest,** **Windows**of **ClientCertificate**.
- Kopieer het HTTP-antwoord as-is of ontleed het met behulp van [ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Als u een HTTP-aanvraag voor het ophalen van gegevens wilt testen voordat u de HTTP-connector configureert in Gegevensfabriek, leest u meer over de API-specificatie voor kop- en hoofdvereisten. U tools zoals Postman of een webbrowser gebruiken om te valideren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die u gebruiken om entiteiten in gegevensfabrieken te definiëren die specifiek zijn voor de HTTP-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de http-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet zijn ingesteld op **HttpServer**. | Ja |
| url | De basis-URL naar de webserver. | Ja |
| enableServerCertificateValidation | Geef op of u serverTLS/SSL-certificaatvalidatie moet inschakelen wanneer u verbinding maakt met een HTTP-eindpunt. Als uw HTTPS-server een zelfondertekend certificaat gebruikt, stelt u deze eigenschap in op **false**. | Nee<br /> (de standaardinstelling is **waar)** |
| authenticationType | Hiermee geeft u het verificatietype op. Toegestane waarden zijn **Anoniem,** **Basis,** **Samenvatting,** **Windows**en **ClientCertificate**. <br><br> Bekijk de secties die deze tabel volgen voor meer eigenschappen en JSON-voorbeelden voor deze verificatietypen. | Ja |
| connectVia | De [runtime integratie](concepts-integration-runtime.md) om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

### <a name="using-basic-digest-or-windows-authentication"></a>Basis-, Digest- of Windows-verificatie gebruiken

Stel de eigenschap **authenticationType** in op **Basic,** **Digest**of **Windows**. Geef naast de algemene eigenschappen die in de vorige sectie worden beschreven, de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | De gebruikersnaam die u wilt gebruiken om toegang te krijgen tot het HTTP-eindpunt. | Ja |
| wachtwoord | Het wachtwoord voor de gebruiker (de **waarde userName).** Markeer dit veld als een **SecureString-type** om het veilig op te slaan in Gegevensfabriek. U ook [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |

**Voorbeeld**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
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

### <a name="using-clientcertificate-authentication"></a>ClientCertificate-verificatie gebruiken

Als u ClientCertificate-verificatie wilt gebruiken, stelt u de eigenschap **authenticationType** in op **ClientCertificate**. Geef naast de algemene eigenschappen die in de vorige sectie worden beschreven, de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| embeddedCertData | Base64-gecodeerde certificaatgegevens. | Geef **ingeslotenCertData** of **certThumbprint op.** |
| certThumbprint | De duimafdruk van het certificaat dat is geïnstalleerd op de cert-winkel van uw zelf gehoste Integration Runtime-machine. Geldt alleen wanneer het zelf gehoste type Integratieruntime is opgegeven in de eigenschap **connectVia.** | Geef **ingeslotenCertData** of **certThumbprint op.** |
| wachtwoord | Het wachtwoord dat aan het certificaat is gekoppeld. Markeer dit veld als een **SecureString-type** om het veilig op te slaan in Gegevensfabriek. U ook [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |

Als u **certThumbprint** gebruikt voor verificatie en het certificaat is geïnstalleerd in de persoonlijke winkel van de lokale computer, verleent u leesmachtigingen aan de zelfgehoste Runtime integratie:

1. Open de Microsoft Management Console (MMC). Voeg de module **Certificaten** toe die is gericht op **lokale computer**.
2. **Certificaten** > **persoonlijk**uitvouwen en selecteer **certificaten**.
3. Klik met de rechtermuisknop op het certificaat in de persoonlijke winkel en selecteer **Alle taken** > **Privésleutels beheren**.
3. Voeg op het tabblad **Beveiliging** het gebruikersaccount toe waaronder de Integration Runtime Host Service (DIAHostService) wordt uitgevoerd, met leestoegang tot het certificaat.

**Voorbeeld 1: CertThumbprint gebruiken**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld 2: EmbeddedCertData gebruiken**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

De volgende eigenschappen worden ondersteund `location` voor HTTP onder instellingen in op indeling gebaseerde gegevensset:

| Eigenschap    | Beschrijving                                                  | Vereist |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | De eigenschap `location` type onder in de gegevensset moet worden ingesteld op **HttpServerLocatie**. | Ja      |
| relativenurl | Een relatieve URL naar de bron die de gegevens bevat. De HTTP-connector kopieert gegevens `[URL specified in linked service][relative URL specified in dataset]`uit de gecombineerde URL: .   | Nee       |

> [!NOTE]
> De ondersteunde HTTP-aanvraag payload grootte is ongeveer 500 KB. Als de payload-grootte die u wilt doorgeven aan uw webeindpunt groter is dan 500 KB, u overwegen het laadvermogen in kleinere stukken te batcheren.

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

In deze sectie vindt u een lijst met eigenschappen die de HTTP-bron ondersteunt.

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="http-as-source"></a>HTTP als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden voor `storeSettings` HTTP ondersteund onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **HttpReadSettings**. | Ja      |
| requestMethode            | De HTTP-methode. <br>Toegestane waarden zijn **Get** (default) en **Post**. | Nee       |
| addtionalHeaders         | Aanvullende HTTP-aanvraagkoppen.                             | Nee       |
| requestBody              | De instantie voor het HTTP-verzoek.                               | Nee       |
| httpRequestTime-out           | De time-out (de **TimeSpan-waarde)** voor het HTTP-verzoek om een antwoord te krijgen. Deze waarde is de time-out om een antwoord te krijgen, niet de time-out om antwoordgegevens te lezen. De standaardwaarde is **00:01:40**. | Nee       |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee       |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
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
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund als-is voor achterwaartse compatibiliteit. U wordt voorgesteld om het nieuwe model te gebruiken dat in bovengenoemde secties wordt vermeld die vooruit gaan, en ADF authoring UI is op het produceren van het nieuwe model overgestapt.

### <a name="legacy-dataset-model"></a>Verouderd gegevenssetmodel

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **HttpFile**. | Ja |
| relativenurl | Een relatieve URL naar de bron die de gegevens bevat. Wanneer deze eigenschap niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de gekoppelde servicedefinitie. | Nee |
| requestMethode | De HTTP-methode. Toegestane waarden zijn **Get** (default) en **Post**. | Nee |
| extra Kopteksten | Aanvullende HTTP-aanvraagkoppen. | Nee |
| requestBody | De instantie voor het HTTP-verzoek. | Nee |
| formaat | Als u gegevens wilt ophalen uit het HTTP-eindpunt as-is zonder deze te ontleden en de gegevens vervolgens wilt kopiëren naar een archief op basis van bestanden, slaat u de **indelingssectie** in zowel de definities van de invoer- als de uitvoergegevenslijst over.<br/><br/>Als u de HTTP-antwoordinhoud tijdens het kopiëren wilt ontleden, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**en **ParquetFormat**. Stel **onder opmaak**de eigenschap **type** in op een van deze waarden. Zie [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Tekstformaat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Avro-formaat](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-formaat](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parketformaat](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)voor meer informatie . |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/><br/>Ondersteunde typen: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus: **optimaal** en **snelst.** |Nee |

> [!NOTE]
> De ondersteunde HTTP-aanvraag payload grootte is ongeveer 500 KB. Als de payload-grootte die u wilt doorgeven aan uw webeindpunt groter is dan 500 KB, u overwegen het laadvermogen in kleinere stukken te batcheren.

**Voorbeeld 1: De methode Oppakken gebruiken (standaard)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Voorbeeld 2: De methode Post gebruiken**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Bronmodel voor verouderde kopieeractiviteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron van kopieeractiviteit moet zijn ingesteld op **HttpSource**. | Ja |
| httpRequestTime-out | De time-out (de **TimeSpan-waarde)** voor het HTTP-verzoek om een antwoord te krijgen. Deze waarde is de time-out om een antwoord te krijgen, niet de time-out om antwoordgegevens te lezen. De standaardwaarde is **00:01:40**.  | Nee |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die activiteit kopiëren als bronnen en sinks in Azure Data Factory ondersteunt.
