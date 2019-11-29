---
title: Azure Stream Analytics uitvoer naar Cosmos DB
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics kunt gebruiken om de uitvoer op te slaan in Azure Cosmos DB voor JSON-uitvoer, voor gegevens archivering en query's met lage latentie voor ongestructureerde JSON-gegevens.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: aa4ac011a7b6258958ac1ac176fd63b18a4ef856
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560190"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics uitvoer naar Azure Cosmos DB  
Stream Analytics kan gericht zijn op [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) voor json-uitvoer, waardoor gegevens worden gearchiveerd en query's met lage latentie worden uitgevoerd op ONgestructureerde JSON-gegevens. Dit document bevat enkele aanbevolen procedures voor het implementeren van deze configuratie.

Voor degenen die niet bekend zijn met Cosmos DB gaat u naar [het leer traject van Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) om aan de slag te gaan. 

> [!Note]
> Op dit moment ondersteunt Azure Stream Analytics alleen de verbinding met Azure Cosmos DB met behulp van **SQL API**.
> Andere Azure Cosmos DB Api's worden nog niet ondersteund. Als u Azure Stream Analytics naar de Azure Cosmos DB-accounts die zijn gemaakt met andere Api's, zijn de gegevens mogelijk niet op de juiste manier opgeslagen. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Basis beginselen van Cosmos DB als uitvoer doel
Met de Azure Cosmos DB uitvoer in Stream Analytics kunt u de resultaten van de stroom verwerking als JSON-uitvoer naar uw Cosmos DB container (s) schrijven. Stream Analytics maakt geen containers in uw data base, zodat u ze vooraf hoeft te maken. Dit is zo dat de facturerings kosten van Cosmos DB containers door u worden beheerd, zodat u de prestaties, consistentie en capaciteit van uw containers rechtstreeks kunt afstemmen met behulp van de [Cosmos DB-api's](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> U moet 0.0.0.0 toevoegen aan de lijst met toegestane IP-adressen van uw Azure Cosmos DB firewall.

Enkele van de Cosmos DB container opties worden hieronder beschreven.

## <a name="tune-consistency-availability-and-latency"></a>Consistentie, Beschik baarheid en latentie afstemmen
Om te voldoen aan de vereisten van uw toepassing, kunt u met Azure Cosmos DB de data base en containers nauw keuriger afstemmen en de beschik baarheid van consistentie, bewerkings periode, latentie en door Voer afzetten. Afhankelijk van de niveaus van de Lees consistentie die uw scenario nodig heeft voor lees-en schrijf latentie, kunt u een consistentie niveau voor uw database account kiezen. De door Voer kan worden verbeterd door het schalen van de aanvraag eenheden (RUs) op de container. Standaard maakt Azure Cosmos DB synchroon indexeren op elke ruwe bewerking naar uw container. Dit is een andere handige optie om de schrijf-en lees prestaties in Azure Cosmos DB te beheren. Raadpleeg het artikel [uw data base en query consistentie niveaus wijzigen](../cosmos-db/consistency-levels.md) voor meer informatie.

## <a name="upserts-from-stream-analytics"></a>Upsert van Stream Analytics
Stream Analytics integratie met Azure Cosmos DB kunt u records in uw container invoegen of bijwerken op basis van een bepaalde document-ID-kolom. Dit wordt ook wel een *Upsert*genoemd.

Stream Analytics gebruikt een optimistische upsert-benadering, waarbij updates alleen worden uitgevoerd wanneer insert mislukt met een document-ID-conflict. Met compatibiliteits niveau 1,0 wordt deze update uitgevoerd als een PATCH, waardoor het document gedeeltelijk kan worden bijgewerkt, dat wil zeggen dat er nieuwe eigenschappen worden toegevoegd of dat een bestaande eigenschap incrementeel wordt vervangen. Wijzigingen in de waarden van matrix eigenschappen in het JSON-document resulteren echter in de gehele matrix, die wordt overschreven, dat wil zeggen, de matrix is niet samengevoegd. Met 1,2 wordt het gedrag upsert gewijzigd om het document in te voegen of te vervangen. Dit wordt verder beschreven in de onderstaande sectie van het compatibiliteits niveau 1,2.

Als het binnenkomende JSON-document een bestaand ID-veld heeft, wordt dat veld automatisch gebruikt als de kolom document-ID in Cosmos DB en worden eventuele daaropvolgende schrijf bewerkingen als zodanig verwerkt, waardoor er een van de volgende situaties is:
- unieke Id's om in te voegen
- dubbele Id's en document-ID zijn ingesteld op ID-leads voor upsert
- dubbele Id's en document-ID zijn niet ingesteld op fout, na het eerste document

Als u <i>alle</i> documenten met een dubbele id wilt opslaan, wijzigt u de naam van het veld id in uw query (met het sleutel woord as) en laat Cosmos DB het veld id maken of de id vervangen door de waarde van een andere kolom (met behulp van het sleutel woord as of met behulp van de instelling ' document-id ').

## <a name="data-partitioning-in-cosmos-db"></a>Gegevens partitioneren in Cosmos DB
Azure Cosmos DB [onbeperkte](../cosmos-db/partition-data.md) containers zijn de aanbevolen benadering voor het partitioneren van uw gegevens, omdat Azure Cosmos DB automatisch partities schaalt op basis van uw werk belasting. Bij het schrijven naar een onbeperkt aantal containers, Stream Analytics gebruikt net zoveel parallelle schrijvers als de vorige query stap of het schema voor invoer partitie.
> [!NOTE]
> Op dit moment ondersteunt Azure Stream Analytics alleen onbeperkte containers met partitie sleutels op het hoogste niveau. `/region` wordt bijvoorbeeld ondersteund. Geneste partitie sleutels (bijvoorbeeld `/region/name`) worden niet ondersteund. 

Afhankelijk van de partitie sleutel die u kiest, kan deze _waarschuwing_worden weer gegeven:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Het is belang rijk dat u een partitie sleutel eigenschap kiest die een aantal afzonderlijke waarden heeft, en u kunt de werk belasting gelijkmatig verdelen over deze waarden. Als een natuurlijk artefact van partitioneren, worden aanvragen waarbij dezelfde partitie sleutel is betrokken, beperkt door de maximale door Voer van één partitie. Daarnaast is de opslag grootte voor documenten die deel uitmaken van dezelfde partitie sleutel beperkt tot 10 GB. Een ideale partitie sleutel is één die regel matig wordt weer gegeven als een filter in uw query's en voldoende kardinaliteit heeft om ervoor te zorgen dat uw oplossing schaalbaar is.

Een partitie sleutel is ook de grens voor trans acties in de opgeslagen procedures en triggers van DocumentDB. U moet de partitie sleutel kiezen, zodat documenten die in trans acties samen komen, dezelfde partitie sleutel waarde hebben. Het artikel [partitioneren in Cosmos DB](../cosmos-db/partitioning-overview.md) biedt meer informatie over het kiezen van een partitie sleutel.

Voor vaste Azure Cosmos DB-containers kunt Stream Analytics op geen enkele manier omhoog of omlaag schalen wanneer ze vol zijn. Ze hebben een maximum van 10 GB en een door Voer van 10.000 RU/s.  Als u de gegevens van een vaste container wilt migreren naar een onbeperkte container (bijvoorbeeld een met ten minste 1.000 RU/s en een partitie sleutel), moet u het [hulp programma voor gegevens migratie](../cosmos-db/import-data.md) of de [bibliotheek voor wijzigings invoer](../cosmos-db/change-feed.md)gebruiken.

De mogelijkheid om te schrijven naar meerdere vaste containers wordt afgeschaft en wordt niet aanbevolen voor het schalen van uw Stream Analytics-taak.

## <a name="improved-throughput-with-compatibility-level-12"></a>Verbeterde door Voer met compatibiliteits niveau 1,2
Met compatibiliteits niveau 1,2 ondersteunt Stream Analytics systeem eigen integratie om bulksgewijs te schrijven naar Cosmos DB. Dit maakt het mogelijk om effectief te Cosmos DB met het maximaliseren van de door Voer en het efficiënt afhandelen van bandbreedte aanvragen. Het verbeterde schrijf mechanisme is beschikbaar onder een nieuw compatibiliteits niveau als gevolg van een verschil in het upsert-gedrag.  Vóór 1,2 is het gedrag van de upsert om het document in te voegen of samen te voegen. Met 1,2 wordt het gedrag upsert gewijzigd om het document in te voegen of te vervangen.

Vóór 1,2 maakt gebruik van een aangepaste opgeslagen procedure voor het bulksgewijs upsert van documenten per partitie sleutel in Cosmos DB, waarbij een batch wordt geschreven als een trans actie. Zelfs wanneer één record een tijdelijke fout (beperking) oplegt, moet de hele batch opnieuw worden geprobeerd. Dit heeft scenario's gemaakt met zelfs redelijk langzamer beperkende taken. In de volgende vergelijking ziet u hoe dergelijke taken zich gedraagt met 1,2.

In het volgende voor beeld ziet u twee identieke Stream Analytics taken die van dezelfde event hub-invoer worden gelezen. Beide Stream Analytics-taken zijn [volledig gepartitioneerd](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) met een Passthrough-query en schrijven naar identieke CosmosDB-containers. De metrische gegevens aan de linkerkant zijn afkomstig uit de taak die is geconfigureerd met compatibiliteits niveau 1,0 en de waarden aan de rechter kant worden geconfigureerd met 1,2. De partitie sleutel van een Cosmos DB container is een unieke GUID die afkomstig is van de invoer gebeurtenis.

![vergelijking van metrische gegevens voor stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

De binnenkomende gebeurtenis snelheid in Event hub is 2x hoger dan Cosmos DB containers (20.000 RUs) die zijn geconfigureerd om te worden uitgesteld, waardoor de beperking wordt verwacht in Cosmos DB. De taak met 1,2, wordt echter constant geschreven met een hogere door Voer (uitvoer gebeurtenissen per minuut) en met een lager gemiddeld gebruik van%. In uw omgeving is dit verschil afhankelijk van meer factoren, zoals de keuze van de gebeurtenis indeling, invoer gebeurtenis/bericht grootte, partitie sleutels, query, enzovoort.

![vergelijking van metrische gegevens voor Cosmos db](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Met 1,2 is Stream Analytics intelligenter in het gebruik van 100% van de beschik bare door Voer in Cosmos DB met weinig herverzendingen tegen beperking/snelheids beperking. Dit biedt een betere ervaring voor andere werk belastingen, zoals query's die op de container worden uitgevoerd. Als u wilt uitschalen hoe ASA met Cosmos DB als Sink voor 1 KB aan 10.000 berichten/seconde wordt geschaald, is dit een [Azure-voorbeeld project](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) waarmee u dit kunt doen.
Houd er rekening mee dat Cosmos DB uitvoer doorvoer gelijk is aan 1,0 en 1,1. Omdat 1,2 momenteel niet de standaard waarde is, kunt u het [compatibiliteits niveau](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) voor een stream Analytics-taak instellen met behulp van de portal of met behulp van de [aanroep taak maken rest API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Het wordt *ten zeerste aanbevolen* om compatibiliteits niveau 1,2 in ASA met Cosmos DB te gebruiken.



## <a name="cosmos-db-settings-for-json-output"></a>Cosmos DB instellingen voor JSON-uitvoer

Als u Cosmos DB maakt als uitvoer in Stream Analytics wordt er een prompt gegenereerd voor informatie zoals hieronder wordt weer gegeven. Deze sectie bevat een uitleg van de definitie van de eigenschappen.

![uitvoer scherm van documentdb stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Veld           | Beschrijving|
|-------------   | -------------|
|Uitvoeralias    | Een alias om deze uitvoer in uw ASA-query te verwijzen.|
|Abonnement    | Kies het Azure-abonnement.|
|Account-id      | De naam of eind punt-URI van het Azure Cosmos DB-account.|
|Account sleutel     | De gedeelde toegangs sleutel voor het Azure Cosmos DB-account.|
|Database        | De naam van de Azure Cosmos DB-Data Base.|
|Containernaam | De container naam die moet worden gebruikt. `MyContainer` is een voor beeld van een geldige invoer: er moet een container met de naam `MyContainer` bestaan.  |
|Document-ID     | Optioneel. De kolom naam in uitvoer gebeurtenissen die wordt gebruikt als de unieke sleutel waarop INSERT-of update-bewerkingen moeten worden gebaseerd. Als dit veld leeg blijft, worden alle gebeurtenissen ingevoegd, zonder update optie.|

Zodra de Cosmos DB uitvoer is geconfigureerd, kan deze in de query worden gebruikt als het doel van een [into-instructie](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Wanneer u een Cosmos DB uitvoer als zodanig gebruikt, [moet een partitie sleutel expliciet worden ingesteld](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). De uitvoer record moet een hoofdletter gevoelige kolom bevatten met de naam na de partitie sleutel in Cosmos DB. Om meer parallel Lise ring te verkrijgen, kan voor de instructie een [partitie by-component](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) met dezelfde kolom zijn vereist.

**Voorbeeld query**:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Fout afhandeling en nieuwe pogingen

In het geval van een tijdelijke fout, wordt de service niet beschik baarheid of beperkt tijdens het verzenden van gebeurtenissen naar Cosmos DB, Stream Analytics voor onbepaalde tijd een nieuwe poging gedaan om de bewerking uit te voeren. Er zijn echter fouten waarvoor geen nieuwe pogingen worden gedaan. Deze zijn:

- Niet toegestaan (HTTP-fout code 401)
- NotFound (HTTP-fout code 404)
- Verboden (HTTP-fout code 403)
- Onjuiste aanvraag (HTTP-fout code 400)
