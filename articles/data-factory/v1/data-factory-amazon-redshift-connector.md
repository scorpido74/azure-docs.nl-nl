---
title: Gegevens van Amazon Redshift verplaatsen met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens van Amazon Redshift met Azure Data Factory Copy Activity.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c2e2394bbcee5294bfb752a0af2969457ffff0ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260525"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Gegevens verplaatsen van Amazon Redshift met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-amazon-redshift-connector.md)
> * [Versie 2 (huidige versie)](../connector-amazon-redshift.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [amazon Redshift-connector in V2](../connector-amazon-redshift.md).

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Amazon Redshift te verplaatsen. Het artikel bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van Amazon Redshift naar een [ondersteunde sink data store](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Het verplaatsen van gegevens van andere gegevenswinkels naar Amazon Redshift wordt niet ondersteund.

> [!TIP]
> Om de beste prestaties te bereiken bij het kopiëren van grote hoeveelheden gegevens van Amazon Redshift, u overwegen de ingebouwde Redshift **UNLOAD-opdracht** te gebruiken via Amazon Simple Storage Service (Amazon S3). Zie UNLOAD [gebruiken om gegevens van Amazon Redshift te kopiëren](#use-unload-to-copy-data-from-amazon-redshift)voor meer informatie.

## <a name="prerequisites"></a>Vereisten
* Als u gegevens verplaatst naar een on-premises gegevensopslag, installeert u [Data Management Gateway](data-factory-data-management-gateway.md) op een on-premises machine. Geef toegang voor een gateway naar het Amazon Redshift-cluster met behulp van het ip-adres van de on-premises machine. Zie Toegang [tot het cluster autoriseren voor](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)instructies .
* Zie het [ID-adres en sql-bereiken van Compute die worden gebruikt door de Microsoft Azure-datacenters](https://www.microsoft.com/download/details.aspx?id=41653)om gegevens naar een Azure-gegevensarchief te verplaatsen.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit om gegevens van een Amazon Redshift-bron te verplaatsen met behulp van verschillende hulpprogramma's en API's.

De eenvoudigste manier om een pijplijn te maken, is door de wizard Kopiëren van Azure Data Factory te gebruiken. Zie de [pijplijn Zelfstudie: Een pijplijn maken met de wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle walkthrough voor het maken van een pijplijn met de wizard Kopiëren .

U ook een pijplijn maken met Visual Studio, Azure PowerShell of andere hulpprogramma's. Azure Resource Manager-sjablonen, de .NET-API of de REST-API kunnen ook worden gebruikt om de pijplijn te maken. Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijplijn met een kopieeractiviteit.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak gekoppelde services om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak gegevenssets om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een pijplijn met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard Kopiëren gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten automatisch gemaakt. Wanneer u hulpprogramma's of API's gebruikt (behalve de .NET API), definieert u de entiteiten van de gegevensfabriek met behulp van de JSON-indeling. Het JSON-voorbeeld: kopieer gegevens van Amazon Redshift naar Azure Blob-opslag toont de JSON-definities voor de entiteiten van de Gegevensfabriek die worden gebruikt om gegevens uit een Amazon Redshift-gegevensarchief te kopiëren.

In de volgende secties worden de JSON-eigenschappen beschreven die worden gebruikt om de entiteiten van de gegevensfabriek voor Amazon Redshift te definiëren.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

In de volgende tabel vindt u beschrijvingen voor de JSON-elementen die specifiek zijn voor een door Amazon Redshift gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **Type** |Deze accommodatie moet worden ingesteld op **AmazonRedshift.** |Ja |
| **Server** |Het IP-adres of de hostnaam van de Amazon Redshift-server. |Ja |
| **Poort** |Het nummer van de TCP-poort dat de Amazon Redshift-server gebruikt om naar clientverbindingen te luisteren. |Nee (standaard is 5439) |
| **database** |De naam van de Amazon Redshift database. |Ja |
| **Gebruikersnaam** |De naam van de gebruiker die toegang heeft tot de database. |Ja |
| **wachtwoord** |Het wachtwoord voor het gebruikersaccount. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. De **structuur,** **beschikbaarheid**en **beleidssecties** zijn vergelijkbaar voor alle gegevenssettypen. Voorbeelden van gegevenssettypen zijn Azure SQL, Azure Blob-opslag en Azure Table-opslag.

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het archief. De sectie **typeEigenschappen** voor een gegevensset van type **RelationalTable**, die de Amazon Redshift-gegevensset bevat, heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **tableName** |De naam van de tabel in de Amazon Redshift-database waarnaar de gekoppelde service verwijst. |Nee (als de **eigenschap query** van een kopieeractiviteit van het type **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. De **naam,** **beschrijving,** **invoertabel,** **uitvoertabel** en **beleidseigenschappen** zijn beschikbaar voor alle soorten activiteiten. De eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen,** verschillen per activiteitstype. Voor Kopieeractiviteit zijn de eigenschappen afhankelijk van de typen gegevensbronnen en -putten.

Voor Kopieeractiviteit, wanneer de bron van het type **AmazonRedshiftSource**is, zijn de volgende eigenschappen beschikbaar in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **Query** | Gebruik de aangepaste query om de gegevens te lezen. |Nee (als de eigenschap **tableName** van een gegevensset is opgegeven) |
| **instellingen voor roodschakelen** | Bevat de eigenschappengroep wanneer u de opdracht **Redshift UNLOAD gebruikt.** | Nee |
| **s3LinkedServiceName** | De Amazon S3 te gebruiken als een tussentijdse winkel. De gekoppelde service wordt opgegeven met behulp van een Azure Data Factory-naam van het type **AwsAccessKey**. | Vereist bij het gebruik van de eigenschap **redshiftUnloadSettings** |
| **bucketName** | Geeft de Amazon S3-bucket aan die moet worden gebruikt om de tussentijdse gegevens op te slaan. Als deze eigenschap niet is verstrekt, genereert Copy Activity automatisch een bucket. | Vereist bij het gebruik van de eigenschap **redshiftUnloadSettings** |

U ook het **relationalsource-type,** dat Amazon Redshift bevat, gebruiken met de volgende eigenschap in de sectie **typeEigenschappen.** Houd er rekening mee dat dit brontype de opdracht Redshift **UNLOAD** niet ondersteunt.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **Query** |Gebruik de aangepaste query om de gegevens te lezen. | Nee (als de eigenschap **tableName** van een gegevensset is opgegeven) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>UNLOAD gebruiken om gegevens van Amazon Redshift te kopiëren

De opdracht Amazon Redshift [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) lost de resultaten van een query naar een of meer bestanden op Amazon S3. Deze opdracht wordt aanbevolen door Amazon voor het kopiëren van grote gegevenssets van Redshift.

**Voorbeeld: Gegevens van Amazon Redshift kopiëren naar Azure SQL Data Warehouse**

In dit voorbeeld worden gegevens van Amazon Redshift overgesmeerd naar Azure SQL Data Warehouse. In het voorbeeld wordt de opdracht Redshift **UNLOAD,** gefaseerde kopieergegevens en Microsoft PolyBase gebruikt.

Voor deze voorbeeldgebruikscase ontlaadt Copy Activity eerst de gegevens van Amazon Redshift naar Amazon S3 zoals geconfigureerd in de optie **roodverschuivingUitname.** Vervolgens worden de gegevens gekopieerd van Amazon S3 naar Azure Blob-opslag zoals opgegeven in de optie **StagingSettings.** Ten slotte laadt PolyBase de gegevens in SQL Data Warehouse. Alle tussentijdse indelingen worden verwerkt door Copy Activity.

![Werkstroom kopiëren van Amazon Redshift naar SQL Data Warehouse](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-voorbeeld: Gegevens van Amazon Redshift kopiëren naar Azure Blob-opslag
In dit voorbeeld ziet u hoe u gegevens uit een Amazon Redshift-database kopiëren naar Azure Blob Storage. Gegevens kunnen rechtstreeks worden gekopieerd naar elke [ondersteunde gootsteen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van Kopieeractiviteit.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

* Een gekoppelde service van het type [AmazonRedshift](#linked-service-properties)
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van type [RelationalTable](#dataset-properties)
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die de eigenschappen [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) gebruikt

Het voorbeeld kopieert gegevens uit een queryresultaat in Amazon Redshift naar een Azure-blob per uur. De JSON-eigenschappen die in het monster worden gebruikt, worden beschreven in de secties die de entiteitsdefinities volgen.

**Amazon Redshift gekoppelde service**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Gekoppelde Azure Blob-opslagservice**

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
**Amazon Redshift-invoergegevensset**

De **externe** eigenschap is ingesteld op "true" om de Data Factory-service te informeren dat de gegevensset zich buiten de gegevensfabriek bevindt. Deze eigenschapsinstelling geeft aan dat de gegevensset niet wordt geproduceerd door een activiteit in de gegevensfabriek. Stel de eigenschap in op true op een invoergegevensset die niet wordt geproduceerd door een activiteit in de pijplijn.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe blob geschreven door de **eigenschap frequency-eigenschap** in te stellen op 'Uur' en de eigenschap **interval** op 1. De eigenschap **folderPath** voor de blob wordt dynamisch geëvalueerd. De eigenschapswaarde is gebaseerd op de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt de delen van de begintijd jaar, maand, dag en uur.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Activiteit in een pijplijn kopiëren met een Azure Redshift-bron (van het type RelationalSource) en een Azure Blob-sink**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken. De pijplijn zal elk uur worden uitgevoerd. In de JSON-definitie voor de pijplijn wordt het **brontype** ingesteld op **RelationalSource** en wordt het **gootsteentype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **eigenschap query** selecteert de gegevens die u wilt kopiëren van het afgelopen uur.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Type mapping voor Amazon Redshift
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert Kopieeractiviteit automatische typeconversies uit van brontype naar gootsteentype. De typen worden geconverteerd met behulp van een aanpak in twee stappen:

1. Converteren van een native brontype naar een .NET-type
2. Converteren van een .NET-type naar een native gootsteentype

De volgende toewijzingen worden gebruikt wanneer Kopieeractiviteit de gegevens van een Amazon Redshift-type converteert naar een .NET-type:

| Amazon Redshift type | .NET-type |
| --- | --- |
| Smallint |Int16 |
| INTEGER |Int32 |
| Bigint |Int64 |
| Decimaal |Decimal |
| REAL |Enkel |
| DUBBELE PRECISIE |Double |
| Booleaanse |Tekenreeks |
| Char |Tekenreeks |
| Varchar |Tekenreeks |
| DATE |DateTime |
| Tijdstempel |DateTime |
| TEXT |Tekenreeks |

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)toewijzen voor meer informatie over het toewijzen van kolommen in de brongegevensset aan kolommen in de sink-gegevensset.

## <a name="repeatable-reads-from-relational-sources"></a>Herhaalbare leest uit relationele bronnen
Wanneer u gegevens uit een relationeel gegevensarchief kopieert, moet u rekening houden met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het **beleid** voor het opnieuw proberen configureren voor een gegevensset om een segment opnieuw uit te voeren wanneer er een fout optreedt. Zorg ervoor dat dezelfde gegevens worden gelezen, ongeacht hoe vaak het segment opnieuw wordt uitgevoerd. Zorg er ook voor dat dezelfde gegevens worden gelezen, ongeacht hoe u het segment opnieuw uitvoert. Zie [Herhaalbare leest uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Meer informatie over belangrijke factoren die van invloed zijn op de prestaties van kopieeractiviteit en manieren om de prestaties te optimaliseren in de [handleiding voor activiteitsprestaties en -afstemming](data-factory-copy-activity-performance.md)kopiëren .

## <a name="next-steps"></a>Volgende stappen
Zie de [zelfstudie Activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijplijn met Kopieeractiviteit.
