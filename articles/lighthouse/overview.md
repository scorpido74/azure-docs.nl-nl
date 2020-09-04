---
title: Wat is Azure Lighthouse?
description: Met Azure Lighthouse kunnen serviceproviders beheerde services leveren voor hun klanten, met een grotere mate van automatisering en efficiëntie op schaal.
ms.date: 08/12/2020
ms.topic: overview
ms.openlocfilehash: f65411c03725058e8aa6491245ffd94987efa6c9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163217"
---
# <a name="what-is-azure-lighthouse"></a>Wat is Azure Lighthouse?

Azure Lighthouse biedt een enkel bedieningspaneel om Azure-resources binnen verschillende tenants te bekijken en beheren, met hogere automatisering, op schaal en verbeterde governance. Met Azure Lighthouse kunnen serviceproviders beheerde services leveren met behulp van uitgebreide en krachtige beheerprogramma's die zijn ingebouwd in het Azure-platform. Deze aanbieding kan ook van pas komen voor zakelijke IT-organisaties die resources binnen meerdere tenants beheren.

![Overzichtsdiagram van Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Voordelen

Met Azure Lighthouse kunt u op een rendabele en efficiënte manier beheerde services bouwen en leveren. Enkele voordelen:

- **Beheer op schaal**: Bewerkingen op het gebied van klantbetrokkenheid en levenscyclus voor het beheer van klantresources zijn eenvoudiger en beter schaalbaar. Bestaande API's, beheerprogramma's en werkstromen kunnen worden gebruikt met gedelegeerde resources, ongeacht de regio's waarin ze zich bevinden.
- **Betere zichtbaarheid en precisie voor klanten**: Klanten hebben meer inzicht in uw acties en nauwkeurige controle over het bereik dat ze overdragen voor beheer, inclusief de mogelijkheid om de toegang volledig in te trekken, terwijl uw IP-adres behouden blijft.
- **Uitgebreide en geïntegreerde hulpprogramma's op het platform**: Onze ervaring met hulpprogramma's betekent dat we kunnen inspelen op belangrijke scenario's van serviceproviders, met inbegrip van verschillende licentiemodellen, zoals EA, CSP en betalen naar gebruik. De nieuwe mogelijkheden werken met bestaande hulpprogramma's en API's, licentiemodellen en partnerprogramma's, zoals het [Cloud Solution Provider-programma (CSP)](/partner-center/csp-overview). Azure Lighthouse kan worden geïntegreerd in bestaande werkstromen en toepassingen, en u kunt uw invloed op klantafspraken bijhouden door [uw partner-id te koppelen](../cost-management-billing/manage/link-partner-id.md).

Er zijn geen extra kosten verbonden aan het gebruik van Azure Lighthouse voor het beheren van de Azure-resources. Elke Azure-klant of -partner kan Azure Lighthouse gebruiken.

## <a name="capabilities"></a>Functionaliteit

Azure Lighthouse biedt meerdere manieren om betrokkenheid en -beheer te stroomlijnen:

- **Gedelegeerd resourcebeheer van Azure**: Beheer de Azure-resources van uw klanten veilig vanuit uw eigen tenant, zonder dat u van context of besturingsvlak hoeft te veranderen. Abonnementen en resourcegroepen kunnen worden gedelegeerd aan opgegeven gebruikers en rollen in de tenant die het beheer uitvoert, met de mogelijkheid om de toegang zo nodig te verwijderen. Zie [Gedelegeerd resourcebeheer van Azure](concepts/azure-delegated-resource-management.md) voor meer informatie.
- **Ervaringen met nieuwe Azure-portal**: Bekijk informatie voor meerdere tenants op de [pagina **Mijn klanten**](./how-to/view-manage-customers.md) in Azure Portal. Via een bijbehorende [pagina **Serviceproviders**](how-to/view-manage-service-providers.md) kunnen uw klanten de toegang tot hun serviceproviders bekijken en beheren.
- **Azure Resource Manager-sjablonen**: Met onze sjablonen kunt u beheertaken voor meerdere tenants en de onboarding uitvoeren van gedelegeerde klantresources. Zie onze [opslagplaats met voorbeelden](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) en [Een klant onboarden in Azure Lighthouse](how-to/onboard-customer.md) voor meer informatie.
- **Aanbiedingen voor beheerde service in Azure Marketplace**: Bied uw services aan klanten aan via privéaanbiedingen of openbare aanbiedingen en laat deze automatisch onboarden in Azure Lighthouse. Zie [Aanbiedingen voor beheerde service in Azure Marketplace](concepts/managed-services-offers.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](concepts/azure-delegated-resource-management.md).
- Verken [Beheerervaring in meerdere tenants](concepts/cross-tenant-management-experience.md).
- Zie [Azure Lighthouse gebruiken in een bedrijf](concepts/enterprise.md).
