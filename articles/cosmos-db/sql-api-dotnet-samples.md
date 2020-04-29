---
title: 'Azure Cosmos DB: .NET-voorbeelden voor de SQL API'
description: Vind C# .NET-voorbeelden op GitHub voor veelvoorkomende taken met de SQL-API in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 9b6635a0dab20c8122ad4df4b7fbc5a0cb6f4c99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71351133"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: .NET-voorbeelden voor de SQL API
> [!div class="op_single_selector"]
> * [.NET-voorbeelden](sql-api-dotnet-samples.md)
> * [Java-voorbeelden](sql-api-java-samples.md)
> * [Async Java-voorbeelden](sql-api-async-java-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Python-voor beelden](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

De GitHub-opslag plaats [Azure-Cosmos-DotNet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) bevat de meest recente .net-voorbeeld oplossingen voor het uitvoeren van ruwe en andere veelvoorkomende bewerkingen op Azure Cosmos DB-resources. Dit artikel bevat:

* Koppelingen naar de taken in elk van de C#-voorbeeldprojectbestanden. 
* Koppelingen naar het bijbehorende API-referentiemateriaal.

Zie de meest recente voor beelden in de GitHub-opslag plaats [Azure-Cosmos-DotNet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3) voor .NET SDK versie 3,0 (preview). 

## <a name="prerequisites"></a>Vereisten

Visual Studio 2019 met de Azure-Ontwikkel werk stroom geïnstalleerd
- U kunt de **gratis** [versie van Visual Studio 2019 Community](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio. 

Het [micro soft. Azure. DocumentDB NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Een Azure-abonnement of gratis Cosmos DB proef account
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- U kunt de [voor delen van Visual Studio-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): uw Visual Studio-abonnement geeft u elke maand tegoed, dat u kunt gebruiken voor betaalde Azure-Services.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> De voor beelden zijn op zichzelf staan en kunnen na zichzelf worden ingesteld en opgeruimd met meerdere aanroepen naar [CreateDocumentCollectionAsync ()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync). Elk exemplaar factureert uw abonnement gedurende één uur gebruik in de prestatie tier van uw verzameling. 
> 

## <a name="database-examples"></a>Voorbeelden voor databases
De methode [RunDatabaseDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L75-L91) van het *DatabaseManagement* -voorbeeld project laat zien hoe u de volgende taken kunt uitvoeren. Zie [werken met data bases, containers en items](databases-containers-items.md)voor meer informatie over Azure Cosmos-data bases voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een database maken](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L77) |[DocumentClient. CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) |
| [Een database lezen op id](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L79) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Alle databases lezen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L83) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Een database verwijderen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L89) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Voorbeelden voor verzamelingen
De methode [RunCollectionDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L86-L104) van het *CollectionManagement* -voorbeeld project laat zien hoe u de volgende taken kunt uitvoeren. Zie [werken met data bases, containers en items](databases-containers-items.md)voor meer informatie over Azure Cosmos-verzamelingen voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een verzameling maken](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L109) |[DocumentClient. CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync) 
| [Geconfigureerde prestaties van een verzameling wijzigen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L190) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Een verzameling ophalen op id](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L202) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Alle verzamelingen in een database lezen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L215) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Een verzameling verwijderen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L228) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Voorbeelden voor documenten
De methode [RunDocumentsDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L109-L118) van het *DocumentManagement* -voorbeeld project laat zien hoe u de volgende taken kunt uitvoeren. Zie [werken met data bases, containers en items](databases-containers-items.md)voor meer informatie over Azure Cosmos-documenten voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een document maken](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L154) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Een document lezen op basis van ID](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L168) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Alle documenten in een verzameling lezen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L190) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Een query uitvoeren voor documenten](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L18-L222) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Een document vervangen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L240-L242) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Een document invoegen of updaten](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L254) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Een document verwijderen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L275-L277) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [Werken met dynamische .NET-objecten](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L349-L397) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Document vervangen door voorwaardelijke ETag-controle](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L405-L501) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Document alleen lezen als het is gewijzigd](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L454-L500) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Voorbeelden van indexen
De methode [RunIndexDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L93-L115) van het *IndexManagement* -voorbeeld project laat zien hoe u de volgende taken kunt uitvoeren. Zie [index beleid](index-policy.md), [index typen](index-types.md)en [index paden](index-paths.md)voor meer informatie over het indexeren in azure Cosmos DB voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een document uitsluiten van de index](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L123-L162) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Luie indexering gebruiken](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L174-L192) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.documents.indexingpolicy.indexingmode) |
| [Opgegeven documentpaden uitsluiten van de index ](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L202-L263) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Een bereikscanbewerking voor een met hash geïndexeerd pad afdwingen](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Bereikindexen voor tekenreeksen gebruiken](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L265-L316) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Een indextransformatie uitvoeren](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L318-L370) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Georuimtelijke voorbeelden
De methode [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L94-L139) van het voor beeld *georuimtelijke* project laat zien hoe u de volgende taken kunt uitvoeren.  Zie [georuimtelijke en geojson-locatie gegevens gebruiken](geospatial.md)voor meer informatie over geojson en georuimtelijke gegevens voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Georuimtelijke indexering op een nieuwe verzameling inschakelen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L48) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Documenten met GeoJSON-punten invoegen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L104-L114) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Punten zoeken binnen een opgegeven afstand](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L158-L199) |[ST_DISTANCE](sql-query-st-distance.md) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Punten in een polygoon vinden](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L204-L227) |[ST_WITHIN](sql-query-st-within.md) </br> [GeometryOperationExtensions. binnen](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Polygoon](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Georuimtelijke indexering op een bestaande verzameling inschakelen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L385-L391) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [Point- en polygoongegevens valideren](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L290-L326) |[ST_ISVALID](sql-query-st-isvalid.md)<br>[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Voorbeelden van query's
De methode [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L64-L129) van het project voorbeeld *query's* laat zien hoe u de volgende taken uitvoert met behulp van de grammatica van de SQL-query, de Linq-provider met de query en Lambda. Zie [SQL query-voor beelden voor Azure Cosmos DB](how-to-sql-query.md)voor meer informatie over de SQL-query verwijzing in azure Cosmos DB voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een query uitvoeren voor alle documenten](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L131-L147) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query uitvoeren voor gelijkheid met behulp van ==](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L186-L198) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query uitvoeren voor ongelijkheid met behulp van != en NOT](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L288-L332) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query uitvoeren met behulp van bereikoperators zoals >, <, >=, <=](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L334-L355) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query uitvoeren met behulp van bereikoperators op tekenreeksen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L355-L370) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query met ORDER BY](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L422-L446) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query met statistische functies](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Werken met subdocumenten](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L498-L524) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query uitvoeren met Joins tussen documenten](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L526-L540) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query's met teken reeks-, reken kundige en matrix operators](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L636-L673) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query uitvoeren met SQL met parameters met behulp van SqlQuerySpec](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L149-L184) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Query uitvoeren met expliciete paginering](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L675-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query gelijktijdig uitvoeren op gepartitioneerde verzamelingen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L736-L807) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Query met volg orde op basis van gepartitioneerde verzamelingen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L809-L882) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Voorbeelden van wijzigingenfeeds 
De methode [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L54-L97) van het *ChangeFeed* -voorbeeld project laat zien hoe u de volgende taken kunt uitvoeren. Voor meer informatie over wijzigings invoer in Azure Cosmos DB voordat u de volgende voor beelden uitvoert, raadpleegt u [lezen Azure Cosmos DB feed wijzigen](read-change-feed.md) en de [feed-processor wijzigen](change-feed-processor.md). 

| Taak | API-verwijzing |
| --- | --- |
| [Wijzigingenfeed lezen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Partitiesleutelbereiken lezen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

In het voor beeld van een feed voor wijzigings voorbeeld, [ChangeFeedMigrationTool](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedMigrationTool)wordt getoond hoe u de wijzigings bibliotheek van de feed-processor gebruikt om gegevens te repliceren naar een andere Cosmos-container.   

## <a name="server-side-programming-examples"></a>Voorbeelden van programmering op de server
De methode [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L58-L91) van het *ServerSideScripts* -voorbeeld project laat zien hoe u de volgende taken kunt uitvoeren. Zie [opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)voor meer informatie over het Program meren van de server in azure Cosmos DB voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een opgeslagen procedure maken](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L110) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Een opgeslagen procedure uitvoeren](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L125) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Een documentfeed voor een opgeslagen procedure document lezen](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L198) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Een opgeslagen procedure maken met ORDER BY](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L223) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Een voorafgaande trigger maken](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L273) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Een navolgende trigger maken](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L341) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Een door de gebruiker gedefinieerde functie maken (UDF)](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L421) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Voorbeelden van gebruikersbeheer
De methode [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/UserManagement/Program.cs#L55-L129) van het *UserManagement* -voorbeeld project laat zien hoe u de volgende taken kunt uitvoeren:

| Taak | API-verwijzing |
| --- | --- |
| [Een gebruiker maken](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L93) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Machtigingen instellen voor een verzameling of document](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L97) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Een lijst met machtigingen van een gebruiker ophalen](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L241) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |

