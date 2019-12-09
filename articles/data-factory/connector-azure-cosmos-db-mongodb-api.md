---
title: Gegevens kopiëren van de API van Azure Cosmos DB voor MongoDB
description: Informatie over het kopiëren van gegevens van ondersteunde brongegevens archieven naar of van de Azure Cosmos DB-API voor MongoDB naar ondersteunde Sink-archieven met behulp van Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 0b38bc3309d8cf265a554a10e36311f53e6fe8a9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929923"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Gegevens kopiëren naar of van de Azure Cosmos DB-API voor MongoDB met behulp van Azure Data Factory

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar de API van Azure Cosmos DB voor MongoDB. Het artikel is gebaseerd op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), die een algemeen overzicht van Kopieeractiviteit geeft.

>[!NOTE]
>Deze connector ondersteunt alleen het kopiëren van gegevens van/naar de API van Azure Cosmos DB voor MongoDB. Raadpleeg [Cosmos DB SQL API connector](connector-azure-cosmos-db.md)voor SQL API. Andere API-typen worden momenteel niet ondersteund.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens van Azure Cosmos DB-API voor MongoDB kopiëren naar een ondersteunde Sink-gegevens opslag of gegevens uit een ondersteund gegevens archief kopiëren naar de API van Azure Cosmos DB voor MongoDB. Zie voor een lijst met gegevens opslaat of Kopieeractiviteit als bronnen en sinks ondersteunt, [ondersteunde gegevensarchieven en indelingen](copy-activity-overview.md#supported-data-stores-and-formats).

U kunt de API van de Azure Cosmos DB voor MongoDB-connector gebruiken voor het volgende:

- Gegevens kopiëren van en naar de [API van de Azure Cosmos DB voor MongoDb](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Schrijven naar Azure Cosmos DB als **invoegen** of **upsert**.
- Importeren en exporteren van JSON-documenten als-is, of gegevens te kopiëren van of naar een gegevensset in tabelvorm. Voorbeelden zijn een SQL-database en een CSV-bestand. Zie JSON-documenten importeren of exporteren als u documenten wilt kopiëren naar of van JSON-bestanden of naar of van een andere Azure Cosmos DB verzameling.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor de API van Azure Cosmos DB voor MongoDB.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de API van de Azure Cosmos DB voor MongoDB gekoppelde service:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **CosmosDbMongoDbApi**. | Ja |
| connectionString |Geef de connection string op voor de API van uw Azure Cosmos DB voor MongoDB. U kunt deze vinden in de Azure Portal-> uw Cosmos DB-Blade-> primaire of secundaire connection string, met het patroon van `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Dit veld als markeert een **SecureString** type voor het veilig opslaan in Data Factory. U kunt ook [verwijzen naar een geheim opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| database | De naam van de data base die u wilt openen. | Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) gebruiken om te verbinden met het gegevensarchief. U kunt de Azure Integration Runtime of een zelf-hostende integratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration Runtime wordt gebruikt. |Nee |

**Voorbeeld**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets en secties, [gegevenssets en gekoppelde services](concepts-datasets-linked-services.md). De volgende eigenschappen worden ondersteund voor de API van Azure Cosmos DB voor MongoDB-gegevensset:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **CosmosDbMongoDbApiCollection**. |Ja |
| collectionName |De naam van de verzameling Azure Cosmos DB. |Ja |

**Voorbeeld**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

In deze sectie vindt u een lijst met eigenschappen die de API van de Azure Cosmos DB voor MongoDB-bron-en Sink-ondersteuning.

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van activiteiten en secties, [pijplijnen](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>De API van Azure Cosmos DB voor MongoDB als bron

De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **bron** sectie:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **CosmosDbMongoDbApiSource**. |Ja |
| filter | Hiermee geeft u selectie filter op met behulp van query operators. Als u alle documenten in een verzameling wilt retour neren, laat u deze para meter weg of geeft u een leeg document door ({}). | Nee |
| cursorMethods.project | Hiermee geeft u de velden op die moeten worden geretourneerd in de documenten voor projectie. Als u alle velden in de overeenkomende documenten wilt retour neren, laat u deze para meter weg. | Nee |
| cursorMethods.sort | Hiermee geeft u de volg orde waarin de query overeenkomende documenten retourneert. Raadpleeg [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nee |
| cursorMethods.limit | Hiermee geeft u het maximum aantal documenten op dat door de server wordt geretourneerd. Raadpleeg [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nee | 
| cursorMethods.skip | Hiermee geeft u het aantal documenten op dat moet worden overgeslagen en waar MongoDB begint met het retour neren van resultaten. Raadpleeg [cursor. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nee |
| batchSize | Hiermee geeft u het aantal documenten op dat moet worden geretourneerd in elke batch van het antwoord van de MongoDB-instantie. In de meeste gevallen heeft het wijzigen van de Batch grootte geen invloed op de gebruiker of de toepassing. Cosmos DB limieten voor elke batch kan niet groter zijn dan 40MB. Dit is de som van de grootte van het batchSize aantal documenten. Verlaag deze waarde dus als de grootte van het document groot is. | Nee<br/>(de standaard waarde is **100**) |

>[!TIP]
>ADF-ondersteuning die BSON-document verbruikt in de **strikte modus**. Zorg ervoor dat uw filter query in de strikte modus in plaats van in de shell modus is. Meer beschrijving vindt u in [MongoDb hand matig](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>De API van Azure Cosmos DB voor MongoDB als Sink

De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **sink** sectie:

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **CosmosDbMongoDbApiSink**. |Ja |
| writeBehavior |Beschrijft hoe u gegevens naar Azure Cosmos DB te schrijven. Toegestane waarden: **invoegen** en **upsert**.<br/><br/>Het gedrag van **upsert** is het vervangen van het document als er al een document met dezelfde `_id` bestaat. Als dat niet het geval is, voegt u het document in.<br /><br />**Opmerking**: Data Factory genereert automatisch een `_id` voor een document als er geen `_id` in het oorspronkelijke document of op kolom toewijzing is opgegeven. Dit betekent dat u moet ervoor zorgen dat voor **upsert** om te werken zoals verwacht, heeft uw document een ID. |Nee<br />(de standaardwaarde is **invoegen**) |
| writeBatchSize | De eigenschap **writeBatchSize** bepaalt de grootte van documenten die in elke batch moeten worden geschreven. U kunt proberen de waarde voor **writeBatchSize** te verhogen om de prestaties te verbeteren en de waarde te verlagen als de grootte van uw document groot is. |Nee<br />(de standaardwaarde is **10.000**) |
| writeBatchTimeout | De wacht tijd waarna de batch INSERT-bewerking moet worden voltooid voordat er een time-out optreedt. De toegestane waarde is time span. | Nee<br/>(de standaard waarde is **00:30:00** -30 minuten) |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Als u JSON-documenten wilt importeren als-is, raadpleegt u de sectie [JSON-documenten importeren of exporteren](#import-or-export-json-documents) . Zie [schema toewijzing](#schema-mapping)voor informatie over het kopiëren van gegevens in tabel vorm.

## <a name="import-or-export-json-documents"></a>Importeren of exporteren van JSON-documenten

U kunt eenvoudig deze Azure Cosmos DB-connector te gebruiken:

* JSON-documenten uit verschillende bronnen met Azure Cosmos DB, met inbegrip van Azure Blob storage, Azure Data Lake Store en andere bestandsgebaseerde winkels die ondersteuning biedt voor Azure Data Factory importeren.
* JSON-documenten uit een Azure Cosmos DB-verzameling exporteren naar verschillende winkels op basis van bestanden.
* Documenten tussen twee Azure Cosmos DB-verzamelingen als kopiëren-is.

Om een dergelijke schema-neutraal kopie te krijgen, slaat u de sectie ' Structure ' (ook wel *schema*genoemd) in de gegevensset en schema toewijzing in de Kopieer activiteit over.

## <a name="schema-mapping"></a>Schematoewijzing

Als u gegevens wilt kopiëren van de API van Azure Cosmos DB voor MongoDB naar tabellaire sink of omgekeerd, raadpleegt u [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping).

Om ervoor te zorgen dat u Cosmos DB met de juiste object-ID van de bron gegevens, bijvoorbeeld, een kolom ' id ' in SQL database tabel hebt en u de waarde wilt gebruiken als de document-ID in MongoDB voor INSERT/upsert, moet u de juiste schema toewijzing instellen volgens MongoDB strict mode definition (`_id.$oid`) als volgt: Cosmos DB

![Toewijzings-ID in MongoDB-Sink](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Nadat de Kopieer activiteit is uitgevoerd, wordt de onderstaande BSON ObjectId gegenereerd in Sink:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Volgende stappen

Zie voor een lijst met gegevensarchieven die Kopieeractiviteit ondersteunt als bronnen en sinks in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
