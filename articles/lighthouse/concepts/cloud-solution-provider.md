---
title: Overwegingen voor een Cloud Solution Provider-programma
description: Voor CSP-partners helpt Azure-gedelegeerd resourcebeheer de beveiliging en controle te verbeteren door gedetailleerde machtigingen in te schakelen.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456908"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse en het Cloud Solution Provider-programma

Als u een [CSP-partner (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) bent, hebt u al toegang tot de Azure-abonnementen die voor uw klanten zijn gemaakt via het CSP-programma met behulp van de [AOBO-functionaliteit beheren namens (AOBO).](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) Met deze toegang u de abonnementen van uw klanten rechtstreeks ondersteunen, configureren en beheren.

Met [Azure Lighthouse](../overview.md)u azure delegated resource management gebruiken, samen met AOBO. Dit helpt de beveiliging te verbeteren en vermindert onnodige toegang door meer gedetailleerde machtigingen voor uw gebruikers in te schakelen. Het zorgt ook voor meer efficiëntie en schaalbaarheid, omdat uw gebruikers kunnen werken via meerdere klantabonnementen met behulp van één aanmelding in uw tenant.

> [!TIP]
> Om de resources van klanten te beschermen, moet u onze [aanbevolen beveiligingspraktijken](recommended-security-practices.md) controleren en volgen, samen met de [vereisten voor de beveiligingsvereisten van](https://docs.microsoft.com/partner-center/partner-security-requirements)de partner.

## <a name="administer-on-behalf-of-aobo"></a>Beheren namens (AOBO)

Met AOBO heeft elke gebruiker met de rol [Admin Agent](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) in uw tenant AOBO-toegang tot Azure-abonnementen die u via het CSP-programma maakt. Gebruikers die toegang nodig hebben tot abonnementen van klanten, moeten lid zijn van deze groep. AOBO staat de flexibiliteit niet toe om verschillende groepen te maken die met verschillende klanten werken of om verschillende rollen voor groepen of gebruikers in te schakelen.

![Tenantmanagement met AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Met Azure delegated resource management u verschillende groepen toewijzen aan verschillende klanten of rollen, zoals weergegeven in het volgende diagram. Omdat gebruikers het juiste toegangsniveau hebben via azure gedelegeerd resourcebeheer, u het aantal gebruikers met de functie Admin Agent verminderen (en dus volledige AOBO-toegang hebben). Dit helpt de beveiliging te verbeteren door onnodige toegang tot de resources van uw klanten te beperken. Het geeft u ook meer flexibiliteit om meerdere klanten op schaal te beheren.

Het inwerken van een abonnement dat u hebt gemaakt via het CSP-programma volgt de stappen die zijn beschreven in [Onboard een abonnement op Azure gedelegeerd resourcebeheer.](../how-to/onboard-customer.md) Elke gebruiker die de rol Van de Beheerder agent in uw tenant heeft, kan deze onboarding uitvoeren.

![Tenantbeheer met AOBO- en Azure-gedelegeerdbronbeheer](../media/csp-2.jpg)

> [!NOTE]
> De pagina [ **Mijn klanten** in de Azure-portal](../how-to/view-manage-customers.md) bevat nu een sectie **Cloud Solution Provider (Preview),** waarin factureringsgegevens en resources worden weergegeven voor CSP-klanten die [de Microsoft Customer Agreement (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) hebben ondertekend en [onder het Azure-abonnement vallen.](https://docs.microsoft.com/partner-center/azure-plan-get-started) Zie [Aan de slag met uw factureringsaccount voor Microsoft Partner Overeenkomst](../../billing/mpa-overview.md)voor meer informatie .
>
> CSP-klanten kunnen in deze sectie worden weergegeven of ze ook zijn ingeschakeld voor azure-gedelegeerd bronbeheer. Als dit het wel is, worden ze ook weergegeven in de sectie **Klanten,** zoals beschreven in [Weergave en beheer klanten en gedelegeerde resources.](../how-to/view-manage-customers.md) Op dezelfde manier hoeft een CSP-klant niet te worden weergegeven in het gedeelte **Cloud Solution Provider (Preview)** van **Mijn klanten,** zodat u ze aan boord nemen voor azure-gedelegeerd bronbeheer.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [cross-tenant management ervaringen](cross-tenant-management-experience.md).
- Meer informatie over het [aan boord maken van een abonnement op azure gedelegeerd resourcebeheer](../how-to/onboard-customer.md).
- Meer informatie over het [Cloud Solution Provider-programma](https://docs.microsoft.com/partner-center/csp-overview).
