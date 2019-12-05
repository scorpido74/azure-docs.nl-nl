---
title: Incrementele indexering toevoegen (preview)
titleSuffix: Azure Cognitive Search
description: Schakel het bijhouden van wijzigingen in en behoud de status van verrijkte inhoud voor beheerde verwerking in een cognitieve vaardigheidset. Deze functie is momenteel beschikbaar als openbare preview-versie.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 92da697c95f2b9ea544bb1f9bfa689c13bd0d2ae
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806759"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Incrementele indexering van verrijkte documenten instellen in azure Cognitive Search

> [!IMPORTANT] 
> Incrementele indexering is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal-of .NET SDK-ondersteuning.

In dit artikel wordt beschreven hoe u status en caching toevoegt aan verrijkte documenten die worden verplaatst via een Azure Cognitive Search-verrijkings pijplijn, zodat u documenten stapsgewijs kunt indexeren vanuit een van de ondersteunde gegevens bronnen. Een vaardig heden is standaard stateless en het wijzigen van een deel van de samen stelling vereist een volledige herinstallatie van de Indexeer functie. Met incrementele indexering kan de Indexeer functie bepalen welke delen van de pijp lijn zijn gewijzigd, de bestaande verrijkingen voor ongewijzigde delen opnieuw gebruiken en verrijkingen worden herzien voor de stappen die gewijzigd kunnen worden. Inhoud in de cache wordt geplaatst in Azure Storage.

Als u niet bekend bent met het instellen van Indexeer functies, start u met [Indexeer functie-overzicht](search-indexer-overview.md) en gaat u verder met [vaardig heden](cognitive-search-working-with-skillsets.md) voor meer informatie over verrijkings pijplijnen. Zie [Incrementeel indexeren](cognitive-search-incremental-indexing-conceptual.md)voor meer achtergrond informatie over de belangrijkste concepten.

## <a name="modify-an-existing-indexer"></a>Een bestaande Indexeer functie wijzigen

Als u een bestaande indexer hebt, volgt u deze stappen om incrementele indexering in te scha kelen.

### <a name="step-1-get-the-indexer"></a>Stap 1: de Indexeer functie ophalen

Begin met een geldige, bestaande Indexeer functie met de vereiste gegevens bron en index die al zijn gedefinieerd. De Indexeer functie moet uitvoer bare zijn. Maak met behulp van een API-client een [Get-aanvraag](https://docs.microsoft.com/rest/api/searchservice/get-indexer) om de huidige configuratie van de Indexeer functie op te halen, waaraan u incrementele indexering wilt toevoegen.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>Stap 2: de eigenschap cache toevoegen

Bewerk de reactie van de GET-aanvraag om de eigenschap `cache` toe te voegen aan de Indexeer functie. Het cache object vereist slechts één eigenschap `storageConnectionString` dit de connection string voor het opslag account is. 

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
    "parameters": {
        "configuration": {
            "enableAnnotationCache": true
        }
    }
}
```
#### <a name="enable-reporocessing"></a>Reporocessing inschakelen

U kunt eventueel de `enableReprocessing` Boole-eigenschap instellen in de cache die standaard is ingesteld op True. Met de vlag `enableReprocessing` kunt u het gedrag van de Indexeer functie beheren. In scenario's waarin u wilt dat de Indexeer functie een prioriteit geeft aan nieuwe documenten aan de index toe te voegen, stelt u de vlag in op false. Als uw Indexeer functie is gekoppeld aan de nieuwe documenten en de vlag naar waar wordt gespiegeld, zou de Indexeer functie de bestaande documenten op de uiteindelijke consistentie kunnen starten. Tijdens de periode dat de vlag `enableReprocessing` is ingesteld op False, schrijft de Indexeer functie alleen naar de cache, maar worden bestaande documenten niet verwerkt op basis van de geïdentificeerde wijzigingen in de verrijkings pijplijn.

### <a name="step-3-reset-the-indexer"></a>Stap 3: de Indexeer functie opnieuw instellen

> [!NOTE]
> Als u de Indexeer functie opnieuw instelt, worden alle documenten in de gegevens bron opnieuw verwerkt, zodat de inhoud in de cache kan worden opgeslagen. Alle cognitieve verrijkingen worden opnieuw uitgevoerd op alle documenten.
>

Het opnieuw instellen van de Indexeer functie is vereist bij het instellen van incrementele indexering voor bestaande Indexeer functies om ervoor te zorgen dat alle documenten een consistente status hebben. Stel de Indexeer functie opnieuw in met behulp van de [rest API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>Stap 4: de bijgewerkte definitie opslaan

De definitie van de Indexeer functie bijwerken met een PUT-aanvraag, de hoofd tekst van de aanvraag moet de bijgewerkte indexerings definitie bevatten.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Als u nu een nieuwe GET-aanvraag voor de Indexeer functie levert, bevat het antwoord van de service een `cacheId` eigenschap in het cache-object. De `cacheId` is de naam van de container die alle in de cache opgeslagen resultaten en de tussenliggende status bevat van elk document dat door deze Indexeer functie wordt verwerkt.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Incrementele indexering inschakelen voor nieuwe Indexeer functies

Als u incrementele indexering wilt instellen voor een nieuwe Indexeer functie, neemt u de eigenschap `cache` op in de nettolading van de definitie van de Indexeer functie. Zorg ervoor dat u gebruikmaakt van de `2019-05-06-Preview` versie van de API.

## <a name="overriding-incremental-indexing"></a>Incrementele indexering overschrijven

Wanneer de configuratie is geconfigureerd, worden wijzigingen in uw indexerings pijplijn bijgehouden en worden documenten getraceerd naar uiteindelijke consistentie in de index en projecties. In sommige gevallen moet u dit gedrag onderdrukken om ervoor te zorgen dat de Indexeer functie niet meer werkt als gevolg van een update voor de Indexing-pijp lijn. Het bijwerken van de gegevens bron connection string vereist bijvoorbeeld dat een indexer opnieuw wordt ingesteld en dat alle documenten opnieuw worden geïndexeerd wanneer de gegevens bron is gewijzigd. Als u de connection string echter alleen bijwerkt met een nieuwe sleutel, wilt u niet dat de wijziging resulteert in updates voor bestaande documenten. Het is echter mogelijk dat de Indexeer functie de cache en verrijkte documenten ongeldig moet maken, zelfs als er geen wijzigingen worden aangebracht in de indexerings pijplijn. Het is bijvoorbeeld mogelijk dat u de Indexeer functie ongeldig wilt maken als u een aangepaste vaardigheid opnieuw zou implementeren met een nieuw model en de vaardigheid wilt herhalen op al uw documenten.

### <a name="override-reset-requirement"></a>Vereiste voor overschrijven opnieuw instellen

Wanneer u wijzigingen aanbrengt in de pijp lijn voor het indexeren, moet een indexer opnieuw worden ingesteld op wijzigingen die resulteren in een ongeldige cache. Als u een wijziging aanbrengt in de Indexeer functie-pijp lijn en niet wilt dat de cache door de wijzigings tracering ongeldig wordt gemaakt, moet u de `ignoreResetRequirement` query string-para meter instellen op `true` voor bewerkingen op de Indexeer functie of gegevens bron.

### <a name="override-change-detection"></a>Wijzigings detectie onderdrukken

Bij het uitvoeren van updates voor de vaardig heden die ertoe leiden dat documenten worden gemarkeerd als inconsistent, bijvoorbeeld het bijwerken van een aangepaste vaardigheids-URL wanneer de vaardigheid opnieuw wordt geïmplementeerd, stelt u de `disableCacheReprocessingChangeDetection` query teken reeks parameter in op `true` op het niveau van de vaardig heden-updates.

### <a name="force-change-detection"></a>Wijzigings detectie forceren

Wanneer u wilt dat de Indexing-pijp lijn een wijziging in een externe entiteit herkent, zoals het implementeren van een nieuwe versie van een aangepaste vaardigheid, moet u de vaardig heden en ' touch ' de specifieke vaardigheid bijwerken door de vaardigheids definitie te bewerken, met name de URL die moet worden geforceerd detectie wijzigen en de cache voor die vaardigheid ongeldig maken.

## <a name="next-steps"></a>Volgende stappen

Dit artikel heeft betrekking op incrementele indexering voor Indexeer functies die vaardig heden bevatten. Als u uw kennis verder wilt afronden, raadpleegt u artikelen over het opnieuw indexeren in het algemeen, die van toepassing zijn op alle indexerings scenario's in azure Cognitive Search.

+ [Een Azure Cognitive search-index opnieuw samen stellen](search-howto-reindex.md). 
+ [Het indexeren van grote gegevens sets in Azure Cognitive Search](search-howto-large-index.md). 
