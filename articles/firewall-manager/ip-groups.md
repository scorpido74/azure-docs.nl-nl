---
title: IP-groepen in het Azure Firewall-beleid
description: Met IP-groepen kunt u IP-adressen voor Azure Firewall regels groeperen en beheren.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 20372d9584becf18c65e13c81b99403ce88d27e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568534"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>IP-groepen in het Azure Firewall-beleid

Met IP-groepen kunt u IP-adressen voor Azure Firewall beleid op de volgende manieren groeperen en beheren:

- Als bron type in DNAT-regels
- Als bron-of doel type in netwerk regels
- Als bron type in toepassings regels


Een IP-groep kan één IP-adres, meerdere IP-adressen of een of meer IP-adresbereiken hebben.

IP-groepen kunnen opnieuw worden gebruikt in Azure Firewall DNAT-, netwerk-en toepassings regels voor meerdere firewalls voor verschillende regio's en abonnementen in Azure. Groeps namen moeten uniek zijn. U kunt een IP-groep configureren in de Azure Portal, Azure CLI of REST API. Er wordt een voorbeeld sjabloon gegeven om u te helpen aan de slag te gaan.

## <a name="sample-format"></a>Voorbeeld indeling

De volgende IPv4-adres notatie voorbeelden zijn geldig voor gebruik in IP-groepen:

- Eén adres: 10.0.0.0
- CIDR-notatie: 10.1.0.0/32
- Adres bereik: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Een IP-groep maken

Een IP-groep kan worden gemaakt met behulp van de Azure Portal, Azure CLI of REST API. Zie [een IP-groep maken](../firewall/create-ip-group.md)voor meer informatie.

## <a name="browse-ip-groups"></a>Bladeren door IP-groepen
1. In de Azure Portal zoek balk typt u **IP-groepen** en selecteert u deze. U kunt de lijst met IP-groepen zien of u kunt **toevoegen** selecteren om een nieuwe IP-groep te maken.
2. Selecteer een IP-groep om de pagina overzicht te openen. U kunt IP-adressen of IP-groepen bewerken, toevoegen of verwijderen.

   ![Overzicht van IP-groepen](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Een IP-groep beheren

U kunt alle IP-adressen in de IP-groep en de bijbehorende regels of bronnen weer geven. Als u een IP-groep wilt verwijderen, moet u eerst de IP-groep ontkoppelen van de resource die deze gebruikt.

1. Als u de IP-adressen wilt weer geven of bewerken, selecteert u **IP-adressen** onder **instellingen** in het linkerdeel venster.
2. Als u één of meerdere IP-adressen wilt toevoegen, selecteert u **IP-adressen toevoegen**. Hiermee opent u de pagina **slepen of bladeren** voor een upload, of u kunt het adres hand matig invoeren.
3.    Selecteer de beletsel tekens (**...**) aan de rechter kant om IP-adressen te bewerken of te verwijderen. Als u meerdere IP-adressen wilt bewerken of verwijderen, selecteert u de vakken en selecteert u bovenaan **bewerken** of **verwijderen** .
4. Ten slotte kan het bestand in de CSV-bestands indeling worden geëxporteerd.

> [!NOTE]
> Als u alle IP-adressen in een IP-groep verwijdert terwijl deze nog steeds in een regel wordt gebruikt, wordt die regel overgeslagen.


## <a name="use-an-ip-group"></a>Een IP-groep gebruiken

U kunt nu **IP-groep** selecteren als **bron type** of **doel type** voor IP-adres (sen) wanneer u een beleid maakt met DNAT-, toepassings-of netwerk regels.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="IP-groepen in de firewall":::

## <a name="ip-address-limits"></a>IP-adres limieten

Voor 50 IP-groepen of minder kunt u elk per firewall exemplaar een maximum van 5000 afzonderlijke IP-adressen hebben. Voor 51 tot 100 IP-groepen kunt u elk per firewall-exemplaar beschikken over 500 afzonderlijke IP-adressen.

### <a name="examples"></a>Voorbeelden

#### <a name="example-1-supported"></a>Voor beeld 1: ondersteund

|IP-groepen  |Aantal IP-adressen  |Notatie  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Firewallregel1|
|IPGroup2     |3|196.0.0.0 - 196.0.0.2|Firewallregel1|
|IPGroup3     |1|1.2.3.4|Firewallregel1|
|     |**Totaal 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>Voor beeld 2: ondersteund

|IP-groepen  |Aantal IP-adressen  |Notatie  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Firewallregel1|
|IPGroup2     |4096|11.0.0.0/20|Firewallregel1|
|     |**Totaal 8192**|         |         |

#### <a name="example-3-not-supported"></a>Voor beeld 3: niet ondersteund

|IP-groepen  |Aantal IP-adressen  |Notatie  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |8192     |10.0.0.0/20, 11.0.0.0/20  |Firewallregel1|
|     |**Totaal 8192**|||

#### <a name="example-4-supported"></a>Voor beeld 4: ondersteund

|IP-groepen  |Aantal IP-adressen  |Notatie  |Regel  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Firewallregel1|
|IPGroup2     |4096|11.0.0.0/20|Firewallregel2|
|     |**Totaal 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>Gerelateerde Azure PowerShell-cmdlets

De volgende Azure PowerShell-cmdlets kunnen worden gebruikt voor het maken en beheren van IP-groepen:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallPolicyNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicynetworkrule?view=azps-3.4.0)
- [New-AzFirewallPolicyApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicyapplicationrule?view=azps-3.4.0)
- [New-AzFirewallPolicyNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicynatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: uw virtuele WAN beveiligen met Azure Firewall Manager](secure-cloud-network.md)