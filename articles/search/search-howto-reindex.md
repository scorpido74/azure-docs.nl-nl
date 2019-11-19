---
title: Een zoek index opnieuw samen stellen
titleSuffix: Azure Cognitive Search
description: Voeg nieuwe elementen toe, werk bestaande elementen of documenten bij of verwijder verouderde documenten in een volledig opnieuw opgebouwde of gedeeltelijke incrementele indexering om een Azure Cognitive Search-index te vernieuwen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b14c153f52e0427e289afeccdfd22d6510e4ace1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112965"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Een index opnieuw samen stellen in azure Cognitive Search

In dit artikel wordt uitgelegd hoe u een Azure Cognitive Search-index opnieuw bouwt, de omstandigheden waaronder opnieuw opgebouwd is vereist, en aanbevelingen voor het beperken van de impact van het opnieuw opbouwen van doorlopende query aanvragen.

Een *Rebuild* verwijst naar het weghalen en opnieuw maken van de fysieke gegevens structuren die zijn gekoppeld aan een index, inclusief alle op velden gebaseerde omgekeerde indexen. In azure Cognitive Search kunt u geen afzonderlijke velden meer verwijderen en opnieuw maken. Als u een index opnieuw wilt samen stellen, moet u alle veld opslag verwijderen, opnieuw maken op basis van een bestaand of gereviseerd index schema en vervolgens opnieuw gevuld met gegevens die naar de index zijn gepusht of uit externe bronnen worden gehaald. Het is gebruikelijk om indexen tijdens de ontwikkeling opnieuw samen te stellen, maar mogelijk moet u ook een index op productie niveau opnieuw samen stellen voor structurele wijzigingen, zoals het toevoegen van complexe typen of het toevoegen van velden aan Voorst Ellen.

In tegens telling tot het opnieuw opbouwen waarbij een index offline wordt gehaald, wordt het *vernieuwen van gegevens* uitgevoerd als achtergrond taak. U kunt documenten toevoegen, verwijderen en vervangen door minimale onderbrekingen voor het uitvoeren van query's op werk belastingen, hoewel query's doorgaans langer duren. Zie [documenten toevoegen, bijwerken of verwijderen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)voor meer informatie over het bijwerken van index inhoud.

## <a name="rebuild-conditions"></a>Voor waarden opnieuw samen stellen

| Voorwaarde | Beschrijving |
|-----------|-------------|
| Een veld definitie wijzigen | Voor het wijzigen van een veld naam, gegevens type of specifieke [index kenmerken](https://docs.microsoft.com/rest/api/searchservice/create-index) (doorzoekbaar, filterbaar, sorteerbaar, facetable) is een volledige heropbouw vereist. |
| Een analyse functie toewijzen aan een veld | [Analyse](search-analyzers.md) functies worden gedefinieerd in een index en vervolgens toegewezen aan velden. U kunt op elk gewenst moment een nieuwe analyse definitie aan een index toevoegen, maar u kunt alleen een analyse *toewijzing toewijzen* wanneer het veld wordt gemaakt. Dit geldt voor de eigenschappen **Analyzer** en **indexAnalyzer** . De eigenschap **searchAnalyzer** is een uitzonde ring (u kunt deze eigenschap toewijzen aan een bestaand veld). |
| Een analyse definitie in een index bijwerken of verwijderen | U kunt een bestaande analyse configuratie (Analyzer, tokenizer, token filter of char-filter) in de index niet verwijderen of wijzigen, tenzij u de volledige index opnieuw opbouwt. |
| Een veld aan een suggestie toevoegen | Als er al een veld bestaat en u dit wilt toevoegen aan de construct [suggesties](index-add-suggesters.md) , moet u de index opnieuw samen stellen. |
| Een veld verwijderen | Als u alle traceringen van een veld fysiek wilt verwijderen, moet u de index opnieuw samen stellen. Wanneer een onmiddellijke heropbouw niet praktisch is, kunt u de toepassings code wijzigen om de toegang tot het veld ' verwijderd ' uit te scha kelen. De definitie en inhoud van het veld blijven fysiek aanwezig in de index tot de volgende keer opnieuw wordt opgebouwd wanneer u een schema toepast dat het betreffende veld weglaat. |
| Lagen wisselen | Als u meer capaciteit nodig hebt, is er geen in-place upgrade in de Azure Portal. Er moet een nieuwe service worden gemaakt en indexen moeten op basis van de nieuwe service volledig worden gebouwd. Om dit proces te automatiseren, kunt u de voorbeeld code **index-Backup-Restore** gebruiken in dit [Azure Cognitive Search .net-voor beeld opslag plaats](https://github.com/Azure-Samples/azure-search-dotnet-samples). Met deze app wordt een back-up gemaakt van uw index naar een reeks JSON-bestanden en wordt de index opnieuw in een zoek service opgegeven die u opgeeft.|

Andere wijzigingen kunnen worden aangebracht zonder dat dit van invloed is op bestaande fysieke structuren. In het bijzonder vereist de volgende wijzigingen *geen* index opnieuw samen stellen:

+ Een nieuw veld toevoegen
+ Het kenmerk **ophalenable** instellen voor een bestaand veld
+ Een **searchAnalyzer** instellen voor een bestaand veld
+ Een nieuwe analyse definitie toevoegen in een index
+ Score profielen toevoegen, bijwerken of verwijderen
+ CORS-instellingen toevoegen, bijwerken of verwijderen
+ SynonymMaps toevoegen, bijwerken of verwijderen

Wanneer u een nieuw veld toevoegt, krijgen bestaande geïndexeerde documenten een null-waarde voor het nieuwe veld. Bij een toekomstige gegevens vernieuwing worden waarden uit externe bron gegevens vervangen door de nullen die zijn toegevoegd door Azure Cognitive Search. Zie [documenten toevoegen, bijwerken of verwijderen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)voor meer informatie over het bijwerken van index inhoud.

## <a name="partial-indexing"></a>Gedeeltelijke indexering

In azure Cognitive Search kunt u indexering niet per veld beheren, en kunt u ervoor kiezen om specifieke velden te verwijderen of opnieuw te maken. Op dezelfde manier is er geen ingebouwd mechanisme voor het [indexeren van documenten op basis van criteria](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Alle vereisten voor het op criteria gebaseerde indexering moeten worden voldaan door aangepaste code.

Wat u gemakkelijk kunt doen, is echter het *vernieuwen van documenten* in een index. Voor veel zoek oplossingen zijn externe bron gegevens vluchtig en is synchronisatie tussen bron gegevens en een zoek index een veelvoorkomende procedure. In code roept u de bewerking voor het [toevoegen, bijwerken of verwijderen van documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) of de [.net-equivalent](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) voor het bijwerken van index inhoud aan of om waarden voor een nieuw veld toe te voegen.

## <a name="partial-indexing-with-indexers"></a>Gedeeltelijke indexering met Indexeer functies

[Indexeer functies](search-indexer-overview.md) vereenvoudigen de taak voor gegevens vernieuwing. Een Indexeer functie kan slechts één tabel of weer gave in de externe gegevens bron indexeren. Als u meerdere tabellen wilt indexeren, is het eenvoudig om een weer gave te maken die tabellen en projecten verbindt met de kolommen die u wilt indexeren. 

Wanneer u Indexeer functies gebruikt voor het verkennen van externe gegevens bronnen, controleert u op een kolom met een hoog water merk in de bron gegevens. Als er een bestaat, kunt u deze gebruiken voor de detectie van incrementele wijzigingen door alleen de rijen met nieuwe of gewijzigde inhoud op te halen. Voor [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)wordt een `lastModified` veld gebruikt. In [Azure Table Storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)fungeert `timestamp` hetzelfde doel. Op dezelfde manier hebben zowel [Azure SQL database Indexeer functie](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) als [Azure Cosmos DB Indexeer functie](search-howto-index-cosmosdb.md#indexing-changed-documents) velden voor het markeren van bijgewerkte rijen. 

Zie [overzicht van Indexeer functie](search-indexer-overview.md) en [Reset Indexeer functie rest API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)voor meer informatie over Indexeer functies.

## <a name="how-to-rebuild-an-index"></a>Een index opnieuw samen stellen

Plan regel matig, volledig opnieuw samen stellen tijdens actieve ontwikkeling, wanneer index schema's een stroom status hebben. Voor toepassingen die al in productie zijn, raden we u aan een nieuwe index te maken die naast elkaar een bestaande index uitvoert om te voor komen dat een query wordt uitgevoerd op downtime.

Machtigingen voor lezen/schrijven op service niveau zijn vereist voor index-updates. 

U kunt een index niet opnieuw samen stellen in de portal. Via een programma kunt u de [Update-Index rest API](https://docs.microsoft.com/rest/api/searchservice/update-index) of [equivalente .net-api's](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) aanroepen voor een volledige heropbouw. Een aanvraag voor een update-index is identiek aan het maken van een [index rest API](https://docs.microsoft.com/rest/api/searchservice/create-index), maar heeft een andere context.

De volgende werk stroom is gericht op de REST API, maar is ook van toepassing op de .NET SDK.

1. Als u een index naam opnieuw gebruikt, [verwijdert u de bestaande index](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Alle query's die met de index worden gericht, worden onmiddellijk verwijderd. Het verwijderen van een index is onomkeerbaar, en het vernietigen van fysieke opslag voor de verzameling velden en andere constructies. Zorg ervoor dat u de gevolgen van het verwijderen van een index duidelijk maakt voordat u deze verwijdert. 

2. Formuleer een [update-index](https://docs.microsoft.com/rest/api/searchservice/update-index) aanvraag met uw service-eind punt, API-sleutel en een [beheer sleutel](https://docs.microsoft.com/azure/search/search-security-api-keys). Een beheerders sleutel is vereist voor schrijf bewerkingen.

3. Geef in de hoofd tekst van de aanvraag een index schema op met de gewijzigde of gewijzigde veld definities. De aanvraag tekst bevat het index schema en constructies voor Score profielen, analyse functies, suggesties en CORS-opties. Schema vereisten worden beschreven in [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Verzend een aanvraag voor een [update-index](https://docs.microsoft.com/rest/api/searchservice/update-index) om de fysieke expressie van de index op Azure Cognitive Search opnieuw op te bouwen. 

5. [Laad de index met documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) uit een externe bron.

Wanneer u de index maakt, wordt er een fysieke opslag toegewezen voor elk veld in het index schema, met een omgekeerde index die is gemaakt voor elk doorzoekbaar veld. Velden die niet kunnen worden doorzocht, kunnen worden gebruikt in filters of expressies, maar hebben geen omgekeerde indexen en zijn niet in volledige tekst of op fuzzy doorzoekbaar. Bij een opnieuw opgebouwde index worden deze omgekeerde indexen verwijderd en opnieuw gemaakt op basis van het index schema dat u opgeeft.

Wanneer u de index laadt, wordt de omgekeerde index van elk veld gevuld met alle unieke, tokenve woorden uit elk document, met een kaart aan de bijbehorende document-Id's. Wanneer u bijvoorbeeld een gegevensset van een Hotels indexeert, kan een omgekeerde index die is gemaakt voor een veld woon plaats voor waarden bevatten voor Seattle, Rotterdam, enzovoort. Documenten met Seattle of Rotterdam in het veld plaats zouden hun document-ID naast de term hebben vermeld. Bij een [toevoeg-, bijwerk-of verwijder](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) bewerking worden de lijst met voor waarden en document-id's dienovereenkomstig bijgewerkt.

> [!NOTE]
> Als u strenge SLA-vereisten hebt, kunt u overwegen om een nieuwe service specifiek voor dit werk in te richten, met ontwikkelen en indexeren in volledige isolatie vanuit een productie-index. Een afzonderlijke service wordt uitgevoerd op zijn eigen hardware, waardoor de kans op bron conflicten wordt geëlimineerd. Wanneer de ontwikkeling is voltooid, kunt u de nieuwe index op locatie laten staan, query's omleiden naar het nieuwe eind punt en de index, of u kunt de programma code uitvoeren om een gereviseerde index op uw oorspronkelijke Azure Cognitive Search-service te publiceren. Er is momenteel geen mechanisme voor het verplaatsen van een gebruiks klare index naar een andere service.

## <a name="view-updates"></a>Updates weer geven

Zodra het eerste document is geladen, kunt u beginnen met het uitvoeren van een query op een index. Als u de ID van een document kent, retourneert het [opzoek document rest API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) het specifieke document. Voor uitgebreid testen moet u wachten tot de index volledig is geladen en vervolgens query's gebruiken om de context te controleren die u verwacht te zien.

## <a name="see-also"></a>Zie ook

+ [Overzicht van de indexeerfunctie](search-indexer-overview.md)
+ [Grote gegevens sets op schaal indexeren](search-howto-large-index.md)
+ [Indexeren in de portal](search-import-data-portal.md)
+ [Indexeer functie Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeerfunctie](search-howto-index-cosmosdb.md)
+ [Indexeerfunctie voor Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexeerfunctie voor Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Beveiliging in azure Cognitive Search](search-security-overview.md)