---
title: Klanten en gedelegeerde resources weer geven en beheren
description: Als service provider met behulp van Azure delegated Resource Management kunt u al uw gedelegeerte klant bronnen en abonnementen weer geven door naar mijn klanten te gaan in de Azure Portal.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 53aabc4dc6c0d3fcef28aab16ea598cbbf6dbd55
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302741"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Klanten en gedelegeerde resources weer geven en beheren

Service providers die gebruikmaken van [Azure delegated resource management](../concepts/azure-delegated-resource-management.md) kunnen de pagina **mijn klanten** in het [Azure Portal](https://portal.azure.com) gebruiken om gedelegeerde klant resources en abonnementen weer te geven. Hoewel we hier naar service providers en klanten verwijzen, kunnen bedrijven die meerdere tenants beheren, hetzelfde proces gebruiken om hun beheer ervaring te consolideren.

Als u toegang wilt krijgen tot de pagina **mijn klanten** in de Azure Portal, selecteert u **alle services**, zoekt u naar **mijn klanten** en selecteert u deze. U kunt deze ook vinden door ' mijn klanten ' in te voeren in het zoekvak boven aan de Azure Portal.

Houd er rekening mee dat de pagina **mijn klanten** alleen informatie over klanten bevat die abonnementen of resource groepen hebben gedelegeerd. Als u met andere klanten werkt (zoals via het [Cloud Solution Provider-programma](https://docs.microsoft.com/partner-center/csp-overview)), ziet u hier geen informatie over deze klanten, tenzij u de resources uitnodigt voor gedelegeerd resource beheer.

> [!NOTE]
> Uw klanten kunnen informatie over service providers weer geven door te navigeren naar **service providers** in de Azure Portal. Zie [service providers weer geven en beheren](view-manage-service-providers.md)voor meer informatie.

## <a name="view-and-manage-customer-details"></a>Klant gegevens weer geven en beheren

Selecteer **klanten** aan de linkerkant van de pagina **mijn klanten** om klant gegevens weer te geven.

Voor elke klant ziet u de naam van de klant, de klant-ID (Tenant-ID) en de aanbieding die aan de afspraak is gekoppeld. In de kolom **delegaties** ziet u het aantal gedelegeerde abonnementen en/of het aantal gedelegeerde resource groepen.

Met filters boven aan de pagina kunt u uw klant gegevens sorteren en groeperen of filteren op specifieke klanten, aanbiedingen of tref woorden.

U kunt de volgende informatie op deze pagina bekijken:

- Als u alle abonnementen, aanbiedingen en delegaties wilt zien die zijn gekoppeld aan een klant, selecteert u de naam van de klant.
- Als u meer informatie over een aanbieding en de delegaties wilt zien, selecteert u de naam van het aanbod.
- Als u meer informatie over roltoewijzingen voor gedelegeerde abonnementen of resource groepen wilt weer geven, selecteert u de vermelding in de kolom **delegaties** .

## <a name="view-delegations"></a>Delegaties weer geven

Delegaties tonen het abonnement/de resource groep die is gedelegeerd, samen met de gebruikers en machtigingen die er toegang toe hebben. Als u deze informatie wilt weer geven, selecteert u **delegaties** aan de linkerkant van de pagina **mijn klanten** .

Met filters boven aan de pagina kunt u uw toegangs toewijzings gegevens sorteren en groeperen of filteren op specifieke klanten, aanbiedingen of tref woorden.

De gebruikers en machtigingen die aan elke delegatie zijn gekoppeld, worden weer gegeven in **de kolom roltoewijzingen** . U kunt elk item selecteren om de volledige lijst met gebruikers, groepen en service-principals weer te geven waaraan toegang is verleend tot het abonnement of de resource groep. Hier kunt u een bepaalde gebruiker, groep of Service Principal Name selecteren om meer informatie weer te geven.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Werken in de context van een gedelegeerd abonnement

U kunt rechtstreeks in de context van een gedelegeerd abonnement in de Azure Portal werken zonder de directory waarin u werkt te scha kelen. Dit doet u als volgt:

1. Selecteer het pictogram voor het adres van de **map en het abonnement** boven aan de Azure Portal.
2. Zorg ervoor dat in het **globale abonnements** filter alleen het vak voor dat gedelegeerd abonnement is geselecteerd. U kunt de vervolg keuzelijst **huidige en gedelegeerde directory's** gebruiken om alleen abonnementen in een specifieke map weer te geven. (Gebruik niet de optie **Directory overschakelen** , want Hiermee wijzigt u de map waarin u bent aangemeld.)

Als u vervolgens een service opent die ondersteuning biedt voor [Cross-Tenant beheer](../concepts/cross-tenant-management-experience.md), wordt de service standaard ingesteld op de context van het overgedragen abonnement dat u hebt geselecteerd. U kunt dit wijzigen door de bovenstaande stappen te volgen en de **selectie** vakjes in te scha kelen (of een of meer abonnementen kiezen om in plaats daarvan te werken).

> [!NOTE]
> Als u toegang hebt gekregen tot een of meer resource groepen in plaats van toegang tot een volledig abonnement, kunt u het abonnement selecteren waarvan de resource groep deel uitmaakt. U werkt vervolgens in de context van dat abonnement, maar heeft alleen toegang tot de aangewezen resource groepen.

U hebt ook toegang tot functionaliteit die betrekking heeft op gedelegeerde abonnementen of resource groepen vanuit Services die ondersteuning bieden voor cross-Tenant beheer door het abonnement of de resource groep te selecteren in de service.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ervaring op het beheer van cross-tenants](../concepts/cross-tenant-management-experience.md).
- Meer informatie over hoe uw klanten [service providers kunnen weer geven en beheren](view-manage-service-providers.md) door naar **service providers** te gaan in de Azure Portal.
