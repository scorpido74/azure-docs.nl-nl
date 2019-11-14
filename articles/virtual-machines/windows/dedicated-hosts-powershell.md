---
title: Implementeer uw eigen Azure-hosts met behulp van de Azure PowerShell
description: Implementeer Vm's op toegewezen hosts met behulp van Azure PowerShell.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 92dca6f4f41ff426aebcb8e580653afaa71afff8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033356"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Voor beeld: Vm's implementeren op toegewezen hosts met behulp van de Azure PowerShell

Dit artikel begeleidt u bij het maken van een toegewezen Azure- [host](dedicated-hosts.md) voor het hosten van uw virtuele machines (vm's). 

Zorg ervoor dat u Azure PowerShell versie 2.4.2 of hoger hebt geïnstalleerd en u bent aangemeld bij een Azure-account in met `Connect-AzAccount`. Als u versie 2.4.2 wilt installeren, opent u een Power shell-prompt en typt u:

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrerelease
```

U hebt ten minste versie 1.6.0 van de PowerShellGet-module nodig om de functionaliteit van de preview-module in Power shell in te scha kelen. De nieuwste versies van Power shell core hebben dit automatisch ingebouwd, maar voor oudere versies van Power shell kunt u de volgende opdracht uitvoeren om naar de nieuwste versie bij te werken:

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> Exclusieve Azure-hosts bevindt zich momenteel in de open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> **Bekende preview-beperkingen**
> - Virtuele-machine schaal sets worden momenteel niet ondersteund op toegewezen hosts.
> - De eerste release van de preview-versie ondersteunt de volgende VM-reeksen: DSv3 en ESv3. 



## <a name="create-a-host-group"></a>Een hostgroep maken

Een **hostgroep** is een resource die een verzameling toegewezen hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheids zone en voegt hierop hosts toe. Bij het plannen van hoge Beschik baarheid zijn er extra opties. U kunt een of beide van de volgende opties gebruiken met uw toegewezen hosts: 
- Beschik over meerdere beschikbaarheids zones. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Over meerdere fout domeinen die zijn toegewezen aan fysieke racks. 
 
In beide gevallen moet u het aantal fout domeinen voor uw hostgroep opgeven. Als u geen fout domeinen in uw groep wilt beslaan, gebruikt u het aantal fouten domein 1. 

U kunt er ook voor kiezen om zowel beschikbaarheids zones als fout domeinen te gebruiken. In dit voor beeld wordt een hostgroep gemaakt in zone 1, met 2 fout domeinen. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Een host maken

We gaan nu een toegewezen host maken in de hostgroep. Naast een naam voor de host, moet u de SKU voor de host opgeven. Host SKU legt de ondersteunde VM-serie en de generatie van de hardware voor uw specifieke host vast.  Tijdens de preview worden de volgende SKU-waarden voor de host ondersteund: DSv3_Type1 en ESv3_Type1.


Zie voor meer informatie over de Sku's en prijzen van de host de [Azure dedicated host prijzen](https://aka.ms/ADHPricing).

Als u het aantal fout domeinen voor uw hostgroep instelt, wordt u gevraagd om het fout domein voor uw host op te geven. In dit voor beeld stellen we het fout domein voor de host in op 1.


```powershell
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een virtuele machine op de toegewezen host. 

Als u een beschikbaarheids zone hebt opgegeven bij het maken van uw hostgroep, moet u dezelfde zone gebruiken bij het maken van de virtuele machine. In dit voor beeld, omdat de hostgroep zich in zone 1 bevindt, moet u de virtuele machine in zone 1 maken.  


```powershell
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
> Als u een virtuele machine maakt op een host die onvoldoende bronnen heeft, wordt de virtuele machine gemaakt met de status mislukt. 

## <a name="check-the-status-of-the-host"></a>Controleer de status van de host

U kunt de status van de host controleren en het aantal virtuele machines dat u nog steeds kunt implementeren op de host met behulp van [GetAzHost](/powershell/module/az.compute/get-azhost) met de para meter `-InstanceView`.

```
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

De uitvoer ziet er ongeveer als volgt uit:

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

## <a name="clean-up"></a>Opruimen

Er worden kosten in rekening gebracht voor uw specifieke hosts, zelfs wanneer er geen virtuele machines zijn geïmplementeerd. U moet alle hosts die u momenteel gebruikt, verwijderen om kosten te besparen.  

U kunt een host alleen verwijderen als er geen virtuele machines meer worden gebruikt. Verwijder de virtuele machines met behulp van [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Nadat u de Vm's hebt verwijderd, kunt u de host verwijderen met [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Wanneer u al uw hosts hebt verwijderd, kunt u de hostgroep verwijderen met [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

U kunt ook de hele resource groep in één opdracht verwijderen met behulp van [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Hiermee verwijdert u alle resources die zijn gemaakt in de groep, inclusief alle Vm's, hosts en hostgroepen.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Volgende stappen

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook toegewezen hosts implementeren met behulp van de [Azure Portal](dedicated-hosts-portal.md).
