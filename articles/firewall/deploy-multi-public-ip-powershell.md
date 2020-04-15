---
title: Azure Firewall implementeren met meerdere openbare IP-adressen met PowerShell
description: In dit artikel leert u hoe u een Azure Firewall met meerdere openbare IP-adressen implementeert met de Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 21f645e64c9944ed102f538710ea6facc26c7e83
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314037"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure Firewall met meerdere openbare IP-adressen implementeren met behulp van Azure PowerShell

Met deze functie worden de volgende scenario's mogelijk:

- **DNAT** - U meerdere standaardpoortexemplaren vertalen naar uw backendservers. Als u bijvoorbeeld twee openbare IP-adressen hebt, u TCP-poort 3389 (RDP) voor beide IP-adressen vertalen.
- **SNAT** - Er zijn extra poorten beschikbaar voor uitgaande SNAT-verbindingen, waardoor de kans op snat-poortuitputting wordt verminderd. Op dit moment selecteert Azure Firewall willekeurig het openbare IP-adres van de bron dat moet worden gebruikt voor een verbinding. Als u downstreamfiltering op uw netwerk hebt, moet u alle openbare IP-adressen toestaan die aan uw firewall zijn gekoppeld.
 
Azure Firewall met meerdere openbare IP-adressen is beschikbaar via de Azure-portal, Azure PowerShell, Azure CLI, REST en sjablonen. U een Azure Firewall implementeren met maximaal 100 openbare IP-adressen.

In de volgende Voorbeelden van Azure PowerShell ziet u hoe u openbare IP-adressen voor Azure Firewall configureren, toevoegen en verwijderen.

> [!NOTE]
> U de eerste ipConfiguration niet verwijderen van de configuratiepagina van het openbare IP-adres van Azure Firewall. Als u het IP-adres wilt wijzigen, u Azure PowerShell gebruiken.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Een firewall maken met twee of meer openbare IP-adressen

In dit voorbeeld wordt een firewall gemaakt die is gekoppeld aan virtuele *netwerkvnet* met twee openbare IP-adressen.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Een openbaar IP-adres toevoegen aan een bestaande firewall

In dit voorbeeld is het openbare IP-adres *azFwPublicIp1* gekoppeld aan de firewall.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Een openbaar IP-adres verwijderen uit een bestaande firewall

In dit voorbeeld wordt het openbare IP-adres *azFwPublicIp1* losgemaakt van de firewall.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een Azure Firewall maken met meerdere openbare IP-adressen - Sjabloon Resourcebeheer](quick-create-multiple-ip-template.md)
