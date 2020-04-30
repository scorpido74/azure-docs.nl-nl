---
title: 'Quick Start: een persoonlijke DNS-zone van Azure maken met behulp van Azure PowerShell'
description: In dit artikel maakt en test u een privé-DNS-zone en-record in Azure DNS. Dit is een stapsgewijze handleiding voor het maken en beheren van uw eerste privé-DNS-zone en -record met behulp van Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 0db53bcd6516bd52e2796deaa49fe0dd582e0588
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76939394"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Snelstartgids: een privé-Azure-zone maken met Azure PowerShell

Dit artikel leidt u stapsgewijs door de procedure voor het maken van uw eerste privé-DNS-zone en -record met behulp van Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone. Als u een privé-DNS-zone wilt publiceren naar uw virtuele netwerk, geeft u de lijst op met virtuele netwerken die zijn toegestaan om records in de zone om te zetten.  Deze worden *gekoppelde* virtuele netwerken genoemd. Als automatische registratie is ingeschakeld, werkt Azure DNS ook de zone records bij wanneer een virtuele machine wordt gemaakt, wordt het IP-adres gewijzigd of wordt dit verwijderd.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een privé-DNS-zone maken
> * Virtuele testmachines maken
> * Een extra DNS-record maken
> * De privézone testen

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u wilt, kunt u deze Snelstartgids volt ooien met behulp van [Azure cli](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>De resourcegroep maken

Eerst maakt u een resourcegroep die de DNS-zone gaat bevatten: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

Een DNS-zone wordt gemaakt met de cmdlet `New-AzPrivateDnsZone`.

In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam **myAzureVNet**. Vervolgens wordt er een DNS-zone met de naam **Private.contoso.com** gemaakt in de resource groep **MyAzureResourceGroup** , wordt de DNS-zone gekoppeld aan het virtuele **MyAzureVnet** -netwerk en wordt automatische registratie ingeschakeld.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Als u alleen een zone voor naam omzetting wilt maken (geen automatische registratie van hostnamen), kunt u de `-EnableRegistration` para meter weglaten.

### <a name="list-dns-private-zones"></a>Lijst met privé-DNS-zones weergeven

Door de zonenaam weg te laten uit `Get-AzPrivateDnsZone`, kunt u een opsomming maken van alle zones in een resourcegroep. Deze bewerking retourneert een matrix met zoneobjecten.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Door zowel de zonenaam als de resourcegroepnaam uit `Get-AzPrivateDnsZone` weg te laten, kunt u een opsomming maken van alle zones in het Azure-abonnement.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>De virtuele testmachines maken

Maak nu twee virtuele machines, zodat u uw DNS-privézone kunt testen:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Het duurt enkele minuten voordat dit is voltooid.

## <a name="create-an-additional-dns-record"></a>Een extra DNS-record maken

U kunt recordsets maken met behulp van de cmdlet `New-AzPrivateDnsRecordSet`. In het volgende voor beeld wordt een record gemaakt met de relatieve naam **db** in de DNS-zone **Private.contoso.com**in de resource groep **MyAzureResourceGroup**. De volledig gekwalificeerde naam van de recordset is **db.private.contoso.com**. Het recordtype is 'A', met IP-adres '10.2.0.4' en de TTL is 3600 seconden.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>DNS-records weergeven

Als u de DNS-records in uw zone wilt weergeven,voert u de volgende opdracht uit:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>De privézone testen

Nu kunt u de naam omzetting voor uw persoonlijke **Private.contoso.com** -zone testen.

### <a name="configure-vms-to-allow-inbound-icmp"></a>VM’s configureren voor het toestaan van inkomende ICMP

U kunt de pingopdracht gebruiken voor het testen van naamomzetting. Configureer de firewall dus op beide virtuele machines om inkomende ICMP-pakketten toe te staan.

1. Maak verbinding met myVM01 en open een Windows PowerShell-venster met beheerdersbevoegdheden.
2. Voer de volgende opdracht uit:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Herhaal voor myVM02.

### <a name="ping-the-vms-by-name"></a>De VM's pingen op naam

1. Ping vanuit de myVM02 Windows PowerShell-opdrachtprompt myVM01 met de naam van de automatisch geregistreerde hostnaam:

   ```
   ping myVM01.private.contoso.com
   ```

   De uitvoer ziet er ongeveer als volgt uit:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Ping nu de **db**-naam die u eerder hebt gemaakt:

   ```
   ping db.private.contoso.com
   ```

   De uitvoer ziet er ongeveer als volgt uit:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Alle resources verwijderen

Als u deze niet meer nodig hebt, verwijdert u de resource groep **MyAzureResourceGroup** om de resources te verwijderen die u in dit artikel hebt gemaakt.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Scenario's voor Azure DNS-privézones](private-dns-scenarios.md)
