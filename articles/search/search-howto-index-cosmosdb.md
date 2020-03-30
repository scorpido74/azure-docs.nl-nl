---
title: Zoeken via Azure Cosmos DB-gegevens
titleSuffix: Azure Cognitive Search
description: Importeer gegevens uit Azure Cosmos DB in een doorzoekbare index in Azure Cognitive Search. Indexers automatiseren gegevensopname voor geselecteerde gegevensbronnen zoals Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283002"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Cosmos DB-gegevens indexeren met behulp van een indexeerfunctie in Azure Cognitive Search 

> [!IMPORTANT] 
> SQL API is algemeen beschikbaar.
> MongoDB API, Gremlin API en Cassandra API-ondersteuning staan momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. U toegang tot de previews aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt preview functies. Er is momenteel beperkte portalondersteuning en geen .NET SDK-ondersteuning.

> [!WARNING]
> Alleen Cosmos DB-verzamelingen met een [indexeringsbeleid](https://docs.microsoft.com/azure/cosmos-db/index-policy) dat is ingesteld op [Consistent,](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) worden ondersteund door Azure Cognitive Search. Het indexeren van verzamelingen met een Lazy-indexeringsbeleid wordt niet aanbevolen en kan leiden tot ontbrekende gegevens. Verzamelingen met uitgeschakelde indexering worden niet ondersteund.

In dit artikel ziet u hoe u een Azure Cosmos [DB-indexer configureert](search-indexer-overview.md) om inhoud te extraheren en doorzoekbaar te maken in Azure Cognitive Search. Met deze werkstroom wordt een Azure Cognitive Search-index gemaakt en geladen met bestaande tekst uit Azure Cosmos DB. 

Omdat terminologie verwarrend kan zijn, is het vermeldenswaard dat [Azure Cosmos DB-indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) en [Azure Cognitive Search-indexering](search-what-is-an-index.md) verschillende bewerkingen zijn, die uniek zijn voor elke service. Voordat u Azure Cognitive Search-indexering start, moet uw Azure Cosmos DB-database al bestaan en gegevens bevatten.

De Cosmos DB-indexer in Azure Cognitive Search kan [Azure Cosmos DB-items](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) crawlen die via verschillende protocollen zijn geopend. 

+ Voor [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), die algemeen beschikbaar is, u de [portal,](#cosmos-indexer-portal) [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)of [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) gebruiken om de gegevensbron en indexer te maken.

+ Voor [MongoDB API (preview)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)u de [portal](#cosmos-indexer-portal) of de [REST API-versie 2019-05-06-Preview](search-api-preview.md) gebruiken om de gegevensbron en indexer te maken.

+ Voor [Cassandra API (preview)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) en [Gremlin API (preview)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)u alleen de [REST API-versie 2019-05-06-Preview](search-api-preview.md) gebruiken om de gegevensbron en indexer te maken.


> [!Note]
> U een stem uitbrengen op User Voice voor de [Tabel-API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) als u deze wilt zien worden ondersteund in Azure Cognitive Search.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Gebruik de portal

> [!Note]
> De portal ondersteunt momenteel de SQL API en MongoDB API (preview).

De eenvoudigste methode voor het indexeren van Azure Cosmos DB-items is het gebruik van een wizard in de [Azure-portal.](https://portal.azure.com/) Door gegevens te samplen en metagegevens in de container te lezen, kan de wizard [**Gegevens importeren**](search-import-data-portal.md) in Azure Cognitive Search een standaardindex maken, bronvelden toewijzen om indexvelden te targeten en de index in één bewerking laden. Afhankelijk van de grootte en complexiteit van brongegevens, u binnen enkele minuten een operationele full text search index hebben.

We raden u aan dezelfde regio of locatie te gebruiken voor zowel Azure Cognitive Search als Azure Cosmos DB voor lagere latentie en om bandbreedtekosten te voorkomen.

### <a name="1---prepare-source-data"></a>1 - Brongegevens voorbereiden

U moet beschikken over een Cosmos DB-account, een Azure Cosmos DB-database die is toegewezen aan de SQL API, MongoDB API (preview) of Gremlin API (preview) en inhoud in de database.

Zorg ervoor dat uw Cosmos DB-database gegevens bevat. De [wizard Gegevens importeren](search-import-data-portal.md) leest metagegevens en voert gegevensbemonstering uit om een indexschema af te leiden, maar laadt ook gegevens van Cosmos DB. Als de gegevens ontbreken, stopt de wizard met deze fout "Fout detecteren indexschema van gegevensbron: kan geen prototype-index bouwen omdat gegevensbron 'emptycollection' geen gegevens heeft geretourneerd".

### <a name="2---start-import-data-wizard"></a>2 - Wizard Gegevens importeren starten

U [de wizard starten](search-import-data-portal.md) vanaf de opdrachtbalk op de servicepagina Azure Cognitive Search of als u verbinding maakt met Cosmos DB SQL API, u op Azure Cognitive **Search** toevoegen in het gedeelte **Instellingen** van het linkernavigatiedeelvenster van uw Cosmos DB-account.

   ![Opdracht Gegevens importeren in portal](./media/search-import-data-portal/import-data-cmd2.png "De wizard Gegevens importeren starten")

### <a name="3---set-the-data-source"></a>3 - De gegevensbron instellen

Op de **gegevensbronpagina** moet de bron **Cosmos DB**zijn, met de volgende specificaties:

+ **Naam** is de naam van het gegevensbronobject. Eenmaal gemaakt, u het kiezen voor andere workloads.

+ **Cosmos DB-account** moet de primaire of secundaire `AccountEndpoint` verbindingstekenreeks `AccountKey`van Cosmos DB zijn, met een en een . Voor MongoDB-verzamelingen voegt u **ApiKind=MongoDb** toe aan het einde van de verbindingstekenreeks en scheidt deze van de verbindingstekenreeks met een puntkomma. Voor de Gremlin API en Cassandra API gebruikt u de instructies voor de [REST API.](#cosmosdb-indexer-rest)

+ **Database** is een bestaande database van het account. 

+ **Ophalen** is een container met documenten. Documenten moeten bestaan om de invoer te laten slagen. 

+ **Query** kan leeg zijn als u alle documenten wilt, anders u een query invoeren die een documentsubset selecteert. **Query** is alleen beschikbaar voor de SQL API.

   ![Cosmos DB-gegevensbrondefinitie](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB-gegevensbrondefinitie")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - De pagina Inhoud verrijken overslaan in de wizard

Het toevoegen van cognitieve vaardigheden (of verrijking) is geen importvereiste. Tenzij u een specifieke noodzaak hebt om [AI-verrijking toe](cognitive-search-concept-intro.md) te voegen aan uw indexeringspijplijn, moet u deze stap overslaan.

Als u de stap wilt overslaan, klikt u op de blauwe knoppen onder aan de pagina voor 'Volgende' en 'Overslaan'.

### <a name="5---set-index-attributes"></a>5 - Indexkenmerken instellen

Op de pagina **Index** ziet u een lijst met velden met een gegevenstype en een reeks selectievakjes voor het instellen van indexkenmerken. De wizard kan een veldenlijst genereren op basis van metagegevens en door de brongegevens te samplen. 

U kenmerken bulkselecteren door boven aan een kenmerkkolom op het selectievakje te klikken. Kies **Opvraagbaar** en **doorzoekbaar** voor elk veld dat moet worden geretourneerd naar een client-app en onder voorbehoud van volledige tekstzoekverwerking. U zult merken dat gehele getallen geen volledige tekst of fuzzy doorzoekbaar zijn (getallen worden letterlijk geëvalueerd en zijn vaak nuttig in filters).

Bekijk de beschrijving van [indexkenmerken](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) en [taalanalysers](https://docs.microsoft.com/rest/api/searchservice/language-support) voor meer informatie. 

Neem even de tijd om uw selecties te bekijken. Zodra u de wizard uitvoert, worden fysieke gegevensstructuren gemaakt en u deze velden niet bewerken zonder alle objecten te laten vallen en opnieuw te maken.

   ![Cosmos DB-indexdefinitie](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB-indexdefinitie")

### <a name="6---create-indexer"></a>6 - Indexermaken

Volledig opgegeven, de wizard maakt drie verschillende objecten in uw zoekservice. Een gegevensbronobject en -indexobject worden opgeslagen als benoemde resources in uw Azure Cognitive Search-service. Met de laatste stap wordt een indexerobject gemaakt. Als u de indexer noemt, kan deze bestaan als een zelfstandige bron, die u onafhankelijk van het index- en gegevensbronobject plannen en beheren dat in dezelfde wizardreeks is gemaakt.

Als u niet bekend bent met indexeerders, is een *indexeerder* een bron in Azure Cognitive Search die een externe gegevensbron crawlt voor doorzoekbare inhoud. De uitvoer van de wizard **Gegevens importeren** is een indexer die uw Cosmos DB-gegevensbron crawlt, doorzoekbare inhoud extraheert en deze importeert in een index op Azure Cognitive Search.

De volgende schermafbeelding toont de standaardindexerconfiguratie. U overschakelen naar **Eén** als u de indexer één keer wilt uitvoeren. Klik **op Verzenden** om de wizard uit te voeren en alle objecten te maken. De indexering begint onmiddellijk.

   ![Cosmos DB indexerdefinitie](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB indexerdefinitie")

U het importeren van gegevens in de portalpagina's controleren. Voortgangsmeldingen geven de indexeringsstatus aan en hoeveel documenten worden geüpload. 

Wanneer de indexering is voltooid, u [Zoekverkenner](search-explorer.md) gebruiken om uw index op te vragen.

> [!NOTE]
> Als u de verwachte gegevens niet ziet, moet u mogelijk meer kenmerken instellen op meer velden. Verwijder de index en indexer die u zojuist hebt gemaakt en stap opnieuw door de wizard en wijzig uw selecties voor indexkenmerken in stap 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST-API's gebruiken

U de REST-API gebruiken om Azure Cosmos DB-gegevens te indexeren, volgens een driedelige werkstroom die alle indexeerders in Azure Cognitive Search gemeen hebben: maak een gegevensbron, maak een index, maak een indexeerder. Gegevensextractie van Cosmos DB vindt plaats wanneer u de aanvraag Indexer maken indient. Nadat dit verzoek is voltooid, hebt u een opvraagbare index. 

> [!NOTE]
> Voor het indexeren van gegevens van Cosmos DB Gremlin API of Cosmos DB Cassandra API moet u eerst toegang vragen tot de gated previews door dit formulier in te [vullen.](https://aka.ms/azure-cognitive-search/indexer-preview) Zodra uw aanvraag is verwerkt, ontvangt u instructies voor het gebruik van de [REST API-versie 2019-05-06-Preview](search-api-preview.md) om de gegevensbron te maken.

Eerder in dit artikel wordt vermeld dat [Azure Cosmos DB-indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) en [Azure Cognitive Search-indexering](search-what-is-an-index.md) verschillende bewerkingen zijn. Voor Cosmos DB-indexering worden standaard alle documenten automatisch geïndexeerd, behalve bij de Cassandra API. Als u automatische indexering uitschakelt, kunnen documenten alleen worden geopend via hun zelfkoppelingen of via query's met behulp van de document-id. Azure Cognitive Search-indexering vereist dat automatische indexering van Cosmos DB wordt ingeschakeld in de verzameling die wordt geïndexeerd door Azure Cognitive Search. Wanneer u zich aanmeldt voor de preview van de Cosmos DB Cassandra API-indexer, krijgt u instructies over het instellen van Cosmos DB-indexering.

> [!WARNING]
> Azure Cosmos DB is de volgende generatie DocumentDB. Voorheen met **API-versie 2017-11-11** kon u de `documentdb` syntaxis gebruiken. Dit betekende dat u uw gegevensbrontype kon opgeven als `cosmosdb` of `documentdb`. Beginnend met **API-versie 2019-05-06** ondersteunen zowel de `cosmosdb` Azure Cognitive Search API's als Portal alleen de syntaxis zoals geïnstrueerd in dit artikel. Dit betekent dat het `cosmosdb` gegevensbrontype moet als u verbinding wilt maken met een Cosmos DB-eindpunt.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Ingangen voor het verzoek monteren

Voor elk verzoek moet u de servicenaam en de beheerderssleutel voor Azure Cognitive Search (in de post-header) en de naam en sleutel voor het opslagaccount voor blobopslag opgeven. U [Postman](search-get-started-postman.md) gebruiken om HTTP-aanvragen naar Azure Cognitive Search te verzenden.

Kopieer de volgende vier waarden naar kladblok, zodat u ze in een aanvraag plakken:

+ Naam azure Cognitive Search-service
+ Azure Cognitive Search-beheersleutel
+ Cosmos DB-verbindingstekenreeks

U vindt deze waarden in de portal:

1. Kopieer de URL van de zoekservice op de portalpagina's voor Azure Cognitive Search vanaf de pagina Overzicht.

2. Klik in het linkernavigatiedeelvenster op **Toetsen** en kopieer vervolgens de primaire of secundaire toets (deze zijn gelijkwaardig).

3. Schakel over naar de portalpagina's voor uw Cosmos-opslagaccount. Klik in het linkernavigatiedeelvenster onder **Instellingen**op **Toetsen**. Deze pagina biedt een URI, twee sets verbindingstekenreeksen en twee sets sleutels. Kopieer een van de verbindingstekenreeksen naar Kladblok.

### <a name="2---create-a-data-source"></a>2 - Een gegevensbron maken

Een **gegevensbron** geeft de gegevens op die moeten worden geïndexeerd, referenties en beleidsregels voor het identificeren van wijzigingen in de gegevens (zoals gewijzigde of verwijderde documenten in uw verzameling). De gegevensbron wordt gedefinieerd als een onafhankelijke bron, zodat deze door meerdere indexeerders kan worden gebruikt.

Als u een gegevensbron wilt maken, formuleert u een POST-verzoek:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

De hoofdtekst van de aanvraag bevat de definitie van gegevensbron, die de volgende velden moet bevatten:

| Veld   | Beschrijving |
|---------|-------------|
| **Naam** | Vereist. Kies een naam die uw gegevensbronobject vertegenwoordigt. |
|**Type**| Vereist. Moet `cosmosdb`wel. |
|**Referenties** | Vereist. Moet een Cosmos DB verbindingstekenreeks zijn.<br/>Voor SQL-verzamelingen zijn verbindingstekenreeksen in deze indeling:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>Voor MongoDB-verzamelingen voegt u **ApiKind=MongoDb** toe aan de verbindingstekenreeks:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Voor Gremlin-grafieken en Cassandra-tabellen u zich aanmelden voor de [voorvertoning van de gated indexer](https://aka.ms/azure-cognitive-search/indexer-preview) om toegang te krijgen tot de preview en informatie over het opmaken van de referenties.<br/><br/>Vermijd poortnummers in de url van het eindpunt. Als u het poortnummer opneemt, kan Azure Cognitive Search uw Azure Cosmos DB-database niet indexeren.|
| **container** | Bevat de volgende elementen: <br/>**naam**: Vereist. Geef de id op van de databaseverzameling die moet worden geïndexeerd.<br/>**query:** optioneel. U een query opgeven om een willekeurig JSON-document af te vlakken naar een plat schema dat Azure Cognitive Search kan indexeren.<br/>Voor de MongoDB API, Gremlin API en Cassandra API worden query's niet ondersteund. |
| **dataChangeDetectionPolicy** | Aanbevolen. Zie De sectie [Gewijzigde documenten indexeren.](#DataChangeDetectionPolicy)|
|**dataDeletionDetectionPolicy** | Optioneel. Zie Sectie [Verwijderde documenten indexeren.](#DataDeletionDetectionPolicy)|

### <a name="using-queries-to-shape-indexed-data"></a>Query's gebruiken om geïndexeerde gegevens vorm te geven
U een SQL-query opgeven om geneste eigenschappen of arrays af te vlakken, JSON-eigenschappen project en de te indexeren gegevens filteren. 

> [!WARNING]
> Aangepaste query's worden niet ondersteund voor **MongoDB API,** **Gremlin API**en **Cassandra API**: `container.query` parameter moet worden ingesteld op null of weggelaten. Als u een aangepaste query wilt gebruiken, laat het ons dan weten op [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Voorbeelddocument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filterquery:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Afvlakkingsquery:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projectiequery:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Matrixafvlakingsquery:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Een doelzoekindex maken 

[Maak een doelAzure Cognitive Search-index](/rest/api/searchservice/create-index) als u die nog niet hebt. In het volgende voorbeeld wordt een index met een id- en beschrijvingsveld geveld:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Controleer of het schema van uw doelindex compatibel is met het schema van de bron-JSON-documenten of de uitvoer van uw aangepaste queryprojectie.

> [!NOTE]
> Voor partitieverzamelingen is de standaarddocumentsleutel de `_rid` eigenschap van Azure Cosmos DB, `rid` waarAzure Cognitive Search automatisch de naam van hernoemt omdat veldnamen niet kunnen beginnen met een underscore-teken. Azure Cosmos DB-waarden `_rid` bevatten ook tekens die ongeldig zijn in Azure Cognitive Search-toetsen. Om deze reden `_rid` zijn de waarden Base64 gecodeerd.
> 
> Voor MongoDB-verzamelingen hernoemt Azure `_id` Cognitive `id`Search de naam van de eigenschap automatisch naar .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Toewijzing tussen JSON-gegevenstypen en Azure Cognitive Search-gegevenstypen
| JSON-gegevenstype | Compatibele doelindexveldtypen |
| --- | --- |
| Booleaanse waarde |Edm.Boolean, Edm.String |
| Getallen die op gehele getallen lijken |Edm.Int32, Edm.Int64, Edm.String |
| Getallen die op zwevende punten lijken |Edm.Double, Edm.String |
| Tekenreeks |Edm.String |
| Arrays van primitieve typen, bijvoorbeeld ["a", "b", "c"] |Collection(EDM.String) |
| Tekenreeksen die op datums lijken |Edm.DateTimeOffset, Edm.String |
| GeoJSON-objecten, bijvoorbeeld { "type": "Punt", "coördinaten": [lang, lat] } |Edm.GeographyPoint |
| Andere JSON-objecten |N.v.t. |

### <a name="4---configure-and-run-the-indexer"></a>4 - De indexeren configureren en uitvoeren

Zodra de index en gegevensbron zijn gemaakt, bent u klaar om de indexer te maken:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Deze indexer wordt elke twee uur uitgevoerd (het schemainterval is ingesteld op 'PT2H'). Als u elke 30 minuten een indexer wilt uitvoeren, stelt u het interval in op "PT30M". Het kortste ondersteunde interval is 5 minuten. De planning is optioneel - als deze wordt weggelaten, wordt een indexer slechts één keer uitgevoerd wanneer deze is gemaakt. U echter op elk gewenst moment een indexer op aanvraag uitvoeren.   

Ga voor meer informatie over de API Indexer maken naar [Indexer maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Zie [Indexeerders voor Azure Cognitive Search](search-howto-schedule-indexers.md)plannen voor meer informatie over het definiëren van indexerschema's.

## <a name="use-net"></a>.NET gebruiken

De algemeen beschikbare .NET SDK heeft volledige pariteit met de algemeen beschikbare REST API. We raden u aan de vorige REST API-sectie te bekijken om concepten, workflow en vereisten te leren. U vervolgens verwijzen naar de volgende .NET API-referentiedocumentatie om een JSON-indexer in beheerde code te implementeren.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Gewijzigde documenten indexeren

Het doel van een detectiebeleid voor gegevenswijziging is om gewijzigde gegevensitems efficiënt te identificeren. Momenteel is het enige ondersteunde [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) beleid `_ts` het gebruik van de eigenschap (tijdstempel) die wordt geleverd door Azure Cosmos DB, die als volgt is opgegeven:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Het gebruik van dit beleid wordt ten zeerste aanbevolen om goede indexerprestaties te garanderen. 

Als u een aangepaste query gebruikt, `_ts` controleert u of de eigenschap wordt geprojecteerd door de query.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Incrementele voortgang en aangepaste query's

Incrementele voortgang tijdens het indexeren zorgt ervoor dat als de uitvoering van indexeerders wordt onderbroken door tijdelijke fouten of uitvoeringstijdslimiet, de indexer kan verder gaan waar hij de volgende keer was gebleven, in plaats van de volledige verzameling opnieuw te indexeren. Dit is vooral belangrijk bij het indexeren van grote collecties. 

Als u incrementele voortgang wilt inschakelen bij het gebruik `_ts` van een aangepaste query, moet u ervoor zorgen dat uw query de resultaten per kolom bestelt. Dit maakt periodieke controle-aanwijzen dat Azure Cognitive Search gebruikt om incrementele vooruitgang in de aanwezigheid van fouten te bieden.   

In sommige gevallen kan Azure `ORDER BY [collection alias]._ts` Cognitive Search, zelfs als uw query een clausule `_ts`bevat, niet afleiden dat de query wordt besteld door de . U Azure Cognitive Search vertellen dat `assumeOrderByHighWaterMarkColumn` de resultaten worden geordend met behulp van de eigenschap configuratie. Als u deze hint wilt opgeven, maakt of werkt u uw indexeerder als volgt: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Verwijderde documenten indexeren

Wanneer rijen uit de verzameling worden verwijderd, wilt u deze rijen normaal gesproken ook uit de zoekindex verwijderen. Het doel van een detectiebeleid voor gegevensverwijdering is om verwijderde gegevensitems efficiënt te identificeren. Momenteel is het enige ondersteunde `Soft Delete` beleid het beleid (verwijdering is gemarkeerd met een vlag van een soort), dat als volgt is opgegeven:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Als u een aangepaste query gebruikt, moet u `softDeleteColumnName` ervoor zorgen dat de eigenschap waarnaar wordt verwezen, wordt geprojecteerd door de query.

In het volgende voorbeeld wordt een gegevensbron met een beleid voor het verwijderen van zachte verwijderingen gezoend:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="next-steps"></a><a name="NextSteps"></a>Volgende stappen

Gefeliciteerd! U hebt geleerd hoe u Azure Cosmos DB integreren met Azure Cognitive Search met behulp van een indexeerder.

* Zie de [azure cosmos DB-servicepagina](https://azure.microsoft.com/services/cosmos-db/)voor meer informatie over Azure Cosmos DB.
* Zie de [pagina Zoekservice](https://azure.microsoft.com/services/search/)voor meer informatie over Azure Cognitive Search.
