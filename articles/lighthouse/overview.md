---
title: Wat is Azure Lighthouse?
description: Met Azure Lighthouse kunnen serviceproviders beheerde services leveren voor hun klanten, met een grotere mate van automatisering en efficiëntie op schaal.
ms.date: 05/28/2020
ms.topic: overview
ms.openlocfilehash: 119f1465c9fdf03779f28a46967c30d899ae37d5
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131364"
---
# <a name="what-is-azure-lighthouse"></a>Wat is Azure Lighthouse?

Azure Lighthouse biedt serviceproviders een enkel bedieningspaneel om Azure bij al hun klanten te bekijken en beheren, met hogere automatisering, op schaal en verbeterde governance. Met Azure Lighthouse kunnen serviceproviders beheerde services leveren met behulp van uitgebreide en krachtige beheerprogramma's die zijn ingebouwd in het Azure-platform. Deze aanbieding kan ook van pas komen voor zakelijke IT-organisaties die resources binnen meerdere tenants beheren.

![Overzichtsdiagram van Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Voordelen

Met Azure Lighthouse kunt u op een rendabele en efficiënte manier beheerde services bouwen en leveren aan uw klanten. Enkele voordelen:

- **Beheer op schaal**: Bewerkingen op het gebied van klantbetrokkenheid en levenscyclus voor het beheer van klantresources zijn eenvoudiger en beter schaalbaar. Bestaande API's, beheerprogramma's en werkstromen kunnen worden gebruikt met gedelegeerde klantresources, ongeacht de regio's waarin ze zich bevinden.
- **Betere zichtbaarheid en precisie voor klanten**: Klanten hebben meer inzicht in uw acties en nauwkeurige controle over het bereik dat ze overdragen voor beheer, inclusief de mogelijkheid om de toegang volledig in te trekken, terwijl uw IP-adres behouden blijft.
- **Uitgebreide en geïntegreerde hulpprogramma's op het platform**: Onze ervaring met hulpprogramma's betekent dat we kunnen inspelen op belangrijke scenario's van serviceproviders, met inbegrip van verschillende licentiemodellen, zoals EA, CSP en betalen naar gebruik. De nieuwe mogelijkheden werken met bestaande hulpprogramma's en API's, licentiemodellen en partnerprogramma's, zoals het [Cloud Solution Provider-programma (CSP)](/partner-center/csp-overview). Azure Lighthouse kan worden geïntegreerd in bestaande werkstromen en toepassingen, en u kunt uw invloed op klantafspraken bijhouden door [uw partner-id te koppelen](../cost-management-billing/manage/link-partner-id.md).

Er zijn geen extra kosten verbonden aan het gebruik van Azure Lighthouse voor het beheren van de Azure-resources van uw klanten. Elke Azure-klant of -partner kan Azure Lighthouse gebruiken.

## <a name="capabilities"></a>Functionaliteit

Azure Lighthouse biedt meerdere manieren om klantbetrokkenheid en -beheer te stroomlijnen:

- **Gedelegeerd resourcebeheer van Azure**: Beheer de Azure-resources van uw klanten veilig vanuit uw eigen tenant, zonder dat u van context of besturingsvlak hoeft te veranderen. Abonnementen en resourcegroepen kunnen worden gedelegeerd aan opgegeven gebruikers en rollen in de tenant die het beheer uitvoert, met de mogelijkheid om de toegang zo nodig te verwijderen. Zie [Gedelegeerd resourcebeheer van Azure](concepts/azure-delegated-resource-management.md) voor meer informatie.
- **Ervaringen met nieuwe Azure-portal**: Bekijk informatie voor meerdere tenants op de nieuwe pagina **Mijn klanten** in de [Azure-portal](https://portal.azure.com). Via een bijbehorende blade **Serviceproviders** kunnen uw klanten de toegang tot serviceproviders weergeven en beheren. Zie [Klanten weergeven en beheren](./how-to/view-manage-customers.md) en [Serviceproviders weergeven en beheren](how-to/view-manage-service-providers.md) voor meer informatie.
- **Azure Resource Manager-sjablonen**: Met deze sjablonen kunt u beheertaken gemakkelijker uitvoeren, met inbegrip van de onboarding van klanten voor het beheer van gedelegeerde resources van Azure. Zie voor meer informatie onze [opslagplaats met voorbeelden](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) en [Een klant in gedelegeerd Azure-resourcebeheer plaatsen](how-to/onboard-customer.md).
- **Aanbiedingen voor beheerde service in Azure Marketplace**: Bied uw services aan klanten aan via privé- of openbare aanbiedingen en laat ze automatisch onboarden via gedelegeerd resourcebeheer van Azure, als alternatief voor onboarding met Azure Resource Manager-sjablonen. Zie [Aanbiedingen voor beheerde service in Azure Marketplace](concepts/managed-services-offers.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](concepts/azure-delegated-resource-management.md).
- Meer informatie over [beheerervaring in meerdere tenants](concepts/cross-tenant-management-experience.md).
