---
title: Meerdere IP-adressen voor virtuele Azure-machines - PowerShell | Microsoft Documenten
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine met PowerShell. | Resourcebeheer
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: a8bd4e4779d94cfc22ac7726c9746fe755764033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279570"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Meerdere IP-adressen toewijzen aan virtuele machines met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) maakt via het Azure Resource Manager-implementatiemodel met PowerShell. Meerdere IP-adressen kunnen niet worden toegewezen aan resources die zijn gemaakt via het klassieke implementatiemodel. Lees het artikel [Implementatiemodellen begrijpen](../resource-manager-deployment-model.md) voor meer informatie over Azure-implementatiemodellen.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Een virtuele machine met meerdere IP-adressen maken

In de volgende stappen wordt uitgelegd hoe u een voorbeeld-vm maakt met meerdere IP-adressen, zoals beschreven in het scenario. Wijzig variabele waarden zoals vereist voor uw implementatie.

1. Open een PowerShell-opdrachtprompt en voltooi de resterende stappen in deze sectie binnen één PowerShell-sessie. Als PowerShell nog niet is geïnstalleerd en geconfigureerd, voert u de stappen uit in het artikel [Azure PowerShell installeren en configureren.](/powershell/azure/overview)
2. Log in op `Connect-AzAccount` uw account met de opdracht.
3. Vervang *myResourceGroup* en *westus* door een naam en locatie naar keuze. Maak een resourcegroep. Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Maak een virtueel netwerk (VNet) en subnet op dezelfde locatie als de resourcegroep:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Maak een netwerkbeveiligingsgroep (NSG) en een regel. De NSG beveiligt de VM met inkomende en uitgaande regels. In dit geval is er een binnenkomende regel gemaakt voor poort 3389, waarmee binnenkomende verbindingen met een extern bureaublad worden toegestaan.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definieer de primaire IP-configuratie voor de NIC. Wijzig 10.0.0.4 in een geldig adres in het subnet dat u hebt gemaakt, als u de eerder gedefinieerde waarde niet hebt gebruikt. Voordat u een statisch IP-adres toekent, wordt aanbevolen dat u eerst bevestigt dat het nog niet in gebruik is. Voer de `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`opdracht in . Als het adres beschikbaar is, retourneert de uitvoer *True*. Als deze niet beschikbaar is, *retourneert* de uitvoer False en een lijst met adressen die beschikbaar zijn. 

    Vervang in de volgende opdrachten ** \<replace-to-your-unique-name> door de unieke DNS-naam die u wilt gebruiken.** De naam moet uniek zijn voor alle openbare IP-adressen binnen een Azure-gebied. Dit is een optionele parameter. Het kan worden verwijderd als u alleen verbinding wilt maken met de VM met behulp van het openbare IP-adres.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Wanneer u meerdere IP-configuraties aan een NIC toewijst, moet één configuratie worden toegewezen als *de primaire .*

    > [!NOTE]
    > Openbare IP-adressen hebben een nominale vergoeding. Lees de pagina IP-adresprijzen voor meer informatie over de prijzen van [IP-adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Er is een limiet aan het aantal openbare IP-adressen dat in een abonnement kan worden gebruikt. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Definieer de secundaire IP-configuraties voor de NIC. U indien nodig configuraties toevoegen of verwijderen. Bij elke IP-configuratie moet een privé-IP-adres zijn toegewezen. Elke configuratie kan optioneel één openbaar IP-adres toegewezen hebben.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Maak de NIC en koppel de drie IP-configuraties eraan:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Hoewel alle configuraties in dit artikel aan één NIC zijn toegewezen, u meerdere IP-configuraties toewijzen aan elke NIC die aan de VM is gekoppeld. Lees het artikel Een vm maken met meerdere NIC's voor meer informatie over het maken van een vm [met meerdere NIC's.](../virtual-machines/windows/multiple-nics.md)

9. Maak de VM door de volgende opdrachten in te voeren:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Voeg de privé-IP-adressen toe aan het VM-besturingssysteem door de stappen voor uw besturingssysteem in het [gedeelte IP-adressen toevoegen aan een vm-besturingssysteemgedeelte](#os-config) van dit artikel in te vullen. Voeg de openbare IP-adressen niet toe aan het besturingssysteem.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-adressen toevoegen aan een virtuele machine

U privé- en openbare IP-adressen toevoegen aan de Azure-netwerkinterface door de volgende stappen uit te voeren. De voorbeelden in de volgende secties gaan ervan uit dat u al een VM hebt met de drie IP-configuraties die in het [scenario](#scenario) in dit artikel zijn beschreven, maar het is niet vereist dat u dat doet.

1. Open een PowerShell-opdrachtprompt en voltooi de resterende stappen in deze sectie binnen één PowerShell-sessie. Als PowerShell nog niet is geïnstalleerd en geconfigureerd, voert u de stappen uit in het artikel [Azure PowerShell installeren en configureren.](/powershell/azure/overview)
2. Wijzig de 'waarden' van de volgende $Variables in de naam van de NIC waaraan u IP-adres wilt toevoegen en de resourcegroep en locatie waarin de NIC bestaat:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Als u de naam van de NIC die u wilt wijzigen niet weet, voert u de volgende opdrachten in en wijzigt u vervolgens de waarden van de vorige variabelen:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Maak een variabele en stel deze in op de bestaande NIC door de volgende opdracht te typen:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Wijzig in de volgende opdrachten *MyVNet* en *MySubnet* in de namen van het VNet en subnet waaraan de NIC is verbonden. Voer de opdrachten in om de VNet- en subnetobjecten op te halen met wie de NIC is verbonden:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Als u de VNet- of subnetnaam niet kent waar de NIC mee is verbonden, voert u de volgende opdracht in:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Zoek in de uitvoer naar tekst die lijkt op de volgende voorbeelduitvoer:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    In deze output is *MyVnet* het VNet en *MySubnet* is het subnet waar de NIC mee verbonden is.

5. Voer de stappen uit in een van de volgende secties, op basis van uw vereisten:

   **Een privé-IP-adres toevoegen**

   Als u een privé-IP-adres aan een NIC wilt toevoegen, moet u een IP-configuratie maken. Met de volgende opdracht wordt een configuratie met een statisch IP-adres van 10.0.0.7 ges. Bij het opgeven van een statisch IP-adres moet het een ongebruikt adres voor het subnet zijn. Het wordt aanbevolen dat u eerst het adres test om `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` ervoor te zorgen dat het beschikbaar is door de opdracht in te voeren. Als het IP-adres beschikbaar is, retourneert de uitvoer *True*. Als deze niet beschikbaar is, *retourneert*de uitvoer False en een lijst met adressen die beschikbaar zijn.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Maak zoveel configuraties als u wilt, met behulp van unieke configuratienamen en privé-IP-adressen (voor configuraties met statische IP-adressen).

   Voeg het privé-IP-adres toe aan het VM-besturingssysteem door de stappen voor uw besturingssysteem in het [gedeelte IP-adressen toevoegen aan een vm-besturingssysteemgedeelte](#os-config) van dit artikel in te vullen.

   **Een openbaar IP-adres toevoegen**

   Een openbaar IP-adres wordt toegevoegd door een openbare IP-adresbron te koppelen aan een nieuwe IP-configuratie of een bestaande IP-configuratie. Voer de stappen uit in een van de volgende secties, zoals u dat wilt.

   > [!NOTE]
   > Openbare IP-adressen hebben een nominale vergoeding. Lees de pagina IP-adresprijzen voor meer informatie over de prijzen van [IP-adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Er is een limiet aan het aantal openbare IP-adressen dat in een abonnement kan worden gebruikt. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **De bron voor openbare IP-adres koppelen aan een nieuwe IP-configuratie**

   Wanneer u een openbaar IP-adres toevoegt in een nieuwe IP-configuratie, moet u ook een privé-IP-adres toevoegen, omdat alle IP-configuraties een privé-IP-adres moeten hebben. U een bestaande openbare IP-adresbron toevoegen of een nieuwe bron maken. Als u een nieuwe wilt maken, voert u de volgende opdracht in:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Als u een nieuwe IP-configuratie wilt maken met een statisch privé-IP-adres en de bijbehorende ip-adresbron *myPublicIp3,* voert u de volgende opdracht in:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **De bron voor openbare IP-adres koppelen aan een bestaande IP-configuratie**

   Een openbare IP-adresbron kan alleen worden gekoppeld aan een IP-configuratie die nog niet is gekoppeld. U bepalen of een IP-configuratie een gekoppeld openbaar IP-adres heeft door de volgende opdracht in te voeren:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   De uitvoer ziet er ongeveer als volgt uit:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Aangezien de kolom **PublicIpAddress** voor *IpConfig-3* leeg is, is er momenteel geen openbare IP-adresbron aan gekoppeld. U een bestaande openbare IP-adresbron toevoegen aan IpConfig-3 of de volgende opdracht invoeren om er een te maken:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Voer de volgende opdracht in om de openbare IP-adresbron te koppelen aan de bestaande IP-configuratie met de naam *IpConfig-3:*

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Stel de NIC in met de nieuwe IP-configuratie door de volgende opdracht in te voeren:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Bekijk de privé-IP-adressen en de openbare IP-adresbronnen die aan de NIC zijn toegewezen door de volgende opdracht in te voeren:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Voeg het privé-IP-adres toe aan het VM-besturingssysteem door de stappen voor uw besturingssysteem in het [gedeelte IP-adressen toevoegen aan een vm-besturingssysteemgedeelte](#os-config) van dit artikel in te vullen. Voeg het openbare IP-adres niet toe aan het besturingssysteem.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]