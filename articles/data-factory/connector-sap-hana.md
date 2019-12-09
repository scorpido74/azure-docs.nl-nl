---
title: Gegevens kopiëren van SAP HANA
description: Informatie over het kopiëren van gegevens van SAP HANA naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 97c277eadbd1b425c50b10d15172c13e17e20eb3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926207"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Gegevens kopiëren van SAP HANA met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-sap-hana-connector.md)
> * [Huidige versie](connector-sap-hana.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een SAP HANA-data base te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

>[!TIP]
>Zie [SAP Data Integration using Azure Data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) with introduction, comparsion en guidance (Engelstalig) voor meer informatie over de algemene ondersteuning van de ADF op SAP Data Integration scenario.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SAP HANA-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit SAP HANA data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks op basis van de Kopieer activiteit.

Deze SAP HANA-connector ondersteunt met name:

- Gegevens kopiëren uit elke versie van SAP HANA data base.
- Gegevens kopiëren van **Hana-informatie modellen** (zoals analytische en berekenings weergaven) en **rij-en kolom tabellen**.
- Kopiëren van gegevens met behulp van **basis** -of **Windows** -verificatie.

> [!TIP]
> Als u gegevens wilt kopiëren **naar** SAP Hana gegevens archief, gebruikt u de algemene ODBC-Connector. Zie [SAP Hana Sink](connector-odbc.md#sap-hana-sink) met details. Houd er rekening mee dat de gekoppelde services voor SAP HANA connector en ODBC-Connector met een ander type dan niet opnieuw kunnen worden gebruikt.

## <a name="prerequisites"></a>Vereisten

Als u deze SAP HANA-connector wilt gebruiken, moet u het volgende doen:

- Stel een zelf-hostende Integration Runtime in. Zie [zelfgehoste Cloudintegratieruntime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.
- Installeer het SAP HANA ODBC-stuur programma op de Integration Runtime computer. U kunt het SAP HANA ODBC-stuurprogramma downloaden via het [SAP Software Download Center](https://support.sap.com/swdc). Zoek met het tref woord **SAP Hana client voor Windows**.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor SAP HANA-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor SAP HANA gekoppelde service:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **SapHana** | Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met de SAP HANA met behulp van **basis verificatie** of **Windows-verificatie**. Raadpleeg de volgende voor beelden.<br>In connection string is server/poort verplicht (de standaard poort is 30015), en de gebruikers naam en het wacht woord zijn verplicht wanneer basis verificatie wordt gebruikt. Raadpleeg [SAP Hana ODBC-verbindings eigenschappen](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) voor aanvullende geavanceerde instellingen<br/>U kunt ook wacht woord in Azure Key Vault plaatsen en de wachtwoord configuratie uit de connection string halen. Raadpleeg de [referenties voor opslaan in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja |
| userName | Geef een gebruikers naam op bij het gebruik van Windows-verificatie. Voorbeeld: `user@domain.com` | Nee |
| wachtwoord | Geef het wacht woord voor het gebruikers account op. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Ja |

**Voor beeld: basis verificatie gebruiken**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: Windows-verificatie gebruiken**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
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

Als u SAP HANA gekoppelde service met de volgende Payload gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

**Voorbeeld:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
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

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die door SAP HANA DataSet worden ondersteund.

De volgende eigenschappen worden ondersteund voor het kopiëren van gegevens uit SAP HANA:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **SapHanaTable** | Ja |
| schema | De naam van het schema in de SAP HANA-data base. | Nee (als 'query' in de activiteitbron is opgegeven) |
| table | De naam van de tabel in de SAP HANA-data base. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die door SAP HANA bron worden ondersteund.

### <a name="sap-hana-as-source"></a>SAP HANA als bron

Als u gegevens wilt kopiëren uit SAP HANA, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **SapHanaSource** | Ja |
| query | Hiermee geeft u de SQL-query voor het lezen van gegevens uit het SAP HANA-exemplaar. | Ja |
| packetSize | Hiermee geeft u de grootte van het netwerk pakket (in kilo bytes) op om gegevens te splitsen in meerdere blokken. Als u grote hoeveel heden gegevens moet kopiëren, kan de grootte van het pakket verhogen in de meeste gevallen de Lees snelheid van SAP HANA. Prestatie testen worden aanbevolen bij het aanpassen van de pakket grootte. | Nee.<br>De standaard waarde is 2048 (2 MB). |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte Kopieer bron gebruikt, wordt deze nog steeds ondersteund als-is. u wordt aangeraden het nieuwe item te gebruiken.

## <a name="data-type-mapping-for-sap-hana"></a>Toewijzing van gegevens type voor SAP HANA

Bij het kopiëren van gegevens uit SAP HANA worden de volgende toewijzingen gebruikt van SAP HANA gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [Schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe copy activity in het schema en de gegevens van een brontype aan de sink toegewezen.

| SAP HANA gegevens type | Data factory tussentijdse gegevenstype |
| ------------------ | ------------------------------ |
| ALPHANUM           | Tekenreeks                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | Tekenreeks                         |
| BLOB               | Byte[]                         |
| BOOL               | byte                           |
| CLOB               | Tekenreeks                         |
| DATE               | Datum/tijd                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | Tekenreeks                         |
| NVARCHAR           | Tekenreeks                         |
| REAL               | Enkelvoudig                         |
| SECONDDATE         | Datum/tijd                       |
| SHORTTEXT          | Tekenreeks                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Tekenreeks                         |
| TIME               | TimeSpan                       |
| TINYINT            | byte                           |
| VARCHAR            | Tekenreeks                         |
| TIMESTAMP          | Datum/tijd                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
