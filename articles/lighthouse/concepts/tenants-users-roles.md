---
title: Tenants, rollen en gebruikers in azure Lighthouse-scenario's
description: Inzicht in de concepten van Azure Active Directory-tenants, gebruikers en rollen, en hoe ze kunnen worden gebruikt in azure Lighthouse-scenario's.
ms.date: 11/05/2019
ms.topic: conceptual
ms.openlocfilehash: 77a247c86765f25539833a6ba70f80e737c6846d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453571"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Tenants, rollen en gebruikers in azure Lighthouse-scenario's

Het is belang rijk om te begrijpen hoe Azure Active Directory (Azure AD)-tenants, gebruikers en rollen werken, en hoe ze kunnen worden gebruikt in azure [Lighthouse-scenario's](azure-delegated-resource-management.md).

Een *Tenant* is een speciaal en vertrouwd exemplaar van Azure AD. Normaal gesp roken vertegenwoordigt elke Tenant één organisatie. Met het gedelegeerde resource beheer van Azure kunt u resources van de ene Tenant naar een andere Tenant maken. Hierdoor kunnen gebruikers in de Tenant beheren (zoals één van een service provider) toegang krijgen tot gedelegeerde resources in de Tenant van een klant, of kunnen [ondernemingen met meerdere tenants hun beheer bewerkingen centraliseren](enterprise.md).

Als u deze logische projectie wilt uitvoeren, moet een abonnement (of een of meer resource groepen binnen een abonnement) in de *Tenant van de klant worden* opvolgd voor het beheer van de gedelegeerde resources van Azure. Dit voorbereidings proces kan worden uitgevoerd [via Azure Resource Manager sjablonen](../how-to/onboard-customer.md) of door [een open bare of persoonlijke aanbieding naar Azure Marketplace te publiceren](../how-to/publish-managed-services-offers.md).

Welke methode u ook kiest, u moet *autorisaties*definiëren. Elke autorisatie geeft een gebruikers account op in de beheer-Tenant die toegang heeft tot de gedelegeerde resources en een ingebouwde rol die de machtigingen instelt die elk van deze gebruikers voor deze resources heeft.

## <a name="role-support-for-azure-delegated-resource-management"></a>Functie ondersteuning voor Azure delegated resource management

Bij het definiëren van een autorisatie moet aan elk gebruikers account een van de [ingebouwde rollen op basis van op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/built-in-roles.md)worden toegewezen. Aangepaste rollen en [beheerders rollen voor klassieke abonnementen](../../role-based-access-control/classic-administrators.md) worden niet ondersteund.

Alle [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) worden momenteel ondersteund met Azure delegated resource management, met de volgende uitzonde ringen:

- De rol van [eigenaar](../../role-based-access-control/built-in-roles.md#owner) wordt niet ondersteund.
- Ingebouwde rollen met de machtiging [DataActions](../../role-based-access-control/role-definitions.md#dataactions) worden niet ondersteund.
- De ingebouwde rol [gebruikers toegangs beheerder](../../role-based-access-control/built-in-roles.md#user-access-administrator) wordt ondersteund, maar alleen voor het beperkte doel van het [toewijzen van rollen aan een beheerde identiteit in de Tenant van de klant](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Er worden gewoonlijk geen andere machtigingen verleend door deze rol. Als u een gebruiker met deze rol definieert, moet u ook de ingebouwde rol (len) opgeven die deze gebruiker aan beheerde identiteiten kan toewijzen.

## <a name="best-practices-for-defining-users-and-roles"></a>Aanbevolen procedures voor het definiëren van gebruikers en rollen

Wanneer u een autorisatie maakt, raden we u aan de volgende aanbevolen procedures uit te voeren:

- In de meeste gevallen moet u machtigingen toewijzen aan een Azure AD-gebruikers groep of Service-Principal, in plaats van aan een reeks afzonderlijke gebruikers accounts. Hiermee kunt u toegang voor afzonderlijke gebruikers toevoegen of verwijderen zonder dat u het plan hoeft bij te werken en opnieuw te publiceren wanneer uw toegangs vereisten veranderen.
- Zorg ervoor dat u het principe van minimale bevoegdheden volgt, zodat gebruikers alleen over de benodigde machtigingen beschikken om hun taak te volt ooien, waardoor de kans op onbedoelde fouten wordt verminderd. Zie [aanbevolen beveiligings procedures](../concepts/recommended-security-practices.md)voor meer informatie.
- Neem een gebruiker op met de functie voor het verwijderen van de [registratie toewijzing van beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , zodat u [de toegang tot de overdracht](../how-to/onboard-customer.md#remove-access-to-a-delegation) later indien nodig kunt verwijderen. Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de Tenant van de klant.
- Zorg ervoor dat alle gebruikers die [de pagina mijn klanten moeten weer geven in de Azure Portal](../how-to/view-manage-customers.md) over de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) beschikken (of een andere ingebouwde rol die lezers toegang bevat).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [aanbevolen beveiligings procedures voor het beheer van gedelegeerde resources van Azure](recommended-security-practices.md).
- Laat uw klanten onboarded resource management ongedaan maken, hetzij met [behulp van Azure Resource Manager sjablonen](../how-to/onboard-customer.md) of door [een aanbieding voor privé-of openbaar beheerde services naar Azure Marketplace te publiceren](../how-to/publish-managed-services-offers.md).
