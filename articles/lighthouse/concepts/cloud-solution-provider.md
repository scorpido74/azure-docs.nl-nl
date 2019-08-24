---
title: Azure Lighthouse en het programma Cloud Solution Provider
description: Wanneer u Azure delegated resource management gebruikt, is het belang rijk dat u rekening houdt met beveiliging en toegangs beheer.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 399d2cb829c0425e3c9ee70a61cafde8568f903b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012124"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse en het programma Cloud Solution Provider

Als u een CSP-partner [(Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) bent, hebt u al toegang tot de Azure-abonnementen die zijn gemaakt voor uw klanten via het CSP-programma met behulp van de functie [beheren namens (administrate)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Met deze toegang kunt u de abonnementen van uw klanten rechtstreeks ondersteunen, configureren en beheren.

Met Azure Lighthouse kunt u Azure delegated Resource Management gebruiken samen met ADMINISTRATE. Dit helpt de beveiliging te verbeteren en de toegang te verminderen door meer gedetailleerde machtigingen in te scha kelen voor uw gebruikers. Het biedt ook een grotere efficiëntie en schaal baarheid, omdat uw gebruikers met één aanmelding in uw Tenant kunnen werken op meerdere abonnementen van klanten.

## <a name="administer-on-behalf-of-aobo"></a>Beheren namens (ADMINISTRATE)

Met ADMINISTRATE krijgt elke gebruiker met de rol [beheerder agent](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) in uw Tenant administrate toegang tot Azure-abonnementen die u via het CSP-programma maakt. Gebruikers die toegang nodig hebben tot abonnementen van klanten, moeten lid zijn van deze groep. ADMINISTRATE staat niet toe dat de flexibiliteit om afzonderlijke groepen te maken die met verschillende klanten werken, of om verschillende rollen voor groepen of gebruikers in te scha kelen.

![Tenant beheer met ADMINISTRATE](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Met behulp van Azure delegated Resource Management kunt u verschillende groepen toewijzen aan verschillende klanten of rollen, zoals in het volgende diagram wordt weer gegeven. Omdat gebruikers over het juiste toegangs niveau beschikken via het beheer van gedelegeerde resources van Azure, kunt u het aantal gebruikers met de rol beheerder (en dus volledige ADMINISTRATE) verminderen. Dit helpt de beveiliging te verbeteren door het beperken van onnodige toegang tot de resources van uw klanten. Daarnaast hebt u meer flexibiliteit om meerdere klanten op schaal te beheren.

Als u een abonnement hebt gemaakt via het CSP-programma, volgt u de stappen die worden beschreven in onboarding van [een abonnement op Azure gedelegeerd resource beheer](../how-to/onboard-customer.md). Gebruikers met de rol beheerder agent in uw Tenant kunnen dit onboarding uitvoeren.

![Tenant beheer met ADMINISTRATE en Azure delegated resource management](../media/csp-2.jpg)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ervaring op het beheer van cross-tenants](cross-tenant-management-experience.md).
- Meer informatie over het [voorbereiden van een abonnement op Azure delegated resource management](../how-to/onboard-customer.md).
- Meer informatie over het [programma Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
