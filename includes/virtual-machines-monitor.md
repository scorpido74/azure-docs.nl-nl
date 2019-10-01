---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: 93a2554b5d3cc24e1b5fc1e3d0f18ed1bfe0579c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71692029"
---
Met de aanzienlijke groei van virtuele machines die worden gehost in azure, is het belang rijk om prestatie-en status problemen te identificeren die van invloed zijn op toepassingen en infrastructuur services die ze ondersteunen. Basis bewaking wordt standaard geleverd met Azure door de metrische typen CPU-gebruik, schijf gebruik, geheugen gebruik en netwerk verkeer dat door de host-Hyper Visor is verzameld. Aanvullende metrische gegevens en logboeken kunnen worden verzameld met behulp van [uitbrei dingen](../articles/virtual-machines/windows/extensions-features.md) voor het configureren van diagnoses op uw vm's vanuit het gast besturingssysteem.

Azure Monitor voorziet in gecentraliseerde bewaking met uitgebreide functies, zoals Azure Monitor voor VM's, om prestatie-en status problemen te detecteren en op te sporen met het gast besturingssysteem, .NET-of Java-onderdelen van webtoepassingen die worden uitgevoerd in de virtuele machine. en Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostische gegevens en metrische gegevens 

U kunt de verzameling [Diagnostische gegevens](https://docs.microsoft.com/cli/azure/vm/diagnostics) instellen en bewaken met behulp van [gegevens](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) in de Azure Portal, de Azure CLI, Azure PowerShell en Programming Applications Programming Interfaces (api's). U kunt bijvoorbeeld:

- **Algemene metrische gegevens voor de virtuele machine bekijken.** In het scherm overzicht van de Azure Portal zijn de basis gegevens die worden weer gegeven, onder andere het CPU-gebruik, het netwerk gebruik, het totale aantal schijf bytes en schijf bewerkingen per seconde.

- **Schakel het verzamelen van diagnostische gegevens over opstarten in en Bekijk deze met behulp van de Azure Portal.** Wanneer u uw eigen installatie kopie naar Azure brengt of als u een van de platform installatie kopieën zelfs opstart, kan er een groot aantal redenen zijn waarom een virtuele machine in een niet-opstart bare status komt. U kunt de diagnostische gegevens over opstarten eenvoudig inschakelen wanneer u een virtuele machine maakt door te klikken op **ingeschakeld** voor diagnostische gegevens over opstarten in het gedeelte bewaking van het scherm instellingen.

    Als het opstarten van de virtuele machine wordt opgestart, wordt in de diagnostische agent voor opstarten de opstart uitvoer vastgelegd en opgeslagen in azure Storage. Deze gegevens kunnen worden gebruikt om opstartproblemen met virtuele machines op te lossen. Diagnostische gegevens over opstarten worden niet automatisch ingeschakeld wanneer u een virtuele machine maakt vanuit opdracht regel Programma's. Voordat u diagnostische gegevens over opstarten inschakelt, moet een opslagaccount worden gemaakt voor het opslaan van de opstartlogboeken. Als u Diagnostische gegevens over opstarten inschakelt in de Azure Portal, wordt er automatisch een opslag account voor u gemaakt.

    Als u Diagnostische gegevens over opstarten niet hebt ingeschakeld toen de virtuele machine werd gemaakt, kunt u deze altijd later inschakelen met behulp van [Azure cli](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)of een [Azure Resource Manager sjabloon](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Schakel de verzameling diagnostische gegevens van het gast besturingssysteem in.** Wanneer u een virtuele machine maakt, hebt u de mogelijkheid om diagnostische gegevens van het gast besturingssysteem in te scha kelen op het scherm instellingen. Wanneer u het verzamelen van diagnostische gegevens inschakelt, wordt de [IaaSDiagnostics-extensie voor Linux](../articles/virtual-machines/linux/diagnostic-extension.md) of de [IaaSDiagnostics-extensie voor Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) toegevoegd aan de VM, waarmee u extra schijf-, CPU-en geheugen gegevens kunt verzamelen.

    Met de verzamelde diagnostische gegevens kunt u automatisch schalen configureren voor uw virtuele machines. U kunt ook [Azure monitor logboeken](../articles/azure-monitor/platform/data-platform-logs.md) configureren om de gegevens op te slaan en waarschuwingen in te stellen om u te laten weten wanneer de prestaties niet goed zijn.

## <a name="alerts"></a>Waarschuwingen

U kunt [waarschuwingen](../articles/azure-monitor/platform/alerts-overview.md) maken op basis van specifieke metrische gegevens voor prestaties. Voor beelden van problemen waarvoor u een waarschuwing krijgt wanneer het gemiddelde CPU-gebruik een bepaalde drempel waarde overschrijdt of de beschik bare vrije schijf ruimte onder een bepaald aantal daalt. Waarschuwingen kunnen worden geconfigureerd in de [Azure Portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), met behulp van [Azure Resource Manager-sjablonen](../articles/azure-monitor/platform/alerts-metric-create-templates.md)of [Azure cli](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure-servicestatus

[Azure service Health](../articles/service-health/service-health-overview.md) biedt persoonlijke begeleiding en ondersteuning als problemen in Azure-Services van invloed zijn op u, en helpt u voor te bereiden voor gepland onderhoud. Azure Service Health waarschuwt u en uw teams met behulp van gerichte en flexibele meldingen.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure resource Health](../articles/service-health/resource-health-overview.md) helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.

## <a name="azure-activity-log"></a>Azure-activiteitenlogboek

Het [Azure-activiteiten logboek](../articles/azure-monitor/platform/activity-logs-overview.md) is een abonnements logboek dat inzicht biedt in gebeurtenissen op abonnements niveau die zich in azure hebben voorgedaan. Het logboek bevat een reeks gegevens, van Azure Resource Manager operationele gegevens naar updates op Service Health-gebeurtenissen. U kunt in de Azure Portal op activiteiten logboek klikken om het logboek voor uw virtuele machine weer te geven.

U kunt onder andere het volgende doen met het activiteiten logboek:

- Een [waarschuwing voor een activiteiten logboek gebeurtenis](../articles/azure-monitor/platform/activity-logs-overview.md)maken.
- [Stream het naar een event hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) voor opname door een service van derden of een aangepaste analyse oplossing, zoals Power bi.
- Analyseer deze in Power BI met behulp van het [Power bi-inhouds pakket](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Sla het bestand op in een opslag account](../articles/azure-monitor/platform/archive-activity-log.md) voor archivering of hand matige inspectie. U kunt de Bewaar periode (in dagen) opgeven met behulp van het logboek profiel.

U kunt activiteiten logboek gegevens ook openen met behulp van [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), de [Azure cli](https://docs.microsoft.com/cli/azure/monitor)of de [monitor rest api's](https://docs.microsoft.com/rest/api/monitor/).

[Azure-resource logboeken](../articles/azure-monitor/platform/resource-logs-overview.md) zijn logboeken die door uw virtuele machine worden gegenereerd en die uitgebreide, frequente gegevens over de werking bieden. Bron logboeken verschillen van het activiteiten logboek door inzicht te bieden in de bewerkingen die zijn uitgevoerd in de virtuele machine.

Hieronder vindt u enkele dingen die u met Diagnostische logboeken kunt doen:

- [Sla ze op in een opslag account](../articles/azure-monitor/platform/archive-diagnostic-logs.md) voor controle of hand matige inspectie. U kunt de Bewaar tijd (in dagen) opgeven met behulp van de diagnostische instellingen van de resource.
- [Stream ze naar Event hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) voor opname door een service van derden of een aangepaste analyse oplossing, zoals Power bi.
- Analyseer ze met [log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Geavanceerde controle

Voor de zicht baarheid van de toepassing of service die wordt ondersteund door de Azure VM en virtuele-machine schaal sets, worden de problemen met het gast besturingssysteem of de werk belasting die wordt uitgevoerd op de VM, geïdentificeerd als ze van invloed zijn op de beschik baarheid of prestaties van de toepassing, of een Maak een probleem met de toepassing en Schakel zowel [Azure monitor voor VM's](../articles/azure-monitor/insights/vminsights-overview.md) als [Application Insights](../articles/azure-monitor/app/app-insights-overview.md)in.

Azure Monitor voor VM's bewaakt uw Azure virtual machines (VM) op schaal door de prestaties en status van uw Windows-en Linux-Vm's te analyseren, met inbegrip van de verschillende processen en onderling verbonden afhankelijkheden voor andere bronnen en externe processen detecteert. Het bevat verschillende diagrammen voor trend prestaties die u kunnen helpen bij het onderzoeken van problemen en het beoordelen van de capaciteit van uw Vm's. De afhankelijkheids kaart toont bewaakte en niet-bewaakte computers, mislukte en actieve netwerk verbindingen tussen processen en deze machines, en toont trend grafieken met standaard metrische netwerk verbindings gegevens. In combi natie met Application Insights kunt u uw toepassing bewaken en telemetrie vastleggen, zoals HTTP-aanvragen, uitzonde ringen, enzovoort Configureer [Azure monitor waarschuwingen](../articles/azure-monitor/platform/alerts-overview.md) om u te waarschuwen over belang rijke voor waarden die worden verzameld door de bewakings gegevens die door Azure monitor voor VM's zijn ingezameld.

## <a name="next-steps"></a>Volgende stappen

- Volg de stappen in [een virtuele Windows-machine bewaken met Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) of [Bewaak een virtuele Linux-machine met de Azure cli](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Meer informatie over de aanbevolen procedures rond [bewaking en diagnose](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
