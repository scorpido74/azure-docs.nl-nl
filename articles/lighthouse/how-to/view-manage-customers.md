---
title: Klanten en gedelegeerde resources weergeven en beheren
description: Als serviceprovider die Azure-gedelegeerd resourcebeheer gebruikt, u al uw gedelegeerde klantbronnen en -abonnementen bekijken door naar Mijn klanten te gaan in de Azure-portal.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543423"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Klanten en gedelegeerde resources weergeven en beheren

Serviceproviders die [Azure-gedelegeerd bronnenbeheer](../concepts/azure-delegated-resource-management.md) gebruiken, kunnen de pagina **Mijn klanten** in de [Azure-portal](https://portal.azure.com) gebruiken om gedelegeerde klantbronnen en -abonnementen weer te geven. Hoewel we hier verwijzen naar serviceproviders en klanten, kunnen bedrijven die meerdere tenants beheren hetzelfde proces gebruiken om hun beheerervaring te consolideren.

Als u de pagina **Mijn klanten** in de Azure-portal wilt openen, selecteert u **Alle services**en zoekt u vervolgens naar Mijn **klanten** en selecteert u deze. U het ook vinden door 'Mijn klanten' in te voeren in het zoekvak boven aan de Azure-portal.

Houd er rekening mee dat in het bovenste **gedeelte van klanten** van de pagina Mijn **klanten** alleen informatie wordt weergegeven over klanten die abonnementen of brongroepen hebben gedelegeerd. Als u met andere klanten werkt (bijvoorbeeld via het [Cloud Solution Provider-programma,](https://docs.microsoft.com/partner-center/csp-overview)ziet u geen informatie over die klanten in de sectie **Klanten,** tenzij u hun resources hebt ingeschakeld voor azure-gedelegeerd bronbeheer.

Lager op de pagina wordt in een aparte sectie met de naam **Cloud Solution Provider (Preview)** factureringsgegevens en resources weergegeven voor uw CSP-klanten die [de Microsoft Customer Agreement (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) hebben ondertekend en [onder het Azure-abonnement vallen.](https://docs.microsoft.com/partner-center/azure-plan-get-started) Zie [Aan de slag met uw factureringsaccount voor Microsoft Partner Overeenkomst](../../billing/mpa-overview.md)voor meer informatie . Houd er rekening mee dat dergelijke CSP-klanten in deze sectie worden weergegeven, ongeacht of u ze ook hebt ingeschakeld voor azure-gedelegeerd bronbeheer. Op dezelfde manier hoeft een CSP-klant niet te worden weergegeven in het gedeelte **Cloud Solution Provider (Preview)** van **Mijn klanten,** zodat u ze aan boord nemen voor azure-gedelegeerd bronbeheer.

> [!NOTE]
> Uw klanten kunnen informatie over serviceproviders bekijken door te navigeren naar **serviceproviders** in de Azure-portal. Zie [Serviceproviders weergeven en beheren](view-manage-service-providers.md)voor meer informatie.

## <a name="view-and-manage-customer-details"></a>Klantgegevens weergeven en beheren

Als u klantgegevens wilt weergeven, selecteert u **Klanten** aan de linkerkant van de pagina **Mijn klanten.**

Voor elke klant ziet u de naam van de klant, de klant-id (tenant-id) en de aanbieding die aan de overeenkomst is gekoppeld. In de kolom **Delegaties** ziet u het aantal gedelegeerde abonnementen en/of het aantal gedelegeerde resourcegroepen.

> [!IMPORTANT]
> Om een delegatie te kunnen zien, moeten gebruikers de [readerrol](../../role-based-access-control/built-in-roles.md#reader) (of een andere ingebouwde rol met readertoegang) in het onboardingproces hebben gekregen.

Met filters boven aan de pagina u uw klantgegevens sorteren en groeperen of filteren op specifieke klanten, aanbiedingen of zoekwoorden.

U de volgende informatie bekijken op deze pagina:

- Als u alle abonnementen, aanbiedingen en delegaties wilt bekijken die zijn gekoppeld aan een klant, selecteert u de naam van de klant.
- Als u meer informatie wilt over een aanbieding en de delegaties, selecteert u de naam van de aanbieding.
- Als u meer informatie wilt weergeven over roltoewijzingen voor gedelegeerde abonnementen of resourcegroepen, selecteert u de vermelding in de kolom **Delegaties.**

## <a name="view-and-manage-delegations"></a>Delegaties bekijken en beheren

In delegaties wordt de gedelegeerde abonnements-/resourcegroep weergegeven, samen met de gebruikers en machtigingen die er toegang toe hebben. Als u deze informatie wilt weergeven, selecteert u **Delegaties** aan de linkerkant van de pagina **Mijn klanten.**

Met filters boven aan de pagina u uw informatie over toegangstoewijzing sorteren en groeperen of filteren op specifieke klanten, aanbiedingen of zoekwoorden.

### <a name="view-role-assignments"></a>Roltoewijzingen weergeven

De gebruikers en machtigingen die aan elke delegatie zijn gekoppeld, worden weergegeven in de kolom **Toewijzingen van** rollen. U elk item selecteren om de volledige lijst met gebruikers, groepen en serviceprincipals weer te geven die toegang hebben gekregen tot het abonnement of de brongroep. Van daaruit u een bepaalde naam voor gebruiker, groep of service selecteren om meer details te krijgen.

### <a name="remove-delegations"></a>Delegaties verwijderen

Als u gebruikers hebt opgenomen met de [rol Beheerde services registratietoewijzing](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) wanneer u een klant inbrengt voor azure-gedelegeerd bronbeheer, kunnen deze gebruikers een delegatie verwijderen door het pictogram prullenbak te selecteren dat in de rij voor die delegatie wordt weergegeven. Wanneer ze dit doen, hebben geen gebruikers in de tenant van de serviceprovider toegang tot de bronnen die eerder waren gedelegeerd.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Werken in het kader van een gedelegeerd abonnement

U rechtstreeks werken in de context van een gedelegeerd abonnement binnen de Azure-portal, zonder de map waarin u werkt te wijzigen. Dit doet u als volgt:

1. Selecteer het pictogram **Directory + Abonnement** boven aan de Azure-portal.
2. Controleer in het **filter Globaal abonnement** of alleen het vak voor dat gedelegeerdabonnement is geselecteerd. U het vervolgkeuzeveld **Huidige + gedelegeerde mappen** gebruiken om alleen abonnementen in een specifieke map weer te geven. (Gebruik de optie **Switch-map** niet, omdat hiermee de map wordt gewijzigd waarop u bent aangemeld.)

Als u vervolgens toegang krijgt tot een service die [beheerervaringen met meerdere tenant's](../concepts/cross-tenant-management-experience.md)ondersteunt, wordt de service standaard ingesteld op de context van het gedelegeerde abonnement dat u hebt geselecteerd. U dit wijzigen door de bovenstaande stappen te volgen en het **vakje Alles selecteren** in te schakelen (of een of meer abonnementen te kiezen om in plaats daarvan in te werken).

> [!NOTE]
> Als u toegang hebt gekregen tot een of meer brongroepen, in plaats van toegang tot een volledig abonnement, u het abonnement selecteren waartoe die brongroep behoort. U werkt dan in de context van dat abonnement, maar hebt alleen toegang tot de aangewezen brongroepen.

U hebt ook toegang tot functionaliteit met betrekking tot gedelegeerde abonnementen of resourcegroepen vanuit services die cross-tenantbeheerervaringen ondersteunen door de abonnements- of resourcegroep te selecteren vanuit die service.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [cross-tenant management ervaringen](../concepts/cross-tenant-management-experience.md).
- Ontdek hoe uw klanten [serviceproviders](view-manage-service-providers.md) kunnen bekijken en beheren door naar **serviceproviders** in de Azure-portal te gaan.
