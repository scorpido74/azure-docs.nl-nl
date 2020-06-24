---
title: Telemetrie van Azure peering service-verbinding
description: Meer informatie over Microsoft Azure telemetrie van peering-service verbindingen
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "84872006"
---
# <a name="peering-service-connection-telemetry"></a>Telemetrie van peering service-verbinding

Telemetrie van de verbinding biedt inzichten die worden verzameld voor de connectiviteit tussen de locatie van de klant en het micro soft-netwerk. Klanten kunnen telemetrie voor de Azure peering service-verbinding verkrijgen door de verbinding te registreren in de Azure Portal. Deze functie biedt een voor voegsel van beveiliging en inzicht in de netwerk prestaties.


Telemetrie van de verbinding bestaat uit de volgende bereiken:

### <a name="latency-measurement"></a>Latentie meting

 De latentie wordt gemeten van de client naar de micro soft Edge-PoP voor de geregistreerde voor voegsels binnen de peering-service.

### <a name="route-prefix-monitoring-and-protection"></a>Controle en bescherming van route voorvoegsel

Routerings paden worden bewaakt voor verdachte activiteiten die vervolgens worden vastgelegd in gebeurtenis Logboeken. Zo worden bijvoorbeeld gebeurtenis logboeken gemaakt voor een aantal van deze factoren:

- Voorvoegsel overname
- Voor voegsel intrekken
- Route lekkage

## <a name="next-steps"></a>Volgende stappen

- Zie [verbinding met peering service](connection.md)voor meer informatie over peering service-verbinding.
- Zie voor een onboarding van een peering- [service model onboarding](onboarding-model.md).
- Zie [verbindings-telemetrie meten](measure-connection-telemetry.md)om telemetrie te meten.
