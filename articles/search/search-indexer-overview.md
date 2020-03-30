---
title: Indexers voor het crawlen van gegevens tijdens het importeren
titleSuffix: Azure Cognitive Search
description: Crawl Azure SQL-database, Azure Cosmos DB of Azure-opslag om doorzoekbare gegevens te extraheren en een Azure Cognitive Search-index te vullen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282989"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexeerfuncties in Azure Cognitive Search

Een *indexer in* Azure Cognitive Search is een crawler die doorzoekbare gegevens en metagegevens uit een externe Azure-gegevensbron haalt en een index vult op basis van veld-naar-veldtoewijzingen tussen de index en uw gegevensbron. Deze aanpak wordt soms aangeduid als een 'pull-model' omdat de service gegevens binnenhaalt zonder dat u code hoeft te schrijven die gegevens toevoegt aan een index.

Indexers zijn gebaseerd op gegevensbrontypen of -platforms, met individuele indexers voor SQL Server op Azure, Cosmos DB, Azure Table Storage en Blob Storage. Blob-opslagindexeerders hebben extra eigenschappen die specifiek zijn voor blob-inhoudstypen.

U kunt een indexeerfunctie gebruiken voor de opname van gegevens of een combinatie van technieken gebruiken, waaronder een indexeerfunctie voor het laden van slechts enkele velden in de index.

U indexeerders op aanvraag of op een terugkerend schema voor het vernieuwen van gegevens uitvoeren dat zo vaak wordt uitgevoerd als elke vijf minuten. Voor frequentere updates is een pushmodel vereist dat tegelijkertijd gegevens bijwerkt in zowel Azure Cognitive Search als uw externe gegevensbron.

## <a name="approaches-for-creating-and-managing-indexers"></a>Strategieën voor het maken en beheren van indexeerfuncties

U kunt op de volgende manieren indexeerfuncties maken en beheren:

* [Wizard > Portal > importeren](search-import-data-portal.md)
* [Service REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Een nieuwe indexeerfunctie wordt in eerste instantie aangekondigd als preview-functie. Preview-functies worden geïntroduceerd in API's (REST en .NET) en vervolgens geïntegreerd in de portal nadat ze geleidelijk algemeen beschikbaar zijn gesteld. Als u een nieuwe indexeerfunctie evalueert, moet u er rekening mee houden dat u code moet schrijven.

## <a name="permissions"></a>Machtigingen

Voor alle bewerkingen met betrekking tot indexeerders, inclusief GET-aanvragen voor status of definities, is een [api-sleutel](search-security-api-keys.md)voor beheerders vereist. 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

Indexers crawlen gegevensopslag op Azure.

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (in preview)
* [Azure-tabelopslag](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server op virtuele machines in Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL Managed-exemplaren op Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Basisconfiguratiestappen
Indexeerfuncties kunnen functies bieden die uniek voor de gegevensbron zijn. In dit opzicht variëren bepaalde aspecten van de configuratie van de indexeerfunctie of de gegevensbron al naar gelang het type indexeerfunctie. Alle indexeerfuncties hebben echter dezelfde basissamenstelling en voor alle indexeerfuncties gelden dezelfde vereisten. Hieronder vindt u de stappen die voor alle indexeerfuncties gemeenschappelijk zijn.

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken
Een indexer verkrijgt gegevensbronverbinding van een *gegevensbronobject.* De definitie van de gegevensbron biedt een verbindingstekenreeks en mogelijk referenties. Roep de klasse [Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API of [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) aan om de bron te maken.

Gegevensbronnen worden geconfigureerd en onafhankelijk van de indexeerfuncties beheerd die gebruikmaken van de gegevensbronnen. Dit betekent dat een gegevensbron door meerdere indexeerfuncties kan worden gebruikt om tegelijkertijd meer dan één index te laden.

### <a name="step-2-create-an-index"></a>Stap 2: een index maken
Een indexeerfunctie automatiseert bepaalde taken met betrekking tot de opname van gegevens, maar het maken van een index behoort hier niet toe. Een vereiste is dat u een vooraf gedefinieerde index moet hebben met velden die overeenkomen met de velden in uw externe gegevensbron. Velden moeten overeenkomen op naam en gegevenstype. Zie [Een index maken (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) of [Indexklasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)voor meer informatie over het structureren van een index. Zie [Veldtoewijzingen in Azure Cognitive Search-indexeerders](search-indexer-field-mappings.md)voor hulp bij veldkoppelingen.

> [!Tip]
> Hoewel indexeerfuncties een index niet voor u kunnen genereren, kan de wizard **Gegevens importeren** in de portal helpen. In de meeste gevallen kan de wizard een indexschema afleiden uit bestaande metagegevens in de bron, met een voorlopig indexschema dat u kunt bewerken terwijl de wizard actief is. Als de index eenmaal is aangemaakt op de service, blijven verdere bewerkingen in de portal meestal beperkt tot het toevoegen van nieuwe velden. Overweeg de wizard voor het maken, maar niet voor het herzien van een index. Doorloop het [Portaloverzicht](search-get-started-portal.md) om aan de slag te gaan.

### <a name="step-3-create-and-schedule-the-indexer"></a>Stap 3: de indexeerfunctie maken en plannen
De indexerdefinitie is een constructie die alle elementen met betrekking tot gegevensopname samenbrengt. Vereiste elementen zijn een gegevensbron en index. Optionele elementen omvatten een planning en veldtoewijzingen. Veldtoewijzing is alleen optioneel als bronvelden en indexvelden duidelijk overeenkomen. Zie [Indexeren maken (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)voor meer informatie over het structureren van een indexeerder.

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Indexers on-demand uitvoeren

Hoewel het gebruikelijk is om indexering te plannen, kan een indexer ook op aanvraag worden aangeroepen met de [opdracht Uitvoeren:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Wanneer RUN API retourneert, is de aanroep van de indexer gepland, maar de werkelijke verwerking gebeurt asynchroon. 

U de indexerstatus in de portal of via Get Indexer Status API controleren. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Indexerstatus krijgen

U de status- en uitvoeringsgeschiedenis van een indexeerder ophalen via de [opdracht Indexerstatus ophalen:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Het antwoord bevat de algemene indexerstatus, de laatste (of lopende) indexeraanroep en de geschiedenis van recente indexaanroepen.

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

Uitvoeringsgeschiedenis bevat maximaal de 50 meest recente voltooide uitvoeringen, die in omgekeerde chronologische volgorde worden gesorteerd (dus de laatste uitvoering komt eerst in het antwoord).

## <a name="next-steps"></a>Volgende stappen
Nu u het uitgangspunt hebt begrepen, is de volgende stap de vereisten en taken te bekijken die specifiek zijn voor elk gegevensbrontype.

* [Azure SQL Database of SQL Server op een virtuele Azure-machine](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure-tabelopslag](search-howto-indexing-azure-tables.md)
* [CSV-blobs indexeren met de Azure Cognitive Search Blob-indexer](search-howto-index-csv-blobs.md)
* [Json-blobs indexeren met Azure Cognitive Search Blob-indexer](search-howto-index-json-blobs.md)
