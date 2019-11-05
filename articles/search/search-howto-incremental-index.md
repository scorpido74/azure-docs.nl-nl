---
title: Incrementele indexering van verrijkte inhoud op basis van wijzigingen instellen
titleSuffix: Azure Cognitive Search
description: Schakel het bijhouden van wijzigingen in en behoud de status van verrijkte inhoud voor beheerde verwerking in een cognitieve vaardigheidset.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ac082d6ecb6624dc0d5bc0ab927ff8b91ebdabce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512182"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Incrementele indexering van verrijkte documenten instellen in azure Cognitive Search

In dit artikel wordt beschreven hoe u status en caching toevoegt aan verrijkte documenten die worden verplaatst via een Azure Cognitive Search-verrijkings pijplijn, zodat u documenten stapsgewijs kunt indexeren vanuit een van de ondersteunde gegevens bronnen. Een vaardig heden is standaard stateless en het wijzigen van een deel van de samen stelling vereist een volledige herinstallatie van de Indexeer functie. Met incrementele indexering kan de Indexeer functie bepalen welke delen van de pijp lijn zijn gewijzigd, de bestaande verrijkingen voor ongewijzigde delen opnieuw gebruiken en verrijkingen worden herzien voor de stappen die gewijzigd kunnen worden. Inhoud in de cache wordt geplaatst in Azure Storage.

Als u niet bekend bent met het instellen van Indexeer functies, start u met [Indexeer functie-overzicht](search-indexer-overview.md) en gaat u verder met [vaardig heden](cognitive-search-working-with-skillsets.md) voor meer informatie over verrijkings pijplijnen. Zie [Incrementeel indexeren](cognitive-search-incremental-indexing-conceptual.md)voor meer achtergrond informatie over de belangrijkste concepten.

Incrementeel indexeren is geconfigureerd met behulp van de [Search rest API-Version = 2019-05 -06-preview](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations).

> [!NOTE]
> Deze functie is nog niet beschikbaar in de portal en moet worden gebruikt via een programma.
>

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

Bewerk de reactie van de GET-aanvraag om de eigenschap `cache` toe te voegen aan de Indexeer functie. Het cache object vereist slechts één eigenschap en de connection string een Azure Storage-account.

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

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
