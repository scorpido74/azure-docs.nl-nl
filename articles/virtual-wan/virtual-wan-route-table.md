---
title: 'Virtueel WAN: Virtuele hubroutetabel maken naar NVA: Azure PowerShell'
description: Virtuele WAN virtuele hub route tabel om verkeer te sturen naar een netwerk virtueel toestel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: a55e1453fe7fe4d135286b22dabf58d434762581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645103"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Een virtuele hub-routetabel maken om verkeer naar een virtueel netwerktoestel te sturen

In dit artikel ziet u hoe u verkeer van een virtuele hub naar een virtueel netwerktoestel sturen. 

![Virtual WAN-diagram](./media/virtual-wan-route-table/vwanroute.png)

In dit artikel leert u het volgende:

* Een WAN maken
* Een hub maken
* Hub-virtuele netwerkverbindingen maken
* Een hubroute maken
* Een routetabel maken
* De routetabel toepassen

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Controleer of u aan de volgende criteria hebt voldaan:

1. Je hebt een Network Virtual Appliance (NVA). Dit is een software van derden naar keuze die doorgaans vanuit Azure Marketplace in een virtueel netwerk is ingericht.
2. U hebt een privé-IP toegewezen aan de NVA-netwerkinterface. 
3. De NVA kan niet worden geïmplementeerd in de virtuele hub. Het moet worden geïmplementeerd in een aparte VNet. Voor dit artikel wordt de NVA VNet aangeduid als de 'DMZ VNet'.
4. De 'DMZ VNet' kan een of meer virtuele netwerken aangesloten. In dit artikel wordt dit VNet aangeduid als 'Indirect gesproken VNet'. Deze VNets kunnen met VNet worden aangesloten op de DMZ VNet.
5. Controleer of er al 2 VNets zijn gemaakt. Deze zullen worden gebruikt als spaak VNets. Voor dit artikel zijn de VNet-spaaksadresruimten 10.0.2.0/24 en 10.0.3.0/24. Zie [Een virtueel netwerk maken met PowerShell](../virtual-network/quick-create-powershell.md)als u informatie nodig hebt over het maken van een VNet.
6. Zorg ervoor dat er geen virtuele netwerkgateways in vnets zijn.

## <a name="1-sign-in"></a><a name="signin"></a>1. Log hier in

Zorg ervoor dat u de nieuwste versie van de PowerShell-cmdlets van Resource Manager installeert. Zie [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets. Dit is belangrijk omdat eerdere versies van de cmdlets niet de huidige waarden bevatten die u nodig hebt voor deze oefening.

1. Open uw PowerShell-console met verhoogde bevoegdheden en meld u aan bij uw Azure-account. Deze cmdlet vraagt u om de aanmeldingsreferenties. Nadat u zich hebt aanmeldt, worden uw accountinstellingen gedownload, zodat deze beschikbaar zijn voor Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Haal een lijst met uw Azure-abonnementen op.

   ```powershell
   Get-AzSubscription
   ```
3. Geef het abonnement op dat u wilt gebruiken.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. Resources maken

1. Maak een resourcegroep.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Maak een virtuele WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Maak een virtuele hub.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. Verbindingen maken

Maak virtuele hub-netwerkverbindingen van Indirect Spoke VNet en de DMZ VNet naar de virtuele hub.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Een virtuele hubroute maken

Voor dit artikel zijn de indirect gesproken VNet-adresruimten 10.0.2.0/24 en 10.0.3.0/24 en het privé-IP-adres van de DMZ NVA-netwerkinterface 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. Een virtuele hubroutetabel maken

Maak een virtuele hubroutetabel en pas de gemaakte route erop toe.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6.

Zet de wijzigingen in de virtuele hub.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
