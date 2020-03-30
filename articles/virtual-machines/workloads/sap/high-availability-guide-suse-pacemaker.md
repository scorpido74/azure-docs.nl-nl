---
title: Pacemaker instellen op SLES in Azure | Microsoft Documenten
description: Pacemaker instellen op SUSE Linux Enterprise Server in Azure
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
ms.date: 03/17/2020
ms.author: radeltch
ms.openlocfilehash: 9d3d0ddbd1282827f17cd82228fcf0f3fba3a60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471979"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Pacemaker instellen op SUSE Linux Enterprise Server in Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Er zijn twee opties om een Pacemaker-cluster in Azure in te stellen. U een hekwerkagent gebruiken, die zorgt voor het opnieuw opstarten van een mislukt knooppunt via de Azure API's of u een SBD-apparaat gebruiken.

Het SBD-apparaat vereist ten minste één extra virtuele machine die fungeert als een iSCSI-doelserver en een SBD-apparaat biedt. Deze iSCSI-doelservers kunnen echter worden gedeeld met andere Pacemaker-clusters. Het voordeel van het gebruik van een SBD-apparaat is een snellere failovertijd en als u on-premises SBD-apparaten gebruikt, hoeft u geen wijzigingen aan te brengen in de manier waarop u het pacemakercluster bedient. U maximaal drie SBD-apparaten gebruiken voor een Pacemaker-cluster, zodat een SBD-apparaat niet meer beschikbaar is, bijvoorbeeld tijdens het patchen van het besturingssysteem van de iSCSI-doelserver. Als u meer dan één SBD-apparaat per pacemaker wilt gebruiken, moet u meerdere iSCSI-doelservers implementeren en één SBD aansluiten vanaf elke iSCSI-doelserver. We raden u aan één Of drie SBD-apparaten te gebruiken. Pacemaker kan een clusterknooppunt niet automatisch afschermen als u slechts twee SBD-apparaten configureert en een van deze apparaten niet beschikbaar is. Als u wilt kunnen afschermen wanneer één iSCSI-doelserver uitvalt, moet u drie SBD-apparaten en dus drie iSCSI-doelservers gebruiken.

Als u niet wilt investeren in één extra virtuele machine, u ook de Azure Fence-agent gebruiken. Het nadeel is dat een failover tussen de 10 en 15 minuten kan duren als een resourcestop mislukt of de clusterknooppunten niet meer kunnen communiceren welke elkaar.

![Pacemaker op SLES overzicht](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Bij het plannen en implementeren van geclusterde Linux Pacemaker-knooppunten en SBD-apparaten is het essentieel voor de algehele betrouwbaarheid van de volledige clusterconfiguratie dat de routering tussen de betrokken VM's en de VM(s) die het SBD-apparaat(en) hosten, niet door andere apparaten zoals [NVA's](https://azure.microsoft.com/solutions/network-appliances/)gaat. Anders kunnen problemen en onderhoudsgebeurtenissen met de NVA een negatief effect hebben op de stabiliteit en betrouwbaarheid van de algehele clusterconfiguratie. Om dergelijke obstakels te voorkomen, definieer t u routeringsregels van NVA's of [door de gebruiker gedefinieerde routeringsregels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) die verkeer tussen geclusterde knooppunten en SBD-apparaten door nva's en vergelijkbare apparaten leiden bij het plannen en implementeren van geclusterde Linux Pacemaker-knooppunten en SBD-apparaten. 
>

## <a name="sbd-fencing"></a>SBD hekwerk

Volg deze stappen als u een SBD-apparaat wilt gebruiken voor schermen.

### <a name="set-up-iscsi-target-servers"></a>ISCSI-doelservers instellen

U moet eerst de virtuele iSCSI-doelmachines maken. iSCSI-doelservers kunnen worden gedeeld met meerdere Pacemaker-clusters.

1. Implementeer nieuwe SLES 12 SP1 of hogere virtuele machines en maak er verbinding mee via ssh. De machines hoeven niet groot te zijn. Een virtuele machinegrootte zoals Standard_E2s_v3 of Standard_D2s_v3 is voldoende. Zorg ervoor dat u premium-opslag de os-schijf gebruikt.

Voer de volgende opdrachten uit op alle **virtuele iSCSI-doelmachines**.

1. SLES bijwerken

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Mogelijk moet u het besturingssysteem opnieuw opstarten nadat u het besturingssysteem hebt geüupgradet of bijgewerkt. 

1. Pakketten verwijderen

   Verwijder de volgende pakketten om een bekend probleem met targetcli en SLES 12 SP3 te voorkomen. U fouten negeren over pakketten die niet kunnen worden gevonden

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. ISCSI-doelpakketten installeren

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. De iSCSI-doelservice inschakelen

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>ISCSI-apparaat maken op de iSCSI-doelserver

Voer de volgende opdrachten uit op alle **virtuele iSCSI-doelmachines** om de iSCSI-schijven te maken voor de clusters die door uw SAP-systemen worden gebruikt. In het volgende voorbeeld worden SBD-apparaten voor meerdere clusters gemaakt. Het laat zien hoe u één iSCSI-doelserver voor meerdere clusters zou gebruiken. De SBD-apparaten worden op de OS-schijf geplaatst. Zorg ervoor dat je genoeg ruimte hebt.

**`nfs`** wordt gebruikt om het NFS-cluster te identificeren, **ascsnw1** wordt gebruikt om het ASCS-cluster van **NW1**te identificeren, **dbnw1** wordt gebruikt om het databasecluster van **NW1,** **nfs-0** en **nfs-1** te identificeren zijn de hostnamen van de NFS-clusterknooppunten, **nw1-xscs-0** en **nw1-xscs-1** zijn de hostnamen van de **NW1** ASCS-clusterknooppunten en **nw1-db-0** en **nw1-db-1** zijn de hostnamen van de databaseclusterknooppunten. Vervang ze door de hostnamen van uw clusterknooppunten en de SID van uw SAP-systeem.

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

U controleren of alles correct is ingesteld met

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

### <a name="set-up-sbd-device"></a>SBD-apparaat instellen

Maak verbinding met het iSCSI-apparaat dat in de laatste stap van het cluster is gemaakt.
Voer de volgende opdrachten uit op de knooppunten van het nieuwe cluster dat u wilt maken.
De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** Verbinding maken met de iSCSI-apparaten

   Schakel eerst de iSCSI- en SBD-services in.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** De naam van de initiator wijzigen op het eerste knooppunt

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Wijzig de inhoud van het bestand aan de ACL's die u hebt gebruikt bij het maken van het iSCSI-apparaat op de iSCSI-doelserver, bijvoorbeeld voor de NFS-server.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** De naam van de initiator wijzigen op het tweede knooppunt

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   De inhoud van het bestand aanpassen aan de ACL's die u hebt gebruikt bij het maken van het iSCSI-apparaat op de iSCSI-doelserver

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** De iSCSI-service opnieuw starten

   Start nu de iSCSI-service opnieuw om de wijziging toe te passen

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Sluit de iSCSI-apparaten aan. In het onderstaande voorbeeld is 10.0.0.17 het IP-adres van de iSCSI-doelserver en 3260 de standaardpoort. <b>iqn.2006-04.nfs.local:nfs</b> is een van de doelnamen die wordt vermeld wanneer u de eerste opdracht hieronder uitvoert (iscsiadm -m discovery).

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

   Zorg ervoor dat de iSCSI-apparaten beschikbaar zijn en noteer de naam van het apparaat (in het volgende voorbeeld /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Haal nu de id's van de iSCSI-apparaten op.

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

   De opdrachtlijst drie apparaat-id's voor elk SBD-apparaat. We raden u aan de ID te gebruiken die begint met scsi-3, in het bovenstaande voorbeeld

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Het SBD-apparaat maken

   Gebruik de apparaat-id van de iSCSI-apparaten om de nieuwe SBD-apparaten op het eerste clusterknooppunt te maken.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Pas de SBD config

   Het SBD-config-bestand openen

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Wijzig de eigenschap van het SBD-apparaat, schakel de integratie van de pacemaker in en wijzig de startmodus van SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Het `softdog` configuratiebestand maken

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Laad nu de module

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Clusterinstallatie

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** Update SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Component installeren, nodig voor clusterbronnen

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Azure-LB-component installeren, die nodig is voor clusterbronnen

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Controleer de versie van pakketresourceagents en controleer of aan de minimale versievereisten wordt voldaan:  
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018.a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resourceagents-4.3.0184.6ee15eb2-4.13.1 zijn.  

1. **[A]** Het besturingssysteem configureren

   In sommige gevallen creëert Pacemaker veel processen en put daardoor het toegestane aantal processen uit. In een dergelijk geval kan een hartslag tussen de clusterknooppunten mislukken en leiden tot failover van uw resources. We raden aan de maximaal toegestane processen te verhogen door de volgende parameter in te stellen.

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

   Verklein de grootte van de vuile cache. Zie [Lage schrijfprestaties op SLES 11/12-servers met groot RAM-geheugen](https://www.suse.com/support/kb/doc/?id=7010287)voor meer informatie.

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Cloud-netconfig-azure configureren voor HA-cluster

   Wijzig het configuratiebestand voor de netwerkinterface zoals hieronder wordt weergegeven om te voorkomen dat de plug-in van het cloudnetwerk het virtuele IP-adres verwijdert (Pacemaker moet de VIP-toewijzing beheren). Zie [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)voor meer informatie. 

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

1. **[A]** Fence-agenten installeren
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Als u Suse Linux Enterprise Server voor SAP 15 gebruikt, moet u zich ervan bewust zijn dat u extra modules moet activeren en extra onderdelen moet installeren, wat een vereiste is voor het gebruik van Azure Fence Agent. Zie [Modules en extensies uitgelegd](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html)voor meer informatie over SUSE-modules en -extensies. Volg de instructies balg om Azure Python SDK te installeren. 

   De volgende instructies voor het installeren van Azure Python SDK zijn alleen van toepassing op Suse Enterprise Server voor SAP **15.**  

    - Als u Bring-Your-Own-Subscription gebruikt, volgt u deze instructies  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Als u een Abonnement op betalen per gebruik gebruikt, volgt u deze instructies  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten. Het voordeel van het gebruik van / etc / hosts is dat uw cluster onafhankelijk wordt van DNS, wat ook een enkel punt van fouten kan zijn.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Cluster installeren

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Knooppunt toevoegen aan cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Hacluster-wachtwoord wijzigen in hetzelfde wachtwoord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Corosync-instellingen aanpassen.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Voeg de volgende vette inhoud toe aan het bestand als de waarden er niet zijn of verschillen. Zorg ervoor dat u het token wijzigt in 30000 zodat het behoud van het geheugen behoudend blijft. Zie [dit artikel voor Linux][virtual-machines-linux-maintenance] of [Windows][virtual-machines-windows-maintenance]voor meer informatie.

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

   Start vervolgens de corosync-service opnieuw

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure Fence-agent STONITH-apparaat maken

Het STONITH-apparaat maakt gebruik van een Service Principal om te autoriseren tegen Microsoft Azure. Volg deze stappen om een serviceprincipal te maken.

1. Ga naar <https://portal.azure.com>
1. Het Azure Active Directory-blad openen  
   Ga naar Eigenschappen en noteer de Directory-id. Dit is de **tenant-id.**
1. Klik op App-registraties
1. Klik op Nieuwe registratie
1. Voer een naam in, selecteer 'Alleen accounts in deze organisatiemap' 
2. Selecteer Toepassingstype 'Web', voer een aanmeldings-URL in (bijvoorbeeld http:\//localhost) en klik op Toevoegen  
   De aanmeldings-URL wordt niet gebruikt en kan een geldige URL zijn
1. Selecteer Certificaten en geheimen en klik op Nieuw clientgeheim
1. Voer een beschrijving in voor een nieuwe sleutel, selecteer 'Nooit verloopt' en klik op Toevoegen
1. Noteer de waarde. Het wordt gebruikt als **wachtwoord** voor de Service Principal
1. Selecteer Overzicht. Noteer de toepassings-id. Het wordt gebruikt als gebruikersnaam **(login-id** in de onderstaande stappen) van de Service Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Maak een aangepaste rol voor de hekagent

De ServicePrincipal heeft standaard geen machtigingen om toegang te krijgen tot uw Azure-bronnen. U moet de serviceprincipal-machtigingen geven om alle virtuele machines van het cluster te starten en te stoppen (detoewijzen). Als u de aangepaste rol nog niet hebt gemaakt, u deze maken met [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) of [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli)

Gebruik de volgende inhoud voor het invoerbestand. U moet de inhoud aanpassen aan uw abonnementen, vervangen c276fc76-9cd4-44c9-99a7-4fd71546436e en e91d47c4-76f3-4271-a796-21b4ecfe3624 met de Ids van uw abonnement. Als u slechts één abonnement hebt, verwijdert u de tweede vermelding in AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action", 
    "Microsoft.Compute/virtualMachines/powerOff/action" 
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** De aangepaste rol toewijzen aan de serviceprincipal

Wijs de aangepaste rol "Linux Fence Agent Role" die in het laatste hoofdstuk is gemaakt, toe aan de Service Principal. Gebruik de rol Eigenaar niet meer!

1. Ga naar[https://portal.azure.com](https://portal.azure.com)
1. Het blad Alle resources openen
1. De virtuele machine van het eerste clusterknooppunt selecteren
1. Klik op Toegangsbesturingselement (IAM)
1. Klik op Roltoewijzing toevoegen
1. Selecteer de rol "Linux Fence Agent Role"
1. Voer de naam in van de toepassing die u hierboven hebt gemaakt
1. Op Opslaan klikken

Herhaal de bovenstaande stappen voor het tweede clusterknooppunt.

### <a name="1-create-the-stonith-devices"></a>**[1]** Maak de STONITH-apparaten

Nadat u de machtigingen voor de virtuele machines hebt bewerkt, u de STONITH-apparaten in het cluster configureren.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Standaardpacemakerconfiguratie voor SBD

1. **[1]** Schakel het gebruik van een STONITH-apparaat in en stel de vertraging van het hek in

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

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemakerconfiguratie voor geplande Azure-gebeurtenissen

Azure biedt [geplande gebeurtenissen](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Geplande gebeurtenissen worden aangeboden via meta-data service en geven de tijd voor de toepassing voor te bereiden op gebeurtenissen zoals VM shutdown, VM herschikking, enz. Azure-gebeurtenissen van resourceagent's worden bewaakt voor geplande **[Azure-gebeurtenissen.](https://github.com/ClusterLabs/resource-agents/pull/1161)** Als gebeurtenissen worden gedetecteerd, probeert de agent alle resources op de getroffen VM te stoppen en deze naar een ander knooppunt in het cluster te verplaatsen. Om dat te bereiken moeten extra Pacemaker-bronnen worden geconfigureerd. 

1. **[A]** Zorg ervoor dat het pakket voor de **azure-events-agent** al is geïnstalleerd en up-to-date is. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Configureer de resources in Pacemaker. 

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
   > Nadat u de pacemakerbronnen voor azure-events-agent hebt geconfigureerd, u waarschuwingsberichten ontvangen als:  
     WAARSCHUWING: cib-bootstrap-opties: onbekend attribuut 'hostName_ <strong>hostname'</strong>  
     WAARSCHUWING: cib-bootstrap-opties: onbekend attribuut 'azure-events_globalPullState'  
     WAARSCHUWING: cib-bootstrap-opties: onbekend attribuut 'hostName_ <strong>hostname'</strong>  
   > Deze waarschuwingsberichten kunnen worden genegeerd.

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server][sles-nfs-guide]
* [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen][sles-guide]
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
