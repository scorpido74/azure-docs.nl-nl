---
title: Gegevens van DB2 kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van DB2 naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 2c2071e4b2a3daa528c7d01f64e38247b063e6f1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417417"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Gegevens van DB2 kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-onprem-db2-connector.md)
> * [Huidige versie](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een DB2-database te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze DB2-databaseconnector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit db2-database kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Met name deze DB2-connector ondersteunt de volgende IBM DB2-platforms en -versies met Distributed Relational Database Architecture (DRDA) SQL Access Manager (SQLAM) versie 9, 10 en 11:

* IBM DB2 voor z/OS 12.1
* IBM DB2 voor z/OS 11.1
* IBM DB2 voor z/OS 10.1
* IBM DB2 voor i 7.3
* IBM DB2 voor i 7.2
* IBM DB2 voor i 7.1
* IBM DB2 voor LUW 11
* IBM DB2 voor LUW 10.5
* IBM DB2 voor LUW 10.1

>[!TIP]
>DB2-connector is gebouwd op de top van Microsoft OLE DB Provider voor DB2. Als u db2-connectorfouten wilt oplossen, raadpleegt u [foutcodes van gegevensprovider](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwd DB2-stuurprogramma, waardoor u geen stuurprogramma handmatig hoeft te installeren bij het kopiëren van gegevens van DB2.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de DB2-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor DB2-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Db2** | Ja |
| server |Naam van de DB2-server. U het poortnummer opgeven dat volgt op de `server:port`servernaam die is afgebakend door de dubbele punt, bijvoorbeeld. |Ja |
| database |Naam van de DB2-database. |Ja |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met de DB2-database.<br/>Toegestane waarde is: **Basic**. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op om verbinding te maken met de DB2-database. |Ja |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor de gebruikersnaam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja |
| pakketCollectie | Geef onder op waar de benodigde pakketten automatisch worden gemaakt door ADF bij het opvragen van de database. | Nee |
| certificateCommonName | Wanneer u SSL-versleuteling (Secure Sockets Layer) of Transport Layer Security (TLS) gebruikt, moet u een waarde invoeren voor de algemene naam van certificate. | Nee |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

> [!TIP]
> Als u een foutbericht `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`ontvangt waarin staat, wordt een benodigd pakket niet gemaakt voor de gebruiker. AdF probeert standaard een pakket te maken onder verzameling met de naam van de gebruiker die u hebt gebruikt om verbinding te maken met de DB2. Geef de eigenschap pakketverzameling op om aan te geven waar u wilt dat ADF de benodigde pakketten maakt bij het opvragen van de database.

**Voorbeeld:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de DB2-gegevensset.

Als u gegevens van DB2 wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **Db2Table** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` `table` en voor nieuwe werkbelasting. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als deze is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door DB2-bron.

### <a name="db2-as-source"></a>DB2 als bron

Als u gegevens van DB2 wilt kopiëren, worden de volgende eigenschappen ondersteund in de **sectie** bron van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **Db2Source** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als is, terwijl u wordt voorgesteld om de nieuwe in de toekomst te gebruiken.

## <a name="data-type-mapping-for-db2"></a>Gegevenstypetoewijzing voor DB2

Bij het kopiëren van gegevens van DB2 worden de volgende toewijzingen gebruikt van DB2-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| DB2-databasetype | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
| Bigint |Int64 |
| Binair |Byte |
| Blob |Byte |
| Char |Tekenreeks |
| Clob Clob |Tekenreeks |
| Date |Datum/tijd |
| DB2DynArray DB2DynArray |Tekenreeks |
| DbClob DbClob |Tekenreeks |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Drijvend |Double |
| Grafische |Tekenreeks |
| Geheel getal |Int32 |
| Longvarbinary (Longvarbinary) |Byte |
| Longvarchar Longvarchar |Tekenreeks |
| Longvargrafisch |Tekenreeks |
| Numeriek |Decimal |
| Echte |Enkel |
| Smallint |Int16 |
| Time |TimeSpan |
| Tijdstempel |DateTime |
| VarBinary (VarBinary) |Byte |
| Varchar |Tekenreeks |
| VarGrafisch |Tekenreeks |
| Xml |Byte |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
