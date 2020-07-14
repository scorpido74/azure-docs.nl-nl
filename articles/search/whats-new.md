---
title: Nieuwe functies in Azure Cognitive Search
description: Aankondigingen van nieuwe en verbeterde functies, waaronder een wijziging van de servicenaam van Azure Search in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/30/2020
ms.openlocfilehash: 078892691bfaec62f71f9d601a42de3f80221149
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958154"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nieuwe functies in Azure Cognitive Search

Meer informatie over nieuwe functies in de service. Voeg een bladwijzer toe aan deze pagina om up-to-date te blijven over de service.

## <a name="feature-announcements"></a>Aankondigingen van functies

### <a name="june-2020"></a>Juni 2020

+ [Kennisarchief](knowledge-store-concept-intro.md) is nu algemeen beschikbaar.

+ [Search Service REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) is de nieuwe stabiele versie van de REST API's. Naast het kennisarchief bevat deze algemeen beschikbare versie verbeteringen wat betreft de relevantie en scoren van de zoekresultaten.

+ Het nieuwe algoritme voor relevantieclassificatie is nu [BM25](https://en.wikipedia.org/wiki/Okapi_BM25) voor alle nieuwe services die u maakt. Voor bestaande services kunt u het gebruiken door de eigenschap `similarity` in te stellen op indexvelden. Deze eigenschap is algemeen beschikbaar.

+ De `executionEnvironment` van nieuwe indexeerfuncties kan expliciet worden ingesteld op `private`. Deze mogelijkheid ondersteunt toegang van indexeerfuncties tot externe gegevens via privé-eindpunten, en is algemeen beschikbaar.

+ [Azure Machine Learning (AML)](cognitive-search-aml-skill.md) is een nieuw vaardigheidstype voor het integreren van een deductie-eindpunt van Azure Machine Learning. De portal-ervaring ondersteunt de detectie en integratie van uw Azure Machine Learning-eindpunt binnen een Cognitive Search-vaardighedenset. Voor de detectie moeten uw Cognitive Search- en Azure ML-services in hetzelfde abonnement zijn geïmplementeerd. Deze vaardigheid is algemeen beschikbaar. Ga aan de slag met [deze zelfstudie](cognitive-search-tutorial-aml-custom-skill.md).

### <a name="may-2020-microsoft-build"></a>Mei 2020 (Microsoft Build)

+ De functie [Foutopsporingssessies](cognitive-search-debug-session.md) is nu beschikbaar als preview-versie. Foutopsporingssessies bieden een portalinterface waarin u problemen kunt onderzoeken en oplossen met een vaardighedenset. Fixes die zijn gemaakt in de foutopsporingssessie, kunnen worden opgeslagen in de productievaardighedenset. Ga aan de slag met [deze zelfstudie](cognitive-search-tutorial-debug-sessions.md).

+ Scherm een zoekservice-eindpunt af van het openbare internet door [IP-regels te configureren voor inkomende firewall-ondersteuning](service-configure-firewall.md) of door gebruik te maken van [Azure Private Link voor een privé-zoekeindpunt](service-create-private-endpoint.md). Beide mogelijkheden zijn algemeen beschikbaar.

+ Gebruik een [door het systeem beheerde identiteit (preview)](search-howto-managed-identities-data-sources.md) om een verbinding met een Azure-gegevensbron in te stellen voor indexeren. Geldt voor [indexeerfuncties](search-indexer-overview.md) die inhoud opnemen uit Azure-gegevensbronnen, zoals Azure SQL Database, Azure Cosmos DB en Azure Storage.

+ Wijzig de basis voor het berekenen van zoekscores, van per shard in alle shards, met behulp van de queryparameters [sessionId](index-similarity-and-scoring.md) en [scoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics). Deze parameters zijn algemeen beschikbaar.

+ Voeg een [featuresMode (preview)](index-similarity-and-scoring.md#featuresMode-param) queryparameter toe om een relevantiescore uit te breiden om meer details weer te geven: vergelijkbaarheidsscore per veld, termfrequentie per veld, en gematcht aantal unieke tokens per veld. U kunt deze gegevenspunten gebruiken in aangepaste scorealgoritmen. Zie [Add machine learning (LearnToRank) to search relevance](https://github.com/Azure-Samples/search-ranking-tutorial) voor een voorbeeld waarin deze mogelijkheid wordt gedemonstreerd.

### <a name="march-2020"></a>Maart 2020

+ Met [Systeemeigen voorlopig verwijderde blobs (preview)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) herkent de Azure Blob Storage-indexeerfunctie in Azure Cognitive Search de blobs die de status Voorlopig verwijderd hebben, en wordt het bijbehorende zoekdocument verwijderd tijdens het indexeren.

+ Nieuwe stabiele [REST API voor beheer (13-03-2020)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) is nu algemeen beschikbaar. 

### <a name="february-2020"></a>Februari 2020

+ [PII-detectie (preview)](cognitive-search-skill-pii-detection.md) is een cognitieve vaardigheid die wordt gebruikt tijdens de indexering. Hiermee worden persoonsgegevens opgehaald uit een invoertekst en krijgt u de mogelijkheid om deze op verschillende manieren in deze tekst te maskeren.

+ Met [Aangepaste entiteiten zoeken (preview)](cognitive-search-skill-custom-entity-lookup.md ) wordt gezocht naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Aan de hand van deze lijst worden alle documenten met overeenkomende entiteiten gelabeld. De vaardigheid ondersteunt ook het zoeken naar fuzzy overeenkomsten, waarmee overeenkomsten worden gevonden die vergelijkbaar zijn, maar niet helemaal exact. 

### <a name="january-2020"></a>Januari 2020

+ [Door de klant beheerde versleutelingssleutels](search-security-manage-encryption-keys.md) zijn nu algemeen beschikbaar. Als u REST gebruikt, kunt u de functie gebruiken met `api-version=2019-05-06` of later. Voor beheerde code is het juiste pakket nog steeds [.NET SDK versie 8.0-preview](search-dotnet-sdk-migration-version-9.md), ook al is de preview-fase van de functie voorbij. 

+ Privétoegang tot een zoekservice is beschikbaar via twee mechanismen, beide momenteel als preview-versie:

  + U kunt de toegang tot specifieke IP-adressen beperken met de REST API voor beheer `api-version=2019-10-01-Preview` om de service te maken. De preview-API bevat de nieuwe eigenschappen **IpRule** en **NetworkRuleSet** in [CreateOrUpdate-API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Deze preview-functie is beschikbaar in bepaalde regio's. Zie [De REST API voor beheer gebruiken](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api) voor meer informatie.

### <a name="december-2019"></a>December 2019

+ [Demo-app maken (preview)](search-create-app-portal.md) is een nieuwe wizard in de portal waarmee een downloadbaar HTML-bestand wordt gegenereerd met query-toegang (alleen-lezen) tot een index. Het bestand wordt geleverd met een ingesloten script waarmee een operationele 'localhost' -achtige web-app wordt weergegeven die is gebonden aan een index in uw zoekservice. Pagina's kunnen worden geconfigureerd in de wizard en kunnen een zoekbalk, een resultaatgebied, navigatie in de zijbalk en ondersteuning voor typeahead-query's bevatten. U kunt de HTML offline wijzigen om de werkstroom of weergave uit te breiden of aan te passen. Een demo-app kan niet gemakkelijk worden uitgebreid met de beveiligings- en hosting-lagen die gewoonlijk nodig zijn in productiescenario's. U dient dit te beschouwen als een validatie- en testhulpmiddel, en niet als een kortere weg naar een volledige client-app.

+ In [Een privé-eindpunt maken voor veilige verbindingen (preview)](service-create-private-endpoint.md) wordt uitgelegd hoe u Private Link instelt voor veilige verbindingen met uw zoekservice. Deze preview-functie is op aanvraag beschikbaar en gebruikt [Azure Private Link](../private-link/private-link-overview.md) en [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) als onderdeel van de oplossing.

### <a name="november-2019---ignite-conference"></a>November 2019: Ignite Conference

+ Met [Incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) voegt u opslaan in cache en statefullness toe aan een verrijkingspijplijn, zodat u aan specifieke stappen of fasen kunt werken zonder inhoud te verliezen die al is verwerkt. Voorheen moest een verrijkingspijplijn volledig opnieuw worden gebouwd na een wijziging. Met stapsgewijze verrijking blijft de uitvoer van kostbare analyses, met name afbeeldingsanalyses, behouden.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Documentextractie (preview)](cognitive-search-skill-document-extraction.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren. U kunt hiermee de inhoud van een bestand extraheren uit een vaardighedenset. Voorheen werd een document alleen gekraakt voordat een vaardighedenset werd uitgevoerd. Door de toevoeging van deze vaardigheid kun je deze bewerking ook uitvoeren binnen de uitvoering van de vaardighedenset.

+ [Tekstomzetting](cognitive-search-skill-text-translation.md) is een cognitieve vaardigheid die wordt gebruikt tijdens indexering. Hiermee wordt tekst geëvalueerd en wordt voor elke record de tekst geretourneerd die is vertaald in de opgegeven doeltaal.

+ Met [Power BI-sjablonen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kunt u aan de slag met uw visualisaties en analyses van verrijkte inhoud in een kennisarchief in Power BI Desktop. Deze sjabloon is ontworpen voor Azure-tabelprojecties die zijn gemaakt met de [wizard Gegevens importeren](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md) en [Cosmos DB Cassandra API (preview)](search-howto-index-cosmosdb.md) worden nu ondersteund in indexeerfuncties. U kunt zich registreren via [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview). Je ontvangt een bevestigingsmail zodra je bent geaccepteerd in het previewprogramma.

### <a name="july-2019"></a>Juli 2019

+ Algemeen beschikbaar in [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nieuwe servicenaam

Azure Search heet nu **Azure Cognitive Search** vanwege het uitgebreide (maar optionele) gebruik van cognitieve vaardigheden en AI-verwerking in kernbewerkingen. API-versies, NuGet-pakketten, naamruimten en eindpunten zijn ongewijzigd. De wijziging van de servicenaam is niet van invloed op nieuwe en bestaande zoekoplossingen.

## <a name="service-updates"></a>Service-updates

[Aankondigingen van service-updates](https://azure.microsoft.com/updates/?product=search&status=all) voor Azure Cognitive Search kunt u vinden op de Azure-website.