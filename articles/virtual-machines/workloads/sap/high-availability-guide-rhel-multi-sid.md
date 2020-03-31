---
title: Azure VM's hoge beschikbaarheid voor SAP NW op RHEL multi-SID-gids | Microsoft Documenten
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
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247432"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op Red Hat Enterprise Linux voor SAP-toepassingen multi-SID-handleiding

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

In dit artikel wordt beschreven hoe u meerdere SAP NetWeaver-systemen (dat wil zeggen multi-SID) implementeert in een cluster met twee clients op Azure VM's met Red Hat Enterprise Linux voor SAP-toepassingen.  

In de voorbeeldconfiguraties worden installatieopdrachten etc. drie SAP NetWeaver 7.50-systemen geïmplementeerd in één cluster met twee knooppunts hoge beschikbaarheid. De SAP-systemen SIDs zijn:
* **NW1**: ASCS-instantienummer **00** en virtuele hostnaam **msnw1ascs**; ERS-instantienummer **02** en virtuele hostnaam **msnw1ers**.  
* **NW2**: ASCS instance number **10** en virtual hostname **msnw2ascs**; ERS instantie nummer **12** en virtuele host naam **msnw2ers**.  
* **NW3**: ASCS-instantienummer **20** en virtuele hostname **msnw3ascs**; ERS instantie nummer **22** en virtuele host naam **msnw3ers**.  

Het artikel heeft geen betrekking op de databaselaag en de implementatie van de SAP NFS-shares. In de voorbeelden in dit artikel gebruiken we het volume **sapMSID** van [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) voor de NFS-shares, ervan uitgaande dat het volume al is geïmplementeerd. We gaan er ook van uit dat het volume van Azure NetApp-bestanden is geïmplementeerd met het NFSv3-protocol en dat de volgende bestandspaden bestaan voor de clusterbronnen voor de ASCS- en ERS-exemplaren van SAP-systemen NW1, NW2 en NW3:  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volumesapMSID (nfs://10.42.0.4/usrsap<b>NW1-ascs)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volumesapMSID (nfs://10.42.0.4/usrsap<b>NW2-ascs)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volumesapMSID (nfs://10.42.0.4/usrsap<b>NW3-ascs)</b>
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Raadpleeg eerst de volgende SAP-notities en -documenten voordat u begint:

* SAP Note [1928533], die heeft:
  * Lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten
  * Ondersteunde SAP-software en besturingssysteem- en databasecombinaties
  * Vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure
* [Azure NetApp-bestandendocumentatie][anf-azure-doc]
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
* [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp-bestanden][anf-sap-applications-azure]

## <a name="overview"></a>Overzicht

De virtuele machines, die deelnemen aan het cluster moeten worden aangepast om alle resources te kunnen uitvoeren, voor het geval er een fail-over plaatsvindt. Elke SAP SID kan onafhankelijk van elkaar mislukken in het cluster met hoge beschikbaarheid met meerdere SID.Each SAP SID can fail over independent from each other in the multi-SID high availability cluster.  

Om een hoge beschikbaarheid te bereiken, heeft SAP NetWeaver sterk beschikbare aandelen nodig. In deze documentatie presenteren we de voorbeelden met de SAP-shares die zijn geïmplementeerd op [Azure NetApp Files NFS-volumes.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) Het is ook mogelijk om de aandelen te hosten op een hoog beschikbaar [GlusterFS-cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), dat door meerdere SAP-systemen kan worden gebruikt.  

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> De ondersteuning voor multi-SID clustering van SAP ASCS/ERS met Red Hat Linux als gastbesturingssysteem in Azure VM's is beperkt tot **vijf** SAP SID's op hetzelfde cluster. Elke nieuwe SID verhoogt de complexiteit. Een mix van SAP Enqueue Replication Server 1 en Enqueue Replication Server 2 op hetzelfde cluster **wordt niet ondersteund.** Multi-SID clustering beschrijft de installatie van meerdere SAP ASCS/ERS-exemplaren met verschillende SidDs in één Pacemaker-cluster. Momenteel wordt multi-SID clustering alleen ondersteund voor ASCS/ERS.  

> [!TIP]
> De multi-SID clustering van SAP ASCS/ERS is een oplossing met een hogere complexiteit. Het is complexer uit te voeren. Het gaat ook om een hogere administratieve inspanning, bij het uitvoeren van onderhoudsactiviteiten (zoals het patchen van het besturingssysteem). Voordat u de daadwerkelijke implementatie start, neemt u de tijd om de implementatie en alle betrokken onderdelen zoals VM's, NFS-bevestigingen, VIP's, load balancer-configuraties enzovoort zorgvuldig te plannen.  

SAP NetWeaver ASCS, SAP NetWeaver SCS en SAP NetWeaver ERS gebruiken virtuele hostname en virtuele IP-adressen. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. We raden u aan [standaard load balancer te gebruiken.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)  

De volgende lijst toont de configuratie van de (A)SCS en ERS load balancer voor dit multi-SID cluster voorbeeld met drie SAP-systemen. U hebt afzonderlijke frontend IP, health probes en load-balancing rules nodig voor elke ASCS en ERS bijvoorbeeld voor elk van de SID's. Wijs alle VM's toe die deel uitmaken van het ASCS/ASCS-cluster aan één backendpool van één ILB.  

### <a name="ascs"></a>(A)SCS

* Frontend-configuratie
  * IP-adres voor NW1: 10.3.1.50
  * IP-adres voor NW2: 10.3.1.52
  * IP-adres voor NW3: 10.3.1.54

* Sondepoorten
  * Poort 620<strong>&lt;&gt;nr</strong>, dus voor NW1, NW2 en NW3 sonde poorten 620**00**, 620**10** en 620**20**
* Load-balancing rules - maak er een voor elke instantie, dat wil zeggen NW1/ASCS, NW2/ASCS en NW3/ASCS.
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
  * IP-adres voor NW1 10.3.1.51
  * IP-adres voor NW2 10.3.1.53
  * IP-adres voor NW3 10.3.1.55

* Sondepoort
  * Poort 621<strong>&lt;&gt;nr</strong>, dus voor NW1, NW2 en N3 sonde poorten 621**02**, 621**12** en 621**22**
* Load-balancing regels - maak er een voor elke instantie, dat wil zeggen, NW1/ERS, NW2/ERS en NW3/ERS.
  * Als u Standard Load Balancer gebruikt, selecteert u **HA-poorten**
  * Als u Basic Load Balancer gebruikt, maakt u regels voor taakverdeling voor de volgende poorten
    * 32<strong>&lt;&gt; nr.</strong>
    * 33<strong>&lt;&gt; nr.</strong>
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Backend-configuratie
  * Verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van het (A)SCS/ERS-cluster

> [!Note]
> Wanneer VM's zonder openbare IP-adressen in de backendpool van interne (geen openbare IP-adres) Standaard Azure-loadbalancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij extra configuratie wordt uitgevoerd om routering naar openbare eindpunten mogelijk te maken. Zie [Openbare eindpuntconnectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)met hoge beschikbaarheid voor meer informatie over het bereiken van uitgaande connectiviteit.  

> [!IMPORTANT]
> Schakel geen TCP-tijdstempels in op Azure VM's die achter Azure Load Balancer zijn geplaatst. Als u TCP-tijdstempels inschakelt, worden de statussprobes mislukt. Stel parameter **net.ipv4.tcp_timestamps** in op **0**. Zie [Health Probes van Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)meer informatie .

## <a name="sap-shares"></a>SAP aandelen

SAP NetWeaver vereist gedeelde opslag voor het transport, de profielmap, enzovoort. Voor het zeer beschikbare SAP-systeem is het belangrijk om zeer beschikbare aandelen te hebben. U moet beslissen over de architectuur voor uw SAP-aandelen. Een optie is het implementeren van de aandelen op [Azure NetApp Files NFS-volumes.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  Met Azure NetApp Files krijgt u een ingebouwde hoge beschikbaarheid voor de SAP NFS-shares.

Een andere optie is om [GlusterFS te bouwen op Azure VM's op Red Hat Enterprise Linux voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), die kan worden gedeeld tussen meerdere SAP-systemen. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Het eerste SAP-systeem in het cluster implementeren

Nu u hebt besloten over de architectuur voor de SAP-aandelen, implementeert u het eerste SAP-systeem in het cluster, volgens de bijbehorende documentatie.

* Als u NFS-volumes van Azure NetApp Files gebruikt, volgt u [Azure VM's met hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* Als u het GlusterFS-cluster gebruikt, volgt u [GlusterFS op Azure VM's op Red Hat Enterprise Linux voor SAP NetWeaver.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)  

De hierboven genoemde documenten begeleiden u door de stappen om de benodigde infrastructuur voor te bereiden, het cluster te bouwen, het besturingssysteem voor te bereiden op het uitvoeren van de SAP-toepassing.  

> [!TIP]
> Test altijd de functie van het cluster, nadat het eerste systeem is geïmplementeerd, voordat u de extra SAP-ID's aan het cluster toevoegt. Op die manier weet u dat de clusterfunctionaliteit werkt, voordat u de complexiteit van extra SAP-systemen aan het cluster toevoegt.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Extra SAP-systemen implementeren in het cluster

In dit voorbeeld gaan we ervan uit dat systeem **NW1** al in het cluster is geïmplementeerd. We zullen laten zien hoe te implementeren in het cluster SAP-systemen **NW2** en **NW3**. 

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

### <a name="prerequisites"></a>Vereisten 

> [!IMPORTANT]
> Voordat u de instructies volgt om extra SAP-systemen in het cluster te implementeren, volgt u de instructies om het eerste SAP-systeem in het cluster te implementeren, omdat er stappen zijn die alleen nodig zijn tijdens de eerste systeemimplementatie.  

In deze documentatie wordt ervan uitgegaan dat:
* Het pacemakercluster is al geconfigureerd en uitgevoerd.  
* Ten minste één SAP-systeem (ASCS / ERS-instantie) is al geïmplementeerd en wordt uitgevoerd in het cluster.  
* De clusterfailoverfunctionaliteit is getest.  
* De NFS-aandelen voor alle SAP-systemen worden geïmplementeerd.  

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

1. Voeg configuratie voor het nieuw geïmplementeerde systeem (dat wil zeggen **NW2**, **NW3)** toe aan de bestaande Azure Load Balancer, volgens de instructies [Implementatie Azure Load Balancer handmatig via Azure portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal). Pas de IP-adressen, health probe-poorten, load-balancing regels voor uw configuratie aan.  

2. **[A]** Naamresolutie instellen voor de extra SAP-systemen. U dns-server `/etc/hosts` gebruiken of wijzigen op alle knooppunten. In dit voorbeeld ziet `/etc/hosts` u hoe u het bestand gebruikt.  Pas de IP-adressen en de hostnamen aan uw omgeving aan. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** Maak de gedeelde mappen voor de extra **NW2-** en **NW3** SAP-systemen die u implementeert in het cluster. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Voeg de mount-items toe voor de /sapmnt/SID- en /usr/sap/SID/SYS-bestandssystemen voor de extra SAP-systemen die u implementeert in het cluster. In dit voorbeeld **NW2** en **NW3**.  

   Update `/etc/fstab` bestand met de bestandssystemen voor de extra SAP-systemen die u implementeert in het cluster.  

   * Als u Azure NetApp-bestanden gebruikt, volgt u de [instructies hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)  
   * Als u het GlusterFS-cluster gebruikt, volgt u de instructies [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>ASCS / ERS installeren

1. Maak de clusterbronnen voor virtuele IP- en statussondes voor de ASCS-exemplaren van de extra SAP-systemen die u in het cluster implementeert. Het voorbeeld dat hier wordt weergegeven is voor **NW2** en **NW3** ASCS, waarbij NFS wordt gebruikt op Azure NetApp Files-volumes met NFSv3-protocol.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.  

2. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ASCS. Voor systeem **NW2**is de virtuele hostnaam bijvoorbeeld <b>msnw2ascs</b>, <b>10.3.1.52</b> en het instantienummer dat u voor de sonde van de load balancer hebt gebruikt, bijvoorbeeld <b>10</b>. Voor systeem **NW3**is de virtuele hostnaam <b>msnw3ascs</b>, <b>10.3.1.54</b> en het instantienummer dat u voor de sonde van de load balancer hebt gebruikt, bijvoorbeeld <b>20</b>. Noteer op welk clusterknooppunt u ASCS hebt geïnstalleerd voor elke SAP SID.  

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst. U parameterSAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van virtuele host.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Als de installatie geen submap maakt in /usr/sap/**SID**/ASCS**Instance#,** probeert u de eigenaar in te stellen op **sid**adm en groep op sapsys van de ASCS**Instance#** en opnieuw te proberen.

3. **[1]** Maak een virtueel IP- en health-probe clusterresources voor het ERS-exemplaar van het extra SAP-systeem dat u implementeert in het cluster. Het voorbeeld dat hier wordt weergegeven is voor **NW2-** en **NW3-ERS,** waarbij NFS wordt gebruikt op Azure NetApp-bestanden met NFSv3-protocol.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart.  

   Zorg er vervolgens voor dat de resources van de nieuw gemaakte ERS-groep worden uitgevoerd op het clusterknooppunt, tegenover het clusterknooppunt waar het ASCS-exemplaar voor hetzelfde SAP-systeem is geïnstalleerd.  Als NW2 ASCS bijvoorbeeld is `rhelmsscl1`geïnstalleerd op , zorg er dan `rhelmsscl2`voor dat de NW2 ERS-groep wordt uitgevoerd op .  U de GROEP NW2 ERS migreren naar door de volgende opdracht uit te `rhelmsscl2` voeren voor een van de clusterbronnen in de groep: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** SAP NetWeaver ERS installeren

   Installeer SAP NetWeaver ERS als root op het andere knooppunt, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ERS. Voor systeem **NW2**zijn de naam van de virtuele host bijvoorbeeld <b>msnw2ers</b>, <b>10.3.1.53</b> en het instantienummer dat u voor de sonde van de load balancer hebt gebruikt, bijvoorbeeld <b>12</b>. Voor systeem **NW3**, de virtuele hostnaam <b>msnw3ers</b>, <b>10.3.1.55</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>22</b>. 

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst. U parameterSAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van virtuele host.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 of hoger gebruiken. Lagere versies stellen de machtigingen niet correct in en de installatie zal mislukken.

   Als de installatie geen submap maakt in /usr/sap/**NW2**/ERS**Instance#,** probeert u de eigenaar in te stellen op **sid**adm en de groep op sapsys van de MAP ERS**Instance#** en opnieuw te proberen.

   Als u de ERS-groep van het nieuw geïmplementeerde SAP-systeem moet migreren naar een ander clusterknooppunt, vergeet dan niet de locatiebeperking voor de ERS-groep te verwijderen. U de beperking verwijderen door de volgende opdracht uit te voeren (het voorbeeld wordt gegeven voor SAP-systemen **NW2** en **NW3).** Verwijder de tijdelijke beperkingen voor dezelfde resource die u in de opdracht hebt gebruikt om de ERS-clustergroep te verplaatsen.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** De ASCS/SCS- en ERS-instantieprofielen aanpassen voor het nieuw geïnstalleerde SAP-systeem(en). Het onderstaande voorbeeld is voor NW2. U moet de ASCS/SCS- en ERS-profielen aanpassen voor alle SAP-exemplaren die aan het cluster zijn toegevoegd.  
 
   * ASCS/SCS-profiel

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * ERS-profiel

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Update het /usr/sap/sapservices-bestand

   Om het begin van de exemplaren door het sapinit-opstartscript te `/usr/sap/sapservices` voorkomen, moeten alle instanties die door Pacemaker worden beheerd, worden becommentarieerd uit het bestand.  Het voorbeeld hieronder is voor SAP-systemen **NW2** en **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Maak de SAP-clusterbronnen voor het nieuw geïnstalleerde SAP-systeem.  

   Als u de enqueue server 1-architectuur (ENSA1) gebruikt, definieert u de bronnen voor SAP-systemen **NW2** en **NW3** als volgt:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP introduceerde ondersteuning voor enqueue server 2, inclusief replicatie, vanaf SAP NW 7.52. Vanaf ABAP Platform 1809 is enqueue server 2 standaard geïnstalleerd. Zie SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor ondersteuning voor enqueue server 2.
   Als u de enqueue server 2-architectuur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)gebruikt, definieert u de bronnen voor SAP-systemen **NW2** en **NW3** als volgt:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Zie SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)als u een upgrade uitvoert van een oudere versie en overschakelt naar server 2. 

   > [!NOTE]
   > De time-outs in de bovenstaande configuratie zijn slechts voorbeelden en moeten mogelijk worden aangepast aan de specifieke SAP-installatie. 

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.
   In het volgende voorbeeld wordt de status van de clusterresources weergegeven nadat SAP-systemen **NW2** en **NW3** aan het cluster zijn toegevoegd. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Firewallregels toevoegen voor ASCS en ERS op beide knooppunten.  Het onderstaande voorbeeld toont de firewallregels voor zowel SAP-systemen **NW2** als **NW3.**  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Doorgaan met de SAP-installatie 

Voltooi uw SAP-installatie door:

* [Uw SAP NetWeaver-toepassingsservers voorbereiden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Een DBMS-exemplaar installeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Een primaire SAP-toepassingsserver installeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Een of meer extra SAP-toepassingsexemplaren installeren

## <a name="test-the-multi-sid-cluster-setup"></a>De installatie van het multi-SID-cluster testen

De volgende tests zijn een subset van de testcases in de best practices gidsen van Red Hat. Ze zijn inbegrepen voor uw gemak. Raadpleeg voor de volledige lijst met clustertests de volgende documentatie:

* Als u NFS-volumes van Azure NetApp Files gebruikt, volgt u [Azure VM's met hoge beschikbaarheid voor SAP NetWeaver op RHEL met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* Als u `GlusterFS`de hoge beschikbaarheid van [Azure VM's voor SAP NetWeaver op RHEL voor SAP-toepassingen gebruikt.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)  

Lees altijd de handleidingen voor aanbevolen procedures van Red Hat en voer alle aanvullende tests uit die mogelijk zijn toegevoegd.  
De tests die worden gepresenteerd zijn in een twee knooppunt, multi-SID cluster met drie SAP-systemen geïnstalleerd.  

1. Migreer de ASCS-instantie handmatig. In het voorbeeld wordt het ascs-exemplaar voor SAP-systeem NW3 gemigreerd.

   Resourcestatus voordat de test wordt gestart:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Voer de volgende opdrachten uit als hoofdom de INSTANTIE NW3 ASCS te migreren.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Resourcestatus na de test:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Knooppuntcrash simuleren

   Resourcestatus voordat de test wordt gestart:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Voer de volgende opdracht uit als hoofd op een knooppunt, waarbij ten minste één ASCS-instantie wordt uitgevoerd. In dit voorbeeld hebben we `rhelmsscl1`de opdracht uitgevoerd op , waar de ASCS-exemplaren voor NW1, NW2 en NW3 worden uitgevoerd.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   De status na de test, en na het knooppunt, dat werd neergestort is weer begonnen, moet er zo uitzien.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Als er berichten zijn voor mislukte resources, moet u de status van de mislukte resources opschonen. Bijvoorbeeld:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
