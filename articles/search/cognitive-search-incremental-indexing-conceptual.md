---
title: Incrementele verrijking (voorbeeld)
titleSuffix: Azure Cognitive Search
description: Cache tussenliggende inhoud en incrementele wijzigingen van AI verrijking pijplijn in Azure Storage om investeringen in bestaande verwerkte documenten te behouden. Deze functie is momenteel beschikbaar als openbare preview-versie.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024140"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Inleiding tot incrementele verrijking en caching in Azure Cognitive Search

> [!IMPORTANT] 
> Incrementele verrijking is momenteel in openbare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal of .NET SDK-ondersteuning.

Incrementele verrijking voegt caching en statefulness toe aan een verrijkingspijplijn, waarbij uw investering in bestaande uitvoer behouden blijft, terwijl alleen die documenten worden gewijzigd die van invloed zijn op bepaalde wijzigingen. Dit behoudt niet alleen uw monetaire investering in verwerking (in het bijzonder OCR en beeldverwerking), maar zorgt ook voor een efficiënter systeem. Wanneer structuren en inhoud in de cache worden opgeslagen, kan een indexer bepalen welke vaardigheden zijn gewijzigd en alleen de vaardigheden uitvoeren die zijn gewijzigd, evenals eventuele downstream-afhankelijke vaardigheden. 

## <a name="indexer-cache"></a>Indexercache

Incrementele verrijking voegt een cache toe aan de verrijkingspijplijn. De indexer slaat de resultaten van het kraken van documenten in de cache, plus de uitvoer van elke vaardigheid voor elk document. Wanneer een skillset wordt bijgewerkt, worden alleen de gewijzigde of downstream-vaardigheden opnieuw uitgevoerd. De bijgewerkte resultaten worden naar de cache geschreven en het document wordt bijgewerkt in de zoekindex of het kennisarchief.

Fysiek wordt de cache opgeslagen in een blobcontainer in uw Azure Storage-account. De cache maakt ook gebruik van tabelopslag voor een interne registratie van verwerkingsupdates. Alle indexen binnen een zoekservice kunnen dezelfde opslagaccount voor de indexercache delen. Elke indexer krijgt een unieke en onveranderlijke cache-id toegewezen aan de container die deze gebruikt.

## <a name="cache-configuration"></a>Cacheconfiguratie

U moet de `cache` eigenschap op de indexer instellen om te profiteren van incrementele verrijking. In het volgende voorbeeld wordt een indexeerder met caching ingeschakeld. Specifieke onderdelen van deze configuratie worden beschreven in volgende secties. Zie [Incrementele verrijking instellen](search-howto-incremental-index.md)voor meer informatie .

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Als u deze eigenschap instelt op een bestaande indexeerder, moet u de indexer opnieuw instellen en opnieuw uitvoeren, waardoor alle documenten in uw gegevensbron opnieuw worden verwerkt. Deze stap is nodig om alle documenten die zijn verrijkt met eerdere versies van de skillset te elimineren. 

## <a name="cache-management"></a>Cachebeheer

De levenscyclus van de cache wordt beheerd door de indexeerder. Als `cache` de eigenschap op de indexer is ingesteld op null of als de verbindingstekenreeks wordt gewijzigd, wordt de bestaande cache verwijderd op de volgende indexerrun. De levenscyclus van de cache is ook gekoppeld aan de levenscyclus van de indexer. Als een indexer wordt verwijderd, wordt ook de bijbehorende cache verwijderd.

Hoewel incrementele verrijking is ontworpen om wijzigingen te detecteren en erop te reageren zonder tussenkomst van uw kant, zijn er parameters die u gebruiken om standaardgedrag te overschrijven:

+ Prioriteit geven aan nieuwe documenten
+ Vaardigheidscontroles omzeilen
+ Gegevensbroncontroles omzeilen
+ Force skillset evaluatie

### <a name="prioritize-new-documents"></a>Prioriteit geven aan nieuwe documenten

Stel `enableReprocessing` de eigenschap in om de verwerking te beheren van binnenkomende documenten die al in de cache zijn weergegeven. Wanneer `true` (standaard) worden documenten die al in de cache staan, opnieuw verwerkt wanneer u de indexer opnieuw uitvoert, ervan uitgaande dat uw vaardigheidsupdate van invloed is op dat document. 

Wanneer `false`bestaande documenten niet opnieuw worden verwerkt, geeft het effectief prioriteit aan nieuwe, binnenkomende inhoud ten opzichte van bestaande inhoud. U moet `enableReprocessing` alleen `false` instellen op een tijdelijke basis. Om de consistentie over `enableReprocessing` het `true` corpus te waarborgen, moet het grootste deel van de tijd, ervoor te zorgen dat alle documenten, zowel nieuw als bestaand, geldig zijn volgens de huidige skillset definitie.

### <a name="bypass-skillset-evaluation"></a>Evaluatie van vaardigheden omzeilen

Het wijzigen van een skillset en opwerking van die skillset gaan meestal hand in hand. Sommige wijzigingen in een skillset mogen echter niet leiden tot opwerking (bijvoorbeeld het implementeren van een aangepaste vaardigheid naar een nieuwe locatie of met een nieuwe toegangssleutel). Hoogstwaarschijnlijk zijn dit perifere wijzigingen die geen echte invloed hebben op de inhoud van de skillset zelf. 

Als u weet dat een wijziging in de skillset inderdaad oppervlakkig `disableCacheReprocessingChangeDetection` is, `true`moet u de vaardigheidssetevaluatie overschrijven door de parameter in te stellen op:

1. Bel Skillset bijwerken en wijzig de definitie van vaardigheden.
1. De `disableCacheReprocessingChangeDetection=true` parameter toevoegen aan de aanvraag.
1. Dien de wijziging in.

Als u deze parameter instelt, zorgt u ervoor dat alleen updates voor de skillset-definitie worden vastgelegd en dat de wijziging niet wordt geëvalueerd op effecten op het bestaande corpus.

In het volgende voorbeeld wordt een aanvraag voor de updateskillset met de parameter weergegeven:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Controle van gegevensbronvalidatie omzeilen

De meeste wijzigingen in een gegevensbrondefinitie maken de cache ongeldig. Voor scenario's waarvan u weet dat een wijziging de cache niet ongeldig mag maken - zoals het`ignoreResetRequirement` wijzigen van een verbindingstekenreeks of het roteren van de sleutel op het opslagaccount - wordt de parameter in de gegevensbronupdate toegevoegd. Als u `true` deze parameter instelt om de commit door te laten gaan, zonder een resetvoorwaarde te activeren die ertoe zou leiden dat alle objecten opnieuw worden opgebouwd en bevolken.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Force skillset evaluatie

Het doel van de cache is om onnodige verwerking te voorkomen, maar stel dat u een wijziging aanbrengt in een vaardigheid die de indexer niet detecteert (bijvoorbeeld iets wijzigen in externe code, zoals een aangepaste vaardigheid).

In dit geval u de [resetvaardigheden](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) gebruiken om opwerking van een bepaalde vaardigheid af te dwingen, inclusief downstreamvaardigheden die afhankelijk zijn van de uitvoer van die vaardigheid. Deze API accepteert een POST-aanvraag met een lijst met vaardigheden die ongeldig en gemarkeerd moeten worden voor opwerking. Nadat u de vaardigheden opnieuw instellen hebt ingesteld, voert u de indexer op om de pijplijn aan te roepen.

## <a name="change-detection"></a>Detectie wijzigen

Zodra u een cache inschakelt, evalueert de indexer wijzigingen in de samenstelling van de pijplijn om te bepalen welke inhoud opnieuw kan worden gebruikt en welke moet worden opgesteld. In deze sectie worden wijzigingen opgesomd die de cache volledig ongeldig maken, gevolgd door wijzigingen die incrementele verwerking activeren. 

### <a name="changes-that-invalidate-the-cache"></a>Wijzigingen die de cache ongeldig maken

Een ongeldig makende wijziging is een wijziging waarbij de volledige cache niet langer geldig is. Een voorbeeld van een ongeldig makende wijziging is een wijziging waarbij uw gegevensbron wordt bijgewerkt. Hier is de volledige lijst met wijzigingen die uw cache ongeldig zouden maken:

* Wijzigen in uw gegevensbrontype
* Wijzigen in gegevensbroncontainer
* Gegevensbronreferenties
* Detectiebeleid voor gegevensbronwijziging
* Detectiebeleid voor gegevensbronnen verwijderen
* Indexerveldtoewijzingen
* Indexerparameters
    * Parsing-modus
    * Uitgesloten bestandsnaamextensies
    * Geïndexeerde bestandsnaamextensies
    * Opslagmetagegevens alleen indexeren voor overmaatse documenten
    * Afgebakende tekstkoppen
    * Afgebakende tekstscheidingsteken
    * Documenthoofd
    * Afbeeldingsactie (Wijzigingen in de manier waarop afbeeldingen worden geëxtraheerd)

### <a name="changes-that-trigger-incremental-processing"></a>Wijzigingen die incrementele verwerking activeren

Incrementele verwerking evalueert uw skillset-definitie en bepaalt welke vaardigheden u opnieuw moet uitvoeren, waarbij de betreffende delen van de documentstructuur selectief worden bijgewerkt. Hier is de volledige lijst van wijzigingen die resulteren in incrementele verrijking:

* Vaardigheid in de skillset heeft een ander type. Het odata-type van de vaardigheid wordt bijgewerkt
* Vaardigheidsspecifieke parameters bijgewerkt, bijvoorbeeld de url, standaardwaarden of andere parameters
* Vaardigheidsuitvoer verandert, de vaardigheid retourneert extra of verschillende uitgangen
* Vaardigheidsupdates die resulteren is verschillende voorouders, skill chaining is veranderd d.w.z. vaardigheidsingangen
* Elke upstream vaardigheid ongeldigverklaring, als een vaardigheid die een input voor deze vaardigheid biedt wordt bijgewerkt
* Updates van de projectielocatie van de kenniswinkel, resulteert in het opnieuw projecteren van documenten
* Wijzigingen in de projecties van de kenniswinkel, resulteert in het herprojecteren van documenten
* Uitvoerveldtoewijzingen die zijn gewijzigd op een indexer resulteert in het opnieuw projecteren van documenten naar de index

## <a name="api-reference"></a>API-verwijzing

REST API-versie `2019-05-06-Preview` biedt incrementele verrijking door extra eigenschappen op indexers, skillsets en gegevensbronnen. Zie [Caching configureren voor incrementele verrijking](search-howto-incremental-index.md) voor meer informatie over het aanroepen van de API's naast de referentiedocumentatie.

+ [Indexeren maken (api-versie=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indexeren bijwerken (api-versie=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Skillset bijwerken (api-versie=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (Nieuwe PARAMETER URI op de aanvraag)

+ [Vaardigheden opnieuw instellen (api-versie=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Database-indexers (Azure SQL, Cosmos DB). Sommige indexers halen gegevens op via query's. Voor query's die gegevens ophalen, ondersteunt [Gegevensbron bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-data-source) een nieuwe parameter `true` op een verzoek **negerenResetRequirement**, die moet worden ingesteld op wanneer uw updateactie de cache niet ongeldig mag maken. 

  Gebruik **ignoreResetRequirement** spaarzaam als het kan leiden tot onbedoelde inconsistentie in uw gegevens die niet gemakkelijk zal worden gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

Incrementele verrijking is een krachtige functie die het bijhouden van wijzigingen uitbreidt naar skillsets en AI-verrijking. AIncremental verrijking maakt hergebruik van bestaande verwerkte inhoud mogelijk terwijl u over skillset-ontwerp heen gaat.

Schakel als volgende stap caching op een bestaande indexer in of voeg een cache toe bij het definiëren van een nieuwe indexer.

> [!div class="nextstepaction"]
> [Caching configureren voor incrementele verrijking](search-howto-incremental-index.md)
