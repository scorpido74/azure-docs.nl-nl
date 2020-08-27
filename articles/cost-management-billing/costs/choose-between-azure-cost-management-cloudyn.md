---
title: Kiezen tussen Azure Cost Management en Cloudyn
description: Dit artikel helpt u te bepalen of Azure Cost Management of Cloudyn het meest geschikt is voor uw kostenbeheerbehoeften.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684963"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Kiezen tussen Azure Cost Management en Cloudyn

Cloudyn wordt eind 2020 afgeschaft. Waar mogelijk worden bestaande Cloudyn-functies rechtstreeks geïntegreerd in de Azure-portal. Met uitzondering van CSP-klanten kan er op dit moment geen onboarding worden uitgevoerd voor nieuwe klanten. Ondersteuning voor het bestaande product blijft bestaan totdat dit product volledig is afgeschaft.

Microsoft heeft Cloudyn overgenomen en de functies voor kostenbeheer worden nu overgebracht van de Cloudyn-portal naar Azure. Als u de nieuwe functies wilt gebruiken, meldt u zich aan bij de Azure-portal en selecteert u [Kostenbeheer en facturering](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) in de lijst met Azure-services. Vergeleken met Cloudyn biedt de systeemeigen ervaring betere prestaties en een lagere gegevenslatentie van ongeveer acht uur.

De migratie van belangrijke functies voor de aanbiedingscategorieën Enterprise Agreement, Betalen per gebruik en MSDN naar Azure Cost Management is voltooid. CSP-abonnementen worden inmiddels gemigreerd naar Azure Cost Management.

Als u een aanbiedingscategorie hebt die nog niet is gemigreerd, moet u de Cloudyn-portal blijven gebruiken. In alle andere gevallen kunt u Azure Cost Management gebruiken.

## <a name="recommended-services-by-offer"></a>Aanbevolen services per aanbieding

| Microsoft Azure-aanbiedingen | Aanbevolen service voor kostenbeheer |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Microsoft-klantovereenkomst | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Microsoft-klantovereenkomst ondersteund door partners | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Beschikbare Cost Management-functies

Enkele van de volgende functies zijn beschikbaar in Cloudyn, maar alle functies zijn nu beschikbaar in Azure Cost Management.

- API's
- Aanbevelingen voor kostenoptimalisatie van Azure, inclusief, maar niet beperkt tot:
    - Aanbevelingen voor het bepalen van de juiste grootte en het afsluiten van Azure-instanties
    - Aanbevelingen voor Azure-reserveringen
- Budgetten
- Kostenanalyse
- Gegevens exporteren naar een Azure-opslagaccount
- Lagere latentie
- Power BI-sjabloon-app
- Ondersteuning voor resourcetags
- Ondersteuning voor kostenanalyse in meerdere clouds voor AWS

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Cost Management](../cost-management-billing-overview.md).