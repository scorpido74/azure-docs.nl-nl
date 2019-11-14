---
title: Gegevens in Azure Cosmos DB (SQL API) kopiëren en transformeren met behulp van Data Factory
description: Informatie over het kopiëren van gegevens van en naar Azure Cosmos DB (SQL-API) en en het transformeren van gegevens in Azure Cosmos DB (SQL API) met behulp van Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 5e9db7c63e1493e1de5593262515040f071186e8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076799"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Gegevens in Azure Cosmos DB (SQL API) kopiëren en transformeren met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-azure-documentdb-connector.md)
> * [Huidige versie](connector-azure-cosmos-db.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Cosmos DB (SQL-API), en gegevens stroom te gebruiken om gegevens te transformeren in Azure Cosmos DB (SQL-API). Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

>[!NOTE]
>Deze connector ondersteunt alleen Cosmos DB SQL-API. Raadpleeg voor MongoDB-API [de connector voor de API van Azure Cosmos DB voor MongoDb](connector-azure-cosmos-db-mongodb-api.md). Andere API-typen worden momenteel niet ondersteund.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Cosmos DB-connector (SQL API) wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

Voor kopieer activiteit ondersteunt deze Azure Cosmos DB-connector (SQL API):

- Gegevens kopiëren van en naar de Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Schrijven naar Azure Cosmos DB als **invoegen** of **upsert**.
- Importeren en exporteren van JSON-documenten als-is, of gegevens te kopiëren van of naar een gegevensset in tabelvorm. Voorbeelden zijn een SQL-database en een CSV-bestand. Zie JSON-documenten importeren of exporteren als u documenten wilt kopiëren naar of van JSON-bestanden of naar of van een andere Azure Cosmos DB verzameling.

Data Factory kan worden geïntegreerd met de [Azure Cosmos DB bulksgewijs executor bibliotheek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) voor de beste prestaties bij het schrijven naar Azure Cosmos DB.

> [!TIP]
> De [gegevensmigratie video](https://youtu.be/5-SRNiC_qOU) begeleidt u stapsgewijs door de procedure voor het kopiëren van gegevens uit Azure Blob-opslag met Azure Cosmos DB. Prestaties afstemmen overwegingen voor het ophalen van gegevens met Azure Cosmos DB in het algemeen wordt ook beschreven in de video.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service Azure Cosmos DB (SQL API):

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **type** eigenschap moet worden ingesteld op **CosmosDb**. | Ja |
| connectionString |Geef informatie op die nodig is om verbinding met de Azure Cosmos DB-database.<br />**Houd er rekening mee**: moet u database-informatie in de verbindingsreeks opgeven zoals wordt weergegeven in de voorbeelden. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U kunt ook account sleutel in Azure Key Vault plaatsen en de `accountKey` configuratie uit de connection string halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. |Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) gebruiken om te verbinden met het gegevensarchief. U kunt de Azure Integration Runtime of een zelf-hostende integratieruntime gebruiken (als het gegevensarchief bevindt zich in een particulier netwerk). Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration Runtime wordt gebruikt. |Nee |

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

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets en secties, [gegevenssets en gekoppelde services](concepts-datasets-linked-services.md).

De volgende eigenschappen worden ondersteund voor de gegevensset Azure Cosmos DB (SQL API): 

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **CosmosDbSqlApiCollection**. |Ja |
| collectionName |De naam van de Azure Cosmos DB-documentverzameling. |Ja |

Als u een gegevensset van het type ' DocumentDbCollection ' gebruikt, wordt deze nog steeds ondersteund als-is voor achterwaartse compatibiliteit voor de activiteit Copy en lookup. dit wordt niet ondersteund voor de gegevens stroom. U wordt aangeraden het nieuwe model verder te gebruiken.

**Voorbeeld**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema door Data Factory

Voor de winkels schemavrije gegevens, zoals Azure Cosmos DB bepaalt Copy Activity in het schema in een van de manieren beschreven in de volgende lijst. Tenzij u wilt [importeren of exporteren van JSON-documenten als-is](#import-or-export-json-documents), de aanbevolen procedure is om op te geven van de structuur van de gegevens in de **structuur** sectie.

Data Factory voldoet aan de toewijzing die u hebt opgegeven voor de activiteit. Als een rij geen waarde voor een kolom bevat, wordt een null-waarde opgegeven voor de waarde in de kolom.

Als u geen toewijzing opgeeft, wordt het schema door de Data Factory-Service afgeleid aan de hand van de eerste rij in de gegevens. Als de eerste rij niet het volledige schema bevat, ontbreken sommige kolommen in het resultaat van de activiteit bewerking.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

In deze sectie vindt u een lijst met eigenschappen die door de Azure Cosmos DB (SQL-API) voor bron-en Sink-ondersteuning worden ondersteund.

Zie voor een volledige lijst van eigenschappen die beschikbaar zijn voor het definiëren van activiteiten en secties, [pijplijnen](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL-API) als bron

Als u gegevens wilt kopiëren van Azure Cosmos DB (SQL-API), stelt u het **bron** type in de Kopieer activiteit in op **DocumentDbCollectionSource**. 

De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **bron** sectie:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **CosmosDbSqlApiSource**. |Ja |
| query |Geef de Azure Cosmos DB-query voor het lezen van gegevens.<br/><br/>Voorbeeld:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nee <br/><br/>Als niet is opgegeven, wordt deze SQL-instructie uitgevoerd: `select <columns defined in structure> from mycollection` |
| preferredRegions | De voorkeurs lijst met regio's waarmee verbinding moet worden gemaakt wanneer ophalen gegevens van Cosmos DB. | Nee |
| pageSize | Het aantal documenten per pagina van het query resultaat. De standaard waarde is '-1 ', wat betekent dat de dynamische pagina grootte aan de kant van de service wordt gebruikt tot 1000. | Nee |

Als u een bron van het type ' DocumentDbCollectionSource ' gebruikt, wordt deze nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat naar uitgebreidere mogelijkheden biedt om gegevens van Cosmos DB te kopiëren.

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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
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

De volgende eigenschappen worden ondersteund in de Kopieeractiviteit **bron** sectie:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **CosmosDbSqlApiSink**. |Ja |
| writeBehavior |Beschrijft hoe u gegevens naar Azure Cosmos DB te schrijven. Toegestane waarden: **invoegen** en **upsert**.<br/><br/>Het gedrag van **upsert** wordt vervangen door het document als een document met dezelfde ID al bestaat; anders wordt het document invoegen.<br /><br />**Houd er rekening mee**: Data Factory genereert automatisch een ID voor een document als een ID niet is opgegeven in het oorspronkelijke document of door in de kolomtoewijzing. Dit betekent dat u moet ervoor zorgen dat voor **upsert** om te werken zoals verwacht, heeft uw document een ID. |Nee<br />(de standaardwaarde is **invoegen**) |
| writeBatchSize | Gegevensfactory maakt gebruik van de [Azure Cosmos DB bulksgewijs executor bibliotheek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) gegevens naar Azure Cosmos DB te schrijven. De eigenschap **writeBatchSize** bepaalt de grootte van documenten die door ADF aan de bibliotheek worden verstrekt. U kunt proberen te verhogen van de waarde voor **writeBatchSize** voor betere prestaties en het verminderen van de waarde als uw document grootte worden grote - Zie onderstaande tips. |Nee<br />(de standaardwaarde is **10.000**) |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens zoals Cosmos DB RUs voor het optimaliseren van Kopieer prestaties en aanbevelingen. Als u zich zorgen maakt over dit gedrag, geeft u `true` op om het uit te scha kelen. | Nee (standaard is `false`) |

>[!TIP]
>Cosmos DB beperkt de grootte van één aanvraag tot 2MB. De formule is grootte aanvragen één documentgrootte = * batchgrootte schrijven. Als u fout uitspraak bereikt **"Aanvragen is te groot."** , **verminderen de `writeBatchSize` waarde** in configuratie sink.

Als u een bron van het type ' DocumentDbCollectionSink ' gebruikt, wordt deze nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat naar uitgebreidere mogelijkheden biedt om gegevens van Cosmos DB te kopiëren.

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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over de [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) in de toewijzing van gegevens stroom.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="import-or-export-json-documents"></a>Importeren of exporteren van JSON-documenten

U kunt deze Azure Cosmos DB-connector (SQL API) eenvoudig gebruiken om het volgende te doen:

* JSON-documenten uit verschillende bronnen met Azure Cosmos DB, met inbegrip van Azure Blob storage, Azure Data Lake Store en andere bestandsgebaseerde winkels die ondersteuning biedt voor Azure Data Factory importeren.
* JSON-documenten uit een Azure Cosmos DB-verzameling exporteren naar verschillende winkels op basis van bestanden.
* Documenten tussen twee Azure Cosmos DB-verzamelingen als kopiëren-is.

Voor een schema-agnostische kopiëren:

* Wanneer u het hulpprogramma Copy Data gebruiken, selecteert u de **als exporteren-JSON-bestanden of Cosmos DB-verzameling** optie.
* Wanneer u de functie voor het ontwerpen van activiteiten gebruikt, kiest u JSON-indeling met de bijbehorende bestands opslag voor de bron of sink.

## <a name="next-steps"></a>Volgende stappen

Zie voor een lijst met gegevensarchieven die Kopieeractiviteit ondersteunt als bronnen en sinks in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
