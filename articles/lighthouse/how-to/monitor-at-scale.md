---
title: Gedelegeerde resources op schaal controleren
description: Meer informatie over het effectief gebruiken van Azure Monitor-logboeken op schaal bare wijze over de tenants van de klant die u beheert.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 9a600cec524c9f715ada9da09dea35f3baa98151
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985198"
---
# <a name="monitor-delegated-resources-at-scale"></a>Gedelegeerde resources op schaal controleren

Als service provider hebt u mogelijk meerdere tenants voor klanten voor het beheer van gedelegeerde resources voor Azure voor bereid. Met [Azure Lighthouse](../overview.md) kunnen service providers bewerkingen op verschillende tijdstippen in meerdere tenants tegelijk uitvoeren, waardoor beheer taken efficiënter zijn.

In dit onderwerp wordt beschreven hoe u [Azure monitor-logboeken](../../azure-monitor/platform/data-platform-logs.md) op schaal bare wijze kunt gebruiken voor de tenants van de klant die u beheert.

## <a name="create-log-analytics-workspaces"></a>Log Analytics-werk ruimten maken

Als u gegevens wilt verzamelen, moet u Log Analytics-werk ruimten maken. Deze Log Analytics-werk ruimten zijn unieke omgevingen voor gegevens die worden verzameld door Azure Monitor. Elke werk ruimte heeft een eigen gegevens opslagplaats en-configuratie, en gegevens bronnen en-oplossingen zijn geconfigureerd om hun gegevens op te slaan in een bepaalde werk ruimte.

We raden u aan deze werk ruimten rechtstreeks te maken in de tenants van de klant. Op deze manier blijven hun gegevens in hun tenants, in plaats van dat ze naar de andere worden geëxporteerd. Dit biedt ook gecentraliseerde bewaking van alle resources of services die door Log Analytics worden ondersteund, waardoor u meer flexibiliteit hebt in welke typen gegevens u kunt bewaken.

U kunt een Log Analytics-werk ruimte maken met behulp van de [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md), met behulp van [Azure cli](../../azure-monitor/learn/quick-create-workspace-cli.md)of met behulp van [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Beleid implementeren waarmee gegevens worden geregistreerd

Nadat u uw Log Analytics-werk ruimten hebt gemaakt, kunt u [Azure Policy](../../governance/policy/index.yml) implementeren in uw klant hiërarchieën zodat diagnostische gegevens worden verzonden naar de juiste werk ruimte in elke Tenant. De exacte beleids regels die u implementeert, kunnen variëren, afhankelijk van de resource typen die u wilt bewaken.

Zie [zelf studie: beleid maken en beheren om naleving](../../governance/policy/tutorials/create-and-manage.md)af te dwingen voor meer informatie over het maken van beleid. Dit [hulp programma](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) van de Community biedt een script waarmee u beleids regels kunt maken voor het bewaken van de specifieke resource typen die u kiest.

Wanneer u hebt vastgesteld welk beleid u wilt implementeren, kunt u [ze op schaal implementeren op uw gedelegeerde abonnementen](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>De verzamelde gegevens analyseren

Nadat u uw beleid hebt geïmplementeerd, worden de gegevens vastgelegd in de Log Analytics werk ruimten die u hebt gemaakt in elke Tenant van de klant. Als u inzicht wilt krijgen in alle beheerde klanten, kunt u gebruikmaken van hulpprogram ma's zoals [Azure monitor werkmappen](../../azure-monitor/platform/workbooks-overview.md) voor het verzamelen en analyseren van gegevens uit meerdere gegevens bronnen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure monitor](../../azure-monitor/index.yml).
- Meer informatie over [Azure monitor-logboeken](../../azure-monitor/platform/data-platform-logs.md).
- Meer informatie over de [ervaring op het beheer van cross-tenants](../concepts/cross-tenant-management-experience.md).
