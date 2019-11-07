---
title: Gegevens kopiëren naar of van Azure Cosmos DB (SQL-API) met behulp van Data Factory
description: Informatie over het kopiëren van gegevens van ondersteunde brongegevens archieven naar of van Azure Cosmos DB (SQL-API) naar ondersteunde Sink-archieven met behulp van Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 74bec61cbb69b17afddeb5abc1df16d8c1140234
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681305"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Cosmos DB (SQL-API) met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-documentdb-connector.md)
> * [Huidige versie](connector-azure-cosmos-db.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Cosmos DB (SQL-API). Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

>[!NOTE]
>Deze connector ondersteunt alleen het kopiëren van gegevens naar/van Cosmos DB SQL-API. Raadpleeg voor MongoDB-API [de connector voor de API van Azure Cosmos DB voor MongoDb](connector-azure-cosmos-db-mongodb-api.md). Andere API-typen worden momenteel niet ondersteund.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Cosmos DB-connector (SQL API) wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Azure Cosmos DB (SQL-API) kopiëren naar elk ondersteund Sink-gegevens archief of gegevens kopiëren van elk ondersteund bron gegevens archief naar Azure Cosmos DB (SQL-API). Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

U kunt de Azure Cosmos DB (SQL API)-connector gebruiken voor het volgende:

- Gegevens kopiëren van en naar de Azure Cosmos DB [SQL-API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Schrijf naar Azure Cosmos DB als **Insert** of **upsert**.
- JSON-documenten importeren en exporteren als-is of gegevens kopiëren van of naar een gegevensset in tabel vorm. Voor beelden zijn een SQL database en een CSV-bestand. Zie JSON-documenten importeren of exporteren als u documenten wilt kopiëren naar of van JSON-bestanden of naar of van een andere Azure Cosmos DB verzameling.

Data Factory integreert met de [bibliotheek van de Azure Cosmos DB bulk](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) -uitvoerder voor de beste prestaties wanneer u naar Azure Cosmos DB schrijft.

> [!TIP]
> De [video over gegevens migratie](https://youtu.be/5-SRNiC_qOU) leidt u door de stappen voor het kopiëren van gegevens uit Azure Blob-opslag naar Azure Cosmos db. In de video worden ook de overwegingen voor prestaties afstemmen beschreven voor het opnemen van gegevens aan Azure Cosmos DB in het algemeen.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service Azure Cosmos DB (SQL API):

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **CosmosDb**. | Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met de Azure Cosmos DB-Data Base.<br />**Opmerking**: u moet database gegevens opgeven in de Connection String, zoals wordt weer gegeven in de volgende voor beelden. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook account sleutel in Azure Key Vault plaatsen en de `accountKey` configuratie uit de connection string halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. |Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration Runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

**Voorbeeld**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: account sleutel opslaan in Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
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

In deze sectie vindt u een lijst met eigenschappen die de Azure Cosmos DB-gegevensset (SQL API) ondersteunt. 

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Als u gegevens wilt kopiëren van of naar Azure Cosmos DB (SQL-API), stelt u de eigenschap **type** van de gegevensset in op **DocumentDbCollection**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **DocumentDbCollection**. |Ja |
| collectionName |De naam van de Azure Cosmos DB document verzameling. |Ja |

**Voorbeeld**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema door Data Factory

Voor gegevens archieven met een schema als Azure Cosmos DB, wordt met Kopieer activiteit het schema afgeleid op een van de manieren die in de volgende lijst wordt beschreven. Tenzij u [JSON-documenten wilt importeren of exporteren zoals](#import-or-export-json-documents)in, moet u de best practice de structuur van de gegevens in de sectie **structure** opgeven.

* Als u de structuur van gegevens opgeeft met behulp van de eigenschap **structure** in de definitie van de gegevensset, wordt deze structuur door Data Factory als schema geaccepteerd. 

    Als een rij geen waarde voor een kolom bevat, wordt er een null-waarde voor de kolom waarde gegeven.
* Als u de structuur van gegevens niet opgeeft met behulp van de eigenschap **structure** in de definitie van de gegevensset, wordt het schema door de Data Factory-service door de eerste rij in de gegevens afgeleid. 

    Als de eerste rij niet het volledige schema bevat, ontbreken sommige kolommen in het resultaat van de Kopieer bewerking.

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

In deze sectie vindt u een lijst met eigenschappen die door de Azure Cosmos DB (SQL-API) voor bron-en Sink-ondersteuning worden ondersteund.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL-API) als bron

Als u gegevens wilt kopiëren van Azure Cosmos DB (SQL-API), stelt u het **bron** type in de Kopieer activiteit in op **DocumentDbCollectionSource**. 

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **DocumentDbCollectionSource**. |Ja |
| query |Geef de Azure Cosmos DB query op om gegevens te lezen.<br/><br/>Voorbeeld:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nee <br/><br/>Als deze SQL-instructie niet wordt opgegeven, wordt deze uitgevoerd: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Een speciaal teken dat aangeeft dat het document is genest en hoe de resultatenset moet worden afgevlakt.<br/><br/>Als een Azure Cosmos DB query bijvoorbeeld het geneste resultaat `"Name": {"First": "John"}`retourneert, wordt met de Kopieer activiteit de naam van de kolom aangeduid als `Name.First`, met de waarde "John" wanneer de waarde van **nestedSeparator** **.** (punt). |Nee<br />(de standaard instelling is **.** (punt)) |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL-API) als Sink

Als u gegevens wilt kopiëren naar Azure Cosmos DB (SQL-API), stelt u het **sink** -type in de Kopieer activiteit in op **DocumentDbCollectionSink**. 

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **DocumentDbCollectionSink**. |Ja |
| WriteBehavior |Hierin wordt beschreven hoe u gegevens naar Azure Cosmos DB schrijft. Toegestane waarden: **Insert** en **upsert**.<br/><br/>Het gedrag van **upsert** is om het document te vervangen als er al een document met dezelfde id bestaat. Als dat niet het geval is, voegt u het document in.<br /><br />**Opmerking**: de Data Factory genereert automatisch een id voor een document als er geen id is opgegeven in het oorspronkelijke document of op kolom toewijzing. Dit betekent dat u ervoor moet zorgen dat uw document een ID heeft om **upsert** te laten werken zoals verwacht. |Nee<br />(de standaard waarde is **Invoegen**) |
| writeBatchSize | Data Factory maakt gebruik van de [Azure Cosmos DB bulk](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) -uitvoerder bibliotheek om gegevens naar Azure Cosmos DB te schrijven. De eigenschap **writeBatchSize** bepaalt de grootte van documenten die door ADF aan de bibliotheek worden verstrekt. U kunt proberen de waarde voor **writeBatchSize** te verhogen om de prestaties te verbeteren en de waarde te verlagen als uw document groot is. Zie de onderstaande tips voor meer informatie. |Nee<br />(de standaard waarde is **10.000**) |
| nestingSeparator |Een speciaal teken in de naam van de **bron** kolom waarmee wordt aangegeven dat een genest document nodig is. <br/><br/>`Name.First` in de structuur van de uitvoer gegevensset genereert bijvoorbeeld de volgende JSON-structuur in het Azure Cosmos DB document wanneer de **nestedSeparator** is **.** (punt): `"Name": {"First": "[value maps to this column from source]"}`  |Nee<br />(de standaard instelling is **.** (punt)) |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens zoals Cosmos DB RUs voor het optimaliseren van Kopieer prestaties en aanbevelingen. Als u zich zorgen maakt over dit gedrag, geeft u `true` op om het uit te scha kelen. | Nee (standaard is `false`) |

>[!TIP]
>Cosmos DB beperkt de grootte van één aanvraag tot 2 MB. De formule is aanvraag grootte = één document grootte * Batch grootte schrijven. Als u een fout bericht krijgt met de melding dat de grootte van de **aanvraag te groot is**, **vermindert u de `writeBatchSize` waarde** in Sink kopiëren.

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```
## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="import-or-export-json-documents"></a>JSON-documenten importeren of exporteren

U kunt deze Azure Cosmos DB-connector (SQL API) eenvoudig gebruiken om het volgende te doen:

* Importeer JSON-documenten uit verschillende bronnen naar Azure Cosmos DB, met inbegrip van Azure Blob-opslag, Azure Data Lake Store en andere op bestanden gebaseerde archieven die door Azure Data Factory worden ondersteund.
* JSON-documenten exporteren vanuit een Azure Cosmos DB-verzameling naar verschillende op bestanden gebaseerde archieven.
* Kopieer documenten tussen twee Azure Cosmos DB verzamelingen.

Schema-neutraal kopiëren:

* Wanneer u het hulp programma Gegevens kopiëren gebruikt, selecteert u de optie **exporteren naar json-bestanden of Cosmos DB verzameling** .
* Wanneer u de functie voor het ontwerpen van activiteiten gebruikt, geeft u de sectie **structuur** (ook wel *schema*genoemd) niet op in de Azure Cosmos DB gegevensset. Geef ook niet de eigenschap **nestingSeparator** op in de Azure Cosmos DB bron of sink in de Kopieer activiteit. Wanneer u gegevens importeert uit of exporteert naar JSON-bestanden, geeft u in de bijbehorende gegevensset van het bestands archief de **indeling** type op als **JsonFormat** en configureert u de **filePattern** zoals beschreven in de sectie [JSON-indeling](supported-file-formats-and-compression-codecs.md#json-format) . Vervolgens geeft u de sectie **structuur** niet op en slaat u de rest van de indelings instellingen over.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven](copy-activity-overview.md##supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.
