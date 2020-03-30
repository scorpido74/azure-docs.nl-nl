---
title: Import en gegevensopname in zoekindexen
titleSuffix: Azure Cognitive Search
description: Gegevens invullen en uploaden naar een index in Azure Cognitive Search uit externe gegevensbronnen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282755"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Overzicht van gegevensimport - Azure Cognitive Search

In Azure Cognitive Search worden query's uitgevoerd over uw inhoud die is geladen in en opgeslagen in een [zoekindex.](search-what-is-an-index.md) In dit artikel worden de twee basisbenaderingen voor het vullen van een index onderzocht: *duw* uw gegevens programmatisch in de index of richt een [Azure Cognitive Search-indexer](search-indexer-overview.md) op een ondersteunde gegevensbron om de gegevens binnen te *halen.*

Met beide benaderingen is het doel om gegevens van een externe gegevensbron te *laden* in een Azure Cognitive Search-index. Azure Cognitive Search laat u een lege index maken, maar totdat u er gegevens in duwt of ophaalt, is deze niet opvraagbaar.

## <a name="pushing-data-to-an-index"></a>Gegevens naar een index pushen
Het pushmodel, dat wordt gebruikt om uw gegevens programmatisch naar Azure Cognitive Search te verzenden, is de meest flexibele aanpak. Ten eerste heeft dit model geen beperkingen met betrekking tot het gegevensbrontype. Elke gegevensset die bestaat uit JSON-documenten kan worden gepusht naar een Azure Cognitive Search-index, ervan uitgaande dat elk document in de gegevensset velden heeft toewijzing aan velden die zijn gedefinieerd in uw indexschema. Ten tweede heeft dit model geen beperkingen met betrekking tot de frequentie van de uitvoering. U kunt wijzigingen naar een index pushen zo vaak als u wilt. Voor toepassingen die een zeer lage latentie vereisen (bijvoorbeeld als zoekopdrachten gesynchroniseerd moeten zijn met dynamische inventarisatiedatabases) is het pushmodel de enige mogelijkheid.

Deze aanpak is flexibeler dan het pull-model, omdat u documenten afzonderlijk of in batches kunt uploaden (maximaal 1000 per batch of 16 MB, afhankelijk van waar de limiet ligt). Met het pushmodel u ook documenten uploaden naar Azure Cognitive Search, ongeacht waar uw gegevens zich bevinden.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Gegevens pushen naar een Azure Cognitive Search-index

U kunt de volgende API's gebruiken om één of meerdere documenten in een index te laden:

+ [Documenten toevoegen, bijwerken of verwijderen (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) of [indexBatch-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Er is momenteel geen ondersteuning voor het pushen van gegevens via de portal.

Zie [Snelstart: Een Azure Cognitive Search-index maken met PowerShell](search-create-index-rest-api.md) of [C# Quickstart: Een Azure Cognitive Search-index maken met behulp van .NET SDK](search-get-started-dotnet.md)voor een inleiding tot elke methodologie.

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexeringsacties: uploaden, samenvoegen, samenvoegenOf Uploaden, verwijderen

U het type indexeringsactie per document beheren en opgeven of het document volledig moet worden geüpload, moet worden samengevoegd met bestaande documentinhoud of moet worden verwijderd.

Geef in de REST-API HTTP POST-aanvragen met JSON-aanvraaginstanties op de eindpunt-URL van de Azure Cognitive Search-index. Elk JSON-object in de array 'waarde' bevat de sleutel van het document en geeft aan of een indexeringsactie documentinhoud toevoegt, bijwerkt of verwijdert. Zie [Documenten laden](search-get-started-dotnet.md#load-documents)voor een codevoorbeeld .

In de .NET SDK u `IndexBatch` uw gegevens verpakken in een object. Een `IndexBatch` verzameling `IndexAction` objecten, die elk een document en een eigenschap bevat die Azure Cognitive Search vertelt welke actie op dat document moet worden uitgevoerd. Zie voor een codevoorbeeld de [C# Quickstart](search-get-started-dotnet.md).


| @search.action | Beschrijving | Vereiste velden voor elk document | Opmerkingen |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Een `upload`-actie is vergelijkbaar met een "upsert", waarbij het document wordt ingevoegd als het nieuw is en wordt bijgewerkt/vervangen als het al bestaat. |sleutel, plus andere velden die u wilt definiëren |Tijdens het bijwerken/vervangen van een bestaand document wordt elk veld dat niet is opgegeven in de aanvraag ingesteld op `null`. Dit gebeurt zelfs als het veld eerder is ingesteld op een niet-null-waarde. |
| `merge` |Een bestaand document wordt bijgewerkt met de opgegeven velden. Als het document niet in de index bestaat, mislukt de samenvoeging. |sleutel, plus andere velden die u wilt definiëren |Alle velden die u in een samenvoeging opgeeft, vervangen de bestaande velden in het document, In de .NET SDK omvat `DataType.Collection(DataType.String)`dit velden van het type . In de REST-API omvat `Collection(Edm.String)`dit typevelden . Als het document bijvoorbeeld een veld `tags` bevat met de waarde `["budget"]` en u een samenvoeging doet met de waarde `["economy", "pool"]` voor `tags`, wordt de uiteindelijke waarde van het veld `tags``["economy", "pool"]`. Het wordt dus niet `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Deze bewerking gedraagt zich als `merge` wanneer een document met de opgegeven sleutel al in de index bestaat. Als het document niet bestaat, gedraagt deze bewerking zich als `upload` met een nieuw document. |sleutel, plus andere velden die u wilt definiëren |- |
| `delete` |Het opgegeven document wordt uit de index verwijderd. |alleen sleutel |Alle andere velden worden genegeerd. Als u een afzonderlijk veld uit een document wilt verwijderen, gebruikt u `merge` en stelt u het veld expliciet in op null. |

## <a name="decide-which-indexing-action-to-use"></a>Bepalen welke indexeerbewerking u moet gebruiken
Gegevens importeren met de .NET SDK (uploaden, samenvoegen, verwijderen en samenvoegen). Afhankelijk van welke van de onderstaande bewerkingen u kiest, moet u slechts bepaalde velden voor elk document opnemen:


### <a name="formulate-your-query"></a>Uw query formuleren
Er zijn twee manieren om [in de index te zoeken met behulp van de REST-API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). De ene manier is om een HTTP POST-aanvraag uit te geven waarbij uw queryparameters worden gedefinieerd in een JSON-object in de aanvraagtekst. De andere manier is om een HTTP GET-aanvraag uit te geven waarbij uw queryparameters worden gedefinieerd in de aanvraag-URL. POST heeft [soepelere limieten](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) met betrekking tot de grootte van queryparameters dan GET. Daarom wordt u aangeraden POST te gebruiken, tenzij er speciale omstandigheden zijn waarin het gebruik van GET beter zou zijn.

Voor zowel POST als GET moet u in de aanvraag-URL de *servicenaam*, de *indexnaam* en de juiste *API-versie* (de huidige API-versie is `2019-05-06` op het moment van publicatie van dit document) opgeven. Voor GET geeft u in de *querytekenreeks* aan het einde van de URL de queryparameters op. Hieronder vindt u de URL-indeling:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

De indeling voor POST is hetzelfde, maar met alleen de api-versie in de queryreeksparameters.


## <a name="pulling-data-into-an-index"></a>Gegevens in een index ophalen
Het pullmodel verkent een ondersteunde gegevensbron en uploadt de gegevens automatisch naar uw index. In Azure Cognitive Search wordt deze mogelijkheid geïmplementeerd via *indexeerders,* momenteel beschikbaar voor deze platforms:

+ [Blob-opslag](search-howto-indexing-azure-blob-storage.md)
+ [Tabelopslag](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database en SQL Server op Azure-VM's](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexeerfuncties verbinden een index met een gegevensbron (meestal een tabel, weergave of equivalente structuur) en wijzen bronvelden toe aan equivalente velden in de index. Tijdens de uitvoering wordt de rijenset automatisch omgezet naar JSON en in de opgegeven index geladen. Alle indexeerfuncties ondersteunen planning. U kunt dus opgeven hoe vaak de gegevens moeten worden vernieuwd. Met de meeste indexeerfuncties kunt u wijzigingen bijhouden als dit door de gegevensbron wordt ondersteund. Als u naast het herkennen van nieuwe documenten, wijzigingen en verwijderingen in een bestaand document bijhoudt, hoeft u de gegevens in een index via indexeerfuncties niet actief bij te houden. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Gegevens opvragen in een Azure Cognitive Search-index

Functionaliteit van de indexeerfunctie wordt weergegeven in [Azure Portal](search-import-data-portal.md), de [REST API](/rest/api/searchservice/Indexer-operations) en de [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Een voordeel van het gebruik van de portal is dat Azure Cognitive Search meestal een standaardindexschema voor u kan genereren door de metagegevens van de brongegevensset te lezen. U kunt de gegenereerde index wijzigen totdat de index wordt verwerkt. Daarna zijn alleen schemabewerkingen toegestaan waarvoor de index niet opnieuw gegenereerd hoeft te worden. Als de wijzigingen die u doorvoert, rechtstreeks invloed hebben op het schema, moet u de index opnieuw opbouwen. 

## <a name="verify-data-import-with-search-explorer"></a>Gegevens importeren verifiëren met Zoekverkenner

Een snelle manier om een voorlopige controle op het uploaden van het document uit te voeren, is door **Zoekverkenner** in de portal te gebruiken. Met de explorer kunt u gegevens uit een index opvragen zonder code te schrijven. De zoekervaring is gebaseerd op standaardinstellingen, zoals de [eenvoudige syntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) en de [standaardparameter searchMode query](/rest/api/searchservice/search-documents). Resultaten worden geretourneerd in JSON, zodat u het hele document kunt inspecteren.

> [!TIP]
> Tal van [voorbeelden van Azure Cognitive Search-code](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) omvatten ingesloten of direct beschikbare gegevenssets, die een eenvoudige manier bieden om aan de slag te gaan. De portal bevat ook een voorbeeldindexeerfunctie en een gegevensbron, bestaande uit een kleine vastgoedgegevensset ('realestate-us-sample'). Wanneer u de vooraf geconfigureerde indexer op de voorbeeldgegevensbron uitvoert, wordt een index gemaakt en geladen met documenten die vervolgens kunnen worden opgevraagd in Search explorer of op code die u schrijft.

## <a name="see-also"></a>Zie ook

+ [Overzicht van de indexeerfunctie](search-indexer-overview.md)
+ [Portaloverzicht: een index maken, gegevens laden, een query in een index uitvoeren](search-get-started-portal.md)
