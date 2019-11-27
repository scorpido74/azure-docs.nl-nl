---
title: Instellen van Pacemaker op SUSE Linux Enterprise Server in Azure | Microsoft Docs
description: Pacemaker op SUSE Linux Enterprise Server in Azure instellen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 8136e65636561079603986f0d6ff30bcbd68258f
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534231"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Pacemaker op SUSE Linux Enterprise Server in Azure instellen

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Er zijn twee opties voor het instellen van een cluster Pacemaker in Azure. U kunt een agent de eerste optie, die zorgt dat er opnieuw starten van een knooppunt via de Azure API's gebruiken of kunt u een apparaat SBD.

De SBD vereist ten minste één extra virtuele machine die fungeert als een iSCSI-doelserver en biedt een SBD-apparaat. Deze iSCSI-doelservers kunnen echter worden gedeeld met andere Pacemaker-clusters. Het voor deel van het gebruik van een SBD-apparaat is een snellere failover-tijd en als u gebruikmaakt van SBD-apparaten on-premises, hoeven er geen wijzigingen te worden aangebracht in de manier waarop u het pacemaker-cluster gebruikt. U kunt maximaal drie SBD apparaten voor een cluster Pacemaker gebruiken om toe te staan een SBD-apparaat niet meer beschikbaar zijn, bijvoorbeeld tijdens het patchen van het besturingssysteem van de iSCSI-doelserver. Als u meer dan één SBD apparaat per Pacemaker gebruiken wilt, moet u het implementeren van meerdere iSCSI-doelservers en verbinding maken met een SBD van elke iSCSI-doelserver. Het is raadzaam om met behulp van één SBD apparaat of drie. Pacemaker zich niet op een clusterknooppunt automatisch omheining als u slechts twee SBD apparaten configureren en een van beide niet beschikbaar is. Als u wilt dat omheining wanneer een iSCSI-doelserver niet actief is, hebt u drie SBD apparaten en daarom de drie iSCSI-doelservers gebruiken.

Als u niet wilt investeren in één extra virtuele machine, kunt u ook de Azure Fence-agent gebruiken. Het nadeel is dat een failover tussen 10 tot 15 minuten duren kan als een resource stoppen is mislukt of als de clusterknooppunten kunnen niet worden gecommuniceerd die elkaar meer.

![Pacemaker op SLES-overzicht](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Bij het plannen en implementeren van Linux pacemaker geclusterde knoop punten en SBD-apparaten is het essentieel voor de algehele betrouw baarheid van de volledige cluster configuratie waardoor de route ring tussen de betrokken Vm's en de virtuele machine (s) die het SBD-apparaat hosten, niet via wordt door gegeven alle andere apparaten, zoals [nva's](https://azure.microsoft.com/solutions/network-appliances/). Anders, problemen en onderhoudsgebeurtenissen met de NVA kunnen een nadelige invloed op de stabiliteit en betrouwbaarheid van de algehele clusterconfiguratie hebben. Om dergelijke obstakels te voor komen, definieert u geen routerings regels van Nva's of door de [gebruiker gedefinieerde routerings regels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) die verkeer routeren tussen geclusterde knoop punten en SBD-apparaten via nva's en vergelijk bare apparaten bij het plannen en implementeren van Linux pacemaker geclusterde knoop punten en SBD-apparaten. 
>

## <a name="sbd-fencing"></a>De eerste optie SBD

Volg deze stappen als u wilt een SBD-apparaat gebruiken voor de eerste optie.

### <a name="set-up-iscsi-target-servers"></a>ISCSI-doelservers instellen

U moet eerst de iSCSI-doel-virtuele machines maken. iSCSI-doelservers kunnen worden gedeeld met meerdere Pacemaker-clusters.

1. Implementeer nieuwe SLES 12 SP1 of hoger virtuele machines en maak er verbinding mee via ssh. De machines hoeven niet groot te zijn. De grootte van een virtuele machine, zoals Standard_E2s_v3 of Standard_D2s_v3 is voldoende. Zorg ervoor dat u de besturingssysteemschijf van de Premium-opslag.

Voer de volgende opdrachten uit op alle **virtuele iSCSI-doel machines**.

1. SLES bijwerken

   <pre><code>sudo zypper update
   </code></pre>

1. Verwijderen van pakketten

   Om te voorkomen dat een bekend probleem met targetcli en SLES 12 SP3, verwijdert u de volgende pakketten. U kunt fouten over pakketten die niet is gevonden negeren

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. ISCSI-doel-pakketten installeren

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. De iSCSI-doelservice inschakelen

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>ISCSI-apparaat maakt op de iSCSI-doelserver

Voer de volgende opdrachten uit op alle **virtuele iSCSI-doel machines** om de iSCSI-schijven te maken voor de clusters die door uw SAP-systemen worden gebruikt. In het volgende voorbeeld worden SBD apparaten voor meerdere clusters gemaakt. Hier ziet u hoe u een iSCSI-doelserver wilt gebruiken voor meerdere clusters. De apparaten SBD worden geplaatst op de besturingssysteemschijf. Zorg ervoor dat u er genoeg ruimte is.

**`nfs`** wordt gebruikt voor het identificeren van het NFS-cluster, **ascsnw1** wordt gebruikt om de ASCS-cluster van **NW1**te identificeren, **dbnw1** wordt gebruikt om het database cluster van **NW1**te identificeren, **NFS-0** en **NFS-1** zijn de hostnamen van de NFS-cluster knooppunten, **NW1-xscs-0** en **NW1-xscs-1** zijn de hostnamen van de **NW1** ASCS-cluster knooppunten en **NW1-DB-0** en **NW1-db-1** zijn de hostnamen van de database cluster knooppunten. Vervang deze door de hostnamen van de clusterknooppunten en wordt de SID van uw SAP-systeem.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

U kunt controleren als alles correct is ingesteld met

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>SBD apparaat instellen

Verbinding maken met het iSCSI-apparaat dat is gemaakt in de vorige stap uit het cluster.
Voer de volgende opdrachten op de knooppunten van het nieuwe cluster die u wilt maken.
De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** verbinding maken met de iSCSI-apparaten

   Het iSCSI- en SBD services eerst inschakelen.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Wijzig de naam van de initiator op het eerste knoop punt

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   De inhoud van het bestand moet overeenkomen met de ACL's die u hebt gebruikt bij het maken van het iSCSI-apparaat op de iSCSI-doelserver, bijvoorbeeld voor de NFS-server wijzigen.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Wijzig de naam van de initiator op het tweede knoop punt

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   De inhoud van het bestand moet overeenkomen met de ACL's die u hebt gebruikt bij het maken van het iSCSI-apparaat op de iSCSI-doelserver wijzigen

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** de iSCSI-service opnieuw starten

   Nu opnieuw opstarten van de iSCSI-service als de wijziging wilt toepassen

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Verbinding maken met iSCSI-apparaten. In het onderstaande voorbeeld 10.0.0.17 is het IP-adres van de iSCSI-doelserver en 3260 is de standaardpoort. <b>IQN. 2006-04. NFS. local: NFS</b> is een van de doel namen die worden weer gegeven wanneer u de eerste opdracht uitvoert (iscsiadm-m-detectie).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Zorg ervoor dat de apparaten van de iSCSI-beschikbaar zijn en noteer de naam van het apparaat (in het volgende voorbeeld/dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Nu de id's van de iSCSI-apparaten worden opgehaald.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   De opdracht lijst drie apparaat-id's voor elk apparaat SBD. Het is raadzaam het gebruik van de ID die met scsi-3, in het bovenstaande dit voorbeeld begint is

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** het SBD-apparaat maken

   De apparaat-ID van de iSCSI-apparaten gebruiken om te maken van de nieuwe SBD apparaten op het eerste clusterknooppunt.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** de SBD-configuratie aanpassen

   Open het configuratiebestand SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   De eigenschap van het apparaat SBD wijzigen, de integratie van pacemaker inschakelen en de startmodus van SBD wijzigen.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Het `softdog`-configuratie bestand maken

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Nu de module niet laden

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Clusterinstallatie van

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** SLES bijwerken

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** installatie onderdeel, vereist voor cluster bronnen

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** het besturings systeem configureren

   In sommige gevallen Pacemaker maakt veel processen en daardoor creditbedrag het toegestane aantal processen. In dat geval kan een heartbeat tussen de knooppunten van het mislukken en leiden tot failover van uw resources. Het is raadzaam om de maximale toegestane processen verhogen door de volgende parameter.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Reduceer de grootte van de vervuilde cache. Zie [lage schrijf prestaties op SLES 11/12-servers met grote hoeveel heden RAM-geheugen](https://www.suse.com/support/kb/doc/?id=7010287)voor meer informatie.

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Cloud-netconfig-Azure voor HA-cluster configureren

   Wijzig het configuratie bestand voor de netwerk interface zoals hieronder wordt weer gegeven om te voor komen dat de invoeg toepassing van het Cloud netwerk het virtuele IP-adres verwijdert (pacemaker moet de VIP-toewijzing beheren). Zie [SuSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)voor meer informatie. 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** SSH-toegang inschakelen

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** SSH-toegang inschakelen

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** SSH-toegang inschakelen

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** omheining-agents installeren
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Als SuSE Linux Enter prise server voor SAP 15 wordt gebruikt, moet u er rekening mee houden dat u aanvullende module wilt activeren en extra onderdelen wilt installeren. Dit is de vereiste voor het gebruik van de Azure Fence-agent. Voor meer informatie over SUSE-modules en-extensies raadpleegt u de [uitleg over modules en extensies](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Volg de instructies onderstaande om Azure python SDK te installeren. 

   De volgende instructies voor het installeren van de Azure python SDK zijn alleen van toepassing op SuSE Enter prise server voor SAP **15**.  

    - Als u gebruikmaakt van uw eigen abonnement, volgt u deze instructies  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Als u gebruikmaakt van betalen per gebruik-abonnement, volgt u deze instructies  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of aanpassen van de/etc/hosts op alle knooppunten. In dit voorbeeld laat zien hoe u het bestand/etc/hosts gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten. Het voordeel van het gebruik van/etc/hosts is dat het cluster wordt onafhankelijk van DNS, wat erop kan een single point of fouten te.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels/etc/hosts. De IP-adres en hostnaam zodat deze overeenkomen met uw omgeving wijzigen   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** cluster installeren

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** knoop punt toevoegen aan cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** hacluster wacht woord wijzigen in hetzelfde wacht woord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** corosync-instellingen aanpassen.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   De volgende vet inhoud toevoegen aan het bestand als de waarden niet er of een ander. Zorg ervoor dat u het token 30000 waarmee onderhoud met statusbehoud geheugen wijzigen. Zie [dit artikel voor Linux][virtual-machines-linux-maintenance] of [Windows][virtual-machines-windows-maintenance]voor meer informatie.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Start de service corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure omheining agent stonith instellen apparaat maken

Het stonith instellen-apparaat maakt gebruik van een Service-Principal te autoriseren op basis van Microsoft Azure. Volg deze stappen voor het maken van een Service-Principal.

1. Ga naar <https://portal.azure.com>
1. Open de Azure Active Directory-blade  
   Ga naar eigenschappen en noteer de map-ID. Dit is de **Tenant-id**.
1. Klik op App-registraties
1. Klik op nieuwe registratie
1. Voer een naam in, selecteer alleen accounts in deze organisatie Directory 
2. Selecteer het toepassings type ' Web ', voer een aanmeldings-URL in (bijvoorbeeld http:\//localhost) en klik op toevoegen  
   De aanmeldings-URL wordt niet gebruikt en kan geldige URL zijn
1. Selecteer certificaten en geheimen en klik vervolgens op nieuw client geheim
1. Voer een beschrijving in voor een nieuwe sleutel, selecteer nooit verloopt en klik op toevoegen
1. Noteer de waarde in. Dit wordt gebruikt als het **wacht woord** voor de Service-Principal
1. Selecteer overzicht. Noteer de toepassings-ID. Deze wordt gebruikt als de gebruikers naam (**aanmeldings-id** in de onderstaande stappen) van de Service-Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** een aangepaste rol maken voor de Fence-agent

De service-principal heeft standaard geen machtigingen voor toegang tot uw Azure-resources. U hoeft op te geven van de Service-Principal machtigingen voor starten en stoppen (toewijzing ongedaan maken) alle virtuele machines van het cluster. Als u de aangepaste rol nog niet hebt gemaakt, kunt u deze maken met behulp van [Power shell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) of [Azure cli](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli)

Gebruik de volgende inhoud voor het invoerbestand. U moet de inhoud voor uw abonnementen die is aangepast, c276fc76-9cd4-44c9-99a7-4fd71546436e en e91d47c4-76f3-4271-a796-21b4ecfe3624 vervangen door de id's van uw abonnement. Als u slechts één abonnement hebt, verwijdert u de tweede vermelding in AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** de aangepaste rol toewijzen aan de Service-Principal

De aangepaste rol 'Linux omheining Agent rol' die is gemaakt in het vorige hoofdstuk aan de Service-Principal toewijzen. Gebruik de rol owner niet meer.

1. Ga naar [https://portal.azure.com](https://portal.azure.com)
1. Open de blade alle resources
1. Selecteer de virtuele machine van het eerste clusterknooppunt
1. Klik op de Access control (IAM)
1. Klik op de roltoewijzing toevoegen
1. Selecteer de rol 'Linux omheining Agent rol'
1. Voer de naam van de toepassing die u hierboven hebt gemaakt
1. Op Opslaan klikken

Herhaal de bovenstaande stappen voor het tweede clusterknooppunt.

### <a name="1-create-the-stonith-devices"></a>**[1]** de STONITH-apparaten maken

Nadat u de machtigingen voor de virtuele machines hebt bewerkt, kunt u de apparaten stonith instellen in het cluster configureren.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Pacemaker standaardconfiguratie voor SBD

1. **[1]** het gebruik van een STONITH-apparaat inschakelen en de Fence-vertraging instellen

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemaker-configuratie voor geplande Azure-evenementen

Azure biedt [geplande gebeurtenissen](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Geplande gebeurtenissen worden via de meta gegevens service verschaft en bieden de tijd om de toepassing voor te bereiden op gebeurtenissen zoals het afsluiten van de VM, het opnieuw implementeren van VM'S, enzovoort. **[Azure-gebeurtenissen](https://github.com/ClusterLabs/resource-agents/pull/1161)** van de resource agent voor geplande Azure-gebeurtenissen. Als er gebeurtenissen worden gedetecteerd, probeert de agent alle resources op de betrokken VM te stoppen en deze naar een ander knoop punt in het cluster te verplaatsen. Om te voor komen dat er extra pacemaker-resources moeten worden geconfigureerd. 

1. **[A]** de **Azure-Events-** agent installeren. 

<pre><code>sudo zypper install resource-agents
</code></pre>

2. **[1]** Configureer de resources in pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Nadat u de pacemaker-resources voor Azure-Events agent hebt geconfigureerd en u het cluster in of uit de onderhouds modus plaatst, worden er mogelijk waarschuwings berichten weer gegeven als:  
     Waarschuwing: CIB-Boots trap: options: onbekend kenmerk ' hostName_ <strong>hostName</strong>'  
     Waarschuwing: CIB-Boots trap: opties: onbekend kenmerk ' Azure-events_globalPullState '  
     Waarschuwing: CIB-Boots trap: options: onbekend kenmerk ' hostName_ <strong>hostName</strong>'  
   > Deze waarschuwings berichten kunnen worden genegeerd.

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [Hoge Beschik baarheid voor NFS op Azure Vm's op SUSE Linux Enterprise Server][sles-nfs-guide]
* [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server voor SAP-toepassingen][sles-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
