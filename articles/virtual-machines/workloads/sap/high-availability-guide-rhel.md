---
title: Azure VM's hoge beschikbaarheid voor SAP NW op RHEL | Microsoft Documenten
description: Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux
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
ms.openlocfilehash: 73b958149d9d6d907785fe1c2c56b8198bb91f70
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351090"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert, het clusterframework installeert en een zeer beschikbaar SAP NetWeaver 7.50-systeem installeert.
In het voorbeeld configuraties, installatie commando's etc. ASCS-instantienummer 00, ERS-instantienummer 02 en SAP System ID NW1 worden gebruikt. De namen van de resources (bijvoorbeeld virtuele machines, virtuele netwerken) in het voorbeeld gaan ervan uit dat u de [ASCS/SCS-sjabloon][template-multisid-xscs] met Resource Prefix NW1 hebt gebruikt om de bronnen te maken.

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
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Productdocumentatie voor Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP Netweaver in pacemakercluster](https://access.redhat.com/articles/3150081)
* Algemene RHEL-documentatie
  * [Overzicht van invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On-beheer met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [ASCS/ERS configureren voor SAP Netweaver met zelfstandige resources in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [SAP S/4HANA ASCS/ERS configureren met standalone enqueueserver 2 (ENSA2) in Pacemaker op RHEL](https://access.redhat.com/articles/3974941)
* Azure-specifieke RHEL-documentatie:
  * [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - Microsoft Azure Virtual Machines als clusterleden](https://access.redhat.com/articles/3131341)
  * [Een Red Hat Enterprise Linux 7.4 (en hoger) cluster met hoge beschikbaarheid installeren en configureren op Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Overzicht

Om een hoge beschikbaarheid te bereiken, heeft SAP NetWeaver gedeelde opslag nodig. GlusterFS is geconfigureerd in een apart cluster en kan door meerdere SAP-systemen worden gebruikt.

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA-database gebruiken virtuele hostname en virtuele IP-adressen. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. We raden u aan [standaard load balancer te gebruiken.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal) In de volgende lijst ziet u de configuratie van de (A)SCS en ERS load balancer.

### <a name="ascs"></a>(A)SCS

* Frontend-configuratie
  * IP-adres 10.0.0.7
* Sondepoort
  * Poort 620<strong>&lt;nr.&gt;</strong>
* Regels voor taakverdeling
  * Als u Standard Load Balancer gebruikt, selecteert u **HA-poorten**
  * Als u Basic Load Balancer gebruikt, maakt u regels voor taakverdeling voor de volgende poorten
    * 32<strong>&lt;&gt; nr.</strong>
    * 36<strong>&lt;&gt; nr.</strong>
    * 39<strong>&lt;&gt; nr.</strong>
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Frontend-configuratie
  * IP-adres 10.0.0.8
* Sondepoort
  * Poort 621<strong>&lt;nr.&gt;</strong>
* Regels voor taakverdeling
  * Als u Standard Load Balancer gebruikt, selecteert u **HA-poorten**
  * Als u Basic Load Balancer gebruikt, maakt u regels voor taakverdeling voor de volgende poorten
    * 32<strong>&lt;&gt; nr.</strong>
    * 33<strong>&lt;&gt; nr.</strong>
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Backend-configuratie
  * Verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van het (A)SCS/ERS-cluster

## <a name="setting-up-glusterfs"></a>GlusterFS instellen

SAP NetWeaver vereist gedeelde opslag voor de transport- en profielmap. Lees [GlusterFS op Azure VM's op Red Hat Enterprise Linux voor SAP NetWeaver][glusterfs-ha] over het instellen van GlusterFS voor SAP NetWeaver.

## <a name="setting-up-ascs"></a>Opzetten (A)SCS

U een Azure-sjabloon van GitHub gebruiken om alle vereiste Azure-resources te implementeren, inclusief de virtuele machines, beschikbaarheidsset en load balancer, of u de resources handmatig implementeren.

### <a name="deploy-linux-via-azure-template"></a>Linux implementeren via Azure-sjabloon

De Azure Marketplace bevat een afbeelding voor Red Hat Enterprise Linux die u gebruiken om nieuwe virtuele machines te implementeren. U een van de quickstartsjablonen op GitHub gebruiken om alle vereiste resources te implementeren. De sjabloon implementeert de virtuele machines, de load balancer, beschikbaarheidset etc. Volg de volgende stappen om de sjabloon te implementeren:

1. De [ASCS/SCS-sjabloon openen][template-multisid-xscs] op de Azure-portal  
1. Voer de volgende parameters in
   1. Bronvoorvoegsel  
      Voer het voorvoegsel in dat u wilt gebruiken. De waarde wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
   1. Stapeltype  
      Het type SAP NetWeaver-stack selecteren
   1. Type besturingssysteem  
      Selecteer een van de Linux-distributies. Selecteer in dit voorbeeld RHEL 7
   1. Db-type  
      Hana selecteren
   1. Aantal Sap-systemen  
      Het aantal SAP-systemen dat in dit cluster wordt uitgevoerd. Selecteer 1.
   1. Beschikbaarheid van het systeem  
      HA selecteren
   1. Gebruikersnaam, beheerderswachtwoord of SSH-toets  
      Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om in te loggen op de machine.
   1. Subnet-id  
   Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal uit als /abonnementen/**&lt;&gt;abonnements-ID**/resourceGroepen/**&lt;&gt;resourcegroepnaam**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuele netwerknaam&gt;**/subnetten/**&lt;subnetnaam&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux handmatig implementeren via Azure-portal

U moet eerst de virtuele machines voor dit cluster maken. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de backend pool.

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
1. Ten minste één gegevensschijf toevoegen aan beide virtuele machines  
   De gegevensschijven worden gebruikt voor de`<SAPSID` /usr/sap/> directory
1. Maak load balancer (intern, standaard):  
   1. De ip-adressen aan de frontend maken
      1. IP-adres 10.0.0.7 voor de ASCS
         1. Open de load balancer, selecteer ip-pool aan frontend en klik op Toevoegen
         1. Voer de naam in van de nieuwe frontend IP-pool (bijvoorbeeld **nw1-ascs-frontend)**
         1. De toewijzing instellen op Statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.7**)
         1. Klik op OK
      1. IP-adres 10.0.0.8 voor de ASCS ERS
         * Herhaal de bovenstaande stappen om een IP-adres voor de ERS te maken (bijvoorbeeld **10.0.0.8** en **nw1-aers-frontend)**
   1. Back-endpool maken
      1. Open de load balancer, selecteer backendpools en klik op Toevoegen
      1. Voer de naam in van de nieuwe backendpool (bijvoorbeeld **nw1-backend)**
      1. Klik op Een virtuele machine toevoegen.
      1. Virtuele machine selecteren.
      1. Selecteer de virtuele machines van het (A)SCS-cluster en hun IP-adressen.
      1. Klik op Add.
   1. De statussondes maken
      1. Poort 620**00** voor ASCS
         1. Open de load balancer, selecteer health probes en klik op Toevoegen
         1. Voer de naam van de nieuwe health probe (bijvoorbeeld **nw1-ascs-hp)**
         1. Selecteer TCP als protocol, poort 620**00,** houd Interval 5 en Niet-gezonde drempel 2
         1. Klik op OK
      1. Poort 621**02** voor ASCS ERS
         * Herhaal de bovenstaande stappen om een gezondheidssonde voor de ERS te maken (bijvoorbeeld 621**02** en **nw1-aers-hp)**
   1. Regels voor taakverdeling
      1. Regels voor taakverdeling voor ASCS
         1. Open de load balancer, selecteer load-balancing rules en klik op Toevoegen
         1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld **nw1-lb-ascs)**
         1. Selecteer de frontend IP-adres, backend pool, en gezondheid sonde die u eerder hebt gemaakt (bijvoorbeeld **nw1-ascs-frontend,** **nw1-backend** en **nw1-ascs-hp**)
         1. **HA-poorten selecteren**
         1. Verhoog de idle time-out tot 30 minuten
         1. **Zorg ervoor dat zwevend IP-adres wordt ingeschakeld**
         1. Klik op OK
         * Herhaal de bovenstaande stappen om regels voor het balanceren van belasting voor ERS te maken (bijvoorbeeld **nw1-lb-ers**)
1. Als uw scenario basisload balancer (intern) vereist, voert u ook de volgende stappen uit:  
   1. De ip-adressen aan de frontend maken
      1. IP-adres 10.0.0.7 voor de ASCS
         1. Open de load balancer, selecteer ip-pool aan frontend en klik op Toevoegen
         1. Voer de naam in van de nieuwe frontend IP-pool (bijvoorbeeld **nw1-ascs-frontend)**
         1. De toewijzing instellen op Statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.7**)
         1. Klik op OK
      1. IP-adres 10.0.0.8 voor de ASCS ERS
         * Herhaal de bovenstaande stappen om een IP-adres voor de ERS te maken (bijvoorbeeld **10.0.0.8** en **nw1-aers-frontend)**
   1. Back-endpool maken
      1. Open de load balancer, selecteer backendpools en klik op Toevoegen
      1. Voer de naam in van de nieuwe backendpool (bijvoorbeeld **nw1-backend)**
      1. Klik op Een virtuele machine toevoegen.
      1. Selecteer de beschikbaarheidsset die u eerder hebt gemaakt
      1. De virtuele machines van het (A)SCS-cluster selecteren
      1. Klik op OK
   1. De statussondes maken
      1. Poort 620**00** voor ASCS
         1. Open de load balancer, selecteer health probes en klik op Toevoegen
         1. Voer de naam van de nieuwe health probe (bijvoorbeeld **nw1-ascs-hp)**
         1. Selecteer TCP als protocol, poort 620**00,** houd Interval 5 en Niet-gezonde drempel 2
         1. Klik op OK
      1. Poort 621**02** voor ASCS ERS
         * Herhaal de bovenstaande stappen om een gezondheidssonde voor de ERS te maken (bijvoorbeeld 621**02** en **nw1-aers-hp)**
   1. Regels voor taakverdeling
      1. 32**00** TCP voor ASCS
         1. Open de load balancer, selecteer load-balancing rules en klik op Toevoegen
         1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld **nw1-lb-3200**)
         1. Selecteer de frontend IP-adres, backend pool, en gezondheid sonde die u eerder hebt gemaakt (bijvoorbeeld **nw1-ascs-frontend**)
         1. Protocol **TCP**bijhouden, poort **3200** invoeren
         1. Verhoog de idle time-out tot 30 minuten
         1. **Zorg ervoor dat zwevend IP-adres wordt ingeschakeld**
         1. Klik op OK
      1. Extra poorten voor de ASCS
         * Herhaal de bovenstaande stappen voor poorten 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 en TCP voor de ASCS
      1. Extra poorten voor de ASCS ERS
         * Herhaal de bovenstaande stappen voor poorten 33**02**, 5**02**13, 5**02**14, 5**02**16 en TCP voor de ASCS ERS

> [!Note]
> Wanneer VM's zonder openbare IP-adressen in de backendpool van interne (geen openbare IP-adres) Standaard Azure-loadbalancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij extra configuratie wordt uitgevoerd om routering naar openbare eindpunten mogelijk te maken. Zie [Openbare eindpuntconnectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)met hoge beschikbaarheid voor meer informatie over het bereiken van uitgaande connectiviteit.  

> [!IMPORTANT]
> Schakel geen TCP-tijdstempels in op Azure VM's die achter Azure Load Balancer zijn geplaatst. Als u TCP-tijdstempels inschakelt, worden de statussprobes mislukt. Stel parameter **net.ipv4.tcp_timestamps** in op **0**. Zie [Health Probes van Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)meer informatie .

### <a name="create-pacemaker-cluster"></a>Pacemakercluster maken

Volg de stappen in [Het instellen van Pacemaker op Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) om een basispacemakercluster voor deze (A)SCS-server te maken.

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Het IP-adres en de hostnaam vervangen in de volgende opdrachten

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** Maak de gedeelde mappen

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** GlusterFS-client en andere vereisten installeren

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** Controleer versie van resource-agents-sap

   Zorg ervoor dat de versie van het geïnstalleerde resource-agents-sap-pakket ten minste 3.9.5-124.el7 is
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** Mount-items toevoegen

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Monteer de nieuwe aandelen

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** SWAP-bestand configureren

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Start de agent opnieuw om de wijziging te activeren

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** RHEL-configuratie

   RHEL configureren zoals beschreven in SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS installeren

1. **[1]** Een virtuele IP-bron en statussonde maken voor de ASCS-instantie

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root op het eerste knooppunt met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ASCS, bijvoorbeeld <b>nw1-ascs,</b> <b>10.0.0.7</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>00</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Als de installatie geen submap maakt in /usr/sap/**NW1**/ASCS**00,** probeert u de eigenaar en groep van de ASCS**00-map** in en probeert u het opnieuw.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Maak een virtuele IP-bron en health-probe voor de ERS-instantie

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver ERS installeren  

   Installeer SAP NetWeaver ERS als root op het tweede knooppunt met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ERS, bijvoorbeeld <b>nw1-aers,</b> <b>10.0.0.8</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>02</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Als de installatie geen submap maakt in /usr/sap/**NW1**/ERS**02,** u de eigenaar en groep van de MAP ERS**02** opnieuw proberen.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** De ASCS/SCS- en ERS-instantieprofielen aanpassen

   * ASCS/SCS-profiel

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS-profiel

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** Keep Alive configureren

   De communicatie tussen de SAP NetWeaver-toepassingsserver en de ASCS/SCS wordt doorgestuurd via een softwareload balancer. De load balancer koppelt inactieve verbindingen na een configureerbare time-out. Om dit te voorkomen, moet u een parameter instellen in het SAP NetWeaver ASCS/SCS-profiel en de Linux-systeeminstellingen wijzigen. Lees [SAP Note 1410736][1410736] voor meer informatie.

   De ASCS/SCS profielparameter enque/encni/set_so_keepalive is al in de laatste stap toegevoegd.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Update het /usr/sap/sapservices-bestand

   Om het begin van de exemplaren door het sapinit-opstartscript te voorkomen, moeten alle exemplaren die door Pacemaker worden beheerd, worden becommentarieerd vanuit het bestand /usr/sap/sapservices. Geef geen commentaar op de SAP HANA instantie als het zal worden gebruikt met HANA SR.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** De SAP-clusterbronnen maken

  Als u de enqueue server 1-architectuur (ENSA1) gebruikt, definieert u de bronnen als volgt:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP introduceerde ondersteuning voor enqueue server 2, inclusief replicatie, vanaf SAP NW 7.52. Vanaf ABAP Platform 1809 is enqueue server 2 standaard geïnstalleerd. Zie SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor ondersteuning voor enqueue server 2.
   Als u de enqueue server 2-architectuur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)gebruikt, installeert u resourceagent resourceagents-sap-4.1.1-12.el7.x86_64 of nieuwer en definieert u de bronnen als volgt:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Zie SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322)als u een upgrade uitvoert van een oudere versie en overschakelt naar server 2. 

   > [!NOTE]
   > De time-outs in de bovenstaande configuratie zijn slechts voorbeelden en moeten mogelijk worden aangepast aan de specifieke SAP-installatie. 

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** Firewallregels toevoegen voor ASCS en ERS op beide knooppunten

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver-toepassingsservervoorbereiding

Sommige databases vereisen dat de installatie van de database-instantie wordt uitgevoerd op een toepassingsserver. Bereid de virtuele machines van de toepassingsserver voor om ze in deze gevallen te kunnen gebruiken.

In de stappen wordt ervan uitgegaan dat u de toepassingsserver installeert op een andere server dan de ASCS/SCS- en HANA-servers. Anders zijn sommige van de onderstaande stappen (zoals het configureren van de hostnaamresolutie) niet nodig.

1. Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Het IP-adres en de hostnaam vervangen in de volgende opdrachten

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. De sapmnt-map maken

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. GlusterFS-client en andere vereisten installeren

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Bevestigingsvermeldingen toevoegen

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Monteer de nieuwe aandelen

   <pre><code>sudo mount -a
   </code></pre>

1. SWAP-bestand configureren
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Start de agent opnieuw om de wijziging te activeren

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Database installeren

In dit voorbeeld wordt SAP NetWeaver geïnstalleerd op SAP HANA. U elke ondersteunde database voor deze installatie gebruiken. Zie [Hoge beschikbaarheid van SAP HANA op Azure VM's op Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]voor meer informatie over het installeren van SAP HANA in Azure.

1. De installatie van de SAP-database-instantie uitvoeren

   Installeer de SAP NetWeaver-databaseinstantie als root met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de database, bijvoorbeeld <b>nw1-db</b> en <b>10.0.0.13</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>INSTALLATIE van SAP NetWeaver-toepassingsserver

Volg deze stappen om een SAP-toepassingsserver te installeren.

1. Toepassingsserver voorbereiden

   Volg de stappen in het hoofdstuk [SAP NetWeaver-toepassingsservervoorbereiding](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) hierboven om de toepassingsserver voor te bereiden.

1. SAP NetWeaver-toepassingsserver installeren

   Installeer een primaire of aanvullende SAP NetWeaver-toepassingenserver.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Update SAP HANA secure store

   Werk de SAP HANA secure store bij om de virtuele naam van de SAP HANA System Replication-installatie aan te wijzen.

   Voer de volgende opdracht uit \<om de vermeldingen als sapsid>adm weer te geven

   <pre><code>hdbuserstore List
   </code></pre>

   Dit moet een lijst van alle items en moet lijken op
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Uit de uitvoer blijkt dat het IP-adres van de standaardvermelding naar de virtuele machine wijst en niet naar het IP-adres van de load balancer. Dit item moet worden gewijzigd om te wijzen op de virtuele hostnaam van de load balancer. Zorg ervoor dat u dezelfde poort **(30313** in de bovenstaande uitvoer) en databasenaam **(HN1** in de bovenstaande uitvoer) gebruikt!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>De clusterinstelling testen

1. De ASCS-instantie handmatig migreren

   Resourcestatus voordat de test wordt gestart:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten uit als hoofdom de ASCS-instantie te migreren.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Knooppuntcrash simuleren

   Resourcestatus voordat de test wordt gestart:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende opdracht als hoofd uit op het knooppunt waar de ASCS-instantie wordt uitgevoerd

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   De status nadat het knooppunt opnieuw is gestart, moet er zo uitzien.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Gebruik de volgende opdracht om de mislukte resources op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Kill message server proces

   Resourcestatus voordat de test wordt gestart:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten uit als root om het proces van de berichtenserver te identificeren en te doden.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Als u de berichtenserver slechts één keer `sapstart`doodt, wordt deze opnieuw gestart door . Als je het vaak genoeg doodt, zal Pacemaker uiteindelijk de ASCS-instantie naar het andere knooppunt verplaatsen. Voer de volgende opdrachten uit als root om de resourcestatus van de instantie ASCS en ERS na de test op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Serverproces in de wachtrij doden

   Resourcestatus voordat de test wordt gestart:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende opdrachten als hoofd uit op het knooppunt waarin de ASCS-instantie wordt uitgevoerd om de wachtrijserver te doden.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   De ASCS-instantie moet onmiddellijk niet naar het andere knooppunt gaan. De INSTANTIE ERS moet ook mislukken nadat de ASCS-instantie is gestart. Voer de volgende opdrachten uit als root om de resourcestatus van de instantie ASCS en ERS na de test op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Replicatieserverproces voor wachtrijen doden

   Resourcestatus voordat de test wordt gestart:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdracht als hoofd op het knooppunt uit waarin de instantie ERS wordt uitgevoerd om het replicatieserverproces in de wachtrij te doden.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Als u de opdracht `sapstart` slechts één keer uitvoert, wordt het proces opnieuw gestart. Als u het vaak `sapstart` genoeg uitvoert, wordt het proces niet opnieuw opgestart en wordt de resource in een gestopte status weergegeven. Voer de volgende opdrachten uit als root om de resourcestatus van de INSTANTIE ERS na de test op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Kill enqueue sapstartsrv proces

   Resourcestatus voordat de test wordt gestart:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten uit als hoofd op het knooppunt waar de ASCS wordt uitgevoerd.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Het sapstartsrv-proces moet altijd opnieuw worden gestart door het pacemaker-bronmiddel als onderdeel van de monitoring. Resourcestatus na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [HA voor SAP NW op Azure VM's op RHEL voor SAP-toepassingen multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [SAP HANA (grote exemplaren) hoge beschikbaarheid en noodherstel op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure.
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
