---
title: Gegevens verplaatsen van de Amazon Simple Storage-Service met behulp van Data Factory | Microsoft Docs
description: Meer informatie over het verplaatsen van gegevens van de Amazon Simple Storage-Service (S3) met behulp van Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0e2468fdd44374343894416c8e39c263cecaa7d5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839554"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Gegevens verplaatsen van de Amazon Simple Storage-Service met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-amazon-simple-storage-service-connector.md)
> * [Versie 2 (huidige versie)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Amazon S3-connector in V2](../connector-amazon-simple-storage-service.md).

In dit artikel wordt uitgelegd hoe u van de kopieeractiviteit in Azure Data Factory om gegevens te verplaatsen van de Amazon Simple Storage-Service (S3). Dit is gebaseerd op de [activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) artikel een algemeen overzicht van de verplaatsing van gegevens met de kopieeractiviteit geeft.

U kunt gegevens vanaf Amazon S3 kopiëren naar een ondersteunde sink-gegevensopslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als sink voor de kopieeractiviteit, de [ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabel. Data Factory ondersteunt momenteel alleen verplaatst gegevens vanaf Amazon S3 naar andere gegevensarchieven, maar niet om gegevens te verplaatsen uit de andere gegevens worden opgeslagen naar Amazon S3.

## <a name="required-permissions"></a>Vereiste machtigingen
Als u wilt kopiëren van gegevens vanaf Amazon S3, zorg ervoor dat u de volgende machtigingen zijn verleend:

* `s3:GetObject` en `s3:GetObjectVersion` voor Amazon S3-Object bewerkingen.
* `s3:ListBucket` voor Amazon S3-Bucket bewerkingen. Als u de Kopieerwizard van Data Factory `s3:ListAllMyBuckets` is ook vereist.

Zie voor meer informatie over de volledige lijst met machtigingen voor Amazon S3 [machtigingen op te geven in een beleid](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Aan de slag
U kunt een pijplijn maken met een kopieeractiviteit die gegevens van een Amazon S3-bron verplaatst met behulp van verschillende hulpprogramma's of API's.

De eenvoudigste manier om een pijplijn te maken is met de **Kopieerwizard**. Zie voor een snel overzicht [zelfstudie: Een pijplijn maken met de Wizard kopiëren](data-factory-copy-data-wizard-tutorial.md).

U kunt ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sjabloon**, **.NET API**, en **REST-API**. Zie voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit de [zelfstudie Kopieeractiviteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Of u hulpprogramma's of API's gebruiken, kunt u de volgende stappen uit voor het maken van een pijplijn die gegevens van een brongegevensarchief naar een sink-gegevensopslag verplaatst uitvoeren:

1. Maak **gekoppelde services** opgeslagen om invoer- en gegevens te koppelen aan uw data factory.
2. Maak **gegevenssets** te vertegenwoordigen invoer- en uitvoergegevens voor de kopieerbewerking.
3. Maak een **pijplijn** met een kopieeractiviteit waarmee een gegevensset als invoer en een gegevensset als uitvoer.

Wanneer u de wizard gebruikt, worden de JSON-definities voor deze Data Factory-entiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u hulpprogramma's of API's (met uitzondering van de .NET API), kunt u deze Data Factory-entiteiten definiëren met behulp van de JSON-indeling. Zie voor een voorbeeld met JSON-definities voor Data Factory-entiteiten die worden gebruikt om gegevens te kopiëren naar een gegevensarchief Amazon S3, de [JSON-voorbeeld: Gegevens kopiëren van Amazon S3 naar Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) sectie van dit artikel.

> [!NOTE]
> Zie voor meer informatie over ondersteunde indelingen voor de bestands- en -compressie voor een kopieeractiviteit [bestands- en compressie indelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

De volgende secties bevatten meer informatie over JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek voor Amazon S3.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service
Een gekoppelde service verbindt een gegevensopslag op een data factory. U maakt een gekoppelde service van het type **AwsAccessKey** uw Amazon S3-gegevensarchief aan uw data factory koppelen. De volgende tabel bevat een beschrijving op voor JSON-elementen die specifiek zijn voor Amazon S3 (AwsAccessKey) gekoppelde service.

| Eigenschap | Description | Toegestane waarden | Verplicht |
| --- | --- | --- | --- |
| accessKeyID |ID van de geheime toegangssleutel. |string |Ja |
| secretAccessKey |De geheime toegangssleutel zelf. |Versleutelde geheime tekenreeks |Ja |

>[!NOTE]
>Toegangssleutels voor het kopiëren van gegevens vanaf Amazon S3 IAM-account voor deze connector vereist. [Tijdelijke beveiligingsreferentie](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) wordt niet ondersteund.
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
Als u een gegevensset die de invoergegevens in Azure Blob-opslag, stel de eigenschap type van de gegevensset in **Amazon S3**. Stel de **linkedServiceName** eigenschap van de gegevensset in de naam van de Amazon S3 gekoppelde service. Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, [gegevenssets maken](data-factory-create-datasets.md). 

Secties, zoals de structuur, beschikbaarheid en het beleid zijn vergelijkbaar voor alle gegevenssettypen (zoals SQL-database, Azure-blob en Azure-tabel). De **typeProperties** sectie verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevensarchief. De **typeProperties** sectie voor een gegevensset van het type **Amazon S3** (waaronder de Amazon S3-gegevensset) heeft de volgende eigenschappen:

| Eigenschap | Description | Toegestane waarden | Verplicht |
| --- | --- | --- | --- |
| bucketName |De naam van de S3-bucket. |String |Ja |
| key |De sleutel van de S3-object. |String |Nee |
| prefix |Voorvoegsel voor de sleutel S3-object. Objecten waarvan sleutels met dit voorvoegsel beginnen worden geselecteerd. Geldt alleen wanneer de sleutel is leeg. |String |Nee |
| version |De versie van de S3-object, als S3 versiebeheer is ingeschakeld. |String |Nee |
| format | De volgende bestandsindelingen worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **type** eigenschap onder indeling op een van deze waarden. Zie voor meer informatie de [tekstindeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-indeling](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format), en [Parquet-indeling ](data-factory-supported-file-and-compression-formats.md#parquet-format) secties. <br><br> Als u wilt kopiëren van bestanden-is tussen op basis van bestanden (binaire kopie), gaat u het gedeelte indeling in beide definities van de gegevensset voor invoer en uitvoer. |Nee | |
| compression | Geef het type en het niveau van compressie voor de gegevens. De ondersteunde typen zijn: **GZip**, **Deflate**, **BZip2**, en **ZipDeflate**. De ondersteunde niveaus zijn: **Optimale** en **snelste**. Zie voor meer informatie, [bestands- en compressie indelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nee | |


> [!NOTE]
> **bucketName + sleutel** geeft de locatie van de S3-object, waarbij bucket is de basiscontainer voor S3-objecten en -sleutel is het volledige pad naar de S3-object.

### <a name="sample-dataset-with-prefix"></a>Voorbeeldgegevensset met het voorvoegsel

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
In het voorgaande voorbeeld wordt een vaste waarden voor de **sleutel** en **bucketName** eigenschappen in de Amazon S3-gegevensset.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

U kunt Data Factory deze eigenschappen in runtime, dynamisch berekenen met behulp van de systeemvariabelen zoals slicestart-waarde hebben.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

U kunt hetzelfde doen voor de **voorvoegsel** eigenschap van een Amazon S3-gegevensset. Zie voor een lijst van ondersteunde functies en variabelen, [Data Factory-functies en systeemvariabelen](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, [het maken van pijplijnen](data-factory-create-pipelines.md). Eigenschappen zoals naam, beschrijving, invoer en uitvoer tabellen en beleidsregels zijn beschikbaar voor alle soorten activiteiten. Eigenschappen die beschikbaar zijn in de **typeProperties** sectie van de activiteit variëren met elk activiteitstype. Voor de kopieerbewerking wordt eigenschappen variëren afhankelijk van de typen van bronnen en sinks. Wanneer een bron in de copy-activiteit is van het type **FileSystemSource** (waaronder Amazon S3), de volgende eigenschap is beschikbaar in **typeProperties** sectie:

| Eigenschap | Description | Toegestane waarden | Verplicht |
| --- | --- | --- | --- |
| recursive |Hiermee geeft u op of S3 recursief moeten lijst objecten in de map. |waar/onwaar |Nee |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-voorbeeld: Gegevens kopiëren van Amazon S3 naar Azure Blob-opslag
Dit voorbeeld laat zien hoe u gegevens vanaf Amazon S3 kopiëren naar een Azure Blob-opslag. Echter, gegevens rechtstreeks naar kunnen worden gekopieerd [een van de sinks die worden ondersteund](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de kopieeractiviteit in Data Factory.

Het voorbeeld bevat JSON-definities voor de volgende Data Factory-entiteiten. U kunt deze definities om te maken van een pijplijn om gegevens te kopiëren van Amazon S3 naar Blob-opslag met behulp van de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Een gekoppelde service van het type [AwsAccessKey](#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Invoer [gegevensset](data-factory-create-datasets.md) van het type [Amazon S3](#dataset-properties).
* Uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met de kopieeractiviteit die gebruikmaakt van [FileSystemSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Het voorbeeld worden gegevens gekopieerd vanaf Amazon S3 met een Azure-blob elk uur. De JSON-eigenschappen die in deze voorbeelden worden beschreven in de secties na de voorbeelden.

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

Instellen van **"extern": true** informeert de Data Factory-service dat de gegevensset niet tot de data factory. Deze eigenschap wordt ingesteld op true voor een invoergegevensset die niet door een activiteit in de pijplijn wordt geproduceerd.

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

Gegevens worden geschreven naar een nieuwe blob elk uur (frequentie: uur en interval: 1). Het pad naar de map voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Pad naar de map maakt gebruik van het jaar, maand, dag en uur onderdelen van de begintijd.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Activiteit in een pijplijn met een Amazon S3-bron- en een blob kopiëren

De pijplijn bevat een kopieeractiviteit die is geconfigureerd voor het gebruik van de invoer- en uitvoergegevenssets en is gepland voor elk uur uitgevoerd. In de pijplijn-JSON-definitie heeft de **bron** type is ingesteld op **FileSystemSource**, en **sink** type is ingesteld op **BlobSink**.

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
> Zie het toewijzen van kolommen uit een brongegevensset op kolommen uit een sink-gegevensset [toewijzing van kolommen in Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* Zie voor meer informatie over de belangrijkste factoren die invloed prestaties van de verplaatsing van gegevens (kopieeractiviteit) in Data Factory en de verschillende manieren om te optimaliseren, de [en afstemmingshandleiding van activiteit kopiëren](data-factory-copy-activity-performance.md).

* Zie voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit de [zelfstudie Kopieeractiviteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
