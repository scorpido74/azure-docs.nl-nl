---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75752162"
---
Met de aanzienlijke groei van VM's die in Azure worden gehost, is het belangrijk om prestatie- en gezondheidsproblemen te identificeren die van invloed zijn op toepassingen en infrastructuurservices die ze ondersteunen. Basisbewaking wordt standaard met Azure geleverd door de metrische typen CPU-gebruik, schijfgebruik, geheugengebruik en netwerkverkeer dat door de hosthypervisor wordt verzameld. Aanvullende metrische en logboekgegevens kunnen worden verzameld met behulp van [extensies](../articles/virtual-machines/windows/extensions-features.md) om diagnostische gegevens op uw VM's te configureren vanuit het gastbesturingssysteem.

Azure Monitor biedt gecentraliseerde bewaking met uitgebreide functies zoals Azure Monitor voor VM's om prestatie- en gezondheidsproblemen met het gastbesturingssysteem, .NET- of Java-webtoepassingscomponenten die in de VM worden uitgevoerd, op te sporen en te diagnosticeren. en Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostiek en statistieken 

U de verzameling [diagnostische gegevens](https://docs.microsoft.com/cli/azure/vm/diagnostics) instellen en bewaken met behulp van [metrische gegevens](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) in de Azure-portal, azure CLI, Azure PowerShell en API's (Programming Interfaces) voor het programmeren van toepassingen. U kunt bijvoorbeeld:

- **Bekijk basisstatistieken voor de VM.** Op het overzichtsscherm van de Azure-portal worden de basisstatistieken weergegeven, waaronder CPU-gebruik, netwerkgebruik, totaal schijfbytes en schijfbewerkingen per seconde.

- **Schakel het verzamelen van opstartdiagnoses in en bekijk deze met behulp van de Azure-portal.** Wanneer u uw eigen afbeelding naar Azure brengt of zelfs een van de platformafbeeldingen opstart, kunnen er vele redenen zijn waarom een VM in een niet-opstartbare status terechtkomt. U opstartdiagnose eenvoudig inschakelen wanneer u een vm maakt door te klikken op **Ingeschakeld** voor opstartdiagnose onder de sectie Controle van het scherm Instellingen.

    Als VM's opstarten, de boot diagnostic agent vangt boot output en slaat het op in Azure-opslag. Deze gegevens kunnen worden gebruikt om opstartproblemen met virtuele machines op te lossen. Opstartdiagnostiek wordt niet automatisch ingeschakeld wanneer u een VM maakt vanuit opdrachtregelgereedschappen. Voordat u diagnostische gegevens over opstarten inschakelt, moet een opslagaccount worden gemaakt voor het opslaan van de opstartlogboeken. Als u opstartdiagnoses inschakelt in de Azure-portal, wordt er automatisch een opslagaccount voor u gemaakt.

    Als u opstartdiagnoses niet hebt ingeschakeld toen de VM werd gemaakt, u deze later altijd inschakelen met [Azure CLI,](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics) [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)of een [Azure Resource Manager-sjabloon](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Schakel het verzamelen van diagnostische gegevens van gastbesturingssysteemgegevens in.** Wanneer u een vm maakt, hebt u de mogelijkheid op het instellingenscherm om diagnose van gastbesturingssysteem in te schakelen. Wanneer u het verzamelen van diagnostische gegevens inschakelt, wordt de [IaaSDiagnostics-extensie voor Linux](../articles/virtual-machines/linux/diagnostic-extension.md) of de [IaaSDiagnostics-extensie voor Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) toegevoegd aan de VM, waarmee u extra schijf-, CPU- en geheugengegevens verzamelen.

    Met behulp van de verzamelde diagnostische gegevens u automatisch schalen configureren voor uw VM's. U [azure-monitorlogboeken](../articles/azure-monitor/platform/data-platform-logs.md) ook configureren om de gegevens op te slaan en waarschuwingen in te stellen om u te laten weten wanneer de prestaties niet goed zijn.

## <a name="alerts"></a>Waarschuwingen

U [waarschuwingen](../articles/azure-monitor/platform/alerts-overview.md) maken op basis van specifieke prestatiestatistieken. Voorbeelden van de problemen waarover u worden gewaarschuwd, zijn wanneer het gemiddelde CPU-gebruik een bepaalde drempel overschrijdt of de beschikbare vrije schijfruimte onder een bepaalde hoeveelheid zakt. Waarschuwingen kunnen worden geconfigureerd in de [Azure-portal,](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)met Azure [Resource Manager-sjablonen](../articles/azure-monitor/platform/alerts-metric-create-templates.md)of [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) biedt persoonlijke richtlijnen en ondersteuning wanneer problemen in Azure-services van invloed zijn op u en helpt u zich voor te bereiden op aankomend gepland onderhoud. Azure Service Health waarschuwt u en uw teams met behulp van gerichte en flexibele meldingen.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource-status](../articles/service-health/resource-health-overview.md) helpt u bij het diagnosticeren en ondersteuning krijgen wanneer een Azure-probleem gevolgen heeft voor uw resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.

## <a name="azure-activity-log"></a>Azure-activiteitenlogboek

Het [Azure Activity Log](../articles/azure-monitor/platform/platform-logs-overview.md) is een abonnementslogboek dat inzicht geeft in gebeurtenissen op abonnementsniveau die zich in Azure hebben voorgedaan. Het logboek bevat een reeks gegevens, van operationele gegevens van Azure Resource Manager tot updates over servicestatusgebeurtenissen. U op Activiteitslogboek klikken in de Azure-portal om het logboek voor uw vm weer te geven.

Enkele van de dingen die je doen met het activiteitenlogboek zijn:

- Maak een [waarschuwing voor een gebeurtenis activiteitenlogboek](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Stream deze naar een Event Hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) voor opname door een service van derden of aangepaste analyseoplossingen zoals Power BI.
- Analyseer het in Power BI met het [Power BI-inhoudspakket](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Sla het op in een opslagaccount](../articles/azure-monitor/platform/archive-activity-log.md) voor archivering of handmatige inspectie. U de bewaartijd (in dagen) opgeven met behulp van het logboekprofiel.

U ook toegang krijgen tot gegevens over activiteitenlogboeken met [Azure PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.insights/)azure [CLI](https://docs.microsoft.com/cli/azure/monitor)of [Monitor REST-API's](https://docs.microsoft.com/rest/api/monitor/).

[Azure Resource Logs](../articles/azure-monitor/platform/platform-logs-overview.md) zijn logboeken die worden uitgezonden door uw VM die uitgebreide, frequente gegevens over de werking ervan bevatten. Resourcelogboeken verschillen van het activiteitenlogboek door inzicht te geven in bewerkingen die binnen de VM zijn uitgevoerd.

Enkele van de dingen die u doen met diagnostische logboeken zijn:

- [Sla ze op in een opslagaccount](../articles/azure-monitor/platform/archive-diagnostic-logs.md) voor controle of handmatige inspectie. U de bewaartijd (in dagen) opgeven met behulp van diagnostische instellingen voor resources.
- [Stream ze naar Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) voor opname door een service van derden of aangepaste analyse-oplossingen zoals Power BI.
- Analyseer ze met [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Geavanceerde controle

Voor de zichtbaarheid van de toepassing of service die wordt ondersteund door de azure VM- en virtuele machineschaalsets, u problemen met het gastbesturingssysteem of de werkbelasting die in de VM wordt uitgevoerd, worden geïdentificeerd om te begrijpen of dit van invloed is op de beschikbaarheid of prestaties van de toepassing of dat dit een probleem is met de toepassing, en schakelt u zowel [Azure Monitor voor VM's](../articles/azure-monitor/insights/vminsights-overview.md) als [Application Insights in.](../articles/azure-monitor/app/app-insights-overview.md)

Azure Monitor voor VM's bewaakt uw Virtuele Azure-machines (VM) op schaal door de prestaties en status van uw Windows- en Linux-VM's te analyseren, inclusief de verschillende processen en onderling verbonden afhankelijkheden van andere resources en externe processen Ontdekt. Het bevat verschillende trendprestatiegrafieken om te helpen bij het onderzoeken van problemen en het beoordelen van de capaciteit van uw VM's. De afhankelijkheidskaart toont bewaakte en niet-bewaakte machines, mislukte en actieve netwerkverbindingen tussen processen en deze machines en toont trenddiagrammen met standaard netwerkverbindingsstatistieken. In combinatie met Application Insights controleert u uw toepassing en legt u telemetrie vast, zoals HTTP-verzoeken, uitzonderingen, enz. Configureer [Azure Monitor-waarschuwingen](../articles/azure-monitor/platform/alerts-overview.md) om u te waarschuwen voor belangrijke voorwaarden die zijn gedetecteerd door bewakingsgegevens die zijn verzameld door Azure Monitor voor VM's.

## <a name="next-steps"></a>Volgende stappen

- Loop door de stappen in [Monitor een Virtuele Windows-machine met Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) of [Monitor een Virtuele Linux-machine met de Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Meer informatie over de aanbevolen procedures rond [monitoring en diagnostiek](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
