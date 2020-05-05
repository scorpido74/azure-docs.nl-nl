---
title: Wat is Azure Lighthouse?
description: Met Azure Lighthouse kunnen service providers beheerde services leveren voor hun klanten, met een grotere automatisering en efficiëntie op schaal.
ms.date: 04/30/2020
ms.topic: overview
ms.openlocfilehash: 17e0535f50240c7ea239ae230dce90e397eadce9
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780807"
---
# <a name="what-is-azure-lighthouse"></a>Wat is Azure Lighthouse?

Azure Lighthouse biedt service providers één besturings vlak voor het weer geven en beheren van Azure voor al hun klanten met meer automatisering, schaal baarheid en verbeterd governance. Met Azure Lighthouse kunnen service providers beheerde services leveren met behulp van uitgebreid en krachtig beheer programma dat is ingebouwd in het Azure-platform. Deze aanbieding kan ook profiteren van zakelijke IT-organisaties die resources over meerdere tenants beheren.

![Overzichts diagram van Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Voordelen

Met Azure Lighthouse kunt u op winstgevende en efficiënte wijze beheerde services bouwen en leveren voor uw klanten. De voordelen zijn:

- **Beheer op schaal**: klant betrokkenheid en levenscyclus bewerkingen om klanten resources te beheren zijn eenvoudiger en schaalbaar.
- **Meer zicht baarheid en nauw keurigheid voor klanten**: klanten hebben meer inzicht in uw acties en nauw keurige controle over het bereik dat ze delegeren voor beheer, inclusief de mogelijkheid om toegang volledig te verwijderen, terwijl uw IP-adres behouden blijft.
- **Uitgebreide en Unified platform-hulp middelen**: onze ervaring voor het gebruik van het hulp programma heeft betrekking op belang rijke service provider scenario's, met inbegrip van meerdere licentie modellen, zoals EA, CSP en betalen naar gebruik. De nieuwe mogelijkheden werken met bestaande hulpprogram ma's en Api's, licentie modellen en partner Programma's, zoals het [Cloud Solution Provider-programma (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Azure Lighthouse kan worden geïntegreerd in uw bestaande werk stromen en toepassingen, en u kunt uw impact op klant afspraken bijhouden door [uw partner-id te koppelen](../billing/billing-partner-admin-link-started.md).

Er zijn geen extra kosten verbonden aan het gebruik van Azure Lighthouse voor het beheren van de Azure-resources van uw klanten.

## <a name="capabilities"></a>Functionaliteit

Azure Lighthouse bevat meerdere manieren om klant betrokkenheid en-beheer te stroom lijnen:

- **Azure delegated resource management**: beheer de Azure-resources van uw klanten veilig vanuit uw eigen Tenant, zonder dat u hoeft te scha kelen tussen context-en besturings plannen. Abonnementen en resource groepen kunnen worden gedelegeerd aan opgegeven gebruikers en rollen in de beheer Tenant, met de mogelijkheid om de toegang te verwijderen naar behoefte. Zie [Azure delegated resource management](concepts/azure-delegated-resource-management.md)voor meer informatie.
- **Nieuwe Azure Portal-ervaring**: Bekijk informatie over meerdere tenants op de pagina nieuwe **mijn klanten** in de [Azure Portal](https://portal.azure.com). Met een bijbehorende Blade **service providers** kunnen uw klanten toegang tot de service provider weer geven en beheren. Zie [klanten weer geven en beheren](./how-to/view-manage-customers.md) en [service providers weer geven en beheren](how-to/view-manage-service-providers.md)voor meer informatie.
- **Azure Resource Manager sjablonen**: beheer taken eenvoudiger uitvoeren, met inbegrip van onboarding van klanten voor het beheer van gedelegeerde resources van Azure. Voor meer informatie raadpleegt u onze voor [beelden opslag plaats](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) en maakt [u een klant onboarding van Azure gedelegeerde resource beheer](how-to/onboard-customer.md).
- **Aanbiedingen voor beheerde services in azure Marketplace**: bied uw services aan klanten aan via privé-of open bare aanbiedingen en laat deze automatisch onboarding uitvoeren op Azure delegated resource management, als alternatief voor onboarding met Azure Resource Manager sjablonen. Zie [Managed Services aanbiedingen in azure Marketplace](concepts/managed-services-offers.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](concepts/azure-delegated-resource-management.md).
- Meer informatie over de [ervaring op het beheer van cross-tenants](concepts/cross-tenant-management-experience.md).
