---
title: Gegevens importeren in een zoekindex met Azure-portal
titleSuffix: Azure Cognitive Search
description: Meer informatie over het gebruik van de wizard Gegevens importeren in de Azure-portal om Azure-gegevens te crawlen vanuit Cosmos DB, Blob-opslag, tabelopslag, SQL Database en SQL Server in Azure VM's.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460697"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Wizard Gegevens importeren voor Azure Cognitive Search

De Azure-portal biedt een wizard **Gegevens importeren** op het Azure Cognitive Search-dashboard voor prototypen en het laden van een index. Dit artikel behandelt voordelen en beperkingen van het gebruik van de wizard, ingangen en uitvoer, en sommige gebruiksinformatie. Zie de Quickstart-index Voor praktische richtlijnen voor het doorlopen van de wizard met ingebouwde voorbeeldgegevens de [module Een Azure Cognitive Search maken met de Azure-portal.](search-get-started-portal.md)

Bewerkingen die deze wizard uitvoert, zijn onder andere:

1 - Maak verbinding met een ondersteunde Azure-gegevensbron.

2 - Maak een indexschema, afgeleid door bemonsteringsbrongegevens.

3 - Voeg optioneel AI-verrijkingen toe om inhoud en structuur te extraheren of te genereren.

4 - Voer de wizard uit om objecten te maken, gegevens te importeren, een planning in te stellen en andere configuratieopties in te stellen.

De wizard maakt een aantal objecten die zijn opgeslagen in uw zoekservice, die u programmatisch of in andere hulpprogramma's openen.

## <a name="advantages-and-limitations"></a>Voordelen en beperkingen

Voordat u een code schrijft, u de wizard gebruiken voor prototypen en proof-of-concept testen. De wizard maakt verbinding met externe gegevensbronnen, bemonstert de gegevens om een eerste index te maken en importeert de gegevens vervolgens als JSON-documenten in een index op Azure Cognitive Search. 

Bemonstering is het proces waarbij een indexschema wordt afgeleid en het heeft een aantal beperkingen. Wanneer de gegevensbron wordt gemaakt, kiest de wizard een voorbeeld van documenten om te bepalen welke kolommen deel uitmaken van de gegevensbron. Niet alle bestanden worden gelezen, omdat dit mogelijk uren kan duren voor zeer grote gegevensbronnen. Met een selectie van documenten worden bronmetagegevens, zoals veldnaam of type, gebruikt om een veldenverzameling in een indexschema te maken. Afhankelijk van de complexiteit van brongegevens moet u mogelijk het oorspronkelijke schema bewerken voor nauwkeurigheid of uitbreiden voor volledigheid. U uw wijzigingen inline aanbrengen op de indexdefinitiepagina.

Over het algemeen zijn de voordelen van het gebruik van de wizard duidelijk: zolang aan de vereisten wordt voldaan, u binnen enkele minuten een querybare index prototypen. Sommige van de complexiteiten van indexering, zoals het verstrekken van gegevens als JSON-documenten, worden behandeld door de wizard.

Bekende beperkingen worden als volgt samengevat:

+ De wizard ondersteunt geen iteratie of hergebruik. Elke doorgang door de wizard maakt een nieuwe index-, skillset- en indexerconfiguratie. Alleen gegevensbronnen kunnen worden gehandhaafd en opnieuw worden gebruikt binnen de wizard. Als u andere objecten wilt bewerken of verfijnen, moet u de REST-API's of .NET SDK gebruiken om de structuren op te halen en te wijzigen.

+ Broninhoud moet zich in een ondersteunde Azure-gegevensbron bevinden.

+ De bemonstering bedraagt meer dan een subset van brongegevens. Voor grote gegevensbronnen is het mogelijk dat de wizard velden mist. Mogelijk moet u het schema uitbreiden of de afgeleide gegevenstypen corrigeren als de bemonstering onvoldoende is.

+ AI verrijking, zoals blootgesteld in het portaal, is beperkt tot een paar ingebouwde vaardigheden. 

+ Een [kennisarchief](knowledge-store-concept-intro.md), die door de wizard kan worden gemaakt, is beperkt tot een paar standaardprojecties. Als u verrijkte documenten wilt opslaan die door de wizard zijn gemaakt, worden de blobcontainer en -tabellen geleverd met standaardnamen en structuur.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Gegevensbroninvoer

De wizard **Gegevens importeren** maakt verbinding met een externe gegevensbron met behulp van de interne logica die wordt geleverd door Azure Cognitive Search-indexeerders, die zijn uitgerust om de bron te bemonsteren, metagegevens te lezen, documenten te kraken om inhoud en structuur te lezen en inhoud te serialiseren als JSON voor latere import in Azure Cognitive Search.

U alleen importeren uit één tabel, databaseweergave of gelijkwaardige gegevensstructuur, maar de structuur kan hiërarchische of geneste substructuren bevatten. Zie [Complexe typen modelleren voor](search-howto-complex-data-types.md)meer informatie.

U moet deze ene tabel of weergave maken voordat u de wizard uitvoert en deze inhoud bevat. Om voor de hand liggende redenen heeft het geen zin om de wizard **Gegevens importeren** uit te voeren op een lege gegevensbron.

|  Selectie | Beschrijving |
| ---------- | ----------- |
| **Bestaande gegevensbron** |Als u al indexeerders hebt gedefinieerd in uw zoekservice, hebt u mogelijk een bestaande gegevensbrondefinitie die u opnieuw gebruiken. In Azure Cognitive Search worden gegevensbronobjecten alleen gebruikt door indexeerders. U een gegevensbronobject programmatisch of via de wizard **Gegevens importeren** maken en indien nodig opnieuw gebruiken.|
| **Monsters**| Azure Cognitive Search biedt twee ingebouwde voorbeeldgegevensbronnen die worden gebruikt in tutorials en quickstarts: een SQL-database voor onroerend goed en een Hotels-database die wordt gehost op Cosmos DB. Zie het snel begin van een [index maken in de Azure-portal](search-get-started-portal.md) voor een wandeling op basis van het voorbeeld Hotels. |
| [**Azure SQL Database**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |De servicenaam, referenties voor een databasegebruiker met leesmachtiging en de naam van een database kunnen worden opgegeven op de pagina of via een ADO.NET-verbindingsreeks. Kies de verbindingsreeksoptie om eigenschappen te bekijken of aan te passen. <br/><br/>De tabel of weergave die de rijenset bevat, moet worden opgegeven op de pagina. Deze optie wordt weergegeven nadat de verbinding tot stand is gebracht, waarna een vervolgkeuzelijst wordt weergegeven zodat u een selectie kunt maken.|
| **SQL Server op virtuele Azure-machine** |Geef een volledig gekwalificeerde servicenaam, gebruikersnaam en wachtwoord en database op als verbindingstekenreeks. Voor het gebruik van deze gegevensbron moet u eerder een certificaat hebben geïnstalleerd in het lokale archief dat de verbinding versleutelt. Zie SQL [VM-verbinding met Azure Cognitive Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)voor instructies . <br/><br/>De tabel of weergave die de rijenset bevat, moet worden opgegeven op de pagina. Deze optie wordt weergegeven nadat de verbinding tot stand is gebracht, waarna een vervolgkeuzelijst wordt weergegeven zodat u een selectie kunt maken. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Vereisten zijn het account, de database en de verzameling. Alle documenten in de verzameling worden opgenomen in de index. U een query definiëren om de rijset af te vlakken of te filteren of de query leeg te laten. Een query is niet vereist in deze wizard.|
| [**Azure Blob-opslag**](search-howto-indexing-azure-blob-storage.md) |Vereisten zijn het opslagaccount en een container. Als blob-namen een virtuele naamconventie voor groeperingsdoeleinden volgen, kunt u desgewenst het gedeelte van de virtuele map van de naam als een map onder de container opgeven. Zie [Blob Storage indexeren](search-howto-indexing-azure-blob-storage.md) voor meer informatie. |
| [**Azure-tabelopslag**](search-howto-indexing-azure-tables.md) |Vereisten zijn het opslagaccount en een tabelnaam. U kunt desgewenst een query opgeven om een subset van de tabellen op te halen. Zie [Table Storage indexeren](search-howto-indexing-azure-tables.md) voor meer informatie. |

## <a name="wizard-output"></a>Wizard-uitvoer

Achter de schermen maakt, configureert en roept de wizard de volgende objecten aan. Nadat de wizard is uitgevoerd, u de uitvoer ervan vinden op de portalpagina's. De overzichtspagina van uw service bevat lijsten met indexen, indexeerders, gegevensbronnen en skillsets. Indexdefinities kunnen volledig worden bekeken JSON in de portal. Voor andere definities u de [REST-API](https://docs.microsoft.com/rest/api/searchservice/) gebruiken om specifieke objecten te krijgen.

| Object | Beschrijving | 
|--------|-------------|
| [Gegevensbron](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Blijft verbindingsgegevens met brongegevens, inclusief referenties, voortduren. Een gegevensbronobject wordt uitsluitend gebruikt bij indexeerders. | 
| [Index](https://docs.microsoft.com/rest/api/searchservice/create-index) | Fysieke gegevensstructuur die wordt gebruikt voor zoeken in volledige tekst en andere query's. | 
| [Vaardighedenset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Een complete set instructies voor het manipuleren, transformeren en vormgeven van inhoud, waaronder het analyseren en extraheren van informatie uit afbeeldingsbestanden. Met uitzondering van zeer eenvoudige en beperkte structuren, bevat het een verwijzing naar een Cognitive Services-bron die verrijking biedt. Optioneel kan het ook een definitie van kennisopslag bevatten.  | 
| [Indexeerfunctie](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Een configuratieobject met een gegevensbron, doelindex, een optionele skillset, optioneel schema en optionele configuratie-instellingen voor foutoverhandiging en base-64-codering. |


## <a name="how-to-start-the-wizard"></a>De wizard starten

De wizard Gegevens importeren wordt gestart vanaf de opdrachtbalk op de pagina ServiceOverzicht.

1. Open in de [Azure-portal](https://portal.azure.com)de pagina met zoekservice vanuit het dashboard of [zoek uw service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in de servicelijst.

2. Klik boven op de pagina serviceoverzicht op **Gegevens importeren**.

   ![Opdracht Gegevens importeren in portal](./media/search-import-data-portal/import-data-cmd2.png "De wizard Gegevens importeren starten")

U ook **Importgegevens** uit andere Azure-services starten, waaronder Azure Cosmos DB, Azure SQL Database en Azure Blob-opslag. Zoek naar **Azure Cognitive Search toevoegen** in het linkernavigatiedeelvenster op de pagina serviceoverzicht.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Een indexschema bewerken of voltooien in de wizard

De wizard genereert een onvolledige index, die wordt gevuld met documenten die zijn verkregen uit de bron van invoergegevens. Voor een functionele index moet u ervoor zorgen dat de volgende elementen zijn gedefinieerd.

1. Is de veldlijst compleet? Voeg nieuwe velden toe die de steekproef hebben gemist en verwijder velden die geen waarde toevoegen aan een zoekervaring of die niet worden gebruikt in een [filterexpressie](search-query-odata-filter.md) of [scoreprofiel.](index-add-scoring-profiles.md)

1. Is het gegevenstype geschikt voor de binnenkomende gegevens? Azure Cognitive Search ondersteunt de [gegevenstypen (Entity Data Model) (EDM).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Voor Azure SQL-gegevens is er [toewijzingsdiagram](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) dat gelijkwaardige waarden bevat. Zie [Veldtoewijzingen en transformaties voor](search-indexer-field-mappings.md)meer achtergrond.

1. Heeft u een veld dat kan dienen als de *sleutel?* Dit veld moet Edm.string zijn en moet een document op unieke wijze identificeren. Voor relationele gegevens kan deze worden toegewezen aan een primaire sleutel. Voor blobs is het `metadata-storage-path`misschien de. Als veldwaarden spaties of streepjes bevatten, moet u de optie **Basis-64 Codesleutel** instellen in de stap **Een indexer maken** onder **Geavanceerde opties**om de validatiecontrole voor deze tekens te onderdrukken.

1. Stel kenmerken in om te bepalen hoe dat veld in een index wordt gebruikt. 

   Neem de tijd met deze stap omdat kenmerken de fysieke expressie van velden in de index bepalen. Als u later, zelfs programmatisch, kenmerken wilt wijzigen, moet u de index bijna altijd laten vallen en opnieuw opbouwen. Kernkenmerken zoals **Doorzoekbaar** en **opvraagbaar** hebben een [verwaarloosbaar effect op de opslag.](search-what-is-an-index.md#index-size) Het inschakelen van filters en het gebruik van suggesties verhogen de opslagvereisten. 
   
   + **Doorzoekbaar** maakt full-text zoeken mogelijk. Elk veld dat wordt gebruikt in vrije formulierquery's of queryexpressies moet dit kenmerk hebben. Omgekeerde indexen worden gemaakt voor elk veld dat u markeert als **Doorzoekbaar**.

   + **Opvraagbaar** retourneert het veld in zoekresultaten. Elk veld dat inhoud aan zoekresultaten levert, moet dit kenmerk hebben. Als u dit veld instelt, heeft dit geen effect op de indexgrootte.

   + **Met filterbaar** kan naar het veld worden verwezen in filterexpressies. Elk veld dat in een **$filter** expressie wordt gebruikt, moet dit kenmerk hebben. Filterexpressies zijn voor exacte overeenkomsten. Omdat teksttekenreeksen intact blijven, is extra opslag vereist om de letterlijke inhoud te kunnen verwerken.

   + **Facetable** maakt het veld mogelijk voor gefacetteerde navigatie. Alleen velden die ook als **filterbaar zijn gemarkeerd,** kunnen worden gemarkeerd als **Facetable**.

   + **Met sorteerbaar** kan het veld in een soort worden gebruikt. Elk veld dat in een **$Orderby** expressie wordt gebruikt, moet dit kenmerk hebben.

1. Heeft u [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis)nodig? Voor edm.string-velden die **doorzoekbaar**zijn, u een **analyzer** instellen als u indexering en query's met een taalhebt verbeterd. 

   De standaardinstelling is *Standard Lucene,* maar u *Microsoft Engels* kiezen als u de analyzer van Microsoft wilt gebruiken voor geavanceerde lexicale verwerking, zoals het oplossen van onregelmatige zelfstandige naamwoorden en werkwoordformulieren. Alleen taalanalysers kunnen in de portal worden opgegeven. Met behulp van een aangepaste analyzer of een niet-taal analyzer zoals Trefwoord, Patroon, enzovoort, moet programmatisch worden gedaan. Zie [Taalanalysers toevoegen](search-language-support.md)voor meer informatie over analysers.

1. Heeft u typeahead-functionaliteit nodig in de vorm van automatisch aanvullen of voorgestelde resultaten? Schakel het **selectievakje Voorsteler** in om suggesties [voor typen vooraf query's](index-add-suggesters.md) in te schakelen en automatisch aanvullen op geselecteerde velden in te schakelen. Suggesties voegen toe aan het aantal tokenized termen in uw index en verbruiken dus meer opslagruimte.


## <a name="next-steps"></a>Volgende stappen

De beste manier om de voordelen en beperkingen van de wizard te begrijpen, is door er doorheen te stappen. De volgende quickstart leidt u door elke stap.

> [!div class="nextstepaction"]
> [Een Azure Cognitive Search-index maken met de Azure-portal](search-get-started-portal.md)