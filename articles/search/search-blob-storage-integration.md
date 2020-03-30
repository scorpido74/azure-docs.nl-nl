---
title: Zoeken met volledige tekst toevoegen aan Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Inhoud extraheren en structuur toevoegen aan Azure-blobs bij het maken van een zoekindex met volledige tekst in Azure Cognitive earch.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496463"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Zoeken in volledige tekst toevoegen aan Azure blob-gegevens met Azure Cognitive Search

Zoeken in de verschillende inhoudstypen die zijn opgeslagen in Azure Blob-opslag kan een moeilijk probleem zijn om op te lossen. U de inhoud van uw Blobs echter in slechts een paar klikken indexeren en doorzoeken met [Azure Cognitive Search.](search-what-is-azure-search.md) Azure Cognitive Search heeft ingebouwde integratie voor het indexeren van blob-opslag via een [*Blob-indexer*](search-howto-indexing-azure-blob-storage.md) die gegevensbronbewuste mogelijkheden toevoegt aan indexering.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Wat betekent het om zoeken in volledige tekst toe te voegen aan blobgegevens

Azure Cognitive Search is een cloudzoekservice die indexerings- en queryengines biedt die werken via door de gebruiker gedefinieerde indexen die worden gehost op uw zoekservice. Co-locating van uw doorzoekbare inhoud met de query-engine in de cloud is noodzakelijk voor de prestaties, terugkerende resultaten met een snelheid die gebruikers gewend zijn van zoekopdrachten.

Azure Cognitive Search integreert met Azure Blob-opslag op de indexeringslaag en importeert uw blob-inhoud als zoekdocumenten die zijn geïndexeerd *in omgekeerde indexen* en andere querystructuren die vrije formuliertekstquery's en filterexpressies ondersteunen. Omdat uw blob-inhoud is geïndexeerd in een zoekindex, kan toegang tot blob-inhoud gebruikmaken van het volledige scala aan queryfuncties in Azure Cognitive Search.

Zodra de index is gemaakt en ingevuld, bestaat deze onafhankelijk van uw blobcontainer, maar u indexeringsbewerkingen opnieuw uitvoeren om uw index te vernieuwen met wijzigingen in de onderliggende container. Tijdstempelinformatie over afzonderlijke blobs wordt gebruikt voor wijzigingsdetectie. U kiezen voor geplande uitvoering of on-demand indexering als het vernieuwingsmechanisme.

Invoer zijn uw blobs, in één container, in Azure Blob-opslag. Blobs kunnen bijna elke vorm van tekstgegevens zijn. Als uw blobs afbeeldingen bevatten, u [AI-verrijking](search-blob-ai-integration.md) toevoegen aan blobindexering om tekst uit afbeeldingen te maken en eruit te halen.

Uitvoer is altijd een Azure Cognitive Search-index, die wordt gebruikt voor snel zoeken naar tekst, ophalen en verkennen in clienttoepassingen. Daartussen zit de indexering van de pijplijnarchitectuur zelf. De pijplijn is gebaseerd op de *indexerfunctie,* die verder op in dit artikel wordt besproken.

## <a name="start-with-services"></a>Begin met services

U hebt Azure Cognitive Search en Azure Blob-opslag nodig. Binnen Blob-opslag hebt u een container nodig die broninhoud biedt.

U direct beginnen op de pagina Met uw opslagaccountportal. Klik op de linkernavigatiepagina onder **Blob-service** op **Azure Cognitive Search toevoegen** om een nieuwe service te maken of een bestaande service te selecteren. 

Zodra u Azure Cognitive Search aan uw opslagaccount hebt toegevoegd, u het standaardproces volgen om blobgegevens te indexeren. We raden de wizard **Gegevens importeren** in Azure Cognitive Search aan voor een eenvoudige eerste introductie of bel de REST API's met behulp van een tool zoals Postman. In deze zelfstudie u de REST-API aanroepen in Postman: [Indexen en semi-gestructureerde gegevens (JSON-blobs) in Azure Cognitive Search .](search-semi-structured-data.md) 

## <a name="use-a-blob-indexer"></a>Een Blob-indexer gebruiken

Een *indexer* is een gegevensbronbewuste subservice die is uitgerust met interne logica voor het samplen van gegevens, het lezen van metagegevens, het ophalen van gegevens en het serialiseren van gegevens uit native formaten in JSON-documenten voor latere import. 

Blobs in Azure Storage worden geïndexeerd met de [Azure Cognitive Search Blob-opslagindexeerder](search-howto-indexing-azure-blob-storage.md). U deze indexer aanroepen met de wizard **Gegevens importeren,** een REST-API of de .NET SDK. In code gebruikt u deze indexer door het type in te stellen en door verbindingsgegevens te verstrekken die een Azure Storage-account bevatten, samen met een blobcontainer. U uw blobs subseten door een virtuele map te maken, die u vervolgens als parameter doorgeven, of door te filteren op een bestandstypeextensie.

Een indexeerder doet het "document kraken", het openen van een blob om inhoud te inspecteren. Na het verbinden met de gegevensbron is dit de eerste stap in de pijplijn. Voor blobgegevens wordt dit waar PDF,office-documenten en andere inhoudstypen worden gedetecteerd. Het kraken van documenten met tekstextractie is geen kosten in rekening. Als uw blobs afbeeldingsinhoud bevatten, worden afbeeldingen genegeerd, tenzij u [AI-verrijking toevoegt.](search-blob-ai-integration.md) Standaardindexering is alleen van toepassing op tekstinhoud.

De Blob-indexer wordt geleverd met configuratieparameters en ondersteunt het bijhouden van wijzigingen als de onderliggende gegevens voldoende informatie bieden. Meer informatie over de kernfunctionaliteit vindt u in [Azure Cognitive Search Blob-opslagindexeerder](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Ondersteunde inhoudstypen

Als u een Blob-indexer over een container uitvoert, u tekst en metagegevens extraheren uit de volgende inhoudstypen met één query:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Blob-metagegevens indexeren

Een veelvoorkomend scenario waarmee het eenvoudig is om blobs van elk inhoudstype te sorteren, is het indexeren van zowel aangepaste metagegevens als systeemeigenschappen voor elke blob. Op deze manier wordt informatie voor alle blobs geïndexeerd, ongeacht het documenttype, opgeslagen in een index in uw zoekservice. Met behulp van uw nieuwe index u vervolgens overgaan tot sorteren, filteren en facet voor alle Blob-opslaginhoud.

### <a name="indexing-json-blobs"></a>JSON-blobs indexeren
Indexers kunnen worden geconfigureerd om gestructureerde inhoud te extraheren die is gevonden in blobs die JSON bevatten. Een indexeerder kan JSON-blobs lezen en de gestructureerde inhoud in de juiste velden van een zoekdocument ontleden. Indexers kunnen ook blobs maken die een array van JSON-objecten bevatten en elk element toewijzen aan een afzonderlijk zoekdocument. U een parsing-modus instellen om het type JSON-object dat door de indexeerder is gemaakt, te beïnvloeden.

## <a name="search-blob-content-in-a-search-index"></a>Blob-inhoud zoeken in een zoekindex 

De uitvoer van een indexering is een zoekindex, die wordt gebruikt voor interactieve verkenning met behulp van vrije tekst en gefilterde query's in een client-app. Voor de eerste verkenning en verificatie van inhoud raden we u aan om te beginnen met [Search Explorer](search-explorer.md) in de portal om de documentstructuur te onderzoeken. U [eenvoudige syntaxis,](query-simple-syntax.md) [syntaxis van volledige query](query-lucene-syntax.md)en [syntaxis van de filterexpressie](query-odata-filter-orderby-syntax.md) gebruiken in Zoekverkenner.

Een meer permanente oplossing is het verzamelen van query-invoer en presenteren van het antwoord als zoekresultaten in een clienttoepassing. In de volgende C#-zelfstudie wordt uitgelegd hoe u een zoektoepassing maakt: [Uw eerste toepassing maken in Azure Cognitive Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Volgende stappen

+ [Blobs uploaden, downloaden en aanbieden met de Azure-portal (Azure Blob-opslag)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Een blob-indexer instellen (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
