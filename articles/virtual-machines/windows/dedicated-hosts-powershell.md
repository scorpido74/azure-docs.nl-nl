---
title: Azure dedicated hosts implementeren met behulp van de Azure PowerShell
description: Implementeer VM's voor speciale hosts met Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082846"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>VM's implementeren voor speciale hosts met de Azure PowerShell

In dit artikel u een [azure-dedicated host](dedicated-hosts.md) maken om uw virtuele machines (VM's) te hosten. 

Zorg ervoor dat u Azure PowerShell-versie 2.8.0 of hoger hebt geïnstalleerd `Connect-AzAccount`en dat u bent aangemeld bij een Azure-account bij . 

## <a name="limitations"></a>Beperkingen

- Virtuele machineschaalsets worden momenteel niet ondersteund op speciale hosts.
- De grootte en hardwaretypen die beschikbaar zijn voor toegewijde hosts verschillen per regio. Raadpleeg de [pagina hostprijzen](https://aka.ms/ADHPricing) voor meer informatie.

## <a name="create-a-host-group"></a>Een hostgroep maken

Een **hostgroep** is een bron die een verzameling toegewijde hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheidszone en voegt er hosts aan toe. Bij het plannen voor hoge beschikbaarheid zijn er extra opties. U een of beide van de volgende opties gebruiken met uw speciale hosts: 
- Overspannen in meerdere beschikbaarheidszones. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Overspannen meerdere foutdomeinen die zijn toegewezen aan fysieke racks. 
 
In beide gevallen moet u het aantal foutdomeinen voor uw hostgroep verstrekken. Als u foutdomeinen in uw groep niet wilt overspannen, gebruikt u een aantal foutdomeinen van 1. 

U ook besluiten om zowel beschikbaarheidszones als foutdomeinen te gebruiken. In dit voorbeeld wordt een hostgroep gemaakt in zone 1, met 2 foutdomeinen. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Een host maken

Laten we nu een speciale host maken in de hostgroep. Naast een naam voor de host, bent u verplicht om de SKU voor de host te verstrekken. Host SKU legt de ondersteunde VM-serie vast, evenals de hardwaregeneratie voor uw toegewijde host.

Zie [Azure Dedicated Host-prijzen](https://aka.ms/ADHPricing)voor meer informatie over de host-SKU's en -prijzen.

Als u een aantal foutdomeinen instelt voor uw hostgroep, wordt u gevraagd het foutdomein voor uw host op te geven. In dit voorbeeld stellen we het foutdomein voor de host in op 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een virtuele machine op de dedicated host. 

Als u een beschikbaarheidszone hebt opgegeven bij het maken van uw hostgroep, moet u dezelfde zone gebruiken bij het maken van de virtuele machine. Omdat onze hostgroep zich in zone 1 bevindt, moeten we bijvoorbeeld de VM in zone 1 maken.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Als u een virtuele machine maakt op een host die niet genoeg resources heeft, wordt de virtuele machine in een mislukte status gemaakt. 

## <a name="check-the-status-of-the-host"></a>Controleer de status van de host

U de status van de hoststatus controleren en hoeveel virtuele machines `-InstanceView` u nog steeds implementeren voor de host met behulp van [GetAzHost](/powershell/module/az.compute/get-azhost) met de parameter.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

De output zal er hetzelfde uitzien als dit:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>Een bestaande VM toevoegen 

U een bestaande vm toevoegen aan een speciale host, maar de VM moet eerst Stop\Deallocated zijn. Voordat u een vm naar een speciale host verplaatst, moet u ervoor zorgen dat de VM-configuratie wordt ondersteund:

- De VM-grootte moet in dezelfde grootte-familie zijn als de toegewezen host. Als uw dedicated host bijvoorbeeld DSv3 is, kan de VM-grootte worden Standard_D4s_v3, maar kan het geen Standard_A4_v2 zijn. 
- De VM moet zich in dezelfde regio bevinden als de speciale host.
- De VM kan geen deel uitmaken van een groep voor plaatsing in de nabijheid. Verwijder de VM uit de plaatsingsgroep in de nabijheid voordat u deze verplaatst naar een speciale host. Zie [Een virtuele machine uit een plaatsingsgroep voor de nabijheid verplaatsen uit een groep nabijheidsplaatsingen](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- De VM kan niet in een beschikbaarheidsset staan.
- Als de VM zich in een beschikbaarheidszone bevindt, moet deze dezelfde beschikbaarheidszone hebben als de hostgroep. De instellingen van de beschikbaarheidszone voor de VM en de hostgroep moeten overeenkomen.

Vervang de waarden van de variabelen door uw eigen gegevens.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Opruimen

Er worden kosten in rekening gebracht voor uw speciale hosts, zelfs als er geen virtuele machines worden geïmplementeerd. U moet alle hosts verwijderen die u momenteel niet gebruikt om kosten te besparen.  

U een host alleen verwijderen als er geen virtuele machines meer worden gebruikt. Verwijder de VM's met [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Nadat u de VM's hebt verwijderd, u de host verwijderen met [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Zodra u al uw hosts hebt verwijderd, u de hostgroep verwijderen met [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

U ook de hele brongroep in één opdracht verwijderen met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Hiermee worden alle bronnen die in de groep zijn gemaakt, inclusief alle VM's, hosts en hostgroepen, verwijderd.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Volgende stappen

- Er is voorbeeld sjabloon, [hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)gevonden, dat zowel zones en fout domeinen gebruikt voor maximale tolerantie in een regio.

- U ook dedicated hosts implementeren via de [Azure-portal.](dedicated-hosts-portal.md)
