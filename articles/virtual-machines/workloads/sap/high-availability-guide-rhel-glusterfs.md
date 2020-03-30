---
title: GlusterFS op Azure VM's op RHEL voor SAP NetWeaver | Microsoft Documenten
description: GlusterFS in Azure VM's in Red Hat Enterprise Linux voor SAP NetWeaver
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
ms.date: 08/16/2018
ms.author: radeltch
ms.openlocfilehash: 388a2db2c888be541d89c5f4274bd38b37e4ca28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591911"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>GlusterFS in Azure VM's in Red Hat Enterprise Linux voor SAP NetWeaver

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert en een GlusterFS-cluster installeert dat kan worden gebruikt om de gedeelde gegevens van een zeer beschikbaar SAP-systeem op te slaan.
Deze handleiding beschrijft hoe je GlusterFS instelt dat wordt gebruikt door twee SAP-systemen, NW1 en NW2. De namen van de resources (bijvoorbeeld virtuele machines, virtuele netwerken) in het voorbeeld gaan ervan uit dat u de [SAP-bestandsserversjabloon][template-file-server] met bronvoorvoegselglust hebt gebruikt. **glust**

Lees eerst de volgende SAP Notes en papers

* SAP Note [1928533], die heeft:
  * Lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten
  * Ondersteunde SAP-software en besturingssysteem- en databasecombinaties
  * Vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure

* SAP Note [2015553] bevat vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure.
* SAP Note [2002167] heeft aanbevolen OS-instellingen voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP HANA Richtlijnen voor Red Hat Enterprise Linux
* SAP Note [2178632] heeft gedetailleerde informatie over alle monitoringstatistieken die voor SAP in Azure worden gerapporteerd.
* SAP Note [2191498] heeft de vereiste SAP Host Agent-versie voor Linux in Azure.
* SAP Note [2243692] heeft informatie over SAP-licenties op Linux in Azure.
* SAP Note [1999351] heeft aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) heeft alle vereiste SAP Notes voor Linux.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines deployment voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Productdocumentatie voor Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Algemene RHEL-documentatie
  * [Overzicht van invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On-beheer met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-specifieke RHEL-documentatie:
  * [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - Microsoft Azure Virtual Machines als clusterleden](https://access.redhat.com/articles/3131341)
  * [Een Red Hat Enterprise Linux 7.4 (en hoger) cluster met hoge beschikbaarheid installeren en configureren op Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Overzicht

Om een hoge beschikbaarheid te bereiken, heeft SAP NetWeaver gedeelde opslag nodig. GlusterFS is geconfigureerd in een apart cluster en kan door meerdere SAP-systemen worden gebruikt.

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>GlusterFS instellen

U een Azure-sjabloon van github gebruiken om alle vereiste Azure-resources te implementeren, inclusief de virtuele machines, beschikbaarheidsset en netwerkinterfaces, of u de resources handmatig implementeren.

### <a name="deploy-linux-via-azure-template"></a>Linux implementeren via Azure-sjabloon

De Azure Marketplace bevat een afbeelding voor Red Hat Enterprise Linux die u gebruiken om nieuwe virtuele machines te implementeren.
U een van de quickstartsjablonen op github gebruiken om alle vereiste resources te implementeren. De sjabloon implementeert de virtuele machines, beschikbaarheidset etc. Volg de volgende stappen om de sjabloon te implementeren:

1. De [sjabloon VOOR SAP-bestandsserver openen][template-file-server] in de Azure-portal
1. Voer de volgende parameters in
   1. Bronvoorvoegsel  
      Voer het voorvoegsel in dat u wilt gebruiken. De waarde wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
   2. SAP-systeemtelling Voer het aantal SAP-systemen in dat deze bestandsserver zal gebruiken. Dit zal het vereiste aantal schijven etc. implementeren.
   3. Type besturingssysteem  
      Selecteer een van de Linux-distributies. Selecteer in dit voorbeeld RHEL 7
   4. Gebruikersnaam, beheerderswachtwoord of SSH-toets  
      Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om zich aan te melden bij de machine.
   5. Subnet-id  
      Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal uit als /abonnementen/**&lt;&gt;abonnements-ID**/resourceGroepen/**&lt;&gt;resourcegroepnaam**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuele netwerknaam&gt;**/subnetten/**&lt;subnetnaam&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux handmatig implementeren via Azure-portal

U moet eerst de virtuele machines voor dit cluster maken. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de backend pools. Wij raden [standaard load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)aan.  

1. Een resourcegroep maken
1. Een virtueel netwerk maken
1. Een beschikbaarheidsset maken  
   Domein voor maximale update instellen
1. Virtuele machine maken 1  
   Gebruik ten minste RHEL 7, in dit voorbeeld de Red Hat Enterprise Linux 7.4 afbeelding<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Beschikbaarheidsset selecteren die eerder is gemaakt  
1. Virtuele machine maken 2  
   Gebruik ten minste RHEL 7, in dit voorbeeld de Red Hat Enterprise Linux 7.4 afbeelding<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Beschikbaarheidsset selecteren die eerder is gemaakt  
1. Voeg voor elk SAP-systeem één gegevensschijf toe aan beide virtuele machines.

### <a name="configure-glusterfs"></a>GlusterFS configureren

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1, **[2]** - alleen van toepassing op knooppunt 2, **[3]** - alleen van toepassing op knooppunt 3.

1. **[A]** Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Het IP-adres en de hostnaam vervangen in de volgende opdrachten

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]** Registreren

   Registreer uw virtuele machines en bevestig deze aan een pool die repositories bevat voor RHEL 7 en GlusterFS

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]** GlusterFS-repo's inschakelen

   Om de vereiste pakketten te installeren, schakelt u de volgende opslagplaatsen in.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]** GlusterFS-pakketten installeren

   Installeer deze pakketten op alle GlusterFS-knooppunten

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   Start de knooppunten opnieuw op na de installatie.

1. **[A]** Firewall wijzigen

   Voeg firewallregels toe om clientverkeer toe te staan aan de GlusterFS-knooppunten.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]** GlusterFS-service inschakelen en starten

   Start de GlusterFS-service op alle knooppunten.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]** GluserFS maken

   De volgende opdrachten uitvoeren om het GlusterFS-cluster te maken

   <pre><code>sudo gluster peer probe glust-1
   sudo gluster peer probe glust-2
   
   # Check gluster peer status
   sudo gluster peer status
   
   # Number of Peers: 2
   # 
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Accepted peer request (Connected)
   # 
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Accepted peer request (Connected)
   </code></pre>

1. **[2]** Peer-status testen

   De peerstatus testen op het tweede knooppunt

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[3]** Peer-status testen

   De peerstatus testen op het derde knooppunt

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[A]** LVM maken

   In dit voorbeeld wordt de GlusterFS gebruikt voor twee SAP-systemen, NW1 en NW2. Gebruik de volgende opdrachten om LVM-configuraties voor deze SAP-systemen te maken.

   Deze opdrachten gebruiken voor NW1

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun0
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW1</b> /dev/disk/azure/scsi1/lun0
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW1</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW1</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW1</b>/aers
   sudo lvscan
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/aers
   
   sudo mkdir -p /rhs/<b>NW1</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW1</b>/trans
   sudo mkdir -p /rhs/<b>NW1</b>/sys
   sudo mkdir -p /rhs/<b>NW1</b>/ascs
   sudo mkdir -p /rhs/<b>NW1</b>/aers
   
   sudo chattr +i /rhs/<b>NW1</b>/sapmnt
   sudo chattr +i /rhs/<b>NW1</b>/trans
   sudo chattr +i /rhs/<b>NW1</b>/sys
   sudo chattr +i /rhs/<b>NW1</b>/ascs
   sudo chattr +i /rhs/<b>NW1</b>/aers

   echo -e "/dev/rhgs-<b>NW1</b>/sapmnt\t/rhs/<b>NW1</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/trans\t/rhs/<b>NW1</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/sys\t/rhs/<b>NW1</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/ascs\t/rhs/<b>NW1</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/aers\t/rhs/<b>NW1</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

   Deze opdrachten gebruiken voor NW2

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun1
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW2</b> /dev/disk/azure/scsi1/lun1
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW2</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW2</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW2</b>/aers
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/aers
   
   sudo mkdir -p /rhs/<b>NW2</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW2</b>/trans
   sudo mkdir -p /rhs/<b>NW2</b>/sys
   sudo mkdir -p /rhs/<b>NW2</b>/ascs
   sudo mkdir -p /rhs/<b>NW2</b>/aers
   
   sudo chattr +i /rhs/<b>NW2</b>/sapmnt
   sudo chattr +i /rhs/<b>NW2</b>/trans
   sudo chattr +i /rhs/<b>NW2</b>/sys
   sudo chattr +i /rhs/<b>NW2</b>/ascs
   sudo chattr +i /rhs/<b>NW2</b>/aers
   sudo lvscan
   
   echo -e "/dev/rhgs-<b>NW2</b>/sapmnt\t/rhs/<b>NW2</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/trans\t/rhs/<b>NW2</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/sys\t/rhs/<b>NW2</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/ascs\t/rhs/<b>NW2</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/aers\t/rhs/<b>NW2</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

1. **[1]** Het gedistribueerde volume maken

   Gebruik de volgende opdrachten om het GlusterFS-volume voor NW1 te maken en te starten.

   <pre><code>sudo gluster vol create <b>NW1</b>-sapmnt replica 3 glust-0:/rhs/<b>NW1</b>/sapmnt glust-1:/rhs/<b>NW1</b>/sapmnt glust-2:/rhs/<b>NW1</b>/sapmnt force
   sudo gluster vol create <b>NW1</b>-trans replica 3 glust-0:/rhs/<b>NW1</b>/trans glust-1:/rhs/<b>NW1</b>/trans glust-2:/rhs/<b>NW1</b>/trans force
   sudo gluster vol create <b>NW1</b>-sys replica 3 glust-0:/rhs/<b>NW1</b>/sys glust-1:/rhs/<b>NW1</b>/sys glust-2:/rhs/<b>NW1</b>/sys force
   sudo gluster vol create <b>NW1</b>-ascs replica 3 glust-0:/rhs/<b>NW1</b>/ascs glust-1:/rhs/<b>NW1</b>/ascs glust-2:/rhs/<b>NW1</b>/ascs force
   sudo gluster vol create <b>NW1</b>-aers replica 3 glust-0:/rhs/<b>NW1</b>/aers glust-1:/rhs/<b>NW1</b>/aers glust-2:/rhs/<b>NW1</b>/aers force
   
   sudo gluster volume start <b>NW1</b>-sapmnt
   sudo gluster volume start <b>NW1</b>-trans
   sudo gluster volume start <b>NW1</b>-sys
   sudo gluster volume start <b>NW1</b>-ascs
   sudo gluster volume start <b>NW1</b>-aers
   </code></pre>

   Gebruik de volgende opdrachten om het GlusterFS-volume voor NW2 te maken en te starten.

   <pre><code>sudo gluster vol create <b>NW2</b>-sapmnt replica 3 glust-0:/rhs/<b>NW2</b>/sapmnt glust-1:/rhs/<b>NW2</b>/sapmnt glust-2:/rhs/<b>NW2</b>/sapmnt force
   sudo gluster vol create <b>NW2</b>-trans replica 3 glust-0:/rhs/<b>NW2</b>/trans glust-1:/rhs/<b>NW2</b>/trans glust-2:/rhs/<b>NW2</b>/trans force
   sudo gluster vol create <b>NW2</b>-sys replica 3 glust-0:/rhs/<b>NW2</b>/sys glust-1:/rhs/<b>NW2</b>/sys glust-2:/rhs/<b>NW2</b>/sys force
   sudo gluster vol create <b>NW2</b>-ascs replica 3 glust-0:/rhs/<b>NW2</b>/ascs glust-1:/rhs/<b>NW2</b>/ascs glust-2:/rhs/<b>NW2</b>/ascs force
   sudo gluster vol create <b>NW2</b>-aers replica 3 glust-0:/rhs/<b>NW2</b>/aers glust-1:/rhs/<b>NW2</b>/aers glust-2:/rhs/<b>NW2</b>/aers force
   
   sudo gluster volume start <b>NW2</b>-sapmnt
   sudo gluster volume start <b>NW2</b>-trans
   sudo gluster volume start <b>NW2</b>-sys
   sudo gluster volume start <b>NW2</b>-ascs
   sudo gluster volume start <b>NW2</b>-aers
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [De SAP ASCS en database installeren](high-availability-guide-rhel.md)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [SAP HANA (grote exemplaren) hoge beschikbaarheid en noodherstel op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure.
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
