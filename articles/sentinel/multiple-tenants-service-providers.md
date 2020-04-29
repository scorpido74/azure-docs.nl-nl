---
title: Werken met meerdere tenants naar Azure Sentinel voor MSSP-service providers | Microsoft Docs
description: Werken met meerdere tenants voor Azure Sentinel voor MSSP-service providers.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476012"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Werken met meerdere tenants in azure Sentinel 

Als u een beheerde Security service provider (MSSP) bent en u [Azure Lighthouse](../lighthouse/overview.md) gebruikt voor het beheren van de beveiliging van uw klanten, kunt u de Azure-Sentinel-resources van uw klanten beheren zonder rechtstreeks verbinding te maken met de Tenant van de klant, vanuit uw eigen Azure-Tenant. 

## <a name="prerequisites"></a>Vereisten
- [Onboarding van Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- Om dit goed te laten werken, moet uw Tenant zijn geregistreerd bij de Azure Sentinel resource provider op ten minste één abonnement. Als u een geregistreerde Azure-Sentinel hebt in uw Tenant, bent u klaar om aan de slag te gaan. Als dat niet het geval is, selecteert u **abonnementen** uit het Azure Portal, gevolgd door **resource providers**.  Zoek en selecteer `Microsoft.OperationalInsights` `Microsoft.SecurityInsights`vervolgens, in het scherm **Soc-resource providers** , en selecteer **registreren**.
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

