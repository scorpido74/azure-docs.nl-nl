---
title: Meldingen voor nieuwe functies
titleSuffix: Azure Cognitive Search
description: Aankondigingen van nieuwe en verbeterde functies, waaronder een service naam wijzigen van Azure Search naar Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0ce2884a2382c7dff2bdb90bd92934609675f314
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834376"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Wat is er nieuw in azure Cognitive Search

Meer informatie over wat er nieuw is in de service. Bladwijzer deze pagina om up-to-date te blijven met de service.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nieuwe service naam voor Azure Search

De naam van Azure Search wordt nu gewijzigd in **Azure Cognitive Search** om het uitgebreide gebruik van cognitieve vaardig heden en AI-verwerking in kern bewerkingen weer te geven. Met cognitieve vaardig heden worden nieuwe mogelijkheden toegevoegd met behulp van AI strikt optioneel. U kunt Azure Cognitive Search zonder AI blijven gebruiken voor het bouwen van geavanceerde oplossingen voor zoeken in volledige tekst in een index die u in de Cloud maakt en beheert. 

API-versies, Nuget-pakketten, naam ruimten en eind punten zijn ongewijzigd. Uw bestaande Zoek oplossingen worden niet beïnvloed door de wijziging van de service naam.

## <a name="feature-announcements"></a>Aankondigingen van functies

### <a name="january-2020"></a>Januari 2020

+ Door de [klant beheerde versleutelings sleutels](search-security-manage-encryption-keys.md) zijn nu algemeen beschikbaar. Als u REST gebruikt, kunt u de functie openen met behulp van `api-version=2019-05-06`. Voor beheerde code is het juiste pakket nog steeds [.NET SDK-versie 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , ook al is de functie niet beschikbaar in de preview-fase. 

+ *Beperkte IP-toegang en persoonlijk eind punt (preview)* voor een Search service-eind punt is nu beschikbaar in **API-Version = 2019-10 -01-preview**. U kunt een beveiligd eind punt instellen met behulp van de nieuwe eigenschappen **IpRule** en **NetworkRuleSet** in de rest API voor het [maken of bijwerken](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) van beheer. Zie [How to use the Management rest API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)voor meer informatie over API-versies en regionale Beschik baarheid.

### <a name="december-2019"></a>December 2019

+ [App maken (preview)](search-create-app-portal.md) is een nieuwe wizard in de portal waarmee een DOWNLOADBAAR HTML-bestand wordt gegenereerd. Het bestand wordt geleverd met een Inge sloten script dat een operationele ' localhost '-stijl web-app weergeeft, gebonden aan een index in uw zoek service. Pagina's kunnen in de wizard worden geconfigureerd en bevatten een zoek balk, een resultaat gebied, een navigatie naar een terzijde en typeahead-query ondersteuning. U kunt de HTML offline wijzigen om de werk stroom of het uiterlijk te verlengen of aan te passen.

### <a name="november-2019---ignite-conference"></a>November 2019-Ignite-conferentie

+ [Incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) voegt caching en statefullness toe aan een verrijkings pijplijn, zodat u kunt werken met specifieke stappen of fasen zonder dat inhoud verloren gaat die al is verwerkt. Voorheen moest elke wijziging in een verrijkings pijplijn een volledig opnieuw opgebouwd. Met incrementele verrijking blijft de uitvoer van een kost bare analyse, met name afbeeldings analyse, behouden.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Document extractie (preview)](cognitive-search-skill-document-extraction.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren, waarmee u de inhoud van een bestand kunt extra heren uit een vakkennisset. Het kraken van documenten is eerder voorgekomen voordat de vaardig heden werden uitgevoerd. Als u deze vaardigheid hebt toegevoegd, kunt u deze bewerking ook uitvoeren binnen de vaardig heden-uitvoering.

+ [Tekst omzetting (preview)](cognitive-search-skill-text-translation.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren waarmee tekst wordt geëvalueerd en, voor elke record, de tekst wordt omgezet in de opgegeven doel taal.

+ [Power bi sjablonen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kunnen uw visualisaties en analyse van verrijkte inhoud in een kennis archief op Power bi bureau blad. Deze sjabloon is ontworpen voor Azure-tabel prognoses die zijn gemaakt met behulp van de [wizard gegevens importeren](knowledge-store-create-portal.md).

+ [Azure data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin-API (preview)](search-howto-index-cosmosdb.md)en [Cosmos DB Cassandra-API (preview)](search-howto-index-cosmosdb.md) worden nu ondersteund in Indexeer functies. U kunt zich aanmelden met [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview). U ontvangt een bevestigings-e-mail zodra u deze hebt geaccepteerd in het preview-programma.

### <a name="july-2019"></a>Juli 2019

+ Algemeen beschikbaar in [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Service-updates

[Service-update aankondigingen](https://azure.microsoft.com/updates/?product=search&status=all) voor Azure Cognitive Search zijn te vinden op de Azure-website.