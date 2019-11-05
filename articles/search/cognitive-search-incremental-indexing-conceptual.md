---
title: Inleiding tot incrementele indexering (preview-versie)
titleSuffix: Azure Cognitive Search
description: Configureer uw AI-verrijkings pijplijn om uw gegevens te laten voldoen aan de uiteindelijke consistentie van het verwerken van updates voor vaardig heden, vaardig heden, Indexeer functies of gegevens bronnen.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ea3bcfc25040f09b6871d85412ac64061ec2f9e8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549118"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Wat is incrementele indexering in azure Cognitive Search?

> [!Note]
> Incrementele indexering is een preview-versie en is niet bedoeld voor productie gebruik. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal-of .NET SDK-ondersteuning.
>

Incrementeel indexeren is een nieuwe functie van Azure Cognitive Search waarmee caching en status worden toegevoegd aan verrijkte inhoud in een cognitieve vaardigheids, waarmee u de verwerking en het opnieuw verwerken van afzonderlijke stappen in een verrijkings pijplijn kunt controleren. Dit houdt niet alleen uw monetaire investering in de verwerking, maar is ook een efficiënter systeem. Wanneer structuren en inhoud in de cache zijn opgeslagen, kan een Indexeer functie bepalen welke vaardig heden zijn gewijzigd en alleen de aangepaste vaardig heden uitvoeren. 

Met incrementele indexering heeft de huidige versie van de verrijkings pijplijn de kleinste hoeveelheid werk om consistentie te garanderen voor alle documenten in uw index. Voor scenario's waarin u volledige controle wilt, kunt u nauw keurige besturings elementen gebruiken om het verwachte gedrag te onderdrukken. Zie [incrementele indexering instellen](search-howto-incremental-index.md)voor meer informatie over de configuratie.

## <a name="indexer-cache"></a>Indexeer functie cache

Incrementele indexering voegt een indexer-cache toe aan de verrijkings pijplijn. De Indexeer functie slaat de resultaten op van het kraken van documenten en de uitvoer van elke vaardigheid voor elk document. Wanneer een vakkennisset wordt bijgewerkt, worden alleen de gewijzigde of downstream-vaardig heden opnieuw uitgevoerd. De bijgewerkte resultaten worden naar de cache geschreven en het document wordt bijgewerkt in de index en het kennis archief.

Fysiek is de cache een opslag account. Alle indexen in een zoek service delen mogelijk hetzelfde opslag account voor de indexer-cache. Aan elke Indexeer functie is een unieke en onveranderlijke cache-id toegewezen.

### <a name="cache-configuration"></a>Cache configuratie

U moet de eigenschap `cache` van de Indexeer functie instellen om profiteert te starten vanuit incrementeel indexeren. In het volgende voor beeld ziet u een Indexeer functie waarin caching is ingeschakeld. Specifieke delen van deze configuratie worden in de volgende secties beschreven.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

Als u deze eigenschap voor de eerste keer op een bestaande Indexeer functie wilt instellen, moet u deze ook opnieuw instellen. Hierdoor worden alle documenten in de gegevens bron opnieuw verwerkt. Het doel van incrementele indexering is om de documenten in uw index consistent te maken met uw gegevens bron en de huidige versie van uw vaardig heden. Het opnieuw instellen van de index is de eerste stap in de richting van deze consistentie, omdat er documenten worden geëlimineerd die worden verrijkt door eerdere versies van de vaardig heden. De Indexeer functie moet opnieuw worden ingesteld om te beginnen met een consistente basis lijn.

### <a name="cache-lifecycle"></a>Cache levenscyclus

De levens cyclus van de cache wordt beheerd door de Indexeer functie. Als de eigenschap `cache` op de Indexeer functie is ingesteld op null of als de connection string is gewijzigd, wordt de bestaande cache verwijderd. De levens cyclus van de cache is ook gekoppeld aan de levens cyclus van de Indexeer functie. Als een Indexeer functie wordt verwijderd, wordt ook de bijbehorende cache verwijderd.

### <a name="indexer-cache-mode"></a>Cache modus voor de Indexeer functie

De Indexeer functie cache kan in modi worden uitgevoerd, waarbij gegevens alleen naar de cache worden geschreven of gegevens naar de cache worden geschreven en worden gebruikt om documenten opnieuw te verrijken.  U kunt incrementele verrijking tijdelijk opschorten door de `enableReprocessing` eigenschap in de cache in te stellen op `false`, en later incrementele verrijking te hervatten en uiteindelijke consistentie te herstellen door deze in te `true`. Dit besturings element is vooral handig wanneer u een prioriteit wilt geven aan het indexeren van nieuwe documenten, waardoor consistentie tussen uw documenten wordt gegarandeerd.

## <a name="change-detection-override"></a>Wijziging detectie negeren

Met incrementele indexering krijgt u nauw keurige controle over alle aspecten van de verrijkings pijplijn. Met dit besturings element kunt u problemen ondervinden waarbij een wijziging mogelijk onbedoelde gevolgen heeft. Als u bijvoorbeeld een vaardig heden bewerkt en de URL voor een aangepaste vaardigheid bijwerkt, zullen de Indexeer functie de in de cache opgeslagen resultaten voor die vaardigheid ongeldig worden. Als u het eind punt alleen verplaatst naar een andere VM of als u uw vaardigheid opnieuw implementeert met een nieuwe toegangs sleutel, wilt u dat bestaande documenten echt niet worden verwerkt.

Om ervoor te zorgen dat de Indexeer functie alleen verrijkingen heeft die u expliciet nodig hebt, kan updates voor de vaardig heden eventueel de `disableCacheReprocessingChangeDetection` query string-para meter instellen op `true`. Als deze para meter is ingesteld, zorgt u ervoor dat alleen de updates van de vaardig heden worden doorgevoerd en wordt de wijziging niet geëvalueerd voor de effecten van de bestaande verzameling.

In het volgende voor beeld wordt het gebruik van query strings geïllustreerd. Het maakt deel uit van de aanvraag, met &-gescheiden sleutel waardeparen. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Ongeldige cache

Het omgekeerde van dat scenario is een manier waarop u een nieuwe versie van een aangepaste vaardigheid kunt implementeren, niets binnen de verrijkings pijplijn wijzigt, maar u moet een specifieke vaardigheid ongeldig maken en alle betrokken documenten opnieuw verwerken om de voor delen van een bijgewerkt model weer te geven. In dergelijke gevallen kunt u de bewerking voor het valideren van vaardig heden aanroepen op de vaardig heden. De API voor het opnieuw instellen van vaardig heden accepteert een POST-aanvraag met de lijst met vaardigheids uitvoer in de cache die ongeldig moet worden gemaakt. Zie [Indexeer functie opnieuw instellen (Search rest API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)voor meer informatie over de API voor het opnieuw instellen van vaardig heden.

## <a name="bi-directional-change-detection"></a>Bi-directionele wijzigings detectie

Indexeer functies verplaatsen niet alleen nieuwe documenten, maar zijn nu in staat om eerder verwerkte documenten naar consistentie te verplaatsen en te verwerken. Met deze nieuwe mogelijkheid is het belang rijk om te begrijpen hoe wijzigingen in uw verrijkings pijplijn onderdelen resulteren in Indexeer werk. De Indexeer functie wacht op het werk in de wachtrij wanneer er een wijziging wordt geïdentificeerd die is ongeldig of niet consistent is ten opzichte van de inhoud in de cache.

### <a name="invalidating-changes"></a>Wijzigingen ongeldig worden

Het ongeldig maken van wijzigingen is zeldzaam, maar heeft een aanzienlijk effect op de status van uw verrijkings pijplijn. Een ongeldige wijziging is een van de wijzigingen die niet meer geldig zijn in de volledige cache. Een voor beeld van een invalidatie wijziging is één waar uw gegevens bron wordt bijgewerkt. Als u weet dat de wijziging de cache niet ongeldig moet worden, zoals het draaien van de sleutel op het opslag account, moet de `ignoreResetRequirement` query string-para meter worden ingesteld op `true` op de update-bewerking van de specifieke resource om ervoor te zorgen dat de bewerking niet afgekeurd.

Hier volgt de volledige lijst met wijzigingen die uw cache ongeldig maken:

* Overschakelen naar het type gegevens bron
* Wijzigen in gegevens bron container
* Gegevens bron referenties
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

### <a name="inconsistent-changes"></a>Inconsistente wijzigingen

Een voor beeld van een inconsistente wijziging is een update voor uw vaardig heden die een vaardigheid wijzigt. De wijziging kan een deel van de cache inconsistent maken. Met de Indexeer functie wordt het werk geïdentificeerd om de dingen opnieuw consistent te maken.  

De volledige lijst met wijzigingen die resulteren in inconsistentie in de cache:

* De kwalificatie in de vakkennisset heeft een ander type. Het odata-type van de vaardigheid is bijgewerkt
* Vakkennis-specifieke para meters zijn bijgewerkt, bijvoorbeeld de URL, standaard waarden of andere para meters
* Wijzigingen in de vaardigheids uitvoer, de vaardigheid retourneert extra of andere uitvoer
* Vaardigheids updates die resulteren in verschillende stamboom, de vaardig heden van vakkennis is gewijzigd, d.w.z. Vaardigheids invoer
* Eventuele invalidatie van de upstream-vaardigheid, als een vaardigheid die een invoer voor deze vaardigheid levert, wordt bijgewerkt
* Updates voor de projectie locatie van de kennis opslag, resultaten voor het opnieuw projecteren van documenten
* Wijzigingen in de projecties van de kennis opslag, resultaten voor het opnieuw projecteren van documenten
* Uitvoer veld toewijzingen die zijn gewijzigd op een Indexeer functie, leiden ertoe dat documenten opnieuw worden geprojecteerd naar de index

## <a name="rest-api-reference-for-incremental-indexing"></a>REST API referentie voor incrementele indexering

REST `api-version=2019-05-06-Preview` levert de Api's voor incrementele indexering, met toevoegingen aan Indexeer functies, vaardig heden en gegevens bronnen. Deze toevoegingen zijn momenteel niet beschikbaar in referentie documentatie. In de volgende sectie worden de wijzigingen in de API beschreven.

### <a name="indexers"></a>Indexeerfuncties

Met [Create Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer) en [Update Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/update-indexer) worden nu nieuwe eigenschappen voor de cache beschikbaar gemaakt:

* `StorageAccountConnectionString`: de connection string naar het opslag account dat wordt gebruikt om de tussenliggende resultaten in de cache op te slaan.

* `CacheId`: de `cacheId` is de id van de container binnen het `annotationCache`-opslag account dat wordt gebruikt als de cache voor deze Indexeer functie. Deze cache is uniek voor deze Indexeer functie en als de Indexeer functie wordt verwijderd en opnieuw wordt gemaakt met dezelfde naam, worden de `cacheId` opnieuw gegenereerd. De `cacheId` kan niet worden ingesteld en wordt altijd gegenereerd door de service.

* `EnableReprocessing`: standaard ingesteld op `true`, wanneer deze is ingesteld op `false`, worden documenten nog steeds naar de cache geschreven, maar worden er geen bestaande documenten opnieuw verwerkt op basis van de gegevens in de cache.

Bij sommige Indexeer functies (via [gegevens bronnen](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) worden gegevens opgehaald via query's. Voor query's waarmee gegevens worden opgehaald, wordt door Indexeer functies ook een nieuwe query teken reeks parameter ondersteund: `ignoreResetRequirement` moet worden ingesteld op `true` wanneer uw update-actie de cache niet moet valideren.

### <a name="skillsets"></a>Vaardighedensets

Vaardig heden biedt geen ondersteuning voor nieuwe bewerkingen, maar biedt wel ondersteuning voor een nieuwe query reeks parameter: `disableCacheReprocessingChangeDetection` moet worden ingesteld op `true` als u wilt dat bestaande documenten niet worden bijgewerkt op basis van de huidige actie.

### <a name="datasources"></a>gegevens bronnen

Gegevens bronnen ondersteunen geen nieuwe bewerkingen, maar ondersteunen een nieuwe query reeks parameter: `ignoreResetRequirement` moet worden ingesteld op `true` wanneer de update-actie de cache niet moet valideren.

## <a name="best-practices"></a>Aanbevolen procedures

De aanbevolen methode voor het gebruik van incrementele indexering is het configureren van incrementele indexering door de cache-eigenschap in te stellen op een nieuwe Indexeer functie of een bestaande Indexeer functie opnieuw in te stellen en de cache-eigenschap in.

Gebruik het `ignoreResetRequirement` spaarzaam als dit kan leiden tot onbedoelde inconsistentie in uw gegevens die niet eenvoudig kunnen worden gedetecteerd.

## <a name="takeaways"></a>Opgedane kennis

Incrementeel indexeren is een krachtige functie waarmee het bijhouden van wijzigingen van gegevens bronnen wordt uitgebreid naar alle aspecten van de verrijkings pijplijn, met inbegrip van de gegevens bron, de huidige versie van uw vaardig heden en de Indexeer functie. Naarmate uw vaardig heden, vaardig heden of verrijkingen worden ontwikkeld, zorgt de verrijkings pijplijn ervoor dat de minst mogelijke werkzaamheden worden uitgevoerd terwijl uw documenten nog steeds consistent zijn.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met incrementele indexering door een cache toe te voegen aan een bestaande indexer of de cache toe te voegen bij het definiëren van een nieuwe Indexeer functie.

> [!div class="nextstepaction"]
> [Incrementele indexering instellen](search-howto-incremental-index.md)
