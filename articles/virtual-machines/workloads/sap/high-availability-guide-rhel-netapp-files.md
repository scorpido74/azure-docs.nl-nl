---
title: Azure VM's hoge beschikbaarheid voor SAP NW op RHEL met Azure NetApp Files| Microsoft Documenten
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
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351247"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp-bestanden voor SAP-toepassingen

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

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert, het clusterframework installeert en een zeer beschikbaar SAP NetWeaver 7.50-systeem installeert met [Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)
In het voorbeeld configuraties, installatie commando's etc. ASCS-instantie is nummer 00, de INSTANTIE ERS is nummer 01, primaire toepassingsinstantie (PAS) is 02 en de instantie Toepassing (AAS) is 03. SAP System ID QAS wordt gebruikt. 

De databaselaag wordt in dit artikel niet in detail behandeld.  

Lees eerst de volgende SAP Notes en papers:

* [Azure NetApp-bestandendocumentatie][anf-azure-doc] 
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

Voor hoge beschikbaarheid (HA) voor centrale SAP Netweaver-services is gedeelde opslag vereist.
Om dat te bereiken op Red Hat Linux tot nu toe was het noodzakelijk om aparte zeer beschikbare GlusterFS cluster te bouwen. 

Nu is het mogelijk om SAP Netweaver HA te bereiken met behulp van gedeelde opslag, geïmplementeerd op Azure NetApp Files. Als u Azure NetApp-bestanden gebruikt voor de gedeelde opslag, is er geen extra [GlusterFS-cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)nodig. Pacemaker is nog steeds nodig voor HA van de SAP Netweaver central services (ASCS/SCS).

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA-database gebruiken virtuele hostname en virtuele IP-adressen. Op Azure is een load balancer vereist om een virtueel IP-adres te gebruiken. We raden u aan [standaard load balancer te gebruiken.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal) De volgende lijst toont de configuratie van de load balancer met aparte front-end IP's voor (A)SCS en ERS.

### <a name="ascs"></a>(A)SCS

* Frontend-configuratie
  * IP-adres 192.168.14.9
* Sondepoort
  * Poort 620<strong>&lt;nr.&gt;</strong>
* Regels voor taakverdeling
  * Als u Standard Load Balancer gebruikt, selecteert u **HA-poorten**
  * 32<strong>&lt;&gt; nr.</strong>
  * 36<strong>&lt;&gt; nr.</strong>
  * 39<strong>&lt;&gt; nr.</strong>
  * 81<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Frontend-configuratie
  * IP-adres 192.168.14.10
* Sondepoort
  * Poort 621<strong>&lt;nr.&gt;</strong>
* Regels voor taakverdeling
  * Als u Standard Load Balancer gebruikt, selecteert u **HA-poorten**
  * 32<strong>&lt;&gt; nr.</strong>
  * 33<strong>&lt;&gt; nr.</strong>
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

* Backend-configuratie
  * Verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van het (A)SCS/ERS-cluster

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>De Azure NetApp Files-infrastructuur instellen 

SAP NetWeaver vereist gedeelde opslag voor de transport- en profielmap.  Voordat u verdergaat met de installatie voor Azure NetApp-bestandeninfrastructuur, moet u vertrouwd raken met de [documentatie van Azure NetApp-bestanden.][anf-azure-doc] Controleer of uw geselecteerde Azure-regio Azure NetApp-bestanden biedt. In de volgende koppeling wordt de beschikbaarheid van Azure NetApp-bestanden per Azure-regio weergegeven: [azure NetApp-bestanden beschikbaarheid per Azure-regio][anf-avail-matrix].

Azure NetApp-bestanden zijn beschikbaar in verschillende [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Voordat u Azure NetApp-bestanden implementeert, moet u onboarding aanvragen bij Azure NetApp-bestanden, volgens de [instructies voor Register for Azure NetApp-bestanden.][anf-register] 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp-bestandenresources implementeren  

De stappen gaan ervan uit dat u [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)al hebt geïmplementeerd. De bronnen Azure NetApp Files en de VM's, waar de Azure NetApp-bestandenbronnen worden gemonteerd, moeten worden geïmplementeerd in hetzelfde Azure Virtual Network of in peered Azure Virtual Networks.  

1. Als u dat nog niet hebt gedaan, vraagt u [onboarding aan bij Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Maak het NetApp-account in het geselecteerde Azure-gebied volgens de [instructies om NetApp-account te maken.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. De capaciteitsgroep azure NetApp-bestanden instellen, volgens de instructies voor het instellen van de capaciteitsgroep van [Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)  
De SAP Netweaver-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp-capaciteitsgroep, Premium SKU. We raden Azure NetApp Files Premium SKU aan voor SAP Netweaver-toepassingswerkbelasting op Azure.  
4. Delegeren van een subnet aan Azure NetApp-bestanden zoals beschreven in de [instructies Een subnet delegeren aan Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Azure NetApp-bestandenvolumes implementeren, volgens de [instructies om een volume voor Azure NetApp-bestanden te maken.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) Implementeer de volumes in het aangewezen [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp Files . Houd er rekening mee dat de azure NetApp-bestanden en de Azure VM's zich in hetzelfde Azure Virtual Network of in gepeerde Azure Virtual Networks moeten bevinden. In dit voorbeeld gebruiken we twee Azure NetApp Files-volumes: sap<b>QAS</b> en transSAP. De bestandspaden die zijn gemonteerd op de bijbehorende mount punten zijn / usrsap<b>qas</b>/ sapmnt<b>QAS</b>, / usrsap<b>qas</b>/ usrsap<b>QAS</b>sys, enz.  

   1. volumesap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volumesap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volumesap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volumesap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volumetransSAP (nfs://192.168.24.4/transSAP)
   6. volumesap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
In dit voorbeeld hebben we Azure NetApp-bestanden gebruikt voor alle SAP Netweaver-bestandssystemen om aan te tonen hoe Azure NetApp-bestanden kunnen worden gebruikt. De SAP-bestandssystemen die niet via NFS hoeven te worden gemonteerd, kunnen ook worden geïmplementeerd als [Azure-schijfopslag.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) In dit voorbeeld moet <b>a-e</b> op Azure NetApp-bestanden staan en <b>f-g</b> (dat wil zeggen/ /usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03)</b>kan worden geïmplementeerd als Azure-schijfopslag. 

### <a name="important-considerations"></a>Belangrijke overwegingen

Wanneer u Azure NetApp-bestanden voor de SAP Netweaver op de SUSE-architectuur voor hoge beschikbaarheid overweegt, moet u rekening houden met de volgende belangrijke overwegingen:

- De minimale capaciteit pool is 4 TiB. De grootte van de capaciteitspool kan in stappen van 1 TiB worden verhoogd.
- Het minimumvolume is 100 GiB
- Azure NetApp-bestanden en alle virtuele machines, waar Azure NetApp-bestandenvolumes worden gemonteerd, moeten zich in hetzelfde Azure Virtual Network bevinden of in [peered virtuele netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) in dezelfde regio. Azure NetApp-bestanden toegang via VNET peering in dezelfde regio wordt nu ondersteund. Azure NetApp-toegang via wereldwijde peering wordt nog niet ondersteund.
- Het geselecteerde virtuele netwerk moet een subnet hebben dat is gedelegeerd aan Azure NetApp-bestanden.
- Azure NetApp Files biedt [exportbeleid:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)u de toegestane clients, het toegangstype (Lees&schrijven, alleen lezen, enz.) beheren. 
- De functie Azure NetApp-bestanden is nog niet zonebewust. Momenteel wordt de functie Azure NetApp-bestanden niet geïmplementeerd in alle beschikbaarheidszones in een Azure-gebied. Wees je bewust van de mogelijke latentie-implicaties in sommige Azure-regio's. 
- Azure NetApp-bestandenvolumes kunnen worden geïmplementeerd als NFSv3- of NFSv4.1-volumes. Beide protocollen worden ondersteund voor de SAP-toepassingslaag (ASCS/ERS, SAP-toepassingsservers). 

## <a name="setting-up-ascs"></a>Opzetten (A)SCS

In dit voorbeeld zijn de resources handmatig geïmplementeerd via de [Azure-portal.](https://portal.azure.com/#home)

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux handmatig implementeren via Azure-portal

Eerst moet u de Azure NetApp Files-volumes maken. Implementeer de VM's. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de backend pool.

1. Maak load balancer (intern, standaard):  
   1. De ip-adressen aan de frontend maken
      1. IP-adres 192.168.14.9 voor de ASCS
         1. Open de load balancer, selecteer ip-pool aan frontend en klik op Toevoegen
         1. Voer de naam in van de nieuwe ip-pool aan de voorzijde (bijvoorbeeld **frontend. Qas. ASCS**)
         1. De toewijzing instellen op Statisch en voer het IP-adres in (bijvoorbeeld **192.168.14.9**)
         1. Klik op OK
      1. IP-adres 192.168.14.10 voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder "a" om een IP-adres voor de ERS te maken (bijvoorbeeld **192.168.14.10** en **frontend. Qas. ERS**)
   1. Back-endpool maken
      1. Open de load balancer, selecteer backendpools en klik op Toevoegen
      1. Voer de naam in van de nieuwe backendpool (bijvoorbeeld **backend. QAS**)
      1. Klik op Een virtuele machine toevoegen.
      1. Virtuele machine selecteren. 
      1. Selecteer de virtuele machines van het (A)SCS-cluster en hun IP-adressen.
      1. Klik op Add.
   1. De statussondes maken
      1. Poort 620**00** voor ASCS
         1. Open de load balancer, selecteer health probes en klik op Toevoegen
         1. Voer de naam in van de nieuwe statussonde (bijvoorbeeld **gezondheid. Qas. ASCS**)
         1. Selecteer TCP als protocol, poort 620**00,** houd Interval 5 en Niet-gezonde drempel 2
         1. Klik op OK
      1. Poort 621**01** voor ASCS ERS
            * Herhaal de bovenstaande stappen onder "c" om een gezondheidssonde voor de ERS te maken (bijvoorbeeld 621**01** en **gezondheid. Qas. ERS**)
   1. Regels voor taakverdeling
      1. Regels voor taakverdeling voor ASCS
         1. Open de load balancer, selecteer Load-balancing rules en klik op Toevoegen
         1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld **lb. Qas. ASCS**)
         1. Selecteer het IP-adres voor frontend voor ASCS, backendpool en statussonde die u eerder hebt gemaakt (bijvoorbeeld **frontend. Qas. ASCS**, **backend. QAS** en **gezondheid. Qas. ASCS**)
         1. **HA-poorten selecteren**
         1. Verhoog de idle time-out tot 30 minuten
         1. **Zorg ervoor dat zwevend IP-adres wordt ingeschakeld**
         1. Klik op OK
         * Herhaal de bovenstaande stappen om regels voor taakverdeling voor ERS te maken (bijvoorbeeld **lb. Qas. ERS**)
1. Als uw scenario basisload balancer (intern) vereist, voert u ook de volgende stappen uit:  
   1. De ip-adressen aan de frontend maken
      1. IP-adres 192.168.14.9 voor de ASCS
         1. Open de load balancer, selecteer ip-pool aan frontend en klik op Toevoegen
         1. Voer de naam in van de nieuwe ip-pool aan de voorzijde (bijvoorbeeld **frontend. Qas. ASCS**)
         1. De toewijzing instellen op Statisch en voer het IP-adres in (bijvoorbeeld **192.168.14.9**)
         1. Klik op OK
      1. IP-adres 192.168.14.10 voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder "a" om een IP-adres voor de ERS te maken (bijvoorbeeld **192.168.14.10** en **frontend. Qas. ERS**)
   1. Back-endpool maken
      1. Open de load balancer, selecteer backendpools en klik op Toevoegen
      1. Voer de naam in van de nieuwe backendpool (bijvoorbeeld **backend. QAS**)
      1. Klik op Een virtuele machine toevoegen.
      1. Selecteer de beschikbaarheidsset die u eerder hebt gemaakt voor ASCS 
      1. De virtuele machines van het (A)SCS-cluster selecteren
      1. Klik op OK
   1. De statussondes maken
      1. Poort 620**00** voor ASCS
         1. Open de load balancer, selecteer health probes en klik op Toevoegen
         1. Voer de naam in van de nieuwe statussonde (bijvoorbeeld **gezondheid. Qas. ASCS**)
         1. Selecteer TCP als protocol, poort 620**00,** houd Interval 5 en Niet-gezonde drempel 2
         1. Klik op OK
      1. Poort 621**01** voor ASCS ERS
            * Herhaal de bovenstaande stappen onder "c" om een gezondheidssonde voor de ERS te maken (bijvoorbeeld 621**01** en **gezondheid. Qas. ERS**)
   1. Regels voor taakverdeling
      1. 32**00** TCP voor ASCS
         1. Open de load balancer, selecteer Load-balancing rules en klik op Toevoegen
         1. Voer de naam in van de nieuwe regel voor load balancer (bijvoorbeeld **lb. Qas. ASCS.3200**)
         1. Selecteer het IP-adres voor frontend voor ASCS, backendpool en statussonde die u eerder hebt gemaakt (bijvoorbeeld **frontend. Qas. ASCS**)
         1. Protocol **TCP**bijhouden, poort **3200** invoeren
         1. Verhoog de idle time-out tot 30 minuten
         1. **Zorg ervoor dat zwevend IP-adres wordt ingeschakeld**
         1. Klik op OK
      1. Extra poorten voor de ASCS
         * Herhaal de bovenstaande stappen onder "d" voor poorten 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 en TCP voor de ASCS
      1. Extra poorten voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder "d" voor poorten 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 en TCP voor de ASCS ERS

      > [!Note]
      > Wanneer VM's zonder openbare IP-adressen in de backendpool van interne (geen openbare IP-adres) Standaard Azure-loadbalancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij extra configuratie wordt uitgevoerd om routering naar openbare eindpunten mogelijk te maken. Zie [Openbare eindpuntconnectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)met hoge beschikbaarheid voor meer informatie over het bereiken van uitgaande connectiviteit.  

      > [!IMPORTANT]
      > Schakel geen TCP-tijdstempels in op Azure VM's die achter Azure Load Balancer zijn geplaatst. Als u TCP-tijdstempels inschakelt, worden de statussprobes mislukt. Stel parameter **net.ipv4.tcp_timestamps** in op **0**. Zie [Health Probes van Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)meer informatie .

## <a name="disable-id-mapping-if-using-nfsv41"></a>ID-toewijzing uitschakelen (als u NFSv4.1 gebruikt)

De instructies in deze sectie zijn alleen van toepassing als u Azure NetApp Files-volumes gebruikt met het NFSv4.1-protocol. Voer de configuratie uit op alle VM's, waarbij NFSv4.1-volumes van Azure NetApp Files worden gemonteerd.  

1. Controleer de NFS-domeininstelling. Zorg ervoor dat het domein is geconfigureerd als het standaard Azure **`defaultv4iddomain.com`** NetApp Files-domein, d.w.z. en dat de toewijzing op **niemand**is ingesteld.  

    > [!IMPORTANT]
    > Zorg ervoor dat het NFS-domein in de VM wordt ingesteld `/etc/idmapd.conf` op **`defaultv4iddomain.com`** de standaarddomeinconfiguratie op Azure NetApp-bestanden: . Als er een mismatch is tussen de domeinconfiguratie op de NFS-client (d.w.z. de VM) en de NFS-server, d.w.z. de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die op de VM's zijn gemonteerd, weergegeven als `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Verifiëren . Het moet worden ingesteld op **Y**. Als u de `nfs4_disable_idmapping` mapstructuur wilt maken waar zich zich bevindt, voert u de opdracht monteren uit. U de map niet handmatig maken onder /sys/modules, omdat de toegang is gereserveerd voor de kernel/drivers.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Zie voor meer informatie `nfs4_disable_idmapping` over https://access.redhat.com/solutions/1749883het wijzigen van de parameter .

### <a name="create-pacemaker-cluster"></a>Pacemakercluster maken

Volg de stappen in [Het instellen van Pacemaker op Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) om een basispacemakercluster voor deze (A)SCS-server te maken.

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op alle knooppunten, **[1]** - alleen van toepassing op knooppunt 1 of **[2]** - alleen van toepassing op knooppunt 2.

1. **[A]** Oplossing hostnaam instellen

   U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Het IP-adres en de hostnaam vervangen in de volgende opdrachten

    ```
    sudo vi /etc/hosts
    ```

   Voeg de volgende regels in op /etc/hosts. De IP-adres en hostnaam wijzigen in uw omgeving

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Sap-mappen maken in het Azure NetApp-bestandenvolume.  
   Monteer tijdelijk het Azure NetApp Files-volume op een van de VM's en maak de SAP-mappen (bestandspaden).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** Maak de gedeelde mappen

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** NFS-client en andere vereisten installeren

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Controleer versie van resource-agents-sap

   Zorg ervoor dat de versie van het geïnstalleerde resource-agents-sap-pakket ten minste 3.9.5-124.el7 is
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Mount-items toevoegen

   Als u NFSv3 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Als u NFSv4.1 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Zorg ervoor dat u de NFS-protocolversie van de Azure NetApp-bestandenvolumes matcht bij het monteren van de volumes. Als de Azure NetApp-bestanden worden gemaakt als NFSv3-volumes, gebruikt u de bijbehorende NFSv3-configuratie. Als de Azure NetApp-bestanden worden gemaakt als NFSv4.1-volumes, volgt u de instructies om id-toewijzing uit te schakelen en zorg ervoor dat u de bijbehorende NFSv4.1-configuratie gebruikt. In dit voorbeeld zijn de Azure NetApp Files-volumes gemaakt als NFSv3-volumes.  

   Monteer de nieuwe aandelen

   ```
   sudo mount -a  
   ```

1. **[A]** SWAP-bestand configureren

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Start de agent opnieuw om de wijziging te activeren

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL-configuratie

   RHEL configureren zoals beschreven in SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS installeren

1. **[1]** Een virtuele IP-bron en statussonde maken voor de ASCS-instantie

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root op het eerste knooppunt met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ASCS, bijvoorbeeld <b>anftstsapvh</b>, <b>192.168.14.9</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>00</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Als de installatie geen submap maakt in /usr/sap/**QAS**/ASCS**00,** probeert u de eigenaar en groep van de ASCS**00-map** in en probeert u het opnieuw.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Maak een virtuele IP-bron en health-probe voor de ERS-instantie

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** SAP NetWeaver ERS installeren  

   Installeer SAP NetWeaver ERS als root op het tweede knooppunt met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de ERS, bijvoorbeeld <b>anftstsapers</b>, <b>192.168.14.10</b> en het instantienummer dat u hebt gebruikt voor de sonde van de load balancer, bijvoorbeeld <b>01</b>.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Als de installatie geen submap maakt in /usr/sap/**QAS**/ERS**01,** probeert u de eigenaar en groep van de MAP ERS**01** in en probeert u het opnieuw.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** De ASCS/SCS- en ERS-instantieprofielen aanpassen

   * ASCS/SCS-profiel

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS-profiel

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Keep Alive configureren

   De communicatie tussen de SAP NetWeaver-toepassingsserver en de ASCS/SCS wordt doorgestuurd via een softwareload balancer. De load balancer koppelt inactieve verbindingen na een configureerbare time-out. Om dit te voorkomen, moet u een parameter instellen in het SAP NetWeaver ASCS/SCS-profiel en de Linux-systeeminstellingen wijzigen. Lees [SAP Note 1410736][1410736] voor meer informatie.

   De ASCS/SCS profielparameter enque/encni/set_so_keepalive is al in de laatste stap toegevoegd.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update het /usr/sap/sapservices-bestand

   Om het begin van de exemplaren door het sapinit-opstartscript te voorkomen, moeten alle exemplaren die door Pacemaker worden beheerd, worden becommentarieerd vanuit het bestand /usr/sap/sapservices. Geef geen commentaar op de SAP HANA instantie als het zal worden gebruikt met HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** De SAP-clusterbronnen maken

   Als u de enqueue server 1-architectuur (ENSA1) gebruikt, definieert u de bronnen als volgt:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP introduceerde ondersteuning voor enqueue server 2, inclusief replicatie, vanaf SAP NW 7.52. Vanaf ABAP Platform 1809 is enqueue server 2 standaard geïnstalleerd. Zie SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor ondersteuning voor enqueue server 2.
   Als u de enqueue server 2-architectuur[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)gebruikt, installeert u resourceagent resourceagents-sap-4.1.1-12.el7.x86_64 of nieuwer en definieert u de bronnen als volgt:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Zie SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322)als u een upgrade uitvoert van een oudere versie en overschakelt naar server 2. 

   > [!NOTE]
   > De time-outs in de bovenstaande configuratie zijn slechts voorbeelden en moeten mogelijk worden aangepast aan de specifieke SAP-installatie. 

   Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk op welk knooppunt de middelen draaien.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Firewallregels voor ASCS en ERS toevoegen op beide knooppunten Voeg de firewallregels voor ASCS en ERS op beide knooppunten toe.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver-toepassingsservervoorbereiding

   Sommige databases vereisen dat de installatie van de database-instantie wordt uitgevoerd op een toepassingsserver. Bereid de virtuele machines van de toepassingsserver voor om ze in deze gevallen te kunnen gebruiken.  

   In de stappen wordt ervan uitgegaan dat u de toepassingsserver installeert op een andere server dan de ASCS/SCS- en HANA-servers. Anders zijn sommige van de onderstaande stappen (zoals het configureren van de hostnaamresolutie) niet nodig.  

   De volgende items zijn vooraf vastgesteld met **[A]** - van toepassing op zowel PAS als AAS, **[P]** - alleen van toepassing op PAS of **[S]** - alleen van toepassing op AAS.  

1. **[A]** Status van de host instellen U een DNS-server gebruiken of de /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:  

   ```
   sudo vi /etc/hosts
   ```

   Voeg de volgende regels in op /etc/hosts. Wijzig het IP-adres en de hostnaam op uw omgeving.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Maak de sapmnt-map Maak de sapmnt-map.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS-client en andere vereisten installeren  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Mount-items toevoegen  
   Als u NFSv3 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Als u NFSv4.1 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Monteer de nieuwe aandelen

   ```
   sudo mount -a
   ```

1. **[P]** De PAS-map maken en monteren  
   Als u NFSv3 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Als u NFSv4.1 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** De AAS-map maken en monteren  
   Als u NFSv3 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Als u NFSv4.1 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** SWAP-bestand configureren
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Start de agent opnieuw om de wijziging te activeren

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Database installeren

In dit voorbeeld wordt SAP NetWeaver geïnstalleerd op SAP HANA. U elke ondersteunde database voor deze installatie gebruiken. Zie [Hoge beschikbaarheid van SAP HANA op Azure VM's op Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]voor meer informatie over het installeren van SAP HANA in Azure.

1. De installatie van de SAP-database-instantie uitvoeren

   Installeer de SAP NetWeaver-databaseinstantie als root met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de frontend-configuratie van de load balancer voor de database.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>INSTALLATIE van SAP NetWeaver-toepassingsserver

Volg deze stappen om een SAP-toepassingsserver te installeren.

1. Toepassingsserver voorbereiden

   Volg de stappen in het hoofdstuk [SAP NetWeaver-toepassingsservervoorbereiding](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) hierboven om de toepassingsserver voor te bereiden.

1. SAP NetWeaver-toepassingsserver installeren

   Installeer een primaire of aanvullende SAP NetWeaver-toepassingenserver.

   U de sapinstparameter SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-rootgebruiker verbinding te laten maken met sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Werk de SAP HANA secure store bij om de virtuele naam van de SAP HANA System Replication-installatie aan te wijzen.

   Voer de volgende opdracht uit \<om de vermeldingen als sapsid>adm weer te geven

   ```
   hdbuserstore List
   ```

   Dit moet een lijst van alle items en moet lijken op
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Uit de uitvoer blijkt dat het IP-adres van de standaardvermelding naar de virtuele machine wijst en niet naar het IP-adres van de load balancer. Dit item moet worden gewijzigd om te wijzen op de virtuele hostnaam van de load balancer. Zorg ervoor dat u dezelfde poort **(30313** in de bovenstaande uitvoer) en databasenaam **(QAS** in de bovenstaande uitvoer) gebruikt!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>De clusterinstelling testen

1. De ASCS-instantie handmatig migreren

   Resourcestatus voordat de test wordt gestart:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Voer de volgende opdrachten uit als hoofdom de ASCS-instantie te migreren.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resourcestatus na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Knooppuntcrash simuleren

   Resourcestatus voordat de test wordt gestart:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Voer de volgende opdracht als hoofd uit op het knooppunt waar de ASCS-instantie wordt uitgevoerd

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   De status nadat het knooppunt opnieuw is gestart, moet er zo uitzien.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Gebruik de volgende opdracht om de mislukte resources op te schonen.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resourcestatus na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kill message server proces

   Resourcestatus voordat de test wordt gestart:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Voer de volgende opdrachten uit als root om het proces van de berichtenserver te identificeren en te doden.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Als u de berichtenserver slechts één keer `sapstart`doodt, wordt deze opnieuw gestart door . Als je het vaak genoeg doodt, zal Pacemaker uiteindelijk de ASCS-instantie naar het andere knooppunt verplaatsen. Voer de volgende opdrachten uit als root om de resourcestatus van de instantie ASCS en ERS na de test op te schonen.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resourcestatus na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Serverproces in de wachtrij doden

   Resourcestatus voordat de test wordt gestart:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Voer de volgende opdrachten als hoofd uit op het knooppunt waarin de ASCS-instantie wordt uitgevoerd om de wachtrijserver te doden.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   De ASCS-instantie moet onmiddellijk niet naar het andere knooppunt gaan. De INSTANTIE ERS moet ook mislukken nadat de ASCS-instantie is gestart. Voer de volgende opdrachten uit als root om de resourcestatus van de instantie ASCS en ERS na de test op te schonen.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resourcestatus na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Replicatieserverproces voor wachtrijen doden

   Resourcestatus voordat de test wordt gestart:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Voer de volgende opdracht als hoofd op het knooppunt uit waarin de instantie ERS wordt uitgevoerd om het replicatieserverproces in de wachtrij te doden.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Als u de opdracht `sapstart` slechts één keer uitvoert, wordt het proces opnieuw gestart. Als u het vaak `sapstart` genoeg uitvoert, wordt het proces niet opnieuw opgestart en wordt de resource in een gestopte status weergegeven. Voer de volgende opdrachten uit als root om de resourcestatus van de INSTANTIE ERS na de test op te schonen.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resourcestatus na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kill enqueue sapstartsrv proces

   Resourcestatus voordat de test wordt gestart:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Voer de volgende opdrachten uit als hoofd op het knooppunt waar de ASCS wordt uitgevoerd.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Het sapstartsrv-proces moet altijd opnieuw worden gestart door het pacemaker-bronmiddel als onderdeel van de monitoring. Resourcestatus na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Volgende stappen

* [HA voor SAP NW op Azure VM's op RHEL voor SAP-toepassingen multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [SAP HANA (grote exemplaren) hoge beschikbaarheid en noodherstel op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure.
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha] voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
