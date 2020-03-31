---
title: Web-apps profileren op een Azure VM - Profiler voor toepassingsinzichten
description: Beprofiel web-apps op een Azure VM met behulp van Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671576"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profielwebapps die worden uitgevoerd op een virtuele Azure-machine of een virtuele machineschaal die is ingesteld met Behulp van Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U Azure Application Insights Profiler ook implementeren op deze services:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Profiler implementeren op een virtuele machine of een virtuele machineschaalset
In dit artikel ziet u hoe u Application Insights Profiler uitvoeren op uw virtuele Azure-machine (VM) of Azure-schaalset voor virtuele machines. Profiler is geïnstalleerd met de Azure Diagnostics-extensie voor VM's. Configureer de extensie om Profiler uit te voeren en bouw de Application Insights SDK in uw toepassing.

1. Voeg de Application Insights SDK toe aan uw [ASP.NET toepassing.](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)

   Als u profielen voor uw aanvragen wilt weergeven, moet u aanvraagtelemetrie naar Application Insights verzenden.

1. Installeer azure diagnostics-extensie op uw vm. Zie voor volledige voorbeelden van resourcebeheersjablonen:  
   * [Virtuele machine](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Schaalset voor virtuele machines](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Het belangrijkste onderdeel is de ApplicationInsightsProfilerSink in het WadCfg. Als u Azure Diagnostics wilt inschakelen dat Profiler gegevens naar uw iKey kan verzenden, voegt u een andere sink toe aan deze sectie.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Implementeer de gewijzigde definitie van implementatie van de omgeving.  

   Het toepassen van de wijzigingen omvat meestal een volledige sjabloonimplementatie of een publicatie op basis van een cloudservice via PowerShell-cmdlets of Visual Studio.  

   De volgende PowerShell-opdrachten zijn een alternatieve benadering voor bestaande virtuele machines die alleen de Azure Diagnostics-extensie raken. Voeg de eerder genoemde ProfilerSink toe aan de config die wordt geretourneerd door de opdracht Get-AzVMDiagnosticsExtension. Geef de bijgewerkte config vervolgens door aan de opdracht Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Als de beoogde toepassing via [IIS](https://www.microsoft.com/web/downloads/platform.aspx) `IIS Http Tracing` wordt uitgevoerd, schakelt u de Windows-functie in.

   a. Stel externe toegang tot de omgeving in en gebruik vervolgens het venster [Windows-functies toevoegen.]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) Of voer de volgende opdracht uit in PowerShell (als beheerder):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Als het instellen van externe toegang een probleem is, u de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) gebruiken om de volgende opdracht uit te voeren:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implementeer uw toepassing.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Profiler Sink instellen met Azure Resource Explorer
We hebben nog geen manier om de Application Insights Profiler-gootsteen van het portaal in te stellen. In plaats van powershell zoals hierboven beschreven te gebruiken, u Azure Resource Explorer gebruiken om de gootsteen in te stellen. Maar let op, als u de VM opnieuw implementeert, gaat de gootsteen verloren. U moet de config bijwerken die u gebruikt bij het implementeren van de VM om deze instelling te behouden.

1. Controleer of de Windows Azure Diagnostics-extensie is geïnstalleerd door de extensies te bekijken die voor uw virtuele machine zijn geïnstalleerd.  

    ![Controleren of de WAD-extensie is geïnstalleerd][wadextension]

2. Zoek de VM Diagnostics-extensie voor uw vm. Ga [https://resources.azure.com](https://resources.azure.com)naar. Vouw uw brongroep, Microsoft.Compute virtualMachines, de naam van virtuele machines en extensies uit.  

    ![Navigeren naar WAD-config in Azure Resource Explorer][azureresourceexplorer]

3. Voeg de Application Insights Profiler-gootsteen toe aan het SinksConfig-knooppunt onder WadCfg. Als u nog geen SinksConfig-sectie hebt, moet u er mogelijk een toevoegen. Zorg ervoor dat u de juiste Application Insights iKey in uw instellingen opgeeft. Je moet de explorers-modus in de rechterbovenhoek lezen/schrijven en druk op de blauwe knop 'Bewerken'.

    ![Add Application Insights Profiler Sink][resourceexplorersinksconfig]

4. Als je klaar bent met het bewerken van de config, druk je op 'Put'. Als de put succesvol is, verschijnt er een groene controle in het midden van het scherm.

    ![Verzend putverzoek om wijzigingen toe te passen][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Kan Profiler draaien op on-premises servers?
We hebben geen plan om Application Insights Profiler te ondersteunen voor on-premises servers.

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren naar uw toepassing (start bijvoorbeeld een [beschikbaarheidstest).](monitor-web-app-availability.md) Wacht vervolgens 10 tot 15 minuten voordat traces naar het exemplaar Application Insights worden verzonden.
- Zie [Profielsporen](profiler-overview.md?toc=/azure/azure-monitor/toc.json) in de Azure-portal.
- Zie [Profiler troubleshooting](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)voor hulp bij het oplossen van problemen met Profiler.

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
