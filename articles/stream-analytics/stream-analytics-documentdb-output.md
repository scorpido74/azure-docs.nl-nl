---
title: Azure Stream Analytics-uitvoer naar Cosmos DB
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics gebruiken om op te slaan van de uitvoer naar Azure Cosmos DB voor JSON-uitvoer, voor het archiveren van gegevens en query's met lage latentie voor niet-gestructureerde JSON-gegevens.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062009"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics-uitvoer naar Azure Cosmos DB  
Stream Analytics kunt richten [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) inschakelen voor JSON-uitvoer, gegevens archiveren en lage latentie-query's voor niet-gestructureerde JSON-gegevens. In dit document staan enkele aanbevolen procedures voor het implementeren van deze configuratie.

Voor gebruikers die niet bekend met Cosmos DB bent, kijk eens [leertraject voor Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) aan de slag. 

> [!Note]
> Op dit moment ondersteunt Azure Stream Analytics alleen verbinding met Azure Cosmos DB met behulp van **SQL API**.
> Andere Azure Cosmos DB-API's zijn nog niet ondersteund. Als u punt Azure Stream Analytics met de Azure Cosmos DB-accounts die zijn gemaakt met andere API's, zijn de gegevens mogelijk niet juist opgeslagen. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Basisbeginselen van het Cosmos DB als een bestemming voor uitvoer
Met de Azure Cosmos DB uitvoer in Stream Analytics kunt u de resultaten van de stroom verwerking als JSON-uitvoer naar uw Cosmos DB container (s) schrijven. Stream Analytics maakt geen containers in uw data base, zodat u ze vooraf hoeft te maken. Dit is zo dat de facturerings kosten van Cosmos DB containers door u worden beheerd, zodat u de prestaties, consistentie en capaciteit van uw containers rechtstreeks kunt afstemmen met behulp van de [Cosmos DB-api's](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> U moet 0.0.0.0 toevoegen aan de lijst met toegestane IP-adressen van uw Azure Cosmos DB firewall.

Enkele van de Cosmos DB container opties worden hieronder beschreven.

## <a name="tune-consistency-availability-and-latency"></a>Consistentie, beschikbaarheid en latentie afstemmen
Om te voldoen aan de vereisten van uw toepassing, kunt u met Azure Cosmos DB de data base en containers nauw keuriger afstemmen en de beschik baarheid van consistentie, bewerkings periode, latentie en door Voer afzetten. Afhankelijk van welke mate van lezen van consistentie op basis van de behoeften van uw scenario lezen en schrijven latentie, dat kunt u een consistentieniveau kiezen voor uw databaseaccount. De door Voer kan worden verbeterd door het schalen van de aanvraag eenheden (RUs) op de container. Standaard maakt Azure Cosmos DB synchroon indexeren op elke ruwe bewerking naar uw container. Dit is een andere handige optie voor het beheren van de prestaties voor schrijven/lezen in Azure Cosmos DB. Raadpleeg voor meer informatie de [wijzigen van uw database en query-consistentieniveaus](../cosmos-db/consistency-levels.md) artikel.

## <a name="upserts-from-stream-analytics"></a>Upsert-bewerking van Stream Analytics
Stream Analytics integratie met Azure Cosmos DB kunt u records in uw container invoegen of bijwerken op basis van een bepaalde document-ID-kolom. Dit wordt ook wel een *Upsert*.

Stream Analytics maakt gebruik van een benadering optimistische upsert waar de updates worden alleen uitgevoerd als invoegen is mislukt met een conflict met Document-ID. Met compatibiliteits niveau 1,0 wordt deze update uitgevoerd als een PATCH, waardoor het document gedeeltelijk kan worden bijgewerkt, dat wil zeggen dat er nieuwe eigenschappen worden toegevoegd of dat een bestaande eigenschap incrementeel wordt vervangen. Echter, wijzigingen in de waarden van Matrixeigenschappen in uw JSON-document, resulteren in de volledige matrix ophalen overschreven, dat wil zeggen, de matrix is niet samengevoegd. Met 1,2 wordt het gedrag upsert gewijzigd om het document in te voegen of te vervangen. Dit wordt verder beschreven in de onderstaande sectie van het compatibiliteits niveau 1,2.

Als het binnenkomende JSON-document een bestaande id-veld heeft, dat veld automatisch als de Document-ID-kolom in Cosmos DB gebruikt wordt en alle daaropvolgende schrijfbewerkingen worden verwerkt als zodanig leidt tot een van deze situaties:
- de unieke id's leiden om in te voegen
- dubbele id's en 'Document-ID' ingesteld op 'ID' leidt tot upsert
- dubbele id's en Document-ID niet set leidt tot een fout, na het eerste document

Als u wilt opslaan <i>alle</i> documenten met inbegrip van de items met een dubbele ID, wijzig de naam van het ID-veld in de query (met het sleutelwoord AS) en laat Cosmos DB maakt het ID-veld of de ID vervangen door een andere kolom-waarde (met behulp van de AS-trefwoord of door met behulp van de instelling 'Document-ID').

## <a name="data-partitioning-in-cosmos-db"></a>Partitioneren van gegevens in Cosmos DB
Azure Cosmos DB [onbeperkte](../cosmos-db/partition-data.md) containers zijn de aanbevolen benadering voor het partitioneren van uw gegevens, omdat Azure Cosmos DB automatisch partities schaalt op basis van uw werk belasting. Bij het schrijven naar een onbeperkt aantal containers, Stream Analytics gebruikt net zoveel parallelle schrijvers als de vorige query stap of het schema voor invoer partitie.
> [!NOTE]
> Op dit moment ondersteunt Azure Stream Analytics alleen onbeperkte containers met partitie sleutels op het hoogste niveau. Bijvoorbeeld, `/region` wordt ondersteund. Geneste partitiesleutels (bijvoorbeeld `/region/name`) worden niet ondersteund. 

Afhankelijk van de partitie sleutel die u kiest, kan deze _waarschuwing_worden weer gegeven:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Het is belang rijk dat u een partitie sleutel eigenschap kiest die een aantal afzonderlijke waarden heeft, en u kunt de werk belasting gelijkmatig verdelen over deze waarden. Als een natuurlijk artefact van partitioneren, worden aanvragen waarbij dezelfde partitie sleutel is betrokken, beperkt door de maximale door Voer van één partitie. Daarnaast is de opslag grootte voor documenten die deel uitmaken van dezelfde partitie sleutel beperkt tot 10 GB. Een ideale partitie sleutel is één die regel matig wordt weer gegeven als een filter in uw query's en voldoende kardinaliteit heeft om ervoor te zorgen dat uw oplossing schaalbaar is.

Een partitie sleutel is ook de grens voor trans acties in de opgeslagen procedures en triggers van DocumentDB. U moet de partitie sleutel kiezen, zodat documenten die in trans acties samen komen, dezelfde partitie sleutel waarde hebben. Het artikel [partitioneren in Cosmos DB](../cosmos-db/partitioning-overview.md) biedt meer informatie over het kiezen van een partitie sleutel.

Voor vaste Azure Cosmos DB-containers kunt Stream Analytics op geen enkele manier omhoog of omlaag schalen wanneer ze vol zijn. Ze hebben een limiet van 10 GB en doorvoer van 10.000 RU/s.  Als u wilt de gegevens van een vaste container migreren naar een onbeperkte container (bijvoorbeeld, één met ten minste 1000 RU/s en een partitiesleutel), die u wilt gebruiken de [hulpprogramma voor gegevensmigratie](../cosmos-db/import-data.md) of de [wijzigingenfeed bibliotheek](../cosmos-db/change-feed.md).

De mogelijkheid om te schrijven naar meerdere vaste containers wordt afgeschaft en wordt niet aanbevolen voor het schalen van uw Stream Analytics-taak.

## <a name="improved-throughput-with-compatibility-level-12"></a>Verbeterde door Voer met compatibiliteits niveau 1,2
Met compatibiliteits niveau 1,2 ondersteunt Stream Analytics systeem eigen integratie om bulksgewijs te schrijven naar Cosmos DB. Dit maakt het mogelijk om effectief te Cosmos DB met het maximaliseren van de door Voer en het efficiënt afhandelen van bandbreedte aanvragen. Het verbeterde schrijf mechanisme is beschikbaar onder een nieuw compatibiliteits niveau als gevolg van een verschil in het upsert-gedrag.  Vóór 1,2 is het gedrag van de upsert om het document in te voegen of samen te voegen. Met 1,2 wordt het gedrag upsert gewijzigd om het document in te voegen of te vervangen.

Vóór 1,2 maakt gebruik van een aangepaste opgeslagen procedure voor het bulksgewijs upsert van documenten per partitie sleutel in Cosmos DB, waarbij een batch wordt geschreven als een trans actie. Zelfs wanneer één record een tijdelijke fout (beperking) oplegt, moet de hele batch opnieuw worden geprobeerd. Dit heeft scenario's gemaakt met zelfs redelijk langzamer beperkende taken. In de volgende vergelijking ziet u hoe dergelijke taken zich gedraagt met 1,2.

In het volgende voor beeld ziet u twee identieke Stream Analytics taken die van dezelfde event hub-invoer worden gelezen. Beide Stream Analytics-taken zijn [volledig](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) gepartitioneerd met een Passthrough-query en schrijven naar identieke CosmosDB-containers. De metrische gegevens aan de linkerkant zijn afkomstig uit de taak die is geconfigureerd met compatibiliteits niveau 1,0 en de waarden aan de rechter kant worden geconfigureerd met 1,2. De partitie sleutel van een Cosmos DB container is een unieke GUID die afkomstig is van de invoer gebeurtenis.

![vergelijking van metrische gegevens voor stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

De binnenkomende gebeurtenis snelheid in Event hub is 2x hoger dan Cosmos DB containers (20.000 RUs) die zijn geconfigureerd om te worden uitgesteld, waardoor de beperking wordt verwacht in Cosmos DB. De taak met 1,2, wordt echter constant geschreven met een hogere door Voer (uitvoer gebeurtenissen per minuut) en met een lager gemiddeld gebruik van%. In uw omgeving is dit verschil afhankelijk van meer factoren, zoals de keuze van de gebeurtenis indeling, invoer gebeurtenis/bericht grootte, partitie sleutels, query, enzovoort.

![vergelijking van metrische gegevens voor Cosmos db](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Met 1,2 is Stream Analytics intelligenter in het gebruik van 100% van de beschik bare door Voer in Cosmos DB met weinig herverzendingen tegen beperking/snelheids beperking. Dit biedt een betere ervaring voor andere werk belastingen, zoals query's die op de container worden uitgevoerd. Als u wilt uitschalen hoe ASA met Cosmos DB als Sink voor 1 KB aan 10.000 berichten/seconde wordt geschaald, is dit een [Azure-voorbeeld project](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) waarmee u dit kunt doen.
Houd er rekening mee dat Cosmos DB uitvoer doorvoer gelijk is aan 1,0 en 1,1. Omdat 1,2 momenteel niet de standaard waarde is, kunt u het [compatibiliteits niveau](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) voor een stream Analytics-taak instellen met behulp van de portal of met behulp van de [aanroep taak maken rest API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Het wordt *ten zeerste aanbevolen* om compatibiliteits niveau 1,2 in ASA met Cosmos DB te gebruiken.



## <a name="cosmos-db-settings-for-json-output"></a>Cosmos DB-instellingen voor JSON-uitvoer

Het maken van Cosmos DB als uitvoer in Stream Analytics, genereert een prompt voor meer informatie, zoals hieronder wordt weergegeven. In deze sectie bevat een uitleg van de definitie van de eigenschappen.

![documentdb stream analytics uitvoerscherm](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Veld           | Description|
|-------------   | -------------|
|Uitvoeralias    | Een alias om deze uitvoer in uw ASA-query te verwijzen.|
|Subscription    | Kies het Azure-abonnement.|
|Account-id      | De naam of eind punt-URI van het Azure Cosmos DB-account.|
|Accountcode     | De gedeelde toegangs sleutel voor het Azure Cosmos DB-account.|
|Database        | De naam van de Azure Cosmos DB-Data Base.|
|Containernaam | De container naam die moet worden gebruikt. `MyContainer`is een voor beeld van een geldige invoer: `MyContainer` er moet één container zijn met de naam.  |
|Document-id     | Optioneel. De naam van de kolom in uitvoergebeurtenissen dat wordt gebruikt als de unieke sleutel bij welke invoegen of bijwerken bewerkingen moeten worden gebaseerd. Als u niets opgeeft, wordt alle gebeurtenissen ingevoegd, zonder optie update.|

## <a name="error-handling-and-retries"></a>Fout afhandeling en nieuwe pogingen

In het geval van een tijdelijke fout, wordt de service niet beschik baarheid of beperkt tijdens het verzenden van gebeurtenissen naar Cosmos DB, Stream Analytics voor onbepaalde tijd een nieuwe poging gedaan om de bewerking uit te voeren. Er zijn echter fouten waarvoor geen nieuwe pogingen worden gedaan. Deze zijn:

- Niet toegestaan (HTTP-fout code 401)
- NotFound (HTTP-fout code 404)
- Verboden (HTTP-fout code 403)
- Onjuiste aanvraag (HTTP-fout code 400)
