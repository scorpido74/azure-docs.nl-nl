---
title: Failover-cluster exemplaren
description: Meer informatie over Failoverclusterinstanties (failover cluster instances) met SQL Server op Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: a40c5512da40ede84251ec16345a3957c391bb71
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965535"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Failover-cluster exemplaren met SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel worden de verschillen in functies beschreven wanneer u werkt met FCI (failover cluster instances) voor SQL Server op Azure Virtual Machines (Vm's). 

## <a name="overview"></a>Overzicht

SQL Server op virtuele machines van Azure maakt gebruik van de WSFC-functionaliteit (Windows Server failover clustering) om lokale hoge Beschik baarheid te bieden via redundantie op het niveau van de server-instantie: een exemplaar van een failovercluster. Een FCI is één exemplaar van SQL Server dat is geïnstalleerd op een WSFC-knoop punt (of gewoon de cluster) en, mogelijk via meerdere subnetten. In het netwerk lijkt een FCI een exemplaar van SQL Server op een enkele computer uit te voeren. Maar de FCI biedt failover van het ene WSFC-knoop punt naar het andere als het huidige knoop punt niet beschikbaar is.

De rest van het artikel richt zich op de verschillen voor failoverclusters wanneer ze worden gebruikt met SQL Server op virtuele Azure-machines. Voor meer informatie over de failover clustering-technologie raadpleegt u: 

- [Windows-cluster technologieën](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Failover-cluster exemplaren SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Failover-cluster exemplaren met SQL Server op Azure Virtual Machines ondersteunen met behulp van een schijfwitness, een Cloud-Witness of een bestandssharewitness voor het cluster quorum.

Zie voor meer informatie [quorum aanbevolen procedures met SQL Server vm's in azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Storage

In traditionele on-premises geclusterde omgevingen gebruikt een Windows-failovercluster een Storage Area Network (SAN) dat door beide knoop punten als de gedeelde opslag toegankelijk is. SQL Server-bestanden worden gehost op de gedeelde opslag, en alleen het actieve knoop punt heeft toegang tot de bestanden in één keer. 

SQL Server op virtuele machines van Azure biedt verschillende opties als een gedeelde opslag oplossing voor een implementatie van SQL Server failoverclusterknooppunten: 

||[Gedeelde Azure-schijven](../../../virtual-machines/windows/disks-shared.md)|[Premium-bestands shares](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Opslagruimten Direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Minimale versie van het besturingssysteem**| Windows Server 2016|Windows Server 2012|Windows Server 2016|
|**Minimale SQL Server versie**|SQL Server 2019|SQL Server 2012|SQL Server 2016|
|**Ondersteunde VM-Beschik baarheid** |Beschikbaarheids sets met proximity-plaatsings groepen |Beschikbaarheids sets en beschikbaarheids zones|Beschikbaarheidssets |
|**Ondersteunt FileStream**|Nee|Nee|Ja |
|**Azure Blob-cache**|Nee|Nee|Ja|

In de rest van deze sectie vindt u een overzicht van de voor delen en beperkingen van elke opslag optie die beschikbaar is voor SQL Server op Azure-Vm's. 

### <a name="azure-shared-disks"></a>Gedeelde Azure-schijven

[Gedeelde Azure-schijven](../../../virtual-machines/windows/disks-shared.md) zijn een functie van [Azure Managed disks](../../../virtual-machines/windows/managed-disks-overview.md). Windows Server failover clustering ondersteunt het gebruik van gedeelde Azure-schijven met een failover-cluster exemplaar. 

**Ondersteund besturings systeem**: Windows Server 2019   
**Ondersteunde SQL-versie**: SQL Server 2019   

**Voor delen**: 
- Dit is handig voor toepassingen die op zoek zijn naar Azure, terwijl hun architectuur met hoge Beschik baarheid en herstel na nood gevallen (HADR) behouden blijft. 
- Kunnen geclusterde toepassingen naar Azure worden gemigreerd als gevolg van SCSI-ondersteuning voor permanente reserve ringen. 
- Ondersteunt gedeelde Azure-Premium-SSD voor alle versies van SQL Server en gedeelde Azure Ultra Disk Storage voor SQL Server 2019. 
- Kan één gedeelde schijf gebruiken of meerdere gedeelde schijven verwijderen om een gedeelde opslag groep te maken. 


**Beperkingen**: 
- Alleen beschikbaar voor SQL Server 2019 en Windows Server 2019 in preview. 
- Virtuele machines moeten worden geplaatst in dezelfde beschikbaarheidsset en proximity-plaatsings groep.
- Beschikbaarheids zones worden niet ondersteund.
- Premium-SSD schijf cache gebruik wordt niet ondersteund.
 
Zie [SQL Server failover-cluster exemplaar met gedeelde Azure-schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md)om aan de slag te gaan. 

### <a name="storage-spaces-direct"></a>Opslagruimten direct

[Opslagruimten direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) is een Windows Server-functie die wordt ondersteund door Failover Clustering in azure virtual machines. Het biedt een virtueel SAN op basis van software.

**Ondersteund besturings systeem**: Windows Server 2016 en hoger   
**Ondersteunde SQL-versie**: SQL Server 2016 en hoger   


**Verleend** 
- Voldoende netwerk bandbreedte biedt een robuuste en zeer krachtige, gedeelde opslag oplossing. 
- Biedt ondersteuning voor Azure Blob-cache, zodat Lees bewerkingen lokaal vanuit de cache kunnen worden uitgevoerd. (Updates worden gelijktijdig gerepliceerd naar beide knoop punten.) 
- Ondersteunt FileStream. 

**Hardwarebeperkingen**
- Alleen beschikbaar voor Windows Server 2016 en hoger. 
- Beschikbaarheids zones worden niet ondersteund.
- Vereist dat dezelfde schijf capaciteit is gekoppeld aan beide virtuele machines. 
- Hoge netwerk bandbreedte is vereist voor hoge prestaties vanwege de continue replicatie van de schijf. 
- Vereist een grotere VM-grootte en dubbele betaling voor opslag, omdat opslag is gekoppeld aan elke virtuele machine. 

Zie [SQL Server failover-cluster exemplaar met opslagruimten direct](failover-cluster-instance-azure-shared-disks-manually-configure.md)om aan de slag te gaan. 

### <a name="premium-file-share"></a>Premium-bestands share

[Premium-bestands shares](../../../storage/files/storage-how-to-create-premium-fileshare.md) zijn een functie van [Azure files](../../../storage/files/index.yml). Premium-bestands shares zijn een SSD-back-up en hebben een consistente lage latentie. Ze worden volledig ondersteund voor gebruik met failover-cluster exemplaren voor SQL Server 2012 of hoger op Windows Server 2012 of hoger. Premium-bestands shares bieden meer flexibiliteit, omdat u een bestands share zonder uitval tijd kunt verg Roten of verkleinen en schalen.

**Ondersteund besturings systeem**: Windows Server 2012 en hoger   
**Ondersteunde SQL-versie**: SQL Server 2012 en hoger   

**Verleend** 
- Alleen gedeelde opslag oplossing voor virtuele machines verspreid over meerdere beschikbaarheids zones. 
- Volledig beheerd bestands systeem met wacht tijden van één cijfer en bursteer I/O-prestaties. 

**Hardwarebeperkingen**
- Alleen beschikbaar voor Windows Server 2012 en hoger. 
- FileStream wordt niet ondersteund. 


Zie [SQL Server failover-cluster exemplaar met Premium-bestands share](failover-cluster-instance-premium-file-share-manually-configure.md)om aan de slag te gaan. 

### <a name="partner"></a>Partner

Er zijn oplossingen voor partner clusters met ondersteunde opslag. 

**Ondersteund besturings systeem**: alle   
**Ondersteunde SQL-versie**: alle   

In een voor beeld wordt SIOS data keeper als opslag gebruikt. Zie voor meer informatie het blog bericht [failover clustering en SIOS data keeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI en ExpressRoute

U kunt ook een gedeelde blok opslag voor iSCSI-doel beschikbaar maken via Azure ExpressRoute. 

**Ondersteund besturings systeem**: alle   
**Ondersteunde SQL-versie**: alle   

NetApp Private Storage (NPS) biedt bijvoorbeeld een iSCSI-doel via ExpressRoute met Equinix voor Azure-Vm's.

Neem contact op met de leverancier voor de oplossingen met betrekking tot gedeelde opslag en gegevens replicatie van micro soft-partners voor problemen met de toegang tot gegevens in de failover.

## <a name="connectivity"></a>Connectiviteit

Failover-cluster exemplaren met SQL Server op Azure Virtual Machines gebruiken een [gedistribueerde netwerk naam (DNN)](hadr-distributed-network-name-dnn-configure.md) of een [virtuele netwerk naam (VNN) met Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md) om verkeer door te sturen naar het SQL Server-exemplaar, ongeacht welk knoop punt momenteel eigenaar is van de geclusterde resources. Er zijn aanvullende overwegingen bij het gebruik van bepaalde functies en de DNN met een SQL Server FCI. Zie [DNN interoperabiliteit met SQL Server FCI](failover-cluster-instance-dnn-interoperability.md) voor meer informatie. 

Zie [HADR-verbindingen naar SQL Server op Azure-Vm's routeren](hadr-cluster-best-practices.md#connectivity)voor meer informatie over opties voor cluster connectiviteit. 

## <a name="limitations"></a>Beperkingen

Houd rekening met de volgende beperkingen voor failover-cluster exemplaren met SQL Server op Azure Virtual Machines. 

### <a name="lightweight-resource-provider"></a>Licht gewicht resource provider   
Op dit moment worden SQL Server failover-cluster exemplaren op virtuele machines van Azure alleen ondersteund met de [licht gewicht beheer modus](sql-vm-resource-provider-register.md#management-modes) van de [SQL Server IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md). Als u wilt overschakelen van de volledige extensie modus naar Lightweight, verwijdert u de resource van de **virtuele SQL-machine** voor de bijbehorende vm's en registreert u deze vervolgens bij de resource provider van de SQL-vm in de Lightweight-modus. Wanneer u de bron van de **virtuele SQL-machine** verwijdert met behulp van de Azure Portal, schakelt u het selectie vakje naast de juiste virtuele machine uit. 

De volledige extensie ondersteunt functies zoals automatische back-ups, patches en Geavanceerd Portal beheer. Deze functies werken niet voor SQL Server Vm's nadat de agent opnieuw is geïnstalleerd in de modus voor licht gewicht beheer.

### <a name="msdtc"></a>MSDTC   
Azure Virtual Machines ondersteunt MSDTC op Windows Server 2019 met opslag op geclusterde gedeelde volumes (CSV) en [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md).

In azure Virtual Machines wordt MSDTC niet ondersteund voor Windows Server 2016 of eerder omdat:

- De geclusterde MSDTC-bron kan niet worden geconfigureerd voor het gebruik van gedeelde opslag. Als u in Windows Server 2016 een MSDTC-bron maakt, wordt er geen gedeelde opslag weer gegeven die beschikbaar is voor gebruik, zelfs als de opslag ruimte beschikbaar is. Dit probleem is opgelost in Windows Server 2019.
- Met de basis load balancer worden geen RPC-poorten afgehandeld.


## <a name="next-steps"></a>Volgende stappen

Bekijk [Aanbevolen procedures voor cluster configuraties](hadr-cluster-best-practices.md)en u kunt [uw SQL Server-VM voorbereiden voor FCI](failover-cluster-instance-prepare-vm.md). 

Zie voor meer informatie: 

- [Windows-cluster technologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Failover-cluster exemplaren SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

