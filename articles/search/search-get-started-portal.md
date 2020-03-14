---
title: Een zoek index maken in de Azure Portal
titleSuffix: Azure Cognitive Search
description: In deze Quick Start van de portal leert u hoe u de wizard gegevens importeren gebruikt om uw eerste zoek index in azure Cognitive Search te maken, te laden en op te vragen.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 6a3bbdae0d3fa898621c1c805388252beb891ecf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241582"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quick Start: een Azure Cognitive Search-index maken in de Azure Portal
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Gebruik de wizard **gegevens importeren** en **Zoek** hulpprogramma's van de portal om snel op concepten te komen en interessante query's te schrijven voor een index binnen enkele minuten.

Als de hulpprogram ma's te beperkt zijn, kunt u een [op code gebaseerde inleiding voor het Program meren van Azure Cognitive Search in .net](search-howto-dotnet-sdk.md) overwegen of [postman gebruiken voor het maken van rest API-aanroepen](search-get-started-postman.md). 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

[Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken. 

### <a name="check-for-space"></a>Controleren of er voldoende ruimte is

Veel klanten beginnen met de gratis service. Deze versie is beperkt tot drie indexen drie gegevensbronnen en drie indexeerfuncties. Zorg ervoor dat er voldoende ruimte is voor extra items voordat u begint. In deze zelfstudie wordt van elk object één exemplaar gemaakt.

Op secties in het servicedashboard wordt weergegeven hoeveel indexen, indexeerfuncties en gegevensbronnen u al hebt. 

![Lijsten met indexen, indexeerfuncties en gegevensbronnen](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Een index maken en gegevens laden

Zoekopdrachten worden herhaald over een [*index*](search-what-is-an-index.md) die doorzoekbare gegevens, metagegevens en aanvullende constructies bevat, die worden gebruikt om bepaald zoekgedrag te optimaliseren.

Voor deze zelf studie gebruiken we een ingebouwde voor beeld-gegevensset die met behulp van een [*Indexeer functie*](search-indexer-overview.md) kan worden verkend via de [wizard **gegevens importeren** ](search-import-data-portal.md). Een indexeerfunctie is een bronspecifieke verkenner die metagegevens en inhoud uit ondersteunde Azure-gegevensbronnen kan lezen. Normaal gesproken worden indexeerfuncties via een programma gebruikt, maar in de portal zijn ze toegankelijk via de wizard **Gegevens importeren**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Stap 1: de wizard Gegevens importeren starten en een gegevensbron maken

1. Klik op het dash board van de Azure Cognitive Search-service op **gegevens importeren** op de opdracht balk om een zoek index te maken en in te vullen.

   ![Opdracht Gegevens importeren](media/search-get-started-portal/import-data-cmd.png)

2. Klik in de wizard op **verbinding maken met uw gegevens > voor** **beelden** > **hotels-voor beeld**. Deze gegevensbron is ingebouwd. Als u uw eigen gegevensbron zou maken, zou u een naam, het type en de verbindingsgegevens moeten opgeven. Zodra de gegevensbron is gemaakt, wordt deze een bestaande gegevensbron genoemd die opnieuw kan worden gebruikt voor andere bewerkingen.

   ![Voorbeeldgegevensset selecteren](media/search-get-started-portal/import-datasource-sample.png)

3. Ga door naar de volgende pagina.

### <a name="step-2---skip-the-enrich-content-page"></a>Stap 2: de pagina verrijkte inhoud overs Laan

De wizard ondersteunt het maken van een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) voor het opnemen van de Cognitive Services AI-algoritmen in indexering. 

Deze stap slaan we nu even over en we gaan naar **Doelindex aanpassen**.

   ![Stap voor cognitieve vaardigheden overslaan](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> U kunt een voor beeld van een AI-indexering door lopen in een [Snelstartgids](cognitive-search-quickstart-blob.md) of [zelf studie](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Stap 3: index configureren

Het maken van een index is meestal een op code gebaseerde oefening die is voltooid voordat gegevens worden geladen. Maar zoals deze zelfstudie laat zien, kan de wizard een basisindex genereren voor elke gegevensbron die kan worden verkend. Een index vereist minimaal een naam en een verzameling velden. Eén van de velden moet zijn gemarkeerd als de documentsleutel die elk document op unieke wijze identificeert. Bovendien kunt u taalanalyses of suggesters opgeven als u wilt dat automatisch aanvullen of het voorstellen van query's wordt gebruikt.

Velden bevatten gegevenstypen en kenmerken. De selectievakjes bovenaan zijn *indexkenmerken* die bepalen hoe het veld wordt gebruikt.

* **Ophalen mogelijk** betekent dat dit veld wordt weergegeven in de lijst met zoekresultaten. U kunt afzonderlijke velden markeren als uitstel voor de zoek resultaten door dit selectie vakje uit te scha kelen, bijvoorbeeld voor velden die alleen worden gebruikt in filter expressies.
* **Sleutel** is de unieke id van het document. Het is altijd een tekenreeks en deze is vereist.
* Met de kenmerken **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten** bepaalt u of velden in een filter, een sorteervolgorde of een facetnavigatiestructuur worden gebruikt.
* **Doorzoekbaar** betekent dat een veld is opgenomen in een zoekopdracht in volledige tekst. Tekenreeksen zijn doorzoekbaar. Numerieke velden en Booleaanse waarden zijn vaak gemarkeerd als niet doorzoekbaar.

Opslagvereisten verschillen niet als gevolg van uw selectie. Als u bijvoorbeeld het kenmerk **Ophalen mogelijk** instelt voor meerdere velden, nemen de opslagvereisten niet toe.

Standaard wordt met de wizard de gegevensbron gescand op unieke id's als basis voor het sleutelveld. *Tekenreeksen* hebben de kenmerken **Ophaalbaar** en **Doorzoekbaar**. *Gehele getallen* hebben de kenmerken **Ophaalbaar**, **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten**.

1. Accepteer de standaardwaarden. 

   Als u de wizard een tweede keer opnieuw start met behulp van een bestaande hotels-gegevens bron, wordt de index niet geconfigureerd met de standaard kenmerken. Voor toekomstige importbewerkingen moet u de kenmerken handmatig selecteren. 

   ![Index gegenereerde hotels](media/search-get-started-portal/hotelsindex.png)

2. Ga door naar de volgende pagina.


### <a name="step-4---configure-indexer"></a>Stap 4: indexeerfunctie configureren

Klik in de wizard**Gegevens importeren** op **Indexeerfunctie** > **Naam** en typ een naam voor de indexeerfunctie.

Dit object definieert een uitvoerbaar proces. U kunt een terugkerend schema instellen, maar in dit geval gebruikt u de standaardoptie om de indexeerfunctie één keer meteen uit te voeren.

Klik op **Verzenden** om de indexeerfunctie te maken en tegelijkertijd uit te voeren.

  ![Indexeer functie voor hotels](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Voortgang controleren

De wizard leidt u naar de lijst met indexeerfuncties waar u de voortgang kunt controleren. Voor zelfnavigatie gaat u naar de overzichtspagina en klikt u op **Indexeerfuncties**.

Het kan een paar minuten duren voordat in de portal de pagina is bijgewerkt, maar u zou de zojuist gemaakte indexeerfunctie in de lijst moeten zien met de status die aangeeft dat deze wordt uitgevoerd of is geslaagd en met het aantal geïndexeerde documenten.

   ![Voortgangsbericht voor de indexeerfunctie](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>De index bekijken

Op de pagina hoofd service vindt u koppelingen naar de resources die in de Azure Cognitive Search-service zijn gemaakt.  Als u de index wilt bekijken die u zojuist hebt gemaakt, klikt u op **Indexen** in de lijst met koppelingen. 

Wacht tot de portal pagina is vernieuwd. Na enkele minuten ziet u de index met het aantal documenten en de opslag grootte.

   ![Lijst met indexen op het servicedashboard](media/search-get-started-portal/indexes-list.png)

In deze lijst kunt u op de voor *beeld-* index van hotels klikken die u zojuist hebt gemaakt, het index schema weer geven. en eventueel nieuwe velden toevoegen. 

Op het tabblad **Velden** wordt het indexschema getoond. Scroll naar de onderkant van de lijst om een nieuw veld in te voeren. In de meeste gevallen kunt u geen bestaande velden wijzigen. Bestaande velden hebben een fysieke weer gave in azure Cognitive Search en zijn dus niet-wijzigbaar, niet zelfs in code. Als u een bestaand veld fundamenteel wilt wijzigen, maakt u een nieuwe index en verwijdert u het oorspronkelijke veld.

   ![voorbeeld van indexdefinitie](media/search-get-started-portal/sample-index-def.png)

Andere constructies, zoals scoreprofielen en CORS-opties, kunnen op elk gewenst moment worden toegevoegd.

Neem even de tijd voor het bekijken van de indexdefinitieopties om duidelijk te begrijpen wat u wel en niet kunt bewerken tijdens indexontwerp. Uitgegrijsde opties geven aan dat een waarde niet kan worden gewijzigd of verwijderd. 

## <a name="query-index"></a> Query die Search Explorer gebruikt

U zou nu een zoekindex moeten hebben die klaar is om query’s uit te voeren met de ingebouwde querypagina [**Search Explorer**](search-explorer.md). Deze pagina biedt een zoekvak waarmee u willekeurige queryreeksen kunt testen.

**Search Explorer** kan alleen omgaan met [REST API-aanvragen](https://docs.microsoft.com/rest/api/searchservice/search-documents), maar er wordt syntaxis geaccepteerd voor [eenvoudige querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) en [volledige Lucene-queryparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), plus alle zoekparameters die beschikbaar zijn in bewerkingen voor [Search-document-REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> De volgende stappen worden op 6m08s getoond in de [overzichts video van Azure Cognitive Search](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klik op **Search explorer** in de opdrachtbalk.

   ![Opdracht Search Explorer](media/search-get-started-portal/search-explorer-cmd.png)

2. Kies in de vervolg keuzelijst **index** de optie *hotels-voor beeld-index*. Klik op de vervolg keuzelijst **API-versie** om te zien welke rest api's beschikbaar zijn. Gebruik de algemeen beschik bare versie (2019-05-06) voor de onderstaande query's.

   ![Index- en API-opdrachten](media/search-get-started-portal/search-explorer-changeindex.png)

3. Plak in de zoekbalk de onderstaande querytekenreeksen en klik op **Zoeken**.

   ![Querytekenreeks en knop Zoeken](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Voorbeelden van query's

U kunt termen en zinnen invoeren, vergelijkbaar met wat u in een zoekopdracht in Bing of Google doet, of u kunt volledig opgegeven query-expressies invoeren. Resultaten worden geretourneerd als uitgebreide JSON-documenten.

### <a name="simple-query-with-top-n-results"></a>Eenvoudige query met de bovenste N resultaten

#### <a name="example-string-query-searchspa"></a>Voorbeeld (tekenreeks): `search=spa`

* De **Zoek** parameter wordt gebruikt om een zoek opdracht voor zoeken in volledige tekst in te voeren, in dit geval het retour neren van Hotel gegevens voor degenen met *beveiligd-wachtwoord* verificatie in een doorzoekbaar veld in het document.

* Met **Search Explorer** worden resultaten geretourneerd in JSON. Deze indeling is uitgebreid en moeilijk te lezen als documenten een compacte structuur hebben. Dit is opzettelijk; dat het hele document zichtbaar is, is belangrijk voor ontwikkeldoeleinden, vooral tijdens het testen. Voor een betere gebruikerservaring moet u code schrijven waarmee [zoekresultaten](search-pagination-page-layout.md) belangrijke elementen weergeven.

* Documenten bestaan uit alle velden die zijn gemarkeerd als ‘Ophalen mogelijk’ in de index. Als u index kenmerken wilt weer geven in de portal, klikt u op *hotels-voor beeld* in de lijst **indexen** .

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Voorbeeld (geparameteriseerde query): `search=spa&$count=true&$top=10`

* Het symbool **&** wordt gebruikt om zoekparameters toe te voegen. Deze kunnen in willekeurige volgorde worden opgegeven.

* De para meter **$Count = True** retourneert het totale aantal geretourneerde documenten. Deze waarde verschijnt bovenaan de zoekresultaten. U kunt filterquery's controleren door de wijzigingen te controleren die door **$count=true** worden gerapporteerd. Lage aantallen geven aan dat uw filter werkt.

* De **$Top = 10** retourneert de hoogst geclassificeerde tien documenten uit het totaal. Standaard retourneert Azure Cognitive Search de eerste treffers van 50. U kunt dit aantal vergroten of verkleinen via **$top**.

### <a name="filter-query"></a>De query filteren

Filters zijn opgenomen in de zoekopdrachten wanneer u de parameter **$filter** toevoegt. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Voorbeeld (gefilterd): `search=beach&$filter=Rating gt 4`

* De parameter **$filter** retourneert resultaten die voldoen aan de criteria die u hebt opgegeven. In dit geval zijn de classificaties groter dan 4.

* Filtersyntaxis is een OData-constructie. Zie [OData-syntaxis filteren](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) voor meer informatie.

### <a name="facet-query"></a>De query facetteren

Facetfilters zijn opgenomen in zoekopdrachten. U kunt de facetparameter gebruiken om een samengevoegd aantal documenten te retourneren die overeenkomen met een door u opgegeven facetwaarde.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Voorbeeld (gefacetteerd met bereikvermindering): `search=*&facet=Category&$top=2`

* **search=** * is een lege zoekopdracht. Met een lege zoekopdracht wordt naar alles gezocht. Eén reden om een lege query in te dienen, is om de hele set documenten te filteren of als facet te gebruiken. U wilt bijvoorbeeld dat een facet navigatie structuur bestaat uit alle hotels in de index.
* **facet** retourneert een navigatiestructuur die u kunt doorgeven aan een UI-besturingselement. Deze retourneert categorieën en een aantal. In dit geval zijn categorieën gebaseerd op een veld dat gemakkelijk een *categorie*wordt genoemd. Er is geen aggregatie in azure Cognitive Search, maar u kunt de aggregatie met behulp van `facet`benaderen. Dit geeft een telling van documenten in elke categorie.

* **$top=2** retourneert twee documenten, om te illustreren dat u `top` kunt gebruiken om het aantal resultaten te verlagen of te verhogen.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Voor beeld (facet op numerieke waarden): `search=spa&facet=Rating`

* Deze query is facet voor classificatie, in een tekst zoekopdracht naar *beveiligd-wachtwoord*verificatie. De term *classificatie* kan worden opgegeven als een facet, omdat het veld is gemarkeerd als ophaalbaar, filterbaar en bruikbaar in de index. de waarden die het bevat (numeriek, 1 tot en met 5), zijn geschikt voor het categoriseren van vermeldingen in groepen.

* Alleen filterbare velden kunnen als facet worden gebruikt. Alleen ophaalbare velden kunnen in de resultaten worden geretourneerd.

* Het veld *classificatie* is een drijvende komma met dubbele precisie en de groepering is nauw keurige waarde. Zie [facet navigatie implementeren in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range)voor meer informatie over groeperen op interval (bijvoorbeeld ' 3 ster beoordelingen ', ' 4 ster waarderingen ' enzovoort).


### <a name="highlight-query"></a> Zoekresultaten markeren

Markeren betekent het toevoegen van opmaak aan tekst die overeenkomt met het trefwoord, overeenkomsten die in een bepaald veld zijn aangetroffen. Als de zoekterm verborgen is in een beschrijving, kunt u de treffers markeren om deze makkelijker te vinden.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Voorbeeld (gemarkeerd): `search=beach&highlight=Description`

* In dit voor beeld is het geopgemaakte woord *strand* makkelijker te herkennen in het veld Beschrijving.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Voorbeeld (taalkundige analyse): `search=beaches&highlight=Description`

* Zoek opdracht in volledige tekst herkent basis variaties in Word-formulieren. In dit geval bevatten de zoek resultaten gemarkeerde tekst voor het "strand" voor hotels die dat woord in hun Doorzoek bare velden hebben, in antwoord op een zoek opdracht naar tref woorden op "stranden". De resultaten kunnen verschillende vormen van hetzelfde woord bevatten vanwege taalkundige analyse. 

* Azure Cognitive Search ondersteunt 56-analyse functies van zowel Lucene als micro soft. De standaard die door Azure Cognitive Search wordt gebruikt, is de Standard lucene Analyzer.

### <a name="fuzzy-search"></a> Fuzzy zoekopdrachten uitproberen

Standaard worden onjuist gespelde query termen, zoals *Seatle* voor "Seattle", niet in de normale zoek opdracht geretourneerd. Het volgende voorbeeld retourneert geen resultaten.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Voorbeeld (verkeerd gespelde term, niet verwerkt): `search=seatle`

U kunt fuzzy zoekopdrachten gebruiken om spelfouten te omzeilen. Fuzzy zoekopdrachten worden ingeschakeld wanneer u de volledige Lucene-querysyntaxis gebruikt, wat gebeurt wanneer u **queryType=full** voor de query instelt en de **~** aan de zoekreeks toevoegt.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Voorbeeld (verkeerd gespelde term, verwerkt): `search=seatle~&queryType=full`

In dit voor beeld worden documenten geretourneerd die overeenkomen met overeenkomsten op ' Seattle '.

Als **queryType** niet is opgegeven, wordt standaard de eenvoudige queryparser gebruikt. De eenvoudige queryparser is sneller, maar als u gebruik wilt maken van fuzzy zoeken, reguliere expressies, zoeken op nabijheid of andere geavanceerde typen query's, dan hebt u de volledige syntaxis nodig.

Fuzzy zoekopdrachten en zoekopdrachten met jokertekens hebben implicaties voor zoekresultaten. Taalkundige analyse wordt niet uitgevoerd op deze queryindelingen. Controleer voordat u fuzzy-en Joker tekens zoekt, [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) en zoek naar de sectie over uitzonde ringen voor lexicale analyse.

Zie voor meer informatie over query scenario's die zijn ingeschakeld door de volledige query-parser, de [syntaxis van de opdracht lucene in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Georuimtelijk zoeken uitproberen

Georuimtelijk zoeken wordt ondersteund door het [gegevenstype edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) voor een veld met coördinaten. Geosearch is een type filter dat wordt opgegeven bij [Filter OData-syntaxis](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Voorbeeld (filters voor geografische coördinaten): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Met dit voorbeeld worden alle resultaten voor positionele gegevens gefilterd die minder dan 5 kilometer zijn verwijderd van een opgegeven punt (opgegeven als coördinaten voor lengte- en breedtegraad). Door **$count** toe te voegen, kunt u zien hoeveel resultaten er worden geretourneerd als u de afstand of de coördinaten wijzigt.

Georuimtelijk zoeken is handig als uw zoektoepassing een functie 'in mijn buurt zoeken' heeft of gebruikmaakt van kaartnavigatie. Dit is echter niet een zoekopdracht in volledige tekst. Als u gebruikers vereisten hebt om op naam te zoeken in een stad of land/regio, voegt u naast coördinaten ook velden toe met de namen stad of land/regio.

## <a name="takeaways"></a>Opgedane kennis

In deze zelf studie hebt u een snelle inleiding tot Azure Cognitive Search met behulp van de Azure Portal.

U hebt geleerd hoe u een zoekindex maakt met de wizard **Gegevens importeren**. U hebt geleerd over [indexeerfuncties](search-indexer-overview.md), en over de basiswerkstroom voor indexontwerp, waaronder [ondersteunde wijzigingen in een gepubliceerde index](https://docs.microsoft.com/rest/api/searchservice/update-index).

Met behulp van de **Search Explorer** in de Azure Portal hebt u wat querysyntaxis geleerd via praktijkvoorbeelden die belangrijke mogelijkheden hebben gedemonstreerd, zoals filters, resultaatmarkering, fuzzy zoekopdrachten en op geografische locaties zoeken.

U hebt ook geleerd hoe u in de portal kunt zoeken naar indexen, indexeerfuncties en gegevensbronnen. Met elke nieuwe gegevensbron in de toekomst kunt u de portal gebruiken om met een minimale inspanning snel de definities of de veldverzamelingen ervan te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Een portal wizard gebruiken om een kant-en-klare Web-app te genereren die in een browser wordt uitgevoerd. U kunt deze wizard uitproberen op de kleine index die u zojuist hebt gemaakt, of een van de ingebouwde voorbeeld gegevens sets gebruiken voor een uitgebreidere Zoek ervaring.

> [!div class="nextstepaction"]
> [Een zoek-app maken in de portal](search-create-app-portal.md)