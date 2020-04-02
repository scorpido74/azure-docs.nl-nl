---
title: Hoge beschikbaarheid van SAP HANA op Azure VM's op RHEL | Microsoft Documenten
description: Stel een hoge beschikbaarheid van SAP HANA vast op virtuele Azure-machines (VM's).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/31/2020
ms.author: radeltch
ms.openlocfilehash: f1ae2c3c949e8bdbf30c8bef496177d56cd2dcbd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521399"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hoge beschikbaarheid van SAP HANA op Azure VM's op Red Hat Enterprise Linux

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Voor on-premises ontwikkeling u HANA-systeemreplicatie gebruiken of gedeelde opslag gebruiken om een hoge beschikbaarheid voor SAP HANA vast te stellen.
Op Azure virtual machines (VM's) is HANA System Replication op Azure momenteel de enige ondersteunde functie met hoge beschikbaarheid.
SAP HANA-replicatie bestaat uit één primair knooppunt en ten minste één secundair knooppunt. Wijzigingen in de gegevens op het primaire knooppunt worden synchroon of asynchroon gerepliceerd naar het secundaire knooppunt.

In dit artikel wordt beschreven hoe u de virtuele machines implementeert en configureert, het clusterframework installeert en SAP HANA-systeemreplicatie installeert en configureert.
In de voorbeeldconfiguraties worden installatieopdrachten, instantienummer **03**en HANA System ID **HN1** gebruikt.

Lees eerst de volgende SAP Notes en papers:

* SAP Note [1928533], die heeft:
  * De lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software.
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten.
  * De ondersteunde SAP-software en besturingssysteem (OS) en databasecombinaties.
  * De vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure.
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
* [SAP HANA-systeemreplicatie in pacemakercluster](https://access.redhat.com/articles/3004101)
* Algemene RHEL-documentatie
  * [Overzicht van invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On-beheer met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Invoegtoepassing met hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-specifieke RHEL-documentatie:
  * [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - Microsoft Azure Virtual Machines als clusterleden](https://access.redhat.com/articles/3131341)
  * [Een Red Hat Enterprise Linux 7.4 (en hoger) cluster met hoge beschikbaarheid installeren en configureren op Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [SAP HANA installeren op Red Hat Enterprise Linux voor gebruik in Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Overzicht

Om een hoge beschikbaarheid te bereiken, is SAP HANA geïnstalleerd op twee virtuele machines. De gegevens worden gerepliceerd met HANA-systeemreplicatie.

![SAP HANA overzicht van hoge beschikbaarheid](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA System Replication setup maakt gebruik van een speciale virtuele hostname en virtuele IP-adressen. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. In de volgende lijst ziet u de configuratie van de load balancer:

* Front-end configuratie: IP-adres 10.0.0.13 voor hn1-db
* Back-endconfiguratie: verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeemreplicatie
* Sondepoort: poort 62503
* Regels voor taakverdeling: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Implementeren voor Linux

De Azure Marketplace bevat een afbeelding voor Red Hat Enterprise Linux 7.4 voor SAP HANA die u gebruiken om nieuwe virtuele machines te implementeren.

### <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U een van de quickstartsjablonen die zich op GitHub bevinden, gebruiken om alle vereiste resources te implementeren. De sjabloon implementeert de virtuele machines, de load balancer, de beschikbaarheidsset, enzovoort.
Voer de volgende stappen uit om de sjabloon te implementeren:

1. Open de [databasesjabloon][template-multisid-db] op de Azure-portal.
1. Voer de volgende parameters in:
    * **Sap-systeem-id:** voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De id wordt gebruikt als voorvoegsel voor de resources die worden geïmplementeerd.
    * **Os Type**: Selecteer een van de Linux-distributies. Selecteer in dit voorbeeld **RHEL 7**.
    * **Db-type**: Selecteer **HANA**.
    * **Sap-systeemgrootte:** voer het aantal SAPS in dat het nieuwe systeem gaat bieden. Als u niet zeker weet hoeveel SAPS het systeem nodig heeft, vraag het dan aan uw SAP Technology Partner of System Integrator.
    * **Beschikbaarheid van het systeem**: Selecteer **HA**.
    * **Gebruikersnaam, beheerderswachtwoord of SSH-sleutel:** Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om zich aan te melden bij de machine.
    * **Subnet-id**: Als u de VM wilt implementeren in een bestaand VNet waarbij een subnet is gedefinieerd waaraan de VM moet worden toegewezen, geeft u de id van dat specifieke subnet een naam. De ID ziet er meestal uit als **/abonnementen/\<abonnements-ID>/resourceGroepen/\<resourcegroepnaam>/providers/Microsoft.Network/virtualNetworks/\<virtuele netwerknaam>/subnets/\<subnetnaam>**. Leeg laten, als u een nieuw virtueel netwerk wilt maken

### <a name="manual-deployment"></a>Handmatige implementatie

1. Maak een resourcegroep.
1. Maak een virtueel netwerk.
1. Maak een beschikbaarheidsset.  
   Stel het domein voor maximale update in.
1. Maak een load balancer (intern). Wij raden [standaard load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)aan.
   * Selecteer het virtuele netwerk dat in stap 2 is gemaakt.
1. Maak virtuele machine 1.  
   Gebruik ten minste Red Hat Enterprise Linux 7.4 voor SAP HANA. In dit voorbeeld wordt de Red Hat Enterprise <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Linux 7.4 gebruikt voor SAP HANA-afbeelding Selecteer de beschikbaarheidsset die is gemaakt in stap 3.
1. Maak virtuele machine 2.  
   Gebruik ten minste Red Hat Enterprise Linux 7.4 voor SAP HANA. In dit voorbeeld wordt de Red Hat Enterprise <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Linux 7.4 gebruikt voor SAP HANA-afbeelding Selecteer de beschikbaarheidsset die is gemaakt in stap 3.
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
   1. Configureer de load balancer. Maak eerst een front-end IP-pool:

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

## <a name="install-sap-hana"></a>SAP HANA installeren

De stappen in deze sectie gebruiken de volgende voorvoegsels:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het pacemakercluster.

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

   Maak de logische volumes. Er wordt een lineair `lvcreate` volume `-i` gemaakt wanneer u zonder de schakelaar gebruikt. We raden u aan een gestreept volume te maken voor betere I/O-prestaties en de streepgrootteaf te stemmen op de waarden die zijn gedocumenteerd in [SAP HANA VM-opslagconfiguraties.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Het `-i` argument moet het aantal van de `-I` onderliggende fysieke volumes zijn en het argument is de streepgrootte. In dit document worden twee fysieke volumes gebruikt `-i` voor het gegevensvolume, zodat het argument voor de switch is ingesteld op **2**. De streepgrootte voor het gegevensvolume is **256KiB.** Eén fysiek volume wordt gebruikt voor `-i` het `-I` logboekvolume, zodat er geen of schakelaars expliciet worden gebruikt voor de opdrachten voor het logboekvolume.  

   > [!IMPORTANT]
   > Gebruik `-i` de schakelaar en stel deze in op het nummer van het onderliggende fysieke volume wanneer u meer dan één fysiek volume gebruikt voor elke gegevens, logboeken of gedeelde volumes. Gebruik `-I` de schakelaar om de streepgrootte op te geven bij het maken van een gestreept volume.  
   > Zie [SAP HANA VM-opslagconfiguraties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) voor aanbevolen opslagconfiguraties, inclusief streepgroottes en het aantal schijven.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
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

1. **[A]** RHEL voor HANA-configuratie

   Configureer RHEL zoals beschreven in SAP Note [2292690] <https://access.redhat.com/solutions/2447641>en [2455582] en .

1. **[A]** Installeer de SAP HANA

   Als u SAP HANA-systeemreplicatie wilt installeren, volgt u <https://access.redhat.com/articles/3004101>.

   * Voer het **hdblcm-programma** uit vanaf de HANA DVD. Voer de volgende waarden in op de prompt:
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

1. **[A]** Firewall configureren

   Maak de firewallregel voor de azure load balancer probe-poort.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0-systeemreplicatie configureren

De stappen in deze sectie gebruiken de volgende voorvoegsels:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het pacemakercluster.

1. **[A]** Firewall configureren

   Maak firewallregels om HANA-systeemreplicatie en clientverkeer toe te staan. De vereiste poorten worden vermeld op [TCP/IP-poorten van alle SAP-producten.](https://help.sap.com/viewer/ports) De volgende opdrachten zijn slechts een voorbeeld waarmee HANA 2.0 System Replication en clientverkeer naar database SYSTEMDB, HN1 en NW1 kunnen worden gebracht.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Maak de tenantdatabase.

   Als u SAP HANA 2.0 of MDC gebruikt, maakt u een tenantdatabase voor uw SAP NetWeaver-systeem. Vervang **NW1** door de SID van uw SAP-systeem.

   Voer uit als\><hanasid adm de volgende opdracht:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Systeemreplicatie configureren op het eerste knooppunt:

   Back-up van de\>databases als <hanasid adm:

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

1. **[1]** Replicatiestatus controleren

   Controleer de replicatiestatus en wacht tot alle databases gesynchroniseerd zijn. Als de status onbekend blijft, controleert u de firewall-instellingen.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0-systeemreplicatie configureren

De stappen in deze sectie gebruiken de volgende voorvoegsels:

* **[A]**: De stap is van toepassing op alle knooppunten.
* **[1]**: De stap is alleen van toepassing op knooppunt 1.
* **[2]**: De stap is alleen van toepassing op knooppunt 2 van het pacemakercluster.

1. **[A]** Firewall configureren

   Maak firewallregels om HANA-systeemreplicatie en clientverkeer toe te staan. De vereiste poorten worden vermeld op [TCP/IP-poorten van alle SAP-producten.](https://help.sap.com/viewer/ports) De volgende opdrachten zijn slechts een voorbeeld om HANA 2.0-systeemreplicatie toe te staan. Pas het aan uw SAP HANA 1.0 installatie aan.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Een pacemakercluster maken

Volg de stappen in [Het instellen van Pacemaker op Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) om een basispacemakercluster voor deze HANA-server te maken.

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-clusterbronnen maken

Installeer de SAP HANA-resourceagents op **alle knooppunten.** Zorg ervoor dat u een opslagplaats inschakelt die het pakket bevat.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Maak vervolgens de HANA-topologie. Voer de volgende opdrachten uit op een van de clusterknooppunten van pacemakers:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
--clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Maak vervolgens de HANA-resources:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de resources worden uitgevoerd.

> [!NOTE]
> De time-outs in de bovenstaande configuratie zijn slechts voorbeelden en moeten mogelijk worden aangepast aan de specifieke HANA-setup. Het kan bijvoorbeeld nodig zijn om de starttime-out te verhogen, als het langer duurt om de SAP HANA-database te starten.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>De clusterinstelling testen

In dit gedeelte wordt beschreven hoe u uw installatie testen. Voordat u een test start, moet u ervoor zorgen dat pacemaker geen mislukte actie heeft (via de pc-status), er geen onverwachte locatiebeperkingen zijn (bijvoorbeeld restjes van een migratietest) en dat HANA de synchronisatiestatus is, bijvoorbeeld met systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>De migratie testen

Resourcestatus voordat de test wordt gestart:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U het SAP HANA-hoofdknooppunt migreren door de volgende opdracht uit te voeren:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Als u `AUTOMATED_REGISTER="false"`deze opdracht instelt, moet deze opdracht het SAP HANA-hoofdknooppunt migreren en de groep die het virtuele IP-adres bevat naar hn1-db-1.

Zodra de migratie is gedaan, de 'sudo pcs status' output ziet er als volgt

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

De SAP HANA-bron op hn1-db-0 wordt gestopt. Configureer in dit geval de HANA-instantie als secundair door deze opdracht uit te voeren:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

De migratie creëert locatiebeperkingen die opnieuw moeten worden verwijderd:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Controleer de status van de HANA-bron met behulp van 'pcs-status'. Zodra HANA is gestart op hn1-db-0, moet de output er zo uitzien

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>De Azure-afrasteringsagent testen

Resourcestatus voordat de test wordt gestart:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

U de installatie van de Azure-afrasteringsagent testen door de netwerkinterface uit te schakelen op het knooppunt waar SAP HANA als Master wordt uitgevoerd.
Zie [Red Hat Knowledgebase artikel 79523](https://access.redhat.com/solutions/79523) voor een beschrijving over het simuleren van een netwerkstoring. In dit voorbeeld gebruiken we het net_breaker script om alle toegang tot het netwerk te blokkeren.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

De virtuele machine moet nu opnieuw opstarten of stoppen, afhankelijk van uw clusterconfiguratie.
Als u `stonith-action` de instelling instelt op uitgeschakeld, wordt de virtuele machine gestopt en worden de resources gemigreerd naar de draaiende virtuele machine.

> [!NOTE]
> Het kan tot 15 minuten duren voordat de virtuele machines weer online zijn.

Nadat u de virtuele machine opnieuw hebt gestart, kan de `AUTOMATED_REGISTER="false"`SAP HANA-bron niet als secundair beginnen als u deze instelt. Configureer in dit geval de HANA-instantie als secundair door deze opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Resourcestatus na de test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Een handmatige failover testen

Resourcestatus voordat de test wordt gestart:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U een handmatige failover testen door het cluster te stoppen op het hn1-db-0-knooppunt:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Na de failover u het cluster opnieuw starten. Als u `AUTOMATED_REGISTER="false"`deze instelt, wordt de SAP HANA-bron op het hn1-db-0-knooppunt niet als secundair gestart. Configureer in dit geval de HANA-instantie als secundair door deze opdracht uit te voeren:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Resourcestatus na de test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [SAP HANA (grote exemplaren) hoge beschikbaarheid en herstel na noodgevallen op Azure](hana-overview-high-availability-disaster-recovery.md) voor meer informatie over het instellen van hoge beschikbaarheid en plannen voor noodherstel van SAP HANA op Azure (grote exemplaren)
