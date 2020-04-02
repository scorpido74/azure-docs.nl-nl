---
title: Meer informatie over gedelegeerd resourcebeheer
description: Met beheerde services kunnen serviceproviders aanbiedingen voor resourcebeheer verkopen aan klanten in Azure Marketplace.
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: db9f562ca4f42d1c1d85eeac44495a8ec7e01beb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548426"
---
# <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Azure delegated resource management is een van de belangrijkste onderdelen van Azure Lighthouse. Met Azure gedelegeerd resourcebeheer kunnen serviceproviders de betrokkenheid van klanten en onboarding-ervaringen vereenvoudigen, terwijl gedelegeerde resources op schaal worden beheerd met flexibiliteit en precisie.

## <a name="what-is-azure-delegated-resource-management"></a>Wat is Azure-gedelegeerd bronbeheer?

Azure gedelegeerd resourcebeheer maakt logische projectie van resources van de ene tenant naar een andere tenant mogelijk. Hiermee kunnen geautoriseerde gebruikers in één Azure Active Directory-tenant (Azure AD) beheerbewerkingen uitvoeren voor verschillende Azure AD-tenants die behoren tot hun klanten. Serviceproviders kunnen zich aanmelden bij hun eigen Azure AD-tenant en hebben toestemming om te werken in gedelegeerde klantabonnementen en resourcegroepen. Hierdoor kunnen ze beheerbewerkingen uitvoeren namens hun klanten, zonder dat ze zich hoeven aan te melden bij elke individuele klanttenant.

> [!NOTE]
> Azure gedelegeerd resourcebeheer kan ook worden gebruikt [binnen een onderneming die meerdere Azure AD-tenants heeft om](enterprise.md) het beheer van meerdere tenants te vereenvoudigen.

Met Azure gedelegeerd resourcebeheer kunnen geautoriseerde gebruikers rechtstreeks werken in de context van een klantabonnement zonder dat ze een account hebben in de tenant van die klant of mede-eigenaar zijn van de tenant van de klant. Ze kunnen ook [alle gedelegeerde klantabonnementen bekijken en beheren op de nieuwe pagina **Mijn klanten** ](../how-to/view-manage-customers.md) in de Azure-portal.

Met [de beheerervaring voor meerdere tenants](cross-tenant-management-experience.md) u efficiënter werken met Azure-beheerservices zoals Azure Policy, Azure Security Center en meer. Alle activiteit van de serviceprovider wordt bijgehouden in het activiteitenlogboek, dat wordt opgeslagen in de tenant van de klant (en kan worden bekeken door gebruikers in de beheertenant). Dit betekent dat zowel de klant als de serviceprovider gemakkelijk de gebruiker kunnen identificeren die aan wijzigingen is gekoppeld.

Wanneer u een klant aan boord maakt van azure-gedelegeerde bronbeheer, hebben ze toegang tot de nieuwe pagina **met serviceproviders** in de Azure-portal, waar ze [hun aanbiedingen, serviceproviders en gedelegeerde resources](../how-to/view-manage-service-providers.md)kunnen bevestigen en beheren. Als de klant ooit de toegang voor een serviceprovider wil intrekken, kan hij dit hier op elk gewenst moment doen.

U [het nieuwe type Managed Service-aanbieding publiceren naar Azure Marketplace](../how-to/publish-managed-services-offers.md) om klanten eenvoudig aan te boorden van azure-gedelegeerd bronbeheer. U ook [het onboardingproces voltooien door Azure Resource Manager-sjablonen te implementeren.](../how-to/onboard-customer.md)

## <a name="how-azure-delegated-resource-management-works"></a>Hoe Azure gedelegeerd resourcebeheer werkt

Op hoog niveau werkt het gedelegeerd resourcebeheer van Azure als een van de besteed bronnen:

1. Als serviceprovider identificeert u de toegang (rollen) die uw groepen, serviceprincipals of gebruikers nodig hebben om de Azure-bronnen van de klant te beheren. De definitie van toegang bevat de tenant-id van de serviceprovider, samen met de vereiste toegang voor de aanbieding, gedefinieerd met behulp van **principalId-identiteiten** van uw tenant die zijn toegewezen aan [ingebouwde **rolDefinitiewaarden** ](../../role-based-access-control/built-in-roles.md) (inzender, VM-bijdrager, lezer, enz.).
2. U geeft deze toegang en aan boord van de klant aan azure gedelegeerd resourcebeheer op twee manieren op:
   - [Een azure Marketplace managed service-aanbieding publiceren](../how-to/publish-managed-services-offers.md) (privé of openbaar) die de klant accepteert
   - [Een Azure Resource Manager-sjabloon implementeren voor de tenant van de klant](../how-to/onboard-customer.md) voor een of meer specifieke abonnementen of resourcegroepen
3. Zodra de klant aan boord is gegaan, kunnen geautoriseerde gebruikers zich aanmelden bij de tenant van uw serviceprovider en beheertaken uitvoeren op basis van de door u gedefinieerde toegang.

> [!NOTE]
> De overdracht van een abonnement tussen twee tenants in afzonderlijke clouds wordt niet ondersteund.

## <a name="support-for-azure-delegated-resource-management"></a>Ondersteuning voor azure gedelegeerd bronbeheer

Als u hulp nodig hebt in verband met Azure gedelegeerd bronbeheer, u een ondersteuningsaanvraag openen in de Azure-portal. Kies Voor **Probleemtype**De optie **Technisch**. Selecteer een abonnement en selecteer **Lighthouse** (onder **Toezicht & beheer).**

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [cross-tenant management ervaringen](cross-tenant-management-experience.md).
- Meer informatie over [aanbiedingen voor beheerde services in Azure Marketplace](managed-services-offers.md).