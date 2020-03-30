---
title: Tenants, rollen en gebruikers in Azure Lighthouse-scenario's
description: Inzicht in de concepten van Azure Active Directory-tenants, -gebruikers en -rollen en hoe ze kunnen worden gebruikt in Azure Lighthouse-scenario's.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7540e17fd80f9a1d8e996295000c126614b838d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246888"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Tenants, rollen en gebruikers in Azure Lighthouse-scenario's

Voordat u klanten inhuurt voor [Azure-gedelegeerd bronbeheer,](azure-delegated-resource-management.md)is het belangrijk om te begrijpen hoe azure active directory-tenants, gebruikers en rollen werken en hoe ze kunnen worden gebruikt in Azure Lighthouse-scenario's.

Een *tenant* is een speciaal en vertrouwd exemplaar van Azure AD. Elke tenant vertegenwoordigt doorgaans één organisatie. Azure gedelegeerd resourcebeheer maakt logische projectie van resources van de ene tenant naar de andere tenant mogelijk. Hierdoor kunnen gebruikers in de beheertenant (zoals een tenant) toegang krijgen tot gedelegeerde resources in de tenant van een klant, of kunnen [ondernemingen met meerdere tenants hun beheeractiviteiten centraliseren.](enterprise.md)

Om deze logische projectie te bereiken, moet een abonnement (of een of meer resourcegroepen binnen een abonnement) in de klanttenant worden *ingeschakeld* voor azure-gedelegeerd resourcebeheer. Dit onboarding-proces kan worden uitgevoerd [via Azure Resource Manager-sjablonen](../how-to/onboard-customer.md) of door [een openbare of privéaanbieding te publiceren op Azure Marketplace.](../how-to/publish-managed-services-offers.md)

Welke onboarding-methode u ook kiest, u moet *autorisaties*definiëren. Elke autorisatie geeft een gebruikersaccount op in de beheertenant die toegang heeft tot de gedelegeerde resources en een ingebouwde rol die de machtigingen instelt die elk van deze gebruikers voor deze bronnen heeft.

## <a name="role-support-for-azure-delegated-resource-management"></a>Functieondersteuning voor Azure gedelegeerd bronbeheer

Bij het definiëren van een autorisatie moet aan elk gebruikersaccount een van de [rbac-ingebouwde rollen (Role-based access control)](../../role-based-access-control/built-in-roles.md)worden toegewezen. Aangepaste rollen en [klassieke abonnementsbeheerdersrollen](../../role-based-access-control/classic-administrators.md) worden niet ondersteund.

Alle [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) worden momenteel ondersteund met Azure-gedelegeerd bronbeheer, met de volgende uitzonderingen:

- De rol [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) wordt niet ondersteund.
- Ingebouwde rollen met de toestemming [van DataActions](../../role-based-access-control/role-definitions.md#dataactions) worden niet ondersteund.
- De ingebouwde rol [van gebruikerstoegangsbeheerder](../../role-based-access-control/built-in-roles.md#user-access-administrator) wordt ondersteund, maar alleen voor het beperkte doel om [rollen toe te wijzen aan een beheerde identiteit in de klanttenant.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant) Er zijn geen andere machtigingen die doorgaans door deze rol worden verleend. Als u een gebruiker met deze rol definieert, moet u ook de ingebouwde rol(en) opgeven die deze gebruiker aan beheerde identiteiten kan toewijzen.

> [!NOTE]
> Zodra een toepasselijke nieuwe ingebouwde rol aan Azure is toegevoegd, kan deze worden toegewezen wanneer [u een klant instelt met Azure Resource Manager-sjablonen.](../how-to/onboard-customer.md) Er kan een vertraging optreden voordat de nieuw toegevoegde rol beschikbaar komt in Cloud Partner Portal bij [het publiceren van een managed service-aanbieding.](../how-to/publish-managed-services-offers.md)

## <a name="best-practices-for-defining-users-and-roles"></a>Aanbevolen procedures voor het definiëren van gebruikers en rollen

Bij het maken van uw autorisaties raden we de volgende aanbevolen procedures aan:

- In de meeste gevallen wilt u machtigingen toewijzen aan een Azure AD-gebruikersgroep of serviceprincipal, in plaats van aan een reeks afzonderlijke gebruikersaccounts. Hiermee u toegang toevoegen of verwijderen voor individuele gebruikers zonder dat u het abonnement hoeft bij te werken en opnieuw te publiceren wanneer uw toegangsvereisten worden gewijzigd.
- Zorg ervoor dat u het principe van de minste bevoegdheden te volgen, zodat gebruikers alleen de machtigingen die nodig zijn om hun taak te voltooien, helpen om de kans op onbedoelde fouten te verminderen. Zie [Aanbevolen beveiligingsprocedures voor](../concepts/recommended-security-practices.md)meer informatie .
- Neem een gebruiker op met de [rol Registratietoewijzing Beheerservices,](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) zodat u de [toegang tot de delegatie](../how-to/onboard-customer.md#remove-access-to-a-delegation) later verwijderen als dat nodig is. Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de tenant van de klant.
- Zorg ervoor dat elke gebruiker die [de pagina Mijn klanten in de Azure-portal](../how-to/view-manage-customers.md) moet bekijken, de [leesrol](../../role-based-access-control/built-in-roles.md#reader) heeft (of een andere ingebouwde rol die lezerstoegang bevat).

> [!IMPORTANT]
> Als u machtigingen wilt toevoegen voor een Azure AD-groep, moet het **type Groep** **beveiliging** zijn en geen **Office 365**. Deze optie wordt geselecteerd wanneer de groep wordt gemaakt. Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [aanbevolen beveiligingsprocedures voor azure-gedelegeerd bronbeheer](recommended-security-practices.md).
- Uw klanten aan boord van Azure gedelegeerd resourcebeheer, door [Azure Resource Manager-sjablonen](../how-to/onboard-customer.md) te gebruiken of [door een privé- of openbare beheerde servicesaantebieden op Azure Marketplace te publiceren.](../how-to/publish-managed-services-offers.md)
