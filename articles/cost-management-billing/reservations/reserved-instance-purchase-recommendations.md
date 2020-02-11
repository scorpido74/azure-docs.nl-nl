---
title: Hoe reserveringsaanbevelingen van Azure worden gemaakt
description: Lees hier hoe reserveringsaanbevelingen van Azure voor virtuele machines worden gemaakt.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851348"
---
# <a name="how-reservation-recommendations-are-created"></a>Hoe reserveringsaanbevelingen worden gemaakt

Aankoopaanbevelingen voor gereserveerde instanties (RI) van Azure worden gegenereerd door de [API Reservation Recommendation](/rest/api/consumption/reservationrecommendations) van Azure Consumption. Aanbevelingen van de API worden ook gebruikt door [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). Advisor toont aanbevelingen in de Azure-portal.

Als u VM's hebt die in Azure worden uitgevoerd, kunt u profiteren van prijzen met korting voor RI's en vooraf betalen voor uw VM's. De API voor aanbevelingen van Microsoft Consumption evalueert uw gebruik gedurende 7, 30 en 60 dagen en raadt optimale configuraties voor RI's aan. U ziet de kosten die u zou moeten betalen als u niet beschikt over RI's en de kosten die u betaalt als u kiest voor RI's. U kunt zo meteen de besparingen zien.

Azure Advisor geeft aanbevelingen weer op basis van een periode van 30 dagen.

In het volgende voorbeeld ziet u om het overzichtelijk te houden berekeningen voor een aanbeveling van zeven dagen. Dezelfde methode wordt toegepast bij het berekenen van aanbevelingen voor 30 of 60 dagen.

## <a name="calculation-method-example"></a>Voorbeeld van berekeningsmethode

Stel dat het gebruik per uur van een Windows-VM voor een specifieke SKU en regio varieert binnen een periode van zeven dagen (168 uur). Het minimumgebruik is 65 eenheden en het maximumgebruik 127 eenheden gedurende de zeven dagen. In dit voorbeeld zijn in uur 79 80 VM's gebruikt en u hebt 75 RI's gekocht.

Als u 75 gereserveerde instanties koopt, betaalt u de volgende kosten voor uur 79:

- 75 gereserveerde instanties. De kosten worden vooraf betaald wanneer u RI's koopt.
- Gereserveerde instanties dekken de hardwarekosten van het uitvoeren van VM's, zodat u 75 uur betaalt tegen de prijs van alleen de software.
- Het gebruik voor uur 79 is 80, dus u betaalt voor vijf uur Windows plus de gecombineerde meterprijs van de hardware. De gecombineerde prijs is gebaseerd op uw Enterprise Agreement (EA) of het tarief voor betalen naar gebruik.

Als u 75 RI's koopt, kunt u de totale kosten berekenen door de voorafgaande kosten per uur op te tellen. U kunt ook uw huidige kosten berekenen op basis van uw tarief. Het verschil tussen de twee bedragen is uw besparing voor zeven dagen in dit voorbeeld.

De API voert berekeningen uit voor elk specifiek gebruikspunt. Vervolgens wordt de aanbevolen hoeveelheid geretourneerd waarbij de besparingen worden gemaximaliseerd. In het volgende voorbeeld toont de grafiek dat de besparingen pieken bij 68. De besparingen nemen later af en dus adviseert de API 68.

![Diagram met piekbesparingen](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Ander verwacht API-gedrag

- De API toont mogelijke besparingen met [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) voor Windows wanneer het voordeel wordt gebruikt. Als het voordeel niet wordt gebruikt, zijn de API-aanbevelingen gebaseerd op de basiskosten voor Windows. Als Azure Hybrid Benefit beschikbaar voor u is, adviseren we om dat te gebruiken om de besparingen te verhogen.
- Wanneer u een lookback-periode van zeven dagen gebruikt, krijgt u mogelijk geen aanbevelingen wanneer VM's langer dan een dag worden afgesloten.

## <a name="next-steps"></a>Volgende stappen
- Lees meer over de [toepassing van de Azure-reserveringskorting op virtuele machines](../manage/understand-vm-reservation-charges.md).
