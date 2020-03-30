---
title: IP-groepen in Azure Firewall
description: Met IP-groepen u IP-adressen groeperen en beheren voor Azure Firewall-regels.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444483"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>IP-groepen (voorbeeld) in Azure Firewall

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Met IP-groepen u ip-adressen voor Azure Firewall-regels op de volgende manieren groeperen en beheren:

- Als bronadres in DNAT-regels
- Als bron- of bestemmingsadres in netwerkregels
- Als bronadres in toepassingsregels


Een IP-groep kan één IP-adres, meerdere IP-adressen of een of meer IP-adresbereiken hebben.

IP-groepen kunnen worden hergebruikt in Azure Firewall DNAT, netwerk- en toepassingsregels voor meerdere firewalls in verschillende regio's en abonnementen in Azure. Groepsnamen moeten uniek zijn. U een IP-groep configureren in de Azure-portal, Azure CLI of REST API. Er wordt een voorbeeldsjabloon verstrekt om u op weg te helpen.

## <a name="sample-format"></a>Voorbeeldnotatie

De volgende voorbeelden van IPv4-adresindelingzijn geldig voor gebruik in IP-groepen:

- Eén adres: 10.0.0.0
- CIDR-notatie: 10.1.0.0/32
- Adresbereik: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Een IP-groep maken

Er kan een IP-groep worden gemaakt met behulp van de Azure-portal, Azure CLI of REST API. Zie [Een IP-groep maken (voorbeeld) voor](create-ip-group.md)meer informatie.

## <a name="browse-ip-groups"></a>Bladeren door IP-groepen
1. Typ **IP-groepen** in de zoekbalk van de Azure-portal en selecteer deze. U de lijst met IP-groepen bekijken of u **Toevoegen** selecteren om een nieuwe IP-groep te maken.
2. Selecteer een IP-groep om de overzichtspagina te openen. U IP-adressen of IP-groepen bewerken, toevoegen of verwijderen.

   ![Overzicht IP-groepen](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Een IP-groep beheren

U alle IP-adressen in de IP-groep en de regels of bronnen die eraan zijn gekoppeld, bekijken. Als u een IP-groep wilt verwijderen, moet u eerst de IP-groep distantiëren van de bron die deze gebruikt.

1. Als u de IP-adressen wilt weergeven of bewerken, selecteert u **IP-adressen** onder **Instellingen** in het linkerdeelvenster.
2. Als u één of meerdere IP-adres(en) wilt toevoegen, selecteert u **IP-adressen toevoegen**. Hiermee wordt de pagina **Slepen of Bladeren** geopend voor een upload of u het adres handmatig invoeren.
3.  Het selecteren van de ellipsen (**...**) aan de rechterkant te bewerken of te verwijderen IP-adressen. Als u meerdere IP-adressen wilt bewerken of verwijderen, selecteert u de vakken en selecteert u **Bewerken** of **Verwijderen** bovenaan.
4. Ten slotte kan u het bestand exporteren in de CSV-bestandsindeling.

> [!NOTE]
> Als u alle IP-adressen in een IP-groep verwijdert terwijl deze nog steeds in een regel wordt gebruikt, wordt die regel overgeslagen.


## <a name="use-an-ip-group"></a>Een IP-groep gebruiken

U NU **IP-groep** selecteren als **brontype** of **doeltype** voor het IP-adres(en) wanneer u Azure Firewall DNAT-, toepassings- of netwerkregels maakt.

> [!NOTE]
> IP-groepen worden niet ondersteund in Firewallbeleid. Het wordt momenteel alleen ondersteund met traditionele firewallregels.

![IP-groepen in Firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Beschikbaarheid in regio’s

IP-groepen zijn momenteel beschikbaar in de volgende regio's:

- VS - west
- VS - west 2
- VS - oost
- VS - oost 2
- VS - centraal
- VS - noord-centraal
- VS - west-centraal
- VS - zuid-centraal
- Canada - midden
- Europa - noord
- Europa -west
- Frankrijk - centraal
- Verenigd Koninkrijk Zuid
- Australië - oost
- Australië - centraal
- Australië - zuidoost

## <a name="related-azure-powershell-cmdlets"></a>Gerelateerde Azure PowerShell-cmdlets

De volgende Azure PowerShell-cmdlets kunnen worden gebruikt om IP-groepen te maken en te beheren:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [AzIP-groep verwijderen](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Nieuwe azfirewallnetwerkregel](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [Nieuwe azfirewalltoepassingsregel](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Nieuw-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure Firewall](tutorial-firewall-deploy-portal.md).