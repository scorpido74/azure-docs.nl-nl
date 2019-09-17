---
title: Een persoonlijk Azure-eind punt maken met behulp van Azure PowerShell | Microsoft Docs
description: Meer informatie over persoonlijke Azure-koppelingen
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: f9a2bd4c4ec176e018948a7a5a01603d075a7ea2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018008"
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
In deze sectie maakt u een virtueel netwerk en een subnet. Vervolgens koppelt u het subnet uw Virtual Network.

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

$adminSqlLogin = "SqlAdmin" $password = "ChangeYourAdminPassword1"

$Server = New-AzSqlServer-ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" ` -Location "WestCentralUS" '-SqlAdministratorCredentials $ (nieuw-object-TypeName System. Management. Automation. PSCredential-argument List $adminSqlLogin, $ ( ConvertTo-SecureString-String $password-AsPlainText-Force))

New-AzSqlDatabase-ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"` -databaseName "myda"`
    -RequestedServiceObjectiveName "S0" ` -samplenaam "AdventureWorksLT"


## <a name="create-a-private-endpoint"></a>Een persoonlijk eind punt maken

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
2. Enternslookup myserver.database.windows.net ontvangt u een bericht dat er ongeveer als volgt uitziet:  Azure PowerShellCopy-server:  Onbekend adres:  168.63.129.16 niet-bindend antwoord:  Naam: myserver.privatelink.database.windows.net-adres:  10.0.0.5-aliassen: myserver.database.windows.net
3. SQL Server Management Studio installeren
4. Typ of Selecteer in verbinding maken met server de volgende informatie: Waarde server type instellen data base-engine selecteren.
      Server naam selecteren myserver.database.windows.net gebruikers naam Geef een gebruikers naam op die tijdens het maken is opgegeven.
      Wacht woord voer een wacht woord in dat u hebt opgegeven tijdens het maken.
      Wacht woord onthouden selecteren Ja.
5. Selecteer verbinding maken.
6. Bladeren door data bases vanuit het menu links. 
7. Eventueel Gegevens uit mydatabase maken of er een query op uitvoeren
8. Sluit de verbinding met extern bureau blad met *myVM*. 

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het persoonlijke eind punt, SQL Database Server en de virtuele machine, gebruikt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resource groep en alle resources te verwijderen die het bevat:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
