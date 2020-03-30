---
title: Gegevensfabriek - .NET API-wijzigingslogboek
description: Beschrijft het breken van wijzigingen, functietoevoegingen, bugfixes, enzovoort, in een specifieke versie van .NET API voor de Azure Data Factory.
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
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978999"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - .NET API-wijzigingslogboek
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

In dit artikel vindt u informatie over wijzigingen in Azure Data Factory SDK in een specifieke versie. U vindt [hier](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) het nieuwste NuGet-pakket voor Azure Data Factory

## <a name="version-4110"></a>Versie 4.11.0
Functietoevoegingen:

* De volgende gekoppelde servicetypen zijn toegevoegd:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* De volgende gegevenssettypen zijn toegevoegd:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* De volgende typen kopieerbronnen zijn toegevoegd:
  * [MongoDbBron](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versie 4.10.0
* De volgende optionele eigenschappen zijn toegevoegd aan TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* De volgende gekoppelde servicetypen zijn toegevoegd:
  * [OnpremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* De volgende gegevenssettypen zijn toegevoegd:
  * [OnpremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* De volgende typen kopieerbronnen zijn toegevoegd:
  * [CassandraBron CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* De eigenschap [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) toevoegen aan AzureMLBatchExecutionActivity
  * Meerdere webservice-ingangen doorschakelen naar een Azure Machine Learning-experiment

## <a name="version-491"></a>Versie 4.9.1
### <a name="bug-fix"></a>Bug fix
* WebApi-gebaseerde verificatie voor [WebLinkedService deprecate](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versie 4.9.0
### <a name="feature-additions"></a>Functietoevoegingen
* Eigenschappen [van EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) en [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) toevoegen aan CopyActivity. Zie [Gefaseerde kopie](data-factory-copy-activity-performance.md#staged-copy) voor meer informatie over de functie.

### <a name="bug-fix"></a>Bug fix
* Introduceer een overbelasting van [activityWindowOperationExtensions.List-methode,](https://msdn.microsoft.com/library/mt767915.aspx) waarbij een instantie [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) wordt uitgevoerd.
* Mark [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) en [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) als optioneel in CopySink.

## <a name="version-480"></a>Versie 4.8.0
### <a name="feature-additions"></a>Functietoevoegingen
* De volgende optionele eigenschappen zijn toegevoegd aan het type activiteit kopiëren om het afstemmen van kopieerprestaties mogelijk te maken:
  * [Parallelle kopieën](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versie 4.7.0
### <a name="feature-additions"></a>Functietoevoegingen
* Toegevoegd nieuwe StorageFormat type [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) type om bestanden te kopiëren in geoptimaliseerde rij columnar (ORC) formaat.
* Eigenschappen [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) en PolyBaseSettings toevoegen aan SqlDWSink.
  * Hiermee u het gebruik van PolyBase gebruiken om gegevens naar SQL Data Warehouse te kopiëren.

## <a name="version-461"></a>Versie 4.6.1
### <a name="bug-fixes"></a>Opgeloste fouten
* Hiermee worden HTTP-aanvragen voor activiteitsvensters verholpen.
  * Hiermee verwijdert u de naam van de resourcegroep en de naam van de gegevensfabriek uit de laadlading van het verzoek.

## <a name="version-460"></a>Versie 4.6.0
### <a name="feature-additions"></a>Functietoevoegingen
* De volgende eigenschappen zijn toegevoegd aan [PipelineProperties:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Gegevenssets](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* De volgende eigenschappen zijn toegevoegd aan [PipelineRuntimeInfo:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Het nieuwe [type JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) van [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) toegevoegd om gegevenssets te definiëren waarvan de gegevens in JSON-indeling zijn.

## <a name="version-450"></a>Versie 4.5.0
### <a name="feature-additions"></a>Functietoevoegingen
* [Lijstbewerkingen toegevoegd voor het activiteitenvenster](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Toegevoegde methoden om activiteitsvensters op te halen met filters op basis van de entiteitstypen (dat wil zeggen gegevensfabrieken, gegevenssets, pijplijnen en activiteiten).
* De volgende gekoppelde servicetypen zijn toegevoegd:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* De volgende gegevenssettypen zijn toegevoegd:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* De volgende typen kopieerbronnen zijn toegevoegd:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versie 4.4.0
### <a name="feature-additions"></a>Functietoevoegingen
* Het volgende gekoppelde servicetype is toegevoegd als gegevensbronnen en -putten voor kopieeractiviteiten:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Zie [Azure Storage SAS Linked Service](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) voor conceptuele informatie en voorbeelden.

## <a name="version-430"></a>Versie 4.3.0
### <a name="feature-additions"></a>Functietoevoegingen
* De volgende gekoppelde servicetypen zijn toegevoegd als gegevensbronnen voor kopieeractiviteiten:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Zie [Gegevens van HDFS verplaatsen met Gegevensfabriek](data-factory-hdfs-connector.md) voor conceptuele informatie en voorbeelden.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Zie [Gegevens verplaatsen van ODBC-gegevensarchieven met Azure Data Factory](data-factory-odbc-connector.md) voor conceptuele informatie en voorbeelden.

## <a name="version-420"></a>Versie 4.2.0
### <a name="feature-additions"></a>Functietoevoegingen
* Het volgende nieuwe activiteitstype is toegevoegd: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Zie [Azure ML-modellen bijwerken met de resourceactiviteit bijwerken](data-factory-azure-ml-batch-execution-activity.md)voor meer informatie over de activiteit .
* Er is een nieuwe optionele [eigenschapupdateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) toegevoegd aan de [klasse AzureMLLinkedService.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)
* [LongRunningOperationInitialTimeout-](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) en [LongRunningOperationRetryTimeout-eigenschappen](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) zijn toegevoegd aan de klasse [DataFactoryManagementClient.](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx)
* De configuratie van de time-outs voor clientgesprekken toestaan voor de Service Data Factory.

## <a name="version-410"></a>Versie 4.1.0
### <a name="feature-additions"></a>Functietoevoegingen
* De volgende gekoppelde servicetypen zijn toegevoegd:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* De volgende activiteitstypen zijn toegevoegd:
  * [DataLakeAnalyticsUSQLActiviteit](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* De volgende gegevenssettypen zijn toegevoegd:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* De volgende bron- en gootsteentypen voor Kopieeractiviteit zijn toegevoegd:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versie 4.0.1
### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
De volgende klassen zijn hernoemd. De nieuwe namen waren de originele namen van klassen vóór 4.0.0 versie.

| Naam in 4.0.0 | Naam in 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| Relationelegegevensset |[Relationele tabeldataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versie 4.0.0
### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
* De volgende klassen/interfaces zijn hernoemd.

| Oude naam | Nieuwe naam |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabel |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| Tabeleigenschappen |[GegevenssetEigenschappen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TabelTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TabelParameters voor createorupdate |[Parameters voor het maken van gegevens](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TabelCreateOrUpdate-reactie |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TabelGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TabellistResponse |[Reactie van datasetlist](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* De **methoden Lijst** geven nu paginaresultaten terug. Als het antwoord een niet-lege **NextLink-eigenschap** bevat, moet de clienttoepassing doorgaan met het ophalen van de volgende pagina totdat alle pagina's zijn geretourneerd.  Hier volgt een voorbeeld:

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
* **Lijstpijplijn-API** retourneert alleen de samenvatting van een pijplijn in plaats van volledige details. Activiteiten in een pijplijnoverzicht bevatten bijvoorbeeld alleen naam en type.

### <a name="feature-additions"></a>Functietoevoegingen
* De klasse [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) ondersteunt twee nieuwe eigenschappen, **SliceIdentifierColumnName** en **SqlWriterCleanupScript**, om idempotent-kopie naar Azure SQL Data Warehouse te ondersteunen. Zie het artikel [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) voor meer informatie over deze eigenschappen.
* We ondersteunen nu het uitvoeren van opgeslagen procedures tegen Azure SQL Database en Azure SQL Data Warehouse-bronnen als onderdeel van de kopieeractiviteit. De [sqlsource-](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) en [SqlDWSource-klassen](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) hebben de volgende eigenschappen: **SqlReaderStoredProcedureName** en **StoredProcedureParameters**. Zie de artikelen [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) en Azure SQL Data [Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) op Azure.com voor meer informatie over deze eigenschappen.  
