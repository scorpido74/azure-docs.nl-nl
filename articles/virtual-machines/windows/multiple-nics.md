---
title: Windows VM's maken en beheren in Azure die meerdere NIC's gebruiken
description: Meer informatie over het maken en beheren van een Windows VM waaraan meerdere NIC's zijn gekoppeld met Azure PowerShell- of Resource Manager-sjablonen.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 6651ae21694022be86d8db08737c609aed3df569
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870260"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Een virtuele Windows-machine maken en beheren met meerdere NIC's
Virtuele machines (VM's) in Azure kunnen meerdere virtuele netwerkinterfacekaarten (NIC's) aan hen koppelen. Een veelvoorkomend scenario is om verschillende subnetten voor front-end en back-end connectiviteit te hebben. U meerdere NIC's op een VM koppelen aan meerdere subnetten, maar die subnetten moeten allemaal in hetzelfde virtuele netwerk (vNet) zijn. In dit artikel wordt beschreven hoe u een VM maakt waaraan meerdere NIC's zijn gekoppeld. U leert ook hoe u NIC's toevoegen of verwijderen uit een bestaande virtuele machine. Verschillende [VM-formaten](sizes.md) ondersteunen een wisselend aantal NIC's, zodat de grootte van uw VM dienovereenkomstig.

## <a name="prerequisites"></a>Vereisten

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeelden van parameternamen zijn *myResourceGroup,* *myVnet*en *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Een virtuele machine met meerdere NIC's maken
Maak eerst een resourcegroep. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de locatie *EastUs gezoend:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Virtueel netwerk en subnetten maken
Een veelvoorkomend scenario is dat een virtueel netwerk twee of meer subnetten heeft. Een subnet kan worden voor front-end verkeer, de andere voor back-end verkeer. Als u verbinding wilt maken met beide subnetten, gebruikt u vervolgens meerdere NIC's op uw VM.

1. Definieer twee virtuele netwerksubnetten met [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voorbeeld worden de subnetten voor *mySubnetFrontEnd* en *mySubnetBackEnd gedefinieerd:*

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Maak uw virtuele netwerk en subnetten met [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVnet geopperd:*

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Meerdere NIC's maken
Maak twee NIC's met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Bevestig een NIC aan het front-end subnet en een NIC aan het back-end subnet. In het volgende voorbeeld worden NIC's met de naam *myNic1* en *myNic2 aanmaken:*

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Meestal maakt u ook een [netwerkbeveiligingsgroep](../../virtual-network/security-overview.md) om netwerkverkeer te filteren op de VM en een [load balancer](../../load-balancer/load-balancer-overview.md) om verkeer over meerdere VM's te distribueren.

### <a name="create-the-virtual-machine"></a>De virtuele machine maken
Begin nu met het bouwen van uw VM-configuratie. Elke VM-grootte heeft een limiet voor het totale aantal NIC's dat u aan een vm toevoegen. Zie [Windows VM-formaten](sizes.md)voor meer informatie .

1. Stel uw VM-referenties `$cred` als volgt in op de variabele:

    ```powershell
    $cred = Get-Credential
    ```

2. Definieer uw VM met [Nieuw-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). In het volgende voorbeeld wordt een VM met de naam *myVM* gedefinieerd en wordt een VM-grootte gebruikt die meer dan twee NIC's *(Standard_DS3_v2)* ondersteunt:

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Maak de rest van uw [VM-configuratie met Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) en [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). In het volgende voorbeeld wordt een Windows Server 2016-vm gemaakt:

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

4. Voeg de twee NIC's die u eerder hebt gemaakt met [Add-AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Maak uw VM met [New-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Voeg routes voor secundaire NIC's toe aan het besturingssysteem door de stappen in [Het besturingssysteem configureren voor meerdere NIC's](#configure-guest-os-for-multiple-nics)te voltooien.

## <a name="add-a-nic-to-an-existing-vm"></a>Een NIC toevoegen aan een bestaande virtuele machine
To add a virtual NIC to an existing VM, you deallocate the VM, add the virtual NIC, then start the VM. Verschillende [VM-formaten](sizes.md) ondersteunen een wisselend aantal NIC's, zodat de grootte van uw VM dienovereenkomstig. Indien nodig u [het formaat van een vm wijzigen.](resize-vm.md)

1. Detoewijzing van de VM met [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). In het volgende voorbeeld wordt de VM met de naam *myVM* in *myResourceGroup toegewezen:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Download de bestaande configuratie van de VM met [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). In het volgende voorbeeld vindt u informatie voor de VM met de naam *myVM* in *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. In het volgende voorbeeld wordt een virtuele NIC gemaakt met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) met de naam *myNic3* die is gekoppeld aan *mySubnetBackEnd.* De virtuele NIC wordt vervolgens gekoppeld aan de VM genaamd *myVM* in *myResourceGroup* met [Add-AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primaire virtuele NIC's
    Een van de NIC's op een multi-NIC VM moet primair zijn. Als een van de bestaande virtuele NIC's op de VM al als primair is ingesteld, u deze stap overslaan. In het volgende voorbeeld wordt ervan uitgegaan dat er nu twee virtuele NIC's aanwezig zijn op een VM en dat u de eerste NIC (`[0]`) als primaire wilt toevoegen:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Start de VM met [Start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Voeg routes voor secundaire NIC's toe aan het besturingssysteem door de stappen in [Het besturingssysteem configureren voor meerdere NIC's](#configure-guest-os-for-multiple-nics)te voltooien.

## <a name="remove-a-nic-from-an-existing-vm"></a>Een NIC verwijderen uit een bestaande virtuele machine
To remove a virtual NIC from an existing VM, you deallocate the VM, remove the virtual NIC, then start the VM.

1. Detoewijzing van de VM met [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). In het volgende voorbeeld wordt de VM met de naam *myVM* in *myResourceGroup toegewezen:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Download de bestaande configuratie van de VM met [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). In het volgende voorbeeld vindt u informatie voor de VM met de naam *myVM* in *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Informatie over de NIC verwijderen met [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). In het volgende voorbeeld vindt u informatie over *myNic3:*

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Verwijder de NIC met [Remove-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) en werk de VM bij met [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). In het volgende voorbeeld wordt `$nicId` *myNic3* verwijderd zoals verkregen in de vorige stap:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Start de VM met [Start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Meerdere NIC's maken met sjablonen
Azure Resource Manager-sjablonen bieden een manier om meerdere exemplaren van een resource te maken tijdens de implementatie, zoals het maken van meerdere NIC's. Resource Manager-sjablonen gebruiken declaratieve JSON-bestanden om uw omgeving te definiëren. Zie [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)voor meer informatie. U *kopiëren* gebruiken om het aantal exemplaren op te geven dat u wilt maken:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Zie [Meerdere exemplaren maken met *kopiëren*](../../resource-group-create-multiple.md)voor meer informatie. 

U `copyIndex()` ook een nummer toevoegen aan een resourcenaam. U dan *myNic1,* *MyNic2* en ga zo maar door. In de volgende code ziet u een voorbeeld van het toevoegen van de indexwaarde:

```json
"name": "[concat('myNic', copyIndex())]", 
```

U een volledig voorbeeld van het maken van [meerdere NIC's lezen met behulp van Resource Manager-sjablonen.](../../virtual-network/template-samples.md)

Voeg routes voor secundaire NIC's toe aan het besturingssysteem door de stappen in [Het besturingssysteem configureren voor meerdere NIC's](#configure-guest-os-for-multiple-nics)te voltooien.

## <a name="configure-guest-os-for-multiple-nics"></a>Gastbesturingssysteem configureren voor meerdere NIC's

Azure wijst een standaardgateway toe aan de eerste (primaire) netwerkinterface die is gekoppeld aan de virtuele machine. Azure wijst geen standaardgateway toe aan extra (secundaire) netwerkinterfaces die zijn gekoppeld aan een virtuele machine. Daarom kunt u standaard niet communiceren met resources buiten het subnet waarin een secundaire netwerkinterface zich bevindt. Secundaire netwerkinterfaces kunnen echter communiceren met bronnen buiten hun subnet, hoewel de stappen om communicatie mogelijk te maken verschillend zijn voor verschillende besturingssystemen.

1. Voer vanuit een Windows-opdrachtprompt de opdracht uit, die de `route print` uitvoer retourneert die vergelijkbaar is met de volgende uitvoer voor een virtuele machine met twee gekoppelde netwerkinterfaces:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    In dit voorbeeld is **Microsoft Hyper-V Network Adapter #4** (interface 7) de secundaire netwerkinterface waaraan geen standaardgateway is toegewezen.

2. Voer in een opdrachtprompt de `ipconfig` opdracht uit om te zien welk IP-adres is toegewezen aan de secundaire netwerkinterface. In dit voorbeeld wordt 192.168.2.4 toegewezen aan interface 7. Er wordt geen standaardgatewayadres geretourneerd voor de secundaire netwerkinterface.

3. Als u al het verkeer dat is bestemd voor adressen buiten het subnet van de secundaire netwerkinterface naar de gateway voor het subnet wilt leiden, voert u de volgende opdracht uit:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Het gatewayadres voor het subnet is het eerste IP-adres (eindigend op .1) in het adresbereik dat voor het subnet is gedefinieerd. Als u niet al het verkeer buiten het subnet wilt routeren, u in plaats daarvan afzonderlijke routes toevoegen naar specifieke bestemmingen. Als u bijvoorbeeld alleen verkeer van de secundaire netwerkinterface naar het 192.168.3.0-netwerk wilt leiden, voert u de opdracht in:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Als u bijvoorbeeld een succesvolle communicatie met een resource op het netwerk 192.168.3.0 wilt bevestigen, voert u de volgende opdracht in om 192.168.3.4 te pingen met interface 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Mogelijk moet u ICMP openen via de Windows-firewall van het apparaat dat u pingt met de volgende opdracht:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Als u wilt bevestigen dat de toegevoegde `route print` route zich in de routetabel bevindt, voert u de opdracht in, die de uitvoer retourneert die vergelijkbaar is met de volgende tekst:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    De route die wordt vermeld met *192.168.1.1* onder **Gateway**, is de route die er standaard is voor de primaire netwerkinterface. De route met *192.168.2.1* onder **Gateway,** is de route die u hebt toegevoegd.

## <a name="next-steps"></a>Volgende stappen
Bekijk [de vm-formaten van Windows](sizes.md) wanneer u een vm probeert te maken met meerdere NIC's. Besteed aandacht aan het maximum aantal NIC's dat elke VM-grootte ondersteunt. 


