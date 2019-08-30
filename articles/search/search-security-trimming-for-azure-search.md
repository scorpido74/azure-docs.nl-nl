---
title: Beveiligings filters voor het verkleinen van resultaten-Azure Search
description: Toegangs beheer voor Azure Search inhoud met behulp van beveiligings filters en gebruikers identiteiten.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 05/02/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
ms.custom: seodec2018
ms.openlocfilehash: 4d1ffa5b29a56d32a4f6a8ccf40f5bafd27795e6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186485"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Beveiligings filters voor het verkleinen van de resultaten in Azure Search

U kunt beveiligings filters toep assen om Zoek resultaten in Azure Search te knippen op basis van de identiteit van de gebruiker. Deze Zoek ervaring vereist meestal het vergelijken van de identiteit van degene die de zoek opdracht vraagt naar een veld met de principes die machtigingen hebben voor het document. Wanneer een overeenkomst wordt gevonden, heeft de gebruiker of principal (zoals een groep of rol) toegang tot dat document.

Een manier om beveiligings filtering te waarborgen, is een gecompliceerde schei ding van gelijkheids `Id eq 'id1' or Id eq 'id2'`expressies: bijvoorbeeld, enzovoort. Deze benadering is fout gevoelig en moeilijk te onderhouden, en in gevallen waarin de lijst honderden of duizenden waarden bevat, wordt de reactie tijd van query's in veel seconden vertraagd. 

Een eenvoudiger en sneller benadering is de `search.in` functie. Als u gebruikt `search.in(Id, 'id1, id2, ...')` in plaats van een gelijkheids expressie, kunt u een reactie tijd van sub seconden verwachten.

In dit artikel wordt beschreven hoe u beveiligings filtering kunt uitvoeren met behulp van de volgende stappen:
> [!div class="checklist"]
> * Een veld maken dat de principal-id's bevat 
> * Bestaande documenten pushen of bijwerken met de relevante Principal-id's
> * Een zoek opdracht uitgeven met `search.in``filter`

>[!NOTE]
> Het proces voor het ophalen van de principal-id's wordt niet behandeld in dit document. U ontvangt dit van uw identiteits service provider.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [Azure Search-service](https://docs.microsoft.com/azure/search/search-create-service-portal)en [Azure search index](https://docs.microsoft.com/azure/search/search-create-index-portal)hebt.  

## <a name="create-security-field"></a>Beveiligings veld maken

Uw documenten moeten een veld bevatten waarin wordt aangegeven welke groepen toegang hebben. Deze informatie wordt het filter criterium waarmee documenten worden geselecteerd of afgewezen in de resultatenset die wordt geretourneerd naar de verlener.
We gaan ervan uit dat we een index van beveiligde bestanden hebben en dat elk bestand toegankelijk is voor een andere set gebruikers.
1. Voeg een `group_ids` veld toe (u kunt hier een wille keurige naam kiezen) als een. `Collection(Edm.String)` Zorg ervoor dat het veld een `filterable` kenmerk heeft `true` ingesteld zodat de zoek resultaten worden gefilterd op basis van de toegang die de gebruiker heeft. Als u bijvoorbeeld het `group_ids` veld instelt op `["group_id1, group_id2"]` voor het document met `file_name` ' secured_file_b ', hebben alleen gebruikers die deel uitmaken van groeps-id's ' group_id1 ' of ' group_id2 ' Lees toegang tot het bestand.
   Zorg ervoor dat het kenmerk `retrievable` van het veld is `false` ingesteld op zodat het niet wordt geretourneerd als onderdeel van de zoek opdracht.
2. U kunt `file_id` ook `file_name` velden toevoegen voor dit voor beeld.  

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

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Gegevens naar uw index pushen met behulp van de REST API
  
Een HTTP POST-aanvraag verzenden naar het URL-eind punt van uw index. De hoofd tekst van de HTTP-aanvraag is een JSON-object dat de documenten bevat die moeten worden toegevoegd:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2019-05-06  
Content-Type: application/json
api-key: [admin key]
```

Geef in de hoofd tekst van de aanvraag de inhoud van uw documenten op:

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

Als u een bestaand document wilt bijwerken met de lijst met groepen, kunt u de `merge` actie of `mergeOrUpload` gebruiken:

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

Voor gedetailleerde informatie over het toevoegen of bijwerken van documenten, kunt u [documenten bewerken](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)lezen.
   
## <a name="apply-the-security-filter"></a>Het beveiligings filter Toep assen

Als u documenten wilt knippen op basis `group_ids` van toegang, moet u een zoek opdracht geven met `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` een filter, waarbij ' group_id1, group_id2,... ' zijn de groepen waartoe de uitgever van de zoek aanvraag behoort.
Dit filter komt overeen met alle documenten waarvoor `group_ids` het veld een van de opgegeven id's bevat.
Voor volledige informatie over het zoeken van documenten met Azure Search, kunt u [Zoek documenten](https://docs.microsoft.com/rest/api/searchservice/search-documents)lezen.
Houd er rekening mee dat in dit voor beeld wordt uitgelegd hoe u met een POST-aanvraag naar documenten zoekt.

De HTTP POST-aanvraag uitgeven:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2019-05-06
Content-Type: application/json  
api-key: [admin or query key]
```

Geef het filter op in de hoofd tekst van de aanvraag:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

U moet de documenten weer `group_ids` geven met ' group_id1 ' of ' group_id2 '. Met andere woorden, u krijgt de documenten waarmee de uitgever van de aanvraag Lees toegang heeft.

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

Zo kunt u resultaten filteren op basis van de gebruikers-id en `search.in()` Azure Search functie. U kunt deze functie gebruiken om te voldoen aan principe-id's waarmee de gebruiker die de aanvraag heeft ingediend, overeenkomt met de principal-id's die zijn gekoppeld aan elk doel document. Wanneer een zoek opdracht wordt verwerkt, filtert de functie de `search.in` Zoek resultaten op waarvoor geen van de principals van de gebruiker lees toegang heeft. De principal-id's kunnen dingen vertegenwoordigen, zoals beveiligings groepen, rollen of zelfs de eigen identiteit van de gebruiker.
 
## <a name="see-also"></a>Zie ook

+ [Active Directory toegangs beheer op basis van een id met behulp van Azure Search filters](search-security-trimming-for-azure-search-with-aad.md)
+ [Filters in Azure Search](search-filters.md)
+ [Gegevens beveiliging en toegangs beheer in Azure Search bewerkingen](search-security-overview.md)