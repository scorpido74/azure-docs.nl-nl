---
title: Een persoonlijk Azure-eind punt maken met behulp van Azure PowerShell | Microsoft Docs
description: Meer informatie over persoonlijke Azure-koppelingen
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: b3f809a21dab86ac50fcf7c194c886b05977e15e
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327109"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Een persoonlijk eind punt maken met Azure PowerShell
Een persoonlijk eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hiermee kunnen Azure-resources, zoals Virtual Machines (Vm's), privé communiceren met persoonlijke koppelings bronnen. 

In deze Quick Start leert u hoe u een virtuele machine kunt maken op een Azure-Virtual Network, een SQL Database-Server met een persoonlijk Azure-eind punt met behulp van Azure PowerShell. Daarna kunt u veilig toegang krijgen tot de SQL Database-Server vanaf de VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw resources kunt maken, moet u een resource groep maken die als host fungeert voor de Virtual Network en het persoonlijke eind punt met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *westus* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Een Virtual Network maken
In deze sectie maakt u een virtueel netwerk en een subnet. Vervolgens koppelt u het subnet aan uw Virtual Network.

### <a name="create-a-virtual-network"></a>Een Virtual Network maken

Maak een virtueel netwerk voor uw persoonlijke eind punt met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een Virtual Network gemaakt met de naam *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Een subnet toevoegen

Azure implementeert resources in een subnet binnen een Virtual Network, dus u moet een subnet maken. Maak een subnet-configuratie met de naam *mySubnet* met [add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). In het volgende voor beeld wordt een subnet met de naam *mySubnet* gemaakt waarbij de vlag voor het particuliere endpoint-netwerk beleid is ingesteld op **uitgeschakeld**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Het subnet aan de Virtual Network koppelen

U kunt de configuratie van het subnet naar het Virtual Network schrijven met [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Met deze opdracht maakt u het subnet:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele machine in de Virtual Network met [New-AzVM](/powershell/module/az.compute/new-azvm). Wanneer u de volgende opdracht uitvoert, wordt u gevraagd referenties op te geven. Voer een gebruikersnaam en wachtwoord voor de virtuele machine in:

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

## <a name="create-a-sql-database-server"></a>Een SQL Database-Server maken 

Maak een SQL Database-Server met behulp van de opdracht New-AzSqlServer. Houd er rekening mee dat de naam van uw SQL Database-Server uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding tussen vier Kante haken door uw eigen unieke waarde:

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

Persoonlijk eind punt voor de SQL Database-Server in uw Virtual Network met [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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

## <a name="configure-the-private-dns-zone"></a>De Privé-DNS zone configureren 
Maak een privé-DNS-zone voor SQL Database Server domein en maak een koppelings koppeling met het virtuele netwerk: 

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

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) om het open bare IP-adres van een virtuele machine te retour neren. In dit voor beeld wordt het open bare IP-adres van de *myVM* -VM geretourneerd:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Open een opdrachtprompt op de lokale computer. Voer de opdracht mstsc uit.  <publicIpAddress>Vervang door het open bare IP-adres dat u in de laatste stap hebt geretourneerd: 


> [!NOTE]
> Als u deze opdrachten vanuit een PowerShell-prompt op de lokale computer hebt uitgevoerd en u met de Azure PowerShell-module versie 1.0 of hoger werkt, kunt u doorgaan in die interface.
```
mstsc /v:<publicIpAddress>
```

1. Selecteer **verbinding maken**als u hierom wordt gevraagd. 
2. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.
  > [!NOTE]
  > Mogelijk moet u meer opties selecteren > een ander account gebruiken om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine. 
  
3. Selecteer **OK**. 
4. Er kan een certificaatwaarschuwing worden weergegeven. Als dit het geval is, selecteert u **Ja** of **door gaan**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>SQL Database Server privé benaderen vanuit de VM

1. Open Power shell in de Extern bureaublad van myVM.
2. Voer `nslookup myserver.database.windows.net`in. 

    U ontvangt een bericht dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install SQL Server Management Studio
4. In Connect to server, enter or select this information:
    Setting Value
      Server type   Select Database Engine.
      Server name   Select myserver.database.windows.net
      Username  Enter a username provided during creation.
      Password  Enter a password provided during creation.
      Remember password Select Yes.
5. Select Connect.
6. Browse Databases from left menu. 
7. (Optionally) Create or query information from mydatabase
8. Close the remote desktop connection to *myVM*. 

## Clean up resources 
When you're done using the private endpoint, SQL Database server and the VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all the resources it has:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
