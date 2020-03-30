---
title: Aankondigingen van nieuwe functies
titleSuffix: Azure Cognitive Search
description: Aankondigingen van nieuwe en verbeterde functies, waaronder een service die de naam van Azure Search wijzigt in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: 475f89fc5b33948864fd83c39ee8058ab6908cad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80247194"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nieuwe functies in Azure Cognitive Search

Meer informatie over wat er nieuw is in de service. Bladwijzer deze pagina om op de hoogte te blijven van de service.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nieuwe servicenaam

Azure Search is nu omgedoopt tot **Azure Cognitive Search** om het uitgebreide (maar optionele) gebruik van cognitieve vaardigheden en AI-verwerking in kernbewerkingen weer te geven. API-versies, NuGet-pakketten, naamruimten en eindpunten zijn ongewijzigd. Nieuwe en bestaande zoekoplossingen worden niet beïnvloed door de wijziging van de servicenaam.

## <a name="feature-announcements"></a>Functie-aankondigingen

### <a name="march-2020"></a>Maart 2020

+ [Native blob soft delete (preview)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) betekent dat de Azure Blob Storage-indexer in Azure Cognitive Search blobs herkent die zich in een zachte verwijderde status bevinden en het bijbehorende zoekdocument tijdens het indexeren verwijdert.

+ Nieuwe stabiele [Management REST API (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) is nu beschikbaar. 

### <a name="february-2020"></a>Februari 2020

+ [PII Detection (preview)](cognitive-search-skill-pii-detection.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren die persoonlijk identificeerbare informatie uit een invoertekst haalt en u de mogelijkheid geeft om deze op verschillende manieren uit die tekst te maskeren.

+ [Aangepaste entiteit opzoeken (voorbeeld)](cognitive-search-skill-custom-entity-lookup.md ) zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinnen. Met behulp van deze lijst worden alle documenten labels met alle overeenkomende entiteiten. De vaardigheid ondersteunt ook een mate van fuzzy matching die kan worden toegepast om overeenkomsten te vinden die vergelijkbaar zijn, maar niet helemaal exact. 

### <a name="january-2020"></a>Januari 2020

+ [Door de klant beheerde versleutelingssleutels](search-security-manage-encryption-keys.md) zijn nu algemeen beschikbaar. Als u REST gebruikt, hebt u `api-version=2019-05-06`toegang tot de functie via . Voor beheerde code is het juiste pakket nog steeds [.NET SDK-versie 8.0-preview,](search-dotnet-sdk-migration-version-9.md) ook al is de functie niet meer van de proef. 

+ Privétoegang tot een zoekservice is beschikbaar via twee mechanismen, beide momenteel in preview:

  + U de toegang tot specifieke IP-adressen `api-version=2019-10-01-Preview` beperken door de API BeheerREST te gebruiken om de service te maken. De preview-API heeft nieuwe **eigenschappen IpRule** en **NetworkRuleSet** in [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Deze voorbeeldfunctie is beschikbaar in geselecteerde regio's. Zie [De API BeheerREST gebruiken](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)voor meer informatie.

  + Momenteel beschikbaar via een voorbeeld met beperkte toegang, u een Azure Search-service inrichten die Azure Private Endpoint ondersteunt voor verbindingen van clients in hetzelfde virtuele netwerk. Zie [Een privéeindpunt maken voor een beveiligde verbinding voor](service-create-private-endpoint.md)meer informatie.

### <a name="december-2019"></a>December 2019

+ [App maken (voorbeeld)](search-create-app-portal.md) is een nieuwe wizard in de portal die een downloadbaar HTML-bestand genereert. Het bestand wordt geleverd met ingesloten script dat een operationele "localhost"-stijl web-app, gebonden aan een index op uw zoekservice maakt. Pagina's kunnen worden geconfigureerd in de wizard en kunnen een zoekbalk, resultaatgebied, navigatie op de zijbalk en ondersteuning voor typende query's bevatten. U de HTML offline wijzigen om de werkstroom of het uiterlijk uit te breiden of aan te passen.

+ [Maak een privéeindpunt voor beveiligde verbindingen (voorbeeld)](service-create-private-endpoint.md) en legt uit hoe u een privékoppeling instelt voor beveiligde verbindingen met uw zoekservice. Deze voorbeeldfunctie is op aanvraag beschikbaar en maakt gebruik van [Azure Private Link](../private-link/private-link-overview.md) en Azure Virtual [Network](../virtual-network/virtual-networks-overview.md) als onderdeel van de oplossing.

### <a name="november-2019---ignite-conference"></a>November 2019 - Ignite Conference

+ [Incrementele verrijking (voorbeeld)](cognitive-search-incremental-indexing-conceptual.md) voegt caching en statefullness toe aan een verrijkingspijplijn, zodat u werken aan specifieke stappen of fasen zonder dat inhoud die al is verwerkt, verloren gaat. Voorheen vereiste elke wijziging in een verrijkingspijplijn een volledige herbouw. Met incrementele verrijking blijft de uitvoer van kostbare analyse, met name beeldanalyse, behouden.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Documentextractie (voorbeeld)](cognitive-search-skill-document-extraction.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren waarmee u de inhoud van een bestand binnen een skillset extraheren. Voorheen traden het kraken van documenten alleen op voordat de skillset-uitvoering plaatsvond. Met de toevoeging van deze vaardigheid u deze bewerking ook uitvoeren binnen de uitvoering van de skillset.

+ [Tekstvertaling](cognitive-search-skill-text-translation.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren en die tekst evalueert en voor elke record de vertaalde tekst teruggeeft naar de opgegeven doeltaal.

+ [Power BI-sjablonen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kunnen uw visualisaties en analyses van verrijkte inhoud starten in een kennisarchief op Power BI-bureaublad. Deze sjabloon is ontworpen voor Azure-tabelprojecties die zijn gemaakt via de [wizard Gegevens importeren](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md)en [Cosmos DB Cassandra API (preview)](search-howto-index-cosmosdb.md) worden nu ondersteund in indexers. U zich aanmelden via [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview). Je ontvangt een bevestigingsmail zodra je bent toegelaten tot het previewprogramma.

### <a name="july-2019"></a>Juli 2019

+ Algemeen beschikbaar in [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Service-updates

[Service-update-aankondigingen](https://azure.microsoft.com/updates/?product=search&status=all) voor Azure Cognitive Search zijn te vinden op de Azure-website.