---
title: Wat is Azure Lighthouse?
description: Azure Lighthouse laat serviceproviders managed services leveren voor hun klanten met een hogere automatisering en efficiëntie op schaal.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0bec1f5b727ca3ecd604d2654358ca9a5507abe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646191"
---
# <a name="what-is-azure-lighthouse"></a>Wat is Azure Lighthouse?

Azure Lighthouse biedt serviceproviders één besturingsvlak om Azure voor al hun klanten te bekijken en te beheren met een hogere automatisering, schaal en verbeterde governance. Met Azure Lighthouse kunnen serviceproviders beheerservices leveren met behulp van uitgebreide en robuuste beheertooling die is ingebouwd in het Azure-platform. Dit aanbod kan ook ten goede komen aan IT-organisaties die resources beheren voor meerdere tenants.

![Overzichtsdiagram van Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Voordelen

Azure Lighthouse helpt u om op een efficiënte manier beheerde services voor uw klanten te bouwen en te leveren. De voordelen zijn:

- **Beheer op schaal**: Customer engagement en life-cycle operations om klantresources te beheren zijn eenvoudiger en schaalbaarder.
- **Meer zichtbaarheid en precisie voor klanten**: klanten waarvan u de resources beheert, hebben meer inzicht in uw acties en nauwkeurige controle over het bereik dat ze delegeren voor beheer, terwijl uw IP behouden blijft.
- **Uitgebreide en uniforme platformtooling:** onze tooling-ervaring richt zich op belangrijke scenario's van serviceproviders, waaronder meerdere licentiemodellen zoals EA, CSP en pay-as-you-go. De nieuwe mogelijkheden werken met bestaande tools en API's, licentiemodellen en partnerprogramma's zoals het [Cloud Solution Provider-programma (CSP).](https://docs.microsoft.com/partner-center/csp-overview) De Azure Lighthouse-opties die u kiest, kunnen worden geïntegreerd in uw bestaande werkstromen en toepassingen en u uw impact op klantinteracties bijhouden door [uw partner-id te koppelen.](../billing/billing-partner-admin-link-started.md)

Er zijn geen extra kosten verbonden aan het gebruik van Azure Lighthouse om de Azure-bronnen van uw klanten te beheren.

## <a name="capabilities"></a>Functionaliteit

Azure Lighthouse bevat meerdere manieren om de betrokkenheid en het beheer van klanten te stroomlijnen:

- **Azure gedelegeerd resourcebeheer:** Beheer de Azure-bronnen van uw klanten veilig vanuit uw eigen tenant, zonder dat u van context hoeft te wisselen en vlakken hoeft te beheren. Zie [Azure delegated resource management](concepts/azure-delegated-resource-management.md)voor meer informatie .
- **Nieuwe Azure-portalervaringen:** Informatie over meerdere tenantweeren in de nieuwe pagina **Mijn klanten** in de [Azure-portal](https://portal.azure.com). Met een bijbehorend **blade van serviceproviders** kunnen uw klanten de toegang tot serviceproviders bekijken en beheren. Zie [Klanten weergeven en beheren en](./how-to/view-manage-customers.md) [serviceproviders bekijken en beheren.](how-to/view-manage-service-providers.md)
- **Azure Resource Manager-sjablonen:** voer beheertaken eenvoudiger uit, inclusief onboarding-klanten voor Azure-gedelegeerd bronbeheer. Zie voor meer informatie onze [voorbeelden repo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) en [Onboard een klant naar Azure gedelegeerd resourcebeheer](how-to/onboard-customer.md).
- **Managed Services-aanbiedingen in Azure Marketplace:** bied uw services aan klanten aan via privé- of openbare aanbiedingen en laat ze automatisch aan azure-gedelegeerd bronbeheer koppelen, als alternatief voor onboarding met Azure Resource Manager-sjablonen. Zie [Aanbiedingen voor beheerde services in Azure Marketplace](concepts/managed-services-offers.md)voor meer informatie.
- **Azure-beheerde toepassingen:** pakket- en verzendtoepassingen die uw klanten eenvoudig kunnen implementeren en gebruiken in hun eigen abonnementen. De toepassing wordt geïmplementeerd in een resourcegroep die u vanuit uw tenant opent, zodat u de service beheren als onderdeel van de algehele Azure Lighthouse-ervaring. Zie [overzicht van Azure managed applications](../azure-resource-manager/managed-applications/overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](concepts/azure-delegated-resource-management.md).
- Meer informatie over [cross-tenant management ervaringen](concepts/cross-tenant-management-experience.md).
