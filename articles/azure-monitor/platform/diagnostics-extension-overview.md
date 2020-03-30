---
title: Overzicht van Azure Diagnostics-extensie
description: Azure-diagnose gebruiken voor foutopsporing, het meten van prestaties, monitoring, verkeersanalyse in cloudservices, virtuele machines en servicefabric
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672375"
---
# <a name="azure-diagnostics-extension-overview"></a>Overzicht van Azure Diagnostics-extensie
Azure Diagnostics-extensie is een [agent in Azure Monitor](agents-overview.md) die bewakingsgegevens verzamelt van het gastbesturingssysteem van Azure-rekenbronnen, waaronder virtuele machines. In dit artikel vindt u een overzicht van de Azure Diagnostics-extensie, inclusief specifieke functionaliteit die wordt ondersteund en opties voor installatie en configuratie. 

> [!NOTE]
> Azure Diagnostics-extensie is een van de agents die beschikbaar is om bewakingsgegevens te verzamelen van het gastbesturingssysteem van compute resources. Zie [Overzicht van de Azure Monitor-agents](agents-overview.md) voor een beschrijving van de verschillende agents en richtlijnen voor het selecteren van de juiste agents voor uw vereisten.

## <a name="comparison-to-log-analytics-agent"></a>Vergelijking met loganalytics-agent
De Log Analytics-agent in Azure Monitor kan ook worden gebruikt om bewakingsgegevens te verzamelen van het gastbesturingssysteem van virtuele machines. U ervoor kiezen om een van beide of beide te gebruiken, afhankelijk van uw vereisten. Zie [Overzicht van de Azure Monitor-agents](agents-overview.md) voor een gedetailleerde vergelijking van de Azure Monitor-agents. 

De belangrijkste verschillen om te overwegen zijn:

- Azure Diagnostics Extension kan alleen worden gebruikt met virtuele Azure-machines. De log-analyse-agent kan worden gebruikt met virtuele machines in Azure, andere clouds en on-premises.
- Azure Diagnostics-extensie verzendt gegevens naar Azure Storage, [Azure Monitor Metrics](data-platform-metrics.md) (alleen Windows) en Event Hubs. De medewerker Logboekanalyse verzamelt gegevens naar [Azure Monitor Logs](data-platform-logs.md).
- De log-analyse-agent is vereist voor [oplossingen,](../monitor-reference.md#insights-and-core-solutions) [Azure Monitor voor VM's](../insights/vminsights-overview.md)en andere services zoals [Azure Security Center.](/azure/security-center/)

## <a name="costs"></a>Kosten
Er zijn geen kosten verbonden aan Azure Diagnostic Extension, maar er kunnen wel kosten in rekening worden gebracht voor de ingenomen gegevens. Controleer [de prijzen van Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) op de bestemming waar u gegevens verzamelt.

## <a name="data-collected"></a>Verzamelde gegevens
In de volgende tabellen worden de gegevens weergegeven die kunnen worden verzameld door de diagnose-extensie Windows en Linux.

### <a name="windows-diagnostics-extension-wad"></a>Windows-diagnose-extensie (WAD)

| Gegevensbron | Beschrijving |
| --- | --- |
| Logboeken voor Windows-gebeurtenissen   | Gebeurtenissen uit windows-gebeurtenislogboek. |
| Prestatiemeteritems | Numerieke waarden meten de prestaties van verschillende aspecten van het besturingssysteem en workloads. |
| IIS-logboeken             | Gebruiksinformatie voor IIS-websites die op het gastbesturingssysteem worden uitgevoerd. |
| Toepassingslogboeken     | Traceer berichten geschreven door uw toepassing. |
| .NET EventSource-logboeken |Codeschrijven gebeurtenissen met de klasse .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| [Manifest gebaseerde ETW-logboeken](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Gebeurtenistracering voor Windows-gebeurtenissen die door elk proces worden gegenereerd. |
| Crashdumps (logboeken)   | Informatie over de status van het proces als een toepassing vastloopt. |
| Op bestanden gebaseerde logboeken    | Logboeken die zijn gemaakt door uw toepassing of service. |
| Diagnostische logboeken voor agent | Informatie over Azure Diagnostics zelf. |


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnostische extensie (LAD)

| Gegevensbron | Beschrijving |
| --- | --- |
| Syslog | Gebeurtenissen die naar het Linux-gebeurtenisregistratiesysteem worden verzonden.   |
| Prestatiemeteritems  | Numerieke waarden meten de prestaties van verschillende aspecten van het besturingssysteem en workloads. |
| Logboekbestanden | Vermeldingen die naar een bestand gebaseerd logboek worden verzonden.  |

## <a name="data-destinations"></a>Gegevensbestemmingen
De Azure Diagnostic-extensie voor zowel Windows als Linux verzamelt altijd gegevens in een Azure Storage-account. Zie [De diagnostische extensie van Windows Azure (WAD) installeren en configureren](diagnostics-extension-windows-install.md) en [Linux-diagnostische extensie gebruiken om statistieken en logboeken te controleren](../../virtual-machines/extensions/diagnostics-linux.md) voor een lijst met specifieke tabellen en blobs waarin deze gegevens worden verzameld.

Configureer een of meer *gegevenssinks* om gegevens naar andere extra bestemmingen te verzenden. In de volgende secties worden de sinks vermeld die beschikbaar zijn voor de Windows- en Linux-diagnostische extensie.

### <a name="windows-diagnostics-extension-wad"></a>Windows-diagnose-extensie (WAD)

| Doel | Beschrijving |
|:---|:---|
| Azure-monitorstatistieken | Prestatiegegevens verzamelen in Azure Monitor Metrics. Zie [Statistieken van gastbesturingssysteem verzenden naar de azure monitor-metrische database](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Event Hubs | Gebruik Azure Event Hubs om gegevens buiten Azure te verzenden. Zie [Azure Diagnostics-gegevens streamen naar gebeurtenishubs](diagnostics-extension-stream-event-hubs.md) |
| Azure Storage-blobs | Schrijf naast tabellen naar gegevens naar blobs in Azure Storage. |
| Application Insights | Verzamel gegevens van toepassingen die in uw VM worden uitgevoerd tot Application Insights om te integreren met andere toepassingsbewaking. Zie [Diagnostische gegevens verzenden naar Application Insights](diagnostics-extension-to-application-insights.md). |

U ook WAD-gegevens uit de opslag verzamelen in een log analytics-werkruimte om deze te analyseren met Azure Monitor Logs, hoewel de log-analyse-agent doorgaans voor deze functionaliteit wordt gebruikt. Het kan gegevens rechtstreeks naar een Log Analytics-werkruimte verzenden en ondersteunt oplossingen en inzichten die extra functionaliteit bieden.  Zie [Diagnostische logboeken van Azure verzamelen vanuit Azure Storage](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnostische extensie (LAD)
LAD schrijft gegevens naar tabellen in Azure Storage. Het ondersteunt de putten in de volgende tabel.

| Doel | Beschrijving |
|:---|:---|
| Event Hubs | Gebruik Azure Event Hubs om gegevens buiten Azure te verzenden. |
| Azure Storage-blobs | Schrijf naast tabellen naar gegevens naar blobs in Azure Storage. |
| Azure-monitorstatistieken | Installeer de Telegraf-agent naast LAD. Zie [Aangepaste statistieken verzamelen voor een Linux-vm met de InfluxData Telegraf-agent](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Installatie en configuratie
De diagnostische extensie wordt geïmplementeerd als een [extensie voor virtuele machines](../../virtual-machines/extensions/overview.md) in Azure, zodat deze dezelfde installatieopties ondersteunt met behulp van Resource Manager-sjablonen, PowerShell en CLI. Zie [Virtuele machine-extensies en -functies voor Windows](../../virtual-machines/extensions/features-windows.md) en virtuele [machine-extensies en -functies voor Linux](../../virtual-machines/extensions/features-linux.md) voor algemene details over het installeren en onderhouden van virtuele machine-extensies.

U ook zowel de diagnose-extensie Windows als Linux installeren en configureren in de Azure-portal onder **Diagnostische instellingen** in het gedeelte **Monitoring** van het menu van de virtuele machine.

Zie de volgende artikelen voor meer informatie over het installeren en configureren van de diagnostische extensie voor Windows en Linux.

- [Windows Azure-diagnostische extensie (WAD) installeren en configureren](diagnostics-extension-windows-install.md)
- [De diagnostische Linux-extensie gebruiken voor het bewaken van metrische gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Andere documentatie

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (klassieke) web- en werknemersrollen
- [Inleiding tot Cloud Service Monitoring](../../cloud-services/cloud-services-how-to-monitor.md)
- [Azure Diagnostics inschakelen in Azure Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Toepassingsinzichten voor Azure-cloudservices](../app/cloudservices.md)<br>[De stroom van een Cloud Services-toepassing traceren met Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Services controleren en diagnosticeren in een ontwikkelomgeving met lokale machines](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Volgende stappen


* Informatie over [het gebruik van prestatiemeteritems in Azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Zie [TroubleShooting Azure Diagnostics](diagnostics-extension-troubleshooting.md) als u problemen ondervindt bij het starten of vinden van uw gegevens in Azure-opslagtabellen.

