---
title: Wat is Azure Lighthouse?
description: Met Azure Lighthouse kunnen serviceproviders beheerde services voor hun klanten met een hogere automation en efficiëntie leveren op schaal.
author: JnHs
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
ms.service: lighthouse
manager: carmonm
ms.openlocfilehash: 05fa16504e25a6bf0f6aa1c0a348284abba6e1ed
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011909"
---
# <a name="what-is-azure-lighthouse"></a>Wat is Azure Lighthouse?

Azure Lighthouse biedt service providers één besturings vlak voor het weer geven en beheren van Azure voor al hun klanten met meer automatisering, schaal baarheid en verbeterd governance. Met Azure Lighthouse kunnen service providers beheerde services leveren met behulp van uitgebreid en krachtig beheer programma dat is ingebouwd in het Azure-platform. Deze aanbieding kan ook profiteren van zakelijke IT-organisaties die resources over meerdere tenants beheren.

![Overzichts diagram van Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Voordelen

Met Azure Lighthouse kunt u op winstgevende en efficiënte wijze beheerde services bouwen en leveren voor uw klanten. De voor delen zijn onder andere:

- **Beheer op schaal**: Klant betrokkenheid en levenscyclus bewerkingen voor het beheer van klant resources zijn eenvoudiger en schaalbaar.
- **Meer inzicht en nauw keurigheid voor klanten**: Klanten van wie de resources die u beheert, hebben meer inzicht in uw acties en nauw keurige controle over het bereik dat ze overdragen voor beheer, terwijl uw IP-adres behouden blijft.
- **Uitgebreide en Unified platform-hulp middelen**: Onze ervaring met Program ma's heeft betrekking op belang rijke service provider scenario's, met inbegrip van meerdere licentie modellen, zoals EA, CSP en betalen naar gebruik. De nieuwe mogelijkheden werken met bestaande hulpprogram ma's en Api's, licentie modellen en partner Programma's, zoals het [Cloud Solution Provider-programma (CSP)](https://docs.microsoft.com/partner-center/csp-overview). De Azure Lighthouse-opties die u kiest, kunnen worden geïntegreerd in uw bestaande werk stromen en toepassingen, en u kunt uw impact op klant afspraken bijhouden door [uw partner-id te koppelen](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Er zijn geen extra kosten verbonden aan het gebruik van Azure Lighthouse voor het beheren van de Azure-resources van uw klanten.

## <a name="capabilities"></a>Mogelijkheden

Azure Lighthouse bevat meerdere manieren om klant betrokkenheid en-beheer te stroom lijnen:

- **Beheer van Azure-gedelegeerde resources**: Beheer de Azure-resources van uw klanten veilig vanuit uw eigen Tenant, zonder dat u context-en besturings plannen hoeft te scha kelen. Zie [Azure delegated resource management](./concepts/azure-delegated-resource-management.md)voor meer informatie.
- **Nieuwe Azure Portal-ervaring**: Bekijk informatie over meerdere tenants op de pagina nieuwe **mijn klanten** in de [Azure Portal](https://portal.azure.com). Met een bijbehorende Blade **service providers** kunnen uw klanten toegang tot de service provider weer geven en beheren. Zie [klanten weer geven en beheren](./how-to/view-manage-customers.md) en [service providers weer geven en beheren](./how-to/view-manage-service-providers.md)voor meer informatie.
- **Azure Resource Manager sjablonen**: Beheer taken gemakkelijker uit te voeren, met inbegrip van onboarding van klanten voor het beheer van gedelegeerde resources van Azure. Voor meer informatie raadpleegt u onze voor [beelden opslag plaats](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) en maakt [u een klant onboarding van Azure gedelegeerde resource beheer](how-to/onboard-customer.md).
- **Aanbiedingen voor beheerde services in azure Marketplace**: Bied uw services aan klanten aan via privé-of open bare aanbiedingen en laat deze automatisch onboarding uitvoeren op Azure delegated resource management, als alternatief voor onboarding met Azure Resource Manager sjablonen. Zie [Managed Services aanbiedingen in azure Marketplace](./concepts/managed-services-offers.md)voor meer informatie.
- Door **Azure beheerde toepassingen**: Verpakken en verzenden van toepassingen die eenvoudig zijn voor uw klanten in hun eigen abonnementen. De toepassing wordt geïmplementeerd in een resource groep waartoe u toegang hebt via uw Tenant, zodat u de service kunt beheren als onderdeel van de algehele Azure Lighthouse-ervaring. Zie overzicht van door [Azure beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/overview)voor meer informatie.

> [!NOTE]
> De mogelijkheden die hierboven worden beschreven, zijn momenteel beschikbaar in open bare Clouds. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor regionale Beschik baarheid van afzonderlijke services.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](concepts/azure-delegated-resource-management.md).
- Meer informatie over de [ervaring op het beheer van cross-tenants](concepts/cross-tenant-management-experience.md).
