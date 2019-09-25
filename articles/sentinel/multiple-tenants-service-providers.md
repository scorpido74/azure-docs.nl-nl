---
title: Werken met meerdere tenants naar Azure Sentinel voor MSSP-service providers | Microsoft Docs
description: Werken met meerdere tenants voor Azure Sentinel voor MSSP-service providers.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240494"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Werken met meerdere tenants in azure Sentinel 

Als u een MSSP (Managed Security service provider) bent en u [Azure Lighthouse](../lighthouse/overview.md) gebruikt voor het beheren van de Security Operations Centers (SOC) van uw klanten, kunt u Azure-Sentinel-bronnen van uw klanten beheren zonder rechtstreeks verbinding te maken met de de Tenant van de klant, vanuit uw eigen Azure-Tenant. 

## <a name="prerequisites"></a>Vereisten
- [Onboarding van Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- Om dit goed te laten werken, moet uw Tenant zijn geregistreerd bij de Azure Sentinel resource provider op ten minste één abonnement. Als u een geregistreerde Azure-Sentinel hebt in uw Tenant, bent u klaar om aan de slag te gaan. Als dat niet het geval is, selecteert u in de Azure Portal **abonnementen** , gevolgd door **resource providers** , zoekt `Microsoft.Security.Insights` en selecteert u **registreren**.
   ![Resource providers controleren](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Toegang tot Azure Sentinel van andere tenants
1. Selecteer onder **adres lijst + abonnement**de gedelegeerde directory's en de abonnementen waar de Azure Sentinel-werk ruimten van uw klant zich bevinden.

   ![Beveiligings incidenten genereren](media/multiple-tenants-service-providers/directory-subscription.png)

1. Open Azure Sentinel. U ziet alle werk ruimten in de geselecteerde abonnementen, en u kunt deze probleemloos samen werken, zoals elke werk ruimte in uw eigen Tenant.

> [!NOTE]
> U kunt geen connectors implementeren in azure Sentinel vanuit een beheerde werk ruimte. Als u een connector wilt implementeren, moet u zich rechtstreeks aanmelden bij de Tenant waarop u een connector wilt implementeren en deze verifiëren met de vereiste machtigingen.





## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u meerdere Azure Sentinel-tenants naadloos kunt beheren. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

