---
title: Veelgestelde vragen (FAQ)-Azure Search
description: Krijg antwoorden op veelgestelde vragen over Microsoft Azure Search Service, een in de Cloud gehoste zoek service op Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d4aae2f2ef9ccbc645647125682d999c11c99ab6
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649838"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Azure Search-Veelgestelde vragen (FAQ)

 Hier vindt u antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot Azure Search.

## <a name="platform"></a>Platform

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Hoe wijkt Azure Search af van zoek opdrachten in volledige tekst in mijn DBMS?

Azure Search ondersteunt meerdere gegevens bronnen, een [taal kundige analyse voor veel talen](https://docs.microsoft.com/rest/api/searchservice/language-support), [aangepaste analyse voor interessante en ongebruikelijke gegevens invoer](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), besturings elementen voor het zoeken van de rang schikking via [Score profielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)en gebruikers ervaring-functies zoals typeahead, treffers markeren en facet navigatie. Het bevat ook andere functies, zoals synoniemen en uitgebreide query syntaxis, maar dit zijn doorgaans geen onderscheid tussen functies.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Wat is het verschil tussen Azure Search en Elasticsearch?

Bij het vergelijken van zoek technologieën vragen klanten regel matig om specifieke informatie over de manier waarop Azure Search vergelijkt met Elasticsearch. Klanten die Azure Search over Elasticsearch voor hun zoek toepassings projecten kiezen, doen dit meestal omdat we een belang rijke taak makkelijker hebben gemaakt of de ingebouwde integratie met andere micro soft-technologieën nodig hebben:

+ Azure Search is een volledig beheerde Cloud service met 99,9% Service Level Agreements (SLA) bij het inrichten met voldoende redundantie (2 replica's voor lees toegang, 3 replica's voor lezen/schrijven).
+ De [natuurlijke taal-processors](https://docs.microsoft.com/rest/api/searchservice/language-support) van micro soft bieden toonaangevende taal analyse van de rand.  
+ [Azure Search Indexeer functies](search-indexer-overview.md) kunnen een groot aantal Azure-gegevens bronnen verkennen voor initiële en incrementele indexering.
+ Als u snelle reacties op schommelingen in query-of indexerings volumes nodig hebt, kunt u [Schuif regelaars](search-manage.md#scale-up-or-down) gebruiken in de Azure portal of een [Power shell-script](search-manage-powershell.md)uitvoeren, zodat u het Shard-beheer rechtstreeks omzeilt.  
+ Beoordelings- [en afstemmings functies](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) bieden de mogelijkheid om te zorgen voor zoek positie scores dan wat de zoek machine alleen kan bieden.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Kan ik Azure Search service onderbreken en de facturering stoppen?

U kunt de service niet onderbreken. Reken-en opslag resources worden toegewezen voor exclusief gebruik wanneer de service wordt gemaakt. Het is niet mogelijk om deze resources op aanvraag vrij te geven en te claimen.

## <a name="indexing-operations"></a>Indexerings bewerkingen

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Back-up en herstel (of down load en verplaats) indexen of index momentopnamen?

Hoewel u op elk gewenst moment [een index definitie kunt ophalen](https://docs.microsoft.com/rest/api/searchservice/get-index) , is er geen index extractie, moment opname of back-upfunctie voor het downloaden van een gevulde index die in de Cloud wordt uitgevoerd naar een lokaal systeem of het verplaatsen naar een andere Azure Search service.

Indexen worden gebouwd en gevuld met code die u schrijft en worden alleen uitgevoerd op Azure Search in de Cloud. Klanten die een index naar een andere service willen verplaatsen, doen dit doorgaans door hun code te bewerken voor het gebruik van een nieuw eind punt en vervolgens het indexeren opnieuw uit te voeren. Als u een moment opname wilt maken of een back-up wilt maken van een index, zet u een stem af op de stem van de [gebruiker](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Kan ik mijn index of service herstellen nadat deze is verwijderd?

Nee, u kunt geen indexen of services herstellen. Als u een Azure Search index verwijdert, is de bewerking definitief en kan de index niet worden hersteld. Wanneer u een Azure Search service verwijdert, worden alle indexen in de service permanent verwijderd. Ook als u een Azure-resource groep verwijdert die een of meer Azure Search Services bevat, worden alle services permanent verwijderd.  

Voor het herstellen van resources zoals indexen, Indexeer functies, gegevens bronnen en vaardig heden moet u ze opnieuw maken op basis van code. In het geval van indexen moet u de gegevens van externe bronnen opnieuw indexeren. Daarom wordt het ten zeerste aanbevolen om een hoofd kopie of een back-up van de oorspronkelijke gegevens in een ander gegevens archief, zoals Azure SQL Database of Cosmos DB, te bewaren.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Kan ik indexeren vanuit SQL database replica's (van toepassing op [Azure SQL database Indexeer functies](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Er zijn geen beperkingen voor het gebruik van primaire of secundaire replica's als gegevens bron wanneer u een volledig nieuwe index bouwt. Voor het vernieuwen van een index met incrementele updates (op basis van gewijzigde records) is echter de primaire replica vereist. Deze vereiste is van SQL Database, die alleen het bijhouden van wijzigingen op primaire replica's waarborgt. Als u secundaire replica's probeert te gebruiken voor een werk belasting voor het vernieuwen van de index, is er geen garantie dat u alle gegevens kunt ophalen.

## <a name="search-operations"></a>Zoek bewerkingen

### <a name="can-i-search-across-multiple-indexes"></a>Kan ik zoeken in meerdere indexen?

Nee, deze bewerking wordt niet ondersteund. Het bereik van de zoek opdracht is altijd hetzelfde als een enkele index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Kan ik de toegang tot de zoek index beperken door de identiteit van de gebruiker?

U kunt [beveiligings filters](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) met behulp van `search.in()` filter implementeren. Het filter is goed samen met [identiteits beheer Services als Azure Active Directory (Aad)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) om Zoek resultaten te beperken op basis van gedefinieerd lidmaatschap van de gebruikers groep.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Waarom zijn er geen overeenkomsten met termen waarvan ik weet dat ze geldig zijn?

Het meest voorkomende geval is niet weten dat elk query type verschillende Zoek gedragingen en niveau taal kundige analyses ondersteunt. Zoek opdracht in volledige tekst, wat de meest voorkomende werk belasting is, omvat een taal analyse fase waarmee de voor waarden naar hoofd formulieren worden gesplitst. Met dit aspect van het parseren van query's wordt een breder net over mogelijke overeenkomsten gecast, omdat de token-term overeenkomt met een groter aantal varianten.

Joker tekens, fuzzy-en regex-query's worden echter niet geanalyseerd zoals normale term of woordgroepen query's en kunnen leiden tot slecht terughalen als de query niet overeenkomt met de geanalyseerde vorm van het woord in de zoek index. Zie [query-architectuur](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)voor meer informatie over het parseren en analyseren van query's.

### <a name="my-wildcard-searches-are-slow"></a>Mijn Zoek opdrachten met Joker tekens zijn langzaam.

De meeste zoek query's met Joker tekens, zoals voor voegsel, fuzzy en regex, worden intern herschreven met overeenkomende voor waarden in de zoek index. Deze extra verwerking van het scannen van de zoek index wordt toegevoegd aan de latentie. Uitgebreide zoek query's, zoals `a*` bijvoorbeeld, die waarschijnlijk worden herschreven, kunnen erg traag zijn. Voor het uitvoeren van zoek opdrachten met Joker tekens kunt u een [aangepaste analyse functie](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)definiëren.

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Waarom rangschikt de zoek opdracht een constante of gelijke Score van 1,0 voor elke treffer?

Standaard worden Zoek resultaten gescoord op basis van de [statistische eigenschappen van overeenkomende voor waarden](search-lucene-query-architecture.md#stage-4-scoring)en in de resultatenset van hoog naar laag geordend. Sommige query typen (joker tekens, voor voegsel, regex) bijdragen altijd een constante score naar de algehele document Score. Dit gedrag is standaard. Azure Search maakt een constante Score om overeenkomsten te toestaan die zijn gevonden via de query uitbreiding, zonder dat dit van invloed is op de rang schikking.

Stel bijvoorbeeld dat een invoer van ' Tour * ' in een zoek opdracht met Joker tekens resulteert in ' rond leidingen ', ' Tourettes ' en ' Tourmaline '. Gezien de aard van deze resultaten is er geen enkele manier om redelijkerwijs te afleiden welke termen waardevoler zijn dan andere. Daarom negeren we de term frequenties wanneer de Score resulteert in query's van het type Joker teken, voor voegsel en regex. Zoek resultaten op basis van een gedeeltelijke invoer krijgen een constante Score om bias te voor komen op mogelijke onverwachte overeenkomsten.

## <a name="design-patterns"></a>Ontwerppatronen

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Wat is de beste benadering voor het implementeren van gelokaliseerde zoek acties?

De meeste klanten kiezen voor een verzameling specifieke velden wanneer het gaat om verschillende land instellingen (talen) in dezelfde index te ondersteunen. Met taalspecifieke velden kunt u een geschikte analyse toewijzen. U kunt bijvoorbeeld de micro soft-Franse analyse toewijzen aan een veld met Franse teken reeksen. Het vereenvoudigt het filteren. Als u weet dat er een query op een fr-FR pagina wordt gestart, kunt u de zoek resultaten beperken tot dit veld. U kunt ook een [Score profiel](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) maken om het veld een groter relatief gewicht te geven. Azure Search ondersteunt meer dan [50 taal analyse](https://docs.microsoft.com/azure/search/search-language-support) functies om uit te kiezen.

## <a name="next-steps"></a>Volgende stappen

Is uw vraag over een ontbrekend onderdeel of functionaliteit? Vraag de functie aan op de website van de [gebruikers spraak](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zie ook

 [StackOverflow: Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [De manier waarop zoeken in volledige tekst werkt in Azure Search](search-lucene-query-architecture.md)  
 [Wat is Azure Search?](search-what-is-azure-search.md)
