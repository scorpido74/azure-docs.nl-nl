---
title: Gekoppelde services in Azure Data Factory
description: Meer informatie over gekoppelde services in Data Factory. Gekoppelde services koppelen compute/data stores aan datafabriek.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 90e51e8b56bd3fb63d56c630d47770e97f439796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563530"
---
# <a name="linked-services-in-azure-data-factory"></a>Gekoppelde services in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-create-datasets.md)
> * [Huidige versie](concepts-linked-services.md)

In dit artikel wordt beschreven wat gekoppelde services zijn, hoe ze zijn gedefinieerd in JSON-indeling en hoe ze worden gebruikt in Azure Data Factory-pijplijnen.

Zie [Inleiding tot Azure Data Factory](introduction.md) voor een overzicht als u nieuw bent in Data Factory.

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een **pijplijn** is een logische groepering van **activiteiten** die samen een taak uitvoeren. Met activiteiten in een pijplijn definieert u welk acties moeten worden uitgevoerd voor uw gegevens. U bijvoorbeeld een kopieeractiviteit gebruiken om gegevens van een on-premises SQL Server naar Azure Blob-opslag te kopiëren. Vervolgens u een Hive-activiteit gebruiken waarop een Hive-script wordt uitgevoerd op een Azure HDInsight-cluster om gegevens uit Blob-opslag te verwerken om uitvoergegevens te produceren. Ten slotte u een tweede kopieeractiviteit gebruiken om de uitvoergegevens naar Azure SQL Data Warehouse te kopiëren, waarop bi-rapportageoplossingen (business intelligence) worden gebouwd. Zie [Pijplijnen en activiteiten](concepts-pipelines-activities.md) in Azure Data Factory voor meer informatie over pijplijnen en activiteiten.

Nu is een **gegevensset** een benoemde weergave van gegevens die eenvoudig verwijst of verwijst naar de gegevens die u in uw **activiteiten** wilt gebruiken als invoer en uitvoer.

Voordat u een gegevensset maakt, moet u een **gekoppelde service** maken om uw gegevensarchief te koppelen aan de gegevensfabriek. Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. Bekijk het zo; de gegevensset vertegenwoordigt de structuur van de gegevens binnen de gekoppelde gegevensopslag en de gekoppelde service definieert de verbinding met de gegevensbron. Een azure storage-gekoppelde service koppelt bijvoorbeeld een opslagaccount aan de gegevensfabriek. Een Azure Blob-gegevensset vertegenwoordigt de blobcontainer en de map in dat Azure-opslagaccount met de invoerblobs die moeten worden verwerkt.

Hier is een voorbeeldscenario. Als u gegevens uit Blob-opslag wilt kopiëren naar een SQL-database, maakt u twee gekoppelde services: Azure Storage en Azure SQL Database. Maak vervolgens twee gegevenssets: Azure Blob-gegevensset (die verwijst naar de gekoppelde azure-opslagservice) en Azure SQL Table-gegevensset (die verwijst naar de gekoppelde azure-databaseservice). De gekoppelde Azure Storage- en Azure SQL Database-services bevatten verbindingstekenreeksen die Data Factory tijdens runtime gebruikt om verbinding te maken met respectievelijk uw Azure Storage en Azure SQL Database. De Azure Blob-gegevensset geeft de blobcontainer en blobmap op die de invoerblobs in uw Blob-opslag bevat. De Azure SQL Table-gegevensset geeft de SQL-tabel in uw SQL-database op waarnaar de gegevens moeten worden gekopieerd.

In het volgende diagram worden de relaties tussen pijplijn, activiteit, gegevensset en gekoppelde service in Gegevensfabriek weergegeven:

![Relatie tussen pijplijn, activiteit, gegevensset, gekoppelde services](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Gekoppelde service JSON
Een gekoppelde service in Data Factory wordt als volgt gedefinieerd in JSON-indeling:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

In de volgende tabel worden de eigenschappen in de bovenstaande JSON beschreven:

Eigenschap | Beschrijving | Vereist |
-------- | ----------- | -------- |
name | Naam van de gekoppelde service. Zie [Azure Data Factory - Naamgevingsregels](naming-rules.md). |  Ja |
type | Type van de gekoppelde service. Bijvoorbeeld: AzureStorage (datastore) of AzureBatch (compute). Zie de beschrijving voor typeEigenschappen. | Ja |
typeProperties | De typeeigenschappen zijn verschillend voor elk gegevensarchief of gegevensbestand. <br/><br/> Zie de [tabel met gegevenssets](concepts-datasets-linked-services.md#dataset-type) in dit artikel voor de ondersteunde gegevensarchieftypen en de teksteigenschappen daarvan. Navigeer naar het artikel voor de connector voor gegevensopslag om meer te weten te komen over teksteigenschappen die specifiek zijn voor een gegevensarchief. <br/><br/> Zie [Gekoppelde services berekenen](compute-linked-services.md)voor de ondersteunde rekentypen en hun typeeigenschappen. | Ja |
connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee

## <a name="linked-service-example"></a>Voorbeeld van gekoppelde service
De volgende gekoppelde service is een gekoppelde Azure Storage-service. Het type is ingesteld op AzureStorage. De teksteigenschappen voor de gekoppelde Azure Storage-service bevatten een verbindingstekenreeks. De service Data Factory gebruikt deze verbindingstekenreeks om verbinding te maken met het gegevensarchief tijdens runtime.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Gekoppelde services maken

U gekoppelde services maken met behulp van een van deze hulpprogramma's of SDK's: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Azure Resource Manager Template en Azure portal

## <a name="data-store-linked-services"></a>Gekoppelde diensten voor gegevensopslag
U vindt de lijst met gegevensopslag die wordt ondersteund door Data Factory in het artikel [over het connectoroverzicht.](copy-activity-overview.md#supported-data-stores-and-formats) Klik op een gegevensarchief om de ondersteunde verbindingseigenschappen te leren.

## <a name="compute-linked-services"></a>Gekoppelde services berekenen
[Referentiecompute-omgevingen die worden ondersteund](compute-linked-services.md) voor details over verschillende compute-omgevingen waarmee u verbinding maken vanuit uw gegevensfabriek en de verschillende configuraties.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudie voor stapsgewijze instructies voor het maken van pijplijnen en gegevenssets met behulp van een van deze hulpprogramma's of SDK's.

- [Snelstartgids: een gegevensfactory maken met .NET](quickstart-create-data-factory-dot-net.md)
- [Snelstart: een gegevensfabriek maken met PowerShell](quickstart-create-data-factory-powershell.md)
- [Snelstart: een gegevensfabriek maken met REST API](quickstart-create-data-factory-rest-api.md)
- [Snelstart: een gegevensfabriek maken met Azure-portal](quickstart-create-data-factory-portal.md)
