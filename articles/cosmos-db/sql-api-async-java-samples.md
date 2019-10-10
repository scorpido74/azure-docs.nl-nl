---
title: 'Azure Cosmos DB: Async Java-voorbeelden voor de SQL API'
description: Vind Async Java-voorbeelden op GitHub voor veelvoorkomende taken met de SQL API in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 12/03/2018
ms.author: sngun
ms.openlocfilehash: 0a9b58a78ee9de48b721511646728bd8140ef980
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170184"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Async Java-voorbeelden voor de SQL API

> [!div class="op_single_selector"]
> * [Voor beelden van .NET v2 SDK](sql-api-dotnet-samples.md)
> * [Voor beelden van .NET v3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Java-voorbeelden](sql-api-java-samples.md)
> * [Async Java-voorbeelden](sql-api-async-java-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Python-voorbeelden](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

De nieuwste voorbeeldtoepassingen waarmee CRUD-bewerkingen en andere veelvoorkomende bewerkingen in Azure Cosmos DB-resources worden uitgevoerd, zijn opgenomen in de GitHub-opslagplaats [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java). Dit artikel bevat:

* Koppelingen naar de taken in elk van de Async Java-voorbeeldprojectbestanden. 
* Koppelingen naar het bijbehorende API-referentiemateriaal.

**Vereisten**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- U kunt de [voordelen voor Visual Studio-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): via uw Visual Studio-abonnement ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

U hebt het volgende nodig om deze voorbeeldtoepassing uit te voeren:

* Java Development Kit 8
* Microsoft Azure Cosmos DB Java SDK

U kunt eventueel Maven gebruiken om de recentste binaire Microsoft Azure Cosmos DB Java SDK-bestanden op te halen voor gebruik in uw project. U kunt [hier](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) de recentste versie kiezen. Maven voegt automatisch eventuele vereiste afhankelijkheden toe. Anders kunt u de afhankelijkheden die worden vermeld in het bestand pom.xml, rechtstreeks downloaden en toevoegen aan uw build-pad.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**De voorbeeldtoepassingen uitvoeren**

De voorbeeldopslagplaats klonen:
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

U kunt de voorbeelden uitvoeren met behulp van Eclipse of vanaf de opdrachtregel met behulp van Maven.

Vanaf Eclipse uitvoeren:
* Laad het bovenliggende hoofdprojectbestand pom.xml in Eclipse. De azure-cosmosdb-voorbeelden moeten automatisch worden geladen.
* Voor het uitvoeren van de voorbeelden hebt u een geldig Azure Cosmos DB-eindpunt nodig. De eindpunten worden gelezen uit `src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java`.
* U kunt de eindpuntreferenties als VM-argumenten in Eclipse JUnit Run Config doorgeven of u kunt de eindpuntreferenties in TestConfigurations.java plaatsen.
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* U kunt nu de voorbeelden uitvoeren als JUnit-tests in Eclipse.

Uitvoeren vanaf de opdrachtregel:
* U kunt voorbeelden ook uitvoeren met behulp van Maven:
* Voer Maven uit en geef uw Azure Cosmos DB-eindpuntreferenties door:
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > Elk voorbeeld staat op zichzelf. Het stelt zichzelf in en aan het einde worden de gegevens automatisch opgeschoond. In de voorbeelden wordt [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) meerdere keren aangeroepen. Telkens wanneer dit wordt gedaan, wordt uw abonnement gefactureerd voor 1 uur gebruik voor de prestatielaag van de gemaakte verzameling. 
   > 
   > 

## <a name="database-examples"></a>Voorbeelden voor databases
In het [DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) -bestand ziet u hoe u de volgende taken uitvoert. Zie [werken met data bases, containers en](databases-containers-items.md) artikel concepten voor meer informatie over de Azure Cosmos-data bases voordat u de volgende voor beelden uitvoert. 

| Taak | API-referentie |
| --- | --- |
| [Een database maken](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [Kan geen database maken die al bestaat](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [Een database maken en lezen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [Een database maken en verwijderen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [Een database maken en een query erop uitvoeren](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>Voorbeelden voor verzamelingen
In het [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) -bestand ziet u hoe u de volgende taken uitvoert. Zie [werken met data bases, containers en](databases-containers-items.md) artikel concepten voor meer informatie over de Azure Cosmos-verzamelingen voordat u de volgende voor beelden uitvoert. 

| Taak | API-referentie |
| --- | --- |
| [Een verzameling met één partitie maken](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [Een aangepaste verzameling met meerdere partities maken](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [Kan geen verzameling maken die al bestaat](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [Een verzameling maken en lezen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [Een verzameling maken en verwijderen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [Een verzameling maken en een query erop uitvoeren](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>Voorbeelden voor documenten
In het [DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) -bestand ziet u hoe u de volgende taken uitvoert. Zie [werken met data bases, containers en](databases-containers-items.md) artikel concepten voor meer informatie over de Azure Cosmos-documenten voordat u de volgende voor beelden uitvoert. 

| Taak | API-referentie |
| --- | --- |
| [Een document maken](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [Een document maken met een programmeerbare documentdefinitie](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [Document](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [Documenten maken en de totale RU-kosten vinden](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [Kan geen document maken dat al bestaat](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [Een document maken en vervangen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [Een document maken en invoegen of bijwerken](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [Een document maken en verwijderen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [Een document maken en lezen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>Voorbeelden van indexen
In het [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) -bestand ziet u hoe u de volgende taken uitvoert.  Zie [Indexing policies](index-policy.md), [Indexing types](index-types.md)en [Indexing Path](index-paths.md) conceptuele articles (Engelstalig) voor meer informatie over het indexeren in azure Cosmos DB voordat u de volgende voor beelden uitvoert. 

| Taak | API-referentie |
| --- | --- |
| [Een index maken en een indexeringsbeleid instellen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

Zie [Azure Cosmos DB-indexeringsbeleid](index-policy.md) voor meer informatie over indexering.

## <a name="query-examples"></a>Voorbeelden van query's
In het [DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) -bestand ziet u hoe u de volgende taken uitvoert. Zie het conceptuele artikel over [SQL query-voor beelden](how-to-sql-query.md) voor meer informatie over de SQL-query verwijzing in azure Cosmos DB voordat u de volgende voor beelden uitvoert. 

| Taak | API-referentie |
| --- | --- |
| [Een eenvoudig documentquery uitvoeren](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [Een eenvoudige documentquery uitvoeren en de totale RU-kosten vinden](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [Een eenvoudige documentquery uitvoeren, één pagina lezen en afmelden voor de geretourneerde observeerbare parameter](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [Een eenvoudige documentquery uitvoeren en de resultaten filteren](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [Een document query sorteren op basis van een kruis partitie](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

Zie [SQL-query in Azure Cosmos DB](how-to-sql-query.md) voor meer informatie over het schrijven van query's.

## <a name="offer-examples"></a>Voorbeelden van aanbieding
Het bestand [OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) laat u zien hoe u de volgende taken uitvoert:

| Taak | API-referentie |
| --- | --- |
| [Een verzameling maken en doorvoer instellen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [Een verzameling lezen om de bijbehorende aanbieding te vinden](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [De doorvoer van een verzameling bijwerken door zijn aanbieding te vervangen](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Voorbeelden van opgeslagen procedures
In het [StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) -bestand ziet u hoe u de volgende taken uitvoert. Zie voor meer informatie over het Program meren van de server in Azure Cosmos DB voordat u de volgende voor beelden uitvoert, [opgeslagen procedures, triggers en](stored-procedures-triggers-udfs.md) conceptueel artikel over door de gebruiker gedefinieerde functies. 

| Taak | API-referentie |
| --- | --- |
| [Een opgeslagen procedure maken en uitvoeren](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [Een opgeslagen procedure met argumenten maken en uitvoeren](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [Een opgeslagen procedure met een objectargument maken en uitvoeren](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>Unieke sleutel
In het [UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) -bestand ziet u hoe u de volgende taken uitvoert. Zie voor meer informatie over de unieke sleutels in Azure Cosmos DB voordat u de volgende voor beelden uitvoert, het conceptuele artikel over [unieke sleutel beperkingen](unique-keys.md) . 

| Taak | API-referentie |
| --- | --- |
| [Een verzameling met een unieke sleutel maken](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |
