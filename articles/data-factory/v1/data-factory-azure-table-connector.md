---
title: Gegevens verplaatsen naar/vanuit Azure-tabel
description: Meer informatie over het verplaatsen van gegevens van/naar Azure Table Storage met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 462d54a9d89d6f03aed5e221fa02609da786c8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260447"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Gegevens verplaatsen van en naar Azure Table met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-azure-table-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-table-storage.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Azure Table Storage-connector in V2](../connector-azure-table-storage.md)als u de huidige versie van de datafabriekservice gebruikt.

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens naar/vanuit Azure Table Storage te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit. 

U gegevens uit elk ondersteund brongegevensarchief kopiëren naar Azure Table Storage of vanuit Azure Table Storage naar een ondersteund sink datastore. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bronnen of sinks door de kopieeractiviteit. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van/naar een Azure-tabelopslag verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken. 

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst: 

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. 
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. 

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeelden](#json-examples) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in gegevensfabriek die worden gebruikt om gegevens naar/van een Azure Table Storage te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor Azure Table Storage: 

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
Er zijn twee typen gekoppelde services die u gebruiken om een Azure blob-opslag te koppelen aan een Azure-gegevensfabriek. Dit zijn: **AzureStorage** linked service en **AzureStorageSas** linked service. De gekoppelde Azure Storage-service biedt de gegevensfabriek algemene toegang tot de Azure Storage. Terwijl de gekoppelde service Azure Storage SAS (Shared Access Signature) de gegevensfabriek beperkte/tijdgebonden toegang biedt tot de Azure Storage. Er zijn geen andere verschillen tussen deze twee gekoppelde diensten. Kies de gekoppelde service die bij uw behoeften past. De volgende secties geven meer details over deze twee gekoppelde diensten.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie typeEigenschappen is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor de gegevensset van type **AzureTable** heeft de volgende eigenschappen.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in de instantie Azure Table Database waarnaar gekoppelde service verwijst. |Ja. Wanneer een tabelNaam is opgegeven zonder azureTableSourceQuery, worden alle records uit de tabel gekopieerd naar de bestemming. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoet, gekopieerd naar de bestemming. |

### <a name="schema-by-data-factory"></a>Schema op gegevensfabriek
Voor gegevensarchieven zonder schema, zoals Azure Table, leidt de service Data Factory het schema op een van de volgende manieren af:

1. Als u de structuur van gegevens opgeeft met behulp van de **eigenschap structuur** in de gegevenssetdefinitie, eert de service Gegevensfabriek deze structuur als het schema. Als een rij in dit geval geen waarde voor een kolom bevat, wordt er een null-waarde voor opgegeven.
2. Als u de structuur van gegevens niet opgeeft met behulp van de **eigenschap structuur** in de gegevenssetdefinitie, leidt Data Factory het schema af met behulp van de eerste rij in de gegevens. Als de eerste rij in dit geval niet het volledige schema bevat, worden sommige kolommen gemist in het resultaat van de kopieerbewerking.

Daarom is het voor schemavrije gegevensbronnen de beste praktijk om de structuur van gegevens op te geven met behulp van de **eigenschap structuur.**

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoergegevenssets en beleidsregels zijn beschikbaar voor alle soorten activiteiten.

Eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit daarentegen, variëren per activiteitstype. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

**AzureTableSource** ondersteunt de volgende eigenschappen in de sectie typeEigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableSourceQuery |Gebruik de aangepaste query om gegevens te lezen. |Azure-tabelquerytekenreeks. Zie voorbeelden in de volgende sectie. |Nee. Wanneer een tabelNaam is opgegeven zonder azureTableSourceQuery, worden alle records uit de tabel gekopieerd naar de bestemming. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoet, gekopieerd naar de bestemming. |
| azureTableSourceIgnoreTableNotFound |Geef aan of slik de uitzondering van de tabel niet bestaan. |TRUE<br/>FALSE |Nee |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-voorbeelden
Als de kolom Azure Table van tekenreekstype is:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Als de kolom Azure Table van datumtijdtype is:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** ondersteunt de volgende eigenschappen in de sectie typeEigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standaardpartitiesleutelwaarde die door de gootsteen kan worden gebruikt. |Een tekenreekswaarde. |Nee |
| azureTablePartitionKeyName |Geef de naam op van de kolom waarvan de waarden worden gebruikt als partitiesleutels. Als dit niet is opgegeven, wordt AzureTableDefaultPartitionKeyValue gebruikt als partitiesleutel. |Een kolomnaam. |Nee |
| azuretablerowkeynaam |Geef de naam op van de kolom waarvan de kolomwaarden als rijsleutel worden gebruikt. Als dit niet is opgegeven, gebruikt u een GUID voor elke rij. |Een kolomnaam. |Nee |
| azureTableInsertType |De modus om gegevens in te voegen in de Azure-tabel.<br/><br/>Met deze eigenschap bepaalt u of bestaande rijen in de uitvoertabel met overeenkomende partitie- en rijsleutels hun waarden hebben vervangen of samengevoegd. <br/><br/>Zie [Entiteit invoegen of samenvoegen](https://msdn.microsoft.com/library/azure/hh452241.aspx) en [Entiteitsonderwerpen invoegen of vervangen](https://msdn.microsoft.com/library/azure/hh452242.aspx) voor meer informatie over hoe deze instellingen werken (samenvoegen en vervangen). <br/><br> Deze instelling is van toepassing op rijniveau, niet op tabelniveau, en geen van beide opties verwijdert rijen in de uitvoertabel die niet in de invoer bestaan. |samenvoegen (standaard)<br/>vervangen |Nee |
| writeBatchSize |Hiermee voegt u gegevens in de Azure-tabel in wanneer de writeBatchSize of writeBatchTimeout wordt geraakt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Hiermee voegt u gegevens in de Azure-tabel in wanneer de writeBatchSize of writeBatchTimeout wordt geraakt |Tijdspanne<br/><br/>Voorbeeld: "00:20:00" (20 minuten) |Nee (Standaard-opslagclientstandaardtime-outwaarde 90 sec) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Wijs een bronkolom toe aan een doelkolom met de eigenschap JSON van vertaler voordat u de doelkolom gebruiken als azureTablePartitionKeyName.

In het volgende voorbeeld wordt bronkolom DivisionID toegewezen aan de doelkolom: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
De DivisionID is opgegeven als de partitiesleutel.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>VOORBEELDEN VAN JSON
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens kopieert van en naar Azure Table Storage en Azure Blob Database. Gegevens kunnen echter **rechtstreeks** worden gekopieerd van een van de bronnen naar een van de ondersteunde putten. Zie voor meer informatie de sectie 'Ondersteunde gegevensopslag en -indelingen' in [Gegevens verplaatsen met kopieeractiviteit](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Voorbeeld: Gegevens uit Azure Table kopiëren naar Azure Blob
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (gebruikt voor beide tabel& blob).
2. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureTable](#dataset-properties).
3. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. De [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die AzureTableSource en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens die behoren tot de standaardpartitie in een Azure-tabel naar een blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Gekoppelde Azure-opslagservice:**

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
Azure Data Factory ondersteunt twee typen gekoppelde Azure Storage-services: **AzureStorage** en **AzureStorageSas**. Voor de eerste geeft u de verbindingstekenreeks op die de accountsleutel bevat en voor de latere tekenreeks geeft u de SAS-uri (Shared Access Signature) op. Zie [sectie Gekoppelde services](#linked-service-properties) voor meer informatie.  

**Gegevensset Azure Table-invoer:**

In het voorbeeld wordt ervan uitgegaan dat u een tabel 'Mijntabel' hebt gemaakt in azure-tabel.

Als u "extern" instelt: "true" informeert de datafabriekservice dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Azure Blob-uitvoergegevensset:**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Activiteit in een pijplijn kopiëren met AzureTableSource en BlobSink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **AzureTableSource** en is **het sinktype** ingesteld op **BlobSink**. De SQL-query die is opgegeven met de eigenschap **AzureTableSourceQuery** selecteert de gegevens van de standaardpartitie die elk uur moet worden gekopieerd.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                    },
                    "sink": {
                        "type": "BlobSink"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Voorbeeld: Gegevens uit Azure Blob kopiëren naar Azure Table
In het volgende voorbeeld wordt het volgende voorbeeld weergegeven:

1. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (gebruikt voor beide tabel& blob)
2. Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureTable](#dataset-properties).
4. De [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [AzureTableSink](#copy-activity-properties)gebruikt.

Het voorbeeld kopieert tijdreeksgegevens van een Azure-blob naar een Azure-tabel per uur. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

**Azure-opslag (voor de gekoppelde service voor Azure Table & Blob):**

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

Azure Data Factory ondersteunt twee typen gekoppelde Azure Storage-services: **AzureStorage** en **AzureStorageSas**. Voor de eerste geeft u de verbindingstekenreeks op die de accountsleutel bevat en voor de latere tekenreeks geeft u de SAS-uri (Shared Access Signature) op. Zie [sectie Gekoppelde services](#linked-service-properties) voor meer informatie.

**Azure Blob-invoergegevensset:**

Gegevens worden elk uur opgehaald uit een nieuwe blob (frequentie: uur, interval: 1). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het jaar-, maand- en daggedeelte van de begintijd en bestandsnaam, het uurdeel van de begintijd. "extern": "true"-instelling informeert de Data Factory-service dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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

**Gegevensset azure-tabeluitvoer:**

Het voorbeeld kopieert gegevens naar een tabel met de naam 'Mijntabel' in azure-tabel. Maak een Azure-tabel met hetzelfde aantal kolommen als u verwacht dat het Blob CSV-bestand bevat. Elk uur worden er nieuwe rijen aan de tabel toegevoegd.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Activiteit in een pijplijn kopiëren met BlobSource en AzureTableSink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **BlobSource** en is **het sinktype** ingesteld op **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Toewijzing typen voor Azure Table
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar sinktypen met de volgende benadering in twee stappen.

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Wanneer gegevens worden verplaatst naar & vanuit Azure Table, worden de volgende [toewijzingen gebruikt die zijn gedefinieerd door Azure Table-service,](https://msdn.microsoft.com/library/azure/dd179338.aspx) van Azure Table OData-typen naar .NET-type en vice versa.

| OData-gegevenstype | .NET-type | Details |
| --- | --- | --- |
| Edm.Binary (Edm.Binary) |byte[] |Een array van bytes tot 64 KB. |
| Edm.Boolean |Booleaanse waarde |Een booleaanse waarde. |
| Edm.DateTime |DateTime |Een 64-bits waarde uitgedrukt als Coordinated Universal Time (UTC). Het ondersteunde DateTime-bereik begint vanaf 12:00 middernacht, 1 januari 1601 na 16.01 uur. (C.E.), UTC. Het assortiment eindigt op 31 december 9999. |
| Edm.Double |double |Een 64-bits drijvende puntwaarde. |
| Edm.Guid Edm.Guid |GUID |Een 128-bits wereldwijd unieke id. |
| Edm.Int32 |Int32 |Een 32-bits geheel getal. |
| Edm.Int64 |Int64 |Een 64-bits geheel getal. |
| Edm.String |Tekenreeks |Een UTF-16-gecodeerde waarde. Tekenreekswaarden kunnen maximaal 64 KB zijn. |

### <a name="type-conversion-sample"></a>Voorbeeld van typeconversie
Het volgende voorbeeld is voor het kopiëren van gegevens uit een Azure Blob naar Azure Table met typeconversies.

Stel dat de blob-gegevensset in CSV-indeling is en drie kolommen bevat. Een van hen is een datumtijdkolom met een aangepaste datumtijdnotatie met verkorte Franse namen voor dag van de week.

Definieer de gegevensset Blobbron als volgt, samen met tekstdefinities voor de kolommen.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Gezien de typetoewijzing van Azure Table OData-type naar .NET-type, definieert u de tabel in Azure Table met het volgende schema.

**Azure Table-schema:**

| Kolomnaam | Type |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| laatsteinlogdatum |Edm.DateTime |

Definieer vervolgens de azure-tabelgegevensset als volgt. U hoeft de sectie 'structuur' niet op te geven aan de typegegevens, omdat de typegegevens al zijn opgegeven in het onderliggende gegevensarchief.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

In dit geval typt Data Factory automatisch conversies, waaronder het veld Datetime met de aangepaste datumtijdindeling met de 'fr-fr'-cultuur wanneer gegevens van Blob naar Azure Table worden verplaatst.

> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Activiteitsprestatie kopiëren & tuninggids](data-factory-copy-activity-performance.md)kopiëren voor meer informatie over de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en verschillende manieren om deze te optimaliseren.
