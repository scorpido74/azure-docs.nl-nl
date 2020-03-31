---
title: Gegevens uit Netezza kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Netezza naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c51469997af23be7a5e1b88677ecadb37e10ac64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244535"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Gegevens uit Netezza kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Netezza te kopiëren. Het artikel bouwt voort op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), dat een algemeen overzicht van Kopieeractiviteit weergeeft.

>[!TIP]
>Voor het scenario voor gegevensmigratie van Netezza naar Azure, leest u meer van [Azure Data Factory gebruiken om gegevens van de on-premises Netezza-server naar Azure te migreren.](data-migration-guidance-netezza-azure-sqldw.md)

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Netezza-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)


U gegevens van Netezza kopiëren naar elk ondersteund sink data store. Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die Activiteit kopiëren als bronnen en sinks ondersteunt.

Netezza-connector ondersteunt parallel kopiëren vanaf de bron. Zie de [parallel kopie van Netezza](#parallel-copy-from-netezza) sectie voor meer informatie.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken. U hoeft geen stuurprogramma handmatig te installeren om deze connector te gebruiken.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

U een pijplijn maken die een kopieeractiviteit gebruikt met behulp van de .NET SDK, de Python SDK, Azure PowerShell, de REST API of een Azure Resource Manager-sjabloon. Zie de [zelfstudie Activiteit kopiëren](quickstart-create-data-factory-dot-net.md) voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.

In de volgende secties vindt u informatie over eigenschappen die u gebruiken om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Netezza-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de netezza gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet worden ingesteld op **Netezza**. | Ja |
| Connectionstring | Een ODBC-verbindingstekenreeks om verbinding te maken met Netezza. <br/>U ook wachtwoord in Azure `pwd` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg de volgende voorbeelden en [store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. | Ja |
| connectVia | De [runtime integratie](concepts-integration-runtime.md) om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

Een typische verbindingstekenreeks is `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. In de volgende tabel worden meer eigenschappen beschreven die u instellen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| SecurityLevel | Het beveiligingsniveau (SSL/TLS) dat het stuurprogramma gebruikt voor de verbinding met het gegevensarchief. Bijvoorbeeld: `SecurityLevel=preferredSecured`. Ondersteunde waarden zijn:<br/>- **Alleen onbeveiligd** **(alleenOnbeveiligd):** Het stuurprogramma maakt geen gebruik van SSL.<br/>- **Voorkeur onbeveiligd (voorkeurOnbeveiligd) (standaard)**: Als de server een keuze biedt, gebruikt het stuurprogramma geen SSL. <br/>- **Preferred secured (preferredSecured)**: Als de server een keuze biedt, gebruikt het stuurprogramma SSL. <br/>- **Alleen beveiligd (alleenBeveiligd)**: Het stuurprogramma maakt geen verbinding tenzij er een SSL-verbinding beschikbaar is. | Nee |
| CaCertFile CaCertFile | Het volledige pad naar het SSL-certificaat dat door de server wordt gebruikt. Voorbeeld: `CaCertFile=<cert path>;`| Ja, als SSL is ingeschakeld |

**Voorbeeld**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: wachtwoord opslaan in Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

In deze sectie vindt u een lijst met eigenschappen die de Netezza-gegevensset ondersteunt.

Zie [Gegevenssets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets.

Als u gegevens uit Netezza wilt kopiëren, stelt u de **eigenschap type** van de gegevensset in op **NetezzaTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **NetezzaTable** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` `table` en voor nieuwe werkbelasting. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

In deze sectie vindt u een lijst met eigenschappen die de Netezza-bron ondersteunt.

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

### <a name="netezza-as-source"></a>Netezza als bron

>[!TIP]
>Als u gegevens van Netezza efficiënt wilt laden door gegevenspartitionering te gebruiken, leest u meer over parallelle kopie van de sectie [Netezza.](#parallel-copy-from-netezza)

Als u gegevens uit Netezza wilt kopiëren, stelt u het **brontype** in Activiteit kopiëren in **op NetezzaSource**. De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron Kopieeractiviteit moet worden ingesteld op **NetezzaSource**. | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Voorbeeld: `"SELECT * FROM MyTable"` | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |
| partitieOpties | Hiermee geeft u de opties voor gegevenspartitionering op die worden gebruikt om gegevens uit Netezza te laden. <br>Waarden toestaan zijn: **Geen** (standaard), **DataSlice**en **DynamicRange**.<br>Wanneer een partitieoptie is ingeschakeld (dat wil zeggen), `None`wordt de mate van parallellisme om [`parallelCopies`](copy-activity-performance.md#parallel-copy) tegelijkertijd gegevens uit een Netezza-database te laden, beheerd door de kopieeractiviteit in te stellen. | Nee |
| partitieInstellingen | Geef de groep van de instellingen voor gegevenspartitionering op. <br>Toepassen wanneer de partitieoptie niet `None`is . | Nee |
| partitionColumnName | Geef de naam op van de bronkolom **in het gehele getaltype** die wordt gebruikt door bereikverdeling voor parallelle kopie. Als dit niet is opgegeven, wordt de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als partitiekolom. <br>Toepassen wanneer de `DynamicRange`partitieoptie is . Als u een query gebruikt om `?AdfRangePartitionColumnName` de brongegevens op te halen, haakt u de WHERE-clausule aan. Zie voorbeeld in Parallelle kopie van de sectie [Netezza.](#parallel-copy-from-netezza) | Nee |
| partitieUpperBound | De maximale waarde van de partitiekolom om gegevens uit te kopiëren. <br>Toepassen wanneer partitieoptie is `DynamicRange`. Als u query's gebruikt `?AdfRangePartitionUpbound` om brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallel van Netezza.](#parallel-copy-from-netezza) | Nee |
| partitionLowerBound | De minimumwaarde van de partitiekolom om gegevens uit te kopiëren. <br>Toepassen wanneer de `DynamicRange`partitieoptie is . Als u een query gebruikt om `?AdfRangePartitionLowbound` de brongegevens op te halen, haakt u de WHERE-component aan. Zie bijvoorbeeld de [sectie Parallel van Netezza.](#parallel-copy-from-netezza) | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Parallelle kopie van Netezza

De Data Factory Netezza-connector biedt ingebouwde gegevenspartitionering om gegevens van Netezza parallel te kopiëren. U opties voor gegevenspartitionering vinden in de **brontabel** van de kopieeractiviteit.

![Schermafbeelding van partitieopties](./media/connector-netezza/connector-netezza-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op uw Netezza-bron om gegevens te laden op partities. De parallelle graad wordt [`parallelCopies`](copy-activity-performance.md#parallel-copy) gecontroleerd door de instelling voor de kopieeractiviteit. Als u bijvoorbeeld `parallelCopies` op vier instelt, genereert en voert Data Factory tegelijkertijd vier query's uit op basis van de opgegeven partitieoptie en -instellingen en haalt elke query een gedeelte gegevens op uit uw Netezza-database.

U wordt voorgesteld om parallelle kopiëren met gegevenspartitionering in te schakelen, vooral wanneer u grote hoeveelheden gegevens uit uw Netezza-database laadt. De volgende zijn voorgestelde configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar het gegevensarchief in bestanden wordt aanbevolen om naar een map te schrijven als meerdere bestanden (geef alleen de naam van de map op), in welk geval de prestaties beter zijn dan naar één bestand te schrijven.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige lading van grote tafel.                                   | **Partitieoptie**: Gegevenssegment. <br><br/>Tijdens de uitvoering partities van Data Factory de gegevens automatisch op basis [van netezza's ingebouwde gegevenssegmenten](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)en kopieert gegevens door partities. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query.                 | **Partitieoptie**: Gegevenssegment.<br>**Query** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`: .<br>Tijdens de uitvoering `?AdfPartitionCount` vervangt Data Factory (met parallelle `?AdfDataSliceCondition` kopienummer ingesteld op kopieeractiviteit) en met de logica van de gegevenssegmentpartitie en verzendt naar Netezza. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query, met een gehele kolom met gelijkmatig verdeelde waarde voor bereikverdeling. | **Partitieopties**: Dynamische bereikpartitie.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partitiekolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U partitioneren tegen de kolom met integer gegevenstype.<br>**Hoofdgrens** en **partitie ondergrens**: Geef op als u wilt filteren op de partitiekolom om alleen gegevens op te halen tussen het onderste en bovenste bereik.<br><br>Tijdens de uitvoering `?AdfRangePartitionColumnName`vervangt `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory , en met de werkelijke kolomnaam en waardebereiken voor elke partitie, en verzendt naar Netezza. <br>Als uw partitiekolom 'ID' bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met parallelle kopie ingesteld als 4, haalt Data Factory gegevens op met 4 partities. Hun ID's liggen tussen [1,20], [21, 40], [41, 60] en [61, 80], respectievelijk. |

**Voorbeeld: query met gegevenssegmentpartitie**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Voorbeeld: query met dynamische bereikpartitie**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die activiteit kopiëren als bronnen en sinks in Azure Data Factory ondersteunt.
