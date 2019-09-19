---
title: Een internetgerichte Load Balancer maken - Azure klassieke CLI
titlesuffix: Azure Load Balancer
description: Meer informatie over het maken van een internetgerichte load balancer in het klassieke implementatiemodel via klassieke Azure CLI
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
ms.openlocfilehash: 2d7f1971c6aff446f9bf8f983bd7859e831c1213
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090935"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Een internetgerichte Load Balancer (klassiek) maken in de klassieke Azure-CLI

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Klassieke versie van Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Voordat u met Azure-resources gaat werken, is het belang rijk te weten dat Azure momenteel twee implementatie modellen heeft: Azure Resource Manager en klassiek. Zorg ervoor dat u begrijpt wat [implementatiemodellen en hulpprogramma's](../azure-classic-rm.md) zijn voordat u met een Azure-resource gaat werken. U kunt de documentatie voor verschillende hulpprogramma's bekijken door op de tabbladen boven aan dit artikel te klikken. Dit artikel is van toepassing op het klassieke implementatiemodel. Hier vindt u [meer informatie over hoe u een internetgerichte load balancer maakt met Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>Een internetgerichte load balancer maken met CLI

In deze handleiding wordt beschreven hoe u een internetgerichte load balancer maakt op basis van bovenstaand scenario.

1. Als u klassieke Azure CLI nog nooit hebt gebruikt, raadpleegt u [De klassieke Azure CLI installeren en configureren](../cli-install-nodejs.md) en volgt u de instructies tot het punt waar u uw Azure-account en -abonnement moet selecteren.
2. Voer de opdracht **azure config mode** uit om over te schakelen naar de klassieke modus, zoals hieronder weergegeven.

    ```azurecli
    azure config mode asm
    ```

    Verwachte uitvoer:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Set met eindpunten en load balancer maken

In het scenario wordt ervan uitgegaan dat de virtuele machines 'web1' en 'web2' al zijn gemaakt.
In deze handleiding wordt een set met gelijke taakverdeling gemaakt die poort 80 als openbare poort en 80 als lokale poort gebruikt. Er is ook een testpoort geconfigureerd op poort 80 en de naam van de set met gelijke taakverdeling is 'lbset'.

### <a name="step-1"></a>Stap 1

Maak de eerste set met een eindpunt en gelijke taakverdeling met behulp van `azure network vm endpoint create` voor de virtuele machine 'web1'.

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>Stap 2

Voeg de tweede virtuele machine 'web2' aan de set met gelijke taakverdeling toe.

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>Stap 3

Controleer de configuratie van de load balancer met behulp van `azure vm show`.

```azurecli
azure vm show web1
```

De uitvoer is:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Een eindpunt voor een extern bureaublad maken voor een virtuele machine

U kunt met `azure vm endpoint create` een eindpunt voor een extern bureaublad maken om voor een bepaalde virtuele machine netwerkverkeer van een openbare poort door te sturen naar een lokale poort.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Virtuele machine verwijderen uit load balancer

U moet het eindpunt dat is gekoppeld aan de set met gelijke taakverdeling, verwijderen van de virtuele machine. Wanneer het eindpunt is verwijderd, maakt de virtuele machine geen deel meer uit van de set met gelijke taakverdeling.

Op basis van bovenstaand voorbeeld kunt u het eindpunt dat is gemaakt voor de virtuele machine 'web1', met de opdracht `azure vm endpoint delete` verwijderen uit de load balancer 'lbset'.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> U kunt meer opties voor het beheren van eindpunten verkennen met de opdracht `azure vm endpoint --help`

## <a name="next-steps"></a>Volgende stappen

[Aan de slag met het configureren van een interne load balancer](load-balancer-get-started-ilb-arm-ps.md)

[Een distributiemodus voor de load balancer configureren](load-balancer-distribution-mode.md)

[TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)
