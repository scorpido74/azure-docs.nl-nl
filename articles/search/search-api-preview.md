---
title: Lijst met preview-functies
titleSuffix: Azure Cognitive Search
description: Preview-functies worden uitgebracht zodat klanten feedback kunnen geven over hun ontwerp en hulp programma. Dit artikel bevat een uitgebreide lijst met alle functies die momenteel als preview-versie beschikbaar zijn.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: b0c6672dcc9340e727c36b0bcf03fc8a8b176a3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830125"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Preview-functies in azure Cognitive Search

Dit artikel bevat een uitgebreide lijst met alle functies die in Preview zijn, zodat u kunt bepalen of u ze in productie code moet gebruiken. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Preview-functies die worden overgezet naar algemene Beschik baarheid, worden verwijderd uit deze lijst. Als een functie hieronder niet wordt vermeld, kunt u ervan uitgaan dat deze algemeen beschikbaar is. Zie [service-updates](https://azure.microsoft.com/updates/?product=search) of [Wat is er nieuw](whats-new.md)voor aankondigingen over algemene Beschik baarheid.

|Hulp&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [**featuresMode-para meter**](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/search-documents#featuresmode) | Relevantie (Score) | Uitbrei ding van relevantie score voor het toevoegen van Details: per veld vergelijk bare Score, per veld term frequentie en per veld aantal unieke tokens die overeenkomen. U kunt deze gegevens punten gebruiken in [aangepaste Score oplossingen](https://github.com/Azure-Samples/search-ranking-tutorial). | Voeg deze query parameter toe met behulp van [Zoek documenten (rest)](https://docs.microsoft.com/rest/api/searchservice/search-documents) met API-Version = 2020-06 -30-preview of 2019-05-06-preview. |
| [**Beheerde service-identiteit**](search-howto-managed-identities-data-sources.md) | Indexeer functies, beveiliging| Registreer een zoek service met Azure Active Directory om het een vertrouwde service te maken en gebruik vervolgens RBAC-machtigingen voor Azure-gegevens bronnen om alleen-lezen toegang toe te staan door een Indexeer functie. | Toegang tot deze mogelijkheid bij het gebruik van de portal of het maken van een [gegevens bron (rest)](https://docs.microsoft.com/rest/api/searchservice/create-datasource) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**Sessies voor fout opsporing**](cognitive-search-debug-session.md) | Portal, AI-verrijking (vaardig heden) | Een in-Session kwalificatieset-editor die wordt gebruikt om problemen met een vaardig heden te onderzoeken en op te lossen. Oplossingen die worden toegepast tijdens een foutopsporingssessie, kunnen worden opgeslagen in een vakkennisset in de service. | Alleen Portal, met behulp van koppelingen op de pagina overzicht voor het openen van een foutopsporingssessie. |
| [**Voorlopig verwijderen van systeem eigen BLOB**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | Indexeer functies, Azure-blobs| De Azure Blob Storage Indexeer functie in azure Cognitive Search herkent blobs met een voorlopig verwijderde status en verwijdert het bijbehorende Zoek document tijdens het indexeren. | Voeg deze configuratie-instelling toe met [Create Indexeer functie (rest)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**Zoek vaardigheid aangepaste entiteit**](cognitive-search-skill-custom-entity-lookup.md ) | AI-verrijking (vaardig heden) | Een cognitieve vaardigheid die zoekt naar tekst van een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Aan de hand van deze lijst worden alle documenten met overeenkomende entiteiten gelabeld. De vaardigheid ondersteunt ook het zoeken naar fuzzy overeenkomsten, waarmee overeenkomsten worden gevonden die vergelijkbaar zijn, maar niet helemaal exact. | Raadpleeg deze preview-vaardigheid met de editor van de vaardig heden in de portal of [Maak vaardig heden (rest)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**PERSOONLIJKE detectie vaardigheid**](cognitive-search-skill-pii-detection.md) | AI-verrijking (vaardig heden) | Een cognitieve vaardigheid die wordt gebruikt tijdens de indexering die persoons gegevens ophaalt uit een invoer tekst en u de mogelijkheid biedt om deze op verschillende manieren te maskeren in de tekst. | Raadpleeg deze preview-vaardigheid met de editor van de vaardig heden in de portal of [Maak vaardig heden (rest)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**Incrementele verrijking**](cognitive-search-incremental-indexing-conceptual.md) | Configuratie van de Indexeer functie| Voegt caching toe aan een verrijkings pijplijn, zodat u de bestaande uitvoer opnieuw kunt gebruiken als een gerichte wijziging, zoals een update van een vakkennisset of een ander object, de inhoud niet wijzigt. Caching is alleen van toepassing op verrijkte documenten die worden geproduceerd door een vaardig heden.| Voeg deze configuratie-instelling toe met [Create Indexeer functie (rest)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**Cosmos DB Indexer: MongoDB-API, Gremlin-API, Cassandra-API**](search-howto-index-cosmosdb.md) | Gegevens bron van Indexeer functie | Voor Cosmos DB is SQL API algemeen beschikbaar, maar MongoDB-, Gremlin-en Cassandra-Api's zijn in preview. | [Meld u eerst](https://aka.ms/azure-cognitive-search/indexer-preview) alleen voor Gremlin en Cassandra aan zodat ondersteuning voor uw abonnement op de back-end kan worden ingeschakeld. MongoDB gegevens bronnen kunnen worden geconfigureerd in de portal. Anders wordt de configuratie van de gegevens bron voor alle drie de Api's ondersteund met behulp van [Create Data Source (rest)](https://docs.microsoft.com/rest/api/searchservice/create-datasource) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
|  [**Indexeer functie Azure Data Lake Storage Gen2**](search-howto-index-azure-data-lake-storage.md) | Gegevens bron van Indexeer functie | Inhoud en meta gegevens indexeren van Data Lake Storage Gen2.| [Registreren](https://aka.ms/azure-cognitive-search/indexer-preview) is vereist zodat ondersteuning kan worden ingeschakeld voor uw abonnement op de back-end. Toegang tot deze gegevens bron met behulp van [Create Data Source (rest)](https://docs.microsoft.com/rest/api/searchservice/create-datasource) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**moreLikeThis**](search-more-like-this.md) | Query’s uitvoeren | Zoekt documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. | Voeg deze query parameter toe aan [Zoek documenten (rest)](https://docs.microsoft.com/rest/api/searchservice/search-documents) -aanroepen met API-Version = 2020-06 -30-preview, 2019-05-06-preview, 2016-09-01-preview of 2017-11-11-preview. |

## <a name="calling-preview-rest-apis"></a>De preview REST-Api's aanroepen

Met Azure Cognitive Search worden eerst experimentele functies vooraf vrijgegeven via de REST API en vervolgens via voorlopige versies van de .NET SDK.

Preview-functies zijn beschikbaar voor testen en experimenten, met als doel het verzamelen van feedback over het ontwerp en de implementatie van functies. Daarom kunnen preview-functies na verloop van tijd veranderen, mogelijk op manieren die achterwaartse compatibiliteit verstoren. Dit is in tegens telling tot functies in een GA-versie, die stabiel zijn en waarschijnlijk niet worden gewijzigd, met uitzonde ring van kleine neerwaarts compatibele oplossingen en uitbrei dingen. Daarnaast maken preview-functies niet altijd deel uit van een GA-versie.

Hoewel sommige preview-functies beschikbaar zijn in de portal en .NET SDK, bevat de REST API altijd preview-functies.

+ Voor zoek bewerkingen [**`2020-06-30-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-preview) is de huidige preview-versie.

+ Voor beheer bewerkingen [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) is de huidige preview-versie.

Oudere previews zijn nog operationeel, maar worden na verloop van tijd verouderd. Als uw code aanroept `api-version=2019-05-06-Preview` of `api-version=2016-09-01-Preview` of `api-version=2017-11-11-Preview` , zijn deze aanroepen nog geldig. De nieuwste preview-versie wordt echter vernieuwd met verbeteringen. 

De volgende voorbeeld syntaxis illustreert een aanroep van de preview API-versie.

```HTTP
GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2020-06-30-Preview
```

De Azure Cognitive Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Bekijk de referentie documentatie voor de zoek REST-voor beeld-API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie over de zoek service REST API (preview-versie)](https://docs.microsoft.com/rest/api/searchservice/index-preview)