---
title: Problemen met het aantal Azure Cosmos DB aanvragen voor een grote uitzonde ring oplossen
description: De aanvraag frequentie voor een grote uitzonde ring vaststellen en oplossen
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294268"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Cosmos DB te veel aanvragen diagnosticeren en problemen oplossen
' De aanvraag snelheid is te groot ' of de fout code 429 geeft aan dat uw aanvragen worden beperkt.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor te veel aanvragen.

### <a name="1-check-the-metrics"></a>1. Controleer de metrische gegevens
De klant moet de [Azure Cosmos DB controle](monitor-cosmos-db.md) controleren om te controleren of het aantal 429 uitzonde ringen is.

## <a name="cause"></a>Oorzaak:
De verbruikte door Voer (RU/s) heeft de [ingerichte door Voer](set-throughput.md)overschreden. De SDK voert automatisch aanvragen opnieuw uit op basis van het opgegeven beleid voor opnieuw proberen. Als deze fout vaak optreedt, kunt u overwegen de door Voer van de verzameling te verhogen. Controleer de metrische gegevens van de portal om te zien of er 429 fouten optreden. Controleer uw partitie sleutel om ervoor te zorgen dat deze een [gelijkmatige verdeling van de opslag en het aanvraag volume](partition-data.md)levert.

## <a name="solution"></a>Oplossing:
1. Gebruik de [Portal of de SDK](set-throughput.md) om de ingerichte door voer te verg Roten.
2. Schakel de data base of container over naar [automatisch schalen](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)