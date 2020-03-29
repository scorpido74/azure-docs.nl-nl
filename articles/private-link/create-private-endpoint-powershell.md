---
title: Een Azure Private Endpoint maken met Azure PowerShell| Microsoft Documenten
description: Meer informatie over Azure Private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430351"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Een privéeindpunt maken met Azure PowerShell
Een privéeindpunt is de fundamentele bouwsteen voor privékoppelingen in Azure. Hiermee kunnen Azure-resources, zoals Virtuele Machines (VM's), privé communiceren met privékoppelingsbronnen. 

In deze Quickstart leert u hoe u een VM maakt op een Azure Virtual Network, een SQL Database Server met een Azure-privéeindpunt met Azure PowerShell. Vervolgens u veilig toegang krijgen tot de SQL Database Server van de VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw resources maken, moet u een resourcegroep maken die het virtuele netwerk en het privéeindpunt host met [Nieuw-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *WestUS-locatie* ge:

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
In deze sectie maakt u een virtueel netwerk en een subnet. Vervolgens koppelt u het subnet aan uw virtuele netwerk.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk voor uw privéeindpunt met [New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork) In het volgende voorbeeld wordt een virtueel netwerk met de naam *MyVirtualNetwork geopperd:*
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Een subnet toevoegen

Azure implementeert resources naar een subnet binnen een virtueel netwerk, dus u moet een subnet maken. Maak een subnetconfiguratie met de naam *mySubnet* met [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). In het volgende voorbeeld wordt een subnet met de naam *mySubnet* gemaakt met de vlag van het privé-eindpuntnetwerkbeleid ingesteld op **Uitgeschakeld**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Het is gemakkelijk om `PrivateEndpointNetworkPoliciesFlag` de parameter te `PrivateLinkServiceNetworkPoliciesFlag`verwarren met een andere beschikbare vlag, omdat ze beide lange woorden zijn en een vergelijkbaar uiterlijk hebben.  Zorg ervoor dat u de `PrivateEndpointNetworkPoliciesFlag`juiste gebruikt, .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Het subnet koppelen aan het virtuele netwerk

U de subnetconfiguratie naar het virtuele netwerk schrijven met [Set-AzVirtualNetwork.](/powershell/module/az.network/Set-azVirtualNetwork) Met deze opdracht maakt u het subnet:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM in het virtuele netwerk met [Nieuw-AzVM](/powershell/module/az.compute/new-azvm). Wanneer u de volgende opdracht uitvoert, wordt u gevraagd referenties op te geven. Voer een gebruikersnaam en wachtwoord voor de virtuele machine in:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

Met de optie `-AsJob` wordt de virtuele machine op de achtergrond gemaakt. U kunt doorgaan met de volgende stap.

Wanneer er op de achtergrond met het maken van de virtuele machine wordt begonnen, wordt er iets dergelijks weergegeven:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Een SQL Database Server maken 

Maak een SQL Database Server met de opdracht Nieuw-AzSqlServer. Houd er rekening mee dat de naam van uw SQL Database-server uniek moet zijn in Azure, dus vervang de tijdelijke aanduidingswaarde tussen haakjes door uw eigen unieke waarde:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

Privéeindpunt voor de SQL Database Server in uw virtuele netwerk met [Nieuwe-AzPrivateLinkServiceVerbinding:](/powershell/module/az.network/New-AzPrivateLinkServiceConnection) 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>De private DNS-zone configureren 
Maak een privé-DNS-zone voor SQL Database Server-domein en maak een koppelingskoppeling met het virtuele netwerk: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) om het openbare IP-adres van een virtuele machine op te halen. In dit voorbeeld wordt het openbare IP-adres van de *myVM* VM geretourneerd:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Open een opdrachtprompt op de lokale computer. Voer de opdracht mstsc uit. Vervang <publicIpAddress> door het openbare IP-adres dat in de laatste stap is geretourneerd: 


> [!NOTE]
> Als u deze opdrachten vanuit een PowerShell-prompt op de lokale computer hebt uitgevoerd en u met de Azure PowerShell-module versie 1.0 of hoger werkt, kunt u doorgaan in die interface.
```
mstsc /v:<publicIpAddress>
```

1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. 
2. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.
  > [!NOTE]
  > Mogelijk moet u Meer opties selecteren > Een ander account gebruiken om de referenties op te geven die u hebt ingevoerd toen u de vm maakte. 
  
3. Selecteer **OK**. 
4. Er kan een certificaatwaarschuwing worden weergegeven. Als dit het geval is, selecteert u **Ja** of **Doorgaan**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>SQL Database Server privé openen vanaf de VM

1. Open PowerShell in het extern bureaublad van myVM.
2. Voer `nslookup myserver.database.windows.net` in. 

    Je ontvangt een bericht dat vergelijkbaar is met dit:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. SQL Server Management Studio installeren
4. Voer in Verbinding maken met de server deze gegevens in of selecteer deze: Waardeservertype Selecteren databaseengine instellen.
      Servernaam Selecteer myserver.database.windows.net gebruikersnaam Voer een gebruikersnaam in die tijdens het maken wordt opgegeven.
      Wachtwoord Voer een wachtwoord in dat tijdens het maken is opgegeven.
      Wachtwoord onthouden Selecteer Ja.
5. Selecteer Verbinden.
6. Blader door Databases in het linkermenu. 
7. (Optioneel) Gegevens maken of query's maken uit mydatabase
8. Sluit de verbinding met extern bureaublad met *myVM*. 

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het privéeindpunt SQL Database-server en de VM, gebruikt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de brongroep en alle resources die deze heeft te verwijderen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Private Link](private-link-overview.md)
