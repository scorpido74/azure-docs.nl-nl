---
title: Data Factory-.NET API-wijzigings logboek
description: Beschrijft de belang rijke wijzigingen, functie toevoegingen, fout oplossingen, enzovoort, in een specifieke versie van .NET API voor de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: b7936fcd1e4a629a813c4266920f6c34a15cf9b4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438939"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory-.NET API-wijzigings logboek
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

Dit artikel bevat informatie over wijzigingen in Azure Data Factory SDK in een specifieke versie. U kunt [hier](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) het meest recente NuGet-pakket voor Azure Data Factory vinden

## <a name="version-4110"></a>Versie 4.11.0
Toevoegingen van functies:

* De volgende gekoppelde service typen zijn toegevoegd:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* De volgende typen gegevensset zijn toegevoegd:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* De volgende Kopieer bron typen zijn toegevoegd:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versie 4.10.0
* De volgende optionele eigenschappen zijn toegevoegd aan TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* De volgende gekoppelde service typen zijn toegevoegd:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* De volgende typen gegevensset zijn toegevoegd:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* De volgende Kopieer bron typen zijn toegevoegd:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Eigenschap [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) toevoegen aan AzureMLBatchExecutionActivity
  * Inschakelen van meerdere web service-ingangen naar een Azure Machine Learning experiment

## <a name="version-491"></a>Versie 4.9.1
### <a name="bug-fix"></a>Bug oplossen
* WebApi-verificatie op basis van [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)op te afschaffing.

## <a name="version-490"></a>Versie 4.9.0
### <a name="feature-additions"></a>Functie toevoegingen
* Voeg de eigenschappen [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) en [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) toe aan CopyActivity. Zie [gefaseerde kopie](data-factory-copy-activity-performance.md#staged-copy) voor meer informatie over de functie.

### <a name="bug-fix"></a>Bug oplossen
* Een overbelasting van de methode [ActivityWindowOperationExtensions. list](https://msdn.microsoft.com/library/mt767915.aspx) introduceren, die een [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) -exemplaar gebruikt.
* Mark [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) en [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) als optioneel in CopySink.

## <a name="version-480"></a>Versie 4.8.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende optionele eigenschappen zijn toegevoegd aan het type Kopieer activiteit om het afstemmen van de Kopieer prestaties mogelijk te maken:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versie 4.7.0
### <a name="feature-additions"></a>Functie toevoegingen
* Het nieuwe StorageFormat type [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) -type is toegevoegd om bestanden te kopiëren in de indeling van de geoptimaliseerde rij in kolommen (Orc).
* Voeg de eigenschappen [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) en PolyBaseSettings toe aan SqlDWSink.
  * Hiermee schakelt u het gebruik van poly base in voor het kopiëren van gegevens naar Azure Synapse Analytics (voorheen SQL Data Warehouse).

## <a name="version-461"></a>Versie 4.6.1
### <a name="bug-fixes"></a>Opgeloste fouten
* Hiermee wordt HTTP-aanvraag voor Windows-aanbiedings activiteit opgelost.
  * Hiermee verwijdert u de naam van de resource groep en de naam van het data factory uit de aanvraag lading.

## <a name="version-460"></a>Versie 4.6.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende eigenschappen zijn toegevoegd aan [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Gegevenssets](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* De volgende eigenschappen zijn toegevoegd aan [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Het nieuwe [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) type [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) -type is toegevoegd voor het definiëren van gegevens sets met een JSON-indeling.

## <a name="version-450"></a>Versie 4.5.0
### <a name="feature-additions"></a>Functie toevoegingen
* [Lijst bewerkingen toegevoegd voor het activiteiten venster](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Er zijn methoden toegevoegd om activiteiten Vensters op te halen met filters op basis van de entiteits typen (dat wil zeggen, gegevens fabrieken, data sets, pijp lijnen en activiteiten).
* De volgende gekoppelde service typen zijn toegevoegd:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* De volgende typen gegevensset zijn toegevoegd:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* De volgende Kopieer bron typen zijn toegevoegd:     
  * [Websource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versie 4.4.0
### <a name="feature-additions"></a>Functie toevoegingen
* Het volgende gekoppelde service type is toegevoegd als gegevens bronnen en sinks voor kopieer activiteiten:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Zie [Azure Storage gekoppelde SAS-service](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) voor conceptuele informatie en voor beelden.

## <a name="version-430"></a>Versie 4.3.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende gekoppelde service typen recent zijn toegevoegd als gegevens bronnen voor kopieer activiteiten:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Zie [gegevens verplaatsen van HDFS met Data Factory](data-factory-hdfs-connector.md) voor conceptuele informatie en voor beelden.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Zie [gegevens verplaatsen van ODBC-gegevens archieven met behulp van Azure Data Factory](data-factory-odbc-connector.md) voor conceptuele informatie en voor beelden.

## <a name="version-420"></a>Versie 4.2.0
### <a name="feature-additions"></a>Functie toevoegingen
* Het volgende nieuwe type activiteit is toegevoegd: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Zie [Azure ml-modellen bijwerken met de activiteit resource bijwerken](data-factory-azure-ml-batch-execution-activity.md)voor meer informatie over de activiteit.
* Er is een nieuwe optionele eigenschap [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) toegevoegd aan de [klasse AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* De eigenschappen [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) en [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) zijn toegevoegd aan de klasse [datafactorymanagementclient te maken](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) .
* Configuratie van de time-outs voor client aanroepen naar de Data Factory-Service toestaan.

## <a name="version-410"></a>Versie 4.1.0
### <a name="feature-additions"></a>Functie toevoegingen
* De volgende gekoppelde service typen zijn toegevoegd:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* De volgende typen activiteiten zijn toegevoegd:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* De volgende typen gegevensset zijn toegevoegd:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* De volgende bron-en Sink-typen voor kopieer activiteiten zijn toegevoegd:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versie 4.0.1
### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
De naam van de volgende klassen is gewijzigd. De nieuwe namen bevinden zich in de oorspronkelijke namen van klassen vóór de release van 4.0.0.

| Naam in 4.0.0 | Naam in 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versie 4.0.0
### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
* De naam van de volgende klassen/interfaces is gewijzigd.

| Oude naam | Nieuwe naam |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabel |[Gegevensset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* De **lijst** methoden retour neren nu pagina resultaten. Als het antwoord een niet-lege eigenschap **Nextlink** bevat, moet de client toepassing de volgende pagina blijven ophalen totdat alle pagina's zijn geretourneerd.  Hier volgt een voorbeeld:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Lijst** PIJPLIJN-API retourneert alleen de samen vatting van een pijp lijn in plaats van volledige details. Bijvoorbeeld: activiteiten in een pijp lijn-samen vatting bevatten alleen naam en type.

### <a name="feature-additions"></a>Functie toevoegingen
* De klasse [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) ondersteunt twee nieuwe eigenschappen, **SliceIdentifierColumnName** en **SqlWriterCleanupScript**, ter ondersteuning van idempotent-kopie naar Azure Azure Synapse Analytics. Zie het artikel over [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) voor meer informatie over deze eigenschappen.
* We ondersteunen nu het uitvoeren van een opgeslagen procedure voor Azure SQL Database en Azure Synapse Analytics-bronnen als onderdeel van de Kopieer activiteit. De klassen [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) en [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) hebben de volgende eigenschappen: **SqlReaderStoredProcedureName** en **StoredProcedureParameters**. Raadpleeg de [Azure SQL database](data-factory-azure-sql-connector.md#sqlsource) -en [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) -artikelen over Azure.com voor meer informatie over deze eigenschappen.  
