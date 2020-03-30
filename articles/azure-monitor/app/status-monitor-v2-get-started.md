---
title: Azure Application Insights Agent - aan de slag | Microsoft Documenten
description: Een quickstart gids voor Application Insights Agent. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7819de1f3dfab7f934421de86c0481d2e063f7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671185"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Aan de slag met Azure Monitor Application Insights Agent voor on-premises servers

Dit artikel bevat de quickstartopdrachten die naar verwachting voor de meeste omgevingen werken.
De instructies zijn afhankelijk van de PowerShell Gallery om updates te distribueren.
Deze opdrachten ondersteunen de `-Proxy` parameter PowerShell.

Zie de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md)voor een uitleg van deze opdrachten, aanpassingsinstructies en informatie over het oplossen van problemen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="download-and-install-via-powershell-gallery"></a>Downloaden en installeren via PowerShell Gallery

### <a name="install-prerequisites"></a>Vereiste onderdelen installeren
Voer PowerShell uit als beheerder.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Sluit PowerShell.

### <a name="install-application-insights-agent"></a>Application Insights Agent installeren
Voer PowerShell uit als beheerder.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Bewaking inschakelen
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Handmatig downloaden en installeren (offline optie)
### <a name="download-the-module"></a>De module downloaden
Download handmatig de nieuwste versie van de module van [PowerShell Gallery.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)

### <a name="unzip-and-install-application-insights-agent"></a>Application Insights Agent uitpakken en installeren
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Bewaking inschakelen
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Volgende stappen

 Uw telemetrie weergeven:

- [Bekijk statistieken](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te controleren.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen te diagnosticeren.
- [Gebruik Analytics](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dashboards maken](../../azure-monitor/app/overview-dashboard.md).

 Meer telemetrie toevoegen:

- [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg telemetrie van webclient toe](../../azure-monitor/app/javascript.md) om uitzonderingen van webpaginacode te bekijken en tracecalls in te schakelen.
- [Voeg de Application Insights SDK toe aan uw code,](../../azure-monitor/app/asp-net.md) zodat u trace- en logoproepen invoegen.

Doe meer met Application Insights Agent:

- Bekijk de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md) voor een uitleg van de commando's hier gevonden.
- Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
