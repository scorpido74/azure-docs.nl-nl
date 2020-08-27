---
title: Aanbevelingen voor Azure-reserveringen
description: Ontdek meer over aanbevelingen voor Azure-reserveringen.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 813cf91195a43a2fc68236febe114c67eb5a3378
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684606"
---
# <a name="reservation-recommendations"></a>Aanbevelingen voor reserveringen

Aankoopaanbevelingen voor gereserveerde Azure-instanties (RI) worden aangeboden via de Azure Consumption [Reservation Recommendation-API](/rest/api/consumption/reservationrecommendations), [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) en de ervaring voor de aankoop van reserveringen in de Azure-portal.

In de volgende stappen wordt gedefinieerd hoe de aanbevelingen worden berekend:

1. De aanbevelingsengine evalueert het gebruik per uur voor uw resources in het gegeven bereik van de afgelopen 7, 30 en 60 dagen.
2. Op basis van de gebruiksgegevens simuleert de engine uw kosten met en zonder reserveringen.
3. De kosten worden gesimuleerd voor verschillende hoeveelheden. De hoeveelheid waarvoor de besparingen het grootst zijn, wordt aanbevolen.
4. Als uw resources regelmatig worden afgesloten, vindt de simulatie geen besparingen en worden er geen aankoopaanbevelingen gedaan.

## <a name="recommendations-in-the-azure-portal"></a>Aanbevelingen in de Azure-portal

Aanbevelingen voor reserveringsaankopen worden ook weergegeven in de Azure-portal in de aankoopervaring. Aanbevelingen worden weergegeven met de **Aanbevolen hoeveelheid**. De hoeveelheid die Azure aanbeveelt, zal de best mogelijke besparingen opleveren na aankoop. U kunt elke hoeveelheid kopen die u maar wilt, maar als u een andere hoeveelheid koopt, zullen uw besparingen niet optimaal zijn.

Laten we enkele voorbeelden bekijken om te zien waarom dat zo is.

In de volgende voorbeeldafbeelding voor de geselecteerde aanbeveling beveelt Azure een aankoophoeveelheid van 6 aan.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Voorbeeld van een aanbeveling voor een reserveringsaankoop" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Meer informatie over de aanbeveling wordt weergegeven wanneer u de koppeling **Details bekijken** selecteert. In de volgende afbeelding worden details over de aanbeveling weergegeven. De aanbevolen hoeveelheid wordt berekend voor het hoogst mogelijke gebruik, gebaseerd op uw historische gebruik. Uw aanbeveling is mogelijk niet voor 100% gebruik als uw gebruik inconsistent is. In het voorbeeld ziet u dat het gebruik in de loop der tijd fluctueerde. De kosten van de reservering, mogelijke besparingen en het gebruikspercentage worden weergegeven.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Voorbeeld van details van een aanbeveling voor een reserveringsaankoop" :::

Wanneer u een hogere of lagere reserveringshoeveelheid dan de aanbeveling selecteert, veranderen de grafiek en de geschatte waarden. Als u de reserveringshoeveelheid verhoogt, nemen uw besparingen af omdat het reserveringsgebruik lager zal zijn. Met andere woorden, u zult betalen voor reserveringen die niet volledig worden gebruikt.

Als u de reserveringshoeveelheid verlaagt, nemen uw besparingen ook af. Hoewel het gebruik hoger zal zijn, zullen er waarschijnlijk perioden zijn wanneer uw reserveringen uw gebruik niet volledig zullen dekken. Als het gebruik uw reserveringshoeveelheid overschrijdt, worden daar duurdere Pay-As-You-Go-bronnen voor gebruikt. De volgende voorbeeldafbeelding illustreert dit. We hebben de reserveringshoeveelheid handmatig verlaagd naar 4. Het reserveringsgebruik wordt verhoogd, maar de algehele besparingen nemen af omdat er Pay-As-You-Go-kosten aanwezig zijn.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Voorbeeld van gewijzigde details van een aanbeveling voor een reserveringsaankoop" :::

Probeer reserveringen zo dicht mogelijk bij de aanbeveling te kopen voor optimale besparingen.

## <a name="recommendations-in-azure-advisor"></a>Aanbevelingen in Azure Advisor

Aanbevelingen voor reserveringsaankopen zijn beschikbaar in Azure Advisor. Houd rekening met de volgende belangrijke punten:

- Advisor heeft alleen aanbevelingen voor bereiken van één abonnement.
- Aanbevelingen worden berekend door de gebruikstrend van de afgelopen 30 dagen in acht te nemen.
- De aanbevelingshoeveelheid en besparingen zijn voor een 3-jarige reservering, indien beschikbaar. Als er geen 3-jarige reservering wordt verkocht voor de service, wordt de aanbeveling berekend op basis van de prijs voor een 1-jarige reservering.
- Aanbevelingen voor reserveringen houden rekening met eventuele speciale kortingen die u hebt op uw gebruikstarieven op aanvraag.
- Als u een reservering met gedeeld bereik aanschaft, kan het tot 30 dagen duren voordat aankoopaanbelingen voor reserveringen van Advisor zijn verdwenen.

## <a name="other-expected-api-behavior"></a>Ander verwacht API-gedrag

- Wanneer u een lookback-periode van zeven dagen gebruikt, krijgt u mogelijk geen aanbevelingen wanneer VM's langer dan een dag worden afgesloten.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over de [toepassing van de Azure-reserveringskorting op virtuele machines](../manage/understand-vm-reservation-charges.md).
