---
title: Inleiding tot Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search is een volledig beheerde gehoste cloudzoekservice van Microsoft. Lees functiebeschrijvingen, de ontwikkelingsworkflow, vergelijkingen met andere Microsoft-zoekproducten en hoe u aan de slag.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/06/2020
ms.openlocfilehash: fee7c8eb73fe0bb7c9fd0bd9de9aa57bd8c40215
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77580648"
---
# <a name="what-is-azure-cognitive-search"></a>Wat is Azure Cognitive Search?

Azure Cognitive Search[(voorheen bekend als "Azure Search"](whats-new.md#new-service-name)) is een search-as-a-service cloudoplossing die ontwikkelaars API's en hulpprogramma's biedt voor het toevoegen van een rijke zoekervaring over privé, heterogene inhoud in web-, mobiele en bedrijfstoepassingen. Uw code of een tool roept gegevensopname (indexering) op om een index te maken en te laden. Optioneel u cognitieve vaardigheden toevoegen om AI-processen toe te passen tijdens het indexeren. Hierdoor kunnen nieuwe informatie en structuren toevoegen die nuttig zijn voor zoekopdrachten en andere scenario's.

Aan de andere kant van uw service geeft uw toepassingscode queryaanvragen op en verwerkt u antwoorden. De zoekervaring wordt in uw client gedefinieerd met behulp van functionaliteit van Azure Cognitive Search, met query-uitvoering over een uitgebreide index die u in uw service maakt, bezit en opslaat.

![Azure Cognitive Search-architectuur](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search-architectuur")

Functionaliteit wordt beschikbaar gemaakt via een eenvoudige [REST API](/rest/api/searchservice/) of [.NET SDK](search-howto-dotnet-sdk.md) waarmee de inherente complexiteit van het ophalen van gegevens wordt gemaskeerd. Naast API’s biedt Azure Portal ondersteuning voor administratie- en inhoudsbeheer met hulpprogramma’s voor het ontwikkelen van prototypen en het doorzoeken van indexen. Omdat de service wordt uitgevoerd in de cloud, worden de infrastructuur en beschikbaarheid beheerd met Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Wanneer Azure Cognitive Search gebruiken

Azure Cognitive Search is zeer geschikt voor de volgende toepassingsscenario's:

+ Consolidatie van heterogene inhoudstypen in een privé, enkele, doorzoekbare index. Query's zijn altijd boven een index die u maakt en laadt met documenten, en de index bevindt zich altijd in de cloud op uw Azure Cognitive Search-service. U een index vullen met streams van JSON-documenten van elke bron of platform. Als alternatief u voor inhoud die is afkomstig uit Azure, een *indexeerder* gebruiken om gegevens in een index te verwijderen. Indexdefinitie en -beheer/eigendom is een belangrijke reden voor het gebruik van Azure Cognitive Search.

+ Ruwe inhoud is grote ongedifferentieerde tekst-, afbeeldingsbestanden of toepassingsbestanden, zoals Office-inhoudstypen op een Azure-gegevensbron zoals Azure Blob-opslag of Cosmos DB. U cognitieve vaardigheden toepassen tijdens het indexeren om structuur toe te voegen of betekenis uit afbeeldings- en toepassingsbestanden te extraheren.

+ Eenvoudige implementatie van zoekgerelateerde functies. Azure Cognitive Search API's vereenvoudigen queryconstructie, gefacetteerde navigatie, filters (inclusief geo-ruimtelijk zoeken), synoniemtoewijzing, typeahead-query's en relevantieafstemming. Met behulp van ingebouwde functies, u voldoen aan de verwachtingen van de eindgebruiker voor een zoekervaring vergelijkbaar met commerciële web zoekmachines.

+ Ongestructureerde tekst indexeren of tekst en informatie uit afbeeldingsbestanden extraheren. De [AI-verrijkingsfunctie](cognitive-search-concept-intro.md) van Azure Cognitive Search voegt AI-verwerking toe aan een indexeringspijplijn. Enkele veelvoorkomende use-cases zijn OCR over gescand document, entiteitsherkenning en sleutelzinextractie over grote documenten, taaldetectie en tekstvertaling en sentimentanalyse.

+ Taalvereisten waaraan is voldaan met behulp van de aangepaste en taalanalysers van Azure Cognitive Search. Als u niet-Engelse inhoud hebt, ondersteunt Azure Cognitive Search zowel Lucene-analyzers als de natuurlijke taalprocessors van Microsoft. U ook analysers configureren om gespecialiseerde verwerking van ruwe inhoud te bereiken, zoals het filteren van diacritics.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Functiebeschrijvingen

| Kernzoekopdracht&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Functies |
|-------------------|----------|
|Zoeken met vrije tekst | [**Zoeken op volledige tekst**](search-lucene-query-architecture.md) is een primaire use case voor de meeste op zoekopdrachten gebaseerde apps. Query’s kunnen worden geformuleerd met behulp van een ondersteunde syntaxis. <br/><br/>[**Eenvoudige querysyntaxis**](query-simple-syntax.md) biedt logische operators, zoekoperators voor woordgroepen, operators voor achtervoegsels, operators voor bewerkingsvolgorde.<br/><br/>[**Lucene-querysyntaxis**](query-lucene-syntax.md) omvat alle bewerkingen in eenvoudige syntaxis, met uitbreidingen voor zoeken bij benadering, zoeken op nabijheid, termenverbetering en reguliere expressies.|
| Relevantie | [**Eenvoudig scoren**](index-add-scoring-profiles.md) is een belangrijk voordeel van Azure Cognitive Search. Scoreprofielen worden gebruikt om relevantie te modelleren als een functie van waarden in de documenten zelf. Zo wilt u bijvoorbeeld dat nieuwere producten of afgeprijsde producten hoger worden weergegeven in de zoekresultaten. U kunt scoreprofielen ook bouwen met behulp van labels voor persoonlijke scores op basis van de zoekvoorkeuren van klanten die u hebt bijgehouden en apart opgeslagen. |
| Op geografische locaties zoeken | Azure Cognitive Search verwerkt, filtert en geeft geografische locaties weer. Zo worden gebruikers in staat gesteld om gegevens te verkennen op basis van de afstand van een zoekresultaat tot een fysieke locatie. [Bekijk deze video ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) of [dit voorbeeld](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) voor meer informatie. |
| Filters en facetten | [**Facetnavigatie**](search-faceted-navigation.md) is ingeschakeld via een enkele queryparameter. Azure Cognitive Search retourneert een gefacetteerde navigatiestructuur die u gebruiken als de code achter een lijst met categorieën, voor zelfsturende filtering (bijvoorbeeld om catalogusitems te filteren op prijsbereik of merk). <br/><br/> [**Filters**](query-odata-filter-orderby-syntax.md) kunnen worden gebruikt om facetnavigatie op te nemen in de gebruikersinterface van uw toepassing, het formuleren van query’s te verbeteren, en te filteren op basis van criteria die zijn opgegeven door gebruikers of ontwikkelaars. Maak filters met behulp van de OData-syntaxis. |
| Functies voor de gebruikerservaring | [**Automatisch aanvullen**](search-autocomplete-tutorial.md) kan worden ingeschakeld voor vervolgquery's in een zoekbalk. <br/><br/>[**Zoeksuggesties**](https://docs.microsoft.com/rest/api/searchservice/suggesters) werkt ook vanuit gedeeltelijke ingevoerde tekst in een zoekbalk, maar de resultaten zijn feitelijke documenten in uw index, geen zoektermen. <br/><br/>[**Synoniemen**](search-synonyms.md) koppelt gelijkwaardige termen die het bereik van een query impliciet uitbreiden, zonder dat de gebruiker de andere termen hoeft op te geven. <br/><br/>Met [**Treffers markeren**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wordt tekstopmaak toegepast op een overeenkomend trefwoord in zoekresultaten. U kunt kiezen welke velden gemarkeerde fragmenten retourneren.<br/><br/>[**Sorteren**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wordt aangeboden voor meerdere velden via het indexschema en vervolgens tijdens het uitvoeren van een query in-/uitgeschakeld met een enkele zoekparameter.<br/><br/> [**Het paging**](search-pagination-page-layout.md) en beperken van uw zoekresultaten is eenvoudig met de fijn afgestemde controle die Azure Cognitive Search biedt over uw zoekresultaten.  <br/><br/>|

| AI-verrijking&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Functies |
|-------------------|----------|
|AI-verwerking tijdens indexering | [**AI-verrijking**](cognitive-search-concept-intro.md) voor beeld- en tekstanalyse kan worden toegepast op een indexeringspijplijn om tekstinformatie uit ruwe inhoud te extraheren. Enkele voorbeelden van [ingebouwde vaardigheden](cognitive-search-predefined-skills.md) zijn: optische tekenherkenning (waardoor gescande JPEG-bestanden doorzoekbaar worden), herkenning van entiteiten (waarmee een organisatie, naam of locatie kan worden geïdentificeerd), en herkenning van sleuteltermen. U kunt ook [aangepaste vaardigheden coderen](cognitive-search-create-custom-skill-example.md) om ze te koppelen aan een pijplijn. |
| Verrijkte inhoud opslaan voor analyse en consumptie in niet-zoekscenario's | [**Knowledge store (preview)**](knowledge-store-concept-intro.md) is een uitbreiding van AI-gebaseerde indexering. Met Azure-opslag als back-end u verrijkingen opslaan die tijdens het indexeren zijn gemaakt. Deze artefacten kunnen worden gebruikt om u te helpen betere skillsets te ontwerpen of vorm en structuur te maken uit amorfe of dubbelzinnige gegevens. U projecties maken van deze structuren die zich richten op specifieke workloads of gebruikers. U de geëxtraheerde gegevens ook rechtstreeks analyseren of in andere apps laden.<br/><br/> |
| Inhoud in cache | [**Incrementele verrijking (voorbeeld)**](cognitive-search-incremental-indexing-conceptual.md) beperkt de verwerking tot alleen de documenten die door specifieke bewerking worden gewijzigd in de pijplijn, met behulp van inhoud in de cache voor de delen van de pijplijn die niet worden gewijzigd. |

| Gegevens&nbsp;importeren/indexeren | Functies |
|----------------------------------|----------|
| Gegevensbronnen | Azure Cognitive Search-indexen accepteren gegevens uit elke bron, op voorwaarde dat deze worden ingediend als json-gegevensstructuur. <br/><br/> [**Indexers**](search-indexer-overview.md) automatiseren gegevensopname voor ondersteunde Azure-gegevensbronnen en verwerken JSON-serialisatie. Maak verbinding met [Azure SQL Database,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) [Azure Cosmos DB](search-howto-index-cosmosdb.md)of Azure [Blob-opslag](search-howto-indexing-azure-blob-storage.md) om doorzoekbare inhoud in primaire gegevensopslag te extraheren. Azure Blob-indexeerfuncties kunnen *documenten kraken* om [tekst te extraheren uit grote bestandsindelingen](search-howto-indexing-azure-blob-storage.md), zoals Microsoft Office, PDF en HTML-bestanden. |
| Hiërarchische en geneste gegevensstructuren | [**Met complexe typen**](search-howto-complex-data-types.md) en verzamelingen u vrijwel elk type JSON-structuur modelleren als een Azure Cognitive Search-index. Een-op-veel en veel-op-veel kardinaliteit kan native worden uitgedrukt door middel van collecties, complexe types en collecties van complexe types.|
| Taalkundige analyse | Analysefuncties zijn onderdelen die worden gebruikt om tekst te verwerken tijdens het indexeren en zoeken. Er zijn twee typen. <br/><br/>[**Aangepaste lexicale analysefuncties**](index-add-custom-analyzers.md) worden gebruikt voor complexe zoekquery’s met behulp van fonetische overeenkomsten en reguliere expressies. <br/><br/>[**Taalanalysefuncties**](index-add-language-analyzers.md) van Lucene of Microsoft worden gebruikt voor het intelligent verwerken van taalspecifieke taalkundige aspecten, zoals werkwoordtijden, geslacht, afwijkende meervoudsvormen voor zelfstandige naamwoorden, het opsplitsen van woorden, het afbreken van woorden (voor talen zonder spaties), en meer. <br/><br/>|


| Platformniveau&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Functies |
|-------------------|----------|
| Hulpprogramma's voor het ontwikkelen van prototypen en voor controle | In de portal kunt u de [**wizard Gegevens importeren**](search-import-data-portal.md) gebruiken om indexeerfuncties te configureren, Index Designer om een index te bouwen, en [**Search Explorer**](search-explorer.md) om query’s te testen en scoreprofielen te verfijnen. U kunt ook elke gewenste index openen om het bijbehorende schema te bekijken. |
| Controle en diagnose | [**Schakel bewakingsfuncties in**](search-monitor-usage.md) om verder te gaan dan de statistieken in één oogopslag die altijd zichtbaar zijn in de portal. Metrische gegevens over query’s per seconde, latentie en beperkingen worden vastgelegd en gerapporteerd op portalpagina’s zonder dat hiervoor extra configuratie is vereist.|
| Versleuteling aan de serverzijde | [**Microsoft-managed encryption-at-rest**](search-security-overview.md#encrypted-transmission-and-storage) is ingebouwd in de interne opslaglaag en is onherroepelijk. Optioneel u de standaardversleuteling aanvullen met [**door de klant beheerde versleutelingssleutels.**](search-security-manage-encryption-keys.md) Sleutels die u maakt en beheert in Azure Key Vault, worden gebruikt om indexen en synoniemenkaarten in Azure Cognitive Search te versleutelen. |
| Infrastructuur | Het **maximaal beschikbare platform** zorgt ervoor dat de zoekservice uiterst betrouwbaar is. Wanneer goed geschaald, [Azure Cognitive Search biedt een 99,9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Azure** Cognitive Search is volledig beheerd en schaalbaar als end-to-end-oplossing en vereist absoluut geen infrastructuurbeheer. De service kan worden aangepast aan uw persoonlijke behoeften door in twee dimensies te schalen voor het verwerken van meer documentopslag, een hogere querybelasting, of beide.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Azure Cognitive Search gebruiken
### <a name="step-1-provision-service"></a>Stap 1: Service inrichten
U een Azure Cognitive Search-service inrichten in de [Azure-portal](https://portal.azure.com/) of via de [Azure Resource Management API.](/rest/api/searchmanagement/) U kunt kiezen voor de gratis service die is gedeeld met andere abonnees, of u kunt een [betaalde laag](https://azure.microsoft.com/pricing/details/search/) kiezen met toegewezen resources die alleen voor uw service worden gebruikt. Voor betaalde lagen kunt u een service in twee dimensies schalen: 

- Voeg replica’s toe om de capaciteit voor het verwerken van zware querybelastingen te vergroten.   
- Voeg partities toe om meer documenten op te kunnen slaan. 

Door de opslag van documenten en de doorvoer van query’s afzonderlijk aan te pakken kunt u het gebruik van resources afstemmen op basis van productievereisten.

### <a name="step-2-create-index"></a>Stap 2: Index maken
Voordat u doorzoekbare inhoud uploaden, moet u eerst een Azure Cognitive Search-index definiëren. Een index is vergelijkbaar met een databasetabel waarin uw gegevens zijn opgeslagen, en u kunt zoekquery’s uitvoeren voor een index. U definieert het indexschema dat moet worden toegewezen, om de structuur van de documenten te weerspiegelen waarin u wilt zoeken, vergelijkbaar met de velden in een database.

U kunt een schema maken in Azure Portal of via een programma met behulp van de [.NET SDK](search-howto-dotnet-sdk.md) of [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Stap 3: Gegevens laden
Nadat u een index hebt gedefinieerd, bent u klaar om inhoud te uploaden. U kunt hiervoor een push- of een pull-model gebruiken.

Met het pull-model worden gegevens opgehaald uit externe gegevensbronnen. Het model wordt ondersteund met *indexeerfuncties* die aspecten van de gegevensopname stroomlijnen en automatiseren, zoals het verbinden met, en lezen en serialiseren van gegevens. Er zijn [indexeerfuncties](/rest/api/searchservice/Indexer-operations) beschikbaar voor Azure Cosmos DB, Azure SQL Database, Azure Blob Storage en SQL Server gehost op een Azure-VM. U kunt een indexeerfunctie configureren op-aanvraag of als geplande gegevensvernieuwing.

Het push-model wordt geboden via de SDK of REST API’s, en gebruikt om bijgewerkte documenten naar een index te verzenden. U kunt gegevens uit nagenoeg elke gegevensset pushen met behulp van de JSON-indeling. Zie [Add, update, or delete Documents](/rest/api/searchservice/addupdate-or-delete-documents) (Documenten toevoegen, bijwerken of verwijderen) of [How to use the .NET SDK](search-howto-dotnet-sdk.md) (De .NET SDK gebruiken) voor informatie over het laden van gegevens.

### <a name="step-4-search"></a>Stap 4: Zoeken
Nadat u een index hebt opgetuerd, u [zoekopdrachten aan](search-query-overview.md) het eindpunt van uw service uitgeven met behulp van eenvoudige HTTP-aanvragen met [REST API](/rest/api/searchservice/Search-Documents) of de [.NET SDK.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations)

Stap door [Uw eerste zoek-app](tutorial-csharp-create-first-app.md) maken om een webpagina te maken en vervolgens uit te breiden die gebruikersinvoer verzamelt en resultaten verwerkt. U [Postman](search-get-started-postman.md) ook gebruiken voor interactieve REST-oproepen of de ingebouwde [Zoekverkenner](search-explorer.md) in Azure-portal om een bestaande index op te vragen.

## <a name="how-it-compares"></a>Vergelijking

Klanten vragen vaak hoe Azure Cognitive Search zich verhoudt tot andere zoekgerelateerde oplossingen. In de volgende tabel worden de verschillen samengevat.

| Vergeleken met | Belangrijke verschillen |
|-------------|-----------------|
|Bing | Met [Bing Webzoekopdrachten-API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) wordt in de indexen op Bing.com gezocht naar overeenkomsten voor termen die u hebt ingediend. Indexen zijn gebouwd uit HTML, XML en andere webinhoud op openbare sites. [Bing Aangepaste zoekopdrachten](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/), dat op dezelfde basis is gebouwd, biedt dezelfde verkenningstechnologie voor typen webinhoud, met een bereik dat is ingesteld op afzonderlijke websites.<br/><br/>Azure Cognitive Search zoekt in een index die u definieert, gevuld met gegevens en documenten die u bezit, vaak uit verschillende bronnen. Azure Cognitive Search heeft crawlermogelijkheden voor sommige gegevensbronnen via [indexeerders,](search-indexer-overview.md)maar u elk JSON-document dat voldoet aan uw indexschema in één geconsolideerde doorzoekbare bron pushen. |
|Zoeken in database | Veel databaseplatforms beschikken over een ingebouwde zoekfunctie. SQL Server heeft een functie voor [zoeken in volledige tekst](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Cosmos DB en soortgelijke technologieën hebben indexen waarop kan worden gezocht. Bij de evaluatie van producten die zoeken en opslag combineren, is het soms lastig om te bepalen wat u moet kiezen. Veel oplossingen gebruiken beide: DBMS voor opslag en Azure Cognitive Search voor gespecialiseerde zoekfuncties.<br/><br/>In vergelijking met DBMS search slaat Azure Cognitive Search inhoud op uit heterogene bronnen en biedt gespecialiseerde tekstverwerkingsfuncties, zoals taalbewuste tekstverwerking (stemming, lemmatisatie, woordformulieren) in [56 talen.](https://docs.microsoft.com/rest/api/searchservice/language-support) Het ondersteunt ook automatische correctie van verkeerd gespelde woorden, [synoniemen](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions), [scoringsbesturingselementen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetten](https://docs.microsoft.com/azure/search/search-filters-facets) en [aangepaste tokenisering](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). De [full text zoekmachine](search-lucene-query-architecture.md) in Azure Cognitive Search is gebouwd op Apache Lucene, een industriestandaard in het ophalen van informatie. Hoewel Azure Cognitive Search gegevens in de vorm van een omgekeerde index blijft gebruiken, is het zelden een vervanging voor echte gegevensopslag. Zie dit [forumbericht](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data) voor meer informatie. <br/><br/>Een ander belangrijk punt in deze categorie is het gebruik van resources. Indexering en bepaalde querybewerkingen zijn vaak rekenintensief. Door de zoekopdracht van de DBMS naar een toegewezen oplossing in de cloud te verplaatsen, blijven systeemresources beschikbaar voor transactieverwerking. En door de zoekfunctie te externaliseren, kunt u eenvoudig schalen naar het juiste queryvolume.|
|Toegewezen zoekoplossing | Ervan uitgaande dat u hebt gekozen voor een toegewezen zoekoplossing met het volledige spectrum aan functies, dan is er nog een essentieel onderscheid tussen on-premises oplossingen en een cloudservice. Veel zoektechnologieën bieden controle over het indexeren en doorzoeken van pijplijnen, toegang tot rijkere syntaxis voor filteren en het uitvoeren van query’s, beheer van rangen en relevantie, en functies voor zelfgestuurde en intelligente zoekopdrachten. <br/><br/>Als u op zoek bent naar een pasklare oplossing met minimale overhead, minimaal onderhoud en aanpasbare schaling is een cloudservice de juiste keuze voor u. <br/><br/>Binnen het cloudmodel bieden verschillende providers vergelijkbare basisfuncties, met Zoekopdracht in volledige tekst, op geografische locaties zoeken en de mogelijkheid om te gaan met een zekere dubbelzinnigheid van zoekinvoergegevens. Meestal bepaalt een [gespecialiseerde functie](#feature-drilldown), of het gemak en de algehele eenvoud van de API’s, de hulpprogramma’s en het beheer, welke oplossing het meest geschikt voor u is. |

Onder cloudproviders is Azure Cognitive Search het sterkst voor full text search workloads via content stores en databases op Azure, voor apps die voornamelijk afhankelijk zijn van zoeken naar zowel informatie ophalen als content navigatie. 

Belangrijke pluspunten zijn onder andere:

+ Azure-gegevensintegratie (verkenners) in de indexeringslaag
+ Azure Portal voor centraal beheer
+ Schaling, betrouwbaarheid en beschikbaarheid van wereldklasse in Azure
+ AI-verwerking van ruwe gegevens om deze beter doorzoekbaar te maken, inclusief tekst uit afbeeldingen, of patronen in ongestructureerde inhoud te vinden.
+ Taalkundige en aangepaste analyse met analysefuncties voor grondig zoeken in volledige tekst in 56 talen
+ [Kernfuncties die eigen zijn aan zoekgerichte apps](#feature-drilldown): scoren, facetten, suggesties, synoniemen, op geografische locaties zoeken, en meer.

> [!Note]
> Niet-Azure-gegevensbronnen worden volledig ondersteund, maar zijn gebaseerd op code-intensievere pushmethodes in plaats van op indexeerfuncties. Met API's u elke JSON-documentverzameling doorgeven naar een Azure Cognitive Search-index.

Onder onze klanten zijn deze in staat om gebruik te maken van het breedste scala aan functies in Azure Cognitive Search online catalogi, bedrijfsprogramma's en documentdetectietoepassingen.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

Hoewel veel taken in de portal kunnen worden uitgevoerd, is Azure Cognitive Search bedoeld voor ontwikkelaars die zoekfunctionaliteit willen integreren in bestaande toepassingen. De volgende programmeerinterfaces zijn beschikbaar.

|Platform |Beschrijving |
|-----|------------|
|[Rest](/rest/api/searchservice/) | HTTP-opdrachten die worden ondersteund door een willekeurig programmeerplatform en een willekeurige programmeertaal, waaronder Xamarin, Java en JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-wrapper voor de REST API biedt efficiënte codering in C# en andere beheerde codetalen die zijn bedoeld voor .NET Framework |

## <a name="free-trial"></a>Gratis proefversie
Azure-abonnees kunnen [een service inrichten in de gratis laag](search-create-service-portal.md).

Als u geen abonnee bent, kunt u [gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). U krijgt een tegoed om de betaalde Azure-services uit te proberen. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er wordt nooit geld van uw creditcard afgeschreven, tenzij u uw instellingen zelf wijzigt en expliciet aangeeft dat u wilt betalen.

U kunt ook [de voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): bij uw MSDN-abonnement ontvangt u elke maand een tegoed dat u kunt gebruiken voor betaalde Azure-services. 

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

1. Maak een [gratis service](search-create-service-portal.md). Alle snelstartgidsen en zelfstudies kunnen worden gevolgd in de gratis service.

2. Doorloop de [zelfstudie over het gebruik van ingebouwde hulpprogramma's voor indexeren en query's](search-get-started-portal.md). Leer meer over belangrijke concepten en raak vertrouwd met de informatie die de portal biedt.

3. Ga verder met code met behulp van de .NET of REST-API:

   + In [De .NET SDK gebruiken](search-howto-dotnet-sdk.md) wordt de hoofdwerkstroom voor beheerde code gedemonstreerd.  
   + In [Get started with the REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) (Aan de slag met REST API) worden dezelfde stappen getoond met behulp van de REST API. U deze quickstart ook gebruiken om REST-API's van Postman of Fiddler te bellen: [Azure Cognitive Search REST API's verkennen.](search-get-started-postman.md)

## <a name="watch-this-video"></a>Deze video bekijken

Zoekmachines zijn de normale stuurprogramma’s voor het ophalen van informatie in mobiele apps, op het web en in zakelijke gegevensopslag. Azure Cognitive Search biedt u tools voor het maken van een zoekervaring die vergelijkbaar is met die op grote commerciële websites.

In deze 9 minuten durende video van programmamanager Liam Cavanagh leert u hoe het integreren van een zoekmachine uw app kan verbeteren. Korte demo's hebben betrekking op de belangrijkste functies in Azure Cognitive Search en hoe een typische werkstroom eruit ziet. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minuten, gaat over de belangrijkste functies en gebruiksvoorbeelden.
+ 3-4 minuten, gaat over het inrichten van de service. 
+ 4-6 minuten, gaat over de wizard Gegevens importeren die wordt gebruikt om een index te maken met behulp van de ingebouwde vastgoedgegevensset.
+ 6-9 minuten, gaat over Search Explorer en verschillende query’s.
