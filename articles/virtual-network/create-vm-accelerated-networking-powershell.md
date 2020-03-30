---
title: Een Azure VM maken met versnelde netwerken - Azure PowerShell
description: Meer informatie over het maken van een Virtuele Linux-machine met Accelerated Networking.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245055"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Een virtuele Windows-machine maken met versnelde netwerken met Azure PowerShell

In deze zelfstudie leert u hoe u een Virtuele Windows-machine (VM) maakt met Accelerated Networking. Zie Een Linux VM maken [met accelerated networking](create-vm-accelerated-networking-cli.md)als u een Linux-vm wilt maken met versnelde netwerken. Versnelde netwerken maakt single root I/O virtualisatie (SR-IOV) naar een VM mogelijk, waardoor de netwerkprestaties aanzienlijk worden verbeterd. Dit krachtige pad omzeilt de host vanaf het gegevenspad en vermindert latentie, jitter en CPU-gebruik, voor gebruik met de meest veeleisende netwerkworkloads op ondersteunde VM-typen. De volgende foto toont communicatie tussen twee VM's met en zonder versnelde netwerken:

![Vergelijking](./media/create-vm-accelerated-networking/accelerated-networking.png)

Zonder versnelde netwerken moet al het netwerkverkeer in en uit de VM de host en de virtuele switch doorkruisen. De virtuele switch biedt alle beleidshandhaving, zoals netwerkbeveiligingsgroepen, toegangscontrolelijsten, isolatie en andere gevirtualiseerde netwerkservices voor netwerkverkeer. Zie [Hyper-V-netwerkvirtualisatie en virtuele switch](https://technet.microsoft.com/library/jj945275.aspx)voor meer informatie over virtuele switches.

Bij versnelde netwerken komt netwerkverkeer binnen bij de netwerkinterface (NIC) van de VM en wordt het vervolgens doorgestuurd naar de VM. Alle netwerkbeleidsregels die de virtuele switch toepast, worden nu ontladen en toegepast in hardware. Door beleid in hardware toe te passen, kan de NIC netwerkverkeer rechtstreeks doorsturen naar de VM, waarbij de host en de virtuele switch worden omzeild, met behoud van al het beleid dat in de host is toegepast.

De voordelen van versnelde netwerken zijn alleen van toepassing op de VM waarop deze is ingeschakeld. Voor de beste resultaten is het ideaal om deze functie in te schakelen op ten minste twee VM's die zijn aangesloten op hetzelfde Azure Virtual Network (VNet). Wanneer u communiceert via VNets of on-premises verbinding maakt, heeft deze functie een minimale impact op de algehele latentie.

## <a name="benefits"></a>Voordelen
* **Lagere latentie / Hogere pakketten per seconde (pps):** Als u de virtuele switch van het gegevenspad verwijdert, worden de tijdpakketten in de host verwijderd voor beleidsverwerking en wordt het aantal pakketten dat binnen de VM kan worden verwerkt, verhoogd.
* **Verminderde jitter:** Verwerking van virtuele schakelaars is afhankelijk van de hoeveelheid beleid die moet worden toegepast en de werkbelasting van de CPU die de verwerking doet. Als u de beleidshandhaving naar de hardware haalt, wordt die variabiliteit verwijderd door pakketten rechtstreeks aan de VM te leveren, de host naar VM-communicatie te verwijderen en alle software-interrupts en contextswitches.
* **Verminderd CPU-gebruik:** Het omzeilen van de virtuele schakelaar in de host leidt tot minder CPU-gebruik voor het verwerken van netwerkverkeer.

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden uit het vak ondersteund vanuit de Azure Gallery:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Accelerated Networking wordt ondersteund op de meeste algemene en voor compute geoptimaliseerde instantieformaten met 2 of meer vCPU's.  Deze ondersteunde series zijn: D/DSv2 en F/Fs

Op gevallen die hyperthreading ondersteunen, wordt Accelerated Networking ondersteund op VM-exemplaren met 4 of meer vCPU's. Ondersteunde series zijn: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms en Ms/Mmsv2.

Zie [Windows VM-formaten](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over VM-exemplaren .

### <a name="regions"></a>Regio's
Beschikbaar in alle openbare Azure-regio's en Azure Government Cloud.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Versneld netwerken inschakelen op een draaiende VM
Een ondersteunde VM-grootte zonder versnelde netwerk ingeschakeld kan alleen de functie ingeschakeld wanneer deze is gestopt en deallocated.

### <a name="deployment-through-azure-resource-manager"></a>Implementatie via Azure Resource Manager
Virtuele machines (klassiek) kunnen niet worden geïmplementeerd met Accelerated Networking.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Een Windows-vm maken met Azure Accelerated Networking
## <a name="portal-creation"></a>Portalcreatie
Hoewel dit artikel stappen bevat om een virtuele machine met versnelde netwerken te maken met Azure Powershell, u ook [een virtuele machine maken met versnelde netwerken met behulp van de Azure-portal.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Kies bij het maken van een virtuele machine in de portal in **het blad Een virtueel machineblad maken** het tabblad **Netwerken.**  In dit tabblad is er een optie voor **Versnelde netwerken.**  Als u een [ondersteund besturingssysteem](#supported-operating-systems) en [vm-grootte](#supported-vm-instances)hebt gekozen, wordt deze optie automatisch ingevuld tot 'Aan'.  Zo niet, dan zal het de optie "Uit" voor Versnelde netwerken vullen en de gebruiker een reden geven waarom deze niet is ingeschakeld.   
* *Let op:* Alleen ondersteunde besturingssystemen kunnen via de portal worden ingeschakeld.  Als u een aangepaste afbeelding gebruikt en uw afbeelding versnelde netwerken ondersteunt, maakt u uw VM met CLI of Powershell. 

Nadat de virtuele machine is gemaakt, u bevestigen dat Versnelde netwerken is ingeschakeld door de instructies in het bevestigen dat versnelde netwerken is ingeschakeld, te volgen.

## <a name="powershell-creation"></a>Powershell-creatie
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installeer [Azure PowerShell-versie](/powershell/azure/install-az-ps) 1.0.0 of hoger. Voer uit om de `Get-Module -ListAvailable Az`huidige geïnstalleerde versie te vinden. Als u de nieuwste versie van de Az-module in de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az)wilt installeren of upgraden. Log in een PowerShell-sessie in bij een [Azure-account met Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeelden van parameternamen waren *myResourceGroup,* *myNic*en *myVM*.

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *centralus-locatie* ge:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Maak eerst een subnetconfiguratie met [New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig) In het volgende voorbeeld wordt een subnet met de naam *mySubnet geopperd:*

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Maak een virtueel netwerk met [New-AzVirtualNetwork,](/powershell/module/az.Network/New-azVirtualNetwork)met het *mySubnet* subnet.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak eerst een regel voor netwerkbeveiliging met [New-AzNetworkSecurityRuleConfig.](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) en wijs de beveiligingsregel *Toestaan-RDP-All* toe. Naast de regel *Toestaan-RDP-Alles* bevat de netwerkbeveiligingsgroep verschillende standaardregels. Eén standaardregel schakelt alle binnenkomende toegang vanaf het internet uit, daarom is de regel *Toestaan-RDP-Allemaal* toegewezen aan de netwerkbeveiligingsgroep, zodat u op afstand verbinding maken met de virtuele machine, zodra deze is gemaakt.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Koppel de netwerkbeveiligingsgroep aan het *subnet van mySubnet* aan [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). De regel in de netwerkbeveiligingsgroep is effectief voor alle resources die in het subnet worden geïmplementeerd.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Een netwerkinterface maken met versnelde netwerken
Maak een openbaar IP-adres met [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Een openbaar IP-adres is niet vereist als u niet van plan bent om toegang te krijgen tot de virtuele machine vanaf het internet, maar om de stappen in dit artikel te voltooien, is dit vereist.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Maak een netwerkinterface met [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) met versnelde netwerkingeschakeld en wijs het openbare IP-adres toe aan de netwerkinterface. In het volgende voorbeeld wordt een netwerkinterface met de naam *myNic* gemaakt in het *mySubnet-subnet* van het virtuele *myVnet-netwerk* en wordt het *ip-adres van myPublicIp* eraan toegeten:

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>De virtuele machine maken

Stel uw VM-referenties `$cred` in op de variabele met [Get-Credential:](/powershell/module/microsoft.powershell.security/get-credential)

```powershell
$cred = Get-Credential
```

Definieer eerst uw VM met [Nieuw-AzVMConfig.](/powershell/module/az.compute/new-azvmconfig) In het volgende voorbeeld wordt een VM met de naam *myVM* gedefinieerd met een VM-grootte die versnelde netwerken ondersteunt *(Standard_DS4_v2):*

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Zie [Windows VM-formaten](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met alle VM-formaten en -kenmerken .

Maak de rest van uw [VM-configuratie met Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) en [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). In het volgende voorbeeld wordt een Windows Server 2016-vm gemaakt:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Voeg de netwerkinterface toe die u eerder hebt gemaakt met [Add-AzVMNetworkInterface:](/powershell/module/az.compute/add-azvmnetworkinterface)

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Maak tot slot uw VM met [New-AzVM:](/powershell/module/az.compute/new-azvm)

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Controleren of het stuurprogramma in het besturingssysteem is geïnstalleerd

Zodra u de VM in Azure hebt gemaakt, maakt u verbinding met de vm en bevestigt u dat het stuurprogramma in Windows is geïnstalleerd.

1. Open vanuit een internetbrowser de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-account.
2. Typ myVm in het vak met de tekst *Zoekbronnen* boven aan de *Azure-portal*. Wanneer **myVm** in de zoekresultaten wordt weergegeven, klikt u erop. Als **Maken** zichtbaar is onder de knop **Verbinding maken,** is Azure nog niet klaar met het maken van de VM. Klik alleen linksboven in het overzicht **op Verbinding** maken nadat u **Maken** niet meer onder de knop **Verbinding ziet.**
3. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd in [De virtuele machine maken.](#create-the-virtual-machine) Zie Verbinding maken met [virtuele machine](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)als u nog nooit verbinding hebt gemaakt met een Windows-vm in Azure.
4. Klik met de rechtermuisknop op de knop Windows Start en klik op **Apparaatbeheer**. Het knooppunt **Netwerkadapters** uitvouwen. Controleer of de **Ethernet-adapter voor virtuele functies van Mellanox ConnectX-3** wordt weergegeven, zoals in de volgende afbeelding wordt weergegeven:

    ![Apparaatbeheer](./media/create-vm-accelerated-networking/device-manager.png)

Versnelde netwerken is nu ingeschakeld voor uw VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Versneld netwerken inschakelen op bestaande VM's
Als u een VM zonder versnelde netwerken hebt gemaakt, is het mogelijk om deze functie in te schakelen op een bestaande virtuele machine.  De VM moet Versnelde netwerken ondersteunen door te voldoen aan de volgende vereisten die hierboven zijn beschreven:

* De VM moet een ondersteunde grootte hebben voor Accelerated Networking
* De VM moet een ondersteunde Azure Gallery-afbeelding zijn (en kernelversie voor Linux)
* Alle VM's in een beschikbaarheidsset of VMSS moeten worden gestopt/deallocated voordat accelerated networking op een NIC wordt ingeschakeld

### <a name="individual-vms--vms-in-an-availability-set"></a>Afzonderlijke VM's & VM's in een beschikbaarheidsset
Eerste stop/deallocate de VM of, als een beschikbaarheidsset, alle VM's in de set:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Belangrijk, let op: als uw VM afzonderlijk is gemaakt, zonder beschikbaarheidsset, hoeft u alleen de afzonderlijke VM te stoppen/detoewijzen om versneld netwerken in te schakelen.  Als uw VM is gemaakt met een beschikbaarheidsset, moeten alle VM's in de beschikbaarheidsset worden gestopt/deallocated voordat versnelde netwerken op een van de NIC's worden ingeschakeld. 

Als u eenmaal gestopt bent, schakelt u Versneld netwerken in op de nic van uw vm:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Start uw VM opnieuw of, als u in een beschikbaarheidsset alle VM's in de set hebt, en bevestig dat Versnelde netwerken is ingeschakeld:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS is iets anders, maar volgt dezelfde workflow.  Stop eerst de VM's:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Zodra de VM's zijn gestopt, werkt u de eigenschap Accelerated Networking bij onder de netwerkinterface:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Houd er rekening mee dat een VMSS VM-upgrades heeft die updates toepassen met behulp van drie verschillende instellingen, automatisch, rollen d.m.v. handmatig.  In deze instructies is het beleid ingesteld op automatisch, zodat de VMSS de wijzigingen onmiddellijk na het opnieuw opstarten oppakt.  Om het automatisch in te stellen, zodat de wijzigingen onmiddellijk worden opgepikt:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Start ten slotte het VMSS opnieuw:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Zodra u opnieuw start, wacht u tot de upgrades zijn voltooid, maar zodra deze is voltooid, wordt de VF in de VM weergegeven.  (Zorg ervoor dat u een ondersteund besturingssysteem en vm-formaat gebruikt)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Het formaat van bestaande VM's wijzigen met accelerated networking

VM's met Accelerated Networking ingeschakeld kunnen alleen worden aangepast aan VM's die Accelerated Networking ondersteunen.  

Een VM met accelerated networking ingeschakeld kan niet worden aangepast aan een VM-exemplaar dat geen ondersteuning biedt voor Versnelde netwerken met behulp van de bewerking Formaat wijzigen.  In plaats daarvan u het formaat van een van deze VM's wijzigen:

* Stop/deallocate de VM of als in een beschikbaarheidsset/VMSS alle VM's in de set/VMSS stoppen/detoewijzen.
* Versnelde netwerken moeten worden uitgeschakeld op de NIC van de VM of als in een beschikbaarheidsset/VMSS alle VM's in de set/VMSS worden ingeschakeld.
* Zodra Accelerated Networking is uitgeschakeld, kan de VM/availability set/VMSS worden verplaatst naar een nieuwe grootte die geen ondersteuning biedt voor Accelerated Networking en opnieuw wordt gestart.
