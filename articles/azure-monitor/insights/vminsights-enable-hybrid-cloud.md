---
title: Azure Monitor inschakelen voor een hybride omgeving | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor VM's inschakelt voor een hybride cloud omgeving die een of meer virtuele machines bevat.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 013515e0608bf790ceef8dc13d9d547496306610
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092844"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Azure Monitor voor VM's inschakelen voor een hybride omgeving

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u Azure Monitor voor VM's inschakelt voor virtuele machines of fysieke computers die worden gehost in uw Data Center of een andere cloud omgeving. Aan het einde van dit proces hebt u uw virtuele machines in uw omgeving kunnen bewaken en leert u of er problemen zijn met de prestaties of de beschik baarheid.

Voordat u aan de slag gaat, moet u de [vereisten](vminsights-enable-overview.md) controleren en controleren of uw abonnement en resources voldoen aan de vereisten. Bekijk de vereisten en implementatie methoden voor de [log Analytics Linux en Windows-agent](../platform/log-analytics-agent.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>De Azure Monitor voor VM's kaart afhankelijke agent verzendt geen gegevens zelf, en vereist geen wijzigingen in firewalls of poorten. De kaart gegevens worden altijd door de Log Analytics agent verzonden naar de Azure Monitor-service, hetzij rechtstreeks of via de [Operations Management Suite-gateway](../../azure-monitor/platform/gateway.md) als uw IT-beveiligings beleid niet toestaat dat computers in het netwerk verbinding maken met internet.

De stappen om deze taak te volt ooien, zijn als volgt samenvatten:

1. Installeer Log Analytics-agent voor Windows of Linux. Voordat u de Agent installeert, raadpleegt u het [overzichts artikel log Analytics agent](../platform/log-analytics-agent.md) om inzicht te krijgen in de systeem vereisten en implementatie methoden.

2. Down load en installeer de Azure Monitor voor VM's kaart dependency agent voor [Windows](https://aka.ms/dependencyagentwindows) of [Linux](https://aka.ms/dependencyagentlinux).

3. Hiermee schakelt u het verzamelen van prestatie meter items in.

4. Azure Monitor voor VM's implementeren.

>[!NOTE]
>De informatie die in dit artikel wordt beschreven voor het implementeren van de afhankelijkheids agent, is ook van toepassing op de [servicetoewijzing oplossing](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>De afhankelijkheids agent installeren in Windows

U kunt de afhankelijkheids agent hand matig installeren op Windows-computers door uit te voeren `InstallDependencyAgent-Windows.exe` . Als u dit uitvoer bare bestand zonder opties uitvoert, wordt een installatie wizard gestart die u kunt volgen om de agent interactief te installeren.

>[!NOTE]
>Er zijn *beheerders* bevoegdheden vereist om de agent te installeren of verwijderen.

De volgende tabel geeft een overzicht van de para meters die worden ondersteund door de installatie voor de agent vanaf de opdracht regel.

| Parameter | Beschrijving |
|:--|:--|
| /? | Retourneert een lijst met de opdracht regel opties. |
| / S | Voert een stille installatie zonder tussen komst van de gebruiker uit. |

`/?`Voer bijvoorbeeld **InstallDependencyAgent-Windows.exe/?** in om het installatie programma uit te voeren met de para meter.

Bestanden voor de Windows-afhankelijkheids agent worden standaard in *C:\Program Files\Microsoft dependency agent* geïnstalleerd. Als de afhankelijkheids agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *%ProgramFiles%\Microsoft dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>De afhankelijkheids agent installeren in Linux

De afhankelijkheids agent is geïnstalleerd op Linux-servers vanuit *InstallDependencyAgent-Linux64. bin*, een shell script met een zelfuitpakkend binair bestand. U kunt het bestand uitvoeren met `sh` of de machtiging voor uitvoeren toevoegen aan het bestand zelf.

>[!NOTE]
> Toegang tot de hoofdmap is vereist om de agent te installeren of configureren.
>

| Parameter | Beschrijving |
|:--|:--|
| -Help | Een lijst met de opdrachtregelopties ophalen. |
| -s | Een installatie op de achtergrond uitvoeren zonder gebruikersvragen. |
| --Controleer | Controleer de machtigingen en het besturings systeem, maar installeer de agent niet. |

`-help`Voer bijvoorbeeld **InstallDependencyAgent-Linux64. bin-Help**in om het installatie programma uit te voeren met de para meter.

Installeer de Linux-afhankelijkheids agent als root door de opdracht uit te voeren `sh InstallDependencyAgent-Linux64.bin` .

Als de afhankelijkheids agent niet kan worden gestart, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. Op Linux-agents is de logboekmap */var/opt/Microsoft/dependency-agent/log*.

Bestanden voor de afhankelijkheids agent worden in de volgende directory's geplaatst:

| Files | Locatie |
|:--|:--|
| Kernbestanden | /opt/microsoft/dependency-agent |
| Logboekbestanden | /var/opt/microsoft/dependency-agent/log |
| Configuratiebestanden | /etc/opt/microsoft/dependency-agent/config |
| Uitvoerbare bestanden van de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binaire opslagbestanden | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Voorbeelden van installatiescript

Als u de afhankelijkheids agent eenvoudig op meerdere servers tegelijk wilt implementeren, wordt het volgende script voorbeeld weer gegeven om de afhankelijkheids agent te downloaden en te installeren op Windows of Linux.

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

Als u de afhankelijkheids agent wilt implementeren met behulp van desired state Configuration (DSC), kunt u de xPSDesiredStateConfiguration-module gebruiken met de volgende voorbeeld code:

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

## <a name="enable-performance-counters"></a>Prestatie meter items inschakelen

Als de Log Analytics werk ruimte waarnaar wordt verwezen door de oplossing nog niet is geconfigureerd voor het verzamelen van de prestatie meter items die vereist zijn voor de oplossing, moet u deze inschakelen. U kunt dit op een van de volgende twee manieren doen:
* Hand matig, zoals beschreven in [Windows-en Linux-prestatie gegevens bronnen in log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Een Power shell-script downloaden en uitvoeren dat beschikbaar is via de [Azure PowerShell galerie](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Azure Monitor voor VM's implementeren

Deze methode bevat een JSON-sjabloon waarmee de configuratie wordt opgegeven voor het inschakelen van de oplossings onderdelen in uw Log Analytics-werk ruimte.

Als u niet weet hoe u resources kunt implementeren met behulp van een sjabloon, raadpleegt u:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u Azure CLI wilt gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.27 of hoger uitvoeren. Voer uit om uw versie te identificeren `az --version` . Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

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

1. Sla dit bestand als *installsolutionsforvminsights.jsop in* een lokale map.

1. Leg de waarden vast voor de *werkruimte*, *ResourceGroupName*en *WorkspaceLocation*. De waarde voor *workspacenaam* is de naam van uw log Analytics-werk ruimte. De waarde voor *WorkspaceLocation* is de regio waarin de werk ruimte is gedefinieerd.

1. U bent klaar om deze sjabloon te implementeren met behulp van de volgende Power shell-opdracht:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Het kan een paar minuten duren voordat de configuratie wijziging is voltooid. Wanneer het is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

    ```powershell
    provisioningState       : Succeeded
    ```
   Nadat u bewaking hebt ingeschakeld, kan het ongeveer 10 minuten duren voordat u de status en metrische gegevens voor de hybride computer kunt weer geven.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="vm-doesnt-appear-on-the-map"></a>De virtuele machine wordt niet weer gegeven op de kaart

Als de installatie van de afhankelijkheids agent is voltooid, maar u de computer niet ziet op de kaart, kunt u de oorzaak van het probleem vaststellen door de volgende stappen uit te voeren.

1. Is de afhankelijkheidsagent correct geïnstalleerd? U kunt dit valideren door te controleren of de service is geïnstalleerd en actief is.

    **Windows**: zoek naar de service met de naam ' micro soft dependency agent '.

    **Linux**: zoek naar het actieve proces ' micro soft-dependency-agent '.

2. Bevindt u zich op de [prijs categorie gratis van log Analytics](./solutions.md)? Het gratis abonnement staat Maxi maal vijf unieke computers toe. Eventuele volgende computers worden niet weer gegeven op de kaart, zelfs als de voor gaande vijf geen gegevens meer verzenden.

3. Stuurt de computer logboek-en prestatie gegevens naar Azure Monitor logboeken? Voer de volgende query uit voor uw computer:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Zijn er een of meer resultaten geretourneerd? Zijn de gegevens recent? Als dit het geval is, wordt uw Log Analytics-agent correct uitgevoerd en communiceert deze met de service. Als dat niet het geval is, controleert u de agent op uw server: [log Analytics-agent voor Windows-probleem oplossing](../platform/agent-windows-troubleshoot.md) of [log Analytics agent voor Linux-probleem oplossing](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>De computer wordt weer gegeven op de kaart, maar heeft geen processen.

Als uw server op de kaart wordt weer gegeven, maar er geen proces-of verbindings gegevens zijn, geeft dit aan dat de afhankelijkheids agent is geïnstalleerd en wordt uitgevoerd, maar het kernel-stuur programma is niet geladen.

Controleer het bestand C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) of het bestand /var/opt/microsoft/dependency-agent/log/service.log (Linux). De laatste regels van het bestand zouden moeten aangeven waarom de kernel niet is geladen. Het is bijvoorbeeld mogelijk dat de kernel niet wordt ondersteund op Linux als u uw kernel hebt bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

Nu de bewaking voor uw virtuele machines is ingeschakeld, is deze informatie beschikbaar voor analyse met Azure Monitor voor VM's.

- Zie [Azure monitor voor VM's kaart weer geven](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven.

- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten en het algehele gebruik van de VM-prestaties te identificeren.
