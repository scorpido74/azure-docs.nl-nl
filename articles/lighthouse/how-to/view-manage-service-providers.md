---
title: Serviceproviders weergeven en beheren
description: Klanten kunnen de pagina service providers in het Azure Portal gebruiken om informatie over service providers, aanbiedingen van providers en gedelegeerde resources weer te geven.
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94103c293ffa7ccfb9d7da0a237dc1b1c6540b72
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649735"
---
# <a name="view-and-manage-service-providers"></a>Serviceproviders weergeven en beheren

Klanten kunnen de pagina **service providers** in het [Azure Portal](https://portal.azure.com) gebruiken om informatie over service providers en service providers te bekijken, specifieke resources te delegeren via [Azure delegated resource management](../concepts/azure-delegated-resource-management.md)en naar nieuwe aanbiedingen voor service providers te kijken. Hoewel we hier naar service providers en klanten verwijzen, kunnen bedrijven die meerdere tenants beheren, hetzelfde proces gebruiken om hun beheer ervaring te consolideren.

Om toegang te krijgen tot de pagina **service providers** in de Azure Portal, kan de klant **alle services**selecteren en vervolgens zoeken naar **service providers** en selecteren. Deze kunnen ook worden gevonden door ' service providers ' in te voeren in het zoekvak boven aan de Azure Portal.

Houd er wel op dat op de pagina **service providers** alleen informatie wordt weer gegeven over de service providers die toegang hebben tot de abonnementen of resource groepen van de klant via het beheer van gedelegeerde resources van Azure. Als een klant werkt met aanvullende service providers die geen Azure delegated Resource Management gebruiken om toegang te krijgen tot de resources van de klant, wordt hier geen informatie over die service providers weer gegeven.

> [!NOTE]
> Service providers kunnen informatie over hun klanten weer geven door te navigeren naar **mijn klanten** in de Azure Portal. Zie [klanten en gedelegeerde resources weer geven en beheren](view-manage-customers.md)voor meer informatie.

## <a name="view-service-provider-details"></a>Details van service provider weer geven

Om informatie over service providers weer te geven, kan de klant **provider aanbiedingen** selecteren aan de linkerkant van de pagina **service providers** .

Voor elke service provider-aanbieding ziet de klant de naam van de service provider en de bijbehorende aanbieding, samen met de naam die de klant tijdens het voorbereidings proces heeft ingevoerd.

In de kolom **delegaties** ziet de klant hoeveel abonnementen en/of resource groepen zijn overgedragen aan de service provider voor die aanbieding. De service provider kan deze abonnementen en/of resource groepen openen en beheren op basis van de toegangs niveaus die zijn opgegeven in de aanbieding.

## <a name="delegate-resources"></a>Resources delegeren

Voordat een service provider de resources van een klant kan openen en beheren, moeten ze worden gedelegeerd. Als een klant een aanbieding heeft geaccepteerd, maar nog geen resources heeft overgedragen, ziet u boven aan de sectie **aanbiedingen van providers** een opmerking. Zo kan de klant weten dat hij actie moet ondernemen voordat de service provider toegang kan krijgen tot de resources van de klant.

Abonnementen of resource groepen delegeren:

1. Schakel het selectie vakje in voor de rij met de service provider, de aanbieding en de naam. Selecteer vervolgens **resources delegeren** boven aan het scherm.
1. Bekijk de details over de service provider en aanbieding in het gedeelte Details van de **aanbieding** van de pagina **gemachtigde resources** . Als u roltoewijzingen voor de aanbieding wilt controleren, selecteert u **hier Klik hier om de details van de geselecteerde aanbieding te bekijken**.
1. Selecteer in de sectie **gemachtigde** de optie **abonnementen overdragen** of **resource groepen delegeren**.
1. Kies de abonnementen en/of resource groepen die u wilt delegeren voor deze aanbieding en selecteer vervolgens **toevoegen**.
1. Schakel het selectie vakje onder aan de pagina in om te bevestigen dat u deze service provider toegang wilt geven tot de resources die u hebt geselecteerd, en selecteer vervolgens **delegeren**.

## <a name="add-or-remove-service-provider-offers"></a>Aanbiedingen voor service providers toevoegen of verwijderen

Een klant kan een nieuwe aanbieding voor een service provider toevoegen via de pagina aanbiedingen van de **provider** door **aanbieding toevoegen**te selecteren. De service provider moet een aanbieding hebben gepubliceerd voor deze klant. De klant kan vervolgens die aanbieding selecteren in het scherm **persoonlijke aanbiedingen** en vervolgens **maken**selecteren.

Als de klant een service provider-aanbieding wil verwijderen, kunnen ze het prullenbak pictogram in de rij voor die aanbieding selecteren. Nadat de verwijdering is bevestigd, heeft die service provider geen toegang meer tot de resources van de klant die voorheen zijn gedelegeerd voor die aanbieding.

## <a name="update-service-provider-offers"></a>Aanbiedingen van service providers bijwerken

Nadat een klant een aanbieding heeft toegevoegd, kan een service provider een bijgewerkte versie van dezelfde aanbieding publiceren op Azure Marketplace. Het is bijvoorbeeld mogelijk dat een nieuwe roldefinitie moet worden toegevoegd. Als er een nieuwe versie van de aanbieding is gepubliceerd, wordt op de pagina met **aanbiedingen** een pictogram voor de update weer gegeven in de rij voor die aanbieding. De klant kan dit pictogram selecteren voor een overzicht van de verschillen tussen de huidige versie van de aanbieding en de nieuwe.

 ![Pictogram aanbieding bijwerken](../media/update-offer.jpg)

Nadat u de wijzigingen hebt bekeken, kan de klant ervoor kiezen om bij te werken naar de nieuwe versie. Als dit het geval is, worden de autorisaties en andere instellingen die zijn opgegeven in de nieuwe versie toegepast op alle abonnementen en/of resource groepen die zijn gedelegeerd voor die aanbieding.

## <a name="view-delegations"></a>Delegaties weer geven

Delegaties vertegenwoordigen de roltoewijzingen die machtigingen verlenen aan de service provider voor de resources die een klant heeft gedelegeerd. Als u deze informatie wilt weer geven, selecteert u **delegaties** aan de linkerkant van de pagina **service providers** .

Met filters boven aan de pagina kunt u uw delegatie gegevens sorteren en groeperen. U kunt ook filteren op specifieke klanten, aanbiedingen of tref woorden.

> [!NOTE]
> Klanten krijgen deze roltoewijzingen niet te zien of gebruikers van de Tenant van de service provider die deze rollen hebben gekregen bij het [weer geven van roltoewijzings gegevens voor het overgedragen bereik in de Azure Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) of via api's.

## <a name="audit-delegations-in-your-environment"></a>Audit overdrachten in uw omgeving

Klanten willen mogelijk inzicht krijgen in de abonnementen en/of resource groepen die zijn gedelegeerd aan service providers voor het beheer van [gedelegeerde resources van Azure](../concepts/azure-delegated-resource-management.md). Dit is met name handig voor klanten met een groot aantal abonnementen of met veel gebruikers die beheer taken uitvoeren.

We bieden een [Azure Policy ingebouwde beleids definitie](../../governance/policy/samples/built-in-policies.md#lighthouse) voor het controleren van de overdracht van scopes naar een beheer Tenant. U kunt dit beleid toewijzen aan een beheer groep die alle abonnementen bevat die u wilt controleren. Wanneer u controleert op naleving van dit beleid, worden alle gedelegeerde abonnementen en/of resource groepen (binnen de beheer groep waaraan het beleid is toegewezen) weer gegeven in een niet-compatibele status. U kunt vervolgens de resultaten bekijken en controleren of er geen onverwachte delegaties zijn.

Zie [Quick Start: een beleids toewijzing maken](../../governance/policy/assign-policy-portal.md)voor meer informatie over het toewijzen van een beleid en het weer geven van de resultaten van de nalevings status.

## <a name="next-steps"></a>Volgende stappen
 
- Meer informatie over [Azure Lighthouse](../overview.md).
- Meer informatie over hoe service providers [klanten kunnen bekijken en beheren](view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.