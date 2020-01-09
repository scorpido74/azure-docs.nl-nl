---
title: Aanbiedingen voor beheerde services in Azure Marketplace
description: Met beheerde services kunnen service providers resource beheer aanbiedingen verkopen aan klanten in azure Marketplace.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453590"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Aanbiedingen voor beheerde services in Azure Marketplace

In dit artikel wordt het nieuwe aanbod type voor **beheerde services** in [Azure Marketplace](https://azuremarketplace.microsoft.com)beschreven. Met Managed Services kunt u Resource Management Services aanbieden aan klanten met het beheer van de gedelegeerde resources van Azure. U kunt deze aanbiedingen beschikbaar stellen aan alle potentiële klanten of alleen aan een of meer specifieke klanten. Omdat u klanten rechtstreeks factureert voor kosten met betrekking tot deze beheerde services, zijn er geen kosten in rekening gebracht door micro soft.

## <a name="understand-managed-services-offers"></a>Meer informatie over Managed Services-aanbiedingen

Beheerde services bieden een stroom lijn voor het proces van onboarding van klanten voor het beheer van gedelegeerde resources van Azure. Wanneer een klant een aanbieding in azure Marketplace koopt, kunnen ze opgeven welke abonnementen en/of resource groepen onboarding moeten worden uitgevoerd. Houd er rekening mee dat het abonnement eerst moet worden geautoriseerd voor onboarding door de resource provider **micro soft. ManagedServices** hand matig te registreren.

Daarna kunnen gebruikers in uw organisatie beheer taken uitvoeren voor deze resources vanuit de Tenant van uw organisatie, op basis van de toegang die u hebt gedefinieerd bij het maken van de aanbieding in de [Cloud Partner-Portal](https://cloudpartner.azure.com/). Dit wordt gedaan via een manifest waarmee de Azure AD-gebruikers,-groepen en-service-principals worden opgegeven die toegang hebben tot klant bronnen met behulp van Azure delegated resource management, samen met rollen die hun toegangs niveau bepalen. Door machtigingen toe te wijzen aan een Azure AD-groep in plaats van een reeks afzonderlijke gebruikers-of toepassings accounts, kunt u afzonderlijke gebruikers toevoegen of verwijderen wanneer uw toegangs vereisten veranderen.

## <a name="public-and-private-offers"></a>Open bare en persoonlijke aanbiedingen

Elk managed services-aanbod omvat een of meer abonnementen. Plannen kunnen privé of openbaar zijn. 

Als u uw aanbieding wilt beperken tot specifieke klanten, kunt u een privé-abonnement publiceren. Wanneer u dit doet, kan het abonnement alleen worden aangeschaft voor de specifieke] abonnements-Id's die u opgeeft. Zie voor meer informatie [persoonlijke aanbiedingen](../../marketplace/private-offers.md).

Met open bare abonnementen kunt u uw services promo veren tot nieuwe klanten. Dit zijn doorgaans handiger wanneer u alleen beperkte toegang tot de Tenant van de klant nodig hebt. Zodra u een relatie met een klant tot stand hebt gebracht, kunt u dit doen door een nieuw privé-abonnement alleen voor die klant te publiceren of door [ze voor verdere toegang te gebruiken met behulp van Azure Resource Manager sjablonen](../how-to/onboard-customer.md).

Indien nodig kunt u zowel open bare als privé-abonnementen in dezelfde aanbieding toevoegen.

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, kunt u het niet wijzigen in persoonlijk. Gebruik een privé abonnement om te bepalen welke klanten uw aanbieding mogen accepteren en resources kunnen delegeren. Met een openbaar abonnement kunt u de beschik baarheid van bepaalde klanten of zelfs voor een bepaald aantal klanten niet beperken (hoewel u ervoor kiest om het abonnement niet volledig te verkopen). Er is momenteel geen mechanisme om delegaties af te wijzen of te verwijderen wanneer een klant een aanbieding accepteert, maar u kunt altijd contact opnemen met een klant en vragen om [uw toegang te verwijderen](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Aanbiedingen voor beheerde services publiceren

Zie [een Managed Services-aanbieding naar Azure Marketplace publiceren](../how-to/publish-managed-services-offers.md)voor meer informatie over het publiceren van een managed services-aanbod. Voor algemene informatie over publiceren naar Azure Marketplace met behulp van de Cloud Partner-portal raadpleegt u [Azure Marketplace en AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md) en [beheert u Azure en AppSource Marketplace-aanbiedingen](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure delegated resource management](azure-delegated-resource-management.md) en [Cross-Tenant beheer ervaring](cross-tenant-management-experience.md).
- [Publiceer Managed Services aanbiedingen](../how-to/publish-managed-services-offers.md) voor Azure Marketplace.
