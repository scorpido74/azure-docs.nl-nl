---
title: Windows-licentie toepassen op virtuele machines voor sessiehost - Azure
description: Beschrijft hoe u de Windows-licentie voor Windows Virtual Desktop VM's toepast.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254233"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Windows-licentie toepassen op virtuele machines voor sessiehost

Klanten met een goede licentie voor het uitvoeren van Windows Virtual Desktop-workloads komen in aanmerking om een Windows-licentie toe te passen op hun virtuele machines voor sessiehost en deze uit te voeren zonder te betalen voor een andere licentie. Zie [Windows Virtual Desktop-prijzen voor](https://azure.microsoft.com/pricing/details/virtual-desktop/)meer informatie.

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Manieren om uw Windows Virtual Desktop-licentie te gebruiken
Met Windows Virtual Desktop-licenties u een licentie toepassen op elke virtuele windows- of Windows Server-machine die is geregistreerd als sessiehost in een hostgroep en gebruikersverbindingen ontvangt. Deze licentie is niet van toepassing op virtuele machines die worden uitgevoerd als bestandsshareservers, domeincontrollers, enzovoort.

Er zijn een paar manieren om de Windows Virtual Desktop-licentie te gebruiken:
- U een hostpool en de virtuele machines van de sessiehost maken met behulp van het [Azure Marketplace-aanbod.](./create-host-pools-azure-marketplace.md) Virtuele machines die op deze manier zijn gemaakt, hebben de licentie automatisch toegepast.
- U een hostgroep en de virtuele machines van de sessiehost maken met de [sjabloon GitHub Azure Resource Manager.](./create-host-pools-arm-template.md) Virtuele machines die op deze manier zijn gemaakt, hebben de licentie automatisch toegepast.
- U een licentie toepassen op een bestaande virtuele machine voor sessiehost. Volg hiervoor eerst de instructies in [Een hostgroep maken met PowerShell](./create-host-pools-powershell.md) om een hostgroep en bijbehorende VM's te maken en ga vervolgens terug naar dit artikel om te leren hoe u de licentie toepassen.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Een Windows-licentie toepassen op een vm van sessiehost
Zorg ervoor dat u [de nieuwste Azure PowerShell](/powershell/azure/overview)hebt geïnstalleerd en geconfigureerd. Voer de volgende PowerShell-cmdlet uit om de Windows-licentie toe te passen:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Controleer of uw sessiehost VM gebruikmaakt van het licentievoordeel
Voer na het implementeren van uw VM deze cmdlet ot uit om het licentietype te verifiëren:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Een vm met sessiehost met de toegepaste Windows-licentie toont u zoiets als dit:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

VM's zonder de toegepaste Windows-licentie toont u iets als dit:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Voer de volgende cmdlet uit om een lijst te zien met alle VM's voor sessiehost die de Windows-licentie hebben toegepast in uw Azure-abonnement:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
