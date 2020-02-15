---
title: Een zoek index opnieuw samen stellen
titleSuffix: Azure Cognitive Search
description: Voeg nieuwe elementen toe, werk bestaande elementen of documenten bij of verwijder verouderde documenten in een volledig opnieuw opgebouwde of gedeeltelijke indexering om een Azure Cognitive Search-index te vernieuwen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 8cebe02ebc638ba62fceec80dff2c6724ccf92c8
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212299"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Een index opnieuw samen stellen in azure Cognitive Search

In dit artikel wordt uitgelegd hoe u een Azure Cognitive Search-index opnieuw bouwt, de omstandigheden waaronder opnieuw opgebouwd is vereist, en aanbevelingen voor het beperken van de impact van het opnieuw opbouwen van doorlopende query aanvragen.

Een *Rebuild* verwijst naar het weghalen en opnieuw maken van de fysieke gegevens structuren die zijn gekoppeld aan een index, inclusief alle op velden gebaseerde omgekeerde indexen. In azure Cognitive Search kunt u geen afzonderlijke velden meer verwijderen en opnieuw maken. Als u een index opnieuw wilt samen stellen, moet u alle veld opslag verwijderen, opnieuw maken op basis van een bestaand of gereviseerd index schema en vervolgens opnieuw gevuld met gegevens die naar de index zijn gepusht of uit externe bronnen worden gehaald. 

Het is gebruikelijk om indexen tijdens de ontwikkeling opnieuw samen te stellen, maar mogelijk moet u ook een index op productie niveau opnieuw samen stellen voor structurele wijzigingen, zoals het toevoegen van complexe typen of het toevoegen van velden aan Voorst Ellen.

## <a name="rebuild-conditions"></a>Voor waarden opnieuw samen stellen

Verwijder een index en maak deze opnieuw als aan een van de volgende voor waarden wordt voldaan. 

| Voorwaarde | Beschrijving |
|-----------|-------------|
| Een veld definitie wijzigen | Voor het wijzigen van een veld naam, gegevens type of specifieke [index kenmerken](https://docs.microsoft.com/rest/api/searchservice/create-index) (doorzoekbaar, filterbaar, sorteerbaar, facetable) is een volledige heropbouw vereist. |
| Een analyse functie toewijzen aan een veld | [Analyse](search-analyzers.md) functies worden gedefinieerd in een index en vervolgens toegewezen aan velden. U kunt op elk gewenst moment een nieuwe analyse definitie aan een index toevoegen, maar u kunt alleen een analyse *toewijzing toewijzen* wanneer het veld wordt gemaakt. Dit geldt voor de eigenschappen **Analyzer** en **indexAnalyzer** . De eigenschap **searchAnalyzer** is een uitzonde ring (u kunt deze eigenschap toewijzen aan een bestaand veld). |
| Een analyse definitie in een index bijwerken of verwijderen | U kunt een bestaande analyse configuratie (Analyzer, tokenizer, token filter of char-filter) in de index niet verwijderen of wijzigen, tenzij u de volledige index opnieuw opbouwt. |
| Een veld aan een suggestie toevoegen | Als er al een veld bestaat en u dit wilt toevoegen aan de construct [suggesties](index-add-suggesters.md) , moet u de index opnieuw samen stellen. |
| Een veld verwijderen | Als u alle traceringen van een veld fysiek wilt verwijderen, moet u de index opnieuw samen stellen. Wanneer een onmiddellijke heropbouw niet praktisch is, kunt u de toepassings code wijzigen om de toegang tot het veld ' verwijderd ' uit te scha kelen. De definitie en inhoud van het veld blijven fysiek aanwezig in de index tot de volgende keer opnieuw wordt opgebouwd wanneer u een schema toepast dat het betreffende veld weglaat. |
| Lagen wisselen | Als u meer capaciteit nodig hebt, is er geen in-place upgrade in de Azure Portal. Er moet een nieuwe service worden gemaakt en indexen moeten op basis van de nieuwe service volledig worden gebouwd. Om dit proces te automatiseren, kunt u de voorbeeld code **index-Backup-Restore** gebruiken in dit [Azure Cognitive Search .net-voor beeld opslag plaats](https://github.com/Azure-Samples/azure-search-dotnet-samples). Met deze app wordt een back-up van uw index gemaakt in een reeks JSON-bestanden en wordt de index vervolgens opnieuw in een zoek service weer gegeven die u opgeeft.|

## <a name="update-conditions"></a>Update voorwaarden

Er kunnen veel andere wijzigingen worden aangebracht zonder dat dit van invloed is op bestaande fysieke structuren. In het bijzonder vereist de volgende wijzigingen *geen* index opnieuw samen stellen. Voor deze wijzigingen kunt u [een index definitie bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-index) met uw wijzigingen.

+ Een nieuw veld toevoegen
+ Het kenmerk **ophalenable** instellen voor een bestaand veld
+ Een **searchAnalyzer** instellen voor een bestaand veld
+ Een nieuwe analyse definitie toevoegen in een index
+ Score profielen toevoegen, bijwerken of verwijderen
+ CORS-instellingen toevoegen, bijwerken of verwijderen
+ SynonymMaps toevoegen, bijwerken of verwijderen

Wanneer u een nieuw veld toevoegt, krijgen bestaande geïndexeerde documenten een null-waarde voor het nieuwe veld. Bij een toekomstige gegevens vernieuwing worden waarden uit externe bron gegevens vervangen door de nullen die zijn toegevoegd door Azure Cognitive Search. Zie [documenten toevoegen, bijwerken of verwijderen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)voor meer informatie over het bijwerken van index inhoud.

## <a name="how-to-rebuild-an-index"></a>Een index opnieuw samen stellen

Tijdens de ontwikkeling worden de index schema's regel matig gewijzigd. U kunt deze plannen door indexen te maken die kunnen worden verwijderd, opnieuw gemaakt en snel opnieuw worden geladen met een kleine representatieve gegevensset. 

Voor toepassingen die al in productie zijn, raden we u aan een nieuwe index te maken die naast elkaar een bestaande index uitvoert om te voor komen dat een query wordt uitgevoerd op downtime. De code van uw toepassing biedt omleiding naar de nieuwe index.

1. Bepaal of een heropbouw vereist is. Als u alleen velden toevoegt of een deel van de index wijzigt die niet gerelateerd zijn aan velden, kunt u [de definitie](https://docs.microsoft.com/rest/api/searchservice/update-index) mogelijk gewoon bijwerken zonder dat u deze hoeft te verwijderen, opnieuw te maken en volledig opnieuw te laden.

1. [Haal een index definitie](https://docs.microsoft.com/rest/api/searchservice/get-index) op als u deze nodig hebt voor toekomstig gebruik.

1. [Verwijder de bestaande index](https://docs.microsoft.com/rest/api/searchservice/delete-index), ervan uitgaande dat u geen nieuwe en oude indexen naast elkaar uitvoert. 

   Alle query's die met de index worden gericht, worden onmiddellijk verwijderd. Houd er rekening mee dat het verwijderen van een index ongedaan kan worden gemaakt en dat fysieke opslag wordt vernietigd voor de verzameling velden en andere constructies. Pauzeer om na te denken over de implicaties voordat u deze verwijdert. 

1. [Maak een gereviseerde index](https://docs.microsoft.com/rest/api/searchservice/create-index), waarbij de hoofd tekst van de aanvraag gewijzigde of gewijzigde veld definities bevat.

1. [Laad de index met documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) uit een externe bron.

Wanneer u de index maakt, wordt er een fysieke opslag toegewezen voor elk veld in het index schema, met een omgekeerde index die is gemaakt voor elk doorzoekbaar veld. Velden die niet kunnen worden doorzocht, kunnen worden gebruikt in filters of expressies, maar hebben geen omgekeerde indexen en zijn niet in volledige tekst of op fuzzy doorzoekbaar. Bij een opnieuw opgebouwde index worden deze omgekeerde indexen verwijderd en opnieuw gemaakt op basis van het index schema dat u opgeeft.

Wanneer u de index laadt, wordt de omgekeerde index van elk veld gevuld met alle unieke, tokenve woorden uit elk document, met een kaart aan de bijbehorende document-Id's. Wanneer u bijvoorbeeld een gegevensset van een Hotels indexeert, kan een omgekeerde index die is gemaakt voor een veld woon plaats voor waarden bevatten voor Seattle, Rotterdam, enzovoort. Documenten met Seattle of Rotterdam in het veld plaats zouden hun document-ID naast de term hebben vermeld. Bij een [toevoeg-, bijwerk-of verwijder](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) bewerking worden de lijst met voor waarden en document-id's dienovereenkomstig bijgewerkt.

> [!NOTE]
> Als u strenge SLA-vereisten hebt, kunt u overwegen om een nieuwe service specifiek voor dit werk in te richten, met ontwikkelen en indexeren in volledige isolatie vanuit een productie-index. Een afzonderlijke service wordt uitgevoerd op zijn eigen hardware, waardoor de kans op bron conflicten wordt geëlimineerd. Wanneer de ontwikkeling is voltooid, kunt u de nieuwe index op locatie laten staan, query's omleiden naar het nieuwe eind punt en de index, of u kunt de programma code uitvoeren om een gereviseerde index op uw oorspronkelijke Azure Cognitive Search-service te publiceren. Er is momenteel geen mechanisme voor het verplaatsen van een gebruiks klare index naar een andere service.

## <a name="check-for-updates"></a>Controleren op updates

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