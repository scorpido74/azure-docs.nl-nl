---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 11c9b2ea3ea054415f25f864651df28288aa0025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266837"
---
U kunt profiteren van veel mogelijkheden om uw Vm's te bewaken door diagnostische en logboek gegevens te verzamelen, weer te geven en te analyseren. Als u de virtuele machine eenvoudig wilt [controleren](../articles/azure-monitor/overview.md) , kunt u het overzichts scherm voor de virtuele machine in de Azure Portal gebruiken. U kunt [uitbrei dingen](../articles/virtual-machines/windows/extensions-features.md) gebruiken voor het configureren van diagnostische gegevens op uw vm's voor het verzamelen van extra metrische informatie. U kunt ook meer geavanceerde bewakings opties gebruiken, zoals [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) en [log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostische gegevens en metrische gegevens 

U kunt de verzameling [Diagnostische gegevens](https://docs.microsoft.com/cli/azure/vm/diagnostics) instellen en bewaken met behulp van [gegevens](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) in de Azure Portal, de Azure CLI, Azure PowerShell en Programming Applications Programming Interfaces (api's). U kunt bijvoorbeeld:

- **Algemene metrische gegevens voor de virtuele machine bekijken.** In het scherm overzicht van de Azure Portal zijn de basis gegevens die worden weer gegeven, onder andere het CPU-gebruik, het netwerk gebruik, het totale aantal schijf bytes en schijf bewerkingen per seconde.

- **Schakel het verzamelen van diagnostische gegevens over opstarten in en Bekijk deze met behulp van de Azure Portal.** Wanneer u uw eigen installatie kopie naar Azure brengt of als u een van de platform installatie kopieën zelfs opstart, kan er een groot aantal redenen zijn waarom een virtuele machine in een niet-opstart bare status komt. U kunt de diagnostische gegevens over opstarten eenvoudig inschakelen wanneer u een virtuele machine maakt door te klikken op **ingeschakeld** voor diagnostische gegevens over opstarten in het gedeelte bewaking van het scherm instellingen.

    Als het opstarten van de virtuele machine wordt opgestart, wordt in de diagnostische agent voor opstarten de opstart uitvoer vastgelegd en opgeslagen in azure Storage. Deze gegevens kunnen worden gebruikt om opstartproblemen met virtuele machines op te lossen. Diagnostische gegevens over opstarten worden niet automatisch ingeschakeld wanneer u een virtuele machine maakt vanuit opdracht regel Programma's. Voordat u diagnostische gegevens over opstarten inschakelt, moet een opslagaccount worden gemaakt voor het opslaan van de opstartlogboeken. Als u Diagnostische gegevens over opstarten inschakelt in de Azure Portal, wordt er automatisch een opslag account voor u gemaakt.

    Als u Diagnostische gegevens over opstarten niet hebt ingeschakeld toen de virtuele machine werd gemaakt, kunt u deze altijd later inschakelen met behulp van [Azure cli](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)of een [Azure Resource Manager sjabloon](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Schakel de verzameling diagnostische gegevens van het gast besturingssysteem in.** Wanneer u een virtuele machine maakt, hebt u de mogelijkheid om diagnostische gegevens van het gast besturingssysteem in te scha kelen op het scherm instellingen. Wanneer u het verzamelen van diagnostische gegevens inschakelt, wordt de [IaaSDiagnostics-extensie voor Linux](../articles/virtual-machines/linux/diagnostic-extension.md) of de [IaaSDiagnostics-extensie voor Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) toegevoegd aan de VM, waarmee u extra schijf-, CPU-en geheugen gegevens kunt verzamelen.

    Met de verzamelde diagnostische gegevens kunt u automatisch schalen configureren voor uw virtuele machines. U kunt ook logboeken configureren om de gegevens op te slaan en waarschuwingen in te stellen om u te laten weten wanneer de prestaties niet helemaal goed zijn.

## <a name="alerts"></a>Waarschuwingen

U kunt [waarschuwingen](../articles/azure-monitor/platform/alerts-overview.md) maken op basis van specifieke metrische gegevens voor prestaties. Voor beelden van problemen waarvoor u een waarschuwing krijgt wanneer het gemiddelde CPU-gebruik een bepaalde drempel waarde overschrijdt of de beschik bare vrije schijf ruimte onder een bepaald aantal daalt. Waarschuwingen kunnen worden geconfigureerd in de [Azure Portal](../articles/azure-monitor/platform/alerts-classic-portal.md), met behulp van [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell)of de [Azure cli](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure-servicestatus

[Azure service Health](../articles/service-health/service-health-overview.md) biedt persoonlijke begeleiding en ondersteuning als problemen in Azure-Services van invloed zijn op u, en helpt u voor te bereiden voor gepland onderhoud. Azure Service Health waarschuwt u en uw teams met behulp van gerichte en flexibele meldingen.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure resource Health](../articles/service-health/resource-health-overview.md) helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.

## <a name="azure-activity-log"></a>Azure-activiteitenlogboek

Het [Azure-activiteiten logboek](../articles/azure-monitor/platform/activity-logs-overview.md) is een abonnements logboek dat inzicht biedt in gebeurtenissen op abonnements niveau die zich in azure hebben voorgedaan. Het logboek bevat een reeks gegevens, van Azure Resource Manager operationele gegevens naar updates op Service Health-gebeurtenissen. U kunt in de Azure Portal op activiteiten logboek klikken om het logboek voor uw virtuele machine weer te geven.

U kunt onder andere het volgende doen met het activiteiten logboek:

- Een [waarschuwing voor een activiteiten logboek gebeurtenis](../articles/azure-monitor/platform/activity-logs-overview.md)maken.
- [Stream het naar een event hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) voor opname door een service van derden of een aangepaste analyse oplossing zoals PowerBI.
- Analyseer deze in Power bi met behulp van het [Power bi-inhouds pakket](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Sla het bestand op in een opslag account](../articles/azure-monitor/platform/archive-activity-log.md) voor archivering of hand matige inspectie. U kunt de Bewaar periode (in dagen) opgeven met behulp van het logboek profiel.

U kunt activiteiten logboek gegevens ook openen met behulp van [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), de [Azure cli](https://docs.microsoft.com/cli/azure/monitor)of de [monitor rest api's](https://docs.microsoft.com/rest/api/monitor/).

[Azure-resource logboeken](../articles/azure-monitor/platform/resource-logs-overview.md) zijn logboeken die door uw virtuele machine worden gegenereerd en die uitgebreide, frequente gegevens over de werking bieden. Bron logboeken verschillen van het activiteiten logboek door inzicht te bieden in de bewerkingen die zijn uitgevoerd in de virtuele machine.

Hieronder vindt u enkele dingen die u met Diagnostische logboeken kunt doen:

- [Sla ze op in een opslag account](../articles/azure-monitor/platform/archive-diagnostic-logs.md) voor controle of hand matige inspectie. U kunt de Bewaar tijd (in dagen) opgeven met behulp van de diagnostische instellingen van de resource.
- [Stream ze naar Event hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) voor opname door een service van derden of een aangepaste analyse oplossing zoals PowerBI.
- Analyseer ze met [log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Geavanceerde controle

- [Azure monitor](../articles/azure-monitor/overview.md) is een service die uw Cloud-en on-premises omgevingen bewaakt om hun Beschik baarheid en prestaties te behouden. Het biedt een uitgebreide oplossing voor het verzamelen, analyseren en handelen op telemetrie in uw Cloud-en on-premises omgevingen. Het helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn. U kunt een uitbrei ding installeren op een [virtuele Linux-machine](../articles/virtual-machines/linux/extensions-oms.md) of een [Windows-vm](../articles/virtual-machines/windows/extensions-oms.md) die de log Analytics-Agent installeert om logboek gegevens te verzamelen en op te slaan in een log Analytics-werk ruimte.

    Voor virtuele Windows-en Linux-machines is de aanbevolen methode voor het verzamelen van Logboeken door de Log Analytics-agent te installeren. De eenvoudigste manier om de Log Analytics-agent op een virtuele machine te installeren, is via de [log Analytics VM-extensie](../articles/log-analytics/log-analytics-azure-vm-extension.md). De extensie vereenvoudigt het installatieproces en configureert automatisch de agent voor het verzenden van gegevens naar de Log Analytics-werkruimte die u opgeeft. De agent wordt bovendien automatisch bijgewerkt, zodat u over de nieuwste functies en correcties beschikt.

- Met [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) kunt u uw virtuele machine en de bijbehorende resources bewaken zoals ze betrekking hebben op het netwerk waarin ze zich bevinden. U kunt de Network Watcher agent-extensie installeren op een [virtuele Linux-machine](../articles/virtual-machines/linux/extensions-nwa.md) of een [Windows-VM](../articles/virtual-machines/windows/extensions-nwa.md).

- [Azure monitor voor VM's](../articles/azure-monitor/insights/vminsights-overview.md) bewaakt uw Azure virtual machines (VM) op schaal door de prestaties en status van uw Windows-en Linux-vm's te analyseren, inclusief de verschillende processen en onderling verbonden afhankelijkheden voor andere bronnen en externe wijzen. 

## <a name="next-steps"></a>Volgende stappen
- Volg de stappen in [een virtuele Windows-machine bewaken met Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) of [Bewaak een virtuele Linux-machine met de Azure cli](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Meer informatie over de aanbevolen procedures rond [bewaking en diagnose](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
