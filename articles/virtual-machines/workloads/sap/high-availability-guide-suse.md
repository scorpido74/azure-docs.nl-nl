---
title: Azure VM's hoge beschikbaarheid voor SAP NetWeaver op SLES | Microsoft Documenten
description: Handleiding voor hoge beschikbaarheid voor SAP NetWeaver op SUSE Linux Enterprise Server voor SAP-toepassingen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 05effb7d2e64c5f27acabad4b086ba27d6849cc8
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348824"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert, het clusterframework installeert en een zeer beschikbaar SAP NetWeaver 7.50-systeem installeert.
In het voorbeeld configuraties, installatie commando's etc. ASCS-instantienummer 00, ERS-instantienummer 02 en SAP System ID NW1 worden gebruikt. De namen van de resources (bijvoorbeeld virtuele machines, virtuele netwerken) in het voorbeeld gaan ervan uit dat u de [geconvergeerde sjabloon][template-converged] met SAP-systeem-ID NW1 hebt gebruikt om de bronnen te maken.

Lees eerst de volgende SAP Notes en papers

* SAP Note [1928533][1928533], die heeft:
  * Lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten
  * Ondersteunde SAP-software en besturingssysteem- en databasecombinaties
  * Vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure

* SAP Note [2015553][2015553] bevat vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure.
* SAP Note [2205917][2205917] heeft os-instellingen aanbevolen voor SUSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [1944799][1944799] heeft SAP HANA-richtlijnen voor SUSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [2178632][2178632] heeft gedetailleerde informatie over alle monitoringstatistieken die voor SAP in Azure worden gerapporteerd.
* SAP Note [2191498][2191498] heeft de vereiste SAP Host Agent-versie voor Linux in Azure.
* SAP Note [2243692][2243692] heeft informatie over SAP-licenties op Linux in Azure.
* SAP Note [1984787][1984787] heeft algemene informatie over SUSE Linux Enterprise Server 12.
* SAP Note [1999351][1999351] heeft aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) heeft alle vereiste SAP Notes voor Linux.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SUSE SAP HA Best Practice Guides][suse-ha-guide] De hulplijnen bevatten alle benodigde informatie om De Netwerkwever HA en SAP HANA-systeemreplicatie on-premises in te stellen. Gebruik deze hulplijnen als algemene basislijn. Ze bieden veel meer gedetailleerde informatie.
* [SUSE High Availability Extension 12 SP3 Release Notes][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Overzicht

Om een hoge beschikbaarheid te bereiken, heeft SAP NetWeaver een NFS-server nodig. De NFS-server is geconfigureerd in een afzonderlijk cluster en kan door meerdere SAP-systemen worden gebruikt.

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-suse/ha-suse.png)

De NFS-server, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA-database gebruiken virtuele hostname en virtuele IP-adressen. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. We raden u aan [standaard load balancer te gebruiken.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal) In de volgende lijst ziet u de configuratie van de (A)SCS en ERS load balancer.

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


## <a name="setting-up-a-highly-available-nfs-server"></a>Een zeer beschikbare NFS-server instellen

SAP NetWeaver vereist gedeelde opslag voor de transport- en profielmap. Lees [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server][nfs-ha] voor het instellen van een NFS-server voor SAP NetWeaver.

## <a name="setting-up-ascs"></a>Opzetten (A)SCS

U een Azure-sjabloon van GitHub gebruiken om alle vereiste Azure-resources te implementeren, inclusief de virtuele machines, beschikbaarheidsset en load balancer, of u de resources handmatig implementeren.

### <a name="deploy-linux-via-azure-template"></a>Linux implementeren via Azure-sjabloon

De Azure Marketplace bevat een afbeelding voor SUSE Linux Enterprise Server voor SAP-toepassingen 12 die u gebruiken om nieuwe virtuele machines te implementeren. De marketplace-afbeelding bevat de resourceagent voor SAP NetWeaver.

U een van de quickstartsjablonen op GitHub gebruiken om alle vereiste resources te implementeren. De sjabloon implementeert de virtuele machines, de load balancer, beschikbaarheidset etc. Volg de volgende stappen om de sjabloon te implementeren:

1. Open de [ASCS/SCS Multi SID-sjabloon][template-multisid-xscs] of de [geconvergeerde sjabloon][template-converged] op de Azure-portal. 
   De ASCS/SCS-sjabloon maakt alleen de load-balancing regels voor de SAP NetWeaver ASCS/SCS- en ERS-exemplaren (alleen Linux), terwijl de geconvergeerde sjabloon ook de regels voor het balanceren van de belasting voor een database maakt (bijvoorbeeld Microsoft SQL Server of SAP HANA). Als u van plan bent een SAP NetWeaver-systeem te installeren en u wilt de database ook op dezelfde machines installeren, gebruikt u de [geconvergeerde sjabloon][template-converged].
1. Voer de volgende parameters in
   1. Resourcevoorvoegsel (alleen ASCS/SCS Multi SID-sjabloon)  
      Voer het voorvoegsel in dat u wilt gebruiken. De waarde wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
   3. Sap-systeem-id (alleen geconvergeerde sjabloon)  
      Voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De id wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
   4. Stapeltype  
      Het type SAP NetWeaver-stack selecteren
   5. Type besturingssysteem  
      Selecteer een van de Linux-distributies. Selecteer in dit voorbeeld SLES 12 BYOS
   6. Db-type  
      Hana selecteren
   7. Sap-systeemgrootte.  
      De hoeveelheid SAPS die het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator
   8. Beschikbaarheid van het systeem  
      HA selecteren
   9. Gebruikersnaam en beheerderswachtwoord voor beheerders  
      Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om zich aan te melden bij de machine.
   10. Subnet-id  
   Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal uit als /abonnementen/**&lt;&gt;abonnements-ID**/resourceGroepen/**&lt;&gt;resourcegroepnaam**/providers/Microsoft.Network/virtualNetworks/**&lt;virtuele netwerknaam&gt;**/subnetten/**&lt;subnetnaam&gt; **

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux handmatig implementeren via Azure-portal

U moet eerst de virtuele machines voor dit NFS-cluster maken. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de backend pool.

1. Een resourcegroep maken
1. Een virtueel netwerk maken
1. Een beschikbaarheidsset maken  
   Domein voor maximale update instellen
1. Virtuele machine maken 1  
   Gebruik ten minste SLES4SAP 12 SP1, in dit voorbeeld de SLES4SAP 12 SP1 afbeeldinghttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES Voor SAP Applications 12 SP1 wordt gebruikt  
   Beschikbaarheidsset selecteren die eerder is gemaakt  
1. Virtuele machine maken 2  
   Gebruik ten minste SLES4SAP 12 SP1, in dit voorbeeld de SLES4SAP 12 SP1 afbeeldinghttps://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES Voor SAP Applications 12 SP1 wordt gebruikt  
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
         * Herhaal de bovenstaande stappen om een IP-adres voor de ERS te maken (bijvoorbeeld **10.0.0.8** en **nw1-aers-backend)**
   1. Back-endpool maken
      1. Open de load balancer, selecteer backendpools en klik op Toevoegen
      1. Voer de naam in van de nieuwe backendpool (bijvoorbeeld **nw1-backend)**
      1. Klik op Een virtuele machine toevoegen.
      1. Virtuele machine selecteren
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

Volg de stappen in [Het instellen van Pacemaker op SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) om een basispacemakercluster voor deze (A)SCS-server te maken.

### <a name="installation"></a>Installeren

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** SUSE-connector installeren

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Het bekende probleem met het gebruik van een streepje in hostnamen is opgelost met versie **3.1.1** van pakket **sap-suse-cluster-connector**. Zorg ervoor dat u ten minste versie 3.1.1 van pakket sap-suse-cluster-connector gebruikt, als u clusterknooppunten gebruikt met streepje in de hostnaam. Anders werkt uw cluster niet. 

   Zorg ervoor dat u de nieuwe versie van de SAP SUSE-clusterconnector hebt geïnstalleerd. De oude heette sap_suse_cluster_connector en de nieuwe heet **sap-suse-cluster-connector**.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** SAP-resourceagents bijwerken  
   
   Een patch voor het resource-agents-pakket is vereist om de nieuwe configuratie te gebruiken, die in dit artikel wordt beschreven. U controleren of de patch al is geïnstalleerd met de volgende opdracht

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   De output moet vergelijkbaar zijn met

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Als de grep-opdracht de parameter IS_ERS niet vindt, moet u de patch installeren die op [de downloadpagina van SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents) wordt vermeld

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Het IP-adres en de hostnaam vervangen in de volgende opdrachten

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

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

1. **[A]** Autofs configureren

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Een bestand maken met

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Herstart autofs om de nieuwe aandelen te monteren

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
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


### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS installeren

1. **[1]** Een virtuele IP-bron en statussonde maken voor de ASCS-instantie

   > [!IMPORTANT]
   > Recente tests brachten situaties aan het licht, waarin netcat stopt met reageren op verzoeken vanwege achterstand en de beperking van de afhandeling van slechts één verbinding. De netcat-bron luistert niet meer naar de aanvragen voor Azure Load balancer en het zwevende IP-adres is niet meer beschikbaar.  
   > Voor bestaande Pacemaker clusters, raden we in het verleden ter vervanging van netcat door socat. Momenteel raden we u aan azure-lb resource agent, die deel uitmaakt van pakket resource-agents, met de volgende pakketversie vereisten:
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018.a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resourceagents-4.3.0184.6ee15eb2-4.13.1 zijn.  
   >
   > Houd er rekening mee dat de wijziging korte downtime vereist.  
   > Als de configuratie al is gewijzigd om socat te gebruiken zoals beschreven in [Azure Load-Balancer Detection Hardening,](https://www.suse.com/support/kb/doc/?id=7024128)is er geen verplichting om onmiddellijk over te schakelen naar azure-lb resource agent.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root op het eerste knooppunt met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ASCS, bijvoorbeeld <b>nw1-ascs,</b> <b>10.0.0.7</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>00</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Als de installatie geen submap maakt in /usr/sap/**NW1**/ASCS**00,** probeert u de eigenaar en groep van de ASCS**00-map** in en probeert u het opnieuw.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Maak een virtuele IP-bron en health-probe voor de ERS-instantie

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver ERS installeren

   Installeer SAP NetWeaver ERS als root op het tweede knooppunt met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ERS, bijvoorbeeld <b>nw1-aers,</b> <b>10.0.0.8</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>02</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 of hoger gebruiken. Lagere versies stellen de machtigingen niet correct in en de installatie zal mislukken.

   Als de installatie geen submap maakt in /usr/sap/**NW1**/ERS**02,** u de eigenaar en groep van de MAP ERS**02** opnieuw proberen.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** De ASCS/SCS- en ERS-instantieprofielen aanpassen
 
   * ASCS/SCS-profiel

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS-profiel

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** Keep Alive configureren

   De communicatie tussen de SAP NetWeaver-toepassingsserver en de ASCS/SCS wordt doorgestuurd via een softwareload balancer. De load balancer koppelt inactieve verbindingen na een configureerbare time-out. Om dit te voorkomen moet u een parameter instellen in het SAP NetWeaver ASCS/SCS-profiel en de Linux-systeeminstellingen wijzigen. Lees [SAP Note 1410736][1410736] voor meer informatie.

   De ASCS/SCS profielparameter enque/encni/set_so_keepalive is al in de laatste stap toegevoegd.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** De SAP-gebruikers configureren na de installatie

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Voeg de ASCS- en ERS SAP-services toe aan het sapservicebestand

   Voeg de ASCS-servicevermelding toe aan het tweede knooppunt en kopieer de toegang van de ERS-service naar het eerste knooppunt.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** De SAP-clusterbronnen maken

Als u de enqueue server 1-architectuur (ENSA1) gebruikt, definieert u de bronnen als volgt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  SAP introduceerde ondersteuning voor enqueue server 2, inclusief replicatie, vanaf SAP NW 7.52. Vanaf ABAP Platform 1809 is enqueue server 2 standaard geïnstalleerd. Zie SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor ondersteuning voor enqueue server 2.
Als u de enqueue server 2-architectuur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)gebruikt, definieert u de bronnen als volgt:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Zie SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)als u een upgrade uitvoert van een oudere versie en overschakelt naar server 2. 

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver-toepassingsservervoorbereiding

Sommige databases vereisen dat de installatie van de database-instantie wordt uitgevoerd op een toepassingsserver. Bereid de virtuele machines van de toepassingsserver voor om ze in deze gevallen te kunnen gebruiken.

In de stappen wordt ervan uitgegaan dat u de toepassingsserver installeert op een andere server dan de ASCS/SCS- en HANA-servers. Anders zijn sommige van de onderstaande stappen (zoals het configureren van de hostnaamresolutie) niet nodig.

1. Besturingssysteem configureren

   Verklein de grootte van de vuile cache. Zie [Lage schrijfprestaties op SLES 11/12-servers met groot RAM-geheugen](https://www.suse.com/support/kb/doc/?id=7010287)voor meer informatie.

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Het IP-adres en de hostnaam vervangen in de volgende opdrachten

   ```bash
   sudo vi /etc/hosts
   ```

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. De sapmnt-map maken

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Autofs configureren

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Een nieuw bestand maken met

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Herstart autofs om de nieuwe aandelen te monteren

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. SWAP-bestand configureren

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

## <a name="install-database"></a>Database installeren

In dit voorbeeld wordt SAP NetWeaver geïnstalleerd op SAP HANA. U elke ondersteunde database voor deze installatie gebruiken. Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha]voor meer informatie over het installeren van SAP HANA in Azure. Zie [SAP Note 1928533][1928533]voor een lijst met ondersteunde databases.

1. De installatie van de SAP-database-instantie uitvoeren

   Installeer de SAP NetWeaver-databaseinstantie als root met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de database, bijvoorbeeld <b>nw1-db</b> en <b>10.0.0.13</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>INSTALLATIE van SAP NetWeaver-toepassingsserver

Volg deze stappen om een SAP-toepassingsserver te installeren.

1. Toepassingsserver voorbereiden

   Volg de stappen in het hoofdstuk [SAP NetWeaver-toepassingsservervoorbereiding](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) hierboven om de toepassingsserver voor te bereiden.

1. SAP NetWeaver-toepassingsserver installeren

   Installeer een primaire of aanvullende SAP NetWeaver-toepassingenserver.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Update SAP HANA secure store

   Werk de SAP HANA secure store bij om de virtuele naam van de SAP HANA System Replication-installatie aan te wijzen.

   Voer de volgende opdracht uit om de vermeldingen weer te geven
   <pre><code>hdbuserstore List
   </code></pre>

   Dit moet een lijst van alle items en moet lijken op
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Uit de uitvoer blijkt dat het IP-adres van de standaardvermelding naar de virtuele machine wijst en niet naar het IP-adres van de load balancer. Dit item moet worden gewijzigd om te wijzen op de virtuele hostnaam van de load balancer. Zorg ervoor dat u dezelfde poort **(30313** in de bovenstaande uitvoer) en databasenaam **(HN1** in de bovenstaande uitvoer) gebruikt!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>De clusterinstelling testen

De volgende tests zijn een kopie van de testcases in de best practices gidsen van SUSE. Ze worden gekopieerd voor uw gemak. Lees ook altijd de handleidingen voor aanbevolen procedures en voer alle aanvullende tests uit die mogelijk zijn toegevoegd.

1. Test HAGetFailoverConfig, HACheckConfig en HACheckFailoverConfig

   Voer de volgende \<opdrachten uit als sapsid>adm op het knooppunt waar momenteel de ASCS-instantie wordt uitgevoerd. Als de opdrachten mislukken met FAIL: Onvoldoende geheugen, kan dit worden veroorzaakt door streepjes in uw hostnaam. Dit is een bekend probleem en zal door SUSE worden opgelost in het sap-suse-cluster-connector pakket.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. De ASCS-instantie handmatig migreren

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten uit als hoofdom de ASCS-instantie te migreren.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Test HAFailoverToNode

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende \<opdrachten uit als sapsid>adm om de ASCS-instantie te migreren.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Knooppuntcrash simuleren

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdracht als hoofd uit op het knooppunt waar de ASCS-instantie wordt uitgevoerd

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Als u SBD gebruikt, mag pacemaker niet automatisch starten op het gedode knooppunt. De status nadat het knooppunt opnieuw is gestart, moet er zo uitzien.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Gebruik de volgende opdrachten om Pacemaker op het gedode knooppunt te starten, de SBD-berichten schoon te maken en de mislukte resources schoon te maken.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Handmatig opnieuw opstarten van ASCS-instantie testen

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Maak een wachtrijvergrendeling door bijvoorbeeld een gebruiker te bewerken in transactiesu01. Voer de volgende \<opdrachten uit als sapsid>adm op het knooppunt waar de ASCS-instantie wordt uitgevoerd. De opdrachten stoppen de ASCS-instantie en starten deze opnieuw. Als u de architectuur van enqueueserver 1 gebruikt, gaat de wachtrijvergrendeling naar verwachting verloren in deze test. Als u de architectuur van enqueue server 2 gebruikt, blijft de wachtrij behouden. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Het ASCS-exemplaar moet nu worden uitgeschakeld in Pacemaker

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Start de ASCS-instantie opnieuw op hetzelfde knooppunt.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Het wachtrijslot van transactiesu01 moet verloren gaan en de back-end moet zijn gereset. Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kill message server proces

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende opdrachten uit als root om het proces van de berichtenserver te identificeren en te doden.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Als u de berichtenserver slechts één keer doodt, wordt deze opnieuw gestart met sapstart. Als je het vaak genoeg doodt, zal Pacemaker uiteindelijk de ASCS-instantie naar het andere knooppunt verplaatsen. Voer de volgende opdrachten uit als root om de resourcestatus van de instantie ASCS en ERS na de test op te schonen.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Serverproces in de wachtrij doden

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten als hoofd uit op het knooppunt waarin de ASCS-instantie wordt uitgevoerd om de wachtrijserver te doden.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   De ASCS-instantie moet onmiddellijk niet naar het andere knooppunt gaan. De INSTANTIE ERS moet ook mislukken nadat de ASCS-instantie is gestart. Voer de volgende opdrachten uit als root om de resourcestatus van de instantie ASCS en ERS na de test op te schonen.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Replicatieserverproces voor wachtrijen doden

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende opdracht als hoofd op het knooppunt uit waarin de instantie ERS wordt uitgevoerd om het replicatieserverproces in de wachtrij te doden.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Als u de opdracht slechts één keer uitvoert, wordt het proces opnieuw gestart. Als u het vaak genoeg uitvoert, start sapstart het proces niet opnieuw op en wordt de resource in een gestopte status. Voer de volgende opdrachten uit als root om de resourcestatus van de INSTANTIE ERS na de test op te schonen.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kill enqueue sapstartsrv proces

   Resourcestatus voordat de test wordt gestart:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende opdrachten uit als hoofd op het knooppunt waar de ASCS wordt uitgevoerd.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Het sapstartsrv-proces moet altijd opnieuw worden gestart door het pacemaker-bronmiddel. Resourcestatus na de test:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [HA voor SAP NW op Azure VM's op SLES voor SAP-toepassingen multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
