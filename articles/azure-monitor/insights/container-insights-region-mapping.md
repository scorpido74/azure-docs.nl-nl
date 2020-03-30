---
title: Azure Monitor voor toewijzingen van containersregio's
description: In dit artikel worden de regiotoewijzingen beschreven die worden ondersteund tussen Azure Monitor voor containers, Log Analytics Workspace en aangepaste statistieken.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403426"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Regiotoewijzingen ondersteund door Azure Monitor voor containers

 Wanneer u Azure Monitor inschakelt voor containers, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte en een AKS-cluster en het verzamelen van aangepaste statistieken die zijn ingediend bij Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Door Log Analytics-werkruimte ondersteunde toewijzingen

De AKS-clusterbronnen of de werkruimte Log Analytics kunnen zich in andere regio's bevinden en in de volgende tabel worden onze toewijzingen weergegeven.

|**AKS-clustergebied** | **Regio Logboekanalysewerkruimte** |
|-----------------------|------------------------------------|
|**Afrika** | |
|Zuid-AfrikaNoord |West-Europa |
|Zuid-AfrikaWest |West-Europa |
|**Australië** | |
|AustraliëOost |AustraliëOost |
|AustraliëCentraal |AustraliëCentraal |
|AustraliëCentraal2 |AustraliëCentraal |
|AustraliëOost |AustraliëOost |
|**Azië en Stille Oceaan** | |
|Oost-Azië |Oost-Azië |
|ZuidoostAzië |ZuidoostAzië |
|**Brazilië** | |
|BraziliëZuid | SouthCentralUS (SouthCentralUS) |
|**Canada** ||
|CanadaCentraal |CanadaCentraal |
|CanadaOost |CanadaCentraal |
|**Europa** | |
|FrankrijkCentraal |FrankrijkCentraal |
|FrankrijkZuid |FrankrijkCentraal |
|Noord-Europa |Noord-Europa |
|UKZuid |UKZuid |
|UKWest |UKZuid |
|West-Europa |West-Europa |
|**India** | |
|Centraal-India |Centraal-India |
|Zuid-India |Centraal-India |
|WestIndia WestIndia |Centraal-India |
|**Japan** | |
|JapanOost |JapanOost |
|JapanWest (JapanWest) |JapanOost |
|**Korea** | |
|KoreaCentraal |KoreaCentraal |
|KoreaZuid |KoreaCentraal |
|**VS** | |
|CentralUS CentralUS |CentralUS CentralUS|
|EastUS EastUS |EastUS EastUS |
|EastUS2 EastUS2 |EastUS2 EastUS2 |
|WestUS (WestUS) |WestUS (WestUS) |
|WestUS2 WestUS2 |WestUS2 WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|VS (overheid) - Virginia |VS (overheid) - Virginia |

<sup>1</sup> Vanwege capaciteitsbeperkingen is de regio niet beschikbaar bij het maken van nieuwe bronnen. Dit omvat een Werkruimte Log Analytics. Reeds bestaande gekoppelde middelen in de regio moeten echter blijven werken.

## <a name="custom-metrics-supported-regions"></a>Ondersteunde aangepaste statistieken regio's

Het verzamelen van statistieken van AKS-clusters en pods (Azure Kubernetes Services) wordt alleen ondersteund voor publicatie als aangepaste statistieken in de volgende [Azure-regio's.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen met het bewaken van uw AKS-cluster, controleert u [Hoe u de Azure-monitor voor containers inschakelt](container-insights-onboard.md) om inzicht te krijgen in de vereisten en beschikbare methoden om bewaking in te schakelen.  
