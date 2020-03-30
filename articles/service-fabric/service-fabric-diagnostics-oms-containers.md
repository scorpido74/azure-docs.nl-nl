---
title: Containers met Azure Monitor-logboeken controleren
description: Azure Monitor-logboeken gebruiken voor het bewaken van containers die worden uitgevoerd op Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614431"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Containers met Azure Monitor-logboeken controleren
 
In dit artikel worden de stappen beschreven die nodig zijn om de oplossing voor containerbewaking van Azure Monitor in te stellen om containergebeurtenissen weer te geven. Als u uw cluster wilt instellen om containergebeurtenissen te verzamelen, raadpleegt u deze [stapsgewijze zelfstudie](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>De oplossing voor containerbewaking instellen

> [!NOTE]
> U moet Azure Monitor-logboeken hebben ingesteld voor uw cluster en de log-analyse-agent op uw knooppunten laten implementeren. Als u dit niet doet, volgt u eerst de stappen in [Azure Monitor-logboeken instellen](service-fabric-diagnostics-oms-setup.md) en voegt u de agent Log Analytics eerst [toe aan een cluster.](service-fabric-diagnostics-oms-agent.md)

1. Zodra uw cluster is ingesteld met Azure Monitor-logboeken en de agent Log Analytics, implementeert u uw containers. Wacht tot uw containers zijn geïmplementeerd voordat u naar de volgende stap gaat.

2. Zoek in Azure Marketplace naar *containermonitoringoplossing* en klik op de **containerbewakingsoplossingsbron** die wordt weergegeven onder de categorie Bewaken + beheer.

    ![Containers-oplossing toevoegen](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Maak de oplossing in dezelfde werkruimte die al is gemaakt voor het cluster. Deze wijziging activeert automatisch de agent om te beginnen met het verzamelen van dockergegevens op de containers. In ongeveer 15 minuten of zo, moet u zien de oplossing oplichten met inkomende logs en statistieken, zoals weergegeven in de afbeelding hieronder.

    ![Basic Log Analytics-dashboard](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

De agent maakt het mogelijk om verschillende containerspecifieke logboeken te verzamelen die kunnen worden opgevraagd in Azure Monitor-logboeken of die kunnen worden gebruikt om prestatie-indicatoren te visualiseren. De logboektypen die worden verzameld zijn:

* ContainerInventory: toont informatie over containerlocatie, naam en afbeeldingen
* ContainerImageInventory: informatie over geïmplementeerde afbeeldingen, inclusief i-adressen of -formaten
* ContainerLog: specifieke foutlogboeken, dockerlogboeken (stdout, enz.) en andere vermeldingen
* ContainerServiceLog: docker daemon opdrachten die zijn uitgevoerd
* Perf: prestatiemeteritems, waaronder containercpu, geheugen, netwerkverkeer, schijf-i/o en aangepaste statistieken van de hostmachines



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Azure Monitor logs Containers-oplossing](../azure-monitor/insights/containers.md).
* Lees meer over containerorchestration op Service Fabric - [Service Fabric en containers](service-fabric-containers-overview.md)
* Maak kennis met de [functies voor zoeken en query's voor logboeken](../log-analytics/log-analytics-log-searches.md) die worden aangeboden als onderdeel van Azure Monitor-logboeken
* Azure Monitor-logboeken configureren om [geautomatiseerde waarschuwingsregels](../log-analytics/log-analytics-alerts.md) in te stellen om te helpen bij het detecteren en diagnosticeren