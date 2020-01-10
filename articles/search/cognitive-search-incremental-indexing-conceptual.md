---
title: Incrementele verrijking (preview-versie)
titleSuffix: Azure Cognitive Search
description: Sla tussenliggende inhoud op in de cache en incrementele wijzigingen van de AI-verrijkings pijplijn in Azure Storage om de investeringen in bestaande verwerkte documenten te behouden. Deze functie is momenteel beschikbaar als openbare preview-versie.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: a5b12a426e52c3b80c58a30b320b2f746bbe990d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832198"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Inleiding tot incrementele verrijking en caching in azure Cognitive Search

> [!IMPORTANT] 
> Incrementele verrijking is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal-of .NET SDK-ondersteuning.

Incrementele verrijking voegt caching en statefulness toe aan een verrijkings pijplijn, waardoor uw investering in bestaande uitvoer behouden blijft, terwijl alleen de documenten worden gewijzigd die worden beïnvloed door een bepaalde wijziging. Dit betekent niet alleen uw monetaire investering in verwerking (met name OCR en verwerking van afbeeldingen), maar is ook een efficiëntere systeem. Wanneer structuren en inhoud in de cache zijn opgeslagen, kan een Indexeer functie bepalen welke vaardig heden zijn gewijzigd en alleen de aangepaste vaardig heden uitvoeren. 

## <a name="indexer-cache"></a>Indexeer functie cache

Incrementele verrijking voegt een cache toe aan de verrijkings pijplijn. De Indexeer functie slaat de resultaten van het kraken van documenten op en de uitvoer van elke vaardigheid voor elk document. Wanneer een vakkennisset wordt bijgewerkt, worden alleen de gewijzigde of downstream-vaardig heden opnieuw uitgevoerd. De bijgewerkte resultaten worden naar de cache geschreven en het document wordt bijgewerkt in de zoek index of in het kennis archief.

Fysiek wordt de cache opgeslagen in een BLOB-container in uw Azure Storage-account. Alle indexen in een zoek service delen mogelijk hetzelfde opslag account voor de indexer-cache. Aan elke Indexeer functie is een unieke en onveranderbare cache-id toegewezen voor de container die wordt gebruikt.

## <a name="cache-configuration"></a>Cache configuratie

U moet de eigenschap `cache` van de Indexeer functie instellen om profiteert te starten vanuit incrementele verrijking. In het volgende voor beeld ziet u een Indexeer functie waarin caching is ingeschakeld. Specifieke delen van deze configuratie worden in de volgende secties beschreven. Zie [incrementele verrijking instellen](search-howto-incremental-index.md)voor meer informatie.

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

De levens cyclus van de cache wordt beheerd door de Indexeer functie. Als de eigenschap `cache` op de Indexeer functie is ingesteld op null of als de connection string wordt gewijzigd, wordt de bestaande cache verwijderd bij de volgende uitvoering van de Indexeer functie. De levens cyclus van de cache is ook gekoppeld aan de levens cyclus van de Indexeer functie. Als een Indexeer functie wordt verwijderd, wordt ook de bijbehorende cache verwijderd.

Hoewel incrementele verrijking is ontworpen om wijzigingen zonder tussen komst van uw onderdeel te detecteren en erop te reageren, zijn er para meters die u kunt gebruiken om standaard gedrag te negeren:

+ Caching onderbreken
+ Vaardigheidset-controles overs Laan
+ Controles van gegevens bronnen overs Laan
+ Evaluatie van vaardig heden forceren

### <a name="suspend-caching"></a>Caching onderbreken

U kunt incrementele verrijking tijdelijk opschorten door de `enableReprocessing` eigenschap in de cache in te stellen op `false`, en later incrementele verrijking te hervatten en uiteindelijke consistentie te herstellen door deze in te `true`. Dit besturings element is vooral handig wanneer u een prioriteit wilt geven aan het indexeren van nieuwe documenten, waardoor consistentie tussen uw documenten wordt gegarandeerd.

### <a name="bypass-skillset-evaluation"></a>Beoordeling van vaardig heden overs Laan

Het aanpassen van een vaardig heden en het opnieuw verwerken van die vaardig heden gaat doorgaans hand matig. Sommige wijzigingen in een vaardig heden moeten echter niet worden verwerkt (bijvoorbeeld het implementeren van een aangepaste vaardigheid naar een nieuwe locatie of met een nieuwe toegangs sleutel). Dit zijn waarschijnlijk een rand wijziging die geen legitieme invloed heeft op de stof van de vaardig heden zelf. 

Als u weet dat een wijziging in de vaardig heden inderdaad Opper vlakking is, moet u de vaardig heden-evaluatie overschrijven door de para meter `disableCacheReprocessingChangeDetection` in te stellen op `true`:

1. Update vaardig heden bijwerken en de definitie van de vaardig heden wijzigen.
1. Voeg de para meter `disableCacheReprocessingChangeDetection=true` toe aan de aanvraag.
1. Verzend de wijziging.

Het instellen van deze para meter zorgt ervoor dat alleen updates van de definitie van de vaardig heden worden vastgelegd en dat de wijziging niet wordt geëvalueerd voor effecten op de bestaande verzameling.

In het volgende voor beeld ziet u een aanvraag voor het bijwerken van de vaardig heden met de para meter:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Validatie controles van gegevens bronnen overs Laan

Bij de meeste wijzigingen in een gegevens bron definitie wordt de cache ongeldig. Voor scenario's waarin u echter weet dat een wijziging de cache niet ongeldig moet maken, zoals het wijzigen van een connection string of het draaien van de sleutel op het opslag account, voegt u de para meter`ignoreResetRequirement` toe voor de update van de gegevens bron. Als u deze para meter instelt op `true`, kan de door voer worden door lopen zonder dat er een reset-voor waarde wordt geactiveerd, waardoor alle objecten opnieuw worden opgebouwd en volledig worden gevuld.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Evaluatie van vaardig heden forceren

Het doel van de cache is om onnodige verwerking te voor komen, maar stel dat u een wijziging hebt aangebracht in een vaardigheid of vaardigheidset die de Indexeer functie niet detecteert (bijvoorbeeld wijzigingen in externe onderdelen, zoals een aangepaste vaardighedenset). 

In dit geval kunt u de API voor het [opnieuw instellen van vaardig heden](preview-api-resetskills.md) gebruiken om het opnieuw verwerken van een bepaalde vaardigheid af te dwingen, inclusief eventuele downstream-vaardig heden die een afhankelijkheid hebben van de uitvoer van die vaardigheid. Deze API accepteert een POST-aanvraag met een lijst met vaardig heden die moeten worden gevalideerd en opnieuw moeten worden uitgevoerd. Nadat u vaardig heden hebt ingesteld, voert u de Indexeer functie uit om de bewerking uit te voeren.

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
* Vaardigheids updates die resulteren in verschillende stamboom, de vaardig heden van vakkennis is gewijzigd, d.w.z. Vaardigheids invoer
* Eventuele invalidatie van de upstream-vaardigheid, als een vaardigheid die een invoer voor deze vaardigheid levert, wordt bijgewerkt
* Updates voor de projectie locatie van de kennis opslag, resultaten voor het opnieuw projecteren van documenten
* Wijzigingen in de projecties van de kennis opslag, resultaten voor het opnieuw projecteren van documenten
* Uitvoer veld toewijzingen die zijn gewijzigd op een Indexeer functie, leiden ertoe dat documenten opnieuw worden geprojecteerd naar de index

## <a name="api-reference-content-for-incremental-enrichment"></a>API-referentie-inhoud voor incrementele verrijking

REST `api-version=2019-05-06-Preview` levert de Api's voor incrementele verrijking, met toevoegingen aan Indexeer functies, vaardig heden en gegevens bronnen. [Officiële referentie documentatie](https://docs.microsoft.com/rest/api/searchservice/) is voor algemeen beschik bare api's en biedt geen voor beeld van preview-functies. De volgende sectie bevat de referentie-inhoud voor beïnvloede Api's.

Gebruiks informatie en voor beelden vindt u in [caching configureren voor incrementele verrijking](search-howto-incremental-index.md).

### <a name="indexers"></a>Indexeerfuncties

Met [Create Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer) en [Update Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/update-indexer) worden nu nieuwe eigenschappen voor de cache beschikbaar gemaakt:

+ `StorageAccountConnectionString`: de connection string naar het opslag account dat wordt gebruikt om de tussenliggende resultaten in de cache op te slaan.

+ `EnableReprocessing`: standaard ingesteld op `true`, wanneer deze is ingesteld op `false`, worden documenten nog steeds naar de cache geschreven, maar worden er geen bestaande documenten opnieuw verwerkt op basis van de gegevens in de cache.

+ `ID` (alleen-lezen): de `ID` is de id van de container binnen het `annotationCache` Storage-account dat wordt gebruikt als cache voor deze Indexeer functie. Deze cache is uniek voor deze Indexeer functie en als de Indexeer functie wordt verwijderd en opnieuw wordt gemaakt met dezelfde naam, worden de `ID` opnieuw gegenereerd. De `ID` kan niet worden ingesteld en wordt altijd gegenereerd door de service.

### <a name="skillsets"></a>Vaardighedensets

+ [Update kwalificatieset](https://docs.microsoft.com/rest/api/searchservice/update-skillset) ondersteunt een nieuwe para meter voor de aanvraag: `disableCacheReprocessingChangeDetection`, die moet worden ingesteld op `true` wanneer u wilt dat bestaande documenten niet worden bijgewerkt op basis van de huidige actie.

+ Het [opnieuw instellen van vaardig heden](preview-api-resetskills.md) is een nieuwe bewerking die wordt gebruikt om een vaardig heden ongeldig te maken.

### <a name="datasources"></a>Gegevensbronnen

+ Met sommige Indexeer functies worden gegevens opgehaald via query's. Voor query's waarmee gegevens worden opgehaald, ondersteunt de [gegevens bron update](https://docs.microsoft.com/rest/api/searchservice/update-datasource) een nieuwe para meter voor een aanvraag `ignoreResetRequirement`, die moet worden ingesteld op `true` wanneer de update-actie de cache niet moet valideren.

Gebruik het `ignoreResetRequirement` spaarzaam als dit kan leiden tot onbedoelde inconsistentie in uw gegevens die niet eenvoudig kunnen worden gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

Incrementele verrijking is een krachtige functie waarmee wijzigingen in vaardig heden en AI-verrijking worden uitgebreid. Net als vaardig heden ontwikkeling zorgt incrementele verrijking ervoor dat de minst mogelijke werkzaamheden worden uitgevoerd terwijl uw documenten nog steeds consistent zijn.

Ga aan de slag door een cache aan een bestaande Indexeer functie toe te voegen of de cache toe te voegen bij het definiëren van een nieuwe Indexeer functie.

> [!div class="nextstepaction"]
> [Caching configureren voor incrementele verrijking](search-howto-incremental-index.md)
