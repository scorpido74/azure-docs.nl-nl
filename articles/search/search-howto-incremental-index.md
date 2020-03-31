---
title: Cache en incrementele verrijking configureren (voorbeeld)
titleSuffix: Azure Cognitive Search
description: Caching inschakelen en de status van verrijkte inhoud behouden voor gecontroleerde verwerking in een cognitieve vaardigheden. Deze functie is momenteel beschikbaar als openbare preview-versie.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989549"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Caching configureren voor incrementele verrijking in Azure Cognitive Search

> [!IMPORTANT] 
> Incrementele verrijking is momenteel in openbare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal of .NET SDK-ondersteuning.

In dit artikel ziet u hoe u caching toevoegt aan een verrijkingspijplijn, zodat u stappen stapsgewijs wijzigen zonder dat u telkens opnieuw hoeft te worden opgebouwd. Standaard is een skillset stateloos en voor het wijzigen van een deel van de samenstelling is een volledige rerun van de indexer vereist. Met incrementele verrijking kan de indexer bepalen welke delen van de documentstructuur moeten worden vernieuwd op basis van wijzigingen die zijn gedetecteerd in de definities van vaardigheden of indexeren. Bestaande verwerkte output wordt bewaard en waar mogelijk hergebruikt. 

Inhoud in cache wordt in Azure Storage geplaatst met behulp van accountgegevens die u opgeeft. De container, `ms-az-search-indexercache-<alpha-numerc-string>`met de naam , wordt gemaakt wanneer u de indexer uitvoert. Het moet worden beschouwd als een intern onderdeel beheerd door uw zoekservice en mag niet worden gewijzigd.

Als u niet bekend bent met het instellen van indexeerders, begint u met [het indexeroverzicht](search-indexer-overview.md) en gaat u verder met [skillsets](cognitive-search-working-with-skillsets.md) om meer te weten te komen over verrijkingspijplijnen. Zie [incrementele verrijking](cognitive-search-incremental-indexing-conceptual.md)voor meer achtergrondinformatie over sleutelbegrippen.

## <a name="enable-caching-on-an-existing-indexer"></a>Caching inschakelen op een bestaande indexer

Als u een bestaande indexeerder hebt die al een skillset heeft, volgt u de stappen in deze sectie om caching toe te voegen. Als een eenmalige bewerking moet u de indexer volledig opnieuw instellen en opnieuw uitvoeren voordat incrementele verwerking van kracht kan worden.

> [!TIP]
> Als proof-of-concept u snel door deze [portal](cognitive-search-quickstart-blob.md) rennen om noodzakelijke objecten te maken en vervolgens Postman of de portal gebruiken om uw updates te maken. U een factureerbare resource voor cognitive services toevoegen. Als u de indexer meerdere keren uitvoert, wordt de gratis dagelijkse toewijzing uitgeput voordat u alle stappen voltooien.

### <a name="step-1-get-the-indexer-definition"></a>Stap 1: De definitie van de indexer krijgen

Begin met een geldige, bestaande indexeerder met deze componenten: gegevensbron, skillset, index. Uw indexer moet kunnen worden uitgevoerd. 

Met behulp van een API-client u een [GET Indexer-aanvraag maken](https://docs.microsoft.com/rest/api/searchservice/get-indexer) om de huidige configuratie van de indexer op te halen. Wanneer u de preview-API-versie gebruikt voor `cache` de GET the indexer, wordt een eigenschapsset op null toegevoegd aan de definities.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Kopieer de definitie van indexer uit het antwoord.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Stap 2: De eigenschap cache in de indexerdefinitie wijzigen

Standaard is `cache` de eigenschap null. Gebruik een API-client om de cacheconfiguratie in te stellen (de portal ondersteunt deze deeltjesupdate niet). 

Wijzig het cacheobject om de volgende vereiste en optionele eigenschappen op te nemen: 

+ Dit `storageConnectionString` is vereist en moet zijn ingesteld op een azure-opslagverbindingstekenreeks. 
+ De `enableReprocessing` eigenschap booleaanse`true` eigenschap is optioneel (standaard) en geeft aan dat incrementele verrijking is ingeschakeld. Indien nodig u `false` instellen op incrementele verwerking terwijl andere resource-intensieve bewerkingen, zoals het indexeren van nieuwe documenten, aan de gang zijn en deze vervolgens terugzetten naar `true` later.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Stap 3: De indexer opnieuw instellen

Een reset van de indexer is vereist bij het instellen van incrementele verrijking voor bestaande indexers om ervoor te zorgen dat alle documenten consistent zijn. U de portal of een API-client en de [REST-API voor resetindexer](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) gebruiken voor deze taak.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Stap 4: De bijgewerkte definitie opslaan

[De indexer bijwerken](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) met een PUT-aanvraag, de hoofdtekst van de aanvraag moet de bijgewerkte indexerdefinitie bevatten die de eigenschap cache heeft. Als u een 400 krijgt, controleert u de definitie van de indexer om te controleren of aan alle vereisten wordt voldaan (gegevensbron, vaardigheden, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Als u nu nog een GET-aanvraag op de indexer `ID` geeft, bevat het antwoord van de service een eigenschap in het cacheobject. De alfanumerieke tekenreeks wordt toegevoegd aan de naam van de container met alle in de cache opgeslagen resultaten en tussenliggende status van elk document dat door deze indexer wordt verwerkt. De ID wordt gebruikt om de cache in Blob-opslag een unieke naam te geven.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Stap 5: De indexeren uitvoeren

Als u indexeren wilt uitvoeren, u de portal of de API gebruiken. Selecteer in de portal in de lijst met indexers de indexeren en klik op **Uitvoeren**. Een voordeel van het gebruik van de portal is dat u de indexerstatus controleren, de duur van de taak noteren en hoeveel documenten worden verwerkt. Portalpagina's worden om de paar minuten vernieuwd.

U ook REST gebruiken om [de indexer uit](https://docs.microsoft.com/rest/api/searchservice/run-indexer)te voeren:

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Nadat de indexer is uitgevoerd, u de cache vinden in Azure Blob-opslag. De containernaam is in de volgende indeling:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Een reset en rerun van de indexer resulteert in een volledige herbouw, zodat inhoud in de cache kan worden opgeslagen. Alle cognitieve verrijkingen worden op alle documenten opnieuw uitgevoerd.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Stap 6: Een skillset wijzigen en incrementele verrijking bevestigen

Als u een skillset wilt wijzigen, u de portal of de API gebruiken. Als u bijvoorbeeld tekstvertaling gebruikt, is een `en` eenvoudige `es` inline-wijziging van of naar een andere taal voldoende voor het proof-of-concept testen van incrementele verrijking.

Voer de indexer opnieuw uit. Alleen die delen van een verrijkte documentstructuur worden bijgewerkt. Als u de [portal snel start](cognitive-search-quickstart-blob.md) als proof-of-concept, het wijzigen van de tekst vertaling vaardigheid om 'es', zult u merken dat slechts 8 documenten worden bijgewerkt in plaats van de oorspronkelijke 14. Afbeeldingsbestanden die niet door het vertaalproces worden beïnvloed, worden opnieuw gebruikt vanuit de cache.

## <a name="enable-caching-on-new-indexers"></a>Caching inschakelen op nieuwe indexeerders

Als u incrementele verrijking wilt instellen voor een nieuwe `cache` indexer, hoeft u alleen de eigenschap op te nemen in de payload van de indexerdefinitie wanneer u [Indexer maken aanroept (2019-05-06-Preview).](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Controleren op uitvoer in cache

De cache wordt gemaakt, gebruikt en beheerd door de indexeerder en de inhoud ervan wordt niet weergegeven in een indeling die leesbaar is voor de mens. De beste manier om te bepalen of de cache wordt gebruikt, is door de indexer op te voeren en voor- en nastatistieken voor uitvoeringstijd en documenttellingen te vergelijken. 

Neem bijvoorbeeld een skillset aan die begint met beeldanalyse en optische tekenherkenning (OCR) van gescande documenten, gevolgd door downstream-analyse van de resulterende tekst. Als u een downstreamtekstvaardigheid wijzigt, kan de indexer alle eerder verwerkte afbeeldings- en OCR-inhoud ophalen uit de cache, zodat alleen tekstgerelateerde wijzigingen die door uw bewerkingen zijn aangegeven, worden bijgewerkt en verwerkt. U verwachten dat er minder documenten in het aantal documenten te zien zijn (bijvoorbeeld 8/8 in tegenstelling tot 14/14 in de oorspronkelijke uitvoering), kortere uitvoeringstijden en minder kosten op uw factuur.

## <a name="working-with-the-cache"></a>Werken met de cache

Zodra de cache operationeel is, controleren indexeerders de cache wanneer [Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer) wordt aangeroepen, om te zien welke delen van de bestaande uitvoer kunnen worden gebruikt. 

In de volgende tabel wordt samengevat hoe verschillende API's zich verhouden tot de cache:

| API           | Impact op cache     |
|---------------|------------------|
| [Indexerermaken (2019-05-06-Voorbeeld)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Hiermee maakt en draait u een indexeerder bij het eerste gebruik, inclusief het maken van een cache als de definitie van uw indexer dit opgeeft. |
| [Indexeren uitvoeren](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Hiermee wordt een verrijkingspijplijn op aanvraag uitgevoerd. Deze API leest uit de cache als deze bestaat of maakt een cache als u caching hebt toegevoegd aan een bijgewerkte indexerdefinitie. Wanneer u een indexer uitvoert die caching heeft ingeschakeld, laat de indexer stappen weg als uitvoer in de cache kan worden gebruikt. U de algemeen beschikbare api-versie van deze API gebruiken.|
| [Indexeren opnieuw instellen](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Hiermee wordt de indexeerder van incrementele indexeringsgegevens gewist. De volgende indexerrun (on-demand of schema) is volledig opwerking vanaf nul, inclusief het opnieuw uitvoeren van alle vaardigheden en het opnieuw opbouwen van de cache. Het is functioneel gelijk aan het verwijderen van de indexer en het opnieuw maken ervan. U de algemeen beschikbare api-versie van deze API gebruiken.|
| [Vaardigheden opnieuw instellen (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Hiermee geeft u op welke vaardigheden u opnieuw moet uitvoeren op de volgende indexerrun, zelfs als u geen vaardigheden hebt gewijzigd. De cache wordt dienovereenkomstig bijgewerkt. Uitvoer, zoals een kennisarchief of zoekindex, worden vernieuwd met herbruikbare gegevens uit de cache plus nieuwe inhoud per de bijgewerkte vaardigheid. |

Zie [Cachebeheer](cognitive-search-incremental-indexing-conceptual.md#cache-management)voor meer informatie over het beheren van wat er met de cache gebeurt.

## <a name="next-steps"></a>Volgende stappen

Incrementele verrijking is van toepassing op indexers die skillsets bevatten. Als volgende stap, bezoek de skillset documentatie om concepten en samenstelling te begrijpen. 

Bovendien wilt u, zodra u de cache inschakelt, meer weten over de parameters en API's die factor zijn in caching, inclusief hoe u bepaalde gedragingen overschrijven of forceren. Zie de volgende links voor meer informatie.

+ [Skillset concepten en compositie](cognitive-search-working-with-skillsets.md)
+ [Hoe maak je een skillset](cognitive-search-defining-skillset.md)
+ [Inleiding tot incrementele verrijking en caching](cognitive-search-incremental-indexing-conceptual.md)
