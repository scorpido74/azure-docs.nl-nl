---
title: Route ring beheren in een Azure Virtual Network-Power shell-klassiek | Microsoft Docs
description: Meer informatie over het beheren van route ring in VNets met Power shell | Klassieke
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: f2f2c17740bd94629209c2bffb82689ecc931fc8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058773"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Route ring en virtuele apparaten (klassiek) met Power shell beheren

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure-CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klassiek)](virtual-network-create-udr-classic-ps.md)
> * [CLI (klassiek)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Voordat u met Azure-resources gaat werken, is het belang rijk te weten dat Azure momenteel twee implementatie modellen heeft: Azure Resource Manager en klassiek. Zorg ervoor dat u begrijpt wat [implementatiemodellen en hulpprogramma's](../azure-resource-manager/resource-manager-deployment-model.md) zijn voordat u met een Azure-resource gaat werken. U kunt de documentatie voor verschillende hulpprogram ma's weer geven door een optie boven in dit artikel te selecteren. Dit artikel is van toepassing op het klassieke implementatiemodel.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

In het voor beeld Azure PowerShell opdrachten hieronder wordt verwacht dat er al een eenvoudige omgeving is gemaakt op basis van het bovenstaande scenario. Als u de opdrachten wilt uitvoeren zoals ze worden weer gegeven in dit document, maakt u de omgeving die wordt weer gegeven in [een VNet (klassiek) maken met behulp van Power shell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>De UDR voor het front-end-subnet maken
Volg de onderstaande stappen om de route tabel en de route die nodig is voor het front-end-subnet te maken op basis van het bovenstaande scenario.

1. Voer de volgende opdracht uit om een route tabel voor het front-end-subnet te maken:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Voer de volgende opdracht uit om een route in de route tabel te maken voor het verzenden van al het verkeer dat bestemd is voor het back-end-subnet (192.168.2.0/24) naar de **fw1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Voer de volgende opdracht uit om de route tabel te koppelen aan het **frontend** -subnet:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>De UDR voor het back-end-subnet maken
Voer de volgende stappen uit om de route tabel en de route die nodig is voor het subnet van de back-end te maken op basis van het scenario:

1. Voer de volgende opdracht uit om een route tabel voor het back-end-subnet te maken:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Voer de volgende opdracht uit om een route in de route tabel te maken voor het verzenden van al het verkeer dat bestemd is voor het front-end-subnet (192.168.1.0/24) naar de **fw1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Voer de volgende opdracht uit om de route tabel te koppelen aan het **back-end** -subnet:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Door sturen via IP inschakelen op de FW1-VM

Voer de volgende stappen uit om door sturen via IP in te scha kelen in de FW1-VM:

1. Voer de volgende opdracht uit om de status van door sturen via IP te controleren:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Voer de volgende opdracht uit om door sturen via IP in te scha kelen voor de virtuele *fw1* -machine:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
