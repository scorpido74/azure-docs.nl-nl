---
title: PowerShell gebruiken om Azure Spot VM's te implementeren
description: Meer informatie over het gebruik van Azure PowerShell om Spot VM's te implementeren om kosten te besparen.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158941"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Voorbeeld: SpotVM's implementeren met Azure PowerShell


Met behulp van [Spot VM's](spot-vms.md) u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kostenbesparing. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt spotvm's van Spot v.s. Daarom zijn Spot VM's ideaal voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/testomgevingen, grote compute workloads en meer.

De prijzen voor spotVM's zijn variabel, gebaseerd op regio en SKU. Zie VM-prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. Zie [Spot VM's - Prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de max.

Je hebt de mogelijkheid om een maximale prijs die u bereid bent te betalen, per uur, voor de VM. De maximale prijs voor een Spot VM kan worden ingesteld in Amerikaanse dollars (USD), met behulp van maximaal 5 decimalen. De waarde `0.98765`is bijvoorbeeld een maximale prijs van $ 0,98765 USD per uur. Als u de maximale `-1`prijs instelt, wordt de VM niet uitgezet op basis van de prijs. De prijs voor de VM zal de huidige prijs voor spot of de prijs voor een standaard VM, die ooit minder is, zolang er capaciteit en quotum beschikbaar is.

> [!IMPORTANT]
> Spotexemplaren staan momenteel in openbare preview.
> Deze preview-versie wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
>



## <a name="create-the-vm"></a>De virtuele machine maken

Maak een spotVM met [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) om de configuratie te maken. Opnemen `-Priority Spot` en `-MaxPrice` instellen op:
- `-1`dus de VM is niet uitgezet op basis van de prijs.
- een dollarbedrag, tot 5 cijfers. Bijvoorbeeld, `-MaxPrice .98765` betekent dat de VM zal worden deallocated zodra de prijs voor een spotVM gaat ongeveer $.98765 per uur.


In dit voorbeeld wordt een spotVM gemaakt die niet wordt toegewezen op basis van prijzen (alleen wanneer Azure de capaciteit terug nodig heeft).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Nadat de VM is gemaakt, u de maximale prijs voor alle VM's in de resourcegroep bekijken.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Volgende stappen

U ook een spot-vm maken met de [Azure CLI](../linux/spot-cli.md) of een [sjabloon.](../linux/spot-template.md)

Zie [Foutcodes](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)als u een fout tegenkomt.