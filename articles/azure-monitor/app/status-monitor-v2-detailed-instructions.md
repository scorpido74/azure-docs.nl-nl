---
title: Gedetailleerde instructies van azure application insights agent | Microsoft Documenten
description: Gedetailleerde instructies om aan de slag te gaan met Application Insights Agent. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8f6134e8f8fdb9af3f578afaf0670c32a3896e01
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766863"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights Agent (voorheen statusmonitor v2 genoemd): gedetailleerde instructies

In dit artikel wordt beschreven hoe u aan boord gaan naar de PowerShell Gallery en de ApplicationMonitor-module downloaden.
Inbegrepen zijn de meest voorkomende parameters die u nodig hebt om aan de slag te gaan.
We hebben ook handmatige downloadinstructies gegeven voor het geval u geen toegang tot internet hebt.

## <a name="get-an-instrumentation-key"></a>Een instrumentatiesleutel

Om te beginnen heb je een instrumentatiesleutel nodig. Zie [Een resource Application Insights maken](create-new-resource.md#copy-the-instrumentation-key)voor meer informatie.

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>PowerShell uitvoeren als beheerder met een verhoogd uitvoeringsbeleid

### <a name="run-as-admin"></a>Uitvoeren als beheerder

PowerShell heeft machtigingen op administratorniveau nodig om wijzigingen aan te brengen in uw computer.
### <a name="execution-policy"></a>Uitvoeringsbeleid
- Beschrijving: Standaard is het uitvoeren van PowerShell-scripts uitgeschakeld. We raden u aan remotesigned-scripts alleen toe te staan voor het huidige bereik.
- Referentie: [Over uitvoeringsbeleid](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) [en set-executionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Opdracht: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Optionele parameter:
    - `-Force`. Omzeilt de bevestigingsprompt.

**Voorbeeldfouten**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Voorwaarden voor PowerShell

Controleer uw exemplaar van PowerShell door de `$PSVersionTable` opdracht uit te voeren.
Met deze opdracht wordt de volgende uitvoer uitgevoerd:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Deze instructies zijn geschreven en getest op een computer met Windows 10 en de hierboven genoemde versies.

## <a name="prerequisites-for-powershell-gallery"></a>Voorwaarden voor PowerShell Gallery

Met deze stappen bereidt u uw server voor om modules van PowerShell Gallery te downloaden.

> [!NOTE] 
> PowerShell Gallery wordt ondersteund op Windows 10, Windows Server 2016 en PowerShell 6.
> Zie [PowerShellGet installeren](/powershell/scripting/gallery/installing-psget)voor informatie over eerdere versies.


1. Voer PowerShell uit als beheerder met een verhoogd uitvoeringsbeleid.
2. Installeer de NuGet-pakketprovider.
    - Beschrijving: Je hebt deze provider nodig om te communiceren met NuGet-gebaseerde repositories zoals PowerShell Gallery.
    - Referentie: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Opdracht: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Optionele parameters:
        - `-Proxy`. Hiermee geeft u een proxyserver op voor de aanvraag.
        - `-Force`. Omzeilt de bevestigingsprompt.
    
    Je ontvangt deze prompt als NuGet niet is ingesteld:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configureer PowerShell Gallery als een vertrouwde opslagplaats.
    - Beschrijving: PowerShell Gallery is standaard een niet-vertrouwde opslagplaats.
    - Referentie: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Opdracht: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Optionele parameter:
        - `-Proxy`. Hiermee geeft u een proxyserver op voor de aanvraag.

    U ontvangt deze prompt als PowerShell Gallery niet wordt vertrouwd:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    U deze wijziging bevestigen en alle `Get-PSRepository` PSRepositories controleren door de opdracht uit te voeren.

4. Installeer de nieuwste versie van PowerShellGet.
    - Beschrijving: Deze module bevat de tooling die wordt gebruikt om andere modules uit PowerShell Gallery te halen. Versie 1.0.0.1 wordt geleverd met Windows 10 en Windows Server. Versie 1.6.0 of hoger is vereist. Voer de `Get-Command -Module PowerShellGet` opdracht uit om te bepalen welke versie is geïnstalleerd.
    - Referentie: [PowerShellGet installeren](/powershell/scripting/gallery/installing-psget).
    - Opdracht: `Install-Module -Name PowerShellGet`.
    - Optionele parameters:
        - `-Proxy`. Hiermee geeft u een proxyserver op voor de aanvraag.
        - `-Force`. Omzeilt de waarschuwing "al geïnstalleerd" en installeert de nieuwste versie.

    U ontvangt deze fout als u de nieuwste versie van PowerShellGet niet gebruikt:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Start PowerShell opnieuw op. U de nieuwe versie niet laden in de huidige sessie. Nieuwe PowerShell-sessies laden de nieuwste versie van PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>De module downloaden en installeren via PowerShell Gallery

Deze stappen downloaden de Az.ApplicationMonitor-module van PowerShell Gallery.

1. Zorg ervoor dat aan alle voorwaarden voor PowerShell Gallery is voldaan.
2. Voer PowerShell uit als beheerder met een verhoogd uitvoeringsbeleid.
3. Installeer de az.applicationmonitor-module.
    - Referentie: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Opdracht: `Install-Module -Name Az.ApplicationMonitor`.
    - Optionele parameters:
        - `-Proxy`. Hiermee geeft u een proxyserver op voor de aanvraag.
        - `-AllowPrerelease`. Hiermee u alfa- en bètaversies installeren.
        - `-AcceptLicense`. Omzeilt de prompt 'Licentie accepteren'
        - `-Force`. Omzeilt de waarschuwing 'Niet-vertrouwde opslagplaats'.

## <a name="download-and-install-the-module-manually-offline-option"></a>De module handmatig downloaden en installeren (offline optie)

Als u om welke reden dan ook geen verbinding maken met de PowerShell-module, u de Az.ApplicationMonitor-module handmatig downloaden en installeren.

### <a name="manually-download-the-latest-nupkg-file"></a>Download het nieuwste nupkg-bestand handmatig

1. Ga naar https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selecteer de nieuwste versie van het bestand in de tabel **Versiegeschiedenis.**
3. Selecteer **onder Installatieopties**de optie **Handmatig downloaden**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Optie 1: installeren in een Map PowerShell-modules
Installeer de handmatig gedownloade PowerShell-module in een PowerShell-map, zodat deze kan worden ontdekt door PowerShell-sessies.
Zie [Een PowerShell-module installeren voor](/powershell/scripting/developer/module/installing-a-powershell-module)meer informatie.


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Nupkg uitritsen als zip-bestand met Uitvouwarchief (v1.0.1.0)

- Beschrijving: De basisversie van Microsoft.PowerShell.Archive (v1.0.1.0) kan nupkg-bestanden niet uitpakken. Wijzig de naam van het bestand met de .zip-extensie.
- Referentie: [Uitvouwen-archief](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Opdracht:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Nupkg uitritsen met Uitbreidarchief (v1.1.0.0)

- Beschrijving: Gebruik een huidige versie van Expand-Archive om nupkg-bestanden uit te pakken zonder de extensie te wijzigen.
- Naslaginformatie: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) en [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Opdracht:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Optie 2: Nupkg handmatig uitpakken en importeren
Installeer de handmatig gedownloade PowerShell-module in een PowerShell-map, zodat deze kan worden ontdekt door PowerShell-sessies.
Zie [Een PowerShell-module installeren voor](/powershell/scripting/developer/module/installing-a-powershell-module)meer informatie.

Als u de module in een andere map installeert, importeert u de module handmatig met [importmodule.](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> DLL's worden geïnstalleerd via relatieve paden.
> Sla de inhoud van het pakket op in uw geplande runtime-map en bevestig dat toegangsmachtigingen lezen toestaan, maar niet schrijven.

1. Wijzig de extensie in "zip" en haal de inhoud van het pakket uit in uw beoogde installatiemap.
2. Zoek het bestandspad van Az.ApplicationMonitor.psd1.
3. Voer PowerShell uit als beheerder met een verhoogd uitvoeringsbeleid.
4. Laad de module `Import-Module Az.ApplicationMonitor.psd1` met behulp van de opdracht.
    

## <a name="route-traffic-through-a-proxy"></a>Routeverkeer via een proxy

Wanneer u een computer op uw privéintranet controleert, moet u HTTP-verkeer door een proxy routeren.

De PowerShell-opdrachten voor het downloaden en installeren van Az.ApplicationMonitor vanuit de PowerShell Gallery ondersteunen een `-Proxy` parameter.
Bekijk de voorgaande instructies wanneer u uw installatiescripts schrijft.

De Application Insights SDK moet de telemetrie van uw app naar Microsoft verzenden. We raden u aan proxy-instellingen voor uw app te configureren in uw web.config-bestand. Zie [VEELgestelde vragen over toepassingsinzichten: proxy-passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)voor meer informatie.


## <a name="enable-monitoring"></a>Bewaking inschakelen

Gebruik `Enable-ApplicationInsightsMonitoring` de opdracht om bewaking in te schakelen.

Zie de [API-referentie](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.



## <a name="next-steps"></a>Volgende stappen

 Uw telemetrie weergeven:

- [Bekijk statistieken](../../azure-monitor/platform/metrics-charts.md) om de prestaties en het gebruik te controleren.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen te diagnosticeren.
- [Gebruik Analytics](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dashboards maken](../../azure-monitor/app/overview-dashboard.md).

 Meer telemetrie toevoegen:

- [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg telemetrie van webclient toe](../../azure-monitor/app/javascript.md) om uitzonderingen van webpaginacode te bekijken en tracecalls in te schakelen.
- [Voeg de Application Insights SDK toe aan uw code,](../../azure-monitor/app/asp-net.md) zodat u trace- en logoproepen invoegen.

Doe meer met Application Insights Agent:

- Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
