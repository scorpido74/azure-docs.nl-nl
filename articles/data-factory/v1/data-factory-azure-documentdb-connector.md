---
title: Gegevens verplaatsen naar/van Azure Cosmos DB
description: Meer informatie over het verplaatsen van gegevens van/naar Azure Cosmos DB-verzameling met Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a638184d5232de916ebd25360147301a93309dd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260512"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Gegevens verplaatsen van en naar Azure Cosmos DB met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-azure-documentdb-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Azure Cosmos DB-connector in V2](../connector-azure-cosmos-db.md)als u de huidige versie van de datafabriekservice gebruikt.

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens naar/van Azure Cosmos DB (SQL API) te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens uit elk ondersteund brongegevensarchief kopiëren naar Azure Cosmos DB of van Azure Cosmos DB naar een ondersteund sink datastore. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bronnen of sinks door de kopieeractiviteit.

> [!IMPORTANT]
> Azure Cosmos DB-connector ondersteunt alleen de SQL API.

Zie [JSON-documenten importeren/exporteren](#importexport-json-documents)als u gegevens wilt kopiëren naar/naar JSON-bestanden of een andere Cosmos DB-verzameling.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van/naar Azure Cosmos DB verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeelden](#json-examples) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in Gegevensfabriek die worden gebruikt om gegevens naar/van Cosmos DB te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor Cosmos DB:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde service azure cosmos DB.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **DocumentDb** |Ja |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met de Azure Cosmos DB-database. |Ja |

Voorbeeld:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Raadpleeg het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een jsonsetgegevenszijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, enz.).

De sectie typeEigenschappen is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van het type **DocumentDbCollection** heeft de volgende eigenschappen.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| collectionNaam |Naam van de Cosmos DB-documentverzameling. |Ja |

Voorbeeld:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Schema op gegevensfabriek
Voor gegevensarchieven zonder schema, zoals Azure Cosmos DB, leidt de service Data Factory het schema op een van de volgende manieren af:

1. Als u de structuur van gegevens opgeeft met behulp van de **eigenschap structuur** in de gegevenssetdefinitie, eert de service Gegevensfabriek deze structuur als het schema. In dit geval wordt een null-waarde opgegeven als een rij geen waarde voor een kolom bevat.
2. Als u de structuur van gegevens niet opgeeft met behulp van de **eigenschap structuur** in de gegevenssetdefinitie, leidt de service Gegevensfabriek het schema af met behulp van de eerste rij in de gegevens. Als de eerste rij in dit geval niet het volledige schema bevat, ontbreken in het resultaat van de kopieerbewerking enkele kolommen.

Daarom is het voor schemavrije gegevensbronnen de beste praktijk om de structuur van gegevens op te geven met behulp van de **eigenschap structuur.**

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Raadpleeg het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

> [!NOTE]
> De kopieeractiviteit neemt slechts één invoer en produceert slechts één uitvoer.

Eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit daarentegen variëren per activiteitstype en in het geval van kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

In het geval van kopieeractiviteit wanneer de bron van het type **DocumentDbCollectionSource is,** zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties:**

| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| query |Geef de query op om gegevens te lezen. |Querytekenreeks die wordt ondersteund door Azure Cosmos DB. <br/><br/>Voorbeeld: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nee <br/><br/>Als deze niet is opgegeven, wordt de SQL-instructie uitgevoerd:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciaal teken om aan te geven dat het document is genest |Elk karakter. <br/><br/>Azure Cosmos DB is een NoSQL-archief voor JSON-documenten, waar geneste structuren zijn toegestaan. Azure Data Factory stelt de gebruiker in staat om hiërarchie aan te duiden via nestingSeparator, wat '' is. in de bovenstaande voorbeelden. Met de scheidingsteken genereert de kopieeractiviteit het object "Naam" met drie onderliggende elementen Eerste, Midden en Laatste, volgens 'Name.First', 'Name.Middle' en 'Name.Last' in de tabeldefinitie. |Nee |

**DocumentDbCollectionSink** ondersteunt de volgende eigenschappen:

| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| nestingSeparator |Een speciaal teken in de naam van de bronkolom om aan te geven dat genest document nodig is. <br/><br/>Bijvoorbeeld hierboven: `Name.First` in de uitvoertabel produceert de volgende JSON-structuur in het Cosmos DB-document:<br/><br/>"Naam": {<br/>    "Ten eerste": "John"<br/>}, |Teken dat wordt gebruikt voor het scheiden van geneste niveaus.<br/><br/>Standaardwaarde `.` is (stip). |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. <br/><br/>Standaardwaarde `.` is (stip). |
| writeBatchSize |Aantal parallelle aanvragen voor Azure Cosmos DB-service om documenten te maken.<br/><br/>U de prestaties verfijnen bij het kopiëren van gegevens naar/van Cosmos DB met behulp van deze eigenschap. U betere prestaties verwachten wanneer u writeBatchSize verhoogt omdat er meer parallelle aanvragen naar Cosmos DB worden verzonden. U moet echter voorkomen dat throttling die de foutmelding kan gooien: "Verzoeksnelheid is groot".<br/><br/>Beperking wordt bepaald door een aantal factoren, waaronder de grootte van documenten, het aantal termen in documenten, indexeringsbeleid van doelverzameling, enz. Voor kopieerbewerkingen u een betere verzameling (bijvoorbeeld S3) gebruiken om de meeste doorvoer beschikbaar te hebben (2.500 aanvraageenheden per seconde). |Geheel getal |Nee (standaard: 5) |
| writeBatchTimeout |Wacht de tijd voordat de bewerking is voltooid voordat deze een time-out heeft. |Tijdspanne<br/><br/> Voorbeeld: "00:30:00" (30 minuten). |Nee |

## <a name="importexport-json-documents"></a>Json-documenten importeren/exporteren
Met deze Cosmos DB-connector u

* Json-documenten uit verschillende bronnen importeren in Cosmos DB, waaronder Azure Blob, Azure Data Lake, on-premises bestandssysteem of andere bestanden die worden ondersteund door Azure Data Factory.
* Exporteer JSON-documenten uit Cosmos DB-verzameling naar verschillende bestandenwinkels.
* Migreer gegevens tussen twee Cosmos DB-verzamelingen as-is.

Om een dergelijke schema-agnostische kopie te bereiken,
* Wanneer u de wizard Kopiëren gebruikt, schakelt u de optie **'Exporteren naar JSON-bestanden of Cosmos DB-verzameling'** in.
* Geef bij het gebruik van JSON-bewerking de sectie "structuur" niet op in de gegevensset (s) van Cosmos DB of 'nestingSeparator' op cosmos DB bron/sink in kopieeractiviteit. Als u wilt importeren van/exporteren naar JSON-bestanden, geeft u in de bestandsarchiefsetgegevens het indelingstype op als 'JsonFormat', config "filePattern" en slaat u de instellingen voor de rustindeling over, zie sectie [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format) over details.

## <a name="json-examples"></a>VOORBEELDEN VAN JSON
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens van en naar Azure Cosmos DB en Azure Blob Storage kopieert. Gegevens kunnen echter **rechtstreeks** worden gekopieerd van een van de bronnen naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Voorbeeld: Gegevens van Azure Cosmos DB kopiëren naar Azure Blob
Het onderstaande voorbeeld toont:

1. Een gekoppelde service van het type [DocumentDb](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [DocumentDbCollection](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [DocumentDbCollectionSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert gegevens in Azure Cosmos DB naar Azure Blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Gekoppelde Azure Cosmos DB-service:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Gekoppelde Azure Blob-opslagservice:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Gegevensset Azure Document DB-invoer:**

In het voorbeeld wordt ervan uitgegaan dat u een verzameling met de naam **Persoon** in een Azure Cosmos DB-database hebt.

'Extern': 'true': 'true' instellen en externegegevens opgevenGegevens van de Azure Data Factory-service dat de tabel zich buiten de gegevensfabriek bevindt en niet is geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure Blob-uitvoergegevensset:**

Gegevens worden elk uur naar een nieuwe blob gekopieerd met het pad voor de blob dat de specifieke datumtijd met uurgranulariteit weergeeft.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Voorbeeld van JSON-document in de verzameling Personen in een Cosmos DB-database:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB ondersteunt het opvragen van documenten met behulp van een SQL-achtige syntaxis ten opzichte van hiërarchische JSON-documenten.

Voorbeeld:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Met de volgende pijplijn worden gegevens uit de verzameling Personen in de Azure Cosmos DB-database gekopieerd naar een Azure-blob. Als onderdeel van de kopieeractiviteit zijn de invoer- en uitvoergegevenssets opgegeven.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Voorbeeld: Gegevens van Azure Blob kopiëren naar Azure Cosmos DB
Het onderstaande voorbeeld toont:

1. Een gekoppelde service van het type DocumentDb.
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type DocumentDbCollection.
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en DocumentDbCollectionSink gebruikt.

Het voorbeeld kopieert gegevens van Azure blob naar Azure Cosmos DB. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Gekoppelde Azure Blob-opslagservice:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Gekoppelde Azure Cosmos DB-service:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob-invoergegevensset:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB-uitvoergegevensset:**

Het voorbeeld kopieert gegevens naar een verzameling met de naam 'Persoon'.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Met de volgende pijplijn worden gegevens van Azure Blob kopieën gemaakt van de verzameling Personen in de Cosmos DB. Als onderdeel van de kopieeractiviteit zijn de invoer- en uitvoergegevenssets opgegeven.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Als de invoer van de voorbeeldblob is als

```
1,John,,Doe
```
Dan is de output JSON in Cosmos DB zal zijn als:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB is een NoSQL-archief voor JSON-documenten, waar geneste structuren zijn toegestaan. Azure Data Factory stelt de gebruiker in staat om hiërarchie aan te duiden via **nestingSeparator,** wat '' is. in dit voorbeeld. Met de scheidingsteken genereert de kopieeractiviteit het object "Naam" met drie onderliggende elementen Eerste, Midden en Laatste, volgens 'Name.First', 'Name.Middle' en 'Name.Last' in de tabeldefinitie.

## <a name="appendix"></a>Bijlage
1. **Vraag:** Ondersteunt de kopieeractiviteit een update van bestaande records?

    **Antwoord:** nee.
2. **Vraag:** Hoe gaat een kopie van een kopie naar Azure Cosmos DB om met reeds gekopieerde records?

    **Antwoord:** Als records een veld 'ID' hebben en de kopieerbewerking een record met dezelfde id probeert in te voegen, wordt er in de kopieerbewerking een fout gegenereerd.
3. **Vraag:** Ondersteunt Data Factory [het bereik of de op hash gebaseerde gegevenspartitionering?](../../cosmos-db/sql-api-partition-data.md)

    **Antwoord:** nee.
4. **Vraag:** Kan ik meer dan één Azure Cosmos DB-verzameling opgeven voor een tabel?

    **Antwoord:** nee. Er kan op dit moment slechts één verzameling worden opgegeven.

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
