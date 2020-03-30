---
title: Diagnostische logboekregistratie voor een netwerkbeveiligingsgroep
titlesuffix: Azure Virtual Network
description: Meer informatie over het inschakelen van diagnostische logboeken voor gebeurtenis- en regeltellervoor een Azure-netwerkbeveiligingsgroep.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 9829e713f19ab9755e9dc79d676446c8048e09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751182"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Diagnostische logboekregistratie voor een netwerkbeveiligingsgroep

Een netwerkbeveiligingsgroep (NSG) bevat regels die verkeer toestaan of weigeren naar een virtueel netwerksubnet, netwerkinterface of beide. Wanneer u diagnostische logboekregistratie inschakelt voor een NSG, u de volgende categorieën informatie registreren:

* **Evenement:** Inzendingen worden geregistreerd waarvoor NSG-regels worden toegepast op VM's, op basis van MAC-adres.
* **Regelteller:** Bevat vermeldingen voor hoe vaak elke NSG-regel wordt toegepast om verkeer te weigeren of toe te staan. De status voor deze regels wordt elke 60 seconden verzameld.

Diagnostische logboeken zijn alleen beschikbaar voor NSG's die zijn geïmplementeerd via het Azure Resource Manager-implementatiemodel. U diagnostische logboekregistratie voor NSG's die via het klassieke implementatiemodel worden geïmplementeerd, niet inschakelen. Zie [Azure-implementatiemodellen begrijpen](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een beter begrip van de twee modellen.

Diagnostische logboekregistratie is afzonderlijk ingeschakeld voor *elke* NSG waarvoor u diagnostische gegevens wilt verzamelen. Zie Azure [activity logging](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)als u geïnteresseerd bent in operationele of activiteitslogboeken.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

U de [Azure Portal,](#azure-portal) [PowerShell](#powershell)of azure [cli](#azure-cli) gebruiken om diagnostische logboekregistratie in te schakelen.

### <a name="azure-portal"></a>Azure-portal

1. Meld u aan bij de [portal](https://portal.azure.com).
2. Selecteer **Alle services**en typ vervolgens *netwerkbeveiligingsgroepen*. Wanneer **netwerkbeveiligingsgroepen** worden weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer de NSG waarvoor u logboekregistratie wilt inschakelen.
4. Selecteer **onder CONTROLE**de optie **Logboeken diagnostische gegevens**en selecteer Diagnostische gegevens **inschakelen,** zoals in de volgende afbeelding wordt weergegeven:

   ![Diagnostische gegevens inschakelen](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Voer **onder Diagnostische instellingen**de volgende gegevens in of selecteer deze en selecteer **Opslaan:**

    | Instelling                                                                                     | Waarde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name                                                                                        | Een naam naar keuze.  Bijvoorbeeld: *myNsgDiagnostics*      |
    | **Archiveren naar een opslagaccount,** **Streamen naar een gebeurtenishub**en **Verzenden naar logboekanalyse** | U zoveel bestemmingen selecteren als u wilt. Zie [Aanmeldingen voor](#log-destinations)meer informatie over elk van deze bestemmingen .                                                                                                                                           |
    | LOG                                                                                         | Selecteer een van beide of beide logboekcategorieën. Zie [Logboekcategorieën](#log-categories)voor meer informatie over de gegevens die voor elke categorie zijn geregistreerd.                                                                                                                                             |
6. Logboeken weergeven en analyseren. Zie [Logboeken weergeven en analyseren](#view-and-analyze-logs)voor meer informatie.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell vanaf uw computer uitvoert, hebt u de Azure PowerShell-module, versie 1.0.0 of hoger, nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet `Connect-AzAccount` u ook uitvoeren om u aan te melden bij Azure met een account dat over de [vereiste machtigingen beschikt.](virtual-network-network-interface.md#permissions)

Om diagnostische logging in te schakelen, hebt u de id van een bestaande NSG nodig. Als u geen bestaande NSG hebt, u er een maken met [New-AzNetworkSecurityGroup.](/powershell/module/az.network/new-aznetworksecuritygroup)

Haal de netwerkbeveiligingsgroep op waarvoor u diagnostische logboekregistratie wilt inschakelen met [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Als u bijvoorbeeld een NSG met de naam *myNsg* wilt ophalen die bestaat in een resourcegroep met de naam *myResourceGroup,* voert u de volgende opdracht in:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

U diagnostische logboeken schrijven naar drie doeltypen. Zie [Aanmeldingen voor](#log-destinations)meer informatie . In dit artikel worden logboeken als voorbeeld naar de *doelvan Log Analytics* verzonden. Haal een bestaande Log Analytics-werkruimte op met [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Als u bijvoorbeeld een bestaande werkruimte met de naam *myWorkspace* wilt ophalen in een resourcegroep met de naam *myWorkspaces,* voert u de volgende opdracht in:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Als u geen bestaande werkruimte hebt, u er een maken met [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Er zijn twee categorieën logboekregistratie waarvoor u logboeken inschakelen. Zie [Logboekcategorieën voor](#log-categories)meer informatie . Diagnostische logboekregistratie inschakelen voor de NSG met [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). In het volgende voorbeeld worden zowel gebeurtenis- als tellercategoriegegevens logboeken aan de werkruimte voor een NSG, met behulp van de i-adressen voor de NSG en werkruimte die u eerder hebt opgehaald:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Als u alleen gegevens wilt registreren voor de ene of `-Categories` de andere categorie, in plaats van beide, voegt u de optie toe aan de vorige opdracht, gevolgd door *NetworkSecurityGroupEvent* of *NetworkSecurityGroupRuleCounter.* Als u zich wilt aanmelden bij een andere [bestemming](#log-destinations) dan een werkruimte log Analytics, gebruikt u de juiste parameters voor een Azure [Storage-account](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [gebeurtenishub.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Logboeken weergeven en analyseren. Zie [Logboeken weergeven en analyseren](#view-and-analyze-logs)voor meer informatie.

### <a name="azure-cli"></a>Azure-CLI

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de Azure CLI vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u de CLI vanaf uw computer uitvoert, hebt u versie 2.0.38 of hoger nodig. Voer `az --version` uit op uw computer om de geïnstalleerde versie te vinden. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest)als u wilt upgraden. Als u de CLI lokaal uitvoert, moet `az login` u ook uitvoeren om u aan te melden bij Azure met een account dat over de [vereiste machtigingen beschikt.](virtual-network-network-interface.md#permissions)

Om diagnostische logging in te schakelen, hebt u de id van een bestaande NSG nodig. Als u geen bestaande NSG hebt, u er een maken met [het AZ-netwerk nsg maken.](/cli/azure/network/nsg#az-network-nsg-create)

Haal de netwerkbeveiligingsgroep op waarvoor u diagnostische logboekregistratie wilt inschakelen met [de NSG-show van het AZ-netwerk.](/cli/azure/network/nsg#az-network-nsg-show) Als u bijvoorbeeld een NSG met de naam *myNsg* wilt ophalen die bestaat in een resourcegroep met de naam *myResourceGroup,* voert u de volgende opdracht in:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

U diagnostische logboeken schrijven naar drie doeltypen. Zie [Aanmeldingen voor](#log-destinations)meer informatie . In dit artikel worden logboeken als voorbeeld naar de *doelvan Log Analytics* verzonden. Zie [Logboekcategorieën voor](#log-categories)meer informatie .

Diagnostische logging inschakelen voor de NSG met [de diagnostische instellingen van az-monitor maken](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). In het volgende voorbeeld worden zowel gebeurtenis- als tellercategoriegegevens logboeken aan een bestaande werkruimte met de naam *myWorkspace*, die bestaat in een resourcegroep met de naam *myWorkspaces*en de ID van de NSG die u eerder hebt opgehaald:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Als u geen bestaande werkruimte hebt, u er een maken met de [Azure-portal](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [PowerShell.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) Er zijn twee categorieën logboekregistratie waarvoor u logboeken inschakelen.

Als u alleen gegevens wilt registreren voor de ene of de andere categorie, verwijdert u de categorie waarvoor u geen gegevens wilt registreren in de vorige opdracht. Als u zich wilt aanmelden bij een andere [bestemming](#log-destinations) dan een werkruimte log Analytics, gebruikt u de juiste parameters voor een Azure [Storage-account](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [gebeurtenishub.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Logboeken weergeven en analyseren. Zie [Logboeken weergeven en analyseren](#view-and-analyze-logs)voor meer informatie.

## <a name="log-destinations"></a>Logbestemmingen

Diagnostische gegevens kunnen zijn:
- [Geschreven naar een Azure Storage-account,](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor controle of handmatige inspectie. U de bewaartijd (in dagen) opgeven met behulp van diagnostische instellingen voor resources.
- [Gestreamd naar een gebeurtenishub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor opname door een service van derden of aangepaste analyseoplossing, zoals PowerBI.
- [Geschreven naar Azure Monitor-logboeken](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-categories"></a>Logboekcategorieën

Json-geformatteerde gegevens zijn geschreven voor de volgende logcategorieën:

### <a name="event"></a>Gebeurtenis

Het gebeurtenislogboek bevat informatie over welke NSG-regels worden toegepast op VM's, op basis van MAC-adres. Voor elke gebeurtenis worden de volgende gegevens geregistreerd. In het volgende voorbeeld worden de gegevens geregistreerd voor een virtuele machine met het IP-adres 192.168.1.4 en een MAC-adres van 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Regelteller

Het regellogboek bevat informatie over elke regel die op resources wordt toegepast. Het volgende voorbeeld gegevens worden geregistreerd elke keer dat een regel wordt toegepast. In het volgende voorbeeld worden de gegevens geregistreerd voor een virtuele machine met het IP-adres 192.168.1.4 en een MAC-adres van 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Het bron-IP-adres voor de communicatie wordt niet geregistreerd. U [nsg-stroomlogboekregistratie](../network-watcher/network-watcher-nsg-flow-logging-portal.md) voor een NSG echter inschakelen, waarbij alle regeltellergegevens worden logboeken en het bron-IP-adres dat de communicatie heeft geïnitieerd. NSG-stroomlogboekgegevens worden naar een Azure Storage-account geschreven. U de gegevens analyseren met de [verkeersanalysemogelijkheden](../network-watcher/traffic-analytics.md) van Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Logboeken weergeven en analyseren

Zie Overzicht van [diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het weergeven van diagnostische logboekgegevens . Als u diagnostische gegevens verzendt naar:
- **Azure Monitor-logboeken**: U de analyseoplossing voor [netwerkbeveiligingen](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) gebruiken voor verbeterde inzichten. De oplossing biedt visualisaties voor NSG-regels die het verkeer per MAC-adres toestaan of weigeren van de netwerkinterface in een virtuele machine.
- **Azure Storage-account**: Gegevens worden naar een PT1H.json-bestand geschreven. U vindt de:
  - Gebeurtenislog in het volgende pad:`insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Regeltellerlog in het volgende pad:`insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [activiteitslogboekregistratie](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), voorheen bekend als audit of operationele logboeken. Activiteitslogboekregistratie is standaard ingeschakeld voor NSG's die zijn gemaakt via een Azure-implementatiemodel. Ga ervoor om te bepalen welke bewerkingen zijn voltooid op NSG's in het activiteitenlogboek, naar vermeldingen die de volgende resourcetypen bevatten:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Zie [NSG-stroomlogboekregistratie](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het registreren van diagnostische gegevens om het bron-IP-adres voor elke stroom op te nemen.
