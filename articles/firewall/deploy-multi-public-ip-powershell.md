---
title: Azure Firewall met meerdere open bare IP-adressen implementeren met behulp van Power shell
description: In dit artikel leert u hoe u een Azure Firewall met meerdere open bare IP-adressen kunt implementeren met behulp van de Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: 178daba47acaf8ad161a0634c16b046ec330abb6
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864424"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure Firewall met meerdere openbare IP-adressen implementeren met behulp van Azure PowerShell

Deze functie biedt de volgende scenario's:

- **DNAT** : u kunt meerdere exemplaren van de standaard poort naar uw back-endservers omzetten. Als u bijvoorbeeld twee open bare IP-adressen hebt, kunt u TCP-poort 3389 (RDP) voor beide IP-adressen omzetten.
- **SNAT** : er zijn extra poorten beschikbaar voor uitgaande SNAT-verbindingen, waardoor de kans op een SNAT-poort ontstaat. Op dit moment Azure Firewall het open bare IP-adres van de bron wille keurig selecteren dat moet worden gebruikt voor een verbinding. Als u een stroomafwaarts filter op uw netwerk hebt, moet u alle open bare IP-adressen die zijn gekoppeld aan uw firewall toestaan. Overweeg het gebruik van een [openbaar IP-adres voorvoegsel](../virtual-network/public-ip-address-prefix.md) om deze configuratie te vereenvoudigen.
 
Azure Firewall met meerdere open bare IP-adressen is beschikbaar via de Azure Portal, Azure PowerShell, Azure CLI, REST en sjablonen. U kunt een Azure Firewall implementeren met Maxi maal 250 open bare IP-adressen.

De volgende Azure PowerShell-voor beelden ziet u hoe u open bare IP-adressen voor Azure Firewall kunt configureren, toevoegen en verwijderen.

> [!NOTE]
> U kunt de eerste ipConfiguration niet verwijderen van de pagina Azure Firewall open bare IP-adres configuratie. Als u het IP-adres wilt wijzigen, kunt u Azure PowerShell gebruiken.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Een firewall met twee of meer open bare IP-adressen maken

In dit voor beeld wordt een firewall gemaakt die is gekoppeld aan het virtuele netwerk *vnet* met twee open bare IP-adressen.

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

In dit voor beeld is het open bare IP-adres *azFwPublicIp1* gekoppeld aan de firewall.

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

In dit voor beeld wordt het open bare IP-adres *azFwPublicIp1* losgekoppeld van de firewall.

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

* [Snelstartgids: een Azure Firewall met meerdere open bare IP-adressen maken-Resource Manager-sjabloon](quick-create-multiple-ip-template.md)
