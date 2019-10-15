---
title: Inleiding tot Azure Search-Azure Search
description: Azure Search is een volledig beheerde, gehoste service voor zoeken in de cloud van micro soft. Bekijk functie beschrijvingen, de werk stroom voor ontwikkel aars, vergelijkingen met andere producten van micro soft Search en hoe u aan de slag gaat.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 09/03/2019
ms.author: heidist
ms.openlocfilehash: b92838f7c43a11cd8bd4e1e19f906d33758bf495
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331154"
---
# <a name="what-is-azure-search"></a>Wat is Azure Search?

Azure Search is een Cloud oplossing op basis van een zoek opdracht als service die ontwikkel aars Api's en hulpprogram ma's biedt voor het toevoegen van een uitgebreide zoek ervaring via persoonlijke, heterogene inhoud in web-, mobiele en zakelijke toepassingen. Met uw aangepaste code wordt gegevens opname (indexering) aangeroepen om een index te maken en te laden. Aan de andere kant worden met de toepassings code query aanvragen aangevraagd en worden antwoorden verwerkt. De zoek ervaring wordt gedefinieerd in uw client met behulp van de functionaliteit van Azure Search, waarbij de query wordt uitgevoerd via een persistente index die u maakt, eigenaar en opslaat in uw service.

Architectuur van ![Azure Search architectuur](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Search")

Functionaliteit wordt beschikbaar gemaakt via een eenvoudige [REST API](/rest/api/searchservice/) of [.NET SDK](search-howto-dotnet-sdk.md) waarmee de inherente complexiteit van het ophalen van gegevens wordt gemaskeerd. Naast API’s biedt Azure Portal ondersteuning voor administratie- en inhoudsbeheer met hulpprogramma’s voor het ontwikkelen van prototypen en het doorzoeken van indexen. Omdat de service wordt uitgevoerd in de cloud, worden de infrastructuur en beschikbaarheid beheerd met Microsoft.

## <a name="when-to-use-azure-search"></a>Wanneer moet ik Azure Search gebruiken?

Azure Search is heel geschikt voor de volgende toepassings scenario's:

+ Consolidatie van heterogene inhouds typen in een persoonlijke, enkelvoudige, Doorzoek bare index. Query's zijn altijd een index die u maakt en laadt met documenten, en de index bevindt zich altijd in de Cloud op uw Azure Search service. U kunt een index vullen met stromen van JSON-documenten van elke bron of elk platform. Voor inhoud die in Azure is gebrond, kunt u ook een *Indexeer functie* gebruiken om gegevens in een index op te halen. Index definitie en beheer/eigendom is een belang rijke reden voor het gebruik van Azure Search.

+ Eenvoudige implementatie van zoek functies. Azure Search-Api's vereenvoudigen de constructie van query's, facet navigatie, filters (inclusief georuimtelijke Zoek opdrachten), synoniemen toewijzing, typeahead query's en relevantie afstemmen. Door ingebouwde functies te gebruiken, kunt u aan eind gebruikers verwachtingen voldoen voor een zoek ervaring die vergelijkbaar is met webzoekmachines.

+ Het indexeren van ongestructureerde tekst of het extra heren van tekst en informatie uit afbeeldings bestanden. De [cognitieve Zoek](cognitive-search-concept-intro.md) functie van Azure Search voegt AI-verwerking toe aan een Indexing-pijp lijn. Enkele veelvoorkomende toepassingen zijn onder andere OCR via gescande documenten, herkenning van entiteiten en het uitpakken van sleutel zinnen over grote documenten, taal detectie en tekst omzetting en sentiment analyse.

+ Er zijn taal vereisten die voldoen aan de aangepaste en taal analyse functies van Azure Search. Als u niet-Engelse inhoud hebt, ondersteunt Azure Search zowel de Lucene-analyse functies als de natuurlijke taal-processors van micro soft. U kunt analyse functies ook configureren voor het uitvoeren van gespecialiseerde verwerking van onbewerkte inhoud, zoals het filteren van diakritische tekens.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Functie beschrijvingen

| Core @ no__t-0search @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11 @ no__t-12 @ no__t-13 @ no__t-14 @ no__t-15 @ no__t-16 @ no__t-17 @ no__t-18 @ no__t-19 @ no__t-20 @ no__t-21 @ no__t-22 @ no__t-23 @ no__t-24  | Functies |
|-------------------|----------|
|Zoeken met vrije tekst | [**Zoeken in volledige tekst**](search-lucene-query-architecture.md) is een primair gebruiks voorbeeld voor de meeste zoek-apps. Query’s kunnen worden geformuleerd met behulp van een ondersteunde syntaxis. <br/><br/>[**Eenvoudige querysyntaxis**](query-simple-syntax.md) biedt logische operators, zoekoperators voor woordgroepen, operators voor achtervoegsels, operators voor bewerkingsvolgorde.<br/><br/>[**Lucene-querysyntaxis**](query-lucene-syntax.md) omvat alle bewerkingen in eenvoudige syntaxis, met uitbreidingen voor zoeken bij benadering, zoeken op nabijheid, termenverbetering en reguliere expressies.|
| Relevantie | [**Eenvoudig scoren**](index-add-scoring-profiles.md) is een belangrijk voordeel van Azure Search. Scoreprofielen worden gebruikt om relevantie te modelleren als een functie van waarden in de documenten zelf. Zo wilt u bijvoorbeeld dat nieuwere producten of afgeprijsde producten hoger worden weergegeven in de zoekresultaten. U kunt scoreprofielen ook bouwen met behulp van labels voor persoonlijke scores op basis van de zoekvoorkeuren van klanten die u hebt bijgehouden en apart opgeslagen. |
| Op geografische locaties zoeken | Met Azure Search worden geografische locaties verwerkt, gefilterd en weergegeven. Zo worden gebruikers in staat gesteld om gegevens te verkennen op basis van de afstand van een zoekresultaat tot een fysieke locatie. [Bekijk deze video ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) of [dit voorbeeld](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) voor meer informatie. |
| Filters en facetten | [**Facetnavigatie**](search-faceted-navigation.md) is ingeschakeld via een enkele queryparameter. Met Azure Search wordt een facetnavigatiestructuur geretourneerd die u kunt gebruiken als de code achter een lijst met categorieën, voor zelfgestuurd filteren (bijvoorbeeld om catalogusitems te filteren op prijsbereik of merk). <br/><br/> [**Filters**](query-odata-filter-orderby-syntax.md) kunnen worden gebruikt om facetnavigatie op te nemen in de gebruikersinterface van uw toepassing, het formuleren van query’s te verbeteren, en te filteren op basis van criteria die zijn opgegeven door gebruikers of ontwikkelaars. Maak filters met behulp van de OData-syntaxis. |
| Functies voor de gebruikerservaring | [**Automatisch aanvullen**](search-autocomplete-tutorial.md) kan worden ingeschakeld voor Type-Ahead query's in een zoek balk. <br/><br/>[**Zoeksuggesties** ](https://docs.microsoft.com/rest/api/searchservice/suggesters) werkt ook vanuit gedeeltelijke ingevoerde tekst in een zoekbalk, maar de resultaten zijn feitelijke documenten in uw index, geen zoektermen. <br/><br/>[**Synoniemen** ](search-synonyms.md) koppelt gelijkwaardige termen die het bereik van een query impliciet uitbreiden, zonder dat de gebruiker de andere termen hoeft op te geven. <br/><br/>Met [**Treffers markeren**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wordt tekstopmaak toegepast op een overeenkomend trefwoord in zoekresultaten. U kunt kiezen welke velden gemarkeerde fragmenten retourneren.<br/><br/>[**Sorteren**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wordt aangeboden voor meerdere velden via het indexschema en vervolgens tijdens het uitvoeren van een query in-/uitgeschakeld met een enkele zoekparameter.<br/><br/> [**Pagineren**](search-pagination-page-layout.md) en beperken van de zoekresultaten is eenvoudig dankzij het goed afgestemde besturingselement dat Azure Search biedt voor uw zoekresultaten.  <br/><br/>|

| AI @ no__t-0enrichment @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11       | Functies |
|-------------------|----------|
|AI-verrijkte documenten | [**Cognitieve Zoek opdrachten**](cognitive-search-concept-intro.md) voor afbeeldings-en tekst analyse kunnen worden toegepast op een indexerings pijplijn om tekst gegevens uit onbewerkte inhoud te halen. Enkele voorbeelden van [ingebouwde vaardigheden](cognitive-search-predefined-skills.md) zijn: optische tekenherkenning (waardoor gescande JPEG-bestanden doorzoekbaar worden), herkenning van entiteiten (waarmee een organisatie, naam of locatie kan worden geïdentificeerd), en herkenning van sleuteltermen. U kunt ook [aangepaste vaardigheden coderen](cognitive-search-create-custom-skill-example.md) om ze te koppelen aan een pijplijn. |
| Opgeslagen verrijkingen voor analyse en verbruik| [**Knowledge Store (preview)** ](knowledge-store-concept-intro.md) is een uitbrei ding van op AI gebaseerde indexeringen. Met Azure Storage als back-end kunt u verrijkingen opslaan die tijdens het indexeren zijn gemaakt. Deze artefacten kunnen worden gebruikt om u te helpen betere vaardig heden te ontwerpen, of om vorm en structuur uit amorphous of ambigue gegevens te maken. U kunt projecties van deze structuren maken die gericht zijn op specifieke workloads of gebruikers. U kunt de geëxtraheerde gegevens ook rechtstreeks analyseren of in andere apps laden.<br/><br/> |

| Data @ no__t-0import/Indexing | Functies |
|----------------------------------|----------|
| Gegevensbronnen | Azure Search-indexen accepteren gegevens uit elke willekeurige bron, mits ze zijn verzonden in een JSON-gegevensstructuur. <br/><br/> [**Indexeer functies**](search-indexer-overview.md) automatiseren gegevens opname voor ondersteunde Azure-gegevens bronnen en verwerken JSON-serialisatie. Maak verbinding met [Azure SQL database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)of [Azure Blob-opslag](search-howto-indexing-azure-blob-storage.md) om Doorzoek bare inhoud in primaire gegevens archieven te extra heren. Azure Blob-indexeerfuncties kunnen *documenten kraken* om [tekst te extraheren uit grote bestandsindelingen](search-howto-indexing-azure-blob-storage.md), zoals Microsoft Office, PDF en HTML-bestanden. |
| Hiërarchische en geneste gegevens structuren | Met [**complexe typen**](search-howto-complex-data-types.md) en verzamelingen kunt u vrijwel elk type JSON-structuur model leren als een Azure search index. Een-op-veel-en veel-op-veel-kardinaliteit kan systeem eigen worden aangegeven via verzamelingen, complexe typen en verzamelingen van complexe typen.|
| Taalkundige analyse | Analysefuncties zijn onderdelen die worden gebruikt om tekst te verwerken tijdens het indexeren en zoeken. Er zijn twee typen. <br/><br/>[**Aangepaste lexicale analysefuncties**](index-add-custom-analyzers.md) worden gebruikt voor complexe zoekquery’s met behulp van fonetische overeenkomsten en reguliere expressies. <br/><br/>[**Taalanalysefuncties**](index-add-language-analyzers.md) van Lucene of Microsoft worden gebruikt voor het intelligent verwerken van taalspecifieke taalkundige aspecten, zoals werkwoordtijden, geslacht, afwijkende meervoudsvormen voor zelfstandige naamwoorden, het opsplitsen van woorden, het afbreken van woorden (voor talen zonder spaties), en meer. <br/><br/>|


| Platform @ no__t-0level @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9 @ no__t-10 @ no__t-11 @ no__t-12 @ no__t-13| Functies |
|-------------------|----------|
| Hulpprogramma's voor het ontwikkelen van prototypen en voor controle | In de portal kunt u de [**wizard Gegevens importeren**](search-import-data-portal.md) gebruiken om indexeerfuncties te configureren, Index Designer om een index te bouwen, en [**Search Explorer**](search-explorer.md) om query’s te testen en scoreprofielen te verfijnen. U kunt ook elke gewenste index openen om het bijbehorende schema te bekijken. |
| Controle en diagnose | [**Schakel de bewakings functies**](search-monitor-usage.md) in om verder te gaan dan de metrische gegevens: in één oogopslag die altijd zichtbaar zijn in de portal. Metrische gegevens over query’s per seconde, latentie en beperkingen worden vastgelegd en gerapporteerd op portalpagina’s zonder dat hiervoor extra configuratie is vereist.|
| Versleuteling aan de serverzijde | [**Micro soft Managed encryption-at-rest**](search-security-overview.md#encrypted-transmission-and-storage) is ingebouwd in de interne opslaglaag en is onherroepelijk. Optioneel kunt u de standaard versleuteling aanvullen met door de [**klant beheerde versleutelings sleutels (preview)** ](search-security-manage-encryption-keys.md). Sleutels die u in Azure Key Vault maakt en beheert, worden gebruikt voor het versleutelen van indexen en synoniemen in Azure Search. |
| Infrastructuur | Het **maximaal beschikbare platform** zorgt ervoor dat de zoekservice uiterst betrouwbaar is. [Azure Search biedt een SLA voor 99,9% beschikbaarheid](https://azure.microsoft.com/support/legal/sla/search/v1_0/) als er naar behoren is geschaald.<br/><br/> Azure Search is een end-to-end oplossing die **volledig beheerd en schaalbaar** is en vereist geen enkel infrastructuurbeheer. De service kan worden aangepast aan uw persoonlijke behoeften door in twee dimensies te schalen voor het verwerken van meer documentopslag, een hogere querybelasting, of beide.<br/><br/>|

## <a name="how-to-use-azure-search"></a>Het gebruik van Azure Search
### <a name="step-1-provision-service"></a>Stap 1: Service inrichten
U kunt een Azure Search-service inrichten in [Azure Portal](https://portal.azure.com/) of met behulp van de [Azure Resource Management-API](/rest/api/searchmanagement/). U kunt kiezen voor de gratis service die is gedeeld met andere abonnees, of u kunt een [betaalde laag](https://azure.microsoft.com/pricing/details/search/) kiezen met toegewezen resources die alleen voor uw service worden gebruikt. Voor betaalde lagen kunt u een service in twee dimensies schalen: 

- Voeg replica’s toe om de capaciteit voor het verwerken van zware querybelastingen te vergroten.   
- Voeg partities toe om meer documenten op te kunnen slaan. 

Door de opslag van documenten en de doorvoer van query’s afzonderlijk aan te pakken kunt u het gebruik van resources afstemmen op basis van productievereisten.

### <a name="step-2-create-index"></a>Stap 2: Index maken
Voordat u doorzoekbare inhoud kunt uploaden, moet u eerst een Azure Search-index definiëren. Een index is vergelijkbaar met een databasetabel waarin uw gegevens zijn opgeslagen, en u kunt zoekquery’s uitvoeren voor een index. U definieert het indexschema dat moet worden toegewezen, om de structuur van de documenten te weerspiegelen waarin u wilt zoeken, vergelijkbaar met de velden in een database.

U kunt een schema maken in Azure Portal of via een programma met behulp van de [.NET SDK](search-howto-dotnet-sdk.md) of [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Stap 3: Gegevens laden
Nadat u een index hebt gedefinieerd, bent u klaar om inhoud te uploaden. U kunt hiervoor een push- of een pull-model gebruiken.

Met het pull-model worden gegevens opgehaald uit externe gegevensbronnen. Het model wordt ondersteund met *indexeerfuncties* die aspecten van de gegevensopname stroomlijnen en automatiseren, zoals het verbinden met, en lezen en serialiseren van gegevens. Er zijn [indexeerfuncties](/rest/api/searchservice/Indexer-operations) beschikbaar voor Azure Cosmos DB, Azure SQL Database, Azure Blob Storage en SQL Server gehost op een Azure-VM. U kunt een indexeerfunctie configureren op-aanvraag of als geplande gegevensvernieuwing.

Het push-model wordt geboden via de SDK of REST API’s, en gebruikt om bijgewerkte documenten naar een index te verzenden. U kunt gegevens uit nagenoeg elke gegevensset pushen met behulp van de JSON-indeling. Zie [Add, update, or delete Documents](/rest/api/searchservice/addupdate-or-delete-documents) (Documenten toevoegen, bijwerken of verwijderen) of [How to use the .NET SDK](search-howto-dotnet-sdk.md) (De .NET SDK gebruiken) voor informatie over het laden van gegevens.

### <a name="step-4-search"></a>Stap 4: Zoeken
Nadat u een index hebt gevuld, kunt u [Zoek query's](search-query-overview.md) naar uw service-eind punt verzenden met behulp van eenvoudige HTTP-aanvragen met [rest API](/rest/api/searchservice/Search-Documents) of de [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

U kunt door gaan met [het maken van uw eerste Zoek-app](tutorial-csharp-create-first-app.md) om een webpagina te bouwen en vervolgens uit te breiden waarmee gebruikers invoer wordt verzameld en resultaten worden verwerkt. U kunt [postman ook gebruiken voor interactieve rest](search-get-started-postman.md) -aanroepen of de ingebouwde [zoek Verkenner](search-explorer.md) in azure Portal om een bestaande index op te vragen.

## <a name="how-it-compares"></a>Vergelijking

Klanten vragen vaak wat de verschillen zijn tussen Azure Search en andere zoekgerelateerde oplossingen. In de volgende tabel worden de verschillen samengevat.

| Vergeleken met | Belangrijke verschillen |
|-------------|-----------------|
|Bing | Met [Bing Webzoekopdrachten-API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) wordt in de indexen op Bing.com gezocht naar overeenkomsten voor termen die u hebt ingediend. Indexen zijn gebouwd uit HTML, XML en andere webinhoud op openbare sites. [Bing Aangepaste zoekopdrachten](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/), dat op dezelfde basis is gebouwd, biedt dezelfde verkenningstechnologie voor typen webinhoud, met een bereik dat is ingesteld op afzonderlijke websites.<br/><br/>Met Azure Search wordt gezocht in een door u gedefinieerde index, die is gevuld met gegevens en documenten waarvan u de eigenaar bent, vaak afkomstig uit verschillende bronnen. Azure Search heeft verkenningsmogelijkheden voor bepaalde gegevensbronnen via [indexeerfuncties](search-indexer-overview.md), maar u kunt elk JSON-document dat overeenstemt met uw indexschema, naar een enkele geconsolideerde resource pushen. |
|Zoeken in database | Veel databaseplatforms beschikken over een ingebouwde zoekfunctie. SQL Server heeft een functie voor [zoeken in volledige tekst](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Cosmos DB en soortgelijke technologieën hebben indexen waarop kan worden gezocht. Bij de evaluatie van producten die zoeken en opslag combineren, is het soms lastig om te bepalen wat u moet kiezen. Veel oplossingen gebruiken beide: DBMS voor opslag en Azure Search voor gespecialiseerde zoekfuncties.<br/><br/>Vergeleken met DBMS slaat Azure Search de inhoud van heterogene bronnen op en biedt het gespecialiseerde tekstverwerkingsfuncties, zoals linguïstische tekstverwerking (woordstammen, lemmata, woordvormen) in [56 talen](https://docs.microsoft.com/rest/api/searchservice/language-support). Het ondersteunt ook automatische correctie van verkeerd gespelde woorden, [synoniemen](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions), [scoringsbesturingselementen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetten](https://docs.microsoft.com/azure/search/search-filters-facets) en [aangepaste tokenisering](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). De [engine voor zoekopdrachten in volledige tekst](search-lucene-query-architecture.md) in Azure Search is gebouwd op Apache Lucene, een industrienorm op het gebied van het ophalen van gegevens. Hoewel Azure Search gegevens bewaart in de vorm van een omgekeerde index, is het zelden een vervanging voor echte gegevensopslag. Zie dit [forumbericht](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data) voor meer informatie. <br/><br/>Een ander belangrijk punt in deze categorie is het gebruik van resources. Indexering en bepaalde querybewerkingen zijn vaak rekenintensief. Door de zoekopdracht van de DBMS naar een toegewezen oplossing in de cloud te verplaatsen, blijven systeemresources beschikbaar voor transactieverwerking. En door de zoekfunctie te externaliseren, kunt u eenvoudig schalen naar het juiste queryvolume.|
|Toegewezen zoekoplossing | Ervan uitgaande dat u hebt gekozen voor een toegewezen zoekoplossing met het volledige spectrum aan functies, dan is er nog een essentieel onderscheid tussen on-premises oplossingen en een cloudservice. Veel zoektechnologieën bieden controle over het indexeren en doorzoeken van pijplijnen, toegang tot rijkere syntaxis voor filteren en het uitvoeren van query’s, beheer van rangen en relevantie, en functies voor zelfgestuurde en intelligente zoekopdrachten. <br/><br/>Als u op zoek bent naar een pasklare oplossing met minimale overhead, minimaal onderhoud en aanpasbare schaling is een cloudservice de juiste keuze voor u. <br/><br/>Binnen het cloudmodel bieden verschillende providers vergelijkbare basisfuncties, met Zoekopdracht in volledige tekst, op geografische locaties zoeken en de mogelijkheid om te gaan met een zekere dubbelzinnigheid van zoekinvoergegevens. Meestal bepaalt een [gespecialiseerde functie](#feature-drilldown), of het gemak en de algehele eenvoud van de API’s, de hulpprogramma’s en het beheer, welke oplossing het meest geschikt voor u is. |

Azure Search is van alle cloudproviders het sterkst op het gebied van zoeken in volledige tekst in inhoudsopslag en databases in Azure, voor apps die hoofdzakelijk afhankelijk zijn van het ophalen van gegevens en van inhoudsnavigatie. 

Belangrijke pluspunten zijn onder andere:

+ Azure-gegevensintegratie (verkenners) in de indexeringslaag
+ Azure Portal voor centraal beheer
+ Schaling, betrouwbaarheid en beschikbaarheid van wereldklasse in Azure
+ AI-verwerking van onbewerkte gegevens om deze meer doorzoekbaar te maken, inclusief tekst uit afbeeldingen of patronen te vinden in ongestructureerde inhoud.
+ Taalkundige en aangepaste analyse met analysefuncties voor grondig zoeken in volledige tekst in 56 talen
+ [Kernfuncties die eigen zijn aan zoekgerichte apps](#feature-drilldown): scoren, facetten, suggesties, synoniemen, op geografische locaties zoeken, en meer.

> [!Note]
> Niet-Azure-gegevensbronnen worden volledig ondersteund, maar zijn gebaseerd op code-intensievere pushmethodes in plaats van op indexeerfuncties. Als u API’s gebruikt, kunt u elke JSON-documentverzameling doorsluizen naar een Azure Search-index.

Onder onze klanten bevinden zich degenen die het breedste scala aan functies in Azure Search gebruiken, onder andere onlinecatalogussen, Line-Of-Business-programma’s en toepassingen voor het ontdekken van documenten.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

Hoewel veel taken in de portal kunnen worden uitgevoerd, is Azure Search bedoelt voor ontwikkelaars die de zoekfunctie willen integreren in bestaande toepassingen. De volgende programmeerinterfaces zijn beschikbaar.

|Platform |Beschrijving |
|-----|------------|
|[REST](/rest/api/searchservice/) | HTTP-opdrachten die worden ondersteund door een willekeurig programmeerplatform en een willekeurige programmeertaal, waaronder Xamarin, Java en JavaScript|
|[.NET-SDK](search-howto-dotnet-sdk.md) | .NET-wrapper voor de REST API biedt efficiënte codering in C# en andere beheerde codetalen die zijn bedoeld voor .NET Framework |

## <a name="free-trial"></a>Gratis versie
Azure-abonnees kunnen [een service inrichten in de gratis laag](search-create-service-portal.md).

Als u geen abonnee bent, kunt u [gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). U krijgt een tegoed om de betaalde Azure-services uit te proberen. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen.

U kunt ook [de voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): bij uw MSDN-abonnement ontvangt u elke maand een tegoed dat u kunt gebruiken voor betaalde Azure-services. 

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

1. Maak een [gratis service](search-create-service-portal.md). Alle snelstartgidsen en zelfstudies kunnen worden gevolgd in de gratis service.

2. Doorloop de [zelfstudie over het gebruik van ingebouwde hulpprogramma's voor indexeren en query's](search-get-started-portal.md). Leer meer over belangrijke concepten en raak vertrouwd met de informatie die de portal biedt.

3. Ga verder met code met behulp van de .NET of REST-API:

   + In [De .NET SDK gebruiken](search-howto-dotnet-sdk.md) wordt de hoofdwerkstroom voor beheerde code gedemonstreerd.  
   + In [Get started with the REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) (Aan de slag met REST API) worden dezelfde stappen getoond met behulp van de REST API. U kunt deze snelstart ook gebruiken om REST-API's van Postman of Fiddler aan te roepen: [REST API's voor Azure Search verkennen ](search-get-started-postman.md).

## <a name="watch-this-video"></a>Deze video bekijken

Zoekmachines zijn de normale stuurprogramma’s voor het ophalen van informatie in mobiele apps, op het web en in zakelijke gegevensopslag. Azure Search biedt hulpprogramma’s voor het creëren van een zoekervaring die vergelijkbaar is met die op grote commerciële websites.

In deze 9 minuten durende video van programmamanager Liam Cavanagh leert u hoe het integreren van een zoekmachine uw app kan verbeteren. Korte demonstraties hebben betrekking op belangrijke functies in Azure Search en hoe een doorsnee werkstroom er uitziet. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minuten, gaat over de belangrijkste functies en gebruiksvoorbeelden.
+ 3-4 minuten, gaat over het inrichten van de service. 
+ 4-6 minuten, gaat over de wizard Gegevens importeren die wordt gebruikt om een index te maken met behulp van de ingebouwde vastgoedgegevensset.
+ 6-9 minuten, gaat over Search Explorer en verschillende query’s.
