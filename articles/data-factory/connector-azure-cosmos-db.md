---
title: Gegevens kopiëren en transformeren in Azure Cosmos DB (SQL API)
description: Meer informatie over het kopiëren van gegevens van en naar Azure Cosmos DB (SQL API) en het transformeren van gegevens in Azure Cosmos DB (SQL API) met behulp van Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: f0aa70333454b327a0ca76beef2985062ce56715
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415387"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Gegevens kopiëren en transformeren in Azure Cosmos DB (SQL API) met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-documentdb-connector.md)
> * [Huidige versie](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Cosmos DB (SQL API), en een gegevensstroom kunt gebruiken om gegevens in Azure Cosmos DB (SQL API) te transformeren. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.



>[!NOTE]
>Deze connector ondersteunt alleen Cosmos DB SQL API. Raadpleeg voor MongoDB API de [api voor Azure Cosmos DB voor MongoDB.](connector-azure-cosmos-db-mongodb-api.md) Andere API-typen worden nu niet ondersteund.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze SQL API-connector (Azure Cosmos DB) wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Gegevensstroom toewijzen](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

Voor Kopieeractiviteit ondersteunt deze SQL API-connector (Azure Cosmos DB) het als volgt:

- Kopieer gegevens van en naar de Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Schrijf naar Azure Cosmos DB als **insert** of **upsert**.
- Json-documenten importeren en exporteren als is of gegevens kopiëren van of naar een tabelgegevensset. Voorbeelden hiervan zijn een SQL-database en een CSV-bestand. Zie [JSON-documenten importeren en exporteren](#import-and-export-json-documents)als u documenten wilt kopiëren naar of van JSON-bestanden of van of naar of van een andere Azure Cosmos DB-verzameling.

Data Factory integreert met de [Azure Cosmos DB bulk executor library](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) om de beste prestaties te leveren wanneer u naar Azure Cosmos DB schrijft.

> [!TIP]
> De [video Gegevensmigratie](https://youtu.be/5-SRNiC_qOU) leidt u door de stappen van het kopiëren van gegevens uit Azure Blob-opslag naar Azure Cosmos DB. De video beschrijft ook prestatieafstemmingoverwegingen voor het innemen van gegevens naar Azure Cosmos DB in het algemeen.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die u gebruiken om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde sql API-service (Azure Cosmos DB):

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet worden ingesteld op **CosmosDb**. | Ja |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met de Azure Cosmos DB-database.<br />**Opmerking:** U moet databasegegevens opgeven in de verbindingstekenreeks zoals weergegeven in de volgende voorbeelden. <br/> U accountsleutel ook in Azure `accountKey` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg de volgende voorbeelden en [store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. |Ja |
| connectVia | De [runtime integratie](concepts-integration-runtime.md) om verbinding te maken met het gegevensarchief. U de runtime azure-integratie of een zelfgehoste nawerking van de integratie gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als deze eigenschap niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: accountsleutel opslaan in Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
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

Zie [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets.

De volgende eigenschappen worden ondersteund voor de SQL API-gegevensset (Azure Cosmos DB): 

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **CosmosDbSqlApiCollection**. |Ja |
| collectionNaam |De naam van de Azure Cosmos DB-documentverzameling. |Ja |

Als u de gegevensset 'DocumentDbCollection' gebruikt, wordt deze nog steeds ondersteund voor achterwaartse compatibiliteit voor kopieer- en opzoekactiviteit, deze wordt niet ondersteund voor gegevensstroom. U wordt voorgesteld om het nieuwe model in de toekomst te gebruiken.

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

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

In deze sectie vindt u een lijst met eigenschappen die de SQL API-bron- en sinkondersteuning (Azure Cosmos DB) heeft. Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) als bron

Als u gegevens uit Azure Cosmos DB (SQL API) wilt kopiëren, stelt u het **brontype** in Activiteit kopiëren in **DocumentDbCollectionSource**. 

De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron van kopieeractiviteit moet zijn ingesteld op **CosmosDbSqlApiSource**. |Ja |
| query |Geef de Azure Cosmos DB-query op om gegevens te lezen.<br/><br/>Voorbeeld:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nee <br/><br/>Als deze SQL-instructie niet is opgegeven, wordt deze SQL-instructie uitgevoerd:`select <columns defined in structure> from mycollection` |
| voorkeurRegio's | De voorkeurslijst met regio's waarmee u verbinding wilt maken bij het ophalen van gegevens uit Cosmos DB. | Nee |
| Pagesize | Het aantal documenten per pagina van het queryresultaat. Standaard is "-1", wat betekent dat de dynamische paginagrootte aan de servicezijde tot 1000 wordt gebruikt. | Nee |

Als u de typebron "DocumentDbCollectionSource" gebruikt, wordt deze nog steeds ondersteund voor achterwaartse compatibiliteit. U wordt voorgesteld om het nieuwe model te gebruiken in de toekomst die rijkere mogelijkheden bieden om gegevens van Cosmos DB te kopiëren.

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

Wanneer gegevens van Cosmos DB worden gekopieerd, tenzij u [JSON-documenten wilt exporteren zoals deze is,](#import-and-export-json-documents)u de toewijzing in kopieeractiviteit opgeven. Data Factory eert de toewijzing die u op de activiteit hebt opgegeven - als een rij geen waarde voor een kolom bevat, wordt een null-waarde opgegeven voor de kolomwaarde. Als u geen toewijzing opgeeft, leidt Data Factory het schema af met behulp van de eerste rij in de gegevens. Als de eerste rij niet het volledige schema bevat, ontbreken er enkele kolommen in het resultaat van de activiteitsbewerking.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) als gootsteen

Als u gegevens wilt kopiëren naar Azure Cosmos DB (SQL API), stelt u het **sinktype** in Activiteit kopiëren in **DocumentDbCollectionSink**. 

De volgende eigenschappen worden ondersteund in de sectie Activiteit **kopiëren:**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de groep Kopieeractiviteit moet worden ingesteld op **CosmosDbSqlApiSink**. |Ja |
| writeBehavior |Beschrijft hoe u gegevens schrijven naar Azure Cosmos DB. Toegestane waarden: **invoegen** en **upsert**.<br/><br/>Het gedrag van **upsert** is om het document te vervangen als er al een document met dezelfde ID bestaat; anders voegt u het document in.<br /><br />**Opmerking:** Data Factory genereert automatisch een ID voor een document als een id niet is opgegeven in het oorspronkelijke document of door kolomtoewijzing. Dit betekent dat u ervoor moet zorgen dat uw document een id heeft om ervoor te zorgen dat **upsert** werkt zoals verwacht. |Nee<br />(de standaardinstelling is **invoegen)** |
| writeBatchSize | Data Factory gebruikt de [Azure Cosmos DB-bulkexecutorbibliotheek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) om gegevens naar Azure Cosmos DB te schrijven. De eigenschap **writeBatchSize** bepaalt de grootte van documenten die ADF aan de bibliotheek verstrekt. U proberen de waarde voor **writeBatchSize** te verhogen om de prestaties te verbeteren en de waarde te verlagen als de grootte van uw document groot is - zie onderstaande tips. |Nee<br />(de standaardinstelling is **10.000**) |
| disableMetricsCollection disableMetricsCollection disableMetricsCollection disableMetrics | Data Factory verzamelt statistieken zoals Cosmos DB RUs voor optimalisatie van kopieerprestaties en aanbevelingen. Als u zich met dit `true` gedrag bezighoudt, geeft u op om het uit te schakelen. | Nee (standaard `false`is) |

>[!TIP]
>Als u JSON-documenten wilt importeren als is, verwijst u naar de sectie [JSON-documenten importeren of exporteren;](#import-and-export-json-documents) om te kopiëren van tabelvormige gegevens, verwijzen naar [Migreren van relationele database naar Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB beperkt de grootte van één aanvraag tot 2 MB. De formule is Aanvraaggrootte = Enkele documentgrootte * Schrijf batchgrootte. Als u op fout staat met de tekst **'Grootte aanvragen is te groot',** **vermindert u de waarde `writeBatchSize` ** in de configuratie van de kopieergootsteen.

Als u de typebron "DocumentDbCollectionSink" gebruikt, wordt deze nog steeds ondersteund voor achterwaartse compatibiliteit. U wordt voorgesteld om het nieuwe model te gebruiken in de toekomst die rijkere mogelijkheden bieden om gegevens van Cosmos DB te kopiëren.

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

### <a name="schema-mapping"></a>Schematoewijzing

Als u gegevens van Azure Cosmos DB wilt kopiëren naar tabelgoot of omgekeerd, raadpleegt u [schematoewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Gegevensstroomeigenschappen toewijzen

Wanneer u gegevens in kaartgegevensstroom transformeert, u in Cosmos DB lezen en schrijven naar verzamelingen. Zie voor meer informatie de [brontransformatie](data-flow-source.md) en [sinktransformatie](data-flow-sink.md) in kaartgegevensstromen.

### <a name="source-transformation"></a>Brontransformatie

Instellingen die specifiek zijn voor Azure Cosmos DB zijn beschikbaar op het tabblad **Bronopties** van de brontransformatie. 

**Systeemkolommen opnemen:** Als dit ```id``` ```_ts```waar is, worden en andere systeemkolommen opgenomen in de metagegevens van CosmosDB. Bij het bijwerken van verzamelingen is het belangrijk om dit op te nemen, zodat u de bestaande rij-id grijpen.

**Paginaformaat:** Het aantal documenten per pagina van het queryresultaat. Standaard is "-1" die de dynamische pagina van de service tot 1000 gebruikt.

**Doorvoer:** Stel een optionele waarde in voor het aantal R's dat u wilt toepassen op uw CosmosDB-verzameling voor elke uitvoering van deze gegevensstroom tijdens de leesbewerking. Minimum is 400.

**Voorkeursregio's:** Kies de voorkeursleesgebieden voor dit proces.

#### <a name="json-settings"></a>JSON-instellingen

**Eén document:** Selecteer deze optie als ADF het hele bestand als één JSON-document moet behandelen.

**Niet-geciteerde kolomnamen:** Selecteer deze optie als kolomnamen in de JSON niet worden geciteerd.

**Heeft opmerkingen:** Gebruik deze selectie als uw JSON-documenten opmerkingen in de gegevens bevatten.

**Single geciteerd:** Dit moet worden geselecteerd als de kolommen en waarden in uw document worden geciteerd met enkele aanhalingstekens.

**Backslash ontsnapte:** Als u backslashes gebruikt om aan tekens in uw JSON te ontsnappen, kiest u deze optie.

### <a name="sink-transformation"></a>Gootsteentransformatie

Instellingen die specifiek zijn voor Azure Cosmos DB zijn beschikbaar op het tabblad **Instellingen** van de sinktransformatie.

**Updatemethode:** Hiermee bepaalt u welke bewerkingen zijn toegestaan op uw databasebestemming. De standaardinstelling is om alleen inserts toe te staan. Als u rijen wilt bijwerken, upsert of verwijdert, is een transformatie met een wijzigingsrij vereist om rijen voor die acties te taggen. Voor updates, upserts en deletes moet een sleutelkolom of kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

**Inzamelingsactie:** Hiermee bepaalt u of u de bestemmingsverzameling opnieuw wilt maken voordat u gaat schrijven.
* Geen: Er wordt geen actie ondernomen om de collectie.
* Opnieuw maken: de collectie wordt gedropt en opnieuw gemaakt

**Batchgrootte:** hiermee bepaalt u hoeveel rijen er in elke bucket worden geschreven. Grotere batchformaten verbeteren compressie en geheugenoptimalisatie, maar lopen risico op uitzonderingen met het geheugen bij het incacheren van gegevens.

**Partitiesleutel:** Voer een tekenreeks in die de partitiesleutel voor uw verzameling vertegenwoordigt. Voorbeeld: ```/movies/title```

**Doorvoer:** Stel een optionele waarde in voor het aantal R's dat u wilt toepassen op uw CosmosDB-verzameling voor elke uitvoering van deze gegevensstroom. Minimum is 400.

**Schrijf doorvoerbudget:** Een geheel getal dat het aantal RU's vertegenwoordigt dat u wilt toewijzen aan de bulk-inname spark-taak. Dit aantal is van de totale doorvoer toegewezen aan de collectie.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Json-documenten importeren en exporteren

U deze SQL API-connector (Azure Cosmos DB) eenvoudig gebruiken:

* Kopieer documenten tussen twee Azure Cosmos DB-verzamelingen as-is.
* Json-documenten uit verschillende bronnen importeren in Azure Cosmos DB, waaronder vanuit Azure Blob-opslag, Azure Data Lake Store en andere bestanden die Azure Data Factory ondersteunt.
* Exporteer JSON-documenten uit een Azure Cosmos DB-verzameling naar verschillende bestandswinkels.

Ga als lid van het schema over een kopie van het schema:

* Wanneer u het gereedschap Gegevens kopiëren gebruikt, selecteert u de optie **Exporteren naar JSON-bestanden of Cosmos DB-verzameling.**
* Wanneer u activiteitsontwerpen gebruikt, kiest u JSON-indeling met het bijbehorende bestandsarchief voor bron of gootsteen.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migreren van relationele database naar Cosmos DB

Wanneer u migreert van een relationele database, bijvoorbeeld SQL Server naar Azure Cosmos DB, kunnen kopieeractiviteiten eenvoudig tabelgegevens van de bron toewijzen om JSON-documenten in Cosmos DB af te vlakken. In sommige gevallen u het gegevensmodel opnieuw ontwerpen om het te optimaliseren voor de NoSQL-use-cases volgens [Gegevensmodellering in Azure Cosmos DB,](../cosmos-db/modeling-data.md)bijvoorbeeld om de gegevens te denormaliseren door alle gerelateerde subitems in één JSON-document in te sluiten. Raadpleeg in dat geval [dit artikel](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) met een walkthrough over hoe u dit bereiken met behulp van azure data factory-kopieeractiviteit.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensopslag die Activiteit kopiëren ondersteunt als bronnen en sinks in Azure Data Factory.
