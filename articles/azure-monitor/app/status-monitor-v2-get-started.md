---
title: 'Azure Status Monitor v2: aan de slag | Microsoft Docs'
description: Een Snelstartgids voor Status Monitor v2. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d9c354edac3cbd3faccaa261654e56f858befdf6
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058235"
---
# <a name="get-started-with-status-monitor-v2"></a>Aan de slag met Status Monitor v2

Dit artikel bevat de Quick Start-opdrachten die naar verwachting werken voor de meeste omgevingen.
De instructies zijn afhankelijk van de PowerShell Gallery voor het distribueren van updates.
Deze opdrachten ondersteunen de Power `-Proxy` shell-para meter.

Raadpleeg de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md)voor een uitleg van deze opdrachten, instructies voor het aanpassen en informatie over het oplossen van problemen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="download-and-install-via-powershell-gallery"></a>Downloaden en installeren via PowerShell Gallery

### <a name="install-prerequisites"></a>Vereiste onderdelen installeren
Voer Power shell uit als beheerder.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Sluit Power shell.

### <a name="install-status-monitor-v2"></a>Status Monitor versie 2 installeren
Voer Power shell uit als beheerder.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Bewaking inschakelen
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Hand matig downloaden en installeren (offline optie)
### <a name="download-the-module"></a>De module downloaden
Down load de nieuwste versie van de module hand matig van [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Unzip en installeer Status Monitor v2
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

- [Bekijk metrische gegevens](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te bewaken.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen op te sporen.
- [Gebruik analyses](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dash boards maken](../../azure-monitor/app/overview-dashboard.md).

 Meer telemetrie toevoegen:

- [Maak](monitor-web-app-availability.md) webtests om ervoor te zorgen dat uw site actief blijft.
- [Voeg](../../azure-monitor/app/javascript.md) de telemetrie van de webclient toe om uitzonde ringen van webpagina code te bekijken en tracerings aanroepen in te scha kelen.
- [Voeg de Application INSIGHTS SDK toe aan uw code](../../azure-monitor/app/asp-net.md) zodat u tracerings-en logboek aanroepen kunt invoegen.

Meer doen met Status Monitor v2:

- Raadpleeg de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md) voor een uitleg van de opdrachten die hier worden weer gegeven.
- Gebruik onze hand leiding voor het [oplossen van problemen](status-monitor-v2-troubleshoot.md) status monitor v2.
