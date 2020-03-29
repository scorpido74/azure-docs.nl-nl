---
title: Geodisaster recovery in Azure Event Grid | Microsoft Documenten
description: Beschrijft hoe Azure Event Grid geodisaster recovery (GeoDR) automatisch ondersteunt.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307315"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Herstel van georamp aan de serverzijde in Azure Event Grid
Event Grid heeft nu een automatische geo-disaster recovery (GeoDR) van meta-gegevens, niet alleen voor nieuwe, maar alle bestaande domeinen, onderwerpen en gebeurtenisabonnementen. Als een hele Azure-regio uitvalt, wordt al met al uw metagegevens van uw gebeurtenisgerelateerde infrastructuurgesynchroniseerd met een gekoppelde regio. Jullie nieuwe gebeurtenissen zullen weer beginnen te stromen zonder tussenkomst van jullie. 

Disaster recovery wordt gemeten met twee statistieken:

- [Recovery Point Objective (RPO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)de minuten of uren aan gegevens die verloren kunnen gaan.
- [Recovery Time Objective (RTO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)de minuten van de uren dat de service kan worden uitgeschakeld.

De automatische failover van Event Grid heeft verschillende RTO's en RTO's voor uw metadata (gebeurtenisabonnementen enz.) en gegevens (gebeurtenissen). Als u een andere specificatie nodig hebt dan de volgende, u nog steeds uw eigen [client-side fail implementeren met behulp van de onderwerpstatusapis.](custom-disaster-recovery.md)

## <a name="recovery-point-objective-rpo"></a>Recovery Point Objective (RPO)
- **Metadata RPO**: nul minuten. Wanneer een resource wordt gemaakt in Gebeurtenisraster, wordt deze direct gerepliceerd in verschillende regio's. Wanneer er een failover optreedt, gaat er geen metadata verloren.
- **Gegevens RPO:** Als uw systeem gezond is en ingehaald op het bestaande verkeer op het moment van regionale failover, de RPO voor evenementen is ongeveer 5 minuten.

## <a name="recovery-time-objective-rto"></a>Beoogde hersteltijd (RTO)
- **Metadata RTO:** Hoewel het over het algemeen veel sneller gebeurt, zal Event Grid binnen 60 minuten beginnen met het accepteren van aan/update/verwijderoproepen voor onderwerpen en abonnementen.
- **Data RTO**: Net als metadata gebeurt het over het algemeen veel sneller, maar binnen 60 minuten zal Event Grid beginnen met het accepteren van nieuw verkeer na een regionale failover.

> [!NOTE]
> De kosten voor metadata GeoDR op Event Grid is: $0.


## <a name="next-steps"></a>Volgende stappen
Als u uw eigen failoverlogica aan de clientzijde wilt implementeren, raadpleegt u [# Bouw uw eigen herstel na noodgevallen voor aangepaste onderwerpen in gebeurtenisraster](custom-disaster-recovery.md)
