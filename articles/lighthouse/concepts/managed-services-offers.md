---
title: Beheerde serviceaanbiedingen in Azure Marketplace
description: Met beheerde serviceaanbiedingen kunnen serviceproviders aanbiedingen voor resourcebeheer verkopen aan klanten in Azure Marketplace.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672430"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Beheerde serviceaanbiedingen in Azure Marketplace

In dit artikel wordt het type **Managed Service-aanbieding** beschreven in [Azure Marketplace.](https://azuremarketplace.microsoft.com) Met beheerde serviceaanbiedingen u resourcebeheerservices aan klanten aanbieden met [azure delegated resource management.](azure-delegated-resource-management.md) U deze aanbiedingen beschikbaar stellen aan alle potentiële klanten, of alleen voor een of meer specifieke klanten. Aangezien u klanten rechtstreeks in rekening brengt voor kosten in verband met deze beheerde services, worden er geen kosten in rekening gebracht door Microsoft.

## <a name="understand-managed-service-offers"></a>Beheerde serviceaanbiedingen begrijpen

Managed service biedt een stroomlijning van het proces van onboarding-klanten voor Azure-gedelegeerd resourcebeheer. Wanneer een klant een aanbieding in Azure Marketplace koopt, kan deze persoon opgeven welke abonnementen en/of resourcegroepen aan boord moeten worden genomen.

Daarna kunnen gebruikers in uw organisatie aan deze bronnen werken vanuit de tenant van uw organisatie, afhankelijk van de toegang die u hebt gedefinieerd bij het maken van de aanbieding in de [Cloud Partner Portal.](https://cloudpartner.azure.com/) Dit gebeurt via een manifest dat de Azure AD-gebruikers, -groepen en serviceprincipals opgeeft die toegang hebben tot klantbronnen met azure gedelegeerd bronbeheer, samen met rollen die hun toegangsniveau bepalen. Door machtigingen toe te wijs aan een Azure AD-groep toe te voegen in plaats van aan een reeks afzonderlijke gebruikers- of toepassingsaccounts, u afzonderlijke gebruikers toevoegen of verwijderen wanneer uw toegangsvereisten worden gewijzigd.

## <a name="public-and-private-offers"></a>Openbare en particuliere aanbiedingen

Elk aangeboden managed services bevat een of meer abonnementen. Plannen kunnen privé of openbaar zijn.

Als u uw aanbieding wilt beperken tot specifieke klanten, u een privéplan publiceren. Wanneer u dit doet, kan het abonnement alleen worden gekocht voor de specifieke] abonnements-id's die u verstrekt. Zie [Privéaanbiedingen](../../marketplace/private-offers.md)voor meer informatie.

Met openbare abonnementen u uw services promoten bij nieuwe klanten. Deze zijn meestal meer geschikt wanneer u slechts beperkte toegang tot de huurder van de klant nodig hebt. Zodra u een relatie met een klant hebt opgebouwd, als deze besluit uw organisatie extra toegang te verlenen, u dit doen door alleen een nieuw privéplan voor die klant te publiceren of door [ze in te stappen voor verdere toegang met Azure Resource Manager-sjablonen.](../how-to/onboard-customer.md)

Indien nodig u zowel openbare als particuliere abonnementen in dezelfde aanbieding opnemen.

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, u het niet privé wijzigen. Als u wilt bepalen welke klanten uw aanbieding kunnen accepteren en resources kunnen delegeren, gebruikt u een privéabonnement. Met een openbaar plan u de beschikbaarheid niet beperken tot bepaalde klanten of zelfs tot een bepaald aantal klanten (hoewel u stoppen met de verkoop van het abonnement volledig als u ervoor kiest om dit te doen). U [de toegang tot een delegatie alleen verwijderen](../how-to/onboard-customer.md#remove-access-to-a-delegation) nadat een klant een aanbieding heeft geaccepteerd als u een **autorisatie** hebt opgenomen met de **roldefinitiedie** is ingesteld op [de rol Van registratietoewijzing voor managed services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) wanneer u de aanbieding hebt gepubliceerd. U ook contact opnemen met de klant en hen vragen om uw toegang te [verwijderen.](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

## <a name="publish-managed-service-offers"></a>Managed serviceaanbiedingen publiceren

Zie [Een managed services-aanbieding publiceren naar Azure Marketplace](../how-to/publish-managed-services-offers.md)voor meer informatie over het publiceren van een managed services-aanbieding. Zie [Azure Marketplace en AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md)voor algemene informatie over publiceren op Azure Marketplace via de Cloud Partner Portal.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [azure gedelegeerd resourcebeheer](azure-delegated-resource-management.md) en [cross-tenant beheerervaringen](cross-tenant-management-experience.md).
- [Beheerservices publiceren op](../how-to/publish-managed-services-offers.md) Azure Marketplace.
