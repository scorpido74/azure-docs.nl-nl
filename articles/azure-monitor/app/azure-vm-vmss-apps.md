---
title: Prestaties bewaken op Azure VM's - Azure Application Insights
description: Toepassingsprestatiebewaking voor azure VM- en Azure-schaalsets voor virtuele machines. Laad- en responstijd in grafieken, afhankelijkheidsgegevens en stel waarschuwingen in op prestaties.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661325"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>De Azure Monitor Application Insights Agent implementeren op azure virtuele machines en Azure virtuele machineschaalsets

Het inschakelen van bewaking op uw .NET-gebaseerde webtoepassingen die worden uitgevoerd op [virtuele Azure-machines](https://azure.microsoft.com/services/virtual-machines/) en [Azure-eenvoudigs- of azure-schaalsets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) voor virtuele machines, is nu eenvoudiger dan ooit. Profiteer van alle voordelen van het gebruik van Application Insights zonder uw code te wijzigen.

In dit artikel u application insights-monitoring inschakelen met behulp van de Application Insights Agent en biedt u voorlopige richtlijnen voor het automatiseren van het proces voor grootschalige implementaties.

> [!IMPORTANT]
> Azure Application Insights Agent voor .NET is momenteel in openbare preview.
> Deze preview-versie wordt geleverd zonder een service-level overeenkomst, en we raden het niet aan voor productie workloads. Sommige functies worden mogelijk niet ondersteund en sommige hebben mogelijk beperkte mogelijkheden.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Er zijn twee manieren om toepassingsbewaking voor virtuele Azure-machines en azure-virtuele machineschaalsets gehoste toepassingen in te schakelen:

* **Codeloos** via Application Insights Agent
    * Deze methode is het gemakkelijkst in te schakelen en er is geen geavanceerde configuratie vereist. Het wordt vaak aangeduid als "runtime" monitoring.

    * Voor Azure virtuele machines en Azure virtuele machine schaalsets raden we aan om dit niveau van monitoring minimaal in te schakelen. Daarna u op basis van uw specifieke scenario beoordelen of handmatige instrumentatie nodig is.

    * De Application Insights Agent verzamelt automatisch dezelfde afhankelijkheidssignalen als de .NET SDK. Zie [Autoverzameling Afhankelijkheid](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) voor meer informatie.
        > [!NOTE]
        > Momenteel worden alleen .Net IIS-gehoste toepassingen ondersteund. Gebruik een SDK om ASP.NET Core-, Java- en Node.js-toepassingen te bewerken die worden gehost op een Azure-virtuele machine en virtuele machineschaalsets.

* **Code-based** via SDK

    * Deze aanpak is veel meer aanpasbaar, maar het vereist [het toevoegen van een afhankelijkheid van de Application Insights SDK NuGet-pakketten.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) Deze methode betekent ook dat u de updates voor de nieuwste versie van de pakketten zelf moet beheren.

    * Als u aangepaste API-aanroepen moet maken om gebeurtenissen/afhankelijkheden bij te houden die niet standaard zijn vastgelegd met agentgebaseerde bewaking, moet u deze methode gebruiken. Bekijk de [API voor aangepaste gebeurtenissen en statistieken artikel](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) voor meer informatie.

> [!NOTE]
> Als zowel agent gebaseerde monitoring en handmatige SDK gebaseerde instrumentatie wordt gedetecteerd alleen de handmatige instrumentatie-instellingen zullen worden gehonoreerd. Dit is om te voorkomen dat dubbele gegevens worden verzonden. Bekijk het [gedeelte probleemoplossing](#troubleshooting) hieronder voor meer informatie over dit gedeelte.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Application Insights Agent voor .NET-toepassingen beheren op Virtuele Azure-machines met PowerShell

> [!NOTE]
> Voordat u de Application Insights Agent installeert, hebt u een verbindingstekenreeks nodig. [Maak een nieuwe Application Insights-bron](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) of kopieer de verbindingstekenreeks vanuit een bestaande bron voor toepassingsinzichten.

> [!NOTE]
> Nieuw bij powershell? Bekijk de [Get Started Guide](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

De Application Insights Agent installeren of bijwerken als extensie voor virtuele Azure-machines
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> U de Application Insights Agent installeren of bijwerken als een extensie op meerdere virtuele machines op schaal met behulp van een Powershell-lus.

Application Insights Agent-extensie verwijderen van de virtuele azure-machine
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Status van querytoepassingsinsightsagent-extensie status voor azure virtuele machine
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Lijst met geïnstalleerde extensies voor virtuele Azure-machine opvragen
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
U ook geïnstalleerde extensies weergeven in het [Azure-virtuele machineblad](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) in de portal.

> [!NOTE]
> Controleer de installatie door te klikken op Live Metrics Stream in de Application Insights Resource die is gekoppeld aan de verbindingstekenreeks die u hebt gebruikt om de Application Insights Agent-extensie te implementeren. Als u gegevens verzendt van meerdere virtuele machines, selecteert u de doelapparaten van Azure virtuele machines onder Servernaam. Het kan tot een minuut duren voordat de gegevens beginnen te stromen.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Application Insights Agent voor .NET-toepassingen beheren op Azure-sets voor virtuele machineschalen met powershell

De Application Insights Agent installeren of bijwerken als extensie voor azure-set voor virtuele machineschaal
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Toepassingscontrole-extensie verwijderen uit azure-schaalsets voor virtuele machines
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Status van querytoepassingsbewaking voor Azure-schaalsets voor virtuele machines
```powershell
# Not supported by extensions framework
```

Lijst met geïnstalleerde extensies voor Azure-eenvoudigmadie
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Problemen oplossen

Tips voor probleemoplossing voor Application Insights Monitoring Agent Extension voor .NET-toepassingen die worden uitgevoerd op virtuele Azure-machines en virtuele machineschaalsets.

> [!NOTE]
> .NET Core-, Java- en Node.js-toepassingen worden alleen ondersteund op virtuele Azure-machines en Azure-sets voor virtuele machines met virtuele machines via handmatige SDK-gebaseerde instrumenten en daarom zijn de onderstaande stappen niet van toepassing op deze scenario's.

De uitvoer van extensieuitvoering wordt vastgelegd in bestanden in de volgende mappen:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [implementeren van een toepassing naar een Azure-set voor virtuele machines](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Webtests beschikbaarheid instellen](monitor-web-app-availability.md) om te worden gewaarschuwd als uw eindpunt is uitgeschakeld.
