---
title: DHCPv6 configureren voor Linux VM's
titleSuffix: Azure Load Balancer
description: In dit artikel leest u hoe u DHCPv6 configureert voor Linux VM's.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, public ip, native ipv6, mobile, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225324"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>DHCPv6 configureren voor Linux VM's


Sommige van de Linux-afbeeldingen met virtuele machines in de Azure Marketplace hebben standaard geen Dynamic Host Configuration Protocol-versie 6 (DHCPv6) geconfigureerd. Om IPv6 te ondersteunen, moet DHCPv6 zijn geconfigureerd in de Linux OS-distributie die u gebruikt. De verschillende Linux-distributies configureren DHCPv6 op verschillende manieren omdat ze verschillende pakketten gebruiken.

> [!NOTE]
> Recente SUSE Linux- en CoreOS-afbeeldingen in de Azure Marketplace zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze afbeeldingen gebruikt.

In dit document wordt beschreven hoe u DHCPv6 inschakelt, zodat uw Virtuele Linux-machine een IPv6-adres krijgt.

> [!WARNING]
> Door netwerkconfiguratiebestanden onjuist te bewerken, u de netwerktoegang tot uw vm verliezen. We raden u aan uw configuratiewijzigingen te testen op niet-productiesystemen. De instructies in dit artikel zijn getest op de nieuwste versies van de Linux-afbeeldingen in de Azure Marketplace. Voor meer gedetailleerde instructies, raadpleeg de documentatie voor uw eigen versie van Linux.

## <a name="ubuntu"></a>Ubuntu

1. Bewerk het bestand */etc/dhcp/dhclient6.conf* en voeg de volgende regel toe:

        timeout 10;

2. Bewerk de netwerkconfiguratie voor de eth0-interface met de volgende configuratie:

   * Op **Ubuntu 12.04 en 14.04**bewerk je het */etc/network/interfaces.d/eth0.cfg* bestand. 
   * Bewerk op **Ubuntu 16.04**het */etc/network/interfaces.d/50-cloud-init.cfg-bestand.*

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Te beginnen met Ubuntu 17.10 is het standaard netwerkconfiguratiemechanisme [NETPLAN]( https://netplan.io).  Op installatie/instantiatietijd leest NETPLAN de netwerkconfiguratie van YAML-configuratiebestanden op deze locatie: /{lib,etc,run}/netplan/*.yaml.

Voeg voor elke ethernetinterface een *dhcp6:true-instructie* toe in uw configuratie.  Bijvoorbeeld:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Tijdens het vroege opstarten, de netplan "netwerk renderer" schrijft configuratie te / draaien om de controle https://netplan.io/referencevan apparaten overhandigen aan de opgegeven netwerk daemon Voor referentie-informatie over NETPLAN, zie .
 
## <a name="debian"></a>Debian

1. Bewerk het bestand */etc/dhcp/dhclient6.conf* en voeg de volgende regel toe:

        timeout 10;

2. Bewerk het */etc/netwerk/interfacesbestand* en voeg de volgende configuratie toe:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS en Oracle Linux

1. Bewerk het */etc/sysconfig/netwerkbestand* en voeg de volgende parameter toe:

        NETWORKING_IPV6=yes

2. Bewerk het */etc/sysconfig/network-scripts/ifcfg-eth0-bestand* en voeg de volgende twee parameters toe:

        IPV6INIT=yes
        DHCPV6C=yes

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 en openSUSE 13

Recente SUSE Linux Enterprise Server (SLES) en openSUSE-afbeeldingen in Azure zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze afbeeldingen gebruikt. Als u een vm hebt die is gebaseerd op een oudere of aangepaste SUSE-afbeelding, gaat u als volgt te werk:

1. Installeer `dhcp-client` het pakket, indien nodig:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Bewerk het *bestand /etc/sysconfig/network/ifcfg-eth0* en voeg de volgende parameter toe:

        DHCLIENT6_MODE='managed'

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 en openSUSE Leap

Recente SLES- en openSUSE-afbeeldingen in Azure zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze afbeeldingen gebruikt. Als u een vm hebt die is gebaseerd op een oudere of aangepaste SUSE-afbeelding, gaat u als volgt te werk:

1. Bewerk het *bestand /etc/sysconfig/network/ifcfg-eth0* `#BOOTPROTO='dhcp4'` en vervang de parameter door de volgende waarde:

        BOOTPROTO='dhcp'

2. Voeg aan het bestand */etc/sysconfig/network/ifcfg-eth0* de volgende parameter toe:

        DHCLIENT6_MODE='managed'

3. Het IPv6-adres vernieuwen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Recente CoreOS-afbeeldingen in Azure zijn vooraf geconfigureerd met DHCPv6. Er zijn geen aanvullende wijzigingen vereist wanneer u deze afbeeldingen gebruikt. Als u een VM hebt op basis van een oudere of aangepaste CoreOS-afbeelding, gaat u als volgt te werk:

1. Bewerk het */etc/systemd/network/10_dhcp.network* file:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Het IPv6-adres vernieuwen:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
