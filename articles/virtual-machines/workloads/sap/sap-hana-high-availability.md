---
title: Hoge beschikbaarheid van SAP HANA op Azure VM's op SLES | Microsoft Documenten
description: Hoge beschikbaarheid van SAP HANA op Azure VM's op SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: radeltch
ms.openlocfilehash: 69dcf91957263cea36f8ff6db6a7af14588998ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927214"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Hoge beschikbaarheid van SAP HANA op Azure VM's op SUSE Linux Enterprise Server

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Voor on-premises ontwikkeling u HANA-systeemreplicatie gebruiken of gedeelde opslag gebruiken om een hoge beschikbaarheid voor SAP HANA vast te stellen.
Op Azure virtual machines (VM's) is HANA System Replication op Azure momenteel de enige ondersteunde functie met hoge beschikbaarheid. SAP HANA-replicatie bestaat uit één primair knooppunt en ten minste één secundair knooppunt. Wijzigingen in de gegevens op het primaire knooppunt worden synchroon of asynchroon gerepliceerd naar het secundaire knooppunt.

In dit artikel wordt beschreven hoe u de virtuele machines implementeert en configureert, het clusterframework installeert en SAP HANA-systeemreplicatie installeert en configureert.
In de voorbeeldconfiguraties worden installatieopdrachten, instantienummer **03**en HANA System ID **HN1** gebruikt.

Lees eerst de volgende SAP Notes en papers:

* SAP Note [1928533], die heeft:
  * De lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software.
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten.
  * De ondersteunde SAP-software en besturingssysteem (OS) en databasecombinaties.
  * De vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure.
* SAP Note [2015553] bevat de vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure.
* SAP Note [2205917] heeft besturingssysteeminstellingen aanbevolen voor SUSE Linux Enterprise Server voor SAP-toepassingen.
* SAP Note [1944799] heeft SAP HANA-richtlijnen voor SUSE Linux Enterprise Server voor SAP-toepassingen.
* SAP Note [2178632] heeft gedetailleerde informatie over alle bewakingsstatistieken die worden gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP Host Agent-versie voor Linux in Azure.
* SAP Note [2243692] heeft informatie over SAP-licenties op Linux in Azure.
* SAP Note [1984787] heeft algemene informatie over SUSE Linux Enterprise Server 12.
* SAP Note [1999351] heeft aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP.
* SAP Note [401162] heeft informatie over hoe u voorkomen dat adres dat al in gebruik is bij het instellen van HANA-systeemreplicatie.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) heeft alle vereiste SAP Notes voor Linux.
* [SAP HANA-gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide] gids.
* [Azure Virtual Machines deployment for SAP on Linux][deployment-guide] (dit artikel).
* [Azure Virtual Machines DBMS-implementatie voor SAP op][dbms-guide] Linux-handleiding.
* [SUSE Linux Enterprise Server voor SAP Applications 12 SP3 best practices handleidingen][sles-for-sap-bp]
  * Het opzetten van een SAP HANA SR Performance Optimized Infrastructure (SLES for SAP Applications 12 SP1). De handleiding bevat alle benodigde informatie voor het instellen van SAP HANA-systeemreplicatie voor on-premises ontwikkeling. Gebruik deze handleiding als basislijn.
  * Een SAP HANA SR Cost Optimized Infrastructure (SLES voor SAP Applications 12 SP1)

## <a name="overview"></a>Overzicht

Om een hoge beschikbaarheid te bereiken, is SAP HANA geïnstalleerd op twee virtuele machines. De gegevens worden gerepliceerd met HANA-systeemreplicatie.

![SAP HANA overzicht van hoge beschikbaarheid](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA System Replication setup maakt gebruik van een speciale virtuele hostname en virtuele IP-adressen. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. In de volgende lijst ziet u de configuratie van de load balancer:

* Front-end configuratie: IP-adres 10.0.0.13 voor hn1-db
* Back-endconfiguratie: verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeemreplicatie
* Sondepoort: poort 62503
* Regels voor taakverdeling: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Implementeren voor Linux

De resourceagent voor SAP HANA is opgenomen in SUSE Linux Enterprise Server voor SAP-toepassingen.
De Azure Marketplace bevat een afbeelding voor SUSE Linux Enterprise Server voor SAP-toepassingen 12 die u gebruiken om nieuwe virtuele machines te implementeren.

### <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U een van de quickstartsjablonen die zich op GitHub bevinden, gebruiken om alle vereiste resources te implementeren. De sjabloon implementeert de virtuele machines, de load balancer, de beschikbaarheidsset, enzovoort.
Voer de volgende stappen uit om de sjabloon te implementeren:

1. Open de [databasesjabloon][template-multisid-db] of de [geconvergeerde sjabloon][template-converged] op de Azure-portal. 
    De databasesjabloon maakt alleen de regels voor het balanceren van een database. De geconvergeerde sjabloon maakt ook de load-balancing regels voor een ASCS/SCS en ERS (Alleen Linux) instantie. Als u van plan bent een SAP NetWeaver-systeem te installeren en u het ascs/SCS-exemplaar op dezelfde machines wilt installeren, gebruikt u de [geconvergeerde sjabloon][template-converged].

1. Voer de volgende parameters in:
    - **Sap-systeem-id:** voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De id wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
    - **Stacktype**: (Deze parameter is alleen van toepassing als u de geconvergeerde sjabloon gebruikt.) Selecteer het type SAP NetWeaver-stack.
    - **Os Type**: Selecteer een van de Linux-distributies. Selecteer in dit voorbeeld **SLES 12**.
    - **Db-type**: Selecteer **HANA**.
    - **Sap-systeemgrootte:** voer het aantal SAPS in dat het nieuwe systeem gaat bieden. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
    - **Beschikbaarheid van het systeem**: Selecteer **HA**.
    - **Gebruikersnaam en beheerderswachtwoord: er**wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om zich aan te melden bij de machine.
    - **Nieuw of bestaand subnet**: bepaalt of een nieuw virtueel netwerk en subnet moet worden gemaakt of een bestaand subnet moet worden gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **Bestaand**.
    - **Subnet-id**: Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal uit als **/abonnementen/\<abonnements-ID>/resourceGroepen/\<resourcegroepnaam>/providers/Microsoft.Network/virtualNetworks/\<virtuele netwerknaam>/subnets/\<subnetnaam>**.

### <a name="manual-deployment"></a>Handmatige implementatie

> [!IMPORTANT]
> Zorg ervoor dat het besturingssysteem dat u selecteert SAP-gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. De lijst van SAP HANA gecertificeerde VM-typen en OS-releases voor die kunnen worden opgezocht in [SAP HANA Certified IaaS Platforms.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Zorg ervoor dat u klikt op de details van het vm-type dat wordt vermeld om de volledige lijst met door SAP HANA ondersteunde OS-releases voor het specifieke VM-type te krijgen
>  

1. Maak een resourcegroep.
1. Maak een virtueel netwerk.
1. Maak een beschikbaarheidsset.
   - Stel het domein voor maximale update in.
1. Maak een load balancer (intern). Wij raden [standaard load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)aan.
   - Selecteer het virtuele netwerk dat in stap 2 is gemaakt.
1. Maak virtuele machine 1.
   - Gebruik een SLES4SAP-afbeelding in de Azure-galerie die wordt ondersteund voor SAP HANA op het vm-type dat u hebt geselecteerd.
   - Selecteer de beschikbaarheidsset die is gemaakt in stap 3.
1. Maak virtuele machine 2.
   - Gebruik een SLES4SAP-afbeelding in de Azure-galerie die wordt ondersteund voor SAP HANA op het vm-type dat u hebt geselecteerd.
   - Selecteer de beschikbaarheidsset die is gemaakt in stap 3. 
1. Gegevensschijven toevoegen.
1. Als u standaard load balancer gebruikt, voert u de volgende configuratiestappen uit:
   1. Maak eerst een front-end IP-pool:
   
      1. Open de load balancer, selecteer **ip-pool frontend**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe front-end IP-pool (bijvoorbeeld **hana-frontend).**
      1. Stel de **toewijzing** in **op Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13**).
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end IP-pool is gemaakt, moet u het IP-adres van de groep noteren.
   
   1. Maak vervolgens een back-endpool:
   
      1. Open de load balancer, selecteer **backendpools**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe back-endpool (bijvoorbeeld **hana-backend).**
      1. Selecteer **Virtueel netwerk**.
      1. Selecteer **Een virtuele machine toevoegen**.
      1. Selecteer ** Virtuele machine**.
      1. Selecteer de virtuele machines van het SAP HANA-cluster en hun IP-adressen.
      1. Selecteer **Toevoegen**.
   
   1. Maak vervolgens een statussonde:
   
      1. Open de load balancer, selecteer **health probes**en selecteer **Toevoegen**.
      1. Voer de naam van de nieuwe health probe (bijvoorbeeld **hana-hp**).
      1. Selecteer **TCP** als protocol en poort 625**03**. Houd de **intervalwaarde** ingesteld op 5 en de **drempelwaarde Niet-inorts is** ingesteld op 2.
      1. Selecteer **OK**.
   
   1. Maak vervolgens de regels voor het balanceren van de lastenafweging:
   
      1. Open de load balancer, selecteer **regels voor taakverdeling**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld **hana-lb).**
      1. Selecteer het front-end IP-adres, de back-end pool en de gezondheidssonde die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend,** **hana-backend** en **hana-hp).**
      1. Selecteer **HA-poorten**.
      1. Verhoog de **idle time-out** tot 30 minuten.
      1. Zorg ervoor dat **zwevend IP wordt ingeschakeld.**
      1. Selecteer **OK**.

   > [!Note]
   > Wanneer VM's zonder openbare IP-adressen in de backendpool van interne (geen openbare IP-adres) Standaard Azure-loadbalancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij extra configuratie wordt uitgevoerd om routering naar openbare eindpunten mogelijk te maken. Zie [Openbare eindpuntconnectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)met hoge beschikbaarheid voor meer informatie over het bereiken van uitgaande connectiviteit.  

1. Als uw scenario voorschrijft met behulp van basislastbalans, voert u ook de volgende configuratiestappen uit:
   1. Maak eerst een front-end IP-pool:
   
      1. Open de load balancer, selecteer **ip-pool frontend**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe front-end IP-pool (bijvoorbeeld **hana-frontend).**
      1. Stel de **toewijzing** in **op Statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13**).
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end IP-pool is gemaakt, moet u het IP-adres van de groep noteren.
   
   1. Maak vervolgens een back-endpool:
   
      1. Open de load balancer, selecteer **backendpools**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe back-endpool (bijvoorbeeld **hana-backend).**
      1. Selecteer **Een virtuele machine toevoegen**.
      1. Selecteer de beschikbaarheidsset die is gemaakt in stap 3.
      1. Selecteer de virtuele machines van het SAP HANA-cluster.
      1. Selecteer **OK**.
   
   1. Maak vervolgens een statussonde:
   
      1. Open de load balancer, selecteer **health probes**en selecteer **Toevoegen**.
      1. Voer de naam van de nieuwe health probe (bijvoorbeeld **hana-hp**).
      1. Selecteer **TCP** als protocol en poort 625**03**. Houd de **intervalwaarde** ingesteld op 5 en de **drempelwaarde Niet-inorts is** ingesteld op 2.
      1. Selecteer **OK**.
   
   1. Maak voor SAP HANA 1.0 de regels voor het balanceren van de lastenafweging:
   
      1. Open de load balancer, selecteer **regels voor taakverdeling**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld hana-lb-3**03**15).
      1. Selecteer het front-end IP-adres, de back-endpool en de statussonde die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
      1. Houd het **protocol** ingesteld op **TCP**en voer poort 3**03**15 in.
      1. Verhoog de **idle time-out** tot 30 minuten.
      1. Zorg ervoor dat **zwevend IP wordt ingeschakeld.**
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3**03**17.
   
   1. Maak voor SAP HANA 2.0 de regels voor het balanceren van de regelvoor de systeemdatabase:
   
      1. Open de load balancer, selecteer **regels voor taakverdeling**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld hana-lb-3**03**13).
      1. Selecteer het front-end IP-adres, de back-endpool en de statussonde die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
      1. Houd het **protocol** ingesteld op **TCP**en voer poort 3**03**13 in.
      1. Verhoog de **idle time-out** tot 30 minuten.
      1. Zorg ervoor dat **zwevend IP wordt ingeschakeld.**
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3**03**14.
   
   1. Maak voor SAP HANA 2.0 eerst de regels voor het balanceren van de regelvoor de tenant:
   
      1. Open de load balancer, selecteer **regels voor taakverdeling**en selecteer **Toevoegen**.
      1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld hana-lb-3**03**40).
      1. Selecteer de ip-adres, backendpool en statussonde die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
      1. Houd het **protocol** ingesteld op **TCP**en voer poort 3**03**40 in.
      1. Verhoog de **idle time-out** tot 30 minuten.
      1. Zorg ervoor dat **zwevend IP wordt ingeschakeld.**
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poorten 3**03**41 en 3**03**42.

   Lees voor meer informatie over de vereiste poorten voor SAP HANA het hoofdstuk [Connections to Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) in de SAP [HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) guide of SAP Note [2388694][2388694].

> [!IMPORTANT]
> Schakel geen TCP-tijdstempels in op Azure VM's die achter Azure Load Balancer zijn geplaatst. Als u TCP-tijdstempels inschakelt, worden de statussprobes mislukt. Stel parameter **net.ipv4.tcp_timestamps** in op **0**. Zie [Health Probes van Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)meer informatie .
> Zie ook SAP note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Een pacemakercluster maken

Volg de stappen in [Het instellen van Pacemaker op SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) om een basispacemakercluster voor deze HANA-server te maken. U hetzelfde pacemakercluster gebruiken voor SAP HANA en SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>SAP HANA installeren

De stappen in deze sectie gebruiken de volgende voorvoegsels:
- **[A]**: De stap is van toepassing op alle knooppunten.
- **[1]**: De stap is alleen van toepassing op knooppunt 1.
- **[2]**: De stap is alleen van toepassing op knooppunt 2 van het pacemakercluster.

1. **[A]** De schijfindeling instellen: **Logische volumebeheer (LVM).**

   We raden u aan LVM te gebruiken voor volumes die gegevens en logboekbestanden opslaan. In het volgende voorbeeld wordt ervan uitgegaan dat er vier gegevensschijven zijn gekoppeld die worden gebruikt om twee volumes te maken.

   Vermeld alle beschikbare schijven:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Voorbeelduitvoer:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak fysieke volumes voor alle schijven die u wilt gebruiken:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak een volumegroep voor de gegevensbestanden. Gebruik één volumegroep voor de logboekbestanden en één voor de gedeelde map van SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak de logische volumes. Er wordt een lineair `lvcreate` volume `-i` gemaakt wanneer u zonder de schakelaar gebruikt. We raden u aan een gestreept volume te maken `-i` voor betere I/O-prestaties, waarbij het argument het nummer van het onderliggende fysieke volume moet zijn. In dit document worden twee fysieke volumes gebruikt `-i` voor het gegevensvolume, zodat het argument voor de switch is ingesteld op **2**. Eén fysiek volume wordt gebruikt voor `-i` het logboekvolume, zodat er geen schakelaar expliciet wordt gebruikt. Gebruik `-i` de schakelaar en stel deze in op het nummer van het onderliggende fysieke volume wanneer u meer dan één fysiek volume gebruikt voor elke gegevens, logboeken of gedeelde volumes.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Maak de mount mappen en kopieer de UUID van alle logische volumes:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Maak `fstab` items voor de drie logische volumes:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Voeg de volgende `/etc/fstab` regel in het bestand in:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Monteer de nieuwe volumes:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** De schijfindeling instellen: **Plain Disks**.

   Voor demosystemen u uw HANA-gegevens en logbestanden op één schijf plaatsen. Maak een partitie op /dev/disk/azure/scsi1/lun0 en maak deze op met xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Voeg deze regel in het bestand /etc/fstab in:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Maak de doelmap en monteer de schijf:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Host name resolution instellen voor alle hosts.

   U een DNS-server gebruiken of het /etc/hosts-bestand op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruiken.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels in het bestand /etc/hosts in. Wijzig het IP-adres en de hostnaam op uw omgeving:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Installeer de SAP HANA pakketten met hoge beschikbaarheid:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Als u SAP HANA-systeemreplicatie wilt installeren, volgt u hoofdstuk 4 van de [sap HANA SR-prestatiehandleiding voor geoptimaliseerdscenario.](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)

1. **[A]** Voer het **hdblcm-programma** uit vanaf de HANA DVD. Voer de volgende waarden in op de prompt:
   * Kies installatie: Voer **1**in.
   * Selecteer extra onderdelen voor installatie: Enter **1**.
   * Voer Installatiepad in [/hana/gedeeld]: Selecteer Enter.
   * Voer de naam van de lokale host in [..]: Selecteer Enter.
   * Wilt u extra hosts toevoegen aan het systeem? (y/n) [n]: Selecteer Enter.
   * Voer SAP HANA-systeem-id in: Voer de SID van HANA in, bijvoorbeeld: **HN1**.
   * Voer instantienummer [00] in: voer het HANA-instantienummer in. Voer **03** in als u de Azure-sjabloon hebt gebruikt of de sectie handmatige implementatie van dit artikel hebt gevolgd.
   * Selecteer Databasemodus / Voer index in [1]: Selecteer Enter.
   * Selecteer Systeemgebruik / Enter Index [4]: Selecteer de waarde van het systeemgebruik.
   * Voer locatie van gegevensvolumes in [/hana/data/HN1]: Selecteer Enter.
   * Voer locatie van logboekvolumes in [/hana/log/HN1]: Selecteer Enter.
   * Maximale geheugentoewijzing beperken? [n]: Selecteer Enter.
   * Voer de naam van certificate host in voor host '...' [...]: Selecteer Enter.
   * Voer sapadm-wachtwoord (SAPADM) van SAP Host Agent In: voer het gebruikerswachtwoord van de hostagent in.
   * Sapadm-wachtwoord bevestigen: voer het gebruikerswachtwoord van de hostagent opnieuw in om dit te bevestigen.
   * Systeembeheerder (hdbadm) Wachtwoord invoeren: voer het wachtwoord van de systeembeheerder in.
   * Systeembeheerder (hdbadm) wachtwoord bevestigen: voer het wachtwoord van de systeembeheerder opnieuw in om dit te bevestigen.
   * Voer de thuismap systeembeheerder [/usr/sap/HN1/home] in: Selecteer Enter.
   * Voer de shell voor het aanmelden van systeembeheerder [/bin/sh] in: Selecteer Enter.
   * Voer de gebruikersnaam van de systeembeheerder in [1001]: selecteer Enter.
   * Voer id van gebruikersgroep (sapsys) in [79]: Selecteer Enter.
   * Systeemwachtwoord (Databasegebruiker) invoeren: voer het wachtwoord van de databasegebruiker in.
   * Systeemwachtwoord (Databasegebruiker bevestigen): voer het wachtwoord van de databasegebruiker opnieuw in om dit te bevestigen.
   * Herstart systeem na het opnieuw opstarten van de machine? [n]: Selecteer Enter.
   * Wilt u doorgaan? (y/n): Valideer de samenvatting. Voer **y** in om door te gaan.

1. **[A]** Upgrade de SAP Host Agent.

   Download het nieuwste SAP Host Agent-archief van het [SAP Software Center][sap-swcenter] en voer de volgende opdracht uit om de agent te upgraden. Vervang het pad naar het archief om naar het bestand te wijzen dat u hebt gedownload:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0-systeemreplicatie configureren

De stappen in deze sectie gebruiken de volgende voorvoegsels:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het pacemakercluster.

1. **[1]** Maak de tenantdatabase.

   Als u SAP HANA 2.0 of MDC gebruikt, maakt u een tenantdatabase voor uw SAP NetWeaver-systeem. Vervang **NW1** door de SID van uw SAP-systeem.

   Voer de volgende opdracht uit\>als <hanasid adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Systeemreplicatie configureren op het eerste knooppunt:

   Een back-up van de\>databases als <hanasid adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopieer de PKI-bestanden van het systeem naar de secundaire site:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   De primaire site maken:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Systeemreplicatie configureren op het tweede knooppunt:
    
   Registreer het tweede knooppunt om de systeemreplicatie te starten. Voer de volgende opdracht uit\>als <hanasid adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0-systeemreplicatie configureren

De stappen in deze sectie gebruiken de volgende voorvoegsels:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het pacemakercluster.

1. **[1]** Maak de vereiste gebruikers.

   Voer de volgende opdracht uit als hoofd. Zorg ervoor dat u vette tekenreeksen (HANA System ID **HN1** en instantienummer **03)** vervangt door de waarden van uw SAP HANA-installatie:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Maak de keystore-vermelding.

   Voer de volgende opdracht uit als hoofdom een nieuw keystore-item te maken:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Een back-up van de database.

   Een back-up maken van de databases als root:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Als u een installatie met meerdere tenant's gebruikt, maakt u ook een back-up van de tenantdatabase:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Systeemreplicatie configureren op het eerste knooppunt.

   Maak de primaire site als\><hanasid adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Systeemreplicatie configureren op het secundaire knooppunt.

   Registreer de secundaire site\>als <hanasid adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-clusterbronnen maken

Maak eerst de HANA-topologie. Voer de volgende opdrachten uit op een van de clusterknooppunten van pacemakers:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Maak vervolgens de HANA-resources:

> [!IMPORTANT]
> Recente tests brachten situaties aan het licht, waarin netcat stopt met reageren op verzoeken vanwege achterstand en de beperking van de afhandeling van slechts één verbinding. De netcat-bron luistert niet meer naar de aanvragen voor Azure Load balancer en het zwevende IP-adres is niet meer beschikbaar.  
> Voor bestaande Pacemaker clusters, raden we in het verleden ter vervanging van netcat door socat. Momenteel raden we u aan azure-lb resource agent, die deel uitmaakt van pakket resource-agents, met de volgende pakketversie vereisten:
> - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018.a7fb5035-3.30.1 zijn.  
> - Voor SLES 15/15 SP1 moet de versie ten minste resourceagents-4.3.0184.6ee15eb2-4.13.1 zijn.  
>
> Houd er rekening mee dat de wijziging korte downtime vereist.  
> Als de configuratie al is gewijzigd om socat te gebruiken zoals beschreven in [Azure Load-Balancer Detection Hardening,](https://www.suse.com/support/kb/doc/?id=7024128)is er geen verplichting om onmiddellijk over te schakelen naar azure-lb resource agent.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b>

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de resources worden uitgevoerd.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>De clusterinstelling testen

In dit gedeelte wordt beschreven hoe u uw installatie testen. Elke test gaat ervan uit dat je root en de SAP HANA master draait op de **hn1-db-0** virtuele machine.

### <a name="test-the-migration"></a>De migratie testen

Voordat u de test start, moet u ervoor zorgen dat Pacemaker geen mislukte actie heeft (via crm_mon -r), er geen onverwachte locatiebeperkingen zijn (bijvoorbeeld restjes van een migratietest) en dat HANA de synchronisatiestatus is, bijvoorbeeld met SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

U het SAP HANA-hoofdknooppunt migreren door de volgende opdracht uit te voeren:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Als u `AUTOMATED_REGISTER="false"`deze reeks opdrachten instelt, moet het SAP HANA-hoofdknooppunt migreren en de groep die het virtuele IP-adres bevat naar hn1-db-1.

Zodra de migratie is gedaan, ziet de crm_mon-r-uitvoer er als volgt uit

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

De SAP HANA-bron op hn1-db-0 start niet als secundair. Configureer in dit geval de HANA-instantie als secundair door deze opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

De migratie creëert locatiebeperkingen die opnieuw moeten worden verwijderd:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

U moet ook de status van de secundaire knooppuntbron opschonen:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Controleer de status van de HANA-bron met behulp van crm_mon -r. Zodra HANA is gestart op hn1-db-0, moet de output er zo uitzien

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>De Azure-afrasteringsagent testen (niet SBD)

U de installatie van de Azure-afrasteringsagent testen door de netwerkinterface op het hn1-db-0-knooppunt uit te schakelen:

<pre><code>sudo ifdown eth0
</code></pre>

De virtuele machine moet nu opnieuw opstarten of stoppen, afhankelijk van uw clusterconfiguratie.
Als u `stonith-action` de instelling instelt op uitgeschakeld, wordt de virtuele machine gestopt en worden de resources gemigreerd naar de draaiende virtuele machine.

Nadat u de virtuele machine opnieuw hebt gestart, kan de `AUTOMATED_REGISTER="false"`SAP HANA-bron niet als secundair beginnen als u deze instelt. Configureer in dit geval de HANA-instantie als secundair door deze opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Test SBD hekwerk

U de installatie van SBD testen door het inquisiteitorproces te doden.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Clusterknooppunt hn1-db-0 moet opnieuw worden opgestart. De Pacemaker-service gaat mogelijk daarna niet meer van start. Zorg ervoor dat u het opnieuw start.

### <a name="test-a-manual-failover"></a>Een handmatige failover testen

U een handmatige failover testen door de `pacemaker` service te stoppen op het hn1-db-0-knooppunt:

<pre><code>service pacemaker stop
</code></pre>

Na de failover u de service opnieuw starten. Als u `AUTOMATED_REGISTER="false"`deze instelt, wordt de SAP HANA-bron op het hn1-db-0-knooppunt niet als secundair gestart. Configureer in dit geval de HANA-instantie als secundair door deze opdracht uit te voeren:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-tests

> [!IMPORTANT]
> Zorg ervoor dat het besturingssysteem dat u selecteert SAP-gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. De lijst van SAP HANA gecertificeerde VM-typen en OS-releases voor die kunnen worden opgezocht in [SAP HANA Certified IaaS Platforms.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Zorg ervoor dat u klikt op de details van het vm-type dat wordt vermeld om de volledige lijst met door SAP HANA ondersteunde OS-releases voor het specifieke VM-type te krijgen

Voer alle testcases uit die worden vermeld in het SAP HANA SR Performance Optimized Scenario of SAP HANA SR Cost Optimized Scenario-handleiding, afhankelijk van uw use case. U vindt de gidsen op de [pagina SLES for SAP best practices.][sles-for-sap-bp]

De volgende tests zijn een kopie van de testbeschrijvingen van de SAP HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server voor SAP Applications 12 SP1 handleiding. Voor een up-to-date versie, lees ook altijd de gids zelf. Zorg er altijd voor dat HANA gesynchroniseerd is voordat de test begint en zorg er ook voor dat de pacemakerconfiguratie correct is.

In de volgende testbeschrijvingen gaan we uit PREFER_SITE_TAKEOVER="waar" en AUTOMATED_REGISTER="false".
OPMERKING: De volgende tests zijn ontworpen om achter elkaar te worden uitgevoerd en zijn afhankelijk van de uitgangsstatus van de voorgaande tests.

1. TEST 1: PRIMAIRE DATABASE STOPPEN OP NODE 1

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit\>als <hanasid adm op node hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker moet detecteren de gestoptHANA instantie en failover naar het andere knooppunt. Zodra de failover is gedaan, wordt de HANA-instantie op node hn1-db-0 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als HANA secundair.

   Voer de volgende opdrachten uit om knooppunt hn1-db-0 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: PRIMAIRE DATABASE STOPPEN OP KNOOPPUNT 2

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende commando's\>uit als <hanasid adm op node hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker moet detecteren de gestoptHANA instantie en failover naar het andere knooppunt. Zodra de failover is gedaan, wordt de HANA-instantie op node hn1-db-1 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als HANA secundair.

   Voer de volgende opdrachten uit om knooppunt hn1-db-1 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: CRASH PRIMAIRE DATABASE OP NODE

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit\>als <hanasid adm op node hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker moet detecteren de gedode HANA instantie en failover naar het andere knooppunt. Zodra de failover is gedaan, wordt de HANA-instantie op node hn1-db-0 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als HANA secundair.

   Voer de volgende opdrachten uit om knooppunt hn1-db-0 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: CRASH PRIMARY DATABASE ON NODE 2 TEST 4: CRASH PRIMARY DATABASE ON NODE 2 TEST 4: CRASH PRIMARY DATABASE ON NODE 2 TEST 4

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende commando's\>uit als <hanasid adm op node hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker moet detecteren de gedode HANA instantie en failover naar het andere knooppunt. Zodra de failover is gedaan, wordt de HANA-instantie op node hn1-db-1 gestopt omdat Pacemaker het knooppunt niet automatisch registreert als HANA secundair.

   Voer de volgende opdrachten uit om knooppunt hn1-db-1 als secundair te registreren en de mislukte resource op te schonen.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: CRASH PRIMARY SITE NODE (NODE 1)

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als root op knooppunt hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet detecteren de gedode cluster knooppunt en hek het knooppunt. Zodra het knooppunt is omheind, zal Pacemaker leiden tot een overname van de HANA-instantie. Wanneer het omheinde knooppunt opnieuw wordt opgestart, wordt pacemaker niet automatisch gestart.

   Voer de volgende opdrachten uit om Pacemaker te starten, de SBD-berichten voor knooppunt hn1-db-0 schoon te maken, registreer knooppunt hn1-db-0 als secundair en ruim de mislukte bron op.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: CRASH SECONDARY SITE NODE (NODE 2)

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Voer de volgende opdrachten uit als root op knooppunt hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet detecteren de gedode cluster knooppunt en hek het knooppunt. Zodra het knooppunt is omheind, zal Pacemaker leiden tot een overname van de HANA-instantie. Wanneer het omheinde knooppunt opnieuw wordt opgestart, wordt pacemaker niet automatisch gestart.

   Voer de volgende opdrachten uit om Pacemaker te starten, de SBD-berichten voor knooppunt hn1-db-1 schoon te maken, registreer knooppunt hn1-db-1 als secundair en ruim de mislukte bron op.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: STOP DE SECUNDAIRE DATABASE OP KNOOPPUNT 2

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende commando's\>uit als <hanasid adm op node hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker detecteert de gestopte HANA-instantie en markeert de bron als mislukt op knooppunt hn1-db-1. Pacemaker moet automatisch opnieuw opstarten van de HANA instantie. Voer de volgende opdracht uit om de mislukte status op te schonen.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: CRASH DE SECUNDAIRE DATABASE OP KNOOPPUNT 2

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende commando's\>uit als <hanasid adm op node hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker detecteert de gedood HANA-instantie en markeert de bron als mislukt op knooppunt hn1-db-1. Voer de volgende opdracht uit om de mislukte status op te schonen. Pacemaker moet dan automatisch opnieuw de HANA instantie.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: CRASH SECONDARY SITE NODE (NODE 2) RUNNING SECONDARY HANA DATABASE

   Resourcestatus voordat de test wordt gestart:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Voer de volgende opdrachten uit als root op knooppunt hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker moet detecteren de gedode cluster knooppunt en hek het knooppunt. Wanneer het omheinde knooppunt opnieuw wordt opgestart, wordt pacemaker niet automatisch gestart.

   Voer de volgende opdrachten uit om Pacemaker te starten, de SBD-berichten voor knooppunt hn1-db-1 schoon te maken en de mislukte bron op te ruimen.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Resourcestatus na de test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]

