---
title: Inleiding tot Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search is een volledig beheerde cloudzoekservice van Microsoft. Lees de beschrijvingen van functies, een ontwikkelingswerkstroom en een vergelijking van Azure Search met andere Microsoft-zoekproducten en lees hoe u aan de slag gaat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/30/2020
ms.openlocfilehash: 93ca06b10a57d54d78ba55fbc9e5a157a1bada91
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932828"
---
# <a name="what-is-azure-cognitive-search"></a>Wat is Azure Cognitive Search?

Azure Cognitive Search ([voorheen 'Azure Search'](whats-new.md#new-service-name)) is een SaaS-cloudoplossing (Search-as-a-Service) die ontwikkelaars API’s en hulpprogramma’s biedt waarmee ze een uitgebreide zoekervaring binnen privé- en heterogene inhoud kunnen toevoegen aan web-, mobiele en bedrijfstoepassingen. 

In een aangepaste oplossing bevindt een zoekservice zich tussen twee primaire workloads: inhoudsopname en query's. Uw code of een hulpprogramma definieert een schema en activeert gegevensopname (indexering) om een index in Azure Cognitive Search te laden. Als u wilt, kunt u ook cognitieve vaardigheden toevoegen om AI toe te passen tijdens de indexering. Zo kunt u nieuwe informatie en structuren maken die handig zijn voor zoekacties en kennisanalyse.

Als een index eenmaal bestaat, verzendt uw toepassingscode query's naar een zoekservice en verwerkt de code de antwoorden. De zoekervaring wordt gedefinieerd in uw client met functionaliteit van Azure Cognitive Search, waarbij query's worden uitgevoerd via een persistente index die u maakt, waarvan u eigenaar bent en die u opslaat in uw service.

![Architectuur van Azure Cognitive Search](media/search-what-is-azure-search/azure-search-diagram.svg "Architectuur van Azure Cognitive Search")

Functionaliteit wordt beschikbaar gemaakt via een eenvoudige [REST API](/rest/api/searchservice/) of [.NET SDK](search-howto-dotnet-sdk.md) waarmee de inherente complexiteit van het ophalen van gegevens wordt gemaskeerd. Naast API’s biedt Azure Portal ondersteuning voor administratie- en inhoudsbeheer met hulpprogramma’s voor het ontwikkelen van prototypen en het doorzoeken van indexen. Omdat de service wordt uitgevoerd in de cloud, worden de infrastructuur en beschikbaarheid beheerd met Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Wanneer gebruikt u Azure Cognitive Search?

Azure Cognitive Search is geschikt voor de volgende toepassingsscenario's:

+ Consolidatie van heterogene inhoudstypen in een privé-, enkelvoudige en doorzoekbare index. Query's worden altijd uitgevoerd via een index die u maakt en laadt met documenten en de index bevindt zich altijd in de cloud op uw Azure Cognitive Search-service. U kunt een index vullen met streams van JSON-documenten vanaf elke bron of platform. U kunt ook voor inhoud vanuit Azure een *indexeerfunctie* gebruiken om gegevens op te halen in een index. Indexdefinitie en beheer/eigendom zijn de belangrijkste redenen om Azure Cognitive Search te gebruiken.

+ Onbewerkte inhoud is veel niet-gedifferentieerde tekst, afbeeldingsbestanden of toepassingsbestanden, zoals Office-inhoudstypen in een Azure-gegevensbron, zoals Azure-blobopslag of Cosmos DB. U kunt cognitieve vaardigheden toepassen tijdens de indexering om structuur toe te voegen of doorzoekbare tekst te halen uit afbeeldings- en toepassingsbestanden.

+ Eenvoudige implementatie van zoekfuncties. Azure Cognitive Search API's vereenvoudigen het bouwen van query's, facetnavigatie, filters (inclusief georuimtelijke zoekacties), toewijzing van synoniemen, automatisch aanvullende query's en afstemming van relevantie. Met ingebouwde functies kunt u aan de verwachtingen van eindgebruikers voldoen en een zoekervaring bieden die vergelijkbaar is met commerciële webzoekprogramma's.

+ Indexering van ongestructureerde tekst of extraheren van tekst en informatie uit afbeeldingsbestanden. De functie [AI-verrijking](cognitive-search-concept-intro.md) van Azure Cognitive Search voegt AI-verwerking toe aan een pijplijn voor indexeren. Enkele veelvoorkomende toepassingen zijn onder meer toepassen van OCR op gescande documenten, entiteitsherkenning en extractie van sleutelwoorden in grote documenten, taalherkenning, tekstvertaling en gevoelsanalyses.

+ Er wordt voldaan aan taalkundige vereisten met de aangepaste en taalanalyses van Azure Cognitive Search. Als u niet-Engelstalige inhoud hebt, ondersteunt Azure Cognitive Search zowel Lucene-analyses als Microsofts natuurlijketaalprocessoren. U kunt ook analyses configureren om gespecialiseerde verwerking van onbewerkte inhoud uit te voeren, zoals het uitfilteren van diakritische tekens.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Beschrijvingen van functies

| Belangrijkste&nbsp;zoekacties&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Functies |
|-------------------|----------|
|Zoeken met vrije tekst | [**Zoeken in volledige tekst**](search-lucene-query-architecture.md) is een primair gebruiksvoorbeeld voor de meeste op zoekopdrachten gebaseerde apps. Query’s kunnen worden geformuleerd met behulp van een ondersteunde syntaxis. <br/><br/>[**Eenvoudige querysyntaxis**](query-simple-syntax.md) biedt logische operators, zoekoperators voor woordgroepen, operators voor achtervoegsels, operators voor bewerkingsvolgorde.<br/><br/>[**Lucene-querysyntaxis**](query-lucene-syntax.md) omvat alle bewerkingen in eenvoudige syntaxis, met uitbreidingen voor zoeken bij benadering, zoeken op nabijheid, termenverbetering en reguliere expressies.|
| Relevantie | [**Eenvoudig scoren**](index-add-scoring-profiles.md) is een belangrijk voordeel van Azure Cognitive Search. Scoreprofielen worden gebruikt om relevantie te modelleren als een functie van waarden in de documenten zelf. Zo wilt u bijvoorbeeld dat nieuwere producten of afgeprijsde producten hoger worden weergegeven in de zoekresultaten. U kunt scoreprofielen ook bouwen met behulp van labels voor persoonlijke scores op basis van de zoekvoorkeuren van klanten die u hebt bijgehouden en apart opgeslagen. |
| Op geografische locaties zoeken | Met Azure Cognitive Search worden geografische locaties verwerkt, gefilterd en weergegeven. Zo worden gebruikers in staat gesteld om gegevens te verkennen op basis van de afstand van een zoekresultaat tot een fysieke locatie. [Bekijk deze video ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) of [dit voorbeeld](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) voor meer informatie. |
| Filters en facetten | [**Facetnavigatie**](search-faceted-navigation.md) is ingeschakeld via een enkele queryparameter. Met Azure Cognitive Search wordt een facetnavigatiestructuur geretourneerd die u kunt gebruiken als de code achter een lijst met categorieën, voor zelfgestuurd filteren (bijvoorbeeld om catalogusitems te filteren op prijsbereik of merk). <br/><br/> [**Filters**](query-odata-filter-orderby-syntax.md) kunnen worden gebruikt om facetnavigatie op te nemen in de gebruikersinterface van uw toepassing, het formuleren van query’s te verbeteren, en te filteren op basis van criteria die zijn opgegeven door gebruikers of ontwikkelaars. Maak filters met behulp van de OData-syntaxis. |
| Functies voor de gebruikerservaring | [**Automatisch aanvullen**](search-autocomplete-tutorial.md) kan worden ingeschakeld voor automatisch aangevulde query’s in een zoekbalk. <br/><br/>[**Zoeksuggesties**](/rest/api/searchservice/suggesters) werkt ook vanuit gedeeltelijke ingevoerde tekst in een zoekbalk, maar de resultaten zijn feitelijke documenten in uw index, geen zoektermen. <br/><br/>[**Synoniemen**](search-synonyms.md) koppelt gelijkwaardige termen die het bereik van een query impliciet uitbreiden, zonder dat de gebruiker de andere termen hoeft op te geven. <br/><br/>Met [**Treffers markeren**](/rest/api/searchservice/Search-Documents) wordt tekstopmaak toegepast op een overeenkomend trefwoord in zoekresultaten. U kunt kiezen welke velden gemarkeerde fragmenten retourneren.<br/><br/>[**Sorteren**](/rest/api/searchservice/Search-Documents) wordt aangeboden voor meerdere velden via het indexschema en vervolgens tijdens het uitvoeren van een query in-/uitgeschakeld met een enkele zoekparameter.<br/><br/> [**Pagineren**](search-pagination-page-layout.md) en beperken van de zoekresultaten is eenvoudig dankzij het goed afgestemde besturingselement dat Azure Cognitive Search biedt voor uw zoekresultaten.  <br/><br/>|

| AI&nbsp;-verrijking&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Functies |
|-------------------|----------|
|AI-verwerking tijdens indexeren | [**AI-verrijking**](cognitive-search-concept-intro.md) voor analyse van afbeeldingen en tekst kan worden toegepast op een pijplijn voor indexeren om tekstinformatie te extraheren uit onbewerkte inhoud. Enkele voorbeelden van [ingebouwde vaardigheden](cognitive-search-predefined-skills.md) zijn: optische tekenherkenning (waardoor gescande JPEG-bestanden doorzoekbaar worden), herkenning van entiteiten (waarmee een organisatie, naam of locatie kan worden geïdentificeerd), en herkenning van sleuteltermen. U kunt ook [aangepaste vaardigheden coderen](cognitive-search-create-custom-skill-example.md) om ze te koppelen aan een pijplijn. U kunt ook [door Azure Machine Learning opgestelde vaardigheden](./cognitive-search-tutorial-aml-custom-skill.md) integreren. |
| Verrijkte inhoud opslaan voor analyse en verwerking in niet-zoekgerelateerde scenario's | [**Kennisarchief**](knowledge-store-concept-intro.md) is een uitbreiding van op AI gebaseerde indexering. Met Azure Storage als back-end kunt u verrijkingen opslaan die zijn gemaakt tijdens het indexeren. Deze artefacten kunnen worden gebruikt om u betere vaardighedensets te maken of vorm en structuur te vinden in ongevormde of onduidelijke gegevens. U kunt projecties maken van deze structuren die zijn gericht op specifieke werkbelastingen of gebruikers. U kunt ook de geëxtraheerde gegevens direct analyseren of laden in andere apps.<br/><br/> |
| In cache opgeslagen inhoud | [**Incrementele verrijking (preview)** ](cognitive-search-incremental-indexing-conceptual.md) beperkt de verwerking tot alleen die documenten die zijn gewijzigd door een specifieke bewerking van de pijplijn en gebruikt in cache opgeslagen inhoud voor de onderdelen van de pijplijn die niet worden gewijzigd. |

| Gegevens&nbsp; importeren/indexeren | Functies |
|----------------------------------|----------|
| Gegevensbronnen | Azure Cognitive Search-indexen accepteren gegevens uit elke willekeurige bron, mits ze zijn verzonden in een JSON-gegevensstructuur. <br/><br/> [**Indexeerfuncties**](search-indexer-overview.md) automatiseren gegevensopname voor ondersteunde Azure-gegevensbronnen en handelen JSON-serialisatie af. Maak verbinding met [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) of [Azure-blobopslag](search-howto-indexing-azure-blob-storage.md) om doorzoekbare inhoud te extraheren in primaire gegevensarchieven. Azure Blob-indexeerfuncties kunnen *documenten kraken* om [tekst te extraheren uit grote bestandsindelingen](search-howto-indexing-azure-blob-storage.md), zoals Microsoft Office, PDF en HTML-bestanden. |
| Hiërarchische en geneste gegevensstructuren | Met [**complexe typen**](search-howto-complex-data-types.md) en verzamelingen kunt u praktisch elk type JSON-structuur modelleren als een Azure Cognitive Search-index. Een-op-veel- en veel-op-veel-kardinaliteit kan systeemeigen worden uitgedrukt in verzamelingen, complexe typen en verzamelingen van complexe typen.|
| Taalkundige analyse | Analysefuncties zijn onderdelen die worden gebruikt om tekst te verwerken tijdens het indexeren en zoeken. Er zijn twee typen. <br/><br/>[**Aangepaste lexicale analysefuncties**](index-add-custom-analyzers.md) worden gebruikt voor complexe zoekquery’s met behulp van fonetische overeenkomsten en reguliere expressies. <br/><br/>[**Taalanalysefuncties**](index-add-language-analyzers.md) van Lucene of Microsoft worden gebruikt voor het intelligent verwerken van taalspecifieke taalkundige aspecten, zoals werkwoordtijden, geslacht, afwijkende meervoudsvormen voor zelfstandige naamwoorden, het opsplitsen van woorden, het afbreken van woorden (voor talen zonder spaties), en meer. <br/><br/>|


| Platform&nbsp;niveau&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Functies |
|-------------------|----------|
| Hulpprogramma's voor het ontwikkelen van prototypen en voor controle | In de portal kunt u de [**wizard Gegevens importeren**](search-import-data-portal.md) gebruiken om indexeerfuncties te configureren, Index Designer om een index te bouwen, en [**Search Explorer**](search-explorer.md) om query’s te testen en scoreprofielen te verfijnen. U kunt ook elke gewenste index openen om het bijbehorende schema te bekijken. |
| Controle en diagnose | [**Schakel controlefuncties in**](search-monitor-usage.md) om verder te gaan dan de oppervlakkige metrische gegevens die altijd zichtbaar zijn in de portal. Metrische gegevens over query’s per seconde, latentie en beperkingen worden vastgelegd en gerapporteerd op portalpagina’s zonder dat hiervoor extra configuratie is vereist.|
| Versleuteling aan de serverzijde | [**Door Microsoft beheerde versleuteling van inactieve gegevens**](search-security-overview.md#encrypted-transmissions-and-storage) is ingebouwd in de interne opslaglaag en is onherroepelijk. U kunt als u wilt de standaardversleuteling aanvullen met [**door de klant beheerde versleutelingssleutels**](search-security-manage-encryption-keys.md). Sleutels die u maakt en beheert in Azure Key Vault worden gebruikt om indexen en synoniemen in Azure Cognitive Search te versleutelen. |
| Infrastructuur | Het **maximaal beschikbare platform** zorgt ervoor dat de zoekservice uiterst betrouwbaar is. [Azure Cognitive Search biedt een SLA voor 99,9% beschikbaarheid](https://azure.microsoft.com/support/legal/sla/search/v1_0/) als er naar behoren is geschaald.<br/><br/> Azure Cognitive Search is een end-to-end oplossing die **volledig beheerd en schaalbaar** is en vereist geen enkel infrastructuurbeheer. De service kan worden aangepast aan uw persoonlijke behoeften door in twee dimensies te schalen voor het verwerken van meer documentopslag, een hogere querybelasting, of beide.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Azure Cognitive Search gebruiken
### <a name="step-1-provision-service"></a>Stap 1: Service inrichten
U kunt een Azure Cognitive Search-service inrichten in [Azure Portal](https://portal.azure.com/) of met behulp van de [Azure Resource Management-API](/rest/api/searchmanagement/). U kunt kiezen voor de gratis service die is gedeeld met andere abonnees, of u kunt een [betaalde laag](https://azure.microsoft.com/pricing/details/search/) kiezen met toegewezen resources die alleen voor uw service worden gebruikt. Voor betaalde lagen kunt u een service in twee dimensies schalen: 

- Voeg replica’s toe om de capaciteit voor het verwerken van zware querybelastingen te vergroten.   
- Voeg partities toe om meer documenten op te kunnen slaan. 

Door de opslag van documenten en de doorvoer van query’s afzonderlijk aan te pakken kunt u het gebruik van resources afstemmen op basis van productievereisten.

### <a name="step-2-create-index"></a>Stap 2: Index maken
Voordat u doorzoekbare inhoud kunt uploaden, moet u eerst een Azure Cognitive Search-index definiëren. Een index is vergelijkbaar met een databasetabel waarin uw gegevens zijn opgeslagen, en u kunt zoekquery’s uitvoeren voor een index. U definieert het indexschema dat moet worden toegewezen, om de structuur van de documenten te weerspiegelen waarin u wilt zoeken, vergelijkbaar met de velden in een database.

U kunt een schema maken in Azure Portal of via een programma met behulp van de [.NET SDK](search-howto-dotnet-sdk.md) of [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Stap 3: Gegevens laden
Nadat u een index hebt gedefinieerd, bent u klaar om inhoud te uploaden. U kunt hiervoor een push- of een pull-model gebruiken.

Met het pull-model worden gegevens opgehaald uit externe gegevensbronnen. Het model wordt ondersteund met *indexeerfuncties* die aspecten van de gegevensopname stroomlijnen en automatiseren, zoals het verbinden met, en lezen en serialiseren van gegevens. Er zijn [indexeerfuncties](/rest/api/searchservice/Indexer-operations) beschikbaar voor Azure Cosmos DB, Azure SQL Database, Azure Blob Storage en SQL Server gehost op een Azure-VM. U kunt een indexeerfunctie configureren op-aanvraag of als geplande gegevensvernieuwing.

Het push-model wordt geboden via de SDK of REST API’s, en gebruikt om bijgewerkte documenten naar een index te verzenden. U kunt gegevens uit nagenoeg elke gegevensset pushen met behulp van de JSON-indeling. Zie [Add, update, or delete Documents](/rest/api/searchservice/addupdate-or-delete-documents) (Documenten toevoegen, bijwerken of verwijderen) of [How to use the .NET SDK](search-howto-dotnet-sdk.md) (De .NET SDK gebruiken) voor informatie over het laden van gegevens.

### <a name="step-4-search"></a>Stap 4: Search
Nadat u een index hebt gevuld, kunt u [zoekquery’s verzenden](search-query-overview.md) naar het service-eindpunt met behulp van eenvoudige HTTP-aanvragen met [REST API](/rest/api/searchservice/Search-Documents) of de [.NET SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations).

Volg [Uw eerste zoek-app maken](tutorial-csharp-create-first-app.md) om een webpagina te maken en uit te breiden die gebruikersinvoer verzamelt en de resultaten verwerkt. U kun ook [Postman voor interactieve REST](search-get-started-postman.md)-aanroepen gebruiken of de in Azure-portal ingebouwde [Search Explorer](search-explorer.md) om een query uit te voeren op een bestaande index.

## <a name="how-it-compares"></a>Vergelijking

Klanten vragen vaak wat de verschillen zijn tussen Azure Cognitive Search en andere zoekgerelateerde oplossingen. In de volgende tabel worden de verschillen samengevat.

| Vergeleken met | Belangrijke verschillen |
|-------------|-----------------|
|Bing | Met [Bing Webzoekopdrachten-API](../cognitive-services/bing-web-search/index.yml) wordt in de indexen op Bing.com gezocht naar overeenkomsten voor termen die u hebt ingediend. Indexen zijn gebouwd uit HTML, XML en andere webinhoud op openbare sites. [Bing Aangepaste zoekopdrachten](/azure/cognitive-services/bing-custom-search/), dat op dezelfde basis is gebouwd, biedt dezelfde verkenningstechnologie voor typen webinhoud, met een bereik dat is ingesteld op afzonderlijke websites.<br/><br/>Met Azure Cognitive Search wordt gezocht in een door u gedefinieerde index, die is gevuld met gegevens en documenten waarvan u de eigenaar bent, vaak afkomstig uit verschillende bronnen. Azure Cognitive Search heeft verkenningsmogelijkheden voor bepaalde gegevensbronnen via [indexeerfuncties](search-indexer-overview.md), maar u kunt elk JSON-document dat overeenstemt met uw indexschema, naar een enkele geconsolideerde resource pushen. |
|Zoeken in database | Veel databaseplatforms beschikken over een ingebouwde zoekfunctie. SQL Server heeft een functie voor [zoeken in volledige tekst](/sql/relational-databases/search/full-text-search). Cosmos DB en soortgelijke technologieën hebben indexen waarop kan worden gezocht. Bij de evaluatie van producten die zoeken en opslag combineren, is het soms lastig om te bepalen wat u moet kiezen. Veel oplossingen gebruiken beide: DBMS voor opslag en Azure Cognitive Search voor gespecialiseerde zoekfuncties.<br/><br/>Vergeleken met DBMS slaat Azure Cognitive Search de inhoud van heterogene bronnen op en biedt het gespecialiseerde tekstverwerkingsfuncties, zoals linguïstische tekstverwerking (woordstammen, lemmata, woordvormen) in [56 talen](/rest/api/searchservice/language-support). Het ondersteunt ook automatische correctie van verkeerd gespelde woorden, [synoniemen](/rest/api/searchservice/synonym-map-operations), [suggesties](/rest/api/searchservice/suggestions), [scoringsbesturingselementen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetten](./search-filters-facets.md) en [aangepaste tokenisering](/rest/api/searchservice/custom-analyzers-in-azure-search). De [engine voor zoekopdrachten in volledige tekst](search-lucene-query-architecture.md) in Azure Cognitive Search is gebouwd op Apache Lucene, een industrienorm op het gebied van het ophalen van gegevens. Hoewel Azure Cognitive Search gegevens bewaart in de vorm van een omgekeerde index, is het zelden een vervanging voor echte gegevensopslag. Zie dit [forumbericht](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data) voor meer informatie. <br/><br/>Een ander belangrijk punt in deze categorie is het gebruik van resources. Indexering en bepaalde querybewerkingen zijn vaak rekenintensief. Door de zoekopdracht van de DBMS naar een toegewezen oplossing in de cloud te verplaatsen, blijven systeemresources beschikbaar voor transactieverwerking. En door de zoekfunctie te externaliseren, kunt u eenvoudig schalen naar het juiste queryvolume.|
|Toegewezen zoekoplossing | Ervan uitgaande dat u hebt gekozen voor een toegewezen zoekoplossing met het volledige spectrum aan functies, dan is er nog een essentieel onderscheid tussen on-premises oplossingen en een cloudservice. Veel zoektechnologieën bieden controle over het indexeren en doorzoeken van pijplijnen, toegang tot rijkere syntaxis voor filteren en het uitvoeren van query’s, beheer van rangen en relevantie, en functies voor zelfgestuurde en intelligente zoekopdrachten. <br/><br/>Als u op zoek bent naar een pasklare oplossing met minimale overhead, minimaal onderhoud en aanpasbare schaling is een cloudservice de juiste keuze voor u. <br/><br/>Binnen het cloudmodel bieden verschillende providers vergelijkbare basisfuncties, met Zoekopdracht in volledige tekst, op geografische locaties zoeken en de mogelijkheid om te gaan met een zekere dubbelzinnigheid van zoekinvoergegevens. Meestal bepaalt een [gespecialiseerde functie](#feature-drilldown), of het gemak en de algehele eenvoud van de API’s, de hulpprogramma’s en het beheer, welke oplossing het meest geschikt voor u is. |

Azure Cognitive Search is van alle cloudproviders het sterkst op het gebied van zoeken in volledige tekst in inhoudsopslag en databases in Azure, voor apps die hoofdzakelijk afhankelijk zijn van het ophalen van gegevens en van inhoudsnavigatie. 

Belangrijke pluspunten zijn onder andere:

+ Azure-gegevensintegratie (verkenners) in de indexeringslaag
+ Azure Portal voor centraal beheer
+ Schaling, betrouwbaarheid en beschikbaarheid van wereldklasse in Azure
+ AI-verwerking van onbewerkte gegevens om ze beter doorzoekbaar te maken, met inbegrip van afbeeldingen of het vinden van patronen in ongestructureerde inhoud.
+ Taalkundige en aangepaste analyse met analysefuncties voor grondig zoeken in volledige tekst in 56 talen
+ [Kernfuncties die eigen zijn aan zoekgerichte apps](#feature-drilldown): scoren, facetten, suggesties, synoniemen, op geografische locaties zoeken, en meer.

> [!Note]
> Niet-Azure-gegevensbronnen worden volledig ondersteund, maar zijn gebaseerd op code-intensievere pushmethodes in plaats van op indexeerfuncties. Als u API’s gebruikt, kunt u elke JSON-documentverzameling doorsluizen naar een Azure Cognitive Search-index.

Onder onze klanten bevinden zich degenen die het breedste scala aan functies in Azure Cognitive Search gebruiken, onder andere onlinecatalogussen, Line-Of-Business-programma’s en toepassingen voor het ontdekken van documenten.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

Hoewel veel taken in de portal kunnen worden uitgevoerd, is Azure Cognitive Search bedoelt voor ontwikkelaars die de zoekfunctie willen integreren in bestaande toepassingen. De volgende programmeerinterfaces zijn beschikbaar.

|Platform |Beschrijving |
|-----|------------|
|[REST](/rest/api/searchservice/) | HTTP-opdrachten die worden ondersteund door een willekeurig programmeerplatform en een willekeurige programmeertaal, waaronder Java, Python en JavaScript|
|[.NET SDK](search-howto-dotnet-sdk.md) | .NET-wrapper voor de REST API biedt efficiënte codering in C# en andere beheerde codetalen die zijn bedoeld voor .NET Framework |

## <a name="free-trial"></a>Gratis proefversie
Azure-abonnees kunnen [een service inrichten in de gratis laag](search-create-service-portal.md).

Als u geen abonnee bent, kunt u [gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). U krijgt een tegoed om de betaalde Azure-services uit te proberen. Als uw tegoed op is, kunt u het account behouden en de [gratis Azure-services](https://azure.microsoft.com/free/) gebruiken. Er worden nooit kosten in rekening gebracht bij uw creditcard tenzij u de instellingen expliciet wijzigt en aangeeft dat u wilt betalen.

U kunt ook [voordelen voor MSDN-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Via uw MSDN-abonnement ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken. 

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

1. Maak een [gratis service](search-create-service-portal.md). Alle snelstartgidsen en zelfstudies kunnen worden gevolgd in de gratis service.

2. Doorloop de [zelfstudie over het gebruik van ingebouwde hulpprogramma's voor indexeren en query's](search-get-started-portal.md). Leer meer over belangrijke concepten en raak vertrouwd met de informatie die de portal biedt.

3. Ga verder met code met behulp van de .NET of REST-API:

   + In [De .NET SDK gebruiken](search-howto-dotnet-sdk.md) wordt de hoofdwerkstroom voor beheerde code gedemonstreerd.  
   + In [Get started with the REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) (Aan de slag met REST API) worden dezelfde stappen getoond met behulp van de REST API. U kunt deze snelstartgids ook gebruiken om REST-API's aan te roepen vanuit Postman of Fiddler: [De Azure Cognitive Search REST API's verkennen](search-get-started-postman.md).

## <a name="watch-this-video"></a>Deze video bekijken

Zoekmachines zijn de normale stuurprogramma’s voor het ophalen van informatie in mobiele apps, op het web en in zakelijke gegevensopslag. Azure Cognitive Search biedt hulpprogramma’s voor het creëren van een zoekervaring die vergelijkbaar is met die op grote commerciële websites.

In deze vijftien minuten durende video introduceert programmamanager Luis Cabrera Azure Cognitive Search. 

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]