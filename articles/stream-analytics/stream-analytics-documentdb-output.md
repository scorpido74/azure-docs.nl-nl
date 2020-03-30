---
title: Azure Stream Analytics-uitvoer naar Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics gebruiken om uitvoer op te slaan naar Azure Cosmos DB voor JSON-uitvoer, voor gegevensarchivering en query's met lage latentie op ongestructureerde JSON-gegevens.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254441"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics-uitvoer naar Azure Cosmos DB  
Azure Stream Analytics kan [azure cosmos DB](https://azure.microsoft.com/services/documentdb/) targeten voor JSON-uitvoer, waardoor gegevensarchivering en query's met lage latentie op ongestructureerde JSON-gegevens mogelijk worden. Dit document bevat een aantal aanbevolen procedures voor het implementeren van deze configuratie.

Als u niet bekend bent met Azure Cosmos DB, raadpleegt u de [Azure Cosmos DB-documentatie](https://docs.microsoft.com/azure/cosmos-db/) om aan de slag te gaan. 

> [!Note]
> Op dit moment ondersteunt Stream Analytics de verbinding met Azure Cosmos DB alleen via de *SQL API.*
> Andere Azure Cosmos DB API's worden nog niet ondersteund. Als u Stream Analytics aanAzure Cosmos DB-accounts aanwijst die zijn gemaakt met andere API's, worden de gegevens mogelijk niet goed opgeslagen. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Basisbeginselen van Azure Cosmos DB als uitvoerdoel
Met de Azure Cosmos DB-uitvoer in Stream Analytics u uw streamverwerkingsresultaten als JSON-uitvoer in uw Azure Cosmos DB-containers schrijven. 

Stream Analytics maakt geen containers in uw database. In plaats daarvan moet u ze vooraf maken. Vervolgens u de factureringskosten van Azure Cosmos DB-containers beheren. U ook de prestaties, consistentie en capaciteit van uw containers rechtstreeks afstemmen met behulp van de [Azure Cosmos DB API's.](https://msdn.microsoft.com/library/azure/dn781481.aspx)

> [!Note]
> U moet 0.0.0.0 toevoegen aan de lijst met toegestane IP's van uw Azure Cosmos DB-firewall.

In de volgende secties worden enkele containeropties voor Azure Cosmos DB beschreven.

## <a name="tuning-consistency-availability-and-latency"></a>Consistentie, beschikbaarheid en latentie afstemmen
Om aan uw toepassingsvereisten te voldoen, u met Azure Cosmos DB de database en containers verfijnen en afwegingen maken tussen consistentie, beschikbaarheid, latentie en doorvoer. 

Afhankelijk van de niveaus van leesconsistentie die uw scenario nodig heeft ten opzichte van lees- en schrijflatentie, u een consistentieniveau kiezen voor uw databaseaccount. U de doorvoer verbeteren door Request Units (RU's) op de container op te schalen. 

Azure Cosmos DB maakt standaard ook synchrone indexering op elke CRUD-bewerking in uw container mogelijk. Dit is een andere handige optie om schrijf-/leesprestaties in Azure Cosmos DB te beheren. 

Controleer het artikel [Uw database en queryconsistentieniveaus wijzigen](../cosmos-db/consistency-levels.md) voor meer informatie.

## <a name="upserts-from-stream-analytics"></a>Upserts van Stream Analytics
Met de Integratie van Stream Analytics met Azure Cosmos DB u records in uw container invoegen of bijwerken op basis van een bepaalde **document-id-kolom.** Dit wordt ook wel een *upsert*.

Stream Analytics hanteert een optimistische upsert-aanpak. Updates vinden alleen plaats wanneer een invoeging mislukt met een document-id-conflict. 

Met compatibiliteitsniveau 1.0 voert Stream Analytics deze update uit als een PATCH-bewerking, zodat het gedeeltelijke updates van het document mogelijk is. Stream Analytics voegt nieuwe eigenschappen toe of vervangt een bestaande eigenschap stapsgewijs. Wijzigingen in de waarden van arrayeigenschappen in uw JSON-document leiden er echter toe dat de hele array wordt overschreven. Dat wil zeggen, de array is niet samengevoegd. 

Met 1.2 wordt het gedrag van upsert gewijzigd om het document in te voegen of te vervangen. In de latere sectie over compatibiliteitsniveau 1.2 wordt dit gedrag verder beschreven.

Als het binnenkomende JSON-document een bestaand ID-veld heeft, wordt dat veld automatisch gebruikt als de kolom **Document-id** in Azure Cosmos DB. Eventuele volgende schrijftworden als zodanig behandeld, wat leidt tot een van deze situaties:

- Unieke id's leiden tot invoegen.
- Dubbele id's en **document-id's** die zijn ingesteld op **id** leiden tot upsert.
- Dubbele id's en **document-id's** worden niet ingesteld op fout, na het eerste document.

Als u *alle* documenten wilt opslaan, inclusief documenten met een dubbele id, wijzigt u de naam van het veld ID in uw query (met het trefwoord **AS).** Laat Azure Cosmos DB het veld ID maken of de id vervangen door de waarde van een andere kolom (met het trefwoord **AS** of met de instelling **Document-id).**

## <a name="data-partitioning-in-azure-cosmos-db"></a>Gegevenspartitionering in Azure Cosmos DB
Azure Cosmos DB schaalt partities automatisch op basis van uw werkbelasting. Daarom raden we [onbeperkte](../cosmos-db/partition-data.md) containers aan als de aanpak voor het partitioneren van uw gegevens. Wanneer Stream Analytics naar onbeperkte containers schrijft, worden evenveel parallelle schrijvers gebruikt als het vorige querystap- of invoerpartitieschema.

> [!NOTE]
> Azure Stream Analytics ondersteunt alleen onbeperkte containers met partitiesleutels op het hoogste niveau. Wordt bijvoorbeeld `/region` ondersteund. Geneste partitiesleutels `/region/name`(bijvoorbeeld) worden niet ondersteund. 

Afhankelijk van uw keuze van de partitiesleutel ontvangt u mogelijk deze _waarschuwing:_

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Het is belangrijk om een eigenschap partitiesleutel te kiezen met een aantal verschillende waarden en waarmee u uw werkbelasting gelijkmatig over deze waarden verdelen. Als een natuurlijk artefact van partitionering worden aanvragen waarbij dezelfde partitiesleutel wordt betrokken, beperkt door de maximale doorvoer van een enkele partitie. 

De opslaggrootte voor documenten die tot dezelfde partitiesleutel behoren, is beperkt tot 10 GB. Een ideale partitiesleutel is een sleutel die vaak wordt weergegeven als een filter in uw query's en voldoende kardinaliteit heeft om ervoor te zorgen dat uw oplossing schaalbaar is.

Een partitiesleutel is ook de grens voor transacties in opgeslagen procedures en triggers voor Azure Cosmos DB. U moet de partitiesleutel kiezen, zodat documenten die samen in transacties voorkomen, dezelfde partitiesleutelwaarde hebben. Het artikel [Partitioneren in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) geeft meer details over het kiezen van een partitiesleutel.

Voor vaste Azure Cosmos DB-containers maakt Stream Analytics geen manier om op te schalen of uit te schalen nadat ze vol zijn. Ze hebben een bovengrens van 10 GB en 10.000 RU/s doorvoer. Als u de gegevens wilt migreren van een vaste container naar een onbeperkte container (bijvoorbeeld een container met ten minste 1.000 RU/s en een partitiesleutel), gebruikt u het [hulpprogramma voor gegevensmigratie](../cosmos-db/import-data.md) of de [feedbibliotheek wijzigen.](../cosmos-db/change-feed.md)

De mogelijkheid om te schrijven naar meerdere vaste containers wordt afgeschaft. We raden het niet aan om je Stream Analytics-taak uit te schalen.

## <a name="improved-throughput-with-compatibility-level-12"></a>Verbeterde doorvoer met compatibiliteitsniveau 1.2
Met compatibiliteitsniveau 1.2 ondersteunt Stream Analytics native integratie met bulkschrijven in Azure Cosmos DB. Deze integratie maakt het mogelijk om effectief naar Azure Cosmos DB te schrijven en tegelijkertijd de doorvoer te maximaliseren en throttling-aanvragen efficiënt te verwerken. 

Het verbeterde schrijfmechanisme is beschikbaar onder een nieuw compatibiliteitsniveau vanwege een verschil in upsertgedrag. Met niveaus vóór 1.2 is het upsert-gedrag het invoegen of samenvoegen van het document. Met 1.2 wordt het gedrag van upsert gewijzigd om het document in te voegen of te vervangen.

Met niveaus vóór 1.2 gebruikt Stream Analytics een aangepaste opgeslagen procedure om upsert-documenten per partitiesleutel in Azure Cosmos DB te bulkeren. Daar wordt een batch geschreven als een transactie. Zelfs wanneer een enkele record een tijdelijke fout (beperking) raakt, moet de hele batch opnieuw worden geprobeerd. Dit maakt scenario's met zelfs redelijke throttling relatief traag.

In het volgende voorbeeld worden twee identieke Stream Analytics-taken weergegeven die worden gelezen vanuit dezelfde Azure Event Hubs-invoer. Beide Stream Analytics-taken worden [volledig verdeeld](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) met een passthrough-query en schrijven naar identieke Azure Cosmos DB-containers. Metrische gegevens aan de linkerkant zijn van de taak geconfigureerd met compatibiliteitsniveau 1.0. Metrische gegevens aan de rechterkant zijn geconfigureerd met 1.2. De partitiesleutel van een Azure Cosmos DB-container is een unieke GUID die afkomstig is van de invoergebeurtenis.

![Vergelijking van Stream Analytics-statistieken](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

De inkomende gebeurtenissnelheid in gebeurtenishubs is twee keer hoger dan Azure Cosmos DB-containers (20.000 RU's) zijn geconfigureerd om op te nemen, dus beperking wordt verwacht in Azure Cosmos DB. De taak met 1.2 schrijft echter consequent bij een hogere doorvoer (uitvoergebeurtenissen per minuut) en met een lager gemiddelde SU%-gebruik. In uw omgeving zal dit verschil afhangen van weinig meer factoren. Deze factoren omvatten de keuze van de gebeurtenis-indeling, invoergebeurtenis/berichtgrootte, partitiesleutels en query.

![Vergelijking van Azure Cosmos DB-statistieken](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Met 1.2 is Stream Analytics intelligenter in het gebruik van 100 procent van de beschikbare doorvoer in Azure Cosmos DB met zeer weinig herinzendingen van beperking of tariefbeperking. Dit biedt een betere ervaring voor andere workloads, zoals query's die tegelijkertijd op de container worden uitgevoerd. Als u wilt zien hoe Stream Analytics wordt geschaald met Azure Cosmos DB als een sink voor 1.000 tot 10.000 berichten per seconde, probeert u [dit Azure-voorbeeldproject](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

De doorvoer van Azure Cosmos DB-uitvoer is identiek met 1.0 en 1.1. We raden u *ten zeerste aan* compatibiliteitsniveau 1.2 te gebruiken in Stream Analytics met Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Azure Cosmos DB-instellingen voor JSON-uitvoer

Met Azure Cosmos DB als uitvoer in Stream Analytics wordt de volgende informatieprompt gegenereerd.

![Informatievelden voor een Azure Cosmos DB-uitvoerstroom](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Veld           | Beschrijving|
|-------------   | -------------|
|Uitvoeralias    | Een alias die naar deze uitvoer moet verwijzen in uw Stream Analytics-query.|
|Abonnement    | Het Azure-abonnement.|
|Account-id      | De naam of eindpunt URI van het Azure Cosmos DB-account.|
|Accountsleutel     | De gedeelde toegangssleutel voor het Azure Cosmos DB-account.|
|Database        | De naam van de Azure Cosmos DB-database.|
|Containernaam | De containernaam, `MyContainer`zoals . Er moet `MyContainer` één container met de naam bestaan.  |
|Document-id     | Optioneel. De kolomnaam in uitvoergebeurtenissen die worden gebruikt als de unieke sleutel waarop bewerkingen voor invoegen of bijwerken moeten zijn gebaseerd. Als u het leeg laat, worden alle gebeurtenissen ingevoegd, zonder updateoptie.|

Nadat u de Azure Cosmos DB-uitvoer hebt geconfigureerd, u deze in de query gebruiken als doel van een [INTO-instructie.](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics) Wanneer u op die manier een Azure Cosmos DB-uitvoer gebruikt, [moet een partitiesleutel expliciet worden ingesteld.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) 

De uitvoerrecord moet een hoofdlettergevoelige kolom bevatten die is vernoemd naar de partitiesleutel in Azure Cosmos DB. Om een grotere parallelisatie te bereiken, kan de instructie een [component verdeling door vereisen](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) die dezelfde kolom gebruikt.

Hier is een voorbeeldquery:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Foutafhandeling en nieuwe pogingen

Als er een tijdelijke fout, serviceonbeschikbaarheid of beperking optreedt terwijl Stream Analytics gebeurtenissen naar Azure Cosmos DB verzendt, wordt Stream Analytics voor onbepaalde tijd opnieuw geprobeerd om de bewerking succesvol te voltooien. Maar het probeert geen pogingen voor de volgende fouten:

- Ongeautoriseerd (HTTP-foutcode 401)
- NotFound (HTTP-foutcode 404)
- Verboden (HTTP-foutcode 403)
- BadRequest (HTTP-foutcode 400)
