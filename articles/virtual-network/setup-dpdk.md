---
title: DPDK in een Azure Linux VM | Microsoft Documenten
description: Meer informatie over het instellen van DPDK in een Linux virtuele machine.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c79c1fd687e329b97a854a3ff66a3cf95076b5d6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384225"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>DPDK instellen in een Virtuele Linux-machine

Data Plane Development Kit (DPDK) op Azure biedt een sneller framework voor het verwerken van gebruikersruimtepakketten voor prestatie-intensieve toepassingen. Dit framework omzeilt de kernel netwerkstack van de virtuele machine.

In typische pakketverwerking die de kernelnetwerkstack gebruikt, wordt het proces onderbroken. Wanneer de netwerkinterface binnenkomende pakketten ontvangt, is er een kernelinterrupt om het pakket te verwerken en schakelt een contextover van de kernelruimte naar de gebruikersruimte. DPDK elimineert contextswitching en de interrupt-gestuurde methode ten gunste van een implementatie in de gebruikersruimte die gebruikmaakt van stuurprogramma's in de pollmodus voor snelle pakketverwerking.

DPDK bestaat uit sets gebruikersruimtebibliotheken die toegang bieden tot bronnen op een lager niveau. Deze bronnen kunnen hardware, logische kernen, geheugenbeheer en stuurprogramma's voor de enquêtemodus voor netwerkinterfacekaarten bevatten.

DPDK kan worden uitgevoerd op virtuele Azure-machines die meerdere distributies van besturingssystemen ondersteunen. DPDK biedt belangrijke prestatiedifferentiatie bij het stimuleren van netwerkfunctievirtualisatie-implementaties. Deze implementaties kunnen de vorm aannemen van netwerkvirtuele apparaten (NVA's), zoals virtuele routers, firewalls, VPN's, load balancers, evolved packet cores en denial-of-service (DDoS) applicaties.

## <a name="benefit"></a>Voordeel

**Hogere pakketten per seconde (PPS)**: Het omzeilen van de kernel en het overnemen van pakketten in de gebruikersruimte vermindert het aantal cyclussen door contextswitches te elimineren. Het verbetert ook de snelheid van pakketten die per seconde worden verwerkt in Azure Linux virtuele machines.


## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende distributies van de Azure Marketplace worden ondersteund:

| Linux OS     | Kernelversie               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8,27-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**Ondersteuning voor aangepaste kernels**

Zie [Patches voor het bouwen van een Op Azure afgestemde Linux-kernel voor elke Linux-kernel](https://github.com/microsoft/azure-linux-kernel)versie die niet wordt vermeld. Voor meer informatie [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)u ook contact opnemen met. 

## <a name="region-support"></a>Ondersteuning voor regio's

Alle Azure-regio's ondersteunen DPDK.

## <a name="prerequisites"></a>Vereisten

Versnelde netwerken moeten worden ingeschakeld op een Linux virtuele machine. De virtuele machine moet ten minste twee netwerkinterfaces hebben, met één interface voor beheer. Meer informatie over het [maken van een Virtuele Linux-machine met versnelde netwerkingeschakeld.](create-vm-accelerated-networking-cli.md)

## <a name="install-dpdk-dependencies"></a>DPDK-afhankelijkheden installeren

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure-kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Standaardkernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>De virtuele machineomgeving (één keer) instellen

1. [Download de nieuwste DPDK](https://core.dpdk.org/download). Versie 18.11 LTS of 19.11 LTS is vereist voor Azure.
2. Bouw de standaard `make config T=x86_64-native-linuxapp-gcc`config met .
3. Schakel Mellanox PMD's in `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`de gegenereerde config in met .
4. Compileren `make`met .
5. Installeren `make install DESTDIR=<output folder>`met .

## <a name="configure-the-runtime-environment"></a>De runtime-omgeving configureren

Voer na het opnieuw opstarten de volgende opdrachten één keer uit:

1. Enorme pagina's

   * Configureer enorme pagina door de volgende opdracht uit te voeren, één keer voor elk numa-knooppunt:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Maak een map `mkdir /mnt/huge`voor montage met .
   * Mount enormepagina's met `mount -t hugetlbfs nodev /mnt/huge`.
   * Controleer of hugepages `grep Huge /proc/meminfo`zijn gereserveerd met .

     > [OPMERKING] Er is een manier om het grub-bestand te wijzigen, zodat hugepages worden gereserveerd op het opstarten door het volgen van de [instructies](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) voor de DPDK. De instructies staan onderaan de pagina. Wanneer u een virtuele Azure Linux-machine gebruikt, wijzigt u bestanden onder **/etc/config/grub.d** om enorme pagina's te reserveren voor reboots.

2. MAC & IP-adressen: gebruik om `ifconfig –a` het MAC- en IP-adres van de netwerkinterfaces weer te geven. De *VF* VF-netwerkinterface en *netvsc-netwerkinterface* hebben hetzelfde MAC-adres, maar alleen de *NETVSC-netwerkinterface* heeft een IP-adres. *VF-interfaces* worden uitgevoerd als ondergeschikte interfaces van *NETVSC-interfaces.*

3. PCI-adressen

   * Gebruik `ethtool -i <vf interface name>` om erachter te komen welk PCI-adres te gebruiken voor *VF*.
   * Als *eth0* netwerkheeft versneld, zorg er dan voor dat testpmd niet per ongeluk het *VF* pci-apparaat voor *eth0*overneemt. Als de DPDK-toepassing per ongeluk de beheernetwerkinterface overneemt en ervoor zorgt dat u uw SSH-verbinding verliest, gebruikt u de seriële console om de DPDK-toepassing te stoppen. U de seriële console ook gebruiken om de virtuele machine te stoppen of te starten.

4. Laad *ibuverbs* op `modprobe -a ib_uverbs`elke reboot met . Voor SLES 15 alleen, `modprobe -a mlx4_ib`ook laden *mlx4_ib* met .

## <a name="failsafe-pmd"></a>Failsafe PMD

DPDK-toepassingen moeten over de failsafe PMD worden uitgevoerd die in Azure wordt weergegeven. Als de toepassing rechtstreeks over de *VF* PMD loopt, ontvangt deze niet **alle** pakketten die zijn bestemd voor de VM, omdat sommige pakketten via de synthetische interface worden weergegeven. 

Als u een DPDK-toepassing uitvoert via de failsafe PMD, garandeert dit dat de toepassing alle pakketten ontvangt die zijn bestemd voor de toepassing. Het zorgt er ook voor dat de toepassing blijft draaien in de DPDK-modus, zelfs als de VF wordt ingetrokken wanneer de host wordt onderhouden. Zie [Fail-safe poll mode driver library](https://doc.dpdk.org/guides/nics/fail_safe.html)voor meer informatie over failsafe PMD.

## <a name="run-testpmd"></a>Testpmd uitvoeren

Als u testpmd wilt `sudo` uitvoeren in de hoofdmodus, gebruikt u vóór de *testpmd-opdracht.*

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Sanity check, failsafe adapter initialisatie

1. Voer de volgende opdrachten uit om één testpmd-toepassing met één poort te starten:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Voer de volgende opdrachten uit om een testpmd-toepassing met twee poorten te starten:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Als u testpmd uitvoert met meer dan `--vdev` twee NIC's, volgt het argument dit patroon: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Nadat het is gestart, voert u de poortgegevens uit. `show port info all` U ziet een of twee DPDK-poorten die net_failsafe zijn (niet *net_mlx4).*
4.  Gebruiken `start <port> /stop <port>` om verkeer te starten.

De vorige opdrachten starten *testpmd* in de interactieve modus, die wordt aanbevolen voor het uitproberen van testpmd-opdrachten.

### <a name="basic-single-sendersingle-receiver"></a>Basic: Single sender/single receiver Basic: Single sender/single receiver Basic: Single sender/single receiver Basic:

In de volgende opdrachten worden de pakketten per seconde periodiek afgedrukt:

1. Voer aan de TX-kant de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Voer aan de RX-kant de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Wanneer u de vorige opdrachten uitvoert op een virtuele machine, wijzigt u *IP_SRC_ADDR* en *IP_DST_ADDR* in `app/test-pmd/txonly.c` om het werkelijke IP-adres van de virtuele machines te evenaren voordat u compileert. Anders worden de pakketten gedropt voordat ze de ontvanger bereiken.

### <a name="advanced-single-sendersingle-forwarder"></a>Geavanceerd: enkele afzender/enkele expeditei
In de volgende opdrachten worden de pakketten per seconde periodiek afgedrukt:

1. Voer aan de TX-kant de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Voer aan de FWD-kant de volgende opdracht uit:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Wanneer u de vorige opdrachten uitvoert op een virtuele machine, wijzigt u *IP_SRC_ADDR* en *IP_DST_ADDR* in `app/test-pmd/txonly.c` om het werkelijke IP-adres van de virtuele machines te evenaren voordat u compileert. Anders worden de pakketten verwijderd voordat ze de expediteer bereiken. U geen derde machine doorgestuurd verkeer laten ontvangen, omdat de *testpmd-expediteer* de laag-3-adressen niet wijzigt, tenzij u enkele codewijzigingen aanbrengt.

## <a name="references"></a>Verwijzingen

* [EAL-opties](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd-opdrachten](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
