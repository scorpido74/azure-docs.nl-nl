---
title: Een zoekindex opnieuw opbouwen
titleSuffix: Azure Cognitive Search
description: Voeg nieuwe elementen toe, werk bestaande elementen of documenten bij of verwijder verouderde documenten in een volledige reconstructie of gedeeltelijke indexering om een Azure Cognitive Search-index te vernieuwen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498382"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Een index opnieuw opbouwen in Azure Cognitive Search

In dit artikel wordt uitgelegd hoe u een Azure Cognitive Search-index herbouwen, de omstandigheden waaronder heropbouw vereist is en aanbevelingen voor het beperken van de impact van het opnieuw opbouwen van lopende queryaanvragen.

Een *reconstructie* verwijst naar het laten vallen en opnieuw maken van de fysieke gegevensstructuren die zijn gekoppeld aan een index, inclusief alle omgekeerde indexen in het veld. In Azure Cognitive Search u afzonderlijke velden niet laten vallen en opnieuw maken. Als u een index wilt herstellen, moet alle veldopslag worden verwijderd, opnieuw worden gemaakt op basis van een bestaand of herzien indexschema en vervolgens opnieuw worden gevuld met gegevens die naar de index worden geschoven of uit externe bronnen worden gehaald. 

Het is gebruikelijk om indexen opnieuw op te bouwen tijdens de ontwikkeling, maar het kan ook nodig zijn om een index op productieniveau opnieuw op te bouwen om structurele veranderingen op te vangen, zoals het toevoegen van complexe typen of het toevoegen van velden aan suggesties.

## <a name="rebuild-conditions"></a>Voorwaarden opnieuw opbouwen

Een index laten vallen en opnieuw maken als een van de volgende voorwaarden waar is. 

| Voorwaarde | Beschrijving |
|-----------|-------------|
| Een velddefinitie wijzigen | Voor het herzien van een veldnaam, gegevenstype of specifieke [indexkenmerken](https://docs.microsoft.com/rest/api/searchservice/create-index) (doorzoekbaar, filterbaar, sorteerbaar, facetable) is een volledige reconstructie vereist. |
| Een analyzer toewijzen aan een veld | [Analysanten](search-analyzers.md) worden gedefinieerd in een index en vervolgens toegewezen aan velden. U op elk gewenst moment een nieuwe definitie van de analysator aan een index toevoegen, maar u alleen een analyzer *toewijzen* wanneer het veld wordt gemaakt. Dit geldt voor zowel de **eigenschappen analyzer** als **indexAnalyzer.** De eigenschap **searchAnalyzer** is een uitzondering (u deze eigenschap toewijzen aan een bestaand veld). |
| Een definitie van een analyzer in een index bijwerken of verwijderen | U een bestaande analyzerconfiguratie (analyzer, tokenizer, tokenfilter of charfilter) in de index niet verwijderen of wijzigen, tenzij u de volledige index opnieuw opbouwt. |
| Een veld toevoegen aan een suggestie | Als er al een veld bestaat en u het wilt toevoegen aan een [construct van Suggesters,](index-add-suggesters.md) moet u de index opnieuw opbouwen. |
| Een veld verwijderen | Als u alle sporen van een veld fysiek wilt verwijderen, moet u de index opnieuw opbouwen. Wanneer een onmiddellijke herbouw niet praktisch is, u de toepassingscode wijzigen om de toegang tot het veld 'verwijderd' uit te schakelen of de [parameter $select query](search-query-odata-select.md) gebruiken om te kiezen welke velden in de resultaatset worden weergegeven. Fysiek blijven de velddefinitie en -inhoud in de index tot de volgende reconstructie, wanneer u een schema toepast dat het betreffende veld weglaat. |
| Van laag wisselen | Als u meer capaciteit nodig hebt, is er geen upgrade in de Azure-portal. Er moet een nieuwe service worden gemaakt en indexen moeten vanaf nul worden opgebouwd op de nieuwe service. Om dit proces te automatiseren, u de **voorbeeldcode voor index-back-up-restore** gebruiken in deze [azure cognitive search .NET-voorbeeldrepo](https://github.com/Azure-Samples/azure-search-dotnet-samples). Deze app maakt een back-up van uw index naar een reeks JSON-bestanden en maakt de index opnieuw in een door u opgegeven zoekservice.|

## <a name="update-conditions"></a>Updatevoorwaarden

Vele andere wijzigingen kunnen worden aangebracht zonder dat dit gevolgen heeft voor bestaande fysieke structuren. In het bijzonder vereisen de volgende wijzigingen *geen* indexreconstructie. Voor deze wijzigingen u [een indexdefinitie bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-index) met uw wijzigingen.

+ Een nieuw veld toevoegen
+ Het **kenmerk opvraagbaar** instellen op een bestaand veld
+ Een **searchAnalyzer** instellen op een bestaand veld
+ Een nieuwe definitie van analysator toevoegen aan een index
+ Scoreprofielen toevoegen, bijwerken of verwijderen
+ CORS-instellingen toevoegen, bijwerken of verwijderen
+ SynoniemMaps toevoegen, bijwerken of verwijderen

Wanneer u een nieuw veld toevoegt, krijgen bestaande geïndexeerde documenten een null-waarde voor het nieuwe veld. Bij een toekomstige gegevensvernieuwing vervangen waarden uit externe brongegevens de nullen die zijn toegevoegd door Azure Cognitive Search. Zie [Documenten toevoegen, Bijwerken of Verwijderen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)voor meer informatie over het bijwerken van indexinhoud.

## <a name="how-to-rebuild-an-index"></a>Een index opnieuw opbouwen

Tijdens de ontwikkeling verandert het indexschema regelmatig. U dit plannen door indexen te maken die snel kunnen worden verwijderd, opnieuw gemaakt en opnieuw geladen met een kleine representatieve gegevensset.

Voor toepassingen die al in productie zijn, raden we u aan een nieuwe index te maken die naast een bestaande index wordt uitgevoerd om uitvaltijd van query's te voorkomen. Uw toepassingscode biedt omleiding naar de nieuwe index.

Indexering wordt niet op de achtergrond uitgevoerd en de service zal de extra indexering in evenwicht brengen met lopende query's. Tijdens het indexeren u [queryaanvragen](search-monitor-queries.md) in de portal controleren om ervoor te zorgen dat query's tijdig worden voltooid.

1. Bepaal of een herbouw vereist is. Als u alleen velden toevoegt of een deel van de index wijzigt dat geen verband houdt met velden, u de definitie mogelijk eenvoudig bijwerken zonder [de definitie](https://docs.microsoft.com/rest/api/searchservice/update-index) te verwijderen, opnieuw te maken en volledig opnieuw te laden.

1. [Krijg een indexdefinitie](https://docs.microsoft.com/rest/api/searchservice/get-index) voor het geval u deze nodig hebt voor toekomstige referentie.

1. [Laat de bestaande index](https://docs.microsoft.com/rest/api/searchservice/delete-index)vallen, ervan uitgaande dat u geen nieuwe en oude indexen naast elkaar uitvoert. 

   Alle query's die op die index zijn gericht, worden onmiddellijk verwijderd. Vergeet niet dat het verwijderen van een index onomkeerbaar is, het vernietigen van fysieke opslag voor de velden verzameling en andere constructies. Pauzeer om na te denken over de implicaties voordat u het laat vallen. 

1. [Maak een herziene index,](https://docs.microsoft.com/rest/api/searchservice/create-index)waarbij de hoofdtekst van de aanvraag gewijzigde of gewijzigde velddefinities bevat.

1. [De index laden met documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) uit een externe bron.

Wanneer u de index maakt, wordt fysieke opslag toegewezen voor elk veld in het indexschema, met een omgekeerde index die is gemaakt voor elk doorzoekbaar veld. Velden die niet doorzoekbaar zijn, kunnen worden gebruikt in filters of expressies, maar hebben geen omgekeerde indexen en zijn niet volledig of vaag doorzoekbaar. Bij het opnieuw opbouwen van de index worden deze omgekeerde indexen verwijderd en opnieuw gemaakt op basis van het indexschema dat u opgeeft.

Wanneer u de index laadt, wordt de omgekeerde index van elk veld gevuld met alle unieke, tokenized woorden van elk document, met een kaart voor bijbehorende document-id's. Wanneer u bijvoorbeeld een hotelgegevensset indexeert, kan een omgekeerde index die is gemaakt voor een veld Stad termen bevatten voor Seattle, Portland, enzovoort. Documenten die Seattle of Portland in het veld Stad bevatten, hebben hun document-id naast de term vermeld. Bij elke [bewerking Toevoegen, Bijwerken of Verwijderen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) worden de termen en de document-id-lijst dienovereenkomstig bijgewerkt.

> [!NOTE]
> Als u strenge SLA-vereisten hebt, u overwegen om een nieuwe service specifiek voor dit werk in te richten, waarbij ontwikkeling en indexering volledig geïsoleerd van een productie-index plaatsvinden. Een aparte service draait op zijn eigen hardware, waardoor elke mogelijkheid van resource twist wordt geëlimineerd. Wanneer de ontwikkeling is voltooid, zou u de nieuwe index op zijn plaats laten, query's omleiden naar het nieuwe eindpunt en de index, of u zou voltooide code uitvoeren om een herziene index te publiceren op uw oorspronkelijke Azure Cognitive Search-service. Er is momenteel geen mechanisme voor het verplaatsen van een kant-en-klare index naar een andere service.

## <a name="check-for-updates"></a>Naar updates zoeken

U beginnen met het opvragen van een index zodra het eerste document is geladen. Als u de id van een document kent, retourneert de [API Voor DocumentREST](https://docs.microsoft.com/rest/api/searchservice/lookup-document) het specifieke document. Voor bredere tests moet u wachten tot de index volledig is geladen en vervolgens query's gebruiken om de context te verifiëren die u verwacht te zien.

U [Search Explorer](search-explorer.md) of een webtesttool zoals [Postman](search-get-started-postman.md) gebruiken om te controleren op bijgewerkte inhoud.

Als u een veld hebt toegevoegd of een andere naam hebt genoemd, gebruikt u [$select](search-query-odata-select.md) om dat veld terug te sturen:`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Zie ook

+ [Overzicht van de indexeerfunctie](search-indexer-overview.md)
+ [Grote gegevenssets op schaal indexeren](search-howto-large-index.md)
+ [Indexering in de portal](search-import-data-portal.md)
+ [Azure SQL Database-indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexeerfunctie voor Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexeerfunctie voor Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexeerfunctie voor Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Beveiliging in Azure Cognitive Search](search-security-overview.md)