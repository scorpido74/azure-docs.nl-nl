---
title: Gegevens naar zoekindex pushen met Gegevensfabriek
description: Meer informatie over het pushen van gegevens naar Azure Cognitive Search Index met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b1170f721cf8521cfe1762df0cc616c938ddf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281559"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Gegevens naar een Azure Cognitive Search-index pushen met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-azure-search-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-search.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Azure Cognitive Search-connector in V2](../connector-azure-search.md)als u de huidige versie van de gegevensfabriekservice gebruikt.

In dit artikel wordt beschreven hoe u de activiteit kopiëren gebruikt om gegevens van een ondersteund brongegevensarchief naar een Azure Cognitive Search-index te pushen. Ondersteunde brongegevensopslag worden vermeld in de kolom Bron van de [tabel ondersteunde bronnen en putten.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Dit artikel bouwt voort op het artikel [over gegevensverplaatsingsactiviteiten,](data-factory-data-movement-activities.md) dat een algemeen overzicht van gegevensverplaatsing met Kopieeractiviteit en ondersteunde combinaties van gegevensopslagpresenteert.

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
Als u de service Data Factory verbinding wilt maken met een on-premises gegevensopslag, installeert u Data Management Gateway in uw on-premises omgeving. U gateway installeren op dezelfde machine die het brongegevensarchief of op een aparte machine host om te voorkomen dat u concurreert om resources met het gegevensarchief.

Data Management Gateway verbindt on-premises gegevensbronnen op een veilige en beheerde manier met cloudservices. Zie [Gegevens verplaatsen tussen on-premises en cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie over Data Management Gateway.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit waarmee gegevens van een brongegevensarchief naar een zoekindex worden gepusht met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie [JSON-voorbeeld: Gegevens kopiëren van on-premises SQL Server naar een azure cognitive search-indexsectie](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index) voor een voorbeeld van JSON voor entiteiten in gegevensfabriek die worden gebruikt om gegevens naar zoekindex te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor een zoekindex:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

In de volgende tabel vindt u beschrijvingen voor JSON-elementen die specifiek zijn voor de azure cognitive search-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| type | De eigenschap type moet zijn ingesteld op: **AzureSearch**. | Ja |
| url | URL voor de zoekservice. | Ja |
| sleutel | Beheerderssleutel voor de zoekservice. | Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen. De sectie **typeEigenschappen** is verschillend voor elk type gegevensset. De sectie typeEigenschappen voor een gegevensset van het type **AzureSearchIndex** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| type | De eigenschap type moet zijn ingesteld op **AzureSearchIndex**.| Ja |
| indexNaam | Naam van de zoekindex. Data Factory maakt de index niet. De index moet bestaan in Azure Cognitive Search. | Ja |


## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en verschillende beleidsregels zijn beschikbaar voor alle soorten activiteiten. Terwijl de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Voor Kopieeractiviteit zijn de volgende eigenschappen beschikbaar in de sectie TypeEigenschappen wanneer de gootsteen van het type **AzureSearchIndexSink**is:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| -------- | ----------- | -------------- | -------- |
| Schrijfgedrag | Hiermee geeft u op of een document al in de index moet worden samengevoegd of vervangen. Zie de [eigenschap Schrijfgedrag](#writebehavior-property).| Samenvoegen (standaard)<br/>Uploaden| Nee |
| Schrijfbatchgrootte | Uploadt gegevens in de zoekindex wanneer de buffergrootte writeBatchSize bereikt. Zie de [eigenschap WriteBatchSize](#writebatchsize-property) voor meer informatie. | 1 tot 1000. De standaardwaarde is 1000. | Nee |

### <a name="writebehavior-property"></a>Schrijfgedrag, eigenschap
AzureSearchSink upserts bij het schrijven van gegevens. Met andere woorden, wanneer u een document schrijft, als de documentsleutel al bestaat in de zoekindex, werkt Azure Cognitive Search het bestaande document bij in plaats van een conflictuitzondering te plaatsen.

De AzureSearchSink biedt de volgende twee upsert gedragingen (met behulp van AzureSearch SDK):

- **Samenvoegen:** combineer alle kolommen in het nieuwe document met het bestaande document. Voor kolommen met null-waarde in het nieuwe document blijft de waarde in het bestaande document behouden.
- **Uploaden:** het nieuwe document vervangt het bestaande document. Voor kolommen die niet in het nieuwe document zijn opgegeven, wordt de waarde ingesteld op null of er een niet-null-waarde in het bestaande document is of niet.

Het standaardgedrag is **Samenvoegen**.

### <a name="writebatchsize-property"></a>WriteBatchSize , eigenschap
Azure Cognitive Search-service ondersteunt het schrijven van documenten als een batch. Een batch kan 1 tot 1.000 acties bevatten. Een actie verwerkt één document om de upload-/samenvoegbewerking uit te voeren.

### <a name="data-type-support"></a>Ondersteuning voor gegevenstype
In de volgende tabel wordt aangegeven of een Azure Cognitive Search-gegevenstype al dan niet wordt ondersteund.

| Gegevenstype Azure Cognitive Search | Ondersteund in Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Tekenreeks | J |
| Int32 | J |
| Int64 | J |
| Double | J |
| Booleaans | J |
| DataTimeOffset | J |
| Tekenreeksarray | N |
| AardrijkskundePoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>JSON-voorbeeld: gegevens kopiëren van on-premises SQL Server naar Azure Cognitive Search-index

In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. Een gekoppelde service van het type [AzureSearch](#linked-service-properties).
2. Een gekoppelde service van het type [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureSearchIndex](#dataset-properties).
4. Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) en [AzureSearchIndexSink](#copy-activity-properties)gebruikt.

Het voorbeeld kopieert tijdreeksgegevens uit een on-premises SQL Server-database naar de zoekindex per uur. De JSON-eigenschappen die in dit monster worden gebruikt, worden beschreven in secties die de monsters volgen.

Als eerste stap u de datamanagementgateway instellen op uw on-premises machine. De instructies bevinden zich in de [verhuisgegevens tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

**Gekoppelde azure cognitive search-service:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server-gekoppelde service**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server-invoergegevensset**

In het voorbeeld wordt ervan uitgegaan dat u een tabel "MyTable" in SQL Server hebt gemaakt en dat deze kolom "tijdstempelkolom" bevat voor tijdreeksgegevens. U meerdere tabellen in dezelfde database doorzoeken met één gegevensset, maar één tabel moet worden gebruikt voor het type TableName van de gegevensset.

Als u "extern" instelt: "true" informeert de dienst Data Factory dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Uitvoergegevensset Azure Cognitive Search:**

Het voorbeeld kopieert gegevens naar een Azure Cognitive Search-index met de naam **producten**. Data Factory maakt de index niet. Als u het voorbeeld wilt testen, maakt u een index met deze naam. Maak de zoekindex met hetzelfde aantal kolommen als in de invoergegevensset. Elk uur worden er nieuwe items toegevoegd aan de zoekindex.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Kopieer activiteit in een pijplijn met SQL-bron en Azure Cognitive Search Index-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn is het **brontype** ingesteld op **SqlSource** en **is het sinktype** ingesteld op **AzureSearchIndexSink**. De SQL-query die is opgegeven voor de eigenschap **SqlReaderQuery** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Als u gegevens uit een cloudgegevensarchief kopieert `executionLocation` naar Azure Cognitive Search, is eigenschap vereist. In het volgende JSON-fragment wordt `typeProperties` de wijziging weergegeven die nodig is onder Activiteit kopiëren als voorbeeld. Schakel Gegevens kopiëren tussen de sectie [cloudgegevensstores](data-factory-data-movement-activities.md#global) in voor ondersteunde waarden en meer details.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Kopiëren vanuit een cloudbron
Als u gegevens uit een cloudgegevensarchief kopieert `executionLocation` naar Azure Cognitive Search, is eigenschap vereist. In het volgende JSON-fragment wordt `typeProperties` de wijziging weergegeven die nodig is onder Activiteit kopiëren als voorbeeld. Schakel Gegevens kopiëren tussen de sectie [cloudgegevensstores](data-factory-data-movement-activities.md#global) in voor ondersteunde waarden en meer details.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

U kolommen van brongegevensset ook toewijzen aan kolommen uit de sink-gegevensset in de definitie van kopieeractiviteit. Zie Kolommen [met gegevenssetstoewijzen in Azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie de [handleiding voor prestaties en tuning kopiëren](data-factory-copy-activity-performance.md) om meer te weten te komen over belangrijke factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) en verschillende manieren om deze te optimaliseren.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* [Zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.
