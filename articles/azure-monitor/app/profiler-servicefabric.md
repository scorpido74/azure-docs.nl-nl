---
title: Live Azure Service Fabric-apps profileren met Application Insights
description: Profiler inschakelen voor een Service Fabric-toepassing
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671610"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Live Azure Service Fabric-toepassingen profileren met Application Insights

U Application Insights Profiler ook implementeren op deze services:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtuele machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>De definitie van de omgevingimplementatie instellen

Application Insights Profiler is opgenomen in Azure Diagnostics. U de Azure Diagnostics-extensie installeren met behulp van een Azure Resource Manager-sjabloon voor uw Cluster ServiceFabric. Download een [sjabloon waarmee Azure Diagnostics wordt geïnstalleerd op een cluster van servicestructuur.](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Als u uw omgeving wilt instellen, gaat u de volgende acties uitvoeren:

1. Profiler ondersteunt .NET Framework en .Net Core. Als u .NET Framework gebruikt, controleert u of u [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) of hoger gebruikt. Het is voldoende om te bevestigen `Windows Server 2012 R2` dat het geïmplementeerde besturingssysteem of hoger is. Profiler ondersteunt .NET Core 2.1 en nieuwere toepassingen.

1. Zoek naar de [Azure Diagnostics-extensie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) in het implementatiesjabloonbestand.

1. Voeg de `SinksConfig` volgende sectie toe `WadCfg`als een onderliggend element van . Vervang `ApplicationInsightsProfiler` de eigenschapswaarde door uw eigen instrumentatietoets Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Zie [Controle en diagnose gebruiken met een Windows VM- en Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor informatie over het toevoegen van de extensie Diagnostische gegevens aan uw implementatiesjabloon.

1. Implementeer uw cluster Servicefabric met behulp van de sjabloon Azure Resource Manager.  
  Als uw instellingen juist zijn, wordt Application Insights Profiler geïnstalleerd en ingeschakeld wanneer de Azure Diagnostics-extensie is geïnstalleerd. 

1. Toepassingsinzichten toevoegen aan uw Service Fabric-toepassing.  
  Als Profiler profielen voor uw aanvragen kan verzamelen, moet uw toepassing bewerkingen bijhouden met Application Insights. Voor stateloze API's u verwijzen naar instructies voor [het bijhouden van aanvragen voor profilering.](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json) Zie [aangepaste bewerkingen bijhouden met Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)voor meer informatie over het bijhouden van aangepaste bewerkingen in andere soorten apps.

1. Uw toepassing opnieuw implementeren.


## <a name="next-steps"></a>Volgende stappen

* Verkeer genereren naar uw toepassing (start bijvoorbeeld een [beschikbaarheidstest).](monitor-web-app-availability.md) Wacht vervolgens 10 tot 15 minuten voordat traces naar het exemplaar Application Insights worden verzonden.
* Zie [Profielsporen](profiler-overview.md?toc=/azure/azure-monitor/toc.json) in de Azure-portal.
* Zie [Profiler troubleshooting](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)voor hulp bij het oplossen van problemen met Profiler.
