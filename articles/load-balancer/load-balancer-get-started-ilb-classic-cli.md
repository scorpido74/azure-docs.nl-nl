---
title: Een interne Load Balancer maken - CLI van Azure classic
titlesuffix: Azure Load Balancer
description: Meer informatie over hoe u met de klassieke Azure CLI een interne load balancer maakt in het klassieke implementatiemodel
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c0b061da308f2e8635cf1722cb6a735624501374
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090978"
---
# <a name="get-started-creating-an-internal-load-balancer-using-the-azure-classic-cli"></a>Aan de slag met het maken van een interne load balancer met behulp van de klassieke Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure-CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloudservices](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources:  [Resource Manager en het klassieke model](../azure-resource-manager/resource-manager-deployment-model.md).  Dit artikel gaat over het gebruik van het klassieke implementatiemodel. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Lees [meer informatie over het uitvoeren van deze stappen met het Resource Manager-model](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Een interne load balancer-set maken voor virtuele machines

U maakt als volgt een interne load balancer-set en servers die verkeer naar deze load balancer verzenden:

1. Maak een instantie voor interne taakverdeling dat als eindpunt fungeert voor binnenkomend verkeer dat gelijkmatig moet worden verdeeld over de servers in een load balancer-set.
2. Voeg eindpunten toe die overeenkomen met de virtuele machines waarop het binnenkomende verkeer kan worden ontvangen.
3. Configureer de servers om het verkeer te verzenden naar het virtuele IP-adres (VIP) van de instantie voor interne taakverdeling.

## <a name="step-by-step-creating-an-internal-load-balancer-using-classic-cli"></a>Stapsgewijze instructies voor het maken van een interne load balancer met behulp van de klassieke CLI

In deze handleiding wordt beschreven hoe u een interne load balancer maakt op basis van bovenstaand scenario.

1. Als u de klassieke CLI nog nooit hebt gebruikt, raadpleegt u [De Azure CLI installeren en configureren](../cli-install-nodejs.md) en volgt u de instructies tot het punt waar u uw Azure-account en -abonnement moet selecteren.
2. Voer de opdracht **azure config mode** uit om over te schakelen naar de klassieke modus, zoals hieronder weergegeven.

    ```azurecli
    azure config mode asm
    ```

    Verwachte uitvoer:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Set met eindpunten en load balancer maken

In dit scenario wordt ervan uitgegaan dat de virtuele machines DB1 en DB2 zich in een cloudservice met de naam 'mytestcloud' bevinden. Beide virtuele machines maken gebruik van het virtuele netwerk 'testvnet' met subnet 'subnet-1'.

In deze handleiding wordt een interne load balancer-set gemaakt die poort 1433 als particuliere poort en 1433 als lokale poort gebruikt.

Dit is een algemeen scenario met virtuele SQL-machines aan de back-end die een interne load balancer gebruiken om ervoor te zorgen dat de databaseservers niet direct worden blootgesteld via een openbaar IP-adres.

### <a name="step-1"></a>Stap 1

Maak een interne load balancer-set met behulp van `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Zie `azure service internal-load-balancer --help` voor meer informatie.

U kunt de eigenschappen van de interne load balancer controleren met de opdracht `azure service internal-load-balancer list` *cloudservicenaam*.

Hier volgt een voorbeeld van de uitvoer:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Stap 2

U configureert de interne load balancer-set wanneer u het eerste eindpunt toevoegt. In deze stap kunt u het eindpunt, de virtuele machine en de testpoort koppelen aan de interne load balancer-set.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Stap 3

Controleer de configuratie van de load balancer met behulp van `azure vm show` *naam van virtuele machine*

```azurecli
azure vm show DB1
```

De uitvoer is als volgt:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Een eindpunt voor een extern bureaublad maken voor een virtuele machine

U kunt met `azure vm endpoint create` een eindpunt voor een extern bureaublad maken om voor een bepaalde virtuele machine netwerkverkeer van een openbare poort door te sturen naar een lokale poort.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Virtuele machine verwijderen uit load balancer

U kunt een virtuele machine verwijderen uit een interne load balancer-set door het bijbehorende eindpunt te verwijderen. Wanneer het eindpunt is verwijderd, maakt de virtuele machine geen deel meer uit van de load balancer-set.

Op basis van bovenstaand voorbeeld kunt u het eindpunt dat is gemaakt voor de virtuele machine DB1, met de opdracht `azure vm endpoint delete` verwijderen uit interne load balancer ilbset.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Zie `azure vm endpoint --help` voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Een distributiemodus voor de load balancer configureren met bron-IP-affiniteit](load-balancer-distribution-mode.md)

[TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)
