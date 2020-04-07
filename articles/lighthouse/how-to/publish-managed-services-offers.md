---
title: Een beheerde serviceaanbieding publiceren op Azure Marketplace
description: Meer informatie over het publiceren van een beheerde serviceaanbieding die klanten aanbiedt aan azure gedelegeerd resourcebeheer.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673938"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Een beheerde serviceaanbieding publiceren op Azure Marketplace

In dit artikel leert u hoe u een openbare of particuliere beheerde serviceaanbieding naar [Azure Marketplace](https://azuremarketplace.microsoft.com) publiceert met behulp van het [Commercial Marketplace-programma](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) in [partnercentrum.](https://partner.microsoft.com/) Klanten die de aanbieding kopen, kunnen vervolgens abonnementen en resourcegroepen aan boord nemen voor [azure-gedelegeerd resourcebeheer.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Publicatievereisten

Je moet een geldig account hebben [in Partner Center](../../marketplace/partner-center-portal/create-account.md) om aanbiedingen te maken en te publiceren. Als je nog geen account hebt, leidt het [aanmeldingsproces](https://aka.ms/joinmarketplace) je door de stappen van het maken van een account in Partner Center en je inschrijven voor het Commercial Marketplace-programma.

Volgens de [Managed Service-certificeringsvereisten](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)moet u een competentieniveau van het [Silver- of Gold Cloud Platform](https://docs.microsoft.com/partner-center/learn-about-competencies) hebben of een Azure Expert [MSP](https://partner.microsoft.com/membership/azure-expert-msp) zijn om een Managed Service-aanbieding te publiceren.

Uw MPN-id (Microsoft Partner Network) wordt [automatisch gekoppeld aan](../../billing/billing-partner-admin-link-started.md) de aanbiedingen die u publiceert om uw impact op klantinteracties bij te houden.

> [!NOTE]
> Als u een aanbieding niet wilt publiceren naar Azure Marketplace, u klanten handmatig ingebruiknemen met Azure Resource Manager-sjablonen. Zie [Een klant aan boord van Azure-gedelegeerd resourcebeheer](onboard-customer.md)voor meer informatie.

## <a name="create-your-offer"></a>Uw voorstel maken

Zie [Een nieuwe aanbieding voor Managed Services maken](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)voor gedetailleerde instructies over het maken van uw aanbieding, inclusief alle informatie en assets die u moet verstrekken.

Zie [Azure Marketplace en AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md)voor meer informatie over het algemene publicatieproces. U moet ook het [certificeringsbeleid voor commerciële marktplaatsen](https://docs.microsoft.com/legal/marketplace/certification-policies)bekijken, met name de sectie [Managed Services.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Zodra een klant uw aanbieding toevoegt, kan deze een of meer abonnementen of resourcegroepen delegeren, die vervolgens worden [ingeschakeld voor azure-gedelegeerd bronbeheer.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Elk plan in een beheerde serviceaanbieding bevat een sectie **Manifestgegevens,** waarin u de Azure Active Directory-entiteiten (Azure AD) in uw tenant definieert die toegang hebben tot de gedelegeerde brongroepen en/of abonnementen voor klanten die dat abonnement kopen. Het is belangrijk om te weten dat elke groep (of gebruiker of service principal) die u opneemt, dezelfde machtigingen heeft voor elke klant die het abonnement koopt. Als u verschillende groepen wilt toewijzen om met elke klant samen te werken, moet u een afzonderlijk [privéplan](../../marketplace/private-offers.md) publiceren dat exclusief is voor elke klant.

## <a name="publish-your-offer"></a>Uw voorstel publiceren

Zodra u alle secties hebt voltooid, is uw volgende stap het publiceren van de aanbieding naar Azure Marketplace. Selecteer de knop **Publiceren** om het proces van het live maken van uw aanbieding te starten. Meer informatie over dit proces is [hier](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)te vinden.

U op elk gewenst moment [een bijgewerkte versie van uw aanbieding publiceren.](../../marketplace/partner-center-portal/update-existing-offer.md) U bijvoorbeeld een nieuwe roldefinitie toevoegen aan een eerder gepubliceerde aanbieding. Wanneer u dit doet, zien klanten die de aanbieding al hebben toegevoegd een pictogram op de pagina [**Serviceproviders**](view-manage-service-providers.md) in de Azure-portal waarmee ze weten dat er een update beschikbaar is. Elke klant kan [de wijzigingen bekijken](view-manage-service-providers.md#update-service-provider-offers) en beslissen of hij of zij de nieuwe versie wil bijwerken. 

## <a name="the-customer-onboarding-process"></a>Het onboardingproces van de klant

Nadat een klant uw aanbieding heeft toegevoegd, kunnen ze [een of meer specifieke abonnementen of resourcegroepen delegeren,](view-manage-service-providers.md#delegate-resources)die vervolgens worden ingeschakeld voor azure-gedelegeerd bronbeheer. Als een klant een aanbieding heeft geaccepteerd, maar nog geen bronnen heeft gedelegeerd, ziet hij een notitie boven aan het gedeelte **Provideraanbiedingen** van de pagina [**Serviceproviders**](view-manage-service-providers.md) in de Azure-portal.

> [!IMPORTANT]
> Delegeren moet worden gedaan door een niet-gastaccount in de tenant van de klant, waarbij de [eigenaar de ingebouwde rol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) heeft voor het abonnement dat wordt aanboord (of die de resourcegroepen bevat die aan boord worden genomen). Als u alle gebruikers wilt zien die het abonnement kunnen delegeren, kan een gebruiker in de tenant van de klant het abonnement selecteren in de Azure-portal, **Access-besturingselement (IAM)** openen en [alle gebruikers weergeven met de rol Eigenaar](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Zodra de klant een abonnement heeft gedelegeerd (of een of meer resourcegroepen binnen een abonnement), wordt de **Microsoft.ManagedServices-resourceprovider** geregistreerd voor dat abonnement en hebben gebruikers in uw tenant toegang tot de gedelegeerde resources op basis van de autorisaties in uw aanbieding.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [commerciële marktplaats](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Meer informatie over [cross-tenant management ervaringen](../concepts/cross-tenant-management-experience.md).
- [Klanten bekijken en beheren](view-manage-customers.md) door naar **Mijn klanten** te gaan in de Azure-portal.
