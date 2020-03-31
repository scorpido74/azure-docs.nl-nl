---
title: Werken met meerdere tenants naar Azure Sentinel voor MSSP-serviceproviders| Microsoft Documenten
description: Hoe u met meerdere tenants werken met Azure Sentinel voor MSSP-serviceproviders.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476012"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Werken met meerdere tenants in Azure Sentinel 

Als u een managed security service provider (MSSP) bent en u Azure [Lighthouse](../lighthouse/overview.md) gebruikt om de beveiligingscentra (SOC) van uw klanten te beheren, u de Azure Sentinel-bronnen van uw klanten beheren zonder rechtstreeks verbinding te maken met de tenant van de klant, vanuit uw eigen Azure-tenant. 

## <a name="prerequisites"></a>Vereisten
- [Azure Lighthouse aan boord](../lighthouse/how-to/onboard-customer.md)
- Om dit goed te laten werken, moet uw tenant zijn geregistreerd bij de Azure Sentinel Resource Provider op ten minste één abonnement. Als u een geregistreerde Azure Sentinel in uw tenant hebt, bent u klaar om aan de slag te gaan. Als dit niet het zo is, selecteert u **Abonnementen** in de Azure-portal, gevolgd door **Resourceproviders.**  Zoek en selecteer vervolgens in het scherm `Microsoft.OperationalInsights` SOC `Microsoft.SecurityInsights` **- Resourceproviders** naar en selecteer **registreren**.
   ![Resourceproviders controleren](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Azure Sentinel openen vanuit andere tenants
1. Selecteer **onder Directory + abonnement**de gedelegeerde mappen en de abonnementen waar de Azure Sentinel-werkruimten van uw klant zich bevinden.

   ![Beveiligingsincidenten genereren](media/multiple-tenants-service-providers/directory-subscription.png)

1. Open Azure Sentinel. U ziet alle werkruimten in de geselecteerde abonnementen en u er naadloos mee werken, zoals elke werkruimte in uw eigen tenant.

> [!NOTE]
> U connectors in Azure Sentinel niet implementeren vanuit een beheerde werkruimte. Als u een connector wilt implementeren, moet u zich rechtstreeks aanmelden bij de tenant waarop u een connector wilt implementeren en daar verifiëren met de vereiste machtigingen.





## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u meerdere Azure Sentinel-tenants naadloos beheren. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)

