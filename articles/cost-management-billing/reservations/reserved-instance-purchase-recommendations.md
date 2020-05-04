---
title: Hoe reserveringsaanbevelingen van Azure worden gemaakt
description: Lees hier hoe reserveringsaanbevelingen van Azure voor virtuele machines worden gemaakt.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 90967e740b87c2f93bd46bfb78684af96f36193a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508476"
---
# <a name="how-reservation-recommendations-are-created"></a>Hoe reserveringsaanbevelingen worden gemaakt

Aankoopaanbevelingen voor gereserveerde Azure-instanties (RI) worden aangeboden via de Azure Consumption [Reservation Recommendation-API](/rest/api/consumption/reservationrecommendations), [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) en de ervaring voor de aankoop van reserveringen in de Azure-portal.

In de volgende stappen wordt gedefinieerd hoe de aanbevelingen worden berekend:

1. De aanbevelingsengine evalueert het gebruik per uur voor uw resources in het gegeven bereik van de afgelopen 7, 30 en 60 dagen.
2. Op basis van de gebruiksgegevens simuleert de engine uw kosten met en zonder reserveringen.
3. De kosten worden gesimuleerd voor verschillende hoeveelheden. De hoeveelheid waarvoor de besparingen het grootst zijn, wordt aanbevolen.
4. Als uw resources regelmatig worden afgesloten, vindt de simulatie geen besparingen en worden er geen aankoopaanbevelingen gedaan.

## <a name="recommendations-in-azure-advisor"></a>Aanbevelingen in Azure Advisor

Aankoopaanbevelingen voor reserveringen voor virtuele machines zijn beschikbaar in Azure Advisor. Houd rekening met de volgende belangrijke punten:

- Advisor heeft alleen aanbevelingen voor bereiken van één abonnement.
- Aanbevelingen die worden berekend op basis van de afgelopen 30 dagen, zijn beschikbaar in Advisor.
- Als u een reservering met gedeeld bereik aanschaft, kan het tot 30 dagen duren voordat aankoopaanbelingen voor reserveringen van Advisor zijn verdwenen.

## <a name="other-expected-api-behavior"></a>Ander verwacht API-gedrag

- Wanneer u een lookback-periode van zeven dagen gebruikt, krijgt u mogelijk geen aanbevelingen wanneer VM's langer dan een dag worden afgesloten.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over de [toepassing van de Azure-reserveringskorting op virtuele machines](../manage/understand-vm-reservation-charges.md).
