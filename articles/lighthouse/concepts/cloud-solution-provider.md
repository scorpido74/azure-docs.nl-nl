---
title: Overwegingen voor een Cloud Solution Provider-programma
description: Voor CSP-partners zorgt Azure gedelegeerd resource beheer voor betere beveiliging en controle door gedetailleerde machtigingen in te scha kelen.
ms.date: 10/23/2019
ms.topic: overview
ms.openlocfilehash: 3ea32418cdf6808ad311d343d28ba2778740dbe4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132521"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse en het programma Cloud Solution Provider

Als u een CSP-partner [(Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) bent, hebt u al toegang tot de Azure-abonnementen die zijn gemaakt voor uw klanten via het CSP-programma met behulp van de functie [beheren namens (administrate)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Met deze toegang kunt u de abonnementen van uw klanten rechtstreeks ondersteunen, configureren en beheren.

Met [Azure Lighthouse](../overview.md)kunt u Azure delegated Resource Management gebruiken samen met administrate. Dit helpt de beveiliging te verbeteren en de toegang te verminderen door meer gedetailleerde machtigingen in te scha kelen voor uw gebruikers. Het biedt ook een grotere efficiëntie en schaal baarheid, omdat uw gebruikers met één aanmelding in uw Tenant kunnen werken op meerdere abonnementen van klanten.

> [!TIP]
> Bekijk en volg de [aanbevolen beveiligings procedures](recommended-security-practices.md) in combi natie met de [beveiligings vereisten](https://docs.microsoft.com/partner-center/partner-security-requirements)van de partner om de resources van klanten te helpen beschermen.

## <a name="administer-on-behalf-of-aobo"></a>Beheren namens (ADMINISTRATE)

Met ADMINISTRATE krijgt elke gebruiker met de rol [beheerder agent](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) in uw Tenant administrate toegang tot Azure-abonnementen die u via het CSP-programma maakt. Gebruikers die toegang nodig hebben tot abonnementen van klanten, moeten lid zijn van deze groep. ADMINISTRATE staat niet toe dat de flexibiliteit om afzonderlijke groepen te maken die met verschillende klanten werken, of om verschillende rollen voor groepen of gebruikers in te scha kelen.

![Tenant beheer met ADMINISTRATE](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Met behulp van Azure delegated Resource Management kunt u verschillende groepen toewijzen aan verschillende klanten of rollen, zoals in het volgende diagram wordt weer gegeven. Omdat gebruikers over het juiste toegangs niveau beschikken via het beheer van gedelegeerde resources van Azure, kunt u het aantal gebruikers met de rol beheerder (en dus volledige ADMINISTRATE) verminderen. Dit helpt de beveiliging te verbeteren door het beperken van onnodige toegang tot de resources van uw klanten. Daarnaast hebt u meer flexibiliteit om meerdere klanten op schaal te beheren.

Als u een abonnement hebt gemaakt via het CSP-programma, volgt u de stappen die worden beschreven in [onboarding van een abonnement op Azure gedelegeerd resource beheer](../how-to/onboard-customer.md). Gebruikers met de rol beheerder agent in uw Tenant kunnen dit onboarding uitvoeren.

![Tenant beheer met ADMINISTRATE en Azure delegated resource management](../media/csp-2.jpg)

> [!NOTE]
> De [pagina **mijn klanten** in de Azure Portal](../how-to/view-manage-customers.md) bevat nu een **Cloud Solution Provider (preview)** -sectie waarin facturerings gegevens en resources voor CSP-klanten worden weer gegeven die [de micro soft-klant overeenkomst (MCA) hebben ondertekend](https://docs.microsoft.com/partner-center/confirm-customer-agreement) en onder het Azure-abonnement vallen. Zie [aan de slag met uw micro soft-partner overeenkomst](https://docs.microsoft.com/azure/billing/mpa-overview)voor meer informatie.
>
> CSP-klanten kunnen in deze sectie worden weer gegeven, ongeacht of ze voor het beheer van de gedelegeerde resources van Azure worden uitgevoerd. Als dat het geval is, worden ze ook weer gegeven in de sectie **klanten** , zoals wordt beschreven in [klanten en gedelegeerde resources weer geven en beheren](../how-to/view-manage-customers.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ervaring op het beheer van cross-tenants](cross-tenant-management-experience.md).
- Meer informatie over het [voorbereiden van een abonnement op Azure delegated resource management](../how-to/onboard-customer.md).
- Meer informatie over het [programma Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
