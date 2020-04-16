---
title: Gegevens uit de API van Azure Cosmos DB voor MongoDB kopiëren
description: Meer informatie over het kopiëren van gegevens uit ondersteunde brongegevensopslag naar of van de API van Azure Cosmos DB voor MongoDB naar ondersteunde sinkstores met behulp van Data Factory.
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
ms.openlocfilehash: 9b23f46a418f2663531cc121f00b83d00d84e48d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415451"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Gegevens kopiëren naar of vanuit de API voor MongoDB van Azure Cosmos DB met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar de API van Azure Cosmos DB voor MongoDB. Het artikel bouwt voort op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), dat een algemeen overzicht van Kopieeractiviteit weergeeft.

>[!NOTE]
>Deze connector ondersteunt alleen kopieergegevens van/naar de API van Azure Cosmos DB voor MongoDB. Raadpleeg voor SQL API [Cosmos DB SQL API-connector](connector-azure-cosmos-db.md). Andere API-typen worden nu niet ondersteund.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U gegevens uit de API van Azure Cosmos DB voor MongoDB kopiëren naar een ondersteund sink data store of gegevens kopiëren van een ondersteunde brongegevensopslag naar de API van Azure Cosmos DB voor MongoDB. Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die Activiteit kopiëren als bronnen en sinks ondersteunt.

U de API van Azure Cosmos DB voor MongoDB-connector gebruiken om:

- Kopieer gegevens van en naar de [API van Azure Cosmos DB voor MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Schrijf naar Azure Cosmos DB als **insert** of **upsert**.
- Json-documenten importeren en exporteren als is of gegevens kopiëren van of naar een tabelgegevensset. Voorbeelden hiervan zijn een SQL-database en een CSV-bestand. Zie JSON-documenten importeren of exporteren als u documenten wilt kopiëren naar of van JSON-bestanden of van of naar of van een andere Azure Cosmos DB-verzameling.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die u gebruiken om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de API van Azure Cosmos DB voor MongoDB.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de API van Azure Cosmos DB voor gekoppelde mongoDB-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet worden ingesteld op **CosmosDbMongoDbApi**. | Ja |
| Connectionstring |Geef de verbindingstekenreeks op voor de API van uw Azure Cosmos DB voor MongoDB. U het vinden in de Azure-portal -> uw Cosmos DB-blad `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`-> primaire of secundaire verbindingstekenreeks, met het patroon van. <br/><br />U ook een wachtwoord in Azure `password` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen.Raadpleeg [storereferenties in Azure Key Vault](store-credentials-in-key-vault.md) met meer details.|Ja |
| database | Naam van de database die u wilt openen. | Ja |
| connectVia | De [runtime integratie](concepts-integration-runtime.md) om verbinding te maken met het gegevensarchief. U de runtime azure-integratie of een zelfgehoste nawerking van de integratie gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als deze eigenschap niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
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

Zie [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. De volgende eigenschappen worden ondersteund voor de API voor MongoDB-gegevensset van Azure Cosmos DB:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **CosmosDbMongoDbApiCollection**. |Ja |
| collectionNaam |De naam van de Azure Cosmos DB-verzameling. |Ja |

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

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

In deze sectie vindt u een lijst met eigenschappen die de API van Azure Cosmos DB voor MongoDB-bron- en sinkondersteuning biedt.

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB's API voor MongoDB als bron

De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron van de kopieeractiviteit moet zijn ingesteld op **CosmosDbMongoDbApiSource**. |Ja |
| filter | Hiermee geeft u het selectiefilter op met queryoperatoren. Als u alle documenten in een verzameling wilt retourneren, laat u deze parameter weg of geeft u een leeg document door ({}). | Nee |
| cursorMethods.project | Hiermee geeft u de velden op die moeten worden teruggevonden in de documenten voor projectie. Als u alle velden in de overeenkomende documenten wilt retourneren, laat u deze parameter weg. | Nee |
| cursorMethoden.sorteren | Hiermee geeft u de volgorde op waarin de query overeenkomende documenten retourneert. Raadpleeg [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nee |
| cursorMethods.limit | Hiermee geeft u het maximumaantal documenten op dat de server retourneert. Raadpleeg [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nee | 
| cursorMethoden.overslaan | Hiermee geeft u het aantal documenten op dat moet worden overgeslagen en van waar MongoDB resultaten begint terug te sturen. Raadpleeg [cursor.overslaan()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nee |
| batchSize | Hiermee geeft u het aantal documenten op dat moet worden retourneren in elke batch van het antwoord van de instantie MongoDB. In de meeste gevallen heeft het wijzigen van de batchgrootte geen invloed op de gebruiker of de toepassing. Cosmos DB beperkt elke batch mag niet groter zijn dan 40 MB, wat de som is van het batchSize-aantal documenten, dus verlaag deze waarde als de grootte van uw document groot is. | Nee<br/>(de standaardinstelling is **100**) |

>[!TIP]
>ADF-ondersteuning voor het consumeren van BSON-document in **de strikte modus**. Zorg ervoor dat uw filterquery in de modus Strikt staat in plaats van in de Shell-modus. Meer beschrijving is te vinden op [MongoDB handleiding](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB's API voor MongoDB als gootsteen

De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de groep Copy Activity moet worden ingesteld op **CosmosDbMongoDbApiSink**. |Ja |
| writeBehavior |Beschrijft hoe u gegevens schrijven naar Azure Cosmos DB. Toegestane waarden: **invoegen** en **upsert**.<br/><br/>Het gedrag van **upsert** is om het document `_id` te vervangen als een document met hetzelfde al bestaat; anders voegt u het document in.<br /><br />**Opmerking:** Data Factory genereert `_id` automatisch een `_id` voor een document als een document niet is opgegeven in het oorspronkelijke document of in kolomtoewijzing. Dit betekent dat u ervoor moet zorgen dat uw document een id heeft om ervoor te zorgen dat **upsert** werkt zoals verwacht. |Nee<br />(de standaardinstelling is **invoegen)** |
| writeBatchSize | De eigenschap **writeBatchSize** bepaalt de grootte van documenten die in elke batch moeten worden geschreven. U proberen de waarde voor **writeBatchSize** te verhogen om de prestaties te verbeteren en de waarde te verlagen als de grootte van uw document groot is. |Nee<br />(de standaardinstelling is **10.000**) |
| writeBatchTimeout | De wachttijd voor de batch invoegen bewerking te voltooien voordat het time-out. De toegestane waarde is tijdspanne. | Nee<br/>(de standaardinstelling is **00:30:00** - 30 minuten) |

>[!TIP]
>Als u JSON-documenten wilt importeren als is, verwijst u naar de sectie [JSON-documenten importeren of exporteren;](#import-and-export-json-documents) om te kopiëren van tabelvormige gegevens, verwijzen naar [Schema mapping](#schema-mapping).

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

## <a name="import-and-export-json-documents"></a>Json-documenten importeren en exporteren

U deze Azure Cosmos DB-connector eenvoudig gebruiken:

* Kopieer documenten tussen twee Azure Cosmos DB-verzamelingen as-is.
* Json-documenten uit verschillende bronnen importeren in Azure Cosmos DB, waaronder van MongoDB, Azure Blob-opslag, Azure Data Lake Store en andere bestanden die Azure Data Factory ondersteunt.
* Exporteer JSON-documenten uit een Azure Cosmos DB-verzameling naar verschillende bestandswinkels.

Ga als lid van het schema over een kopie van het schema:

* Wanneer u het gereedschap Gegevens kopiëren gebruikt, selecteert u de optie **Exporteren naar JSON-bestanden of Cosmos DB-verzameling.**
* Wanneer u activiteitsontwerpen gebruikt, kiest u JSON-indeling met het bijbehorende bestandsarchief voor bron of gootsteen.

## <a name="schema-mapping"></a>Schematoewijzing

Als u gegevens uit de API van Azure Cosmos DB voor MongoDB wilt kopiëren naar tabelgoot of omgekeerd, raadpleegt u [schematoewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping).

Specifiek voor het schrijven in Cosmos DB, om ervoor te zorgen dat u Cosmos DB bevolken met de juiste object-ID van uw brongegevens, bijvoorbeeld, hebt u een "id" kolom in SQL database tabel en wilt u`_id.$oid`de waarde van dat te gebruiken als het document-ID in MongoDB voor insert / upsert, moet u de juiste schema mapping in te stellen volgens MongoDB strikte modus definitie ( ) als volgt:

![Kaart-ID in MongoDB-gootsteen](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Na de uitvoering van kopieeractiviteiten wordt onder BSON ObjectId gegenereerd in gootsteen:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensopslag die Activiteit kopiëren ondersteunt als bronnen en sinks in Azure Data Factory.
