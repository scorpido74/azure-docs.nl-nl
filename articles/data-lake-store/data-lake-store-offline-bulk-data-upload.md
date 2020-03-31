---
title: Grote gegevensset uploaden naar Azure Data Lake Storage Gen1 - offlinemethoden
description: De service Importeren/exporteren gebruiken om gegevens uit Azure Blob-opslag te kopiëren naar Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839296"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>De Azure Import/Export-service gebruiken voor offline kopiëren van gegevens naar Data Lake Storage Gen1

In dit artikel leert u hoe u enorme gegevenssets (>200 GB) kopieert naar Data Lake Storage Gen1 met behulp van offline kopieermethoden, zoals de [Azure Import/Export-service.](../storage/common/storage-import-export-service.md) Het bestand dat als voorbeeld in dit artikel wordt gebruikt, is 339.420.860.416 bytes of ongeveer 319 GB op schijf. Laten we dit bestand 319GB.tsv noemen.

Met de Azure Import/Export-service u grote hoeveelheden gegevens veiliger overbrengen naar Azure Blob-opslag door harde schijven naar een Azure-datacenter te verzenden.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure-opslagaccount**.
* **Een Azure Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.

## <a name="prepare-the-data"></a>De gegevens voorbereiden

Voordat u de service Importeren/exporteren gebruikt, moet u het gegevensbestand dat moet worden overgebracht naar kopieën met een grootte **van minder dan 200 GB** verbreken. Het importhulpprogramma werkt niet met bestanden van meer dan 200 GB. In dit artikel splitsen we het bestand in brokken van 100 GB per stuk. U dit doen met behulp van [Cygwin](https://cygwin.com/install.html). Cygwin ondersteunt Linux commando's. Gebruik in dit geval de volgende opdracht:

    split -b 100m 319GB.tsv

Met de gesplitste bewerking worden bestanden gemaakt met de volgende namen.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Maak schijven klaar met gegevens

Volg de instructies in [Het gebruik van de Azure Import/Export-service](../storage/common/storage-import-export-service.md) (onder de sectie Uw schijven **voorbereiden)** om uw harde schijven voor te bereiden. Hier is de algehele volgorde:

1. Een harde schijf aanschaffen die voldoet aan de vereiste om te worden gebruikt voor de Azure Import/Export-service.
2. Identificeer een Azure-opslagaccount waar de gegevens worden gekopieerd nadat deze naar het Azure-datacenter zijn verzonden.
3. Gebruik het [Azure Import/Export Tool](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), een command-line utility. Hier is een voorbeeldfragment dat laat zien hoe u het gereedschap gebruiken.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Zie [De azure import/export-service gebruiken](../storage/common/storage-import-export-service.md) voor meer voorbeeldfragmenten.
4. Met de vorige opdracht wordt een journaluurbestand op de opgegeven locatie ge. Gebruik dit logboekbestand om een importtaak te maken vanuit de [Azure-portal.](https://portal.azure.com)

## <a name="create-an-import-job"></a>Een importtaak maken

U nu een importtaak maken met behulp van de instructies in [De azure import/export-service gebruiken](../storage/common/storage-import-export-service.md) (onder de sectie Taak **importeren).** Voor deze importtaak, met andere details, moet u ook het logboekbestand opgeven dat is gemaakt tijdens de voorbereiding van de schijven.

## <a name="physically-ship-the-disks"></a>Fysiek verzenden van de schijven

U de schijven nu fysiek verzenden naar een Azure-datacenter. Daar worden de gegevens gekopieerd naar de Azure Storage-blobs die u hebt opgegeven tijdens het maken van de importtaak. Als u ervoor hebt gekozen om de trackinggegevens later te verstrekken, u nu tijdens het maken van de taak ook teruggaan naar uw importtaak en het trackingnummer bijwerken.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Gegevens van blobs kopiëren naar Data Lake Storage Gen1

Nadat de status van de importtaak heeft laten zien dat deze is voltooid, u controleren of de gegevens beschikbaar zijn in de azure-opslagblobs die u hebt opgegeven. U vervolgens verschillende methoden gebruiken om die gegevens van de blobs naar Azure Data Lake Storage Gen1 te verplaatsen. Zie Gegevens opnemen in Data Lake Storage Gen1 voor alle beschikbare opties voor het [uploaden van gegevens.](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1)

In deze sectie bieden we u de JSON-definities die u gebruiken om een Azure Data Factory-pijplijn te maken voor het kopiëren van gegevens. U deze JSON-definities gebruiken via de [Azure-portal](../data-factory/tutorial-copy-data-portal.md) of [Visual Studio.](../data-factory/tutorial-copy-data-dot-net.md)

### <a name="source-linked-service-azure-storage-blob"></a>Brongekoppelde service (Azure Storage blob)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Doelgekoppelde service (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Invoergegevensset

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Uitvoergegevensset

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Pijplijn (kopieeractiviteit)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Zie [Gegevens verplaatsen van Azure Storage blob naar Azure Data Lake Storage Gen1 met Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)voor meer informatie.

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>De gegevensbestanden reconstrueren in Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

We zijn begonnen met een bestand van 319 GB en hebben het opgesplitst in bestanden van kleiner formaat, zodat het kan worden overgedragen met behulp van de Azure Import/Export-service. Nu de gegevens zich in Azure Data Lake Storage Gen1 bevinden, kunnen we het bestand reconstrueren tot de oorspronkelijke grootte. U hiervoor de volgende Azure PowerShell-cmdlets gebruiken.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
