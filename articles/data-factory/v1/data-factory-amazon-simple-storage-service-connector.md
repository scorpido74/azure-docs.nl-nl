---
title: Gegevens van Amazon Simple Storage Service verplaatsen met behulp van Data Factory
description: Meer informatie over het verplaatsen van gegevens van Amazon Simple Storage Service (S3) met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281650"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Gegevens van Amazon Simple Storage Service verplaatsen met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Versie 2 (huidige versie)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Amazon S3-connector in V2](../connector-amazon-simple-storage-service.md).

In dit artikel wordt uitgelegd hoe u de kopieeractiviteit in Azure Data Factory gebruiken om gegevens van Amazon Simple Storage Service (S3) te verplaatsen. Het bouwt voort op het artikel van de activiteiten van de [gegevensbeweging,](data-factory-data-movement-activities.md) wat een algemeen overzicht van gegevensbeweging met de exemplaaractiviteit voorstelt.

U gegevens van Amazon S3 kopiëren naar elk ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van Amazon S3 naar andere gegevensopslag, maar niet het verplaatsen van gegevens van andere gegevenswinkels naar Amazon S3.

## <a name="required-permissions"></a>Vereiste machtigingen
Als u gegevens van Amazon S3 wilt kopiëren, controleert u of u de volgende machtigingen hebt gekregen:

* `s3:GetObject`en `s3:GetObjectVersion` voor Amazon S3 Object Operations.
* `s3:ListBucket`voor Amazon S3 Bucket Operations. Als u de wizard Gegevensfabriekkopiëren gebruikt, `s3:ListAllMyBuckets` is dit ook vereist.

Zie [Machtigingen opgeven in een beleid voor](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)meer informatie over de volledige lijst met Amazon S3-machtigingen.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van een Amazon S3-bron verplaatst met behulp van verschillende hulpprogramma's of API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Een pijplijn maken met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle walkthrough.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.

Of u nu hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u hulpprogramma's of API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie het [JSON-voorbeeld: Gegevens kopiëren van Amazon S3 naar Azure Blob-sectie](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) voor een voorbeeld van JSON met JSON-definities voor entiteiten in gegevensfabriek die worden gebruikt om gegevens uit een Amazon S3-gegevensarchief te kopiëren.

> [!NOTE]
> Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md)voor meer informatie over ondersteunde bestands- en compressieindelingen voor een kopieeractiviteit.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om gegevensfabrieksentiteiten te definiëren die specifiek zijn voor Amazon S3.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
Een gekoppelde service koppelt een gegevensarchief aan een gegevensfabriek. U maakt een gekoppelde service van het type **AwsAccessKey** om uw Amazon S3-gegevensarchief te koppelen aan uw gegevensfabriek. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde service van Amazon S3 (AwsAccessKey).

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| accessKeyID |ID van de geheime toegangssleutel. |tekenreeks |Ja |
| secretAccessKey |De geheime toegangssleutel zelf. |Versleutelde geheime tekenreeks |Ja |

>[!NOTE]
>Deze connector vereist toegangssleutels voor IAM-account om gegevens van Amazon S3 te kopiëren. [Tijdelijke beveiligingsreferenties](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) worden niet ondersteund.
>

Hier volgt een voorbeeld:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven om invoergegevens in Azure Blob-opslag weer te geven, stelt u de eigenschap type van de gegevensset in op **AmazonS3**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de gekoppelde Amazon S3-service. Zie [Gegevenssets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

Secties zoals structuur, beschikbaarheid en beleid zijn vergelijkbaar voor alle gegevenssettypen (zoals SQL-database, Azure blob en Azure-tabel). De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie **typeEigenschappen** voor een gegevensset van het type **AmazonS3** (die de Amazon S3-gegevensset bevat) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| bucketName |De Naam van de S3-emmer. |Tekenreeks |Ja |
| sleutel |De S3-objecttoets. |Tekenreeks |Nee |
| Voorvoegsel |Voorvoegsel voor de S3-objecttoets. Objecten waarvan de toetsen met dit voorvoegsel beginnen, zijn geselecteerd. Geldt alleen wanneer de sleutel leeg is. |Tekenreeks |Nee |
| versie |De versie van het S3-object, als S3-versieing is ingeschakeld. |Tekenreeks |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie voor meer informatie de secties [Tekstindeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formaat](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parketformaat.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Als u bestanden wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. | |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. De ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. De ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. | |Nee |


> [!NOTE]
> **bucketName + toets** geeft de locatie aan van het S3-object, waarbij de emmer de hoofdcontainer voor S3-objecten is en de sleutel het volledige pad naar het S3-object is.

### <a name="sample-dataset-with-prefix"></a>Voorbeeldgegevensset met voorvoegsel

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Voorbeeldgegevensset (met versie)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Dynamische paden voor S3
In het vorige voorbeeld worden vaste waarden gebruikt voor de **eigenschappen sleutel-** en **bucketName** in de Amazon S3-gegevensset.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

U Data Factory deze eigenschappen dynamisch laten berekenen tijdens runtime, met behulp van systeemvariabelen zoals SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

U hetzelfde doen voor het **voorvoegsel** van een Amazon S3-gegevensset. Zie [Data Factory-functies en systeemvariabelen](data-factory-functions-variables.md)voor een lijst met ondersteunde functies en variabelen.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie [Pijplijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleidsregels zijn beschikbaar voor alle soorten activiteiten. Eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit, variëren per activiteitstype. Voor de kopieeractiviteit zijn de eigenschappen afhankelijk van de typen bronnen en putten. Wanneer een bron in de kopieeractiviteit van het type **FileSystemSource** is (waaronder Amazon S3), is de volgende eigenschap beschikbaar in de sectie **typeProperties:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u op of u s3-objecten opnieuw onder de map wilt weergeven. |waar/onwaar |Nee |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-voorbeeld: Gegevens van Amazon S3 kopiëren naar Azure Blob-opslag
In dit voorbeeld ziet u hoe u gegevens van Amazon S3 kopieert naar een Azure Blob-opslag. Gegevens kunnen echter rechtstreeks worden gekopieerd naar [een van de putten die worden ondersteund](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de kopieeractiviteit in Data Factory.

Het voorbeeld bevat JSON-definities voor de volgende entiteiten in gegevensfabrieken. U deze definities gebruiken om een pijplijn te maken om gegevens van Amazon S3 naar Blob-opslag te kopiëren met behulp van Visual [Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md)   

* Een gekoppelde service van het type [AwsAccessKey](#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [AmazonS3](#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [Bestandssysteembron](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens van Amazon S3 naar een Azure-blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

### <a name="amazon-s3-linked-service"></a>Amazon S3 gekoppelde service

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="amazon-s3-input-dataset"></a>Amazon S3-invoergegevensset

Instelling **"extern": true** informeert de Data Factory-service dat de gegevensset zich buiten de gegevensfabriek bevindt. Stel deze eigenschap in op true op een invoergegevensset die niet wordt geproduceerd door een activiteit in de pijplijn.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt de delen van de begintijd jaar, maand, dag en uur.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Activiteit in een pijplijn kopiëren met een Amazon S3-bron en een blobsink

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **FileSystemSource**en is **het sinktype** ingesteld op **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen van een brongegevensset wilt toewijzen aan kolommen uit een sink-set.


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* Zie de [handleiding Activiteitsprestaties en tuning](data-factory-copy-activity-performance.md)kopiëren voor meer informatie over de prestaties van gegevensverplaatsing (kopieeractiviteit) in Gegevensfabriek en verschillende manieren om deze te optimaliseren.

* Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.
