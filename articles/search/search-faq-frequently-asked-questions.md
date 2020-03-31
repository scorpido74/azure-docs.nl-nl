---
title: Veelgestelde vragen
titleSuffix: Azure Cognitive Search
description: Antwoorden op veelgestelde vragen over microsoft Azure Cognitive Search-service, een door de cloud gehoste zoekservice op Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fee74cb6ec5acd5fa0f171eab9769a833f04ad66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792916"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Cognitive Search - veelgestelde vragen (FAQ)

 Vind antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot Azure Cognitive Search.

## <a name="platform"></a>Platform

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Waarin verschilt Azure Cognitive Search van zoeken in volledige tekst in mijn DBMS?

Azure Cognitive Search ondersteunt meerdere gegevensbronnen, [taalkundige analyse voor veel talen,](https://docs.microsoft.com/rest/api/searchservice/language-support)aangepaste analyse voor interessante en ongebruikelijke [gegevensinvoer,](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)besturingselementen voor zoekrangen via [scoreprofielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)en functies voor gebruikerservaring, zoals typeahead, hithighlighting en gefacetteerde navigatie. Het bevat ook andere functies, zoals synoniemen en syntaxis voor uitgebreide query's, maar die zijn over het algemeen geen onderscheidende functies.

### <a name="what-is-the-difference-between-azure-cognitive-search-and-elasticsearch"></a>Wat is het verschil tussen Azure Cognitive Search en Elasticsearch?

Bij het vergelijken van zoektechnologieën vragen klanten vaak om details over hoe Azure Cognitive Search zich verhoudt tot Elasticsearch. Klanten die Azure Cognitive Search verkiezen boven Elasticsearch voor hun zoektoepassingsprojecten doen dit doorgaans omdat we een belangrijke taak eenvoudiger hebben gemaakt of omdat ze de ingebouwde integratie met andere Microsoft-technologieën nodig hebben:

+ Azure Cognitive Search is een volledig beheerde cloudservice met 99,9% service level agreements (SLA) wanneer deze zijn uitgerust met voldoende redundantie (2 replica's voor leestoegang, drie replica's voor read-write).
+ Microsoft's [Natural language processors](https://docs.microsoft.com/rest/api/searchservice/language-support) bieden toonaangevende taalkundige analyse.  
+ [Azure Cognitive Search-indexeerders](search-indexer-overview.md) kunnen verschillende Azure-gegevensbronnen crawlen voor initiële en incrementele indexering.
+ Als u snel wilt reageren op schommelingen in query- of indexeringsvolumes, u [schuifregelaarbesturingselementen](search-manage.md#scale-up-or-down) gebruiken in de Azure-portal of een [PowerShell-script](search-manage-powershell.md)uitvoeren, waarbij shardbeheer rechtstreeks wordt omzeild.  
+ [Score- en tuningfuncties](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) bieden de middelen om de score van de zoekpositie te beïnvloeden die verder gaat dan wat de zoekmachine alleen kan bieden.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Kan ik de Azure Cognitive Search-service onderbreken en de facturering stoppen?

U de service niet onderbreken. Reken- en opslagbronnen worden toegewezen voor uw exclusieve gebruik wanneer de service wordt gemaakt. Het is niet mogelijk om deze middelen on-demand vrij te geven en terug te vorderen.

## <a name="indexing-operations"></a>Indexeringsbewerkingen

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Indexen of indexen verplaatsen, back-ups maken en herstellen?

Tijdens de ontwikkelingsfase u uw index verplaatsen tussen zoekservices. U bijvoorbeeld een basis- of gratis prijslaag gebruiken om uw index te ontwikkelen en deze vervolgens naar de standaard of hogere laag voor productiegebruik verplaatsen. 

U ook een back-up maken van een indexmomentopname naar bestanden die later kunnen worden gebruikt om deze te herstellen. 

U al deze dingen doen met de **voorbeeldcode voor index-back-up-herstel** in deze [Azure Cognitive Search .NET-voorbeeldrepo](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

U ook op elk gewenst moment [een indexdefinitie krijgen](https://docs.microsoft.com/rest/api/searchservice/get-index) met behulp van de Azure Cognitive Search REST API.

Er is momenteel geen ingebouwde indexextractie, momentopname of back-upherstelfunctie in de Azure-portal. We overwegen echter om de back-up- en herstelfunctionaliteit toe te voegen in een toekomstige release. Als u uw steun voor deze functie wilt tonen, brengt u een stem uit op [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Kan ik mijn index of service herstellen nadat deze is verwijderd?

Nee, als u een Azure Cognitive Search-index of -service verwijdert, kan deze niet worden hersteld. Wanneer u een Azure Cognitive Search-service verwijdert, worden alle indexen in de service permanent verwijderd. Als u een Azure-brongroep verwijdert die een of meer Azure Cognitive Search-services bevat, worden alle services permanent verwijderd.  

Voor het opnieuw maken van resources zoals indexen, indexers, gegevensbronnen en skillsets moet u deze opnieuw maken vanuit code. 

Als u een index opnieuw wilt maken, moet u gegevens uit externe bronnen opnieuw indexeren. Daarom wordt aanbevolen een hoofdkopie of back-up van de oorspronkelijke gegevens in een ander gegevensarchief te bewaren, zoals Azure SQL Database of Cosmos DB.

Als alternatief u de **voorbeeldcode voor index-back-up-herstel** gebruiken in deze [repo Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) om een back-up te maken van een indexdefinitie en indexmomentopname naar een reeks JSON-bestanden. Later u het gereedschap en de bestanden gebruiken om de index te herstellen, indien nodig.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Kan ik indexeren vanuit SQL-databasereplica's (van toepassing op [Azure SQL Database-indexeerders)](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)

Er zijn geen beperkingen op het gebruik van primaire of secundaire replica's als gegevensbron bij het bouwen van een index vanaf nul. Voor het vernieuwen van een index met incrementele updates (op basis van gewijzigde records) is echter de primaire replica vereist. Deze vereiste is afkomstig van SQL Database, die alleen het bijhouden van wijzigingen op primaire replica's garandeert. Als u secundaire replica's probeert te gebruiken voor een frequentie van indexvernieuwing, is er geen garantie dat u alle gegevens krijgt.

## <a name="search-operations"></a>Zoekbewerkingen

### <a name="can-i-search-across-multiple-indexes"></a>Kan ik zoeken in meerdere indexen?

Nee, deze bewerking wordt niet ondersteund. Zoeken is altijd scoped om een enkele index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Kan ik de toegang tot de zoekindex beperken op basis van de identiteit van de gebruiker?

U [beveiligingsfilters](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) implementeren met `search.in()` filter. Het filter vormt goed samen met [identiteitsbeheerservices zoals Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) om zoekresultaten bij te snijden op basis van gedefinieerd lidmaatschap van gebruikersgroepen.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Waarom zijn er nul overeenkomsten op voorwaarden die ik weet geldig te zijn?

Het meest voorkomende geval is niet wetende dat elk querytype verschillende zoekgedragen en niveaus van taalkundige analyses ondersteunt. Full text search, dat is de overheersende werklast, bevat een taalanalysefase die termen opsplitst in hoofdvormen. Dit aspect van query parsing werpt een breder net over mogelijke overeenkomsten, omdat de tokenized term overeenkomt met een groter aantal varianten.

Wildcard-, fuzzy- en regex-query's worden echter niet geanalyseerd zoals gewone term- of woordgroepquery's en kunnen leiden tot een slechte terugroepactie als de query niet overeenkomt met de geanalyseerde vorm van het woord in de zoekindex. Zie [queryarchitectuur](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)voor meer informatie over queryparsing en analyse.

### <a name="my-wildcard-searches-are-slow"></a>Mijn wildcard zoekopdrachten zijn traag.

De meeste wildcard-zoekopdrachten, zoals voorvoegsel, fuzzy en regex, worden intern herschreven met overeenkomende termen in de zoekindex. Deze extra verwerking van het scannen van de zoekindex draagt bij aan latentie. Verder kunnen brede zoekopdrachten, zoals `a*` bijvoorbeeld, die waarschijnlijk met veel termen worden herschreven, erg traag zijn. Voor performante wildcardzoekopdrachten u overwegen een [aangepaste analyzer te](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)definiëren.

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Waarom is de zoekopdracht rang een constante of gelijke score van 1.0 voor elke hit?

Standaard worden zoekresultaten beoordeeld op basis van de [statistische eigenschappen van overeenkomende termen](search-lucene-query-architecture.md#stage-4-scoring)en geordend van hoog tot laag in de resultaatset. Sommige querytypen (wildcard, voorvoegsel, regex) dragen echter altijd een constante score bij aan de totale documentscore. Dit gedrag is standaard. Azure Cognitive Search legt een constante score op om overeenkomsten die via queryuitbreiding worden gevonden, in de resultaten te kunnen opnemen, zonder dat dit gevolgen heeft voor de rangschikking.

Stel dat een invoer van "tour*" in een wildcard-zoekopdracht overeenkomsten oplevert op "tours", "tourettes" en "tourmaline". Gezien de aard van deze resultaten, is er geen manier om redelijkerwijs af te leiden welke termen waardevoller zijn dan andere. Daarom negeren we termfrequenties bij het scoren van resultaten in query's van typen wildcard, voorvoegsel en regex. Zoekresultaten op basis van een gedeeltelijke invoer krijgen een constante score om bias naar mogelijk onverwachte overeenkomsten te voorkomen.

## <a name="design-patterns"></a>Ontwerppatronen

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Wat is de beste aanpak voor het implementeren van gelokaliseerdzoeken?

De meeste klanten kiezen speciale velden boven een verzameling als het gaat om het ondersteunen van verschillende landlocaties (talen) in dezelfde index. Locale-specifieke velden maken het mogelijk om een geschikte analyzer toe te wijzen. Bijvoorbeeld het toewijzen van de Microsoft French Analyzer aan een veld met Franse tekenreeksen. Het vereenvoudigt ook het filteren. Als u weet dat een query wordt gestart op een fr-fr-pagina, u de zoekresultaten beperken tot dit veld. Of maak een [scoreprofiel](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) om het veld meer relatief gewicht te geven. Azure Cognitive Search ondersteunt meer dan [50 taalanalysers](https://docs.microsoft.com/azure/search/search-language-support) om uit te kiezen.

## <a name="next-steps"></a>Volgende stappen

Is uw vraag over een ontbrekende functie of functionaliteit? Vraag de functie aan op de [website van User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zie ook

 [StackOverflow: Azure Cognitive Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)  
 [Wat is Azure Cognitive Search?](search-what-is-azure-search.md)
