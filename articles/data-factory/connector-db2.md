---
title: Gegevens van DB2 kopiëren met behulp van Azure Data Factory | Microsoft Docs
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
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 16dd40a521a9b0a240574c2f177f896c31508a37
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211704"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Gegevens van DB2 kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-db2-connector.md)
> * [Huidige versie](connector-db2.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een DB2-Data Base te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit de DB2-Data Base kopiëren naar elk ondersteund Sink-gegevens archief. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

In het bijzonder ondersteunt deze DB2-connector de volgende IBM DB2-platforms en-versies met distributed Relationed data base Architecture (DRDA) SQL Access Manager (SQLAM) versie 9, 10 en 11:

* IBM DB2 voor z/OS 12
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
> - DB2 for i (AS400): de hoofd gebruiker kan een verzameling voor de aanmeldings gebruiker maken voordat u de Kopieer activiteit gebruikt. Cmd`create collection <username>`
> - DB2 voor z/O'S of LUW: gebruik een account met hoge bevoegdheden-Power gebruiker of beheerder met pakket instanties en BIND, BINDADD, GRANT EXECUTe TO open bare Permissions: als u de Kopieer activiteit eenmaal wilt uitvoeren, wordt het benodigde pakket automatisch gemaakt tijdens het kopiëren. Daarna kunt u teruggaan naar de normale gebruiker voor de volgende Kopieer uitvoeringen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwd DB2-stuur programma. Daarom hoeft u niet hand matig een stuur programma te installeren bij het kopiëren van gegevens uit DB2.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de DB2-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor gekoppelde DB2-service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **Db2** | Ja |
| server |Naam van de DB2-Server. U kunt het poort nummer opgeven na de server naam gescheiden door een dubbele punt, `server:port`bijvoorbeeld. |Ja |
| database |Naam van de DB2-Data Base. |Ja |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de DB2-Data Base.<br/>Toegestane waarde is: **Basic**. |Ja |
| username |Geef de gebruikers naam op om verbinding te maken met de DB2-Data Base. |Ja |
| password |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
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

Zie het artikel gegevenssets voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Deze sectie bevat een lijst met eigenschappen die door de DB2-gegevensset worden ondersteund.

Als u gegevens wilt kopiëren uit DB2, stelt u de eigenschap type van de gegevensset in op **RelationalTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **RelationalTable** | Ja |
| tableName | De naam van de tabel in de DB2-Data Base. | Nee (als 'query' in de activiteitbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de DB2-bron.

### <a name="db2-as-source"></a>DB2 als bron

Als u gegevens wilt kopiëren uit DB2, stelt u het bron type in de Kopieer activiteit in op **RelationalSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **source** sectie:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op: **RelationalSource** | Ja |
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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Toewijzing van gegevens type voor DB2

Bij het kopiëren van gegevens uit de DB2 worden de volgende toewijzingen gebruikt vanuit DB2-gegevens typen voor het Azure Data Factory van tussenliggende gegevens typen. Zie [Schema en gegevens typt toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe copy activity in het schema en de gegevens van een brontype aan de sink toegewezen.

| DB2-database type | Data factory tussentijdse gegevenstype |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |Tekenreeks |
| Clob |Tekenreeks |
| Date |Datetime |
| DB2DynArray |Tekenreeks |
| DbClob |Tekenreeks |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |Tekenreeks |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Tekenreeks |
| LongVarGraphic |Tekenreeks |
| Numeric |Decimal |
| Werkelijk |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |Tekenreeks |
| VarGraphic |Tekenreeks |
| Xml |Byte[] |


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
