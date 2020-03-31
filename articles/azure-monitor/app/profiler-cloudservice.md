---
title: Live Azure Cloud Services profileren met toepassingsinzichten | Microsoft Documenten
description: Application Insights Profiler inschakelen voor Azure Cloud Services.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671661"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Live Azure Cloud Services profileren met Application Insights

U Application Insights Profiler ook implementeren op deze services:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-toepassingen](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtuele machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler is geïnstalleerd met de Azure Diagnostics-extensie. U hoeft alleen azure diagnostics te configureren om Profiler te installeren en profielen naar uw Application Insights-bron te verzenden.

## <a name="enable-profiler-for-azure-cloud-services"></a>Profiler inschakelen voor Azure Cloud Services
1. Controleer of u [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) of nieuwer gebruikt. Als u OS-familie 4 gebruikt, moet u .NET Framework 4.6.1 of nieuwer installeren met een [opstarttaak.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet) OS Family 5 bevat standaard een compatibele versie van .NET Framework. 

1. Add [Application Insights SDK to Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **De bug in de profiler die schepen in de WAD voor Cloud Services is opgelost.** De nieuwste versie van WAD (1.12.2.0) voor Cloud Services werkt met alle recente versies van de App Insights SDK. Cloud Service-hosts upgraden WAD automatisch, maar het is niet onmiddellijk. Als u een upgrade wilt forceren, u uw service opnieuw implementeren of het knooppunt opnieuw opstarten.

1. Aanvragen bijhouden met Application Insights:

    * Voor ASP.NET webrollen kan Application Insights de aanvragen automatisch bijhouden.

    * Voor werknemersrollen [voegt u code toe om aanvragen bij te houden](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configureer de azure diagnostics-extensie om Profiler in te schakelen:

    a. Zoek het [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Diagnostics.wadcfgx-bestand* voor uw toepassingsrol, zoals hier wordt weergegeven:  

      ![Locatie van het diagnose-config-bestand](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Zie Diagnostische gegevens instellen voor [Azure Cloud Services en Virtuele Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)als u het bestand niet vinden.

    b. Voeg de `SinksConfig` volgende sectie toe `WadCfg`als onderliggend element van :  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Als het *bestand diagnostics.wadcfgx* ook een andere gootsteen van het type ApplicationInsights bevat, moeten alle drie de volgende instrumentatietoetsen overeenkomen met:  
    > * De sleutel die wordt gebruikt door uw toepassing. 
    > * De sleutel die wordt gebruikt door de ApplicationInsights-gootsteen. 
    > * De sleutel die wordt gebruikt door de ApplicationInsightsProfiler-gootsteen. 
    >
    > U de werkelijke instrumentatiesleutelwaarde vinden `ApplicationInsights` die wordt gebruikt door de gootsteen in de *serviceconfiguratie.\*. cscfg-bestanden.* 
    > Na de Visual Studio 15.5 Azure SDK-release moeten alleen de instrumentatietoetsen die door de toepassing worden gebruikt en de Sink ApplicationInsightsProfiler bij elkaar passen.

1. Implementeer uw service met de nieuwe configuratie Diagnostics en Application Insights Profiler is geconfigureerd om op uw service uit te voeren.
 
## <a name="next-steps"></a>Volgende stappen

* Verkeer genereren naar uw toepassing (start bijvoorbeeld een [beschikbaarheidstest).](monitor-web-app-availability.md) Wacht vervolgens 10 tot 15 minuten voordat traces naar het exemplaar Application Insights worden verzonden.
* Zie [Profielsporen](profiler-overview.md?toc=/azure/azure-monitor/toc.json) in de Azure-portal.
* Zie Problemen met [Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)voor problemen met Profiler .
