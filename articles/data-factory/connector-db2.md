---
title: Gegevens van DB2 kopiëren met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van DB2 naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
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
ms.openlocfilehash: 22ecac12e049e58e533cdde0078f4a25f6bb2aa6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423824"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Gegevens van DB2 kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-onprem-db2-connector.md)
> * [Huidige versie](connector-db2.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een DB2-Data Base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze DB2 Data Base-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit de DB2-Data Base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

In het bijzonder ondersteunt deze DB2-connector de volgende IBM DB2-platforms en-versies met distributed Relationed data base Architecture (DRDA) SQL Access Manager (SQLAM) versie 9, 10 en 11:

* IBM DB2 voor z/OS 12,1
* IBM DB2 voor z/OS 11,1
* IBM DB2 voor z/OS 10,1
* IBM DB2 voor i 7,3
* IBM DB2 voor i 7,2
* IBM DB2 voor i 7,1
* IBM DB2 voor LUW 11
* IBM DB2 voor LUW 10,5
* IBM DB2 voor LUW 10,1

>[!TIP]
>DB2-connector is gebaseerd op Microsoft OLE DB-provider voor DB2. Raadpleeg de [fout codes van de gegevens provider](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)om problemen met de DB2-connector op te lossen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwd DB2-stuur programma. Daarom hoeft u niet hand matig een stuur programma te installeren bij het kopiëren van gegevens uit DB2.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de DB2-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor gekoppelde DB2-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Db2** | Ja |
| server |Naam van de DB2-Server. U kunt het poort nummer opgeven na de server naam gescheiden door dubbele punt, bijvoorbeeld `server:port`. |Ja |
| enddatabase |Naam van de DB2-Data Base. |Ja |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de DB2-Data Base.<br/>Toegestane waarde is: **Basic**. |Ja |
| gebruikersnaam |Geef de gebruikers naam op om verbinding te maken met de DB2-Data Base. |Ja |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| packageCollection | Geef op onder waar de benodigde pakketten automatisch worden gemaakt door ADF bij het uitvoeren van query's op de data base. | Nee |
| certificateCommonName | Wanneer u Secure Sockets Layer (SSL) of Transport Layer Security (TLS)-code ring gebruikt, moet u een waarde opgeven voor de algemene naam van het certificaat. | Nee |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

> [!TIP]
> Als er een fout bericht wordt weer gegeven met de melding dat `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`, is de reden dat het benodigde pakket niet is gemaakt voor de gebruiker. Standaard probeert ADF een pakket te maken onder de verzameling met de naam van de gebruiker die u hebt gebruikt voor verbinding met de DB2. Geef de verzamelings eigenschap van het pakket op om aan te geven onder waar u de benodigde pakketten wilt maken bij het uitvoeren van een query op de data base.

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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de DB2-gegevensset worden ondersteund.

Als u gegevens wilt kopiëren uit DB2, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **Db2Table** | Ja |
| schema | De naam van het schema. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| table | Naam van de tabel. |Nee (als 'query' in de activiteitbron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table` voor nieuwe workloads. | Nee (als 'query' in de activiteitbron is opgegeven) |

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

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de DB2-bron.

### <a name="db2-as-source"></a>DB2 als bron

Als u gegevens wilt kopiëren uit DB2, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **Db2Source** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nee (als de 'tableName' in de gegevensset is opgegeven) |

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

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="data-type-mapping-for-db2"></a>Toewijzing van gegevens type voor DB2

Bij het kopiëren van gegevens uit de DB2 worden de volgende toewijzingen gebruikt vanuit DB2-gegevens typen voor het Azure Data Factory van tussenliggende gegevens typen. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| DB2-database type | Data factory tussentijdse gegevenstype |
|:--- |:--- |
| BigInt |Int64 |
| Binair bestand |Byte[] |
| Blob |Byte[] |
| char |Tekenreeks |
| Clob |Tekenreeks |
| Date |Datum en tijd |
| DB2DynArray |Tekenreeks |
| DbClob |Tekenreeks |
| decimaal |decimaal |
| DecimalFloat |decimaal |
| Double-waarde |Double-waarde |
| Float |Double-waarde |
| Graphic |Tekenreeks |
| Geheel getal |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Tekenreeks |
| LongVarGraphic |Tekenreeks |
| Numeric |decimaal |
| Realistische |Enkelvoudig |
| SmallInt |Int16 |
| Time |TimeSpan |
| Tijdstempel |DateTime |
| VarBinary |Byte[] |
| VarChar |Tekenreeks |
| VarGraphic |Tekenreeks |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
