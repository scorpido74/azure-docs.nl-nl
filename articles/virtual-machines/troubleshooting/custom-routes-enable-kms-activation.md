---
title: Gebruik aangepaste Azure-routes om KMS-activering in te schakelen met geforceerde tunneling | Microsoft Documenten
description: Hier ziet u hoe u aangepaste Azure-routes gebruikt om KMS-activering in te schakelen bij het gebruik van gedwongen tunneling in Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 90034a56fcf5211059d37270e12391249f7a16b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920158"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows-activering mislukt in geforceerd tunnelscenario

In dit artikel wordt beschreven hoe u het KMS-activeringsprobleem oplossen dat u ondervinden wanneer u gedwongen tunneling inschakelt in vpn-verbindings- of ExpressRoute-scenario's van site-to-site.

## <a name="symptom"></a>Symptoom

U maakt [gedwongen tunneling](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) op virtuele netwerksubnetten van Azure mogelijk om al het internetgebonden verkeer terug te leiden naar uw on-premises netwerk. In dit scenario kunnen de Virtuele Azure-machines (VM's) waarop Windows wordt uitgevoerd, Windows niet activeren.

## <a name="cause"></a>Oorzaak

De Azure Windows VM's moeten verbinding maken met de Azure KMS-server voor Windows-activering. De activering vereist dat de activeringsaanvraag afkomstig is van een openbaar IP-adres van Azure. In het scenario voor gedwongen tunneling mislukt de activering omdat de activeringsaanvraag afkomstig is van uw on-premises netwerk in plaats van van een openbaar IP-adres in Azure.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, gebruikt u de aangepaste Azure-route om activeringsverkeer naar de Azure KMS-server te leiden.

Het IP-adres van de KMS-server voor de Azure Global-cloud is 23.102.135.246. De DNS-naam is kms.core.windows.net. Als u andere Azure-platforms gebruikt, zoals Azure Germany, moet u het IP-adres van de bijbehorende KMS-server gebruiken. Zie de volgende tabel voor meer informatie:

|Platform| DNS-KMS|KMS-IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Duitsland|kms.core.cloudapi.de|51.4.143.248|
|Azure van de Amerikaanse overheid|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Voer de volgende stappen uit om de aangepaste route toe te voegen:

### <a name="for-resource-manager-vms"></a>Vm's voor resourcebeheer

 

> [!NOTE] 
> Activering maakt gebruik van openbare IP-adressen en wordt beïnvloed door een standaard Configuratie van sku-load balancer. Controleer [uitgaande verbindingen in Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) zorgvuldig om meer te weten te komen over de vereisten.

1. Open Azure PowerShell en [meld u aan bij uw Azure-abonnement.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
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
3. Ga naar de VM met activeringsproblemen. Gebruik [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) om te testen of deze de KMS-server kan bereiken:

        psping kms.core.windows.net:1688

4. Probeer Windows te activeren en kijk of het probleem is opgelost.

### <a name="for-classic-vms"></a>Voor klassieke VM's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Open Azure PowerShell en [meld u aan bij uw Azure-abonnement.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
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

3. Ga naar de VM met activeringsproblemen. Gebruik [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) om te testen of deze de KMS-server kan bereiken:

        psping kms.core.windows.net:1688

4. Probeer Windows te activeren en kijk of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

- [Installatiecodes voor KMS-client](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Activeringsmethoden controleren en selecteren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
