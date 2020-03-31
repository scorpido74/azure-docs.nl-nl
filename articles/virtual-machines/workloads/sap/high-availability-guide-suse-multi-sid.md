---
title: Azure VM's hoge beschikbaarheid voor SAP NetWeaver op SLES multi-SID-gids | Microsoft Documenten
description: Multi-SID high-availability guide for SAP NetWeaver on SUSE Linux Enterprise Server for SAP applications Multi-SID high-availability guide for SAP NetWeaver on SUSE Linux Enterprise Server for SAP applications Multi-SID high-availability guide for SAP NetWeaver on SUSE Linux Enterprise Server for SAP applications Multi-
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
ms.openlocfilehash: 793851780e1154b6b6a21c88ea8cae063a277790
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350059"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen multi-SID-handleiding

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

In dit artikel wordt beschreven hoe u meerdere SAP NetWeaver- of S4HANA-systemen (dat wil zeggen multi-SID) implementeert in een cluster met twee clients op Azure VM's met SUSE Linux Enterprise Server voor SAP-toepassingen.  

In de voorbeeldconfiguraties worden installatieopdrachten etc. drie SAP NetWeaver 7.50-systemen geïmplementeerd in één cluster met twee knooppunts hoge beschikbaarheid. De SAP-systemen SIDs zijn:
* **NW1**: ASCS-instantienummer **00** en virtuele hostnaam **msnw1ascs**; ERS-instantienummer **02** en virtuele hostnaam **msnw1ers**.  
* **NW2**: ASCS instance number **10** en virtual hostname **msnw2ascs**; ERS instantie nummer **12** en virtuele host naam **msnw2ers**.  
* **NW3**: ASCS-instantienummer **20** en virtuele hostname **msnw3ascs**; ERS instantie nummer **22** en virtuele host naam **msnw3ers**.  

Het artikel heeft geen betrekking op de databaselaag en de implementatie van de SAP NFS-shares. In de voorbeelden in dit artikel gebruiken we virtuele namen nw2-nfs voor de NW2 NFS-shares en nw3-nfs voor de NW3 NFS-shares, ervan uitgaande dat NFS-cluster is geïmplementeerd.  

Raadpleeg eerst de volgende SAP-notities en -documenten voordat u begint:

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
* [SUSE multi-SID clusterguide voor SLES 12 en SLES 15](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp-bestanden][anf-sap-applications-azure]
## <a name="overview"></a>Overzicht

De virtuele machines, die deelnemen aan het cluster moeten worden aangepast om alle resources te kunnen uitvoeren, voor het geval er een fail-over plaatsvindt. Elke SAP SID kan onafhankelijk van elkaar mislukken in het cluster met hoge beschikbaarheid met meerdere SID.Each SAP SID can fail over independent from each other in the multi-SID high availability cluster.  Bij gebruik van SBD-schermen kunnen de SBD-apparaten worden gedeeld tussen meerdere clusters.  

Om een hoge beschikbaarheid te bereiken, heeft SAP NetWeaver zeer beschikbare NFS-aandelen nodig. In dit voorbeeld gaan we ervan uit dat de SAP NFS-aandelen worden gehost op zeer beschikbare [NFS-bestandsserver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), die kan worden gebruikt door meerdere SAP-systemen. Of de aandelen worden geïmplementeerd op [NFS-volumes van Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> De ondersteuning voor multi-SID clustering van SAP ASCS/ERS met SUSE Linux als gastbesturingssysteem in Azure VM's is beperkt tot **vijf** SAP SID's op hetzelfde cluster. Elke nieuwe SID verhoogt de complexiteit. Een mix van SAP Enqueue Replication Server 1 en Enqueue Replication Server 2 op hetzelfde cluster **wordt niet ondersteund.** Multi-SID clustering beschrijft de installatie van meerdere SAP ASCS/ERS-exemplaren met verschillende SidDs in één Pacemaker-cluster. Momenteel wordt multi-SID clustering alleen ondersteund voor ASCS/ERS.  

> [!TIP]
> De multi-SID clustering van SAP ASCS/ERS is een oplossing met een hogere complexiteit. Het is complexer uit te voeren. Het gaat ook om een hogere administratieve inspanning, bij het uitvoeren van onderhoudsactiviteiten (zoals het patchen van het besturingssysteem). Voordat u de daadwerkelijke implementatie start, neemt u de tijd om de implementatie en alle betrokken onderdelen zoals VM's, NFS-bevestigingen, VIP's, load balancer-configuraties enzovoort zorgvuldig te plannen.  

De NFS-server, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA-database gebruiken virtuele hostname en virtuele IP-adressen. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. We raden u aan [standaard load balancer te gebruiken.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)  

De volgende lijst toont de configuratie van de (A)SCS en ERS load balancer voor dit multi-SID cluster voorbeeld met drie SAP-systemen. U hebt afzonderlijke frontend IP, health probes en load-balancing rules nodig voor elke ASCS en ERS bijvoorbeeld voor elk van de SID's. Wijs alle VM's toe die deel uitmaken van het ASCS/ASCS-cluster aan één backendpool.  

### <a name="ascs"></a>(A)SCS

* Frontend-configuratie
  * IP-adres voor NW1: 10.3.1.14
  * IP-adres voor NW2: 10.3.1.16
  * IP-adres voor NW3: 10.3.1.13
* Sondepoorten
  * Poort 620<strong>&lt;&gt;nr</strong>, dus voor NW1, NW2 en NW3 sonde poorten 620**00**, 620**10** en 620**20**
* Regels voor het balanceren van de lasten - 
* maak er een voor elke instantie, dat wil zeggen NW1/ASCS, NW2/ASCS en NW3/ASCS.
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
  * IP-adres voor NW1 10.3.1.15
  * IP-adres voor NW2 10.3.1.17
  * IP-adres voor NW3 10.3.1.19
* Sondepoort
  * Poort 621<strong>&lt;&gt;nr</strong>, dus voor NW1, NW2 en N# sondepoorten 621**02**, 621**12** en 621**22**
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

## <a name="sap-nfs-shares"></a>SAP NFS-aandelen

SAP NetWeaver vereist gedeelde opslag voor het transport, de profielmap, enzovoort. Voor zeer beschikbaar SAP-systeem is het belangrijk om zeer beschikbare NFS-aandelen te hebben. U moet beslissen over de architectuur voor uw SAP NFS-aandelen. Een optie is het bouwen [van zeer beschikbare NFS-cluster op Azure VM's op SUSE Linux Enterprise Server][nfs-ha], die kunnen worden gedeeld tussen meerdere SAP-systemen. 

Een andere optie is het implementeren van de aandelen op [NFS-volumes van Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  Met Azure NetApp Files krijgt u een ingebouwde hoge beschikbaarheid voor de SAP NFS-shares.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Het eerste SAP-systeem in het cluster implementeren

Nu u hebt besloten over de architectuur voor de SAP NFS-aandelen, implementeert u het eerste SAP-systeem in het cluster, volgens de bijbehorende documentatie.

* Als u de zeer beschikbare NFS-server gebruikt, volgt u [De hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)  
* Als u NFS-volumes van Azure NetApp Files gebruikt, volgt u [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

De hierboven genoemde documenten begeleiden u door de stappen om de benodigde infrastructuren voor te bereiden, het cluster te bouwen, het besturingssysteem voor te bereiden op het uitvoeren van de SAP-toepassing.  

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
* De cluster fail over functionaliteit is getest.  
* De NFS-aandelen voor alle SAP-systemen worden geïmplementeerd.  

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

1. Voeg configuratie voor het nieuw geïmplementeerde systeem (dat wil zeggen **NW2**, **NW3)** toe aan de bestaande Azure Load Balancer, volgens de instructies [Implementatie Azure Load Balancer handmatig via Azure portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#deploy-azure-load-balancer-manually-via-azure-portal). Pas de IP-adressen, health probe-poorten, load-balancing regels voor uw configuratie aan.  

2. **[A]** Stel naamresolutie in voor de extra SAP-systemen. U dns-server `/etc/hosts` gebruiken of wijzigen op alle knooppunten. In dit voorbeeld ziet `/etc/hosts` u hoe u het bestand gebruikt.  Pas de IP-adressen en de hostnamen aan uw omgeving aan. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
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

4. **[A]** `autofs` Configureren om de /sapmnt/SID- en /usr/sap/SID/SYS-bestandssystemen te monteren voor de extra SAP-systemen die u implementeert in het cluster. In dit voorbeeld **NW2** en **NW3**.  

   Update `/etc/auto.direct` bestand met de bestandssystemen voor de extra SAP-systemen die u implementeert in het cluster.  

   * Als u nfs-bestandsserver gebruikt, volgt u de [instructies hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#prepare-for-sap-netweaver-installation)
   * Als u Azure NetApp-bestanden gebruikt, volgt u de [instructies hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#prepare-for-sap-netweaver-installation) 

   U moet de `autofs` service opnieuw starten om de nieuw toegevoegde aandelen te monteren.  

### <a name="install-ascs--ers"></a>ASCS / ERS installeren

1. Maak de clusterbronnen voor virtuele IP- en statussondes voor het ASCS-exemplaar van het extra SAP-systeem dat u in het cluster implementeert. Het voorbeeld hier wordt weergegeven is voor **NW2** en **NW3** ASCS, met behulp van zeer beschikbare NFS-server.  

   > [!IMPORTANT]
   > Recente tests brachten situaties aan het licht, waarin netcat stopt met reageren op verzoeken vanwege achterstand en de beperking van de afhandeling van slechts één verbinding. De netcat-bron luistert niet meer naar de aanvragen voor Azure Load balancer en het zwevende IP-adres is niet meer beschikbaar.  
   > Voor bestaande Pacemaker clusters, raden we in het verleden ter vervanging van netcat door socat. Momenteel raden we u aan azure-lb resource agent, die deel uitmaakt van pakket resource-agents, met de volgende pakketversie vereisten:
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018.a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resourceagents-4.3.0184.6ee15eb2-4.13.1 zijn.  
   >
   > Houd er rekening mee dat de wijziging korte downtime vereist.  
   > Als de configuratie al is gewijzigd om socat te gebruiken zoals beschreven in [Azure Load-Balancer Detection Hardening,](https://www.suse.com/support/kb/doc/?id=7024128)is er geen verplichting om onmiddellijk over te schakelen naar azure-lb resource agent.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Als u de resources maakt, kunnen ze aan verschillende clusterbronnen worden toegewezen. Wanneer u ze groepeert, migreren ze naar een van de clusterknooppunten. Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

2. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ASCS. Voor systeem **NW2**is de virtuele hostnaam bijvoorbeeld <b>msnw2ascs</b>, <b>10.3.1.16</b> en het instantienummer dat u voor de sonde van de load balancer hebt gebruikt, bijvoorbeeld <b>10</b>. voor systeem **NW3**, de virtuele hostname is <b>msnw3ascs</b>, <b>10.3.1.13</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>20</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst. U parameterSAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van virtuele host.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Als de installatie geen submap maakt in /usr/sap/**SID**/ASCS**Instance#,** probeert u de eigenaar in te stellen op **sid**adm en groep op sapsys van de ASCS**Instance#** en opnieuw te proberen.

3. **[1]** Maak een virtueel IP- en health-probe clusterresources voor het ERS-exemplaar van het extra SAP-systeem dat u implementeert in het cluster. Het voorbeeld hier wordt weergegeven is voor **NW2** en **NW3** ERS, met behulp van zeer beschikbare NFS-server. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Als u de resources maakt, kunnen ze worden toegewezen aan verschillende clusterknooppunten. Wanneer u ze groepeert, migreren ze naar een van de clusterknooppunten. Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart.  

   Zorg er vervolgens voor dat de resources van de nieuw gemaakte ERS-groep worden uitgevoerd op het clusterknooppunt, tegenover het clusterknooppunt waar het ASCS-exemplaar voor hetzelfde SAP-systeem is geïnstalleerd.  Als NW2 ASCS bijvoorbeeld is `slesmsscl1`geïnstalleerd op , zorg er dan `slesmsscl2`voor dat de NW2 ERS-groep wordt uitgevoerd op .  U de NW2 `slesmsscl2` ERS-groep migreren naar door de volgende opdracht uit te voeren: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** SAP NetWeaver ERS installeren

   Installeer SAP NetWeaver ERS als root op het andere knooppunt, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ERS. Voor systeem **NW2**zijn de naam van de virtuele host bijvoorbeeld <b>msnw2ers</b>, <b>10.3.1.17</b> en het instantienummer dat u voor de sonde van de load balancer hebt gebruikt, bijvoorbeeld <b>12</b>. Voor systeem **NW3**, de virtuele hostnaam <b>msnw3ers</b>, <b>10.3.1.19</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>22</b>. 

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst. U parameterSAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van virtuele host.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 of hoger gebruiken. Lagere versies stellen de machtigingen niet correct in en de installatie zal mislukken.

   Als de installatie geen submap maakt in /usr/sap/**NW2**/ERS**Instance#,** probeert u de eigenaar in te stellen op **sid**adm en de groep op sapsys van de MAP ERS**Instance#** en opnieuw te proberen.

   Als u de ERS-groep van het nieuw geïmplementeerde SAP-systeem moet migreren naar een ander clusterknooppunt, vergeet dan niet de locatiebeperking voor de ERS-groep te verwijderen. U de beperking verwijderen door de volgende opdracht uit te voeren (het voorbeeld wordt gegeven voor SAP-systemen **NW2** en **NW3).**  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** De ASCS/SCS- en ERS-instantieprofielen aanpassen voor het nieuw geïnstalleerde SAP-systeem(en). Het onderstaande voorbeeld is voor NW2. U moet de ASCS/SCS- en ERS-profielen aanpassen voor alle SAP-exemplaren die aan het cluster zijn toegevoegd.  
 
 * ASCS/SCS-profiel

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

 * ERS-profiel

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** Configureer de SAP-gebruikers voor het nieuw geïmplementeerde SAP-systeem, in dit voorbeeld **NW2** en **NW3.** 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Voeg de ASCS- en ERS SAP-services `sapservice` voor het nieuw geïnstalleerde SAP-systeem toe aan het bestand. Het voorbeeld hieronder is voor SAP-systemen **NW2** en **NW3**.  

   Voeg de ASCS-servicevermelding toe aan het tweede knooppunt en kopieer de toegang van de ERS-service naar het eerste knooppunt. Voer de opdrachten uit voor elk SAP-systeem op het knooppunt, waarbij de ASCS-instantie voor het SAP-systeem is geïnstalleerd.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** Maak de SAP-clusterbronnen voor het nieuw geïnstalleerde SAP-systeem. 

   Als u de enqueue server 1-architectuur (ENSA1) gebruikt, definieert u de bronnen voor SAP-systemen **NW2** en **NW3** als volgt:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   SAP introduceerde ondersteuning voor enqueue server 2, inclusief replicatie, vanaf SAP NW 7.52. Vanaf ABAP Platform 1809 is enqueue server 2 standaard geïnstalleerd. Zie SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor ondersteuning voor enqueue server 2.
   Als u de enqueue server 2-architectuur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)gebruikt, definieert u de bronnen voor SAP-systemen **NW2** en **NW3** als volgt:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Zie SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)als u een upgrade uitvoert van een oudere versie en overschakelt naar server 2. 

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.
   In het volgende voorbeeld wordt de status van de clusterresources weergegeven nadat SAP-systemen **NW2** en **NW3** aan het cluster zijn toegevoegd. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   De volgende foto laat zien hoe de middelen eruit zouden zien in de HA Web Konsole (Hawk), met de middelen voor SAP-systeem **NW2** uitgebreid.  

   [![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Doorgaan met de SAP-installatie 

Voltooi uw SAP-installatie door:

* [Uw SAP NetWeaver-toepassingsservers voorbereiden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Een DBMS-exemplaar installeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#install-database)
* [Een primaire SAP-toepassingsserver installeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#sap-netweaver-application-server-installation)
* Een of meer extra SAP-toepassingsexemplaren installeren

## <a name="test-the-multi-sid-cluster-setup"></a>De installatie van het multi-SID-cluster testen

De volgende tests zijn een subset van de testcases in de best practices gidsen van SUSE. Ze zijn inbegrepen voor uw gemak. Raadpleeg voor de volledige lijst met clustertests de volgende documentatie:

* Als u de zeer beschikbare NFS-server gebruikt, volgt u [De hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)  
* Als u NFS-volumes van Azure NetApp Files gebruikt, volgt u [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

Lees altijd de handleidingen voor aanbevolen procedures van SUSE en voer alle aanvullende tests uit die mogelijk zijn toegevoegd.  
De tests die worden gepresenteerd zijn in een twee knooppunt, multi-SID cluster met drie SAP-systemen geïnstalleerd.  

1. Test HAGetFailoverConfig en HACheckFailoverConfig

   Voer de volgende <sapsid>opdrachten uit als adm op het knooppunt waar momenteel de ASCS-instantie wordt uitgevoerd. Als de opdrachten mislukken met FAIL: Onvoldoende geheugen, kan dit worden veroorzaakt door streepjes in uw hostnaam. Dit is een bekend probleem en zal door SUSE worden opgelost in het sap-suse-cluster-connector pakket.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Migreer de ASCS-instantie handmatig. In het voorbeeld wordt het ascs-exemplaar voor SAP-systeem NW2 gemigreerd.  
   Resourcestatus voordat de test wordt gestart:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Voer de volgende opdrachten uit als hoofdom de INSTANTIE NW2 ASCS te migreren.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Resourcestatus na de test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Test HAFailoverToNode. De hier gepresenteerde test toont het migreren van de ASCS-instantie voor SAP-systeem NW2.  

   Resourcestatus voordat de test wordt gestart:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Voer de volgende opdrachten uit als **nw2**adm om de NW2 ASCS-instantie te migreren.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Resourcestatus na de test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Knooppuntcrash simuleren

   Resourcestatus voordat de test wordt gestart:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Voer de volgende opdracht als hoofd uit op het knooppunt waarin ten minste één ASCS-instantie wordt uitgevoerd. In dit voorbeeld hebben we `slesmsscl2`de opdracht uitgevoerd op , waar de ASCS-exemplaren voor NW1 en NW3 worden uitgevoerd.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Als u SBD gebruikt, mag pacemaker niet automatisch starten op het gedode knooppunt. De status nadat het knooppunt opnieuw is gestart, moet er zo uitzien.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Gebruik de volgende opdrachten om Pacemaker op het gedode knooppunt te starten, de SBD-berichten schoon te maken en de mislukte resources schoon te maken.

   ```# run as root
   # list the SBD device(s)
   slesmsscl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   slesmsscl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   slesmsscl2:~ # systemctl start pacemaker
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW1_ERS02
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW2_ERS12
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Resourcestatus na de test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
