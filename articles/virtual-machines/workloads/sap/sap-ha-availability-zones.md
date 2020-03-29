---
title: SAP-workloadconfiguraties met Azure Availability Zones | Microsoft Documenten
description: Architectuur en scenario's met hoge beschikbaarheid voor SAP NetWeaver met Azure Availability Zones
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7a92bef85cd4ee7530940a065135e88c7530781
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675610"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP-werkbelastingconfiguraties met Azure-beschikbaarheidszones
[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) is een van de functies met hoge beschikbaarheid die Azure biedt. Beschikbaarheidszones gebruiken verbetert de algehele beschikbaarheid van SAP-workloads op Azure. Deze functie is al beschikbaar in sommige [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/regions/) In de toekomst zal het in meer regio's beschikbaar zijn.

Deze afbeelding toont de basisarchitectuur van SAP hoge beschikbaarheid:

![Standaard configuratie met hoge beschikbaarheid](./media/sap-ha-availability-zones/standard-ha-config.png)

De SAP-toepassingslaag wordt geïmplementeerd in één [Azure-beschikbaarheidsset.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Voor een hoge beschikbaarheid van SAP Central Services u twee VM's implementeren in een aparte beschikbaarheidsset. Gebruik Windows Server Failover Clustering of Pacemaker (Linux) als een framework met hoge beschikbaarheid met automatische failover in geval van een infrastructuur- of softwareprobleem. Zie voor meer informatie over deze implementaties:

- [Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van een gedeelde clusterschijf](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met bestandsshare](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Een vergelijkbare architectuur is van toepassing op de DBMS-laag van SAP NetWeaver-, S/4HANA- of Hybris-systemen. U implementeert de DBMS-laag in een actieve/passieve modus met een failoverclusteroplossing om te beschermen tegen infrastructuur- of softwarefouten. De failoverclusteroplossing kan een DBMS-specifiek failoverframework, Windows Server Failover Clustering of Pacemaker zijn.

Als u dezelfde architectuur wilt implementeren met Azure Availability Zones, moet u enkele wijzigingen aanbrengen in de eerder beschreven architectuur. In dit artikel worden deze wijzigingen beschreven.

## <a name="considerations-for-deploying-across-availability-zones"></a>Overwegingen voor het implementeren van verschillende beschikbaarheidszones


Houd rekening met het volgende wanneer u beschikbaarheidszones gebruikt:

- Er zijn geen garanties met betrekking tot de afstanden tussen verschillende beschikbaarheidszones binnen een Azure-gebied.
- Beschikbaarheidszones zijn geen ideale DR-oplossing. Natuurrampen kunnen in de wereld regio's op grote schaal schade toebrengen, waaronder zware schade aan energie-infrastructuur. De afstanden tussen de verschillende zones zijn mogelijk niet groot genoeg om een goede DR-oplossing te vormen.
- De netwerklatentie voor beschikbaarheidszones is niet in alle Azure-regio's hetzelfde. In sommige gevallen u de SAP-toepassingslaag in verschillende zones implementeren en uitvoeren omdat de netwerklatentie van één zone naar de actieve DBMS VM aanvaardbaar is. Maar in sommige Azure-regio's is de latentie tussen de actieve DBMS VM en de SAP-toepassingsinstantie, wanneer deze in verschillende zones worden geïmplementeerd, mogelijk niet acceptabel voor SAP-bedrijfsprocessen. In deze gevallen moet de implementatiearchitectuur anders zijn, met een actieve/actieve architectuur voor de toepassing of een actieve/passieve architectuur waarbij de latentie van het netwerk tussen de zones te hoog is.
- Wanneer u beslist waar beschikbaarheidszones worden gebruikt, baseert u uw beslissing op de netwerklatentie tussen de zones. Netwerklatentie speelt een belangrijke rol op twee gebieden:
    - Latentie tussen de twee DBMS-exemplaren die synchrone replicatie moeten hebben. Hoe hoger de netwerklatentie, hoe groter de kans dat dit van invloed is op de schaalbaarheid van uw werkbelasting.
    - Het verschil in netwerklatentie tussen een VM die een SAP-dialoogvenster in de zone uitvoert met de actieve DBMS-instantie en een vergelijkbare VM in een andere zone. Naarmate dit verschil toeneemt, neemt ook de invloed op de looptijd van bedrijfsprocessen en batchtaken toe, afhankelijk van of ze in de zone met de DBMS of in een andere zone worden uitgevoerd.

Wanneer u Azure VM's implementeert in beschikbaarheidszones en failoveroplossingen binnen dezelfde Azure-regio opstelt, gelden enkele beperkingen:

- U moet [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) gebruiken wanneer u implementeert in Azure Availability Zones. 
- Het toewijzen van zone-opsommingen aan de fysieke zones is vastgesteld op basis van een Azure-abonnement. Als u verschillende abonnementen gebruikt om uw SAP-systemen te implementeren, moet u de ideale zones voor elk abonnement definiëren.
- U Azure-beschikbaarheidssets niet implementeren binnen een Azure Availability Zone, tenzij u [Azure Proximity Placement Group gebruikt.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) De manier waarop u de SAP DBMS-laag en de centrale services in verschillende zones implementeren en tegelijkertijd de SAP-toepassingslaag implementeren met beschikbaarheidssets en toch de nabijheid van de VM's bereiken, is gedocumenteerd in het artikel [Azure Proximity Placement Groups voor optimale netwerklatentie met SAP-toepassingen.](sap-proximity-placement-scenarios.md) Als u geen gebruik maakt van Azure-plaatsingsgroepen voor nabijheid, moet u het ene of het andere kiezen als implementatiekader voor virtuele machines.
- U een [Azure Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) niet gebruiken om failoverclusteroplossingen te maken op basis van Windows Server Failover Clustering of Linux Pacemaker. In plaats daarvan moet u de [SKU voor Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)gebruiken.



## <a name="the-ideal-availability-zones-combination"></a>De ideale combinatie van beschikbaarheidszones
Voordat u beslist hoe u beschikbaarheidszones gebruikt, moet u bepalen:

- De netwerklatentie tussen de drie zones van een Azure-gebied. Hiermee u de zones kiezen met de minste netwerklatentie in netwerkverkeer tussen verschillende zones.
- Het verschil tussen VM-naar-VM latentie binnen een van de zones, naar keuze, en de netwerklatentie in twee zones van uw keuze.
- Een bepaling of de VM-typen die u moet implementeren, beschikbaar zijn in de twee zones die u hebt geselecteerd. Bij sommige VM's, met name VM's uit de M-serie, u situaties tegenkomen waarin sommige SKU's beschikbaar zijn in slechts twee van de drie zones.

## <a name="network-latency-between-and-within-zones"></a>Netwerklatentie tussen en binnen zones
Als u de latentie tussen de verschillende zones wilt bepalen, moet u het:

- Implementeer de VM SKU die u wilt gebruiken voor uw DBMS-exemplaar in alle drie de zones. Controleer of [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld wanneer u deze meting uitvoert.
- Wanneer u de twee zones met de minste netwerklatentie vindt, implementeert u nog eens drie VM's van de VM SKU die u als toepassingslaagVM wilt gebruiken in de drie beschikbaarheidszones. Meet de netwerklatentie ten opzichte van de twee DBMS VM's in de twee DBMS-zones die u hebt geselecteerd. 
- Gebruik **niping** als meetinstrument. Deze tool, van SAP, wordt beschreven in SAP support notes [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Focus op de opdrachten die zijn gedocumenteerd voor latentiemetingen. Omdat **ping** niet werkt via de Azure Accelerated Networking-codepaden, raden we u niet aan deze te gebruiken.

U hoeft deze tests niet handmatig uit te voeren. U een PowerShell-procedure [Availability Zone Latency Test](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) vinden die de beschreven latentietests automatiseert. 

Op basis van uw metingen en de beschikbaarheid van uw VM SKU's in de beschikbaarheidszones moet u een aantal beslissingen nemen:

- Definieer de ideale zones voor de DBMS-laag.
- Bepaal of u uw actieve SAP-toepassingslaag wilt distribueren over één, twee of alle drie de zones, op basis van verschillen in netwerklatentie in de zone versus tussen zones.
- Bepaal of u een actieve/passieve configuratie of een actieve/actieve configuratie wilt implementeren vanuit toepassingsoogpunt. (Deze configuraties worden later in dit artikel uitgelegd.)

Houd bij het nemen van deze beslissingen ook rekening met de netwerklatentieaanbevelingen van SAP, zoals gedocumenteerd in SAP-nota [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> De metingen en beslissingen die u neemt, zijn geldig voor het Azure-abonnement dat u hebt gebruikt toen u de metingen hebt uitgevoerd. Als u een ander Azure-abonnement gebruikt, moet u de metingen herhalen. Het toewijzen van opgesomde zones kan verschillen voor een ander Azure-abonnement.


> [!IMPORTANT]
> De verwachting is dat de eerder beschreven metingen verschillende resultaten opleveren in elke Azure-regio die [beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview)ondersteunt. Zelfs als de vereisten voor netwerklatentie hetzelfde zijn, moet u mogelijk verschillende implementatiestrategieën in verschillende Azure-regio's toepassen, omdat de netwerklatentie tussen zones kan verschillen. In sommige Azure-regio's kan de netwerklatentie tussen de drie verschillende zones sterk verschillen. In andere regio's kan de netwerklatentie tussen de drie verschillende zones uniformer zijn. De bewering dat er altijd een netwerklatentie is tussen 1 en 2 milliseconden is niet correct. De netwerklatentie voor beschikbaarheidszones in Azure-regio's kan niet worden gegeneraliseerd.

## <a name="activeactive-deployment"></a>Actieve/actieve implementatie
Deze implementatiearchitectuur wordt actief/actief genoemd omdat u uw actieve SAP-toepassingsservers in twee of drie zones implementeert. De instantie SAP Central Services die replicatie in wachtrijen gebruikt, wordt tussen twee zones geïmplementeerd. Hetzelfde geldt voor de DBMS-laag, die wordt geïmplementeerd in dezelfde zones als SAP Central Service.

Wanneer u deze configuratie overweegt, moet u de twee beschikbaarheidszones in uw regio vinden die netwerklatentie tussen verschillende zones bieden die acceptabel is voor uw werkbelasting en uw synchrone DBMS-replicatie. U wilt er ook zeker van zijn dat de delta tussen netwerklatentie binnen de geselecteerde zones en de latentie tussen verschillende zones niet te groot is. Dit komt omdat u geen grote variaties wilt, afhankelijk van of een taak in de zone wordt uitgevoerd met de DBMS-server of tussen zones, in de bedrijfstijden van uw bedrijfsprocessen of batchtaken. Sommige variaties zijn aanvaardbaar, maar geen factoren van verschil.

Een vereenvoudigd schema van een actieve/actieve implementatie in twee zones kan er als volgt uitzien:

![Actieve/actieve zone-implementatie](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Voor deze configuratie gelden de volgende overwegingen:

- U gebruikt [azure proximity placement group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)niet, u behandelt de Azure Availability Zones als fout- en updatedomeinen voor alle VM's omdat beschikbaarheidssets niet kunnen worden geïmplementeerd in Azure Availability Zones.
- Als u zonale implementaties wilt combineren voor de DBMS-laag en centrale services, maar Azure-beschikbaarheidssets wilt gebruiken voor de toepassingslaag, moet u Azure-nabijheidsgroepen gebruiken zoals beschreven in het artikel [Azure Proximity Placement Groups voor optimale netwerklatentie met SAP-toepassingen.](sap-proximity-placement-scenarios.md)
- Voor de load balancers van de failoverclusters van SAP Central Services en de DBMS-laag moet u de [Standaard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)gebruiken. De Basic Load Balancer werkt niet in verschillende zones.
- Het Azure virtuele netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de subnetten, wordt over verschillende zones uitgerekt. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Onbeheerde schijven worden niet ondersteund voor zonale implementaties.
- Azure Premium Storage en [Ultra SSD-opslag](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) bieden geen ondersteuning voor elk type opslagreplicatie in verschillende zones. De toepassing (DBMS of SAP Central Services) moet belangrijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-aandeel (Windows) of een NFS-share (Linux). U moet een technologie gebruiken die deze gedeelde schijven of delen tussen de zones nabootst. Deze technologieën worden ondersteund:
  - Voor Windows is een clusteroplossing die SIOS DataKeeper gebruikt, zoals gedocumenteerd in [cluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een gedeelde clusterschijf in Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
  - Voor SUSE Linux is een NFS-aandeel dat is gebouwd zoals gedocumenteerd in [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    
    Momenteel wordt de oplossing die Microsoft Scale-Out File Server gebruikt, zoals gedocumenteerd in [Azure-infrastructuur voorbereiden voor SAP-hoge beschikbaarheid met behulp van een Windows-failovercluster en bestandsshare voor SAP ASCS/SCS-exemplaren,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)niet in verschillende zones ondersteund.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten voor het geval u een [SUSE Linux Pacemaker-cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) of extra toepassingsexemplaren bouwt.
- Als u de consistentie van de runtime voor kritieke bedrijfsprocessen wilt bereiken, u proberen bepaalde batchtaken en gebruikers naar toepassingsinstanties te sturen die zich in de zone bevinden met de actieve DBMS-instantie met behulp van SAP-batchservergroepen, SAP-aanmeldingsgroepen of RFC-groepen. In het geval van een zonale failover moet u deze groepen echter handmatig verplaatsen naar exemplaren die worden uitgevoerd op VM's die in de zone zijn met de actieve DB VM.  
- U sluimerende dialoogvensters in elk van de zones implementeren. Dit is om een onmiddellijke terugkeer naar de voormalige resourcecapaciteit mogelijk te maken als een zone die wordt gebruikt door een deel van uw toepassingsinstanties buiten gebruik is.

> [!IMPORTANT]
> In dit actieve/actieve scenario worden vanaf 04/01/2020 extra kosten voor bandbreedte aangekondigd door Microsoft. Controleer de details van de [prijsprijzen van het](https://azure.microsoft.com/pricing/details/bandwidth/)document Bandbreedte . De gegevensoverdracht tussen de SAP-toepassingslaag en de SAP DBMS-laag is vrij intensief. Hierdoor kan het actieve/actieve scenario behoorlijk bijdragen aan de kosten. Blijf dit artikel controleren om de exacte kosten te krijgen 


## <a name="activepassive-deployment"></a>Actieve/passieve implementatie
Als u geen acceptabele delta vinden tussen de netwerklatentie binnen één zone en de latentie van netwerkverkeer tussen verschillende zones, u een architectuur implementeren die een actief/passief karakter heeft vanuit het oogpunt van de SAP-toepassingslaag. U definieert een *actieve* zone, de zone waar u de volledige toepassingslaag implementeert en waar u zowel de actieve DBMS als de instantie SAP Central Services probeert uit te voeren. Met een dergelijke configuratie moet u ervoor zorgen dat u geen extreme tijdsvariaties hebt, afhankelijk van of een taak in de zone wordt uitgevoerd met de actieve DBMS-instantie of niet, in zakelijke transacties en batchtaken.

De basislay-out van de architectuur ziet er als volgt uit:

![Actieve/passieve zone-implementatie](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Voor deze configuratie gelden de volgende overwegingen:

- Beschikbaarheidssets kunnen niet worden geïmplementeerd in Azure Availability Zones. Om dat te compenseren, u Azure-nabijheidsplaatsingsgroepen gebruiken zoals gedocumenteerd in het artikel [Azure Proximity Placement Groups voor optimale netwerklatentie met SAP-toepassingen.](sap-proximity-placement-scenarios.md)
- Wanneer u deze architectuur gebruikt, moet u de status nauwlettend volgen en proberen de actieve DBMS- en SAP Central Services-exemplaren in dezelfde zone te houden als de geïmplementeerde toepassingslaag. In het geval van een failover van SAP Central Service of de DBMS-instantie, wilt u ervoor zorgen dat u handmatig falen in de zone met de SAP-toepassingslaag die zo snel mogelijk wordt geïmplementeerd.
- Voor de load balancers van de failoverclusters van SAP Central Services en de DBMS-laag moet u de [Standaard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)gebruiken. De Basic Load Balancer werkt niet in verschillende zones.
- Het Azure virtuele netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de subnetten, wordt over verschillende zones uitgerekt. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Onbeheerde schijven worden niet ondersteund voor zonale implementaties.
- Azure Premium Storage en [Ultra SSD-opslag](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) bieden geen ondersteuning voor elk type opslagreplicatie in verschillende zones. De toepassing (DBMS of SAP Central Services) moet belangrijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-aandeel (Windows) of een NFS-share (Linux). U moet een technologie gebruiken die deze gedeelde schijven of delen tussen de zones nabootst. Deze technologieën worden ondersteund:
    - Voor Windows is een clusteroplossing die SIOS DataKeeper gebruikt, zoals gedocumenteerd in [cluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een gedeelde clusterschijf in Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - Voor SUSE Linux is een NFS-aandeel dat is gebouwd zoals gedocumenteerd in [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    
  Momenteel wordt de oplossing die Microsoft Scale-Out File Server gebruikt, zoals gedocumenteerd in [Azure-infrastructuur voorbereiden voor SAP-hoge beschikbaarheid met behulp van een Windows-failovercluster en bestandsshare voor SAP ASCS/SCS-exemplaren,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)niet in verschillende zones ondersteund.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten voor het geval u een [SUSE Linux Pacemaker-cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) of extra toepassingsexemplaren bouwt.
- U moet slapende VM's implementeren in de passieve zone (vanuit een DMS-oogpunt) zodat u toepassingsbronnen starten in het geval van een zonefout.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kan momenteel geen actieve VM's repliceren naar slapende VM's tussen zones. 
- U moet investeren in automatisering waarmee u, in geval van een zonestoring, de SAP-toepassingslaag automatisch in de tweede zone starten.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Gecombineerde configuratie met hoge beschikbaarheid en noodherstel
Microsoft deelt geen informatie over geografische afstanden tussen de faciliteiten die verschillende Azure Availability Zones in een Azure-regio hosten. Toch gebruiken sommige klanten zones voor een gecombineerde HA- en DR-configuratie die een herstelpuntdoelstelling (RPO) van nul belooft. Dit betekent dat u geen toegezegde databasetransacties mag verliezen, zelfs niet in het geval van herstel na noodgevallen. 

> [!NOTE]
> Wij raden u aan om een dergelijke configuratie alleen in bepaalde omstandigheden te gebruiken. U het bijvoorbeeld gebruiken wanneer gegevens de Azure-regio om beveiligings- of nalevingsredenen niet kunnen verlaten. 

Hier is een voorbeeld van hoe een dergelijke configuratie eruit zou kunnen zien:

![Gecombineerde hoge beschikbaarheid DR in zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Voor deze configuratie gelden de volgende overwegingen:

- U gaat ervan uit dat er een aanzienlijke afstand is tussen de faciliteiten die een beschikbaarheidszone hosten of dat u gedwongen bent om binnen een bepaalde Azure-regio te blijven. Beschikbaarheidssets kunnen niet worden geïmplementeerd in Azure Availability Zones. Om dat te compenseren, u Azure-nabijheidsplaatsingsgroepen gebruiken zoals gedocumenteerd in het artikel [Azure Proximity Placement Groups voor optimale netwerklatentie met SAP-toepassingen.](sap-proximity-placement-scenarios.md)
- Wanneer u deze architectuur gebruikt, moet u de status nauwlettend volgen en proberen de actieve DBMS- en SAP Central Services-exemplaren in dezelfde zone te houden als de geïmplementeerde toepassingslaag. In het geval van een failover van SAP Central Service of de DBMS-instantie, wilt u ervoor zorgen dat u handmatig falen in de zone met de SAP-toepassingslaag die zo snel mogelijk wordt geïmplementeerd.
- U moet productietoepassingsexemplaren vooraf hebben geïnstalleerd in de VM's waarop de actieve QA-toepassingsinstanties worden uitgevoerd.
- In het geval van een zonefout sluit u de qa-toepassingsinstanties af en start u de productie-instanties in plaats daarvan. Houd er rekening mee dat u virtuele namen voor de toepassingsinstanties moet gebruiken om dit te laten werken.
- Voor de load balancers van de failoverclusters van SAP Central Services en de DBMS-laag moet u de [Standaard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)gebruiken. De Basic Load Balancer werkt niet in verschillende zones.
- Het Azure virtuele netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de subnetten, wordt over verschillende zones uitgerekt. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Onbeheerde schijven worden niet ondersteund voor zonale implementaties.
- Azure Premium Storage en [Ultra SSD-opslag](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) bieden geen ondersteuning voor elk type opslagreplicatie in verschillende zones. De toepassing (DBMS of SAP Central Services) moet belangrijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-aandeel (Windows) of een NFS-share (Linux). U moet een technologie gebruiken die deze gedeelde schijven of delen tussen de zones nabootst. Deze technologieën worden ondersteund:
    - Voor Windows is een clusteroplossing die SIOS DataKeeper gebruikt, zoals gedocumenteerd in [cluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een gedeelde clusterschijf in Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - Voor SUSE Linux is een NFS-aandeel dat is gebouwd zoals gedocumenteerd in [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)

  Momenteel wordt de oplossing die Microsoft Scale-Out File Server gebruikt, zoals gedocumenteerd in [Azure-infrastructuur voorbereiden voor SAP-hoge beschikbaarheid met behulp van een Windows-failovercluster en bestandsshare voor SAP ASCS/SCS-exemplaren,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)niet in verschillende zones ondersteund.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten voor het geval u een [SUSE Linux Pacemaker-cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) of extra toepassingsexemplaren bouwt.





## <a name="next-steps"></a>Volgende stappen
Hier volgen enkele volgende stappen voor het implementeren in Azure Availability Zones:

- [Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van een gedeelde clusterschijf in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Azure-infrastructuur voorbereiden op hoge beschikbaarheid van SAP met behulp van een Windows-failovercluster en bestandsshare voor SAP ASCS/SCS-exemplaren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






