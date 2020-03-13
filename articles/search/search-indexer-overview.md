---
title: Indexeer functies voor het verkennen van gegevens tijdens het importeren
titleSuffix: Azure Cognitive Search
description: Verken Azure SQL database, Azure Cosmos DB of Azure Storage om Doorzoek bare gegevens op te halen en een Azure Cognitive Search-index te vullen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282989"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexeer functies in azure Cognitive Search

Een *Indexeer functie* in azure Cognitive Search is een crawler die Doorzoek bare gegevens en meta gegevens ophaalt uit een externe Azure-gegevens bron en een index vult op basis van veld-naar-veld Toewijzingen tussen de index en uw gegevens bron. Deze methode wordt ook wel ' pull model ' genoemd, omdat de service gegevens ophaalt zonder dat u code hoeft te schrijven waarmee gegevens worden toegevoegd aan een index.

Indexeer functies zijn gebaseerd op gegevens bron typen of-platformen, met afzonderlijke Indexeer functies voor SQL Server op Azure, Cosmos DB, Azure Table Storage en Blob Storage. Blob Storage-Indexeer functies hebben aanvullende eigenschappen die specifiek zijn voor BLOB-inhouds typen.

U kunt een indexeerfunctie gebruiken voor de opname van gegevens of een combinatie van technieken gebruiken, waaronder een indexeerfunctie voor het laden van slechts enkele velden in de index.

U kunt op aanvraag Indexeer functies of een periodiek schema voor gegevens vernieuwing uitvoeren dat wordt uitgevoerd op elke vijf minuten. Voor frequentere updates is een push model vereist waarmee gelijktijdig gegevens worden bijgewerkt in zowel Azure Cognitive Search als uw externe gegevens bron.

## <a name="approaches-for-creating-and-managing-indexers"></a>Strategieën voor het maken en beheren van indexeerfuncties

U kunt op de volgende manieren indexeerfuncties maken en beheren:

* [Wizard Portal-> gegevens importeren](search-import-data-portal.md)
* [Service-REST-API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Een nieuwe indexeerfunctie wordt in eerste instantie aangekondigd als preview-functie. Preview-functies worden geïntroduceerd in API's (REST en .NET) en vervolgens geïntegreerd in de portal nadat ze geleidelijk algemeen beschikbaar zijn gesteld. Als u een nieuwe indexeerfunctie evalueert, moet u er rekening mee houden dat u code moet schrijven.

## <a name="permissions"></a>Machtigingen

Voor alle bewerkingen met betrekking tot Indexeer functies, waaronder GET-aanvragen voor status of definities, is een [admin API-sleutel](search-security-api-keys.md)vereist. 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

Indexeer functies verkennen gegevens archieven in Azure.

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (in preview-versie)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server op virtuele machines in Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL Managed instances op Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Basisconfiguratiestappen
Indexeerfuncties kunnen functies bieden die uniek voor de gegevensbron zijn. In dit opzicht variëren bepaalde aspecten van de configuratie van de indexeerfunctie of de gegevensbron al naar gelang het type indexeerfunctie. Alle indexeerfuncties hebben echter dezelfde basissamenstelling en voor alle indexeerfuncties gelden dezelfde vereisten. Hieronder vindt u de stappen die voor alle indexeerfuncties gemeenschappelijk zijn.

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken
Met een Indexeer functie wordt een gegevens bron verbinding opgehaald van een *gegevens bron* object. De definitie van de gegevens bron biedt een connection string en mogelijk referenties. Roep de [Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) -rest API of- [klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) op om de resource te maken.

Gegevensbronnen worden geconfigureerd en onafhankelijk van de indexeerfuncties beheerd die gebruikmaken van de gegevensbronnen. Dit betekent dat een gegevensbron door meerdere indexeerfuncties kan worden gebruikt om tegelijkertijd meer dan één index te laden.

### <a name="step-2-create-an-index"></a>Stap 2: een index maken
Een indexeerfunctie automatiseert bepaalde taken met betrekking tot de opname van gegevens, maar het maken van een index behoort hier niet toe. Een vereiste is dat u een vooraf gedefinieerde index moet hebben met velden die overeenkomen met de velden in uw externe gegevensbron. Velden moeten overeenkomen met de naam en het gegevens type. Zie [Create a index (Azure Cognitive Search rest API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) of [index class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)(Engelstalig) voor meer informatie over het structureren van een index. Zie [veld toewijzingen in Azure Cognitive Search-Indexeer functies](search-indexer-field-mappings.md)voor hulp bij veld koppelingen.

> [!Tip]
> Hoewel indexeerfuncties een index niet voor u kunnen genereren, kan de wizard **Gegevens importeren** in de portal helpen. In de meeste gevallen kan de wizard een indexschema afleiden uit bestaande metagegevens in de bron, met een voorlopig indexschema dat u kunt bewerken terwijl de wizard actief is. Als de index eenmaal is aangemaakt op de service, blijven verdere bewerkingen in de portal meestal beperkt tot het toevoegen van nieuwe velden. Overweeg de wizard voor het maken, maar niet voor het herzien van een index. Doorloop het [Portaloverzicht](search-get-started-portal.md) om aan de slag te gaan.

### <a name="step-3-create-and-schedule-the-indexer"></a>Stap 3: de indexeerfunctie maken en plannen
De definitie van de Indexeer functie is een constructie waarmee alle elementen worden gecombineerd die zijn gerelateerd aan gegevens opname. De vereiste elementen bevatten een gegevens bron en index. Optionele elementen bevatten een schema-en veld toewijzingen. Veld toewijzing is alleen optioneel als bron velden en index velden duidelijk overeenkomen. Zie [Indexeer functie maken (Azure Cognitive Search rest API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)voor meer informatie over het structureren van een Indexeer functie.

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Indexeer functies op aanvraag uitvoeren

Hoewel het gebruikelijk is om het indexeren te plannen, kan een Indexeer functie ook op aanvraag worden aangeroepen met behulp van de [opdracht uitvoeren](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Wanneer run API wordt geretourneerd, is de aanroep van de Indexeer functie gepland, maar wordt de daad werkelijke verwerking asynchroon uitgevoerd. 

U kunt de status van de Indexeer functie bewaken in de portal of via de API van de Indexeer functie ophalen. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>De Indexeer functie-status ophalen

U kunt de status en de uitvoerings geschiedenis van een Indexeer functie ophalen met behulp van de [opdracht Get Indexing-status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Het antwoord bevat de algemene status van de Indexeer functie, de laatste (of in uitvoering zijnde) indexerings aanroep en de geschiedenis van recente Indexeer functie-aanroepen.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

De uitvoerings geschiedenis bevat tot de 50 meest recente voltooide uitvoeringen, die in omgekeerde chronologische volg orde worden gesorteerd (zodat de meest recente uitvoering eerst in het antwoord komt).

## <a name="next-steps"></a>Volgende stappen
Nu u het uitgangspunt hebt begrepen, is de volgende stap de vereisten en taken te bekijken die specifiek zijn voor elk gegevensbrontype.

* [Azure SQL Database of SQL Server op een virtuele Azure-machine](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [CSV-blobs indexeren met de Azure Cognitive Search BLOB-Indexer](search-howto-index-csv-blobs.md)
* [JSON-blobs indexeren met Azure Cognitive Search BLOB-Indexer](search-howto-index-json-blobs.md)
