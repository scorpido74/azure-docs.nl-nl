---
title: Een Azure-VM maken met versneld netwerken-Azure PowerShell
description: Meer informatie over het maken van een virtuele Linux-machine met versneld netwerken.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245055"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Maak een virtuele Windows-machine met versneld netwerken met behulp van Azure PowerShell

In deze zelf studie leert u hoe u een virtuele Windows-machine (VM) kunt maken met versneld netwerken. Zie [een virtuele Linux-machine maken met versneld](create-vm-accelerated-networking-cli.md)netwerken voor het maken van een virtuele Linux-machine met versneld netwerken. Versneld netwerken maken gebruik van I/O-virtualisatie met één hoofdmap (SR-IOV) naar een virtuele machine, waardoor de netwerk prestaties aanzienlijk worden verbeterd. Dit pad met hoge prestaties omzeilt de host van de DataPath, vermindert latentie, jitter en CPU-gebruik, voor gebruik met de meest veeleisende netwerk workloads op ondersteunde VM-typen. In de volgende afbeelding ziet u communicatie tussen twee Vm's met en zonder versneld netwerken:

![Vergelijking](./media/create-vm-accelerated-networking/accelerated-networking.png)

Zonder versneld netwerk moeten al het netwerk verkeer van en naar de virtuele machine de host en de Virtual Switch door lopen. De virtuele switch biedt alle beleids afdwinging, zoals netwerk beveiligings groepen, Toegangs beheer lijsten, isolatie en andere gevirtualiseerde netwerk services voor netwerk verkeer. Zie [Hyper-V-Netwerkvirtualisatie en virtuele switch](https://technet.microsoft.com/library/jj945275.aspx)voor meer informatie over virtuele switches.

Met versneld netwerken arriveert het netwerk verkeer op de netwerk interface van de VM (NIC) en wordt vervolgens doorgestuurd naar de virtuele machine. Alle netwerk beleidsregels waarmee de virtuele switch wordt toegepast, worden nu geoffload en toegepast in hardware. Door beleid toe te passen op hardware kan de NIC netwerk verkeer rechtstreeks naar de virtuele machine door sturen, waardoor de host en de virtuele switch worden omzeild, terwijl alle beleids regels die op de host worden toegepast, behouden blijven.

De voor delen van versneld netwerken zijn alleen van toepassing op de virtuele machine waarop deze is ingeschakeld. Voor de beste resultaten is het ideaal om deze functie in te scha kelen op ten minste twee virtuele machines die zijn verbonden met hetzelfde Azure-Virtual Network (VNet). Bij de communicatie tussen VNets of on-premises, heeft deze functie een minimale invloed op de algehele latentie.

## <a name="benefits"></a>Voordelen
* **Lagere latentie/hogere pakketten per seconde (PPS):** Als u de virtuele switch uit de DataPath verwijdert, worden de tijd pakketten op de host verwijderd voor beleids verwerking en wordt het aantal pakketten dat kan worden verwerkt in de virtuele machine verhoogd.
* **Gereduceerde jitter:** De verwerking van virtuele switches is afhankelijk van de hoeveelheid beleid die moet worden toegepast en de werk belasting van de CPU die de verwerking uitvoert. Door het beleid afdwingen naar de hardware te verwijderen, verwijdert u die variabiliteit door pakketten rechtstreeks aan de virtuele machine te leveren, waardoor de host wordt verwijderd naar de VM-communicatie en alle software-interrupts en-context switches.
* **Verminderd CPU-gebruik:** Het overs laan van de virtuele switch in de host leidt tot minder CPU-gebruik voor het verwerken van netwerk verkeer.

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden ondersteund uit het vak van de Azure-galerie:
* **Windows Server 2016 Data Center** 
* **Windows Server 2012 R2 Data Center**
* **Windows Server 2019 Data Center**

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Versnelde netwerken worden ondersteund in de meeste algemene doel stellingen en met Compute geoptimaliseerde exemplaar grootten met twee of meer Vcpu's.  Deze ondersteunde reeksen zijn: D/DSv2 en F/FS

Op instanties die HyperThreading ondersteunen, wordt versneld netwerken ondersteund op VM-exemplaren met vier of meer Vcpu's. Ondersteunde reeksen zijn: D/Dsv3, E/Esv3, Fsv2, Lsv2, MS/MMS en MS/Mmsv2.

Zie [Windows VM-grootten](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over VM-exemplaren.

### <a name="regions"></a>Regio's
Beschikbaar in alle open bare Azure-regio's en Azure Government Cloud.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Versnelde netwerken inschakelen op een actieve virtuele machine
Voor een ondersteunde VM-grootte zonder versneld netwerken is ingeschakeld, kan de functie alleen worden ingeschakeld wanneer deze wordt gestopt en de toewijzing ongedaan wordt gemaakt.

### <a name="deployment-through-azure-resource-manager"></a>Implementatie via Azure Resource Manager
Virtuele machines (klassiek) kunnen niet worden geïmplementeerd met versneld netwerken.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Een Windows-VM maken met versneld Azure-netwerken
## <a name="portal-creation"></a>Portal maken
Hoewel dit artikel stappen bevat voor het maken van een virtuele machine met versneld netwerken met behulp van Azure Power shell, kunt u ook [een virtuele machine maken met versneld netwerken met behulp van de Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wanneer u een virtuele machine in de portal maakt, kiest u op de Blade **een virtuele machine maken** het tabblad **netwerk** .  Op dit tabblad is er een optie voor **versneld netwerken**.  Als u een [ondersteund besturings systeem](#supported-operating-systems) en een [VM-grootte](#supported-vm-instances)hebt gekozen, wordt deze optie automatisch ingevuld op ' aan '.  Als dat niet het geval is, wordt de optie ' uit ' gevuld voor versneld netwerken en krijgt de gebruiker een reden waarom deze niet is ingeschakeld.   
* *Opmerking:* Alleen ondersteunde besturings systemen kunnen worden ingeschakeld via de portal.  Als u een aangepaste installatie kopie gebruikt en uw installatie kopie versneld netwerken ondersteunt, maakt u uw virtuele machine met CLI of Power shell. 

Nadat de virtuele machine is gemaakt, kunt u de versnelde netwerken bevestigen door de instructies te volgen in de bevestigen dat versneld netwerken zijn ingeschakeld.

## <a name="powershell-creation"></a>Power shell maken
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installeer [Azure PowerShell](/powershell/azure/install-az-ps) versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az`uit om de momenteel geïnstalleerde versie te vinden. Als u wilt installeren of upgraden, installeert u de nieuwste versie van de AZ-module vanaf de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). Meld u in een Power shell-sessie aan bij een Azure [-account met Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myNic*en *myVM*.

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *middenus* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Maak eerst een subnet-configuratie met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). In het volgende voor beeld wordt een subnet met de naam *mySubnet*gemaakt:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)met het *mySubnet* -subnet.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak eerst een regel voor de netwerk beveiligings groep met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Maak een netwerk beveiligings groep met [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) en wijs hieraan de regel *Allow-RDP-all* Security toe. Naast de regel *Allow-RDP-all* , bevat de netwerk beveiligings groep verschillende standaard regels. Met één standaard regel wordt alle binnenkomende toegang van Internet uitgeschakeld. dat is de reden waarom de regel voor het *toestaan van RDP-alle* is toegewezen aan de netwerk beveiligings groep, zodat u op afstand verbinding kunt maken met de virtuele machine nadat deze is gemaakt.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Koppel de netwerk beveiligings groep aan het *mySubnet* -subnet met [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). De regel in de netwerk beveiligings groep is effectief voor alle resources die in het subnet zijn geïmplementeerd.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Een netwerk interface met versneld netwerken maken
Maak een openbaar IP-adres met [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Een openbaar IP-adres is niet vereist als u niet van plan bent om toegang te krijgen tot de virtuele machine via internet, maar dit is vereist om de stappen in dit artikel uit te voeren.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Maak een netwerk interface met [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) met versneld netwerken ingeschakeld en wijs het open bare IP-adres toe aan de netwerk interface. In het volgende voor beeld wordt een netwerk interface met de naam *myNic* in het *mySubnet* -subnet van het virtuele netwerk *MyVnet* gemaakt en wordt het *myPublicIp* open bare IP-adres toegewezen:

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

Stel uw VM-referenties in op de `$cred` variabele met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Definieer eerst uw virtuele machine met [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). In het volgende voor beeld wordt een virtuele machine met de naam *myVM* gedefinieerd met een VM-grootte die ondersteuning biedt voor versneld netwerken (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Zie [Windows VM-grootten](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met alle VM-grootten en-kenmerken.

Maak de rest van uw VM-configuratie met [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) en [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). In het volgende voor beeld wordt een virtuele machine met Windows Server 2016 gemaakt:

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

Koppel de netwerk interface die u eerder hebt gemaakt met [add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Maak tot slot uw virtuele machine met [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Controleren of het stuur programma is geïnstalleerd in het besturings systeem

Nadat u de virtuele machine in azure hebt gemaakt, maakt u verbinding met de virtuele machine en controleert u of het stuur programma is geïnstalleerd in Windows.

1. Open vanuit een Internet browser de Azure- [Portal](https://portal.azure.com) en meld u aan met uw Azure-account.
2. Typ *myVm*in het vak met de tekst *zoeken naar resources* bovenaan het Azure Portal. Wanneer **myVm** wordt weer gegeven in de zoek resultaten, klikt u erop. Als **maken** wordt weer gegeven onder de knop **verbinding** maken, is de virtuele machine nog niet door Azure gemaakt. Klik in de linkerbovenhoek van het overzicht op **verbinding maken** als u de knop **verbinding** **maken** niet meer ziet.
3. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven in [de virtuele machine maken](#create-the-virtual-machine). Als u nooit hebt verbonden met een Windows-VM in azure, raadpleegt u [verbinding maken met de virtuele machine](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Klik met de rechter muisknop op de Windows-knop Start en klik op **Apparaatbeheer**. Vouw het knoop punt **netwerk adapters** uit. Bevestig dat de **Mellanox connectx-3 virtuele function Ethernet-adapter** wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

    ![Apparaatmanager](./media/create-vm-accelerated-networking/device-manager.png)

Versneld netwerken zijn nu ingeschakeld voor uw VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Versneld netwerken op bestaande Vm's inschakelen
Als u een VM hebt gemaakt zonder versneld netwerk, is het mogelijk om deze functie in te scha kelen op een bestaande virtuele machine.  De virtuele machine moet versneld netwerken ondersteunen door aan de volgende vereisten te voldoen die ook hierboven worden beschreven:

* De virtuele machine moet een ondersteunde grootte voor versneld netwerken zijn
* De virtuele machine moet een ondersteunde installatie kopie van Azure Gallery zijn (en de kernel-versie voor Linux)
* Alle virtuele machines in een beschikbaarheidsset of VMSS moeten worden gestopt/vrijgegeven voordat versnelde netwerken op een NIC worden ingeschakeld

### <a name="individual-vms--vms-in-an-availability-set"></a>Afzonderlijke Vm's & Vm's in een beschikbaarheidsset
Eerst stoppen/toewijzing van de virtuele machine ongedaan maken of, als u een Beschikbaarheidsset hebt, alle virtuele machines in de set:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Belang rijk: als uw virtuele machine afzonderlijk is gemaakt, zonder dat er een beschikbaarheidsset beschikbaar is, hoeft u de afzonderlijke virtuele machine alleen te stoppen/detoewijzen om versnelde netwerken mogelijk te maken.  Als uw virtuele machine is gemaakt met een beschikbaarheidsset, moeten alle virtuele machines in de beschikbaarheidsset worden gestopt/vrijgegeven voordat versneld netwerken op een van de Nic's worden ingeschakeld. 

Als u bent gestopt, schakelt u versneld netwerken in op de NIC van uw VM:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Start de VM opnieuw op of, in een beschikbaarheidsset, alle virtuele machines in de set en controleer of versneld netwerken zijn ingeschakeld:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS is iets anders, maar volgt dezelfde werk stroom.  Eerst stopt u de Vm's:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Zodra de Vm's zijn gestopt, werkt u de eigenschap versneld netwerk bij onder de netwerk interface:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Houd er rekening mee dat een VMSS VM-upgrades heeft die updates Toep assen met behulp van drie verschillende instellingen, automatische, Rolling en hand matig.  In deze instructies wordt het beleid ingesteld op automatisch, zodat de VMSS de wijzigingen direct na het opnieuw opstarten ophaalt.  Stel deze in op automatisch, zodat de wijzigingen onmiddellijk worden opgehaald:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Ten slotte start u de VMSS opnieuw:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Nadat u de computer opnieuw hebt opgestart, wacht u totdat de upgrade is voltooid, wordt het VF weer gegeven in de virtuele machine.  (Controleer of u een ondersteund besturings systeem en VM-grootte gebruikt)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Het formaat wijzigen van bestaande Vm's met versneld netwerken

Vm's waarvoor versneld netwerken zijn ingeschakeld, kunnen alleen worden gewijzigd in de grootte van virtuele machines die ondersteuning bieden voor versneld netwerken.  

Het formaat van een virtuele machine met versneld netwerken kan niet worden gewijzigd in een VM-exemplaar dat geen ondersteuning biedt voor versnelde netwerken met behulp van de bewerking voor het wijzigen van de grootte.  In plaats daarvan wijzigt u het formaat van een van deze Vm's:

* Stop/Hef de toewijzing van de virtuele machine op of hef de toewijzing van de virtuele machines in de set-VMSS op en sluit deze toe.
* Versnelde netwerken moeten worden uitgeschakeld op de NIC van de virtuele machine of in een beschikbaarheidsset/VMSS alle virtuele machines in de set/VMSS.
* Zodra versneld netwerken zijn uitgeschakeld, kunnen de set/VMSS van de virtuele machine en de beschik baarheid worden verplaatst naar een nieuwe grootte die geen versneld netwerken ondersteunt en opnieuw wordt gestart.
