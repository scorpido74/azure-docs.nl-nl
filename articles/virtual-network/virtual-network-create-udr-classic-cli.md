---
title: Route ring beheren in een Azure Virtual Network-CLI-klassiek | Microsoft Docs
description: Meer informatie over het beheren van route ring in VNets met behulp van de Azure CLI in het klassieke implementatie model
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058791"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Route ring en virtuele apparaten (klassiek) gebruiken met behulp van de Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure-CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klassiek)](virtual-network-create-udr-classic-ps.md)
> * [CLI (klassiek)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U kunt ook [route ring en virtuele apparaten gebruiken in het Resource Manager-implementatie model](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Het voor beeld van Azure CLI-opdrachten hieronder verwacht een eenvoudige omgeving die al is gemaakt op basis van het bovenstaande scenario. Als u de opdrachten wilt uitvoeren zoals ze worden weer gegeven in dit document, maakt u de omgeving die wordt weer gegeven in [een VNet (klassiek) maken met behulp van de Azure cli](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>De UDR voor het front-end-subnet maken
Volg de onderstaande stappen om de route tabel en de route die nodig is voor het front-end-subnet te maken op basis van het bovenstaande scenario.

1. Voer de volgende opdracht uit om over te scha kelen naar de klassieke modus:

    ```azurecli
    azure config mode asm
    ```

    Uitvoer:

        info:    New mode is asm

2. Voer de volgende opdracht uit om een route tabel voor het front-end-subnet te maken:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Uitvoer:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parameters:
   
   * **-l (of --locatie)** . De Azure-regio waar de nieuwe NSG wordt gemaakt. In ons scenario is *westus*.
   * **-n (of --naam)** . De naam voor de nieuwe NSG. In ons scenario *NSG-front-end*.
3. Voer de volgende opdracht uit om een route in de route tabel te maken voor het verzenden van al het verkeer dat bestemd is voor het back-end-subnet (192.168.2.0/24) naar de **fw1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Uitvoer:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parameters:
   
   * **-r (of--route-table-name)** . De naam van de route tabel waarin de route wordt toegevoegd. In ons scenario *UDR-front-end*.
   * **-a (of --adresvoorvoegsel)** . Adres voorvoegsel voor het subnet waarnaar pakketten worden bestemd. In ons scenario *192.168.2.0/24*.
   * **-t (of--next-hop-type)** . Het type object verkeer waarnaar wordt verzonden. Mogelijke waarden zijn *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*of *none*.
   * **-p (of--volgende hop-IP-adres**). Het IP-adres voor de volgende hop. In ons scenario *192.168.0.4*.
4. Voer de volgende opdracht uit om de route tabel te koppelen die is gemaakt met het **frontend** -subnet:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Uitvoer:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parameters:
   
   * **-t (of--vnet-naam)** . De naam van het VNet waar het subnet zich bevindt. In ons scenario *TestVNet*.
   * **-n (of-subnet-name**). Naam van het subnet waaraan de route tabel wordt toegevoegd. In ons scenario *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>De UDR voor het back-end-subnet maken
Voer de volgende stappen uit om de route tabel en de route die nodig is voor het back-end-subnet te maken op basis van het scenario:

1. Voer de volgende opdracht uit om een route tabel voor het back-end-subnet te maken:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Voer de volgende opdracht uit om een route in de route tabel te maken voor het verzenden van al het verkeer dat bestemd is voor het front-end-subnet (192.168.1.0/24) naar de **fw1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Voer de volgende opdracht uit om de route tabel te koppelen aan het **back-end** -subnet:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

