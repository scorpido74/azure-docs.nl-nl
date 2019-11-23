---
title: Create a search index in the Azure portal
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to use the Import Data wizard to create, load, and query your first search index in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: be2425d97573e7990ea7f0dfd4c2d999e85fe922
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407002"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Create an Azure Cognitive Search index in the Azure portal
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Use the portal's **Import data** wizard and **Search explorer** tools to quickly ramp up on concepts, and write interesting queries against an index within minutes.

If the tools are too limiting, you can consider a [code-based introduction to programming Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md) or use [Postman for making REST API calls](search-get-started-postman.md). 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this quickstart. 

### <a name="check-for-space"></a>Controleren of er voldoende ruimte is

Veel klanten beginnen met de gratis service. Deze versie is beperkt tot drie indexen drie gegevensbronnen en drie indexeerfuncties. Zorg ervoor dat er voldoende ruimte is voor extra items voordat u begint. In deze zelfstudie wordt van elk object één exemplaar gemaakt.

Op secties in het servicedashboard wordt weergegeven hoeveel indexen, indexeerfuncties en gegevensbronnen u al hebt. 

![Lijsten met indexen, indexeerfuncties en gegevensbronnen](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Een index maken en gegevens laden

Zoekopdrachten worden herhaald over een [*index*](search-what-is-an-index.md) die doorzoekbare gegevens, metagegevens en aanvullende constructies bevat, die worden gebruikt om bepaald zoekgedrag te optimaliseren.

For this tutorial, we use a built-in sample dataset that can be crawled using an [*indexer*](search-indexer-overview.md) via the [**Import data** wizard](search-import-data-portal.md). Een indexeerfunctie is een bronspecifieke verkenner die metagegevens en inhoud uit ondersteunde Azure-gegevensbronnen kan lezen. Normaal gesproken worden indexeerfuncties via een programma gebruikt, maar in de portal zijn ze toegankelijk via de wizard **Gegevens importeren**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Stap 1: de wizard Gegevens importeren starten en een gegevensbron maken

1. On the Azure Cognitive Search service dashboard, click **Import data** on the command bar to create and populate a search index.

   ![Opdracht Gegevens importeren](media/search-get-started-portal/import-data-cmd.png)

2. In the wizard, click **Connect to your data** > **Samples** > **hotels-sample**. Deze gegevensbron is ingebouwd. Als u uw eigen gegevensbron zou maken, zou u een naam, het type en de verbindingsgegevens moeten opgeven. Zodra de gegevensbron is gemaakt, wordt deze een bestaande gegevensbron genoemd die opnieuw kan worden gebruikt voor andere bewerkingen.

   ![Voorbeeldgegevensset selecteren](media/search-get-started-portal/import-datasource-sample.png)

3. Ga door naar de volgende pagina.

### <a name="step-2---skip-the-enrich-content-page"></a>Step 2 - Skip the "Enrich content" page

The wizard supports the creation of an [AI enrichment pipeline](cognitive-search-concept-intro.md) for incorporating the Cognitive Services AI algorithms into indexing. 

Deze stap slaan we nu even over en we gaan naar **Doelindex aanpassen**.

   ![Stap voor cognitieve vaardigheden overslaan](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> You can step through an AI-indexing example in a [quickstart](cognitive-search-quickstart-blob.md) or [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Stap 3: index configureren

Het maken van een index is meestal een op code gebaseerde oefening die is voltooid voordat gegevens worden geladen. Maar zoals deze zelfstudie laat zien, kan de wizard een basisindex genereren voor elke gegevensbron die kan worden verkend. Een index vereist minimaal een naam en een verzameling velden. Eén van de velden moet zijn gemarkeerd als de documentsleutel die elk document op unieke wijze identificeert. Bovendien kunt u taalanalyses of suggesters opgeven als u wilt dat automatisch aanvullen of het voorstellen van query's wordt gebruikt.

Velden bevatten gegevenstypen en kenmerken. De selectievakjes bovenaan zijn *indexkenmerken* die bepalen hoe het veld wordt gebruikt.

* **Ophalen mogelijk** betekent dat dit veld wordt weergegeven in de lijst met zoekresultaten. You can mark individual fields as off limits for search results by clearing this checkbox, for example for fields used only in filter expressions.
* **Sleutel** is de unieke id van het document. Het is altijd een tekenreeks en deze is vereist.
* Met de kenmerken **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten** bepaalt u of velden in een filter, een sorteervolgorde of een facetnavigatiestructuur worden gebruikt.
* **Doorzoekbaar** betekent dat een veld is opgenomen in een zoekopdracht in volledige tekst. Tekenreeksen zijn doorzoekbaar. Numerieke velden en Booleaanse waarden zijn vaak gemarkeerd als niet doorzoekbaar.

Opslagvereisten verschillen niet als gevolg van uw selectie. Als u bijvoorbeeld het kenmerk **Ophalen mogelijk** instelt voor meerdere velden, nemen de opslagvereisten niet toe.

Standaard wordt met de wizard de gegevensbron gescand op unieke id's als basis voor het sleutelveld. *Tekenreeksen* hebben de kenmerken **Ophaalbaar** en **Doorzoekbaar**. *Gehele getallen* hebben de kenmerken **Ophaalbaar**, **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten**.

1. Accepteer de standaardwaarden. 

   If you rerun the wizard a second time using an existing hotels data source, the index won't be configured with default attributes. Voor toekomstige importbewerkingen moet u de kenmerken handmatig selecteren. 

   ![Generated hotels index](media/search-get-started-portal/hotelsindex.png)

2. Ga door naar de volgende pagina.


### <a name="step-4---configure-indexer"></a>Stap 4: indexeerfunctie configureren

Klik in de wizard**Gegevens importeren** op **Indexeerfunctie** > **Naam** en typ een naam voor de indexeerfunctie.

Dit object definieert een uitvoerbaar proces. U kunt een terugkerend schema instellen, maar in dit geval gebruikt u de standaardoptie om de indexeerfunctie één keer meteen uit te voeren.

Klik op **Verzenden** om de indexeerfunctie te maken en tegelijkertijd uit te voeren.

  ![hotels indexer](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Voortgang controleren

De wizard leidt u naar de lijst met indexeerfuncties waar u de voortgang kunt controleren. Voor zelfnavigatie gaat u naar de overzichtspagina en klikt u op **Indexeerfuncties**.

Het kan een paar minuten duren voordat in de portal de pagina is bijgewerkt, maar u zou de zojuist gemaakte indexeerfunctie in de lijst moeten zien met de status die aangeeft dat deze wordt uitgevoerd of is geslaagd en met het aantal geïndexeerde documenten.

   ![Voortgangsbericht voor de indexeerfunctie](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>De index bekijken

The main service page provides links to the resources created in your Azure Cognitive Search service.  Als u de index wilt bekijken die u zojuist hebt gemaakt, klikt u op **Indexen** in de lijst met koppelingen. 

Wait for the portal page to refresh. After a few minutes, you should see the index with a document count and storage size.

   ![Lijst met indexen op het servicedashboard](media/search-get-started-portal/indexes-list.png)

From this list, you can click on the *hotels-sample* index that you just created, view the index schema. en eventueel nieuwe velden toevoegen. 

Op het tabblad **Velden** wordt het indexschema getoond. Scroll naar de onderkant van de lijst om een nieuw veld in te voeren. In de meeste gevallen kunt u geen bestaande velden wijzigen. Existing fields have a physical representation in Azure Cognitive Search and are thus non-modifiable, not even in code. Als u een bestaand veld fundamenteel wilt wijzigen, maakt u een nieuwe index en verwijdert u het oorspronkelijke veld.

   ![voorbeeld van indexdefinitie](media/search-get-started-portal/sample-index-def.png)

Andere constructies, zoals scoreprofielen en CORS-opties, kunnen op elk gewenst moment worden toegevoegd.

Neem even de tijd voor het bekijken van de indexdefinitieopties om duidelijk te begrijpen wat u wel en niet kunt bewerken tijdens indexontwerp. Uitgegrijsde opties geven aan dat een waarde niet kan worden gewijzigd of verwijderd. 

## <a name="query-index"></a> Query die Search Explorer gebruikt

U zou nu een zoekindex moeten hebben die klaar is om query’s uit te voeren met de ingebouwde querypagina [**Search Explorer**](search-explorer.md). Deze pagina biedt een zoekvak waarmee u willekeurige queryreeksen kunt testen.

**Search Explorer** kan alleen omgaan met [REST API-aanvragen](https://docs.microsoft.com/rest/api/searchservice/search-documents), maar er wordt syntaxis geaccepteerd voor [eenvoudige querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) en [volledige Lucene-queryparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), plus alle zoekparameters die beschikbaar zijn in bewerkingen voor [Search-document-REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> The following steps are demonstrated at 6m08s into the [Azure Cognitive Search Overview video](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klik op **Search explorer** in de opdrachtbalk.

   ![Opdracht Search Explorer](media/search-get-started-portal/search-explorer-cmd.png)

2. From the **Index** dropdown, choose  *hotels-sample-index*. Click the **API Version** dropdown, to see which REST APIs are available. For the queries below, use the generally available version (2019-05-06).

   ![Index- en API-opdrachten](media/search-get-started-portal/search-explorer-changeindex.png)

3. Plak in de zoekbalk de onderstaande querytekenreeksen en klik op **Zoeken**.

   ![Querytekenreeks en knop Zoeken](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Voorbeelden van query's

U kunt termen en zinnen invoeren, vergelijkbaar met wat u in een zoekopdracht in Bing of Google doet, of u kunt volledig opgegeven query-expressies invoeren. Resultaten worden geretourneerd als uitgebreide JSON-documenten.

### <a name="simple-query-with-top-n-results"></a>Eenvoudige query met de bovenste N resultaten

#### <a name="example-string-query-searchspa"></a>Voorbeeld (tekenreeks): `search=spa`

* The **search** parameter is used to input a keyword search for full text search, in this case, returning hotel data for those containing *spa* in any searchable field in the document.

* Met **Search Explorer** worden resultaten geretourneerd in JSON. Deze indeling is uitgebreid en moeilijk te lezen als documenten een compacte structuur hebben. Dit is opzettelijk; dat het hele document zichtbaar is, is belangrijk voor ontwikkeldoeleinden, vooral tijdens het testen. Voor een betere gebruikerservaring moet u code schrijven waarmee [zoekresultaten](search-pagination-page-layout.md) belangrijke elementen weergeven.

* Documenten bestaan uit alle velden die zijn gemarkeerd als ‘Ophalen mogelijk’ in de index. To view index attributes in the portal, click *hotels-sample* in the **Indexes** list.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Voorbeeld (geparameteriseerde query): `search=spa&$count=true&$top=10`

* Het symbool **&** wordt gebruikt om zoekparameters toe te voegen. Deze kunnen in willekeurige volgorde worden opgegeven.

* The **$count=true** parameter returns the total count of all documents returned. Deze waarde verschijnt bovenaan de zoekresultaten. U kunt filterquery's controleren door de wijzigingen te controleren die door **$count=true** worden gerapporteerd. Lage aantallen geven aan dat uw filter werkt.

* The **$top=10** returns the highest ranked 10 documents out of the total. By default, Azure Cognitive Search returns the first 50 best matches. U kunt dit aantal vergroten of verkleinen via **$top**.

### <a name="filter-query"></a>De query filteren

Filters zijn opgenomen in de zoekopdrachten wanneer u de parameter **$filter** toevoegt. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Voorbeeld (gefilterd): `search=beach&$filter=Rating gt 4`

* De parameter **$filter** retourneert resultaten die voldoen aan de criteria die u hebt opgegeven. In this case, ratings greater than 4.

* Filtersyntaxis is een OData-constructie. Zie [OData-syntaxis filteren](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

### <a name="facet-query"></a>De query facetteren

Facetfilters zijn opgenomen in zoekopdrachten. U kunt de facetparameter gebruiken om een samengevoegd aantal documenten te retourneren die overeenkomen met een door u opgegeven facetwaarde.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Voorbeeld (gefacetteerd met bereikvermindering): `search=*&facet=Category&$top=2`

* **search=** * is een lege zoekopdracht. Met een lege zoekopdracht wordt naar alles gezocht. Eén reden om een lege query in te dienen, is om de hele set documenten te filteren of als facet te gebruiken. For example, you want a faceting navigation structure to consist of all hotels in the index.
* **facet** retourneert een navigatiestructuur die u kunt doorgeven aan een UI-besturingselement. Deze retourneert categorieën en een aantal. In this case, categories are based on a field conveniently called *Category*. There is no aggregation in Azure Cognitive Search, but you can approximate aggregation via `facet`, which gives a count of documents in each category.

* **$top=2** retourneert twee documenten, om te illustreren dat u `top` kunt gebruiken om het aantal resultaten te verlagen of te verhogen.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Example (facet on numeric values): `search=spa&facet=Rating`

* This query is facet for rating, on a text search for *spa*. The term *Rating* can be specified as a facet because the field is marked as retrievable, filterable, and facetable in the index, and the values it contains (numeric, 1 through 5), are suitable for categorizing listings into groups.

* Alleen filterbare velden kunnen als facet worden gebruikt. Alleen ophaalbare velden kunnen in de resultaten worden geretourneerd.

* The *Rating* field is double-precision floating point and the grouping will be by precise value. For more information on grouping by interval (for instance, "3 star ratings," "4 star ratings," etc.), see [How to implement faceted navigation in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Zoekresultaten markeren

Markeren betekent het toevoegen van opmaak aan tekst die overeenkomt met het trefwoord, overeenkomsten die in een bepaald veld zijn aangetroffen. Als de zoekterm verborgen is in een beschrijving, kunt u de treffers markeren om deze makkelijker te vinden.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Voorbeeld (gemarkeerd): `search=beach&highlight=Description`

* In this example, the formatted word *beach* is easier to spot in the description field.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Voorbeeld (taalkundige analyse): `search=beaches&highlight=Description`

* Full text search recognizes basic variations in word forms. In this case, search results contain highlighted text for "beach", for hotels that have that word in their searchable fields, in response to a keyword search on "beaches". De resultaten kunnen verschillende vormen van hetzelfde woord bevatten vanwege taalkundige analyse. 

* Azure Cognitive Search supports 56 analyzers from both Lucene and Microsoft. The default used by Azure Cognitive Search is the standard Lucene analyzer.

### <a name="fuzzy-search"></a> Fuzzy zoekopdrachten uitproberen

By default, misspelled query terms, like *seatle* for "Seattle", fail to return matches in typical search. Het volgende voorbeeld retourneert geen resultaten.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Voorbeeld (verkeerd gespelde term, niet verwerkt): `search=seatle`

U kunt fuzzy zoekopdrachten gebruiken om spelfouten te omzeilen. Fuzzy zoekopdrachten worden ingeschakeld wanneer u de volledige Lucene-querysyntaxis gebruikt, wat gebeurt wanneer u **queryType=full** voor de query instelt en de **~** aan de zoekreeks toevoegt.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Voorbeeld (verkeerd gespelde term, verwerkt): `search=seatle~&queryType=full`

This example now returns documents that include matches on "Seattle".

Als **queryType** niet is opgegeven, wordt standaard de eenvoudige queryparser gebruikt. De eenvoudige queryparser is sneller, maar als u gebruik wilt maken van fuzzy zoeken, reguliere expressies, zoeken op nabijheid of andere geavanceerde typen query's, dan hebt u de volledige syntaxis nodig.

Fuzzy zoekopdrachten en zoekopdrachten met jokertekens hebben implicaties voor zoekresultaten. Taalkundige analyse wordt niet uitgevoerd op deze queryindelingen. Before using fuzzy and wildcard search, review [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) and look for the section about exceptions to lexical analysis.

For more information about query scenarios enabled by the full query parser, see [Lucene query syntax in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Georuimtelijk zoeken uitproberen

Georuimtelijk zoeken wordt ondersteund door het [gegevenstype edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) voor een veld met coördinaten. Geosearch is een type filter dat wordt opgegeven bij [Filter OData-syntaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Voorbeeld (filters voor geografische coördinaten): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Met dit voorbeeld worden alle resultaten voor positionele gegevens gefilterd die minder dan 5 kilometer zijn verwijderd van een opgegeven punt (opgegeven als coördinaten voor lengte- en breedtegraad). Door **$count** toe te voegen, kunt u zien hoeveel resultaten er worden geretourneerd als u de afstand of de coördinaten wijzigt.

Georuimtelijk zoeken is handig als uw zoektoepassing een functie 'in mijn buurt zoeken' heeft of gebruikmaakt van kaartnavigatie. Dit is echter niet een zoekopdracht in volledige tekst. If you have user requirements for searching on a city or country/region by name, add fields containing city or country/region names, in addition to coordinates.

## <a name="takeaways"></a>Opgedane kennis

This tutorial provided a quick introduction to Azure Cognitive Search using the Azure portal.

U hebt geleerd hoe u een zoekindex maakt met de wizard **Gegevens importeren**. U hebt geleerd over [indexeerfuncties](search-indexer-overview.md), en over de basiswerkstroom voor indexontwerp, waaronder [ondersteunde wijzigingen in een gepubliceerde index](https://docs.microsoft.com/rest/api/searchservice/update-index).

Met behulp van de **Search Explorer** in de Azure Portal hebt u wat querysyntaxis geleerd via praktijkvoorbeelden die belangrijke mogelijkheden hebben gedemonstreerd, zoals filters, resultaatmarkering, fuzzy zoekopdrachten en op geografische locaties zoeken.

U hebt ook geleerd hoe u in de portal kunt zoeken naar indexen, indexeerfuncties en gegevensbronnen. Met elke nieuwe gegevensbron in de toekomst kunt u de portal gebruiken om met een minimale inspanning snel de definities of de veldverzamelingen ervan te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

When you're working in your own subscription, it's a good idea at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you are using a free service, remember that you are limited to three indexes, indexers, and data sources. You can delete individual items in the portal to stay under the limit. 

## <a name="next-steps"></a>Volgende stappen

While the portal is useful for initial exploration and small tasks, reviewing the APIs early on will help you understand the concepts and workflow on a deeper level:

> [!div class="nextstepaction"]
> [Een index maken met behulp van de .NET-SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)