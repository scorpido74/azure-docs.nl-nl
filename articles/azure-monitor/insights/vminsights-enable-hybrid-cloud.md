---
title: Azure Monitor inschakelen voor een hybride omgeving | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Monitor voor VM's inschakelt voor een hybride cloudomgeving die een of meer virtuele machines bevat.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480739"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Azure-monitor voor VM's inschakelen voor een hybride omgeving

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u Azure Monitor voor VM's inschakelt voor virtuele machines of fysieke computers die worden gehost in uw datacenter of andere cloudomgeving. Aan het einde van dit proces bent u met succes begonnen met het bewaken van uw virtuele machines in uw omgeving en leert u of ze problemen ondervinden met prestaties of beschikbaarheid.

Voordat u aan de slag gaat, moet u de [vereisten](vminsights-enable-overview.md) bekijken en controleren of uw abonnement en resources aan de vereisten voldoen. Bekijk de vereisten en implementatiemethoden voor de [Agent Log Analytics Linux en Windows.](../../log-analytics/log-analytics-agent-overview.md)

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>De Azure Monitor for VMs Map Dependency-agent verzendt zelf geen gegevens en vereist geen wijzigingen in firewalls of poorten. De kaartgegevens worden altijd door de Log Analytics-agent naar de Azure Monitor-service verzonden, rechtstreeks of via de [Operations Management Suite-gateway](../../azure-monitor/platform/gateway.md) als in uw IT-beveiligingsbeleid geen verbinding kan worden gemaakt met computers in het netwerk.

De stappen om deze taak uit te voeren worden als volgt samengevat:

1. Installeer de Log Analytics-agent voor Windows of Linux. Voordat u de agent installeert, controleert u het overzichtsartikel van de [log-analyse-agent](../platform/log-analytics-agent.md) om inzicht te krijgen in systeemvereisten en implementatiemethoden.

2. Download en installeer de Azure Monitor for VMs Map Dependency agent voor [Windows](https://aka.ms/dependencyagentwindows) of [Linux.](https://aka.ms/dependencyagentlinux)

3. Schakel het verzamelen van prestatiemeteritems in.

4. Azure-monitor voor VM's implementeren.

>[!NOTE]
>De informatie die in dit artikel wordt beschreven voor het implementeren van de afhankelijkheidsagent is ook van toepassing op de [oplossing Servicekaart.](service-map.md)  

## <a name="install-the-dependency-agent-on-windows"></a>De afhankelijkheidsagent op Windows installeren

U de afhankelijkheidsagent handmatig installeren `InstallDependencyAgent-Windows.exe`op Windows-computers door. Als u dit uitvoerbare bestand zonder opties uitvoert, wordt een installatiewizard gestart die u volgen om de agent interactief te installeren.

>[!NOTE]
>*Beheerdersrechten* zijn vereist om de agent te installeren of te verwijderen.

In de volgende tabel worden de parameters weergegeven die worden ondersteund door de installatie voor de agent van de opdrachtregel.

| Parameter | Beschrijving |
|:--|:--|
| /? | Geeft als resultaat een lijst met de opdrachtregelopties. |
| / S | Hiermee voert u een stille installatie uit zonder interactie van de gebruiker. |

Als u bijvoorbeeld het installatieprogramma `/?` met de parameter wilt uitvoeren, voert u **InstallDependencyAgent-Windows.exe /?**.

Bestanden voor de Windows Dependency-agent zijn standaard geïnstalleerd in *C:\Program Files\Microsoft Dependency Agent.* Als de afhankelijkheidsagent niet kan starten nadat de installatie is voltooid, controleert u de logboeken op gedetailleerde foutgegevens. De logboekmap is *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>De afhankelijkheidsagent op Linux installeren

De Dependency-agent is geïnstalleerd op Linux-servers van *InstallDependencyAgent-Linux64.bin,* een shell script met een zelf-extraherende binaire. U het bestand `sh` uitvoeren met behulp van of uitvoermachtigingen toevoegen aan het bestand zelf.

>[!NOTE]
> Toegang tot de hoofdmap is vereist om de agent te installeren of configureren.
>

| Parameter | Beschrijving |
|:--|:--|
| -help | Een lijst met de opdrachtregelopties ophalen. |
| -s | Een installatie op de achtergrond uitvoeren zonder gebruikersvragen. |
| --check | Controleer machtigingen en het besturingssysteem, maar installeer de agent niet. |

Als u bijvoorbeeld het installatieprogramma `-help` met de parameter wilt uitvoeren, voert u **InstallDependencyAgent-Linux64.bin -help in.**

Installeer de Linux-afhankelijkheidsagent als `sh InstallDependencyAgent-Linux64.bin`root door de opdracht uit te voeren.

Als de afhankelijkheidsagent niet wordt gestart, controleert u de logboeken op gedetailleerde foutgegevens. Op Linux-agents is de logmap */var/opt/microsoft/dependency-agent/log.*

Bestanden voor de afhankelijkheidsagent worden in de volgende mappen geplaatst:

| Bestanden | Locatie |
|:--|:--|
| Kernbestanden | /opt/microsoft/dependency-agent |
| Logboekbestanden | /var/opt/microsoft/dependency-agent/log |
| Configuratiebestanden | /etc/opt/microsoft/dependency-agent/config |
| Uitvoerbare bestanden van de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binaire opslagbestanden | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Voorbeelden van installatiescript

Om de afhankelijkheidsagent eenvoudig op veel servers tegelijk te implementeren, wordt het volgende scriptvoorbeeld gegeven om de afhankelijkheidsagent op Windows of Linux te downloaden en te installeren.

### <a name="powershell-script-for-windows"></a>PowerShell-script voor Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Shell-script voor Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Als u de afhankelijkheidsagent wilt implementeren met de gewenste statusconfiguratie (DSC), u de xPSDesiredStateConfiguration-module gebruiken met de volgende voorbeeldcode:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Prestatiemeteritems inschakelen

Als de werkruimte Log Analytics waarnaar naar de oplossing wordt verwezen nog niet is geconfigureerd om de prestatiemeteritems te verzamelen die door de oplossing worden vereist, moet u deze inschakelen. U dit op twee manieren doen:
* Handmatig, zoals beschreven in [Windows- en Linux-prestatiegegevensbronnen in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Door een PowerShell-script te downloaden en uit te voeren dat beschikbaar is in de [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Azure-monitor voor VM's implementeren

Deze methode bevat een JSON-sjabloon die de configuratie opgeeft voor het inschakelen van de oplossingsonderdelen in uw Log Analytics-werkruimte.

Zie als u niet weet hoe u resources implementeert met behulp van een sjabloon:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u de Azure CLI wilt gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.27 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)als u de Azure CLI wilt installeren of upgraden.

### <a name="create-and-execute-a-template"></a>Een sjabloon maken en uitvoeren

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Sla dit bestand op als *installsolutionsforvminsights.json* in een lokale map.

1. De waarden voor *WorkspaceName,* *ResourceGroupName*en *WorkspaceLocation vastleggen*. De waarde voor *WorkspaceName* is de naam van uw Log Analytics-werkruimte. De waarde voor *WorkspaceLocation* is het gebied waarin de werkruimte is gedefinieerd.

1. U deze sjabloon implementeren met de volgende PowerShell-opdracht:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

    ```powershell
    provisioningState       : Succeeded
    ```
   Nadat u bewaking hebt ingeschakeld, kan het ongeveer 10 minuten duren voordat u de status en statistieken voor de hybride computer bekijken.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="vm-doesnt-appear-on-the-map"></a>VM verschijnt niet op de kaart

Als de installatie van uw afhankelijkheidsagent is geslaagd, maar u uw computer niet op de kaart ziet, stelt u het probleem vast door deze stappen te volgen.

1. Is de afhankelijkheidsagent geïnstalleerd? U dit valideren door te controleren of de service is geïnstalleerd en uitgevoerd.

    **Windows:** Zoek naar de service met de naam 'Microsoft Dependency agent'.

    **Linux**: Zoek naar het lopende proces "microsoft-dependency-agent."

2. Bent u op de [gratis prijscategorie van Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Het gratis abonnement biedt maximaal vijf unieke computers. Volgende computers worden niet op de kaart weergegeven, zelfs als de voorgaande vijf geen gegevens meer verzenden.

3. Verzendt de computer logboek- en perf-gegevens naar Azure Monitor-logboeken? Voer de volgende query voor uw computer uit:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Heeft het een of meer resultaten? Zijn de gegevens recent? Als dat het zo is, werkt uw Log Analytics-agent correct en communiceert u met de service. Zo niet, controleer dan de agent op uw server: [Log Analytics-agent voor Windows-probleemoplossing](../platform/agent-windows-troubleshoot.md) of [Log Analytics-agent voor het oplossen van problemen met Linux.](../platform/agent-linux-troubleshoot.md)

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Computer verschijnt op de kaart, maar heeft geen processen

Als u uw server op de kaart ziet, maar geen proces- of verbindingsgegevens heeft, geeft dit aan dat de afhankelijkheidsagent is geïnstalleerd en uitgevoerd, maar dat het kernelstuurprogramma niet is geladen.

Controleer het C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file (Windows) of /var/opt/microsoft/dependency-agent/log/service.log file (Linux). De laatste regels van het bestand moeten aangeven waarom de kernel niet is geladen. De kernel wordt bijvoorbeeld mogelijk niet ondersteund op Linux als u uw kernel hebt bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

Nu bewaking is ingeschakeld voor uw virtuele machines, is deze informatie beschikbaar voor analyse met Azure Monitor voor VM's.

- Zie Azure Monitor for [VM-toewijzing weergeven](vminsights-maps.md)om gedetecteerde toepassingsafhankelijkheden weer te geven.

- Zie [Azure VM-prestaties weergeven](vminsights-performance.md)om knelpunten en algemeen gebruik met de prestaties van uw VM te identificeren.
