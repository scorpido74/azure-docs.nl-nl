---
title: Problemen met Azure Cosmos DB HTTP 408 of time-outs met .NET SDK oplossen
description: Een time-outuitzondering voor .NET SDK-aanvragen vaststellen en oplossen
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 29b0c6237ae04ea5da9ec496498fc7c20890b173
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294396"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Problemen vaststellen en oplossen Azure Cosmos DB time-out voor de .NET SDK-aanvraag
De HTTP 408-fout treedt op als de SDK de aanvraag niet heeft kunnen volt ooien voordat de time-outlimiet optreedt.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor time-outuitzonderingen voor aanvragen.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. hoog CPU-gebruik (het meest voorkomende geval)
Voor een optimale latentie wordt aanbevolen dat het CPU-gebruik ongeveer 40% is. Het is raadzaam 10 seconden te gebruiken als het interval voor het controleren van het maximum (niet gemiddeld) CPU-gebruik. CPU-pieken komen vaker voor bij Kruis partitie query's waarbij er meerdere verbindingen voor één query kunnen worden uitgevoerd.

#### <a name="solution"></a>Oplossing:
De client toepassing die gebruikmaakt van de SDK moet omhoog of omlaag worden geschaald.

### <a name="2-socket--port-availability-might-be-low"></a>2. Beschik baarheid van socket/poort is mogelijk laag
Bij het uitvoeren in azure kunnen clients die gebruikmaken van de .NET SDK, de poort uitputting voor Azure SNAT (PAT) aanraken.

#### <a name="solution-1"></a>Oplossing 1:
Volg de [SNAT-hand leiding voor poort uitputting](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Oplossing 2:
Als u een HTTP-proxy gebruikt, moet u ervoor zorgen dat het het aantal verbindingen dat in de SDK is geconfigureerd kan ondersteunen `ConnectionPolicy` .
Anders worden er verbindings problemen beschreven.

### <a name="3-creating-multiple-client-instances"></a>3. meerdere client exemplaren maken
Het maken van meerdere client exemplaren kan leiden tot verbindings conflicten en time-outproblemen.

#### <a name="solution"></a>Oplossing:
Volg de [Tips voor prestaties](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)en gebruik één CosmosClient-exemplaar in een heel proces.

### <a name="4-hot-partition-key"></a>4. Hot-partitie sleutel
Azure Cosmos DB distribueert de totale ingerichte door Voer gelijkmatig over fysieke partities. Wanneer er een dynamische partitie is, worden in een of meer logische partitie sleutels op een fysieke partitie de RU/s van de fysieke partitie verbruikt, terwijl de RU/s op andere fysieke partities niet worden gebruikt. Als symptoom is het totale aantal aanwezige RU/s kleiner dan de totale ingerichte RU/s in de data base of container, maar u hebt nog steeds vertraging (429s) op de aanvragen voor de logische partitie sleutel Hot Logical. Gebruik de [genormaliseerde metrische gegevens van ru-verbruik](monitor-normalized-request-units.md) om te zien of de werk belasting een hete partitie tegen komt. 

#### <a name="solution"></a>Oplossing:
Kies een goede partitie sleutel waarmee het aanvraag volume en de opslag gelijkmatig worden verdeeld. Meer informatie over het [wijzigen van de partitie sleutel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. hoge mate van gelijktijdigheid
De toepassing heeft een hoog niveau van gelijktijdigheid, wat kan leiden tot conflicten op het kanaal

#### <a name="solution"></a>Oplossing:
De client toepassing die gebruikmaakt van de SDK moet omhoog of omlaag worden geschaald.

### <a name="6-large-requests-andor-responses"></a>6. grote aanvragen en/of reacties
Grote aanvragen of antwoorden kunnen leiden tot hoofd-of-line blok kering van het kanaal en de exacerbate-inhoud, zelfs met een relatief lage mate van gelijktijdigheid.

#### <a name="solution"></a>Oplossing:
De client toepassing die gebruikmaakt van de SDK moet omhoog of omlaag worden geschaald.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. het aantal fouten ligt binnen Cosmos DB SLA
De toepassing moet tijdelijke storingen kunnen verwerken en opnieuw proberen wanneer dat nodig is. 408 uitzonde ringen worden niet opnieuw geprobeerd omdat bij paden maken het niet mogelijk is om te weten of de service het item heeft gemaakt of dat dit niet het geval is. Als u hetzelfde item opnieuw verzendt om te maken, treedt er een conflict op. Bedrijfs logica voor gebruikers toepassingen kan aangepaste logica hebben voor het afhandelen van conflicten, wat zou afbreken van de dubbel zinnigheid van een bestaand item versus een conflict met het maken van een nieuwe poging.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. het aantal fouten is in strijd met de Cosmos DB SLA
Neem contact op met de ondersteuning van Azure.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)