---
title: Gegevens van DB2 kopiëren met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van DB2 naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: e72e6c112913d646b6dc1479a9b80acc6d4ec7b1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280758"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Gegevens van DB2 kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-db2-connector.md)
> * [Huidige versie](connector-db2.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een DB2-Data Base te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze DB2 Data Base-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit de DB2-Data Base kopiëren naar elk ondersteund Sink-gegevens archief. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

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

> [!TIP]
> Als u een fout bericht ontvangt met de melding dat het pakket dat overeenkomt met een aanvraag voor het uitvoeren van een SQL-instructie, niet is gevonden. SQLSTATE = 51002 SQLCODE =-805. de reden hiervoor is dat er geen vereist pakket is gemaakt voor een normale gebruiker van dit besturings systeem. Volg deze instructies op basis van het type DB2-Server:
> - DB2 for i (AS400): de hoofd gebruiker kan een verzameling voor de aanmeldings gebruiker maken voordat u de Kopieer activiteit gebruikt. Opdracht: `create collection <username>`
> - DB2 voor z/O'S of LUW: gebruik een account met hoge bevoegdheden-Power gebruiker of beheerder met pakket instanties en BIND, BINDADD, GRANT EXECUTe TO open bare Permissions: als u de Kopieer activiteit eenmaal wilt uitvoeren, wordt het benodigde pakket automatisch gemaakt tijdens het kopiëren. Daarna kunt u teruggaan naar de normale gebruiker voor de volgende Kopieer uitvoeringen.

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
| database |Naam van de DB2-Data Base. |Ja |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de DB2-Data Base.<br/>Toegestane waarde is: **Basic**. |Ja |
| gebruikersnaam |Geef de gebruikers naam op om verbinding te maken met de DB2-Data Base. |Ja |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory, of [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| packageCollection | Opgeven wanneer de benodigde pakketten automatisch worden gemaakt door ADF bij het uitvoeren van query's op de data base | Nee |
| certificateCommonName | Wanneer u Secure Sockets Layer (SSL) of Transport Layer Security (TLS)-code ring gebruikt, moet u een waarde opgeven voor de algemene naam van het certificaat. | Nee |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als niet is opgegeven, wordt de standaard Azure Integration Runtime. |Nee |

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die door de DB2-gegevensset worden ondersteund.

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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de DB2-bron.

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

Bij het kopiëren van gegevens uit de DB2 worden de volgende toewijzingen gebruikt vanuit DB2-gegevens typen voor het Azure Data Factory van tussenliggende gegevens typen. Zie [Schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe copy activity in het schema en de gegevens van een brontype aan de sink toegewezen.

| DB2-database type | Data factory tussentijdse gegevenstype |
|:--- |:--- |
| BigInt |Int64 |
| Binair bestand |Byte[] |
| Blob |Byte[] |
| CHAR |Tekenreeks |
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
| Real |Enkelvoudig |
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
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
