---
title: Serviceproviders weergeven en beheren
description: Klanten kunnen de pagina Serviceproviders in de Azure-portal gebruiken om informatie over serviceproviders, serviceprovideraanbiedingen en gedelegeerde resources weer te geven.
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94103c293ffa7ccfb9d7da0a237dc1b1c6540b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270626"
---
# <a name="view-and-manage-service-providers"></a>Serviceproviders weergeven en beheren

Klanten kunnen de pagina **Serviceproviders** in de [Azure-portal](https://portal.azure.com) gebruiken om informatie over serviceproviders en serviceprovideraanbiedingen weer te geven, specifieke bronnen te [delegeren](../concepts/azure-delegated-resource-management.md)via azure gedelegeerd resourcebeheer en te winkelen voor nieuwe serviceprovideraanbiedingen. Hoewel we hier verwijzen naar serviceproviders en klanten, kunnen bedrijven die meerdere tenants beheren hetzelfde proces gebruiken om hun beheerervaring te consolideren.

Als u toegang wilt krijgen tot de pagina **Serviceproviders** in de Azure-portal, kan de klant **Alle services**selecteren, vervolgens naar **serviceproviders** zoeken en deze selecteren. Ze kunnen het ook vinden door 'Serviceproviders' in te voeren in het zoekvak boven aan de Azure-portal.

Houd er rekening mee dat op de pagina **Serviceproviders** alleen informatie wordt weergegeven over de serviceproviders die toegang hebben tot de abonnementen of resourcegroepen van de klant via Azure-gedelegeerd bronbeheer. Als een klant werkt met extra serviceproviders die geen gebruik maken van Azure delegated resource management om toegang te krijgen tot de bronnen van de klant, wordt hier geen informatie over die serviceproviders weergegeven.

> [!NOTE]
> Serviceproviders kunnen informatie over hun klanten bekijken door naar **Mijn klanten** te navigeren in de Azure-portal. Zie [Klanten en gedelegeerde bronnen weergeven en beheren voor](view-manage-customers.md)meer informatie.

## <a name="view-service-provider-details"></a>Gegevens van serviceproviderweeren

Als u informatie over serviceproviders wilt bekijken, kan de klant **aanbiedingen provider** selecteren aan de linkerkant van de pagina **Serviceproviders.**

Voor elke serviceprovideraanbieding ziet de klant de naam van de serviceprovider en de bijbehorende aanbieding, samen met de naam die de klant tijdens het onboardingproces heeft ingevoerd.

In de kolom **Delegaties** ziet de klant hoeveel abonnementen en/of resourcegroepen zijn gedelegeerd aan de serviceprovider voor die aanbieding. De serviceprovider kan toegang krijgen tot deze abonnementen en/of resourcegroepen en deze beheren op basis van de toegangsniveaus die in de aanbieding zijn opgegeven.

## <a name="delegate-resources"></a>Resources delegeren

Voordat een serviceprovider toegang heeft tot de resources van een klant en deze beheert, moeten deze worden gedelegeerd. Als een klant een aanbieding heeft geaccepteerd, maar nog geen bronnen heeft gedelegeerd, ziet hij een notitie boven aan de sectie Aanbiedingen van de **provider.** Dit laat de klant weten dat ze actie moeten ondernemen voordat de serviceprovider toegang heeft tot de bronnen van de klant.

Ga als u abonnementen of resourcegroepen delegeren:

1. Schakel het selectievakje in voor de rij met de serviceprovider, aanbieding en naam. Selecteer vervolgens **Resources delegeren** boven aan het scherm.
1. Bekijk in het gedeelte **Details van de aanbieding** van de pagina Bronnen **de** details over de serviceprovider en de aanbieding. Als u roltoewijzingen voor de aanbieding wilt bekijken, selecteert **u Klik hier om de details van de geselecteerde aanbieding te bekijken.**
1. Selecteer **in** de sectie Gemachtigde **abonnementen of** **Resourcegroepen delegeren**.
1. Kies de abonnementen en/of resourcegroepen die u wilt delegeren voor deze aanbieding en selecteer **Vervolgens Toevoegen**.
1. Schakel het selectievakje onder aan de pagina in om te bevestigen dat u deze serviceprovider toegang wilt verlenen tot de bronnen die u hebt geselecteerd en selecteer **Vervolgens Gemachtigde**.

## <a name="add-or-remove-service-provider-offers"></a>Aanbiedingen van serviceproviders toevoegen of verwijderen

Een klant kan een nieuwe serviceprovideraanbieding toevoegen op de pagina **Aanbiedingen van** de provider door Aanbieding **toevoegen te**selecteren. De dienstverlener moet een aanbieding voor deze klant hebben gepubliceerd. De klant kan die aanbieding vervolgens selecteren in het scherm **Privé-aanbiedingen** en vervolgens **Maken**selecteren.

Als de klant een serviceprovideraanbieding wil verwijderen, kan hij het pictogram prullenbak in de rij voor die aanbieding selecteren. Na het bevestigen van de verwijdering heeft die serviceprovider geen toegang meer tot de klantbronnen die voorheen voor die aanbieding zijn gedelegeerd.

## <a name="update-service-provider-offers"></a>Serviceprovider bijwerken biedt

Nadat een klant een aanbieding heeft toegevoegd, kan een serviceprovider een bijgewerkte versie van dezelfde aanbieding publiceren naar Azure Marketplace. Ze willen bijvoorbeeld een nieuwe roldefinitie toevoegen. Als er een nieuwe versie van de aanbieding is gepubliceerd, wordt op de pagina **'update'** een pictogram voor bijwerken weergegeven in de rij voor die aanbieding. De klant kan dit pictogram selecteren om de verschillen te zien tussen de huidige versie van de aanbieding en de nieuwe.

 ![Pictogram Aanbieding bijwerken](../media/update-offer.jpg)

Na het bekijken van de wijzigingen kan de klant ervoor kiezen om te updaten naar de nieuwe versie. Zodra deze zijn uitgevoerd, zijn de autorisaties en andere instellingen die in de nieuwe versie zijn opgegeven, van toepassing op abonnementen en/of resourcegroepen die voor die aanbieding zijn gedelegeerd.

## <a name="view-delegations"></a>Delegaties bekijken

Delegaties vertegenwoordigen de roltoewijzingen die machtigingen verlenen aan de serviceprovider voor de resources die een klant heeft gedelegeerd. Als u deze informatie wilt weergeven, selecteert u **Delegaties** aan de linkerkant van de pagina **Serviceproviders.**

Met filters boven aan de pagina u uw delegatiegegevens sorteren en groeperen. U ook filteren op specifieke klanten, aanbiedingen of zoekwoorden.

> [!NOTE]
> Klanten zien deze roltoewijzingen of gebruikers van de serviceprovidertenant die deze rollen hebben gekregen, niet wanneer ze [de toewijzingsgegevens van rollen bekijken voor het gedelegeerde bereik in de Azure-portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) of via API's.

## <a name="audit-delegations-in-your-environment"></a>Auditdelegaties in uw omgeving

Klanten willen mogelijk inzicht krijgen in de abonnementen en/of resourcegroepen die zijn gedelegeerd aan serviceproviders voor [azure-gedelegeerd resourcebeheer.](../concepts/azure-delegated-resource-management.md) Dit is vooral handig voor klanten met een groot aantal abonnementen, of die veel gebruikers hebben die beheertaken uitvoeren.

We bieden een [ingebouwde beleidsdefinitie voor Azure Policy](../../governance/policy/samples/built-in-policies.md#lighthouse) om delegatie van scopes te controleren naar een beheertenant. U dit beleid toewijzen aan een beheergroep die alle abonnementen bevat die u wilt controleren. Wanneer u controleert of dit beleid wordt nageleefd, worden gedelegeerde abonnementen en/of resourcegroepen (binnen de beheergroep waaraan het beleid is toegewezen) in een niet-conforme status weergegeven. U vervolgens de resultaten bekijken en bevestigen dat er geen onverwachte delegaties zijn.

Zie [Snelstart: Een beleidstoewijzing maken voor](../../governance/policy/assign-policy-portal.md)meer informatie over het toewijzen van een beleid en het weergeven van nalevingsstatusresultaten.

## <a name="next-steps"></a>Volgende stappen
 
- Meer informatie over [Azure Lighthouse](../overview.md).
- Ontdek hoe serviceproviders klanten kunnen [bekijken en beheren](view-manage-customers.md) door naar Mijn **klanten** te gaan in de Azure-portal.