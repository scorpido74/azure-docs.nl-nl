---
title: Meldingen voor nieuwe functies
titleSuffix: Azure Cognitive Search
description: Aankondigingen van nieuwe en verbeterde functies, waaronder een service naam wijzigen van Azure Search naar Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 27dae07328af125c25512ab9f1eb81d0f4eda99b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271324"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Wat is er nieuw in azure Cognitive Search

Meer informatie over wat er nieuw is in de service. Bladwijzer deze pagina om up-to-date te blijven met de service.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nieuwe service naam

De naam van Azure Search wordt nu gewijzigd in **Azure Cognitive Search** om het uitgebreide (nog optioneel) gebruik van cognitieve vaardig heden en AI-verwerking in kern bewerkingen weer te geven. API-versies, NuGet-pakketten, naam ruimten en eind punten zijn ongewijzigd. Nieuwe en bestaande Zoek oplossingen worden niet beïnvloed door de wijziging van de service naam.

## <a name="feature-announcements"></a>Aankondigingen van functies

### <a name="february-2020"></a>Februari 2020

+ [PII-detectie (preview)](cognitive-search-skill-pii-detection.md) is een cognitieve vaardigheid die wordt gebruikt tijdens de indexering die persoonlijke gegevens ophaalt uit een invoer tekst en die u de mogelijkheid biedt om de tekst op verschillende manieren te maskeren.

+ [Zoek opdracht voor aangepaste entiteiten (preview)](cognitive-search-skill-custom-entity-lookup.md ) zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Met deze lijst worden alle documenten met alle overeenkomende entiteiten labeld. De vaardigheid ondersteunt ook een mate van fuzzy matching die kan worden toegepast om overeenkomsten te vinden die vergelijkbaar zijn, maar niet helemaal precies. 

### <a name="january-2020"></a>Januari 2020

+ Door de [klant beheerde versleutelings sleutels](search-security-manage-encryption-keys.md) zijn nu algemeen beschikbaar. Als u REST gebruikt, kunt u de functie openen met behulp van `api-version=2019-05-06`. Voor beheerde code is het juiste pakket nog steeds [.NET SDK-versie 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , ook al is de functie niet beschikbaar in de preview-fase. 

+ Persoonlijke toegang tot een zoek service is beschikbaar via twee mechanismen, zowel momenteel als preview-versie:

  + U kunt de toegang tot specifieke IP-adressen beperken met behulp van de beheer REST API `api-version=2019-10-01-Preview` om de service te maken. De preview-API bevat nieuwe eigenschappen **IpRule** en **NetworkRuleSet** in de [CreateOrUpdate-API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Deze preview-functie is beschikbaar in geselecteerde regio's. Zie [How to use the Management rest API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)voor meer informatie.

  + Momenteel beschikbaar via een preview-versie van beperkte toegang kunt u een Azure Search service inrichten die Azure private endpoint ondersteunt voor verbindingen van clients in hetzelfde virtuele netwerk. Zie [een persoonlijk eind punt maken voor een beveiligde verbinding](service-create-private-endpoint.md)voor meer informatie.

### <a name="december-2019"></a>December 2019

+ [App maken (preview)](search-create-app-portal.md) is een nieuwe wizard in de portal waarmee een DOWNLOADBAAR HTML-bestand wordt gegenereerd. Het bestand wordt geleverd met een Inge sloten script dat een operationele ' localhost '-stijl web-app weergeeft, gebonden aan een index in uw zoek service. Pagina's kunnen in de wizard worden geconfigureerd en bevatten een zoek balk, een resultaat gebied, een navigatie naar een terzijde en typeahead-query ondersteuning. U kunt de HTML offline wijzigen om de werk stroom of het uiterlijk te verlengen of aan te passen.

+ [Een persoonlijk eind punt maken voor beveiligde verbindingen (preview)](service-create-private-endpoint.md) in dit artikel wordt uitgelegd hoe u een persoonlijke koppeling kunt instellen voor beveiligde verbindingen met uw zoek service. Deze preview-functie is beschikbaar op aanvraag en maakt gebruik van [Azure private link](../private-link/private-link-overview.md) en [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) als onderdeel van de oplossing.

### <a name="november-2019---ignite-conference"></a>November 2019-Ignite-conferentie

+ [Incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) voegt caching en statefullness toe aan een verrijkings pijplijn, zodat u kunt werken met specifieke stappen of fasen zonder dat inhoud verloren gaat die al is verwerkt. Voorheen moest elke wijziging in een verrijkings pijplijn een volledig opnieuw opgebouwd. Met incrementele verrijking blijft de uitvoer van een kost bare analyse, met name afbeeldings analyse, behouden.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Document extractie (preview)](cognitive-search-skill-document-extraction.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren, waarmee u de inhoud van een bestand kunt extra heren uit een vakkennisset. Het kraken van documenten is eerder voorgekomen voordat de vaardig heden werden uitgevoerd. Als u deze vaardigheid hebt toegevoegd, kunt u deze bewerking ook uitvoeren binnen de vaardig heden-uitvoering.

+ [Tekst omzetting](cognitive-search-skill-text-translation.md) is een cognitieve vaardigheid die wordt gebruikt tijdens de indexering die tekst evalueert en, voor elke record, de tekst die wordt vertaald naar de opgegeven doel taal.

+ [Power bi sjablonen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kunnen uw visualisaties en analyse van verrijkte inhoud in een kennis archief op Power bi bureau blad. Deze sjabloon is ontworpen voor Azure-tabel prognoses die zijn gemaakt met behulp van de [wizard gegevens importeren](knowledge-store-create-portal.md).

+ [Azure data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin-API (preview)](search-howto-index-cosmosdb.md)en [Cosmos DB Cassandra-API (preview)](search-howto-index-cosmosdb.md) worden nu ondersteund in Indexeer functies. U kunt zich aanmelden met [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview). U ontvangt een bevestigings-e-mail zodra u deze hebt geaccepteerd in het preview-programma.

### <a name="july-2019"></a>Juli 2019

+ Algemeen beschikbaar in [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Service-updates

[Service-update aankondigingen](https://azure.microsoft.com/updates/?product=search&status=all) voor Azure Cognitive Search zijn te vinden op de Azure-website.