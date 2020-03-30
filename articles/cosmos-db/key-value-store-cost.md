---
title: Eenheidskosten aanvragen voor Azure Cosmos DB als sleutelwaardeopslag
description: Meer informatie over de kosten voor aanvraageenheden van Azure Cosmos DB voor eenvoudige schrijf- en leesbewerkingen wanneer deze worden gebruikt als een sleutel-/waardeopslag.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647509"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB als key value store – kostenoverzicht

Azure Cosmos DB is een wereldwijd gedistribueerde databaseservice met meerdere modellen voor het eenvoudig bouwen van zeer beschikbare, grootschalige toepassingen. Azure Cosmos DB indexeert standaard automatisch en efficiënt alle gegevens die het inneemt. Dit maakt snelle en consistente [SQL -query's](how-to-sql-query.md) (en [JavaScript)](stored-procedures-triggers-udfs.md)query's op de gegevens mogelijk. 

In dit artikel worden de kosten van Azure Cosmos DB beschreven voor eenvoudige schrijf- en leesbewerkingen wanneer deze worden gebruikt als een sleutel-/waardeopslag. Schrijfbewerkingen omvatten inserts, vervangt, verwijdert en upserts van gegevensitems. Naast het garanderen van een SLA van 99,999% beschikbaarheid voor alle multi-region accounts, biedt Azure Cosmos DB gegarandeerde <latency van 10 ms voor reads en voor de (geïndexeerde) schrijft, op het 99e percentiel. 

## <a name="why-we-use-request-units-rus"></a>Waarom we Request Units (RU's) gebruiken

De prestaties van Azure Cosmos DB zijn gebaseerd op de hoeveelheid ingerichte doorvoer uitgedrukt in [aanvraageenheden](request-units.md) (RU/s). De inrichting is op een tweede granulariteit en wordt gekocht in RU / s ([niet te verwarren met de uurfacturering](https://azure.microsoft.com/pricing/details/cosmos-db/)). VALUTA's moeten worden beschouwd als een logische abstractie (een valuta) die de bepaling van de vereiste doorvoer voor de toepassing vereenvoudigt. Gebruikers hoeven niet te denken aan onderscheid tussen lees- en schrijfdoorvoer. Het eenheidsmuntmodel van R's creëert efficiëntieom de ingerichte capaciteit tussen reads en schrijft te delen. Dit ingerichte capaciteitsmodel stelt de service in staat om een **voorspelbare en consistente doorvoer, gegarandeerde lage latentie en hoge beschikbaarheid**te bieden. Ten slotte, terwijl RU-model wordt gebruikt om doorvoer weer te geven, elke ingerichte RU heeft ook een bepaalde hoeveelheid middelen (bijvoorbeeld geheugen, cores / CPU en IOPS).

Als wereldwijd gedistribueerd databasesysteem is Cosmos DB de enige Azure-service die uitgebreide SLA's biedt voor latentie, doorvoer, consistentie en hoge beschikbaarheid. De doorvoer die u indient, wordt toegepast op elk van de regio's die zijn gekoppeld aan uw Cosmos-account. Voor reads biedt Cosmos DB meerdere, goed gedefinieerde [consistentieniveaus](consistency-levels.md) waaruit u kiezen. 

In de volgende tabel ziet u het aantal R's dat nodig is om lees- en schrijfbewerkingen uit te voeren op basis van een gegevensitem van grootte 1 KB en 100 GB met standaard automatische indexering uitgeschakeld. 

|Objectgrootte|1 Gelezen|1 Schrijf|
|-------------|------|-------|
|1 kB|1 RU|5 -10U's|
|100 kB|Tien aanvraageenheden|50 10 000|

## <a name="cost-of-reads-and-writes"></a>Kosten van lezen en schrijven

Als u 1.000 RU/s indient, bedraagt dit 3,6 miljoen RU/uur en kost dit $ 0,08 voor het uur (in de VS en Europa). Voor een gegevensitem van 1 KB grootte betekent dit dat u 3,6 miljoen lees- of 0,72 miljoen schrijft (3,6 miljoen RU / 5) gebruiken met behulp van uw ingerichte doorvoer. Genormaliseerd tot miljoen leest en schrijft, zou de kosten zijn 0,022 dollar / miljoen van leest ($ 0,08 / 3,6) en 0,111 dollar / miljoen dollar van schrijft ($ 0,08 / 0,72). De kosten per miljoen worden minimaal, zoals in de onderstaande tabel.

|Objectgrootte|Kosten van 1 miljoen leest|Kosten van 1 miljoen schrijft|
|-------------|-------|--------|
|1 kB|$ 0,022|$ 0,111|
|100 kB|$ 0,222|$ 1,111|


De meeste basisblob- of objectstoresservices brengen $ 0,40 per miljoen leestransactie en $ 5 per miljoen schrijftransactie in rekening. Indien optimaal gebruikt, kan Cosmos DB tot 98% goedkoper zijn dan deze andere oplossingen (voor 1 KB-transacties).

## <a name="next-steps"></a>Volgende stappen

* Gebruik [RU-calculator](https://cosmos.azure.com/capacitycalculator/) om de doorvoer voor uw workloads in te schatten.

