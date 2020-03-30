---
title: Grote gegevensset indexeren met ingebouwde indexeerders
titleSuffix: Azure Cognitive Search
description: Strategieën voor het indexeren van grote gegevens of rekenintensieve indexering via batchmodus, resourcing en technieken voor geplande, parallelle en gedistribueerde indexering.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190967"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Grote gegevenssets indexeren in Azure Cognitive Search

Naarmate de gegevensvolumes toenemen of de verwerkingsbehoeften veranderen, u merken dat eenvoudige of standaardindexeringsstrategieën niet langer praktisch zijn. Voor Azure Cognitive Search zijn er verschillende benaderingen voor het aanpassen van grotere gegevenssets, variërend van hoe u een aanvraag voor het uploaden van gegevens structureert tot het gebruik van een bronspecifieke indexeerder voor geplande en gedistribueerde workloads.

Dezelfde technieken zijn ook van toepassing op langlopende processen. In het bijzonder zijn de stappen die in [parallelle indexering](#parallel-indexing) worden beschreven nuttig voor rekenintensieve indexering, zoals beeldanalyse of natuurlijke taalverwerking in een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md).

In de volgende secties worden drie technieken onderzocht voor het indexeren van grote hoeveelheden gegevens.

## <a name="option-1-pass-multiple-documents"></a>Optie 1: Meerdere documenten doorgeven

Een van de eenvoudigste mechanismen voor het indexeren van een grotere gegevensset is het indienen van meerdere documenten of records in één aanvraag. Zolang de volledige payload minder dan 16 MB is, kan een aanvraag tot 1000 documenten verwerken in een bulkuploadbewerking. Deze limieten zijn van toepassing of u de [API Documenten REST toevoegen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) of de [indexmethode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) gebruikt in de .NET SDK. Voor beide API's zou u 1000 documenten verpakken in de hoofdtekst van elk verzoek.

Batchindexering wordt geïmplementeerd voor individuele aanvragen met REST of .NET of via indexers. Een paar indexeerders werken onder verschillende limieten. In het bijzonder stelt Azure Blob-indexering batchgrootte in op 10 documenten als erkenning van de grotere gemiddelde documentgrootte. Voor indexers op basis van de [API Voor indexeren kunt](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)u het `BatchSize` argument instellen om deze instelling aan te passen aan de kenmerken van uw gegevens. 

> [!NOTE]
> Als u de grootte van het document laag wilt houden, moet u voorkomen dat niet-opvraagbare gegevens aan een index worden toegevoegd. Afbeeldingen en andere binaire gegevens zijn niet direct doorzoekbaar en mogen niet worden opgeslagen in de index. Als u niet-opvraagbare gegevens wilt integreren in zoekresultaten, moet u een niet-doorzoekbaar veld definiëren dat een URL-verwijzing naar de bron opslaat.

## <a name="option-2-add-resources"></a>Optie 2: Resources toevoegen

Services die zijn ingericht op een van de [standaardprijsniveaus](search-sku-tier.md) hebben vaak onderbenutte capaciteit voor zowel opslag als workloads (query's of indexering), waardoor [het verhogen van de partitie en replica telt](search-capacity-planning.md) een voor de hand liggende oplossing voor het accommoderen van grotere gegevenssets. Voor de beste resultaten hebt u beide resources nodig: partities voor opslag en replica's voor het werk voor het innemen van gegevens.

Het verhogen van replica's en partities zijn factureerbare gebeurtenissen die uw kosten verhogen, maar tenzij u continu indexeert onder maximale belasting, u schaal toevoegen voor de duur van het indexeringsproces en vervolgens resourceniveaus naar beneden aanpassen nadat de indexering is Klaar.

## <a name="option-3-use-indexers"></a>Optie 3: Indexers gebruiken

[Indexers](search-indexer-overview.md) worden gebruikt om ondersteunde Azure-gegevensbronnen te crawlen voor doorzoekbare inhoud. Hoewel niet specifiek bedoeld voor grootschalige indexering, zijn verschillende indexermogelijkheden bijzonder nuttig voor het accommoderen van grotere gegevenssets:

+ Planners u pakket uit indexeren op regelmatige tijdstippen, zodat u het verspreiden over de tijd.
+ Geplande indexering kan worden hervat bij het laatst bekende stoppunt. Als een gegevensbron niet volledig binnen een periode van 24 uur is gecrawld, wordt de indexer op dag twee hervat met indexeren, waar deze ook was gebleven.
+ Het verdelen van gegevens in kleinere afzonderlijke gegevensbronnen maakt parallelle verwerking mogelijk. U brongegevens opsplitsen in kleinere componenten, zoals in meerdere containers in Azure Blob-opslag, en vervolgens overeenkomstige, meerdere [gegevensbronobjecten](https://docs.microsoft.com/rest/api/searchservice/create-data-source) maken in Azure Cognitive Search die parallel kunnen worden geïndexeerd.

> [!NOTE]
> Indexers zijn gegevensbronspecifiek, dus het gebruik van een indexerbenadering is alleen haalbaar voor geselecteerde gegevensbronnen op Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob-opslag,](search-howto-indexing-azure-blob-storage.md) [Tabelopslag](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Geplande indexering

Indexerplanning is een belangrijk mechanisme voor het verwerken van grote gegevenssets, evenals traag lopende processen zoals beeldanalyse in een cognitieve zoekpijplijn. Indexerverwerking werkt binnen een periode van 24 uur. Als de verwerking niet binnen 24 uur is voltooid, kan het gedrag van indexerplanning in uw voordeel werken. 

Volgens het ontwerp begint de geplande indexering met specifieke intervallen, waarbij een taak doorgaans wordt voltooid voordat deze wordt hervat met het volgende geplande interval. Als de verwerking echter niet binnen het interval wordt voltooid, stopt de indexer (omdat de tijd opis). Bij het volgende interval wordt de verwerking hervat waar het voor het laatst is gebleven, waarbij het systeem bijhoudt waar dat gebeurt. 

In de praktijk, voor index belastingen verspreid over meerdere dagen, u de indexer op een 24-uurs schema. Wanneer de indexering wordt hervat voor de volgende 24-uurscyclus, wordt deze opnieuw gestart bij het laatst bekende goede document. Op deze manier kan een indexeerder zich een weg banen door een documentachterstand gedurende een reeks dagen totdat alle onverwerkte documenten zijn verwerkt. Zie [Grote gegevenssets indexeren in Azure Blob-opslag](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)voor meer informatie over deze aanpak. Zie [API voor indexeren maken](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) of [indexeren voor Azure Cognitive Search voor](search-howto-schedule-indexers.md)meer informatie over het instellen van schema's in het algemeen.

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Parallelle indexering

Een parallelle indexeringsstrategie is gebaseerd op het indexeren van meerdere gegevensbronnen in unino, waarbij elke gegevensbrondefinitie een subset van de gegevens opgeeft. 

Voor niet-routinematige, rekenintensieve indexeringsvereisten - zoals OCR op gescande documenten in een pijplijn voor cognitief onderzoek, beeldanalyse of natuurlijke taalverwerking - is een parallelle indexeringsstrategie vaak de juiste aanpak voor het voltooien van een langlopend proces in de kortste tijd. Als u queryaanvragen elimineren of verminderen, is parallelle indexering van een service die query's niet tegelijkertijd verwerkt, uw beste strategieoptie voor het verwerken van een grote hoeveelheid slowprocessinginhoud. 

Parallelle verwerking heeft de volgende elementen:

+ Brongegevens onderverdelen tussen meerdere containers of meerdere virtuele mappen in dezelfde container. 
+ Breng elke mini-gegevensset in kaart met zijn eigen [gegevensbron](https://docs.microsoft.com/rest/api/searchservice/create-data-source), gekoppeld aan zijn eigen [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Raadpleeg voor cognitief zoeken dezelfde [vaardigheden in](https://docs.microsoft.com/rest/api/searchservice/create-skillset) elke indexerdefinitie.
+ Schrijf in dezelfde doelzoekindex. 
+ Plan alle indexers op hetzelfde moment uit te voeren.

> [!NOTE]
> In Azure Cognitive Search u geen afzonderlijke replica's of partities toewijzen aan indexering of queryverwerking. Het systeem bepaalt hoe resources worden gebruikt. Als u de impact op de queryprestaties wilt begrijpen, u proberen parallelle indexering in een testomgeving te gebruiken voordat u deze in productie rolt.  

### <a name="how-to-configure-parallel-indexing"></a>Parallelle indexering configureren

Voor indexers is de verwerkingscapaciteit losjes gebaseerd op één indexersubsysteem voor elke service-eenheid (SU) die door uw zoekservice wordt gebruikt. Meerdere gelijktijdige indexeerders zijn mogelijk op Azure Cognitive Search-services die zijn ingericht op basis- of standaardlagen met ten minste twee replica's. 

1. Controleer in de [Azure-portal](https://portal.azure.com)op de **pagina** Overzicht van uw zoekservicedashboard de **prijslaag** om te controleren of deze geschikt is voor parallelle indexering. Zowel Basic- als Standard-lagen bieden meerdere replica's.

2. Verhoog in > **Instellingenschaal** [replica's](search-capacity-planning.md) voor parallelle verwerking: één extra replica voor elke indexerwerkbelasting. **Settings** Laat een voldoende aantal achter voor het bestaande queryvolume. Het opofferen van queryworkloads voor indexering is geen goede afweging.

3. Gegevens distribueren naar meerdere containers op een niveau dat Azure Cognitive Search-indexeerders kunnen bereiken. Dit kunnen meerdere tabellen zijn in Azure SQL Database, meerdere containers in Azure Blob-opslag of meerdere verzamelingen. Definieer één gegevensbronobject voor elke tabel of container.

4. Meerdere indexers maken en plannen om parallel te lopen:

   + Neem een service met zes replica's aan. Configureer zes indexers, elk toegewezen aan een gegevensbron met een zesde van de gegevensset voor een 6-wegsplitsing van de gehele gegevensset. 

   + Wijs elke indexeerder op dezelfde index. Voor cognitieve zoekworkloads wijst u elke indexeerder naar dezelfde vaardigheden.

   + Plan binnen elke indexerdefinitie hetzelfde run-time uitvoeringspatroon. Hiermee `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` maakt u bijvoorbeeld een schema op 2018-05-15 op alle indexeerders, die met intervallen van acht uur worden uitgevoerd.

Op de geplande tijd beginnen alle indexeerders met de uitvoering, laden gegevens, het toepassen van verrijkingen (als u een cognitieve zoekpijplijn hebt geconfigureerd) en schrijven naar de index. Azure Cognitive Search vergrendelt de index niet voor updates. Gelijktijdige schrijfbewerkingen worden beheerd, met opnieuw proberen als een bepaalde schrijven niet lukt bij de eerste poging.

> [!Note]
> Bij het verhogen van replica's u overwegen het aantal partities te verhogen als de indexgrootte naar verwachting aanzienlijk zal toenemen. Partities slaan segmenten van geïndexeerde inhoud op; hoe meer partities je hebt, hoe kleiner het segment dat je moet opslaan.

## <a name="see-also"></a>Zie ook

+ [Overzicht van de indexeerfunctie](search-indexer-overview.md)
+ [Indexering in de portal](search-import-data-portal.md)
+ [Azure SQL Database-indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexeerfunctie voor Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexeerfunctie voor Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexeerfunctie voor Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Beveiliging in Azure Cognitive Search](search-security-overview.md)
