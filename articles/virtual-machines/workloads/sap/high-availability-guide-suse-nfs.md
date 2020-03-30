---
title: Hoge beschikbaarheid voor NFS op Azure VM's op SLES | Microsoft Documenten
description: Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 4dce0a675f5841591da00a322b72718964d382ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348877"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert, het clusterframework installeert en een zeer beschikbare NFS-server installeert die kan worden gebruikt om de gedeelde gegevens van een zeer beschikbaar SAP-systeem op te slaan.
In deze handleiding wordt beschreven hoe u een zeer beschikbare NFS-server instellen die wordt gebruikt door twee SAP-systemen, NW1 en NW2. De namen van de resources (bijvoorbeeld virtuele machines, virtuele netwerken) in het voorbeeld gaan ervan uit dat u de [SJABLOON VOOR-BESTANDEN-bestandmet][template-file-server] bronvoorvoegsel **hebt**gebruikt.

Lees eerst de volgende SAP Notes en papers

* SAP Note [1928533], die heeft:
  * Lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten
  * Ondersteunde SAP-software en besturingssysteem- en databasecombinaties
  * Vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure

* SAP Note [2015553] bevat vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure.
* SAP Note [2205917] heeft os-instellingen aanbevolen voor SUSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [1944799] heeft SAP HANA-richtlijnen voor SUSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [2178632] heeft gedetailleerde informatie over alle monitoringstatistieken die voor SAP in Azure worden gerapporteerd.
* SAP Note [2191498] heeft de vereiste SAP Host Agent-versie voor Linux in Azure.
* SAP Note [2243692] heeft informatie over SAP-licenties op Linux in Azure.
* SAP Note [1984787] heeft algemene informatie over SUSE Linux Enterprise Server 12.
* SAP Note [1999351] heeft aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) heeft alle vereiste SAP Notes voor Linux.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines deployment voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SUSE Linux Enterprise High Availability Extension 12 SP3 best practices guides SUSE Linux Enterprise High Availability Extension 12 SP3 best practices guides SUSE Linux Enterprise High Availability Extension 12 SP3 best practices guides SUSE][sles-hae-guides]
  * Zeer beschikbare NFS-opslag met DRBD en Pacemaker
* [SUSE Linux Enterprise Server voor SAP Applications 12 SP3 best practices handleidingen][sles-for-sap-bp]
* [SUSE High Availability Extension 12 SP3 Release Notes][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Overzicht

Om een hoge beschikbaarheid te bereiken, heeft SAP NetWeaver een NFS-server nodig. De NFS-server is geconfigureerd in een afzonderlijk cluster en kan door meerdere SAP-systemen worden gebruikt.

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-nfs/ha-suse-nfs.png)

De NFS-server maakt gebruik van een speciale virtuele hostname en virtuele IP-adressen voor elk SAP-systeem dat deze NFS-server gebruikt. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. In de volgende lijst ziet u de configuratie van de load balancer.        

* Frontend-configuratie
  * IP-adres 10.0.0.4 voor NW1
  * IP-adres 10.0.0.5 voor NW2
* Backend-configuratie
  * Verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van het NFS-cluster
* Sondepoort
  * Poort 61000 voor NW1
  * Poort 61001 voor NW2
* Regels voor taakverdeling (bij gebruik van basisload balancer)
  * 2049 TCP voor NW1
  * 2049 UDP voor NW1
  * 2049 TCP voor NW2
  * 2049 UDP voor NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Een zeer beschikbare NFS-server instellen

U een Azure-sjabloon van GitHub gebruiken om alle vereiste Azure-resources te implementeren, inclusief de virtuele machines, de beschikbaarheidsset en de load balancer, of u de resources handmatig implementeren.

### <a name="deploy-linux-via-azure-template"></a>Linux implementeren via Azure-sjabloon

De Azure Marketplace bevat een afbeelding voor SUSE Linux Enterprise Server voor SAP-toepassingen 12 die u gebruiken om nieuwe virtuele machines te implementeren.
U een van de quickstartsjablonen op GitHub gebruiken om alle vereiste resources te implementeren. De sjabloon implementeert de virtuele machines, de load balancer, beschikbaarheidset etc. Volg de volgende stappen om de sjabloon te implementeren:

1. De [sjabloon VOOR SAP-bestandsserver openen][template-file-server] in de Azure-portal   
1. Voer de volgende parameters in
   1. Bronvoorvoegsel  
      Voer het voorvoegsel in dat u wilt gebruiken. De waarde wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
   2. AANTAL SAP-systemen  
      Voer het aantal SAP-systemen in dat deze bestandsserver gebruikt. Dit implementeert de vereiste hoeveelheid frontend configuraties, load-balancing regels, probe poorten, schijven etc.
   3. Type besturingssysteem  
      Selecteer een van de Linux-distributies. Selecteer in dit voorbeeld SLES 12
   4. Gebruikersnaam en beheerderswachtwoord voor beheerders  
      Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om zich aan te melden bij de machine.
   5. Subnet-id  
      Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal uit als /abonnementen/**&lt;&gt;abonnements-ID**/resourceGroepen/**&lt;&gt;resourcegroepnaam**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuele netwerknaam&gt;**/subnetten/**&lt;subnetnaam&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux handmatig implementeren via Azure-portal

U moet eerst de virtuele machines voor dit NFS-cluster maken. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de backend pools.

1. Een resourcegroep maken
1. Een virtueel netwerk maken
1. Een beschikbaarheidsset maken  
   Domein voor maximale update instellen
1. Maak Virtual Machine 1 Gebruik ten minste SLES4SAP 12 SP3, in dit voorbeeld wordt de SLES4SAP 12 SP3 BYOS-afbeelding SLES For SAP Applications 12 SP3 (BYOS) gebruikt  
   Beschikbaarheidsset selecteren die eerder is gemaakt  
1. Maak Virtual Machine 2 Gebruik ten minste SLES4SAP 12 SP3, in dit voorbeeld de SLES4SAP 12 SP3 BYOS-afbeelding  
   SLES Voor SAP Applications 12 SP3 (BYOS) wordt gebruikt  
   Beschikbaarheidsset selecteren die eerder is gemaakt  
1. Voeg voor elk SAP-systeem één gegevensschijf toe aan beide virtuele machines.
1. Maak een load balancer (intern). Wij raden [standaard load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)aan.  
   1. Volg deze instructies om standaard load balancer te maken:
      1. De ip-adressen aan de frontend maken
         1. IP-adres 10.0.0.4 voor NW1
            1. Open de load balancer, selecteer ip-pool aan frontend en klik op Toevoegen
            1. Voer de naam in van de nieuwe ip-pool aan de voorzijde (bijvoorbeeld **nw1-frontend)**
            1. De toewijzing instellen op Statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.4**)
            1. Klik op OK
         1. IP-adres 10.0.0.5 voor NW2
            * Herhaal de bovenstaande stappen voor NW2
      1. De backendpools maken
         1. Verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van het NFS-cluster
            1. Open de load balancer, selecteer backendpools en klik op Toevoegen
            1. Voer de naam in van de nieuwe backendpool (bijvoorbeeld **nw-backend)**
            1. Virtueel netwerk selecteren
            1. Klik op Een virtuele machine toevoegen
            1. Selecteer de virtuele machines van het NFS-cluster en hun IP-adressen.
            1. Klik op Toevoegen.
      1. De statussondes maken
         1. Poort 61000 voor NW1
            1. Open de load balancer, selecteer health probes en klik op Toevoegen
            1. Voer de naam van de nieuwe health probe (bijvoorbeeld **nw1-hp**)
            1. Selecteer TCP als protocol, poort 610**00,** houd Interval 5 en Niet-gezonde drempel 2
            1. Klik op OK
         1. Poort 61001 voor NW2
            * Herhaal de bovenstaande stappen om een gezondheidssonde voor NW2 te maken
      1. Regels voor taakverdeling
         1. Open de load balancer, selecteer load-balancing rules en klik op Toevoegen
         1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld **nw1-lb)**
         1. Selecteer de ip-adres, backendpool en statussonde die u eerder hebt gemaakt (bijvoorbeeld **nw1-frontend**. **nw-backend** en **nw1-pk**)
         1. Selecteer **HA-poorten**.
         1. Verhoog de idle time-out tot 30 minuten
         1. **Zorg ervoor dat zwevend IP-adres wordt ingeschakeld**
         1. Klik op OK
         * Herhaal de bovenstaande stappen om regel voor taakverdeling voor NW2 te maken
   1. Als uw scenario een basisload balancer vereist, volgt u ook de volgende instructies:
      1. De ip-adressen aan de frontend maken
         1. IP-adres 10.0.0.4 voor NW1
            1. Open de load balancer, selecteer ip-pool aan frontend en klik op Toevoegen
            1. Voer de naam in van de nieuwe ip-pool aan de voorzijde (bijvoorbeeld **nw1-frontend)**
            1. De toewijzing instellen op Statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.4**)
            1. Klik op OK
         1. IP-adres 10.0.0.5 voor NW2
            * Herhaal de bovenstaande stappen voor NW2
      1. De backendpools maken
         1. Verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van het NFS-cluster
            1. Open de load balancer, selecteer backendpools en klik op Toevoegen
            1. Voer de naam in van de nieuwe backendpool (bijvoorbeeld **nw-backend)**
            1. Klik op Een virtuele machine toevoegen
            1. Selecteer de beschikbaarheidsset die u eerder hebt gemaakt
            1. De virtuele machines van het NFS-cluster selecteren
            1. Klik op OK
      1. De statussondes maken
         1. Poort 61000 voor NW1
            1. Open de load balancer, selecteer health probes en klik op Toevoegen
            1. Voer de naam van de nieuwe health probe (bijvoorbeeld **nw1-hp**)
            1. Selecteer TCP als protocol, poort 610**00,** houd Interval 5 en Niet-gezonde drempel 2
            1. Klik op OK
         1. Poort 61001 voor NW2
            * Herhaal de bovenstaande stappen om een gezondheidssonde voor NW2 te maken
      1. Regels voor taakverdeling
         1. 2049 TCP voor NW1
            1. Open de load balancer, selecteer load balancing rules en klik op Toevoegen
            1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld **nw1-lb-2049**)
            1. Selecteer de ip-adres, backendpool en statussonde die u eerder hebt gemaakt (bijvoorbeeld **nw1-frontend**)
            1. Protocol **TCP**behouden, voer poort **2049** in
            1. Verhoog de idle time-out tot 30 minuten
            1. **Zorg ervoor dat zwevend IP-adres wordt ingeschakeld**
            1. Klik op OK
         1. 2049 UDP voor NW1
            * Herhaal de bovenstaande stappen voor poort 2049 en UDP voor NW1
         1. 2049 TCP voor NW2
            * Herhaal de bovenstaande stappen voor poort 2049 en TCP voor NW2
         1. 2049 UDP voor NW2
            * Herhaal de bovenstaande stappen voor poort 2049 en UDP voor NW2

> [!Note]
> Wanneer VM's zonder openbare IP-adressen in de backendpool van interne (geen openbare IP-adres) Standaard Azure-loadbalancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij extra configuratie wordt uitgevoerd om routering naar openbare eindpunten mogelijk te maken. Zie [Openbare eindpuntconnectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)met hoge beschikbaarheid voor meer informatie over het bereiken van uitgaande connectiviteit.  

> [!IMPORTANT]
> Schakel geen TCP-tijdstempels in op Azure VM's die achter Azure Load Balancer zijn geplaatst. Als u TCP-tijdstempels inschakelt, worden de statussprobes mislukt. Stel parameter **net.ipv4.tcp_timestamps** in op **0**. Zie [Health Probes van Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)meer informatie .

### <a name="create-pacemaker-cluster"></a>Pacemakercluster maken

Volg de stappen in [Het instellen van Pacemaker op SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) om een basispacemakercluster voor deze NFS-server te maken.

### <a name="configure-nfs-server"></a>NFS-server configureren

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Het IP-adres en de hostnaam vervangen in de volgende opdrachten

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** NFS-server inschakelen

   Het exportitem voor hoofdNFS maken

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Drbd-componenten installeren

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Een partitie maken voor de drbd-apparaten

   Alle beschikbare gegevensschijven weergeven

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Voorbeelduitvoer
   
   ```
   lun0  lun1
   ```

   Partities maken voor elke gegevensschijf

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** LVM-configuraties maken

   Alle beschikbare partities weergeven

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Voorbeelduitvoer
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   LVM-volumes maken voor elke partitie

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Drbd configureren

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Zorg ervoor dat het drbd.conf-bestand de volgende twee regels bevat

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   De globale drbd-configuratie wijzigen

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Voeg de volgende vermeldingen toe aan de handler en de nettosectie.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** De NFS drbd-apparaten maken

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   De configuratie voor het nieuwe drbd-apparaat en het afsluiten invoegen

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   De configuratie voor het nieuwe drbd-apparaat en het afsluiten invoegen

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Het drbd-apparaat maken en starten

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Eerste synchronisatie overslaan

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Het primaire knooppunt instellen

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Wacht tot de nieuwe drbd-apparaten zijn gesynchroniseerd

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Bestandssystemen maken op de drbd-apparaten

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** Setup drbd split-brain detection

   Bij het gebruik van drbd om gegevens van de ene host naar de andere te synchroniseren, kan een zogenaamde split brain optreden. Een gesplitst brein is een scenario waarbij beide clusterknooppunten het drbd-apparaat hebben gepromoot als het primaire en niet synchroon zijn gegaan. Het is misschien een zeldzame situatie, maar je wilt nog steeds omgaan en een gespleten brein zo snel mogelijk oplossen. Het is daarom belangrijk om op de hoogte te worden gesteld wanneer een gespleten brein is gebeurd.

   Lees [de officiële drbd documentatie](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) over het opzetten van een split brain notification.

   Het is ook mogelijk om automatisch te herstellen van een split brain scenario. Voor meer informatie, lees [Automatische split brain recovery beleid](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Clusterframework configureren

1. **[1]** Voeg de NFS drbd-apparaten voor SAP-systeem NW1 toe aan de clusterconfiguratie

   > [!IMPORTANT]
   > Recente tests brachten situaties aan het licht, waarin netcat stopt met reageren op verzoeken vanwege achterstand en de beperking van de afhandeling van slechts één verbinding. De netcat-bron luistert niet meer naar de aanvragen voor Azure Load balancer en het zwevende IP-adres is niet meer beschikbaar.  
   > Voor bestaande Pacemaker clusters, raden we in het verleden ter vervanging van netcat door socat. Momenteel raden we u aan azure-lb resource agent, die deel uitmaakt van pakket resource-agents, met de volgende pakketversie vereisten:
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018.a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resourceagents-4.3.0184.6ee15eb2-4.13.1 zijn.  
   >
   > Houd er rekening mee dat de wijziging korte downtime vereist.  
   > Als de configuratie al is gewijzigd om socat te gebruiken zoals beschreven in [Azure Load-Balancer Detection Hardening,](https://www.suse.com/support/kb/doc/?id=7024128)is er geen verplichting om onmiddellijk over te schakelen naar azure-lb resource agent.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Voeg de NFS drbd-apparaten voor SAP-systeem NW2 toe aan de clusterconfiguratie

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   De `crossmnt` optie `exportfs` in de clusterbronnen is aanwezig in onze documentatie voor achterwaartse compatibiliteit met oudere SLES-versies.  

1. **[1]** Onderhoudsmodus uitschakelen
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [De SAP ASCS en database installeren](high-availability-guide-suse.md)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
