---
title: Een zoekindex maken in de Azure-portal
titleSuffix: Azure Cognitive Search
description: Gebruik in deze Snelstart van Azure-portal de wizard Gegevens importeren om uw eerste zoekindex in Azure Cognitive Search te maken, laden en op te vragen.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 8324ca0184c508591fa4568175bad0f606f952a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369459"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Snelstart: een Azure Cognitive Search-index maken in de Azure-portal
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C #](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [Powershell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Gebruik de wizard **Gegevens importeren van** de portal en de **hulpprogramma's Search explorer** om snel concepten op te voeren en binnen enkele minuten interessante query's te schrijven op een index.

Als de hulpprogramma's te beperkend zijn, u een [op code gebaseerde inleiding overwegen om Azure Cognitive Search programmeren in .NET](search-howto-dotnet-sdk.md) of Postman gebruiken [voor het maken van REST API-aanroepen.](search-get-started-postman.md) 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="prerequisites"></a>Vereisten

[Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis service voor deze quickstart. 

### <a name="check-for-space"></a>Controleren of er voldoende ruimte is

Veel klanten beginnen met de gratis service. Deze versie is beperkt tot drie indexen drie gegevensbronnen en drie indexeerfuncties. Zorg ervoor dat er voldoende ruimte is voor extra items voordat u begint. In deze zelfstudie wordt van elk object één exemplaar gemaakt.

Op secties in het servicedashboard wordt weergegeven hoeveel indexen, indexeerfuncties en gegevensbronnen u al hebt. 

![Lijsten met indexen, indexeerfuncties en gegevensbronnen](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Een index maken en gegevens laden

Zoekopdrachten worden herhaald over een [*index*](search-what-is-an-index.md) die doorzoekbare gegevens, metagegevens en aanvullende constructies bevat, die worden gebruikt om bepaald zoekgedrag te optimaliseren.

Voor deze zelfstudie gebruiken we een ingebouwde voorbeeldgegevensset die kan worden gecrawld met behulp van een [*indexer*](search-indexer-overview.md) via de wizard [ **Gegevens importeren** ](search-import-data-portal.md). Een indexeerfunctie is een bronspecifieke verkenner die metagegevens en inhoud uit ondersteunde Azure-gegevensbronnen kan lezen. Normaal gesproken worden indexeerfuncties via een programma gebruikt, maar in de portal zijn ze toegankelijk via de wizard **Gegevens importeren**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Stap 1: de wizard Gegevens importeren starten en een gegevensbron maken

1. Klik in het azure cognitive search-servicedashboard op **Gegevens importeren** op de opdrachtbalk om een zoekindex te maken en in te vullen.

   ![Opdracht Gegevens importeren](media/search-get-started-portal/import-data-cmd.png)

2. Klik in de wizard op **Verbinding maken met uw gegevens** > **Voorbeelden** > **hotels-voorbeeld**. Deze gegevensbron is ingebouwd. Als u uw eigen gegevensbron zou maken, zou u een naam, het type en de verbindingsgegevens moeten opgeven. Zodra de gegevensbron is gemaakt, wordt deze een bestaande gegevensbron genoemd die opnieuw kan worden gebruikt voor andere bewerkingen.

   ![Voorbeeldgegevensset selecteren](media/search-get-started-portal/import-datasource-sample.png)

3. Ga door naar de volgende pagina.

### <a name="step-2---skip-the-enrich-content-page"></a>Stap 2 - De pagina Inhoud verrijken overslaan

De wizard ondersteunt het maken van een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) voor het opnemen van de AI-algoritmen voor Cognitive Services in indexering. 

Deze stap slaan we nu even over en we gaan naar **Doelindex aanpassen**.

   ![Stap voor cognitieve vaardigheden overslaan](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> U een voorbeeld van een AI-indexering doorlopen in een [snelle start](cognitive-search-quickstart-blob.md) of [zelfstudie.](cognitive-search-tutorial-blob.md)

### <a name="step-3---configure-index"></a>Stap 3: index configureren

Het maken van een index is meestal een op code gebaseerde oefening die is voltooid voordat gegevens worden geladen. Maar zoals deze zelfstudie laat zien, kan de wizard een basisindex genereren voor elke gegevensbron die kan worden verkend. Een index vereist minimaal een naam en een verzameling velden. Eén van de velden moet zijn gemarkeerd als de documentsleutel die elk document op unieke wijze identificeert. Bovendien kunt u taalanalyses of suggesters opgeven als u wilt dat automatisch aanvullen of het voorstellen van query's wordt gebruikt.

Velden bevatten gegevenstypen en kenmerken. De selectievakjes bovenaan zijn *indexkenmerken* die bepalen hoe het veld wordt gebruikt.

* **Ophalen mogelijk** betekent dat dit veld wordt weergegeven in de lijst met zoekresultaten. U afzonderlijke velden markeren als verboden voor zoekresultaten door dit selectievakje te wissen, bijvoorbeeld voor velden die alleen in filterexpressies worden gebruikt.
* **Sleutel** is de unieke id van het document. Het is altijd een tekenreeks en deze is vereist.
* Met de kenmerken **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten** bepaalt u of velden in een filter, een sorteervolgorde of een facetnavigatiestructuur worden gebruikt.
* **Doorzoekbaar** betekent dat een veld is opgenomen in een zoekopdracht in volledige tekst. Tekenreeksen zijn doorzoekbaar. Numerieke velden en Booleaanse waarden zijn vaak gemarkeerd als niet doorzoekbaar.

Opslagvereisten verschillen niet als gevolg van uw selectie. Als u bijvoorbeeld het kenmerk **Ophalen mogelijk** instelt voor meerdere velden, nemen de opslagvereisten niet toe.

Standaard wordt met de wizard de gegevensbron gescand op unieke id's als basis voor het sleutelveld. *Tekenreeksen* worden toegeschreven als **Opvraagbaar** en **Doorzoekbaar**. *Gehele getallen* worden toegeschreven als **Opvraagbaar**, **Filterbaar**, **Sorteerbaar**en **Facetable**.

1. Accepteer de standaardwaarden. 

   Als u de wizard een tweede keer opnieuw uitvoert met een bestaande gegevensbron voor hotels, wordt de index niet geconfigureerd met standaardkenmerken. Voor toekomstige importbewerkingen moet u de kenmerken handmatig selecteren. 

   ![Gegenereerde hotels index](media/search-get-started-portal/hotelsindex.png)

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

De hoofdservicepagina bevat koppelingen naar de bronnen die zijn gemaakt in uw Azure Cognitive Search-service.  Als u de index wilt bekijken die u zojuist hebt gemaakt, klikt u op **Indexen** in de lijst met koppelingen. 

Wacht tot de portalpagina is vernieuwd. Na een paar minuten ziet u de index met een documenttelling en de opslaggrootte.

   ![Lijst met indexen op het servicedashboard](media/search-get-started-portal/indexes-list.png)

In deze lijst u klikken op de *hotels-voorbeeldindex* die u zojuist hebt gemaakt, het indexschema weergeven. en eventueel nieuwe velden toevoegen. 

Op het tabblad **Velden** wordt het indexschema getoond. Scroll naar de onderkant van de lijst om een nieuw veld in te voeren. In de meeste gevallen kunt u geen bestaande velden wijzigen. Bestaande velden hebben een fysieke weergave in Azure Cognitive Search en zijn dus niet-aanpasbaar, zelfs niet in code. Als u een bestaand veld fundamenteel wilt wijzigen, maakt u een nieuwe index en verwijdert u het oorspronkelijke veld.

   ![voorbeeld van indexdefinitie](media/search-get-started-portal/sample-index-def.png)

Andere constructies, zoals scoreprofielen en CORS-opties, kunnen op elk gewenst moment worden toegevoegd.

Neem even de tijd voor het bekijken van de indexdefinitieopties om duidelijk te begrijpen wat u wel en niet kunt bewerken tijdens indexontwerp. Uitgegrijsde opties geven aan dat een waarde niet kan worden gewijzigd of verwijderd. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Query die Search Explorer gebruikt

U zou nu een zoekindex moeten hebben die klaar is om query’s uit te voeren met de ingebouwde querypagina [**Search Explorer**](search-explorer.md). Deze pagina biedt een zoekvak waarmee u willekeurige queryreeksen kunt testen.

**Search Explorer** kan alleen omgaan met [REST API-aanvragen](https://docs.microsoft.com/rest/api/searchservice/search-documents), maar er wordt syntaxis geaccepteerd voor [eenvoudige querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) en [volledige Lucene-queryparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), plus alle zoekparameters die beschikbaar zijn in bewerkingen voor [Search-document-REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> De volgende stappen worden op 6m08s gedemonstreerd in de [video Azure Cognitive Search Overview.](https://channel9.msdn.com/Events/Connect/2016/138)
>

1. Klik op **Search explorer** in de werkbalk.

   ![Opdracht Search Explorer](media/search-get-started-portal/search-explorer-cmd.png)

2. Kies in de **vervolgkeuzelijst Index** *de hotels-sample-index*. Klik op de vervolgkeuzelijst **API-versie** om te zien welke REST-API's beschikbaar zijn. Gebruik voor de onderstaande query's de algemeen beschikbare versie (2019-05-06).

   ![Index- en API-opdrachten](media/search-get-started-portal/search-explorer-changeindex.png)

3. Plak in de zoekbalk de onderstaande querytekenreeksen en klik op **Zoeken**.

   ![Querytekenreeks en knop Zoeken](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Voorbeelden van query's

U kunt termen en zinnen invoeren, vergelijkbaar met wat u in een zoekopdracht in Bing of Google doet, of u kunt volledig opgegeven query-expressies invoeren. Resultaten worden geretourneerd als uitgebreide JSON-documenten.

### <a name="simple-query-with-top-n-results"></a>Eenvoudige query met de bovenste N resultaten

#### <a name="example-string-query-searchspa"></a>Voorbeeld (tekenreeks): `search=spa`

* De **zoekparameter** wordt gebruikt om een trefwoord zoeken voor full text zoeken, in dit geval, het retourneren van hotelgegevens voor die *spa* in een doorzoekbaar veld in het document.

* Met **Search Explorer** worden resultaten geretourneerd in JSON. Deze indeling is uitgebreid en moeilijk te lezen als documenten een compacte structuur hebben. Dit is opzettelijk; dat het hele document zichtbaar is, is belangrijk voor ontwikkeldoeleinden, vooral tijdens het testen. Voor een betere gebruikerservaring moet u code schrijven waarmee [zoekresultaten](search-pagination-page-layout.md) belangrijke elementen weergeven.

* Documenten bestaan uit alle velden die zijn gemarkeerd als ‘Ophalen mogelijk’ in de index. Als u indexkenmerken in de portal wilt weergeven, klikt u op *hotels-voorbeeld* in de lijst **Indexen.**

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Voorbeeld (geparameteriseerde query): `search=spa&$count=true&$top=10`

* Het **&** symbool wordt gebruikt om zoekparameters toe te passen, die in elke volgorde kunnen worden opgegeven.

* De parameter **$count=true** retourneert het totale aantal geretourneerde documenten. Deze waarde verschijnt bovenaan de zoekresultaten. U kunt filterquery's controleren door de wijzigingen te controleren die door **$count=true** worden gerapporteerd. Lage aantallen geven aan dat uw filter werkt.

* De **$top=10** retourneert de hoogst gerangschikte 10 documenten van het totaal. Azure Cognitive Search retourneert standaard de eerste 50 beste overeenkomsten. U kunt dit aantal vergroten of verkleinen via **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a>De query filteren

Filters zijn opgenomen in de zoekopdrachten wanneer u de parameter **$filter** toevoegt. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Voorbeeld (gefilterd): `search=beach&$filter=Rating gt 4`

* De parameter **$filter** retourneert resultaten die voldoen aan de criteria die u hebt opgegeven. In dit geval, beoordelingen groter dan 4.

* Filtersyntaxis is een OData-constructie. Zie [OData-syntaxis filteren](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

### <a name="facet-the-query"></a><a name="facet-query"></a>De query facetteren

Facetfilters zijn opgenomen in zoekopdrachten. U kunt de facetparameter gebruiken om een samengevoegd aantal documenten te retourneren die overeenkomen met een door u opgegeven facetwaarde.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Voorbeeld (gefacetteerd met bereikvermindering): `search=*&facet=Category&$top=2`

* **search=*** is een lege zoekopdracht. Met een lege zoekopdracht wordt naar alles gezocht. Eén reden om een lege query in te dienen, is om de hele set documenten te filteren of als facet te gebruiken. U wilt bijvoorbeeld dat een facetingsnavigatiestructuur bestaat uit alle hotels in de index.
* **facet** retourneert een navigatiestructuur die u kunt doorgeven aan een UI-besturingselement. Deze retourneert categorieën en een aantal. In dit geval zijn categorieën gebaseerd op een veld dat handig *Categorie*wordt genoemd. Er is geen aggregatie in Azure Cognitive `facet`Search, maar u aggregatie benaderen via, die een telling van documenten in elke categorie geeft.

* **$top=2** retourneert twee documenten, om te illustreren dat u `top` kunt gebruiken om het aantal resultaten te verlagen of te verhogen.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Voorbeeld (gefacetteerd op numerieke waarden): `search=spa&facet=Rating`

* Deze query is facet voor rating, op een tekst zoeken naar *spa*. De term *Rating* kan worden opgegeven als een facet omdat het veld is gemarkeerd als opvraagbaar, filterbaar en facetable in de index, en de waarden die het bevat (numeriek, 1 tot en met 5), zijn geschikt voor het categoriseren van aanbiedingen in groepen.

* Alleen filterbare velden kunnen als facet worden gebruikt. Alleen ophaalbare velden kunnen in de resultaten worden geretourneerd.

* Het *veld Beoordeling* is een zwevend punt met dubbele precisie en de groepering wordt nauwkeurig bepaald. Zie [Faceted Navigation implementeren in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range)voor meer informatie over het groeperen op interval (bijvoorbeeld '3-sterrenbeoordelingen', '4-sterrenbeoordelingen', enz.).


### <a name="highlight-search-results"></a><a name="highlight-query"></a> Zoekresultaten markeren

Markeren betekent het toevoegen van opmaak aan tekst die overeenkomt met het trefwoord, overeenkomsten die in een bepaald veld zijn aangetroffen. Als de zoekterm verborgen is in een beschrijving, kunt u de treffers markeren om deze makkelijker te vinden.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Voorbeeld (gemarkeerd): `search=beach&highlight=Description`

* In dit voorbeeld is het opgemaakte *woordstrand* gemakkelijker te herkennen in het beschrijvingsveld.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Voorbeeld (taalkundige analyse): `search=beaches&highlight=Description`

* Full text search herkent basisvariaties in woordvormen. In dit geval bevatten zoekresultaten gemarkeerde tekst voor 'strand', voor hotels die dat woord in hun doorzoekbare velden hebben, als reactie op een trefwoordzoekopdracht op 'stranden'. De resultaten kunnen verschillende vormen van hetzelfde woord bevatten vanwege taalkundige analyse. 

* Azure Cognitive Search ondersteunt 56 analyzers van zowel Lucene als Microsoft. De standaardwaarde die wordt gebruikt door Azure Cognitive Search is de standaard Lucene analyzer.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Fuzzy zoekopdrachten uitproberen

Verkeerd gespelde querytermen, zoals *seatle* voor 'Seattle', geven standaard geen overeenkomsten terug in de typische zoekopdracht. Het volgende voorbeeld retourneert geen resultaten.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Voorbeeld (verkeerd gespelde term, niet verwerkt): `search=seatle`

U kunt fuzzy zoekopdrachten gebruiken om spelfouten te omzeilen. Fuzzy zoekopdrachten worden ingeschakeld wanneer u de volledige Lucene-querysyntaxis gebruikt, wat gebeurt wanneer u **queryType=full** voor de query instelt en de **~** aan de zoekreeks toevoegt.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Voorbeeld (verkeerd gespelde term, verwerkt): `search=seatle~&queryType=full`

In dit voorbeeld worden nu documenten geretourneerd die overeenkomsten bevatten op 'Seattle'.

Als **queryType** niet is opgegeven, wordt standaard de eenvoudige queryparser gebruikt. De eenvoudige queryparser is sneller, maar als u gebruik wilt maken van fuzzy zoeken, reguliere expressies, zoeken op nabijheid of andere geavanceerde typen query's, dan hebt u de volledige syntaxis nodig.

Fuzzy zoekopdrachten en zoekopdrachten met jokertekens hebben implicaties voor zoekresultaten. Taalkundige analyse wordt niet uitgevoerd op deze queryindelingen. Voordat u fuzzy- en wildcardzoekt gebruikt, bekijkt u [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) en zoekt u naar de sectie over uitzonderingen op lexicale analyse.

Zie [Lucene querysyntaxis in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)voor meer informatie over queryscenario's die zijn ingeschakeld door de volledige queryparser.

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Georuimtelijk zoeken uitproberen

 Georuimtelijk zoeken wordt ondersteund door het [gegevenstype edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) op een veld met coördinaten. Geosearch is een type filter dat wordt opgegeven bij [Filter OData-syntaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Voorbeeld (filters voor geografische coördinaten): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Met dit voorbeeld worden alle resultaten voor positionele gegevens gefilterd die minder dan 5 kilometer zijn verwijderd van een opgegeven punt (opgegeven als coördinaten voor lengte- en breedtegraad). Door **$count** toe te voegen, kunt u zien hoeveel resultaten er worden geretourneerd als u de afstand of de coördinaten wijzigt.

Georuimtelijk zoeken is handig als uw zoektoepassing een functie 'in mijn buurt zoeken' heeft of gebruikmaakt van kaartnavigatie. Dit is echter niet een zoekopdracht in volledige tekst. Als u gebruikersvereisten hebt voor het zoeken op een stad of land/regio op naam, voegt u naast coördinaten ook velden toe met namen van stad of land/regio.

## <a name="takeaways"></a>Opgedane kennis

Deze zelfstudie gaf een snelle inleiding tot Azure Cognitive Search met behulp van de Azure-portal.

U hebt geleerd hoe u een zoekindex maakt met de wizard **Gegevens importeren**. U hebt geleerd over [indexeerfuncties](search-indexer-overview.md), en over de basiswerkstroom voor indexontwerp, waaronder [ondersteunde wijzigingen in een gepubliceerde index](https://docs.microsoft.com/rest/api/searchservice/update-index).

Met behulp van de **Search Explorer** in de Azure Portal hebt u wat querysyntaxis geleerd via praktijkvoorbeelden die belangrijke mogelijkheden hebben gedemonstreerd, zoals filters, resultaatmarkering, fuzzy zoekopdrachten en op geografische locaties zoeken.

U hebt ook geleerd hoe u in de portal kunt zoeken naar indexen, indexeerfuncties en gegevensbronnen. Met elke nieuwe gegevensbron in de toekomst kunt u de portal gebruiken om met een minimale inspanning snel de definities of de veldverzamelingen ervan te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Gebruik een portalwizard om een kant-en-klare webapp te genereren die in een browser wordt uitgevoerd. U deze wizard uitproberen op de kleine index die u zojuist hebt gemaakt, of een van de ingebouwde voorbeeldgegevenssets gebruiken voor een rijkere zoekervaring.

> [!div class="nextstepaction"]
> [Een zoek-app maken in de portal](search-create-app-portal.md)