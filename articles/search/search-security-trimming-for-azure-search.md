---
title: Beveiligingsfilters voor het bijsnijden van resultaten
titleSuffix: Azure Cognitive Search
description: Toegangsbeheer voor Azure Cognitive Search-inhoud met behulp van beveiligingsfilters en gebruikersidentiteiten.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24f168f68a60ebb0408b7f1c367039ea5caea6d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794268"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Beveiligingsfilters voor het bijsnijden van resultaten in Azure Cognitive Search

U beveiligingsfilters toepassen om zoekresultaten bij te snijden in Azure Cognitive Search op basis van de identiteit van de gebruiker. Deze zoekervaring vereist over het algemeen het vergelijken van de identiteit van degene die de zoekopdracht aanvraagt met een veld met de beginselen die machtigingen voor het document hebben. Wanneer een overeenkomst wordt gevonden, heeft de gebruiker of opdrachtgever (zoals een groep of rol) toegang tot dat document.

Een manier om security filtering te bereiken is door middel `Id eq 'id1' or Id eq 'id2'`van een ingewikkelde disjunctie van gelijkheid uitdrukkingen: bijvoorbeeld, , enzovoort. Deze aanpak is foutgevoelig, moeilijk te onderhouden en in gevallen waarin de lijst honderden of duizenden waarden bevat, vertraagt de reactietijd van query's met vele seconden. 

Een eenvoudigere en snellere `search.in` aanpak is door de functie. Als u `search.in(Id, 'id1, id2, ...')` in plaats van een gelijkheidsuitdrukking gebruikt, u reactietijden van subseconden verwachten.

In dit artikel ziet u hoe u beveiligingsfilters uitvoeren met behulp van de volgende stappen:
> [!div class="checklist"]
> * Een veld maken dat de hoofd-id's bevat 
> * Bestaande documenten pushen of bijwerken met de relevante hoofd-id's
> * Een zoekaanvraag `search.in` afgeven met`filter`

>[!NOTE]
> Het proces van het ophalen van de hoofd-id's wordt niet behandeld in dit document. U moet het krijgen van uw identity service provider.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een [Azure-abonnement,](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) [Azure Cognitive Search-service](https://docs.microsoft.com/azure/search/search-create-service-portal)en [Azure Cognitive Search Index](https://docs.microsoft.com/azure/search/search-create-index-portal)hebt.  

## <a name="create-security-field"></a>Beveiligingsveld maken

Uw documenten moeten een veld bevatten waarin wordt aangegeven welke groepen toegang hebben. Deze informatie wordt de filtercriteria waartegen documenten worden geselecteerd of afgewezen uit de resultatenset die aan de uitgever wordt geretourneerd.
Laten we aannemen dat we een index van beveiligde bestanden hebben en dat elk bestand toegankelijk is voor een andere groep gebruikers.
1. Voeg `group_ids` veld (u hier een `Collection(Edm.String)`naam kiezen) als een. Zorg ervoor dat `filterable` het veld `true` een attribuut heeft ingesteld op zodat zoekresultaten worden gefilterd op basis van de toegang die de gebruiker heeft. Als u bijvoorbeeld het `group_ids` veld `["group_id1, group_id2"]` instelt `file_name` op het document met 'secured_file_b', hebben alleen gebruikers die tot groeps-id's behoren "group_id1" of "group_id2" de toegang tot het bestand gelezen.
   Zorg ervoor dat `retrievable` het kenmerk `false` van het veld is ingesteld op, zodat het niet wordt geretourneerd als onderdeel van de zoekaanvraag.
2. Voeg `file_id` ook `file_name` toe en velden omwille van dit voorbeeld.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Gegevens in uw index duwen met de REST API
  
Geef een HTTP POST-verzoek uit aan het URL-eindpunt van uw index. De hoofdtekst van de HTTP-aanvraag is een JSON-object dat de toe te voegen documenten bevat:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

Geef in de aanvraaginstantie de inhoud van uw documenten op:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Als u een bestaand document moet bijwerken met de `merge` lijst `mergeOrUpload` met groepen, u de actie of actie gebruiken:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Voor meer informatie over het toevoegen of bijwerken van documenten u [Documenten bewerken](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)lezen.
   
## <a name="apply-the-security-filter"></a>Het beveiligingsfilter toepassen

Als u documenten wilt `group_ids` bijsnijden op basis van `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` toegang, moet u een zoekopdracht met een filter uitvoeren, waarbij 'group_id1, group_id2,...' zijn de groepen waartoe de uitgever van de zoekaanvraag behoort.
Dit filter komt overeen `group_ids` met alle documenten waarvoor het veld een van de opgegeven id's bevat.
Voor meer informatie over het zoeken naar documenten met Azure Cognitive Search u [Zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents)lezen.
In dit voorbeeld wordt uitgelegd hoe u documenten doorzoeken met een POST-verzoek.

Geef het HTTP POST-verzoek uit:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Geef het filter op in de aanvraaginstantie:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

U moet de documenten `group_ids` terug waar bevat "group_id1" of "group_id2". Met andere woorden, u krijgt de documenten waartoe de aanvraaguitgever toegang heeft.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Conclusie

Zo u resultaten filteren op basis van `search.in()` gebruikersidentiteit en Azure Cognitive Search-functie. U deze functie gebruiken om in principe id's door te geven aan de verzoekende gebruiker om te matchen met hoofd-id's die aan elk doeldocument zijn gekoppeld. Wanneer een zoekverzoek wordt `search.in` behandeld, filtert de functie zoekresultaten waarvoor geen van de opdrachtgevers van de gebruiker leestoegang heeft. De belangrijkste id's kunnen zaken als beveiligingsgroepen, rollen of zelfs de eigen identiteit van de gebruiker weergeven.
 
## <a name="see-also"></a>Zie ook

+ [Active Directory-toegangsbeheer op basis van identiteit met Azure Cognitive Search-filters](search-security-trimming-for-azure-search-with-aad.md)
+ [Filters in Azure Cognitive Search](search-filters.md)
+ [Gegevensbeveiliging en toegangscontrole in Azure Cognitive Search-bewerkingen](search-security-overview.md)