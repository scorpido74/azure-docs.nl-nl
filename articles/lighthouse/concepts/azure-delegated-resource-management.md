---
title: Meer informatie over gedelegeerd resourcebeheer
description: Azure delegated resource management is een belang rijk onderdeel van Azure Lighthouse, zodat service providers gedelegeerde resources op schaal kunnen beheren met flexibiliteit en precisie.
ms.date: 05/28/2020
ms.topic: conceptual
ms.openlocfilehash: bbe3c28cdcd252755b8350eaa5d2e72044981174
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120888"
---
# <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Azure delegated resource management is een van de belangrijkste onderdelen van [Azure Lighthouse](../overview.md). Met Azure delegated resource management kunnen service providers de klant betrokkenheid en de onboarding-ervaring vereenvoudigen, terwijl gedelegeerde resources op schaal worden beheerd met flexibiliteit en precisie.

## <a name="what-is-azure-delegated-resource-management"></a>Wat is Azure delegated resource management?

Met het gedelegeerde resource beheer van Azure kunt u resources van de ene Tenant naar een andere Tenant maken. Hierdoor kunnen geautoriseerde gebruikers in één Azure Active Directory (Azure AD)-Tenant beheer bewerkingen uitvoeren in verschillende Azure AD-tenants die deel uitmaken van hun klanten. Service providers kunnen zich aanmelden bij hun eigen Azure AD-Tenant en hebben toestemming om te werken in gedelegeerde klant abonnementen en resource groepen. Hierdoor kunnen ze beheer bewerkingen uitvoeren namens hun klanten, zonder dat ze zich hoeven aan te melden bij elke afzonderlijke klant Tenant.

> [!NOTE]
> Azure delegated resource management kan ook worden gebruikt [binnen een onderneming met meerdere Azure AD-tenants](enterprise.md) , om het beheer van meerdere tenants te vereenvoudigen.

Met Azure delegated resource management kunnen geautoriseerde gebruikers rechtstreeks in de context van een klant abonnement werken zonder dat ze een account in de Tenant van de klant hebben of mede-eigenaar zijn van de Tenant van de klant. Ze kunnen ook [alle gedelegeerde klant abonnementen weer geven en beheren op de pagina nieuwe **mijn klanten** ](../how-to/view-manage-customers.md) in de Azure Portal.

Met de [beheer ervaring voor cross-tenants](cross-tenant-management-experience.md) kunt u efficiënter werken met Azure-beheer Services, zoals Azure Policy, Azure Security Center en meer. Alle activiteiten van de service provider worden bijgehouden in het activiteiten logboek, dat is opgeslagen in de Tenant van de klant (en kan worden weer gegeven door gebruikers in de Tenant beheren). Dit betekent dat zowel de klant als de service provider gemakkelijk de gebruiker kan identificeren die is gekoppeld aan wijzigingen.

Wanneer u een klant voor het beheer van Azure-gedelegeerde resources ongedaan maakt, hebben ze toegang tot de pagina **service providers** in het Azure Portal, waar ze [hun aanbiedingen, service providers en gedelegeerde resources kunnen bevestigen en beheren](../how-to/view-manage-service-providers.md). Als de klant ooit toegang wil intrekken voor een service provider, kan dit hier op elk gewenst moment worden gedaan.

U kunt [het nieuwe type managed service-aanbieding publiceren naar Azure Marketplace](../how-to/publish-managed-services-offers.md) , zodat klanten eenvoudig kunnen worden gemigreerd naar Azure gedelegeerde resource beheer. U kunt ook [het voorbereidings proces volt ooien door Azure Resource Manager sjablonen te implementeren](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Hoe Azure gedelegeerd resource beheer werkt

Op hoog niveau is dit de manier waarop Azure gedelegeerd resource management werkt:

1. Als service provider identificeert u de toegang (rollen) die uw groepen, service-principals of gebruikers nodig hebben voor het beheren van de Azure-resources van de klant. De toegangs definitie bevat de Tenant-ID van de service provider, samen met de vereiste toegang voor de aanbieding, die is gedefinieerd met behulp van **principalId** -identiteiten uit uw Tenant die is toegewezen aan [ingebouwde **roleDefinition** -waarden](../../role-based-access-control/built-in-roles.md) (Inzender, VM-bijdrager, lezer, enzovoort).
2. U geeft deze toegang op een van de volgende twee manieren op om de klant in te delen voor het beheer van Azure-resources:
   - [Een Azure Marketplace managed service-aanbieding](../how-to/publish-managed-services-offers.md) (privé of openbaar) publiceren die door de klant wordt geaccepteerd
   - [Een Azure Resource Manager-sjabloon implementeren naar de Tenant van de klant](../how-to/onboard-customer.md) voor een of meer specifieke abonnementen of resource groepen
3. Zodra de klant onboarded is, kunnen geautoriseerde gebruikers zich aanmelden bij de Tenant van de service provider en beheer taken uitvoeren op het opgegeven klant bereik, op basis van de toegang die u hebt gedefinieerd.

> [!NOTE]
> U kunt gedelegeerde resources beheren die zich in verschillende [regio's](../../availability-zones/az-overview.md#regions)bevinden. Overdracht van abonnementen over een [nationale Cloud](../../active-directory/develop/authentication-national-cloud.md) en de open bare Azure-Cloud, of over twee afzonderlijke nationale Clouds, wordt echter niet ondersteund.

## <a name="support-for-azure-delegated-resource-management"></a>Ondersteuning voor Azure delegated resource management

Als u hulp nodig hebt met betrekking tot het beheer van de gedelegeerde resources van Azure, kunt u een ondersteunings aanvraag openen in de Azure Portal. Kies voor **type probleem**de optie **technisch**. Selecteer een abonnement en selecteer vervolgens **Lighthouse** (onder **bewaking & Management**).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](cross-tenant-management-experience.md).
- Meer informatie over [Managed Services-aanbiedingen in azure Marketplace](managed-services-offers.md).