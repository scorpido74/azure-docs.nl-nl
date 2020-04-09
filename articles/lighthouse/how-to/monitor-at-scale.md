---
title: Gedelegeerde resources op schaal controleren
description: Meer informatie over het effectief gebruiken van Azure Monitor Logs op een schaalbare manier voor de klanttenants die u beheert.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 9a600cec524c9f715ada9da09dea35f3baa98151
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985198"
---
# <a name="monitor-delegated-resources-at-scale"></a>Gedelegeerde resources op schaal controleren

Als serviceprovider hebt u mogelijk meerdere klanttenants aan boord genomen voor Azure-gedelegeerd bronbeheer. [Azure Lighthouse](../overview.md) stelt serviceproviders in staat om bewerkingen op schaal uit te voeren voor meerdere tenants tegelijk, waardoor beheertaken efficiënter worden.

In dit onderwerp ziet u hoe u [Azure Monitor Logs](../../azure-monitor/platform/data-platform-logs.md) op een schaalbare manier gebruiken voor de klanttenants die u beheert.

## <a name="create-log-analytics-workspaces"></a>Log Analytics-werkruimten maken

Als u gegevens wilt verzamelen, moet u Logboekanalyse-werkruimten maken. Deze Log Analytics-werkruimten zijn unieke omgevingen voor gegevens die worden verzameld door Azure Monitor. Elke werkruimte heeft zijn eigen gegevensopslagplaats en -configuratie en gegevensbronnen en -oplossingen zijn geconfigureerd om hun gegevens in een bepaalde werkruimte op te slaan.

We raden u aan deze werkruimten rechtstreeks in de huurders van de klant te maken. Op deze manier hun gegevens blijft in hun huurders in plaats van te worden geëxporteerd naar de jouwe. Dit maakt het ook mogelijk om gecentraliseerde bewaking van alle resources of services die worden ondersteund door Log Analytics mogelijk te maken, zodat u meer flexibiliteit hebt over welke soorten gegevens u controleert.

U een Log Analytics-werkruimte maken met behulp van de [Azure-portal,](../../azure-monitor/learn/quick-create-workspace.md)met [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)of met [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Beleidsregels implementeren die gegevens registreren

Nadat u uw Log Analytics-werkruimten hebt gemaakt, u [Azure Policy](../../governance/policy/index.yml) implementeren in uw klanthiërarchieën, zodat diagnostische gegevens worden verzonden naar de juiste werkruimte in elke tenant. Het exacte beleid dat u implementeert, kan variëren afhankelijk van de resourcetypen die u wilt controleren.

Zie [Zelfstudie: Beleid maken en beheren om naleving af te dwingen](../../governance/policy/tutorials/create-and-manage.md)voor meer informatie over het maken van beleid. Deze [communitytool](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) biedt een script waarmee u beleid maken om de specifieke resourcetypen die u kiest, te controleren.

Wanneer u hebt bepaald welk beleid u wilt implementeren, u [deze op schaal implementeren op uw gedelegeerde abonnementen.](policy-at-scale.md)

## <a name="analyze-the-gathered-data"></a>De verzamelde gegevens analyseren

Nadat u uw beleid hebt geïmplementeerd, worden gegevens geregistreerd in de Log Analytics-werkruimten die u in elke klanttenant hebt gemaakt. Om inzicht te krijgen in alle beheerde klanten, u hulpprogramma's zoals [Azure Monitor Workbooks](../../azure-monitor/platform/workbooks-overview.md) gebruiken om informatie uit meerdere gegevensbronnen te verzamelen en te analyseren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Monitor](../../azure-monitor/index.yml).
- Meer informatie over [Azure Monitor-logboeken](../../azure-monitor/platform/data-platform-logs.md).
- Meer informatie over [cross-tenant management ervaringen](../concepts/cross-tenant-management-experience.md).
