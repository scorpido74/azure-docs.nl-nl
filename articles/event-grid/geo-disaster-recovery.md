---
title: Geografisch nood herstel in Azure Event Grid | Microsoft Docs
description: Hierin wordt beschreven hoe Azure Event Grid geo-nood herstel (GeoDR) automatisch ondersteunt.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "66307315"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Geografisch nood herstel aan de server zijde in Azure Event Grid
Event Grid hebt nu een automatische geo-nood herstel (GeoDR) van meta gegevens, niet alleen voor nieuwe, maar alle bestaande domeinen, onderwerpen en gebeurtenis abonnementen. Als een hele Azure-regio uitvalt, worden er al met de meta gegevens van de infra structuur van uw gebeurtenis een gepaarde regio gesynchroniseerd met Event Grid. Uw nieuwe evenementen worden opnieuw stromen zonder tussen komst van u. 

Herstel na nood gevallen wordt gemeten met twee metrische gegevens:

- [Beoogd herstel punt (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): de minuten of uren aan gegevens die mogelijk verloren zijn gegaan.
- [Beoogde herstel tijd (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): het aantal minuten dat de service mogelijk niet actief is.

De automatische failover van Event Grid heeft verschillende Rpo's en Rto's voor uw meta gegevens (gebeurtenis abonnementen, enzovoort) en gegevens (gebeurtenissen). Als u een andere specificatie van de volgende items nodig hebt, kunt u uw eigen [failover van de client implementeren met behulp van de onderwerp Health-api's](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Recovery Point Objective (RPO)
- **RPO van meta gegevens**: nul minuten. Telkens wanneer een resource wordt gemaakt in Event Grid, wordt deze direct gerepliceerd tussen regio's. Wanneer een failover optreedt, gaan er geen meta gegevens verloren.
- **Data RPO**: als uw systeem in orde is en op het moment van de regionale failover op het bestaande verkeer is, is de RPO voor gebeurtenissen ongeveer 5 minuten.

## <a name="recovery-time-objective-rto"></a>Beoogde hersteltijd (RTO)
- **Meta gegevens RTO**: Hoewel dit doorgaans veel sneller plaatsvindt, binnen 60 minuten, begint Event grid met het accepteren van aanroepen voor maken/bijwerken/verwijderen voor onderwerpen en abonnementen.
- **Data RTO**: zoals meta gegevens, gebeurt dit doorgaans veel sneller, maar binnen 60 minuten gaat Event grid het accepteren van nieuw verkeer na een regionale failover.

> [!NOTE]
> De kosten voor de GeoDR van meta gegevens op Event Grid zijn: $0.


## <a name="next-steps"></a>Volgende stappen
Zie [# uw eigen herstel na nood gevallen maken voor aangepaste onderwerpen in Event grid voor](custom-disaster-recovery.md) meer informatie over het implementeren van een failover-logica aan de client zijde.
