---
title: Overzicht van inzichten in Azure Monitor | Microsoft Documenten
description: Inzichten bieden een aangepaste monitoringervaring in Azure Monitor voor bepaalde toepassingen en services. Dit artikel bevat een korte beschrijving van elk van de inzichten die momenteel beschikbaar zijn.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657245"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Overzicht van inzichten in Azure Monitor
Inzichten bieden een op maat gemaakte monitoringervaring voor bepaalde toepassingen en services. Ze slaan gegevens op in het [Azure Monitor-gegevensplatform](../platform/data-platform.md) en maken gebruik van andere Azure Monitor-functies voor analyse en waarschuwingen, maar kunnen extra gegevens verzamelen en een unieke gebruikerservaring bieden in de Azure-portal. Toegang tot inzichten in het gedeelte **Insights** van het azure-monitormenu in de Azure-portal.

In de volgende secties vindt u een korte beschrijving van de inzichten die momenteel beschikbaar zijn in Azure Monitor. Zie de gedetailleerde documentatie voor meer informatie over elk.

## <a name="application-insights"></a>Application Insights
Application Insights is een uitbreidbare APM-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Hiermee kunt u uw livewebtoepassing controleren. Het werkt voor toepassingen op een breed scala aan platforms, waaronder .NET, Node.js en Java EE, gehost on-premises, hybride of een openbare cloud. Het integreert ook met uw DevOps-proces en heeft verbindingspunten voor een verscheidenheid aan ontwikkeltools.

Zie [Wat is Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
Azure Monitor voor containers bewaakt de prestaties van containerworkloads die zijn geïmplementeerd voor Azure Container Instances of beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). Het bewaken van uw containers is van cruciaal belang, vooral wanneer u een productiecluster uitvoert, op schaal, met meerdere toepassingen.

Zie [overzicht van Azure Monitor voor containers](../insights/container-insights-overview.md).

![Azure Monitor voor containers](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure-monitor voor resourcegroepen (voorbeeld)
Azure Monitor voor resourcegroepen helpt bij het trigeren en diagnosticeren van eventuele problemen die uw afzonderlijke resources tegenkomen, terwijl het context biedt met betrekking tot de status en prestaties van de resourcegroep als geheel.

Zie [Brongroepen controleren met Azure Monitor (voorbeeld)](../insights/resource-group-insights.md).

![Azure-monitor voor resourcegroepen](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure-monitor voor VM's (voorbeeld)
Azure Monitor voor VM's bewaakt uw virtuele Azure-machines (VM) en de schaalsets voor virtuele machines op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen.

Zie [Wat is Azure Monitor voor VM's?](vminsights-overview.md)

![Azure Monitor voor virtuele machines](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor voor netwerken (voorbeeld)
[Azure Monitor for Networks](network-insights-overview.md) biedt een uitgebreid overzicht van status en statistieken voor al uw netwerkbronnen. De geavanceerde zoekmogelijkheid helpt u bij het identificeren van resourceafhankelijkheden, waardoor scenario's zoals het identificeren van bronnen die uw website hosten, worden geïdentificeerd door simpelweg naar de naam van uw website te zoeken.

![Azure Monitor voor netwerken](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [Azure Monitor-gegevensplatform](../platform/data-platform.md) dat wordt gebruikt door inzichten.
* Meer informatie over de verschillende [gegevensbronnen die door Azure Monitor worden gebruikt](../platform/data-sources.md) en de verschillende soorten gegevens die door elk van de inzichten worden verzameld.
