---
title: Een grote gegevensset indexeren met ingebouwde Indexeer functies-Azure Search
description: Meer informatie over strategieën voor het indexeren van grote gegevens of reken kundige, intensieve indexering via de batch modus, het opruimen en technieken voor geplande, parallelle en gedistribueerde indexeringen.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a98d716562f53488e9adb5d485a1dbf7fafc3102
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648158"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Grote gegevens sets indexeren in Azure Search

Naarmate gegevens volumes groeien of verwerkings behoeften wijzigen, is het mogelijk dat standaard indexerings strategieën niet meer praktisch zijn. Voor Azure Search zijn er verschillende benaderingen voor grotere gegevens sets, variërend van de manier waarop u een aanvraag voor gegevens uploadt, om een serverspecifieke Indexeer functie te gebruiken voor geplande en gedistribueerde werk belastingen.

Dezelfde technieken voor grote gegevens zijn ook van toepassing op langlopende processen. Met name de stappen die worden beschreven in [parallelle indexering](#parallel-indexing) zijn handig voor reken kundige indexen, zoals het uitvoeren van een afbeeldings analyse of natuurlijke taal verwerking in [cognitieve Zoek pijplijnen](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Batch indexering

Een van de eenvoudigste mechanismen voor het indexeren van een grotere gegevensset is het indienen van meerdere documenten of records in één aanvraag. Zolang de volledige Payload minder is dan 16 MB, kan een aanvraag tot 1000 documenten in een bulk upload bewerking verwerken. Uitgaande van de [rest API documenten toevoegen of bijwerken](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), pakt u 1000-documenten in de hoofd tekst van de aanvraag.

Batch indexering wordt geïmplementeerd voor afzonderlijke aanvragen met behulp van REST of .NET of via Indexeer functies. Een paar Indexeer functies kunnen worden gebruikt onder verschillende limieten. Met de indexering van Azure Blob wordt de Batch grootte bij 10 documenten ingesteld op het herkennen van de grotere gemiddelde document grootte. Voor Indexeer functies op basis van de [rest API Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ), kunt u het `BatchSize` argument instellen om deze instelling aan te passen, zodat deze beter overeenkomt met de kenmerken van uw gegevens. 

> [!NOTE]
> Als u de document grootte wilt behouden, moet u niet-bevraagde gegevens uit de aanvraag uitsluiten. Afbeeldingen en andere binaire gegevens kunnen niet rechtstreeks worden doorzocht en mogen niet worden opgeslagen in de index. Als u niet-query bare gegevens wilt integreren in Zoek resultaten, moet u een niet-doorzoekbaar veld definiëren waarin een URL-verwijzing naar de resource wordt opgeslagen.

## <a name="add-resources"></a>Resources toevoegen

Services die zijn ingericht in een van de [standaard prijs categorieën](search-sku-tier.md) , hebben vaak weinig gebruikte capaciteit voor zowel opslag als werk belastingen (query's of indexeringen), waardoor [het verhogen van de partitie en replica](search-capacity-planning.md) een duidelijke oplossing voor grotere gegevens sets. Voor de beste resultaten moet u beide bronnen gebruiken: partities voor opslag en replica's voor de gegevens opname.

Het verg Roten van replica's en partities zijn factureer bare gebeurtenissen waarmee uw kosten worden verhoogd, maar tenzij u continu wilt indexeren onder maximale belasting, kunt u een schaal voor de duur van het indexerings proces toevoegen en vervolgens de resource niveaus omlaag aanpassen nadat het indexeren is uitgevoerd geïnstalleerd.

## <a name="use-indexers"></a>Indexeer functies gebruiken

[Indexeer functies](search-indexer-overview.md) worden gebruikt voor het verkennen van externe gegevens bronnen voor Doorzoek bare inhoud. Hoewel het niet specifiek is bedoeld voor grootschalig indexeren, zijn verschillende indexerings mogelijkheden bijzonder nuttig voor grotere gegevens sets:

+ Met planners kunt u indexeren op regel matige intervallen uitdelen, zodat u deze in de loop van de tijd kan verdelen.
+ Geplande indexering kan worden hervat op het laatste bekende stop punt. Als een gegevens bron niet volledig in een 24-uurs venster wordt verkend, wordt de indexering hervat op dag twee op elke locatie waar deze is gebleven.
+ Het partitioneren van gegevens in kleinere afzonderlijke gegevens bronnen maakt parallelle verwerking mogelijk. U kunt een grote gegevensset opdelen in kleinere gegevens sets en vervolgens meerdere definities voor gegevens bronnen maken die parallel kunnen worden geïndexeerd.

> [!NOTE]
> Indexeer functies zijn gegevens bron-specifiek, dus het gebruik van een indexerings benadering is alleen haalbaar voor geselecteerde gegevens bronnen op Azure: [SQL database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Geplande indexering

De planning van de Indexeer functie is een belang rijk mechanisme voor het verwerken van grote gegevens sets, evenals trage processen zoals het analyseren van afbeeldingen in een cognitieve Zoek pijplijn. De verwerking van de Indexeer functie werkt binnen een 24-uurs venster. Als de verwerking niet binnen 24 uur wordt voltooid, kan het gedrag van het plannen van de Indexeer functie worden gebruikt voor uw voor deel. 

Geplande indexering begint op specifieke intervallen, met een taak die doorgaans wordt voltooid voordat het volgende geplande interval wordt hervat. Als de verwerking echter niet binnen het interval wordt voltooid, stopt de indexer (omdat deze verouderd is). Bij het volgende interval wordt de verwerking hervat, waar deze zich de laatste keer bevindt, waarbij het systeem bijhoudt waar dit gebeurt. 

In de praktijk is het mogelijk om de Indexeer functie op een 24-uurs schema in te stellen. Wanneer het indexeren voor de volgende 24 uur wordt hervat, wordt het opnieuw gestart bij het laatst bekende goede document. Op deze manier kan een Indexeer functie worden gebruikt tijdens een document achterstand over een reeks dagen tot alle niet-verwerkte documenten worden verwerkt. Zie voor meer informatie over deze aanpak [grote gegevens sets indexeren in Azure Blob-opslag](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Zie voor meer informatie over het instellen van planningen in het algemeen [Indexeer functie maken rest API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax) of Zie [Indexeer functies plannen voor Azure Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Parallelle indexering

Een strategie voor parallelle indexering is gebaseerd op het indexeren van meerdere gegevens bronnen in gelijktijdig, waarbij elke definitie van de gegevens bron een subset van de gegevens bevat. 

Voor niet-routine matige, reken bare indexerings vereisten, zoals OCR op gescande documenten in een cognitieve Zoek pijplijn, afbeeldings analyse of natuurlijke taal verwerking, is een strategie voor parallelle indexering de juiste aanpak voor het volt ooien van een langlopend proces in de kortste periode. Als u query aanvragen kunt elimineren of beperken, is parallelle indexering van een service die niet tegelijkertijd query's afhandelt, uw beste strategie optie voor het werken met een grote inhoud van trage verwerking. 

Parallelle verwerking heeft de volgende elementen:

+ Verdeel bron gegevens over meerdere containers of meerdere virtuele mappen binnen dezelfde container. 
+ Wijs elke mini maal gegevensset toe aan een eigen [gegevens bron](https://docs.microsoft.com/rest/api/searchservice/create-data-source), gekoppeld aan een eigen [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Voor cognitieve Zoek opdrachten verwijzen we naar dezelfde [vaardig heden](https://docs.microsoft.com/rest/api/searchservice/create-skillset) in elke Indexeer functie definitie.
+ Schrijf naar dezelfde doel zoek index. 
+ Plannen dat alle Indexeer functies op hetzelfde moment worden uitgevoerd.

> [!NOTE]
> Azure Search biedt geen ondersteuning voor het toewijzen van replica's of partities aan specifieke werk belastingen. Het risico van zware gelijktijdige indexering is een overbelasting van uw systeem tot nadelige gevolgen voor de prestaties van query's. Als u een test omgeving hebt, implementeert u eerst parallelle indexeringen om de voor afwegingen te begrijpen.

### <a name="how-to-configure-parallel-indexing"></a>Parallelle indexering configureren

Voor Indexeer functies is de verwerkings capaciteit soepel gebaseerd op één indexerings subsysteem voor elke service-eenheid (SU) die wordt gebruikt door uw zoek service. Meerdere gelijktijdige Indexeer functies zijn mogelijk op Azure Search Services die zijn ingericht op basis-of standaard lagen met ten minste twee replica's. 

1. Controleer in de [Azure Portal](https://portal.azure.com)op de pagina **overzicht** van het zoek service dashboard de **prijs categorie** om te bevestigen dat deze parallelle indexering kan bevatten. De lagen basis en standaard bieden meerdere replica's.

2. Verhoog in **instellingen** > **schaal** [replica's](search-capacity-planning.md) voor parallelle verwerking: een extra replica voor elke Indexeer functie-workload. Geef een voldoende waarde voor het bestaande query volume. Het voldoet niet aan query werkbelastingen voor indexering is geen goede balans.

3. Gegevens distribueren naar meerdere containers op een niveau dat Azure Search Indexeer functies kunnen bereiken. Dit kan meerdere tabellen zijn in Azure SQL Database, meerdere containers in Azure Blob Storage of meerdere verzamelingen. Definieer één gegevens bron object voor elke tabel of container.

4. Meerdere Indexeer functies maken en plannen om parallel uit te voeren:

   + Ga ervan uit dat een service met zes replica's. U kunt zes Indexeer functies configureren, die zijn toegewezen aan een gegevens bron die een-zesde van de gegevensset bevat voor een 6 richtings splitsing van de gehele gegevensset. 

   + Wijs elke Indexeer functie aan dezelfde index toe. Voor cognitieve Zoek werkbelastingen wijst u elke Indexeer functie aan dezelfde vaardig heden toe.

   + In elke indexers definitie kunt u hetzelfde run-time uitvoerings patroon plannen. `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` Maakt bijvoorbeeld een planning voor 2018-05-15 op alle Indexeer functies, die worden uitgevoerd op een interval van acht uur.

Op de geplande tijd starten alle Indexeer functies, het laden van gegevens, het Toep assen van verrijkingen (als u een cognitieve Zoek pijplijn hebt geconfigureerd) en het schrijven naar de index. Azure Search de index niet vergrendelt voor updates. Gelijktijdige schrijf bewerkingen worden beheerd met een nieuwe poging als een bepaalde schrijf bewerking niet kan worden uitgevoerd bij de eerste poging.

> [!Note]
> Bij het verg Roten van replica's, overweeg dan het aantal partities te verhogen als de index grootte wordt geprojecteerd om aanzienlijk te verg Roten. Partities bevatten segmenten van geïndexeerde inhoud; Hoe meer partities u hebt, des te kleiner het segment dat elke partitie bevat om op te slaan.

## <a name="see-also"></a>Zie ook

+ [Overzicht van de indexeerfunctie](search-indexer-overview.md)
+ [Indexeren in de portal](search-import-data-portal.md)
+ [Indexeer functie Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeerfunctie](search-howto-index-cosmosdb.md)
+ [Indexeerfunctie voor Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexeerfunctie voor Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Beveiliging in Azure Search](search-security-overview.md)
