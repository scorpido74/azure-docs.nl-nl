---
title: Aangepaste Azure-routes gebruiken om de KMS-activering met geforceerde Tunneling in te scha kelen | Microsoft Docs
description: Laat zien hoe u aangepaste Azure-routes kunt gebruiken om KMS-activering in te scha kelen bij gebruik van geforceerde Tunneling in Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 8c0c8f5543af425091c821f28d08fcab01118fd4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090450"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows-activering mislukt in scenario met geforceerde tunneling

In dit artikel wordt beschreven hoe u het KMS-activerings probleem oplost dat zich kan voordoen wanneer u geforceerde tunneling inschakelt in de site-naar-site VPN-verbinding of ExpressRoute-scenario's.

## <a name="symptom"></a>Symptoom

U schakelt [geforceerde tunneling](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) in subnetten van het virtuele netwerk van Azure in om alle Internet verkeer terug te sturen naar uw on-premises netwerk. In dit scenario wordt Windows niet geactiveerd door de Azure virtual machines (Vm's) die Windows uitvoeren.

## <a name="cause"></a>Oorzaak

De Azure Windows-Vm's moeten verbinding maken met de Azure KMS-server voor Windows-activering. De activering vereist dat de activerings aanvraag afkomstig is van een openbaar IP-adres van Azure. In het scenario met geforceerde tunneling mislukt de activering omdat de activerings aanvraag afkomstig is van uw on-premises netwerk in plaats van vanuit een openbaar IP-adres van Azure.

## <a name="solution"></a>Oplossing

Om dit probleem op te lossen, gebruikt u de aangepaste Azure-route om activerings verkeer naar de Azure KMS-server te routeren.

Het IP-adres van de KMS-server voor de globale cloud van Azure is 23.102.135.246. De DNS-naam is kms.core.windows.net. Als u andere Azure-platforms gebruikt, zoals Azure Duitsland, moet u het IP-adres van de bijbehorende KMS-server gebruiken. Zie de volgende tabel voor meer informatie:

|Platform| KMS DNS|KMS-IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Duitsland|kms.core.cloudapi.de|51.4.143.248|
|Azure van de Amerikaanse overheid|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Voer de volgende stappen uit om de aangepaste route toe te voegen:

### <a name="for-resource-manager-vms"></a>Voor Resource Manager-Vm's

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

> [!NOTE] 
> Activering maakt gebruik van open bare IP-adressen en wordt beïnvloed door een standaard-SKU Load Balancer configuratie. Lees zorgvuldig de [uitgaande verbindingen in azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) om meer te weten te komen over de vereisten.

1. Open Azure PowerShell en [Meld u vervolgens aan bij uw Azure-abonnement](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Voer de volgende opdrachten uit:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Ga naar de virtuele machine met activerings problemen. Gebruik [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) om te testen of de KMS-server kan worden bereikt:

        psping kms.core.windows.net:1688

4. Probeer Windows te activeren en te controleren of het probleem is opgelost.

### <a name="for-classic-vms"></a>Voor klassieke Vm's

1. Open Azure PowerShell en [Meld u vervolgens aan bij uw Azure-abonnement](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Voer de volgende opdrachten uit:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Ga naar de virtuele machine met activerings problemen. Gebruik [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) om te testen of de KMS-server kan worden bereikt:

        psping kms.core.windows.net:1688

4. Probeer Windows te activeren en te controleren of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

- [Installatie sleutels voor KMS-client](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Activerings methoden bekijken en selecteren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
