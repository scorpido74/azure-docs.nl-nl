---
title: Meldingen voor nieuwe functies
titleSuffix: Azure Cognitive Search
description: Aankondigingen van nieuwe en verbeterde functies, waaronder een service naam wijzigen van Azure Search naar Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f35fcc2604866c2ff5a330b65ae844be0eed9476
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422393"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Wat is er nieuw in azure Cognitive Search

Meer informatie over wat er nieuw is in de service. Bladwijzer deze pagina om up-to-date te blijven met de service.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nieuwe service naam voor Azure Search

De naam van Azure Search wordt nu gewijzigd in **Azure Cognitive Search** om het uitgebreide gebruik van cognitieve vaardig heden en AI-verwerking in kern bewerkingen weer te geven. Met cognitieve vaardig heden worden nieuwe mogelijkheden toegevoegd met behulp van AI strikt optioneel. U kunt Azure Cognitive Search zonder AI blijven gebruiken voor het bouwen van geavanceerde oplossingen voor zoeken in volledige tekst in een index die u in de Cloud maakt en beheert. 

API-versies, Nuget-pakketten, naam ruimten en eind punten zijn ongewijzigd. Uw bestaande Zoek oplossingen worden niet beïnvloed door de wijziging van de service naam.

## <a name="feature-announcements"></a>Aankondigingen van functies

4 november 2019-Ignite-conferentie

+ Met [incrementele indexering (preview)](cognitive-search-incremental-indexing-conceptual.md) kunt u kiezen welke stappen opnieuw moeten worden verwerkt wanneer u wijzigingen aanbrengt in een verrijkings pijplijn. Incrementeel indexeren is handig als u afbeeldings inhoud hebt die u eerder hebt geanalyseerd. De uitvoer van een kost bare analyse wordt opgeslagen en vervolgens gebruikt als basis voor aanvullende indexering of verrijking.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Document extractie (preview)](cognitive-search-skill-document-extraction.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren, waarmee u de inhoud van een bestand kunt extra heren uit een vakkennisset. Het kraken van documenten is eerder voorgekomen voordat de vaardig heden werden uitgevoerd. Als u deze vaardigheid hebt toegevoegd, kunt u deze bewerking ook uitvoeren binnen de vaardig heden-uitvoering.

+ [Tekst omzetting (preview)](cognitive-search-skill-text-translation.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren waarmee tekst wordt geëvalueerd en, voor elke record, de tekst wordt omgezet in de opgegeven doel taal.

+ [Power bi sjablonen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kunnen uw visualisaties en analyse van verrijkte inhoud in een kennis archief op Power bi bureau blad. Deze sjabloon is ontworpen voor Azure-tabel prognoses die zijn gemaakt met behulp van de [wizard gegevens importeren](knowledge-store-create-portal.md).

+ [Azure data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin-API (preview)](search-howto-index-cosmosdb.md)en [Cosmos DB Cassandra-API (preview)](search-howto-index-cosmosdb.md) worden nu ondersteund in Indexeer functies. U kunt zich aanmelden met [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview). U ontvangt een bevestigings-e-mail zodra u deze hebt geaccepteerd in het preview-programma.

24 2019 juli

+ Algemeen beschikbaar in [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Service-updates

[Service-update aankondigingen](https://azure.microsoft.com/updates/?product=search&status=all) voor Azure Cognitive Search zijn te vinden op de Azure-website.