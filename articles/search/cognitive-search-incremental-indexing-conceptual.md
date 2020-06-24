---
title: Incrementele uitgebreide concepten (preview-versie)
titleSuffix: Azure Cognitive Search
description: Sla tussenliggende inhoud op in de cache en incrementele wijzigingen van de AI-verrijkings pijplijn in Azure Storage om de investeringen in bestaande verwerkte documenten te behouden. Deze functie is momenteel beschikbaar als openbare preview-versie.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 0fa152a2edc55067aa8a15a90766a9ad5f66149e
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052060"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Incrementele verrijking en caching in azure Cognitive Search

> [!IMPORTANT] 
> Incrementele verrijking is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal-of .NET SDK-ondersteuning.

*Incrementele verrijking* is een functie die gericht is op [vaardig heden](cognitive-search-working-with-skillsets.md). Het maakt gebruik van Azure Storage om de verwerkings uitvoer die door een verrijkings pijplijn wordt gegenereerd, op te slaan voor hergebruik in toekomstige Indexeer functies. Waar mogelijk gebruikt de Indexeer functie een in cache opgeslagen uitvoer die nog geldig is. 

Bij een incrementele verrijking wordt niet alleen de monetaire investering in verwerking behouden (met name voor OCR-en afbeeldings verwerking), maar dit is ook een efficiënter systeem. Wanneer structuren en inhoud in de cache zijn opgeslagen, kan een Indexeer functie bepalen welke vaardig heden zijn gewijzigd en alleen de aangepaste vaardig heden uitvoeren. 

Een werk stroom die gebruikmaakt van incrementele caching, omvat de volgende stappen:

1. [Maak of Identificeer een Azure-opslag account](../storage/common/storage-account-create.md) om de cache op te slaan.
1. [Schakel incrementele verrijking](search-howto-incremental-index.md) in de Indexeer functie in.
1. [Maak een Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer) -plus een [vaardig heden](https://docs.microsoft.com/rest/api/searchservice/create-skillset) -om de pijp lijn aan te roepen. Tijdens de verwerking worden de stadia van verrijking opgeslagen voor elk document in Blob Storage voor toekomstig gebruik.
1. Test uw code en gebruik nadat u wijzigingen hebt aangebracht, [vaardig heden bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-skillset) om een definitie te wijzigen.
1. [Voer Indexeer functie uit](https://docs.microsoft.com/rest/api/searchservice/run-indexer) om de pijp lijn aan te roepen en de uitvoer in de cache op te halen voor snellere en rendabele verwerking.

Zie [incrementele verrijking instellen](search-howto-incremental-index.md)voor meer informatie over de stappen en overwegingen bij het werken met een bestaande indexer.

## <a name="indexer-cache"></a>Indexeer functie cache

Incrementele verrijking voegt een cache toe aan de verrijkings pijplijn. De Indexeer functie slaat de resultaten van het kraken van documenten op en de uitvoer van elke vaardigheid voor elk document. Wanneer een vakkennisset wordt bijgewerkt, worden alleen de gewijzigde of downstream-vaardig heden opnieuw uitgevoerd. De bijgewerkte resultaten worden naar de cache geschreven en het document wordt bijgewerkt in de zoek index of in het kennis archief.

Fysiek wordt de cache opgeslagen in een BLOB-container in uw Azure Storage-account. De cache gebruikt ook Table Storage voor een interne record van verwerkings updates. Alle indexen in een zoek service delen mogelijk hetzelfde opslag account voor de indexer-cache. Aan elke Indexeer functie is een unieke en onveranderbare cache-id toegewezen voor de container die wordt gebruikt.

## <a name="cache-configuration"></a>Cache configuratie

U moet de `cache` eigenschap op de Indexeer functie instellen om profiteert te starten vanuit incrementele verrijking. In het volgende voor beeld ziet u een Indexeer functie waarin caching is ingeschakeld. Specifieke delen van deze configuratie worden in de volgende secties beschreven. Zie [incrementele verrijking instellen](search-howto-incremental-index.md)voor meer informatie.

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

Als u deze eigenschap instelt voor een bestaande Indexeer functie, moet u de Indexeer functie opnieuw instellen en opnieuw uitvoeren. Dit leidt ertoe dat alle documenten in de gegevens bron opnieuw worden verwerkt. Deze stap is nodig voor het elimineren van documenten die worden verrijkt door eerdere versies van de vaardig heden. 

## <a name="cache-management"></a>Cache beheer

De levens cyclus van de cache wordt beheerd door de Indexeer functie. Als de `cache` eigenschap op de Indexeer functie is ingesteld op null of als de Connection String is gewijzigd, wordt de bestaande cache verwijderd bij de volgende uitvoering van de Indexeer functie. De levens cyclus van de cache is ook gekoppeld aan de levens cyclus van de Indexeer functie. Als een Indexeer functie wordt verwijderd, wordt ook de bijbehorende cache verwijderd.

Hoewel incrementele verrijking is ontworpen om wijzigingen zonder tussen komst van uw onderdeel te detecteren en erop te reageren, zijn er para meters die u kunt gebruiken om standaard gedrag te negeren:

+ Prioriteit geven aan nieuwe documenten
+ Vaardigheidset-controles overs Laan
+ Controles van gegevens bronnen overs Laan
+ Evaluatie van vaardig heden forceren

### <a name="prioritize-new-documents"></a>Prioriteit geven aan nieuwe documenten

Stel de `enableReprocessing` eigenschap in op het beheren van de verwerking van inkomende documenten die al in de cache worden weer gegeven. Wanneer `true` (standaard) worden documenten die al in de cache staan, opnieuw verwerkt wanneer u de Indexeer functie opnieuw uitvoeren, ervan uitgaande dat uw vaardigheids update van invloed is op dat document. 

Wanneer `false` worden bestaande documenten niet opnieuw verwerkt, waardoor er in feite nieuwe, binnenkomende inhoud in de bestaande inhoud wordt geplaatst. U dient alleen `enableReprocessing` `false` op tijdelijke basis in te stellen. Om consistentie tussen de verzameling te garanderen, `enableReprocessing` moet het `true` grootste deel van de tijd zijn, zodat alle documenten, zowel nieuwe als bestaande, geldig zijn volgens de huidige definitie van de vaardig heden.

### <a name="bypass-skillset-evaluation"></a>Beoordeling van vaardig heden overs Laan

Het aanpassen van een vaardig heden en het opnieuw verwerken van die vaardig heden gaat doorgaans hand matig. Sommige wijzigingen in een vaardig heden moeten echter niet worden verwerkt (bijvoorbeeld het implementeren van een aangepaste vaardigheid naar een nieuwe locatie of met een nieuwe toegangs sleutel). Dit zijn waarschijnlijk een rand wijziging die geen legitieme invloed heeft op de stof van de vaardig heden zelf. 

Als u weet dat een wijziging in de vaardig heden inderdaad Opper vlakking is, moet u de vaardig heden-evaluatie overschrijven door de `disableCacheReprocessingChangeDetection` para meter in te stellen op `true` :

1. Update vaardig heden bijwerken en de definitie van de vaardig heden wijzigen.
1. Voeg de `disableCacheReprocessingChangeDetection=true` para meter toe aan de aanvraag.
1. Verzend de wijziging.

Het instellen van deze para meter zorgt ervoor dat alleen updates van de definitie van de vaardig heden worden vastgelegd en dat de wijziging niet wordt geëvalueerd voor effecten op de bestaande verzameling.

In het volgende voor beeld ziet u een aanvraag voor het bijwerken van de vaardig heden met de para meter:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Validatie controles van gegevens bronnen overs Laan

Bij de meeste wijzigingen in een gegevens bron definitie wordt de cache ongeldig. Voor scenario's waarin u echter weet dat een wijziging de cache niet ongeldig moet maken, zoals het wijzigen van een connection string of het draaien van de sleutel op het opslag account, voegt u de `ignoreResetRequirement` para meter toe aan de update van de gegevens bron. Door deze para meter in te stellen `true` , kan de door voer worden door lopen zonder dat er een reset-voor waarde wordt geactiveerd, waardoor alle objecten opnieuw worden opgebouwd en volledig worden gevuld.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Evaluatie van vaardig heden forceren

Het doel van de cache is om onnodige verwerking te voor komen, maar stel dat u een wijziging aanbrengt in een vaardigheid die de Indexeer functie niet detecteert (bijvoorbeeld het wijzigen van iets in externe code, zoals een aangepaste vaardigheid).

In dit geval kunt u de [vaardig heden opnieuw instellen](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) gebruiken om het opnieuw verwerken van een bepaalde vaardigheid af te dwingen, inclusief eventuele downstream-vaardig heden die een afhankelijkheid hebben van de uitvoer van die vaardigheid. Deze API accepteert een POST-aanvraag met een lijst met vaardig heden die ongeldig moeten worden gemaakt en moeten worden gemarkeerd voor opnieuw verwerken. Nadat u vaardig heden hebt ingesteld, voert u de Indexeer functie uit om de pijp lijn aan te roepen.

## <a name="change-detection"></a>Wijzigings detectie

Zodra u een cache inschakelt, evalueert de Indexeer functie de wijzigingen in uw pijplijn samenstelling om te bepalen welke inhoud opnieuw kan worden gebruikt en wat opnieuw moet worden verwerkt. In deze sectie worden wijzigingen die de cache-uitgaan ongeldig gemaakt, gevolgd door wijzigingen die incrementele verwerking activeren. 

### <a name="changes-that-invalidate-the-cache"></a>Wijzigingen die de cache ongeldig maken

Een ongeldige wijziging is een van de wijzigingen die niet meer geldig zijn in de volledige cache. Een voor beeld van een invalidatie wijziging is één waar uw gegevens bron wordt bijgewerkt. Hier volgt de volledige lijst met wijzigingen die uw cache ongeldig maken:

* Overschakelen naar het type gegevens bron
* Wijzigen in gegevens bron container
* Gegevensbronreferenties
* Beleid voor wijzigings detectie van gegevens bronnen
* Gegevens bronnen detectie beleid verwijderen
* Indexeer functie veld toewijzingen
* Indexer-para meters
    * Parserings modus
    * Uitgesloten bestandsnaam extensies
    * Geïndexeerde bestandsnaam extensies
    * Meta gegevens van de opslag index alleen voor grote documenten
    * Tekst koppen met scheidings tekens
    * Scheidings teken voor tekst
    * Hoofdmap van document
    * Afbeeldings actie (wijzigingen in de uitgepakte installatie kopieën)

### <a name="changes-that-trigger-incremental-processing"></a>Wijzigingen die incrementele verwerking activeren

Met incrementele verwerking wordt de definitie van uw vaardig heden geëvalueerd en wordt bepaald welke vaardig heden opnieuw moeten worden uitgevoerd, waarbij de betrokken delen van de document structuur selectief worden bijgewerkt. Dit is de volledige lijst met wijzigingen die het gevolg zijn van incrementele verrijking:

* De kwalificatie in de vakkennisset heeft een ander type. Het odata-type van de vaardigheid is bijgewerkt
* Vakkennis-specifieke para meters zijn bijgewerkt, bijvoorbeeld de URL, standaard waarden of andere para meters
* Wijzigingen in de vaardigheids uitvoer, de vaardigheid retourneert extra of andere uitvoer
* Vaardigheids updates die resulteren in verschillende stamboom, de vaardig heden van vakkennis is gewijzigd, d.w.z. vaardigheids invoer
* Eventuele invalidatie van de upstream-vaardigheid, als een vaardigheid die een invoer voor deze vaardigheid levert, wordt bijgewerkt
* Updates voor de projectie locatie van de kennis opslag, resultaten voor het opnieuw projecteren van documenten
* Wijzigingen in de projecties van de kennis opslag, resultaten voor het opnieuw projecteren van documenten
* Uitvoer veld toewijzingen die zijn gewijzigd op een Indexeer functie, leiden ertoe dat documenten opnieuw worden geprojecteerd naar de index

## <a name="api-reference"></a>API-verwijzing

REST API versie `2019-05-06-Preview` voorziet in incrementele verrijking via extra eigenschappen voor Indexeer functies, vaardig heden en gegevens bronnen. Zie [caching configureren voor incrementele verrijking](search-howto-incremental-index.md) in aanvulling op de referentie documentatie voor meer informatie over het aanroepen van de api's.

+ [Indexeer functie maken (API-Version = 2019-05 -06-preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indexeer functie bijwerken (API-Version = 2019-05 -06-preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Update vaardig heden (API-Version = 2019-05 -06-preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (nieuwe Uri-para meter voor de aanvraag)

+ [Vaardigheden opnieuw instellen (api-versie=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Database Indexeer functies (Azure SQL, Cosmos DB). Met sommige Indexeer functies worden gegevens opgehaald via query's. Voor query's waarmee gegevens worden opgehaald, ondersteunt de [gegevens bron update](https://docs.microsoft.com/rest/api/searchservice/update-data-source) een nieuwe para meter voor een aanvraag **ignoreResetRequirement**, die moet worden ingesteld op `true` wanneer de cache niet door de update actie moet worden gevalideerd. 

  Gebruik **ignoreResetRequirement** spaarzaam, omdat dit kan leiden tot onbedoelde inconsistentie in uw gegevens die niet eenvoudig kunnen worden gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

Incrementele verrijking is een krachtige functie waarmee wijzigingen in vaardig heden en AI-verrijking worden uitgebreid. AIncremental-verrijking maakt het hergebruik van bestaande verwerkte inhoud mogelijk tijdens het herhalen van het ontwerp van de vaardig heden.

Als volgende stap moet u caching inschakelen voor een bestaande Indexeer functie of een cache toevoegen bij het definiëren van een nieuwe Indexeer functie.

> [!div class="nextstepaction"]
> [Caching configureren voor incrementele verrijking](search-howto-incremental-index.md)
