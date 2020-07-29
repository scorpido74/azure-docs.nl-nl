---
title: Time-outuitzondering voor Azure Cosmos DB service aanvraag oplossen
description: Een time-outuitzondering voor Cosmos DB service aanvragen vaststellen en oplossen
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294275"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Problemen met de time-out van Azure Cosmos DB aanvragen vaststellen en oplossen
Azure Cosmos DB heeft een time-out voor een HTTP 408-aanvraag geretourneerd

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor time-outuitzonderingen voor aanvragen.

### <a name="1-check-the-sla"></a>1. Controleer de SLA
De klant moet de [Azure Cosmos DB controle](monitor-cosmos-db.md) controleren om te controleren of het aantal 408 uitzonde ringen de SLA van Cosmos DB schendt.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Oplossing 1: de Cosmos DB SLA is niet geschonden
De toepassing moet dit scenario afhandelen en opnieuw proberen met deze tijdelijke fouten.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Oplossing 2: de Cosmos DB SLA is geschonden
Neem contact op met de ondersteuning van Azure:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. hete partitie sleutel
Azure Cosmos DB distribueert de totale ingerichte door Voer gelijkmatig over fysieke partities. Wanneer er een dynamische partitie is, worden in een of meer logische partitie sleutels op een fysieke partitie de RU/s van de fysieke partitie verbruikt, terwijl de RU/s op andere fysieke partities niet worden gebruikt. Als symptoom is het totale aantal aanwezige RU/s kleiner dan de totale ingerichte RU/s in de data base of container, maar u hebt nog steeds vertraging (429s) op de aanvragen voor de logische partitie sleutel Hot Logical. Gebruik de [genormaliseerde metrische gegevens van ru-verbruik](monitor-normalized-request-units.md) om te zien of de werk belasting een hete partitie tegen komt. 

#### <a name="solution"></a>Oplossing:
Kies een goede partitie sleutel waarmee het aanvraag volume en de opslag gelijkmatig worden verdeeld. Meer informatie over het [wijzigen van de partitie sleutel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)