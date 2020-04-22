---
title: Prestaties optimaliseren op virtuele machines uit azure Lsv2-serie - Opslag
description: Ontdek hoe u de prestaties voor uw oplossing optimaliseren op de virtuele machines uit de Lsv2-serie.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 7a0d5e29097bc9a672e142fcffb0ebe879fe2475
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757683"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimaliseer de prestaties op de virtuele machines uit de Lsv2-serie

Virtuele machines uit de Lsv2-serie ondersteunen een verscheidenheid aan workloads die een hoge I/O-en -doorvoer op lokale opslag in een breed scala aan toepassingen en industrieën nodig hebben.  De Lsv2-serie is ideaal voor Big Data, SQL, NoSQL-databases, datawarehousing en grote transactionele databases, waaronder Cassandra, MongoDB, Cloudera en Redis.

Het ontwerp van de Lsv2-serie Virtual Machines (VM's) maximaliseert de AMD EPYC™ 7551-processor om de beste prestaties te leveren tussen de processor, het geheugen, de NVMe-apparaten en de VM's. In samenwerking met partners in Linux zijn verschillende builds beschikbaar voor Azure Marketplace die zijn geoptimaliseerd voor prestaties uit de Lsv2-serie en momenteel:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

Dit artikel bevat tips en suggesties om ervoor te zorgen dat uw workloads en toepassingen de maximale prestaties bereiken die zijn ontworpen in de VM's. De informatie op deze pagina wordt voortdurend bijgewerkt naarmate er meer Lsv2-geoptimaliseerde afbeeldingen worden toegevoegd aan de Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ chipsetarchitectuur

Vm's uit de Lsv2-serie maken gebruik van AMD EYPC™ serverprocessors op basis van de Zen-microarchitectuur. AMD ontwikkelde Infinity Fabric (IF) voor EYPC™ als schaalbare interconnect voor zijn NUMA-model dat kan worden gebruikt voor on-die, on-package en multi-package communicatie. Vergeleken met QPI (Quick-Path Interconnect) en UPI (Ultra-Path Interconnect) die worden gebruikt op Intel moderne monolithische-die-processors, kan AMD's veel-NUMA small-die architectuur zowel prestatievoordelen als uitdagingen met zich meebrengen. De werkelijke impact van geheugenbandbreedte en latentiebeperkingen kan variëren afhankelijk van het type workloads dat wordt uitgevoerd.

## <a name="tips-to-maximize-performance"></a>Tips om de prestaties te maximaliseren

* Als u een aangepaste Linux GuestOS uploadt voor uw workload, houdt u er rekening mee dat Accelerated Networking standaard **uitgeschakeld** is. Als u versnelde netwerken wilt inschakelen, schakelt u dit in op het moment van het maken van vm's voor de beste prestaties.

* De hardware die de Lsv2-serie VM's aandrijft, maakt gebruik van NVMe-apparaten met acht I/O-wachtrijparen (QP)s. Elke NVMe-apparaat I/O-wachtrij is eigenlijk een paar: een indieningswachtrij en een voltooiingswachtrij. De NVMe driver is ingesteld om het gebruik van deze acht I/O QPs te optimaliseren door I/O's te distribueren in een round robin schema. Voer acht taken per apparaat uit om maximaal te presteren.

* Vermijd het mengen van NVMe-beheeropdrachten (bijvoorbeeld NVMe SMART info query, enz.) met NVMe I/O-opdrachten tijdens actieve workloads. Lsv2 NVMe-apparaten worden ondersteund door Hyper-V NVMe Direct-technologie, die overschakelt naar "slow mode" wanneer er NVMe-beheeropdrachten in behandeling zijn. Lsv2-gebruikers kunnen een dramatische prestatiedaling in nvme-i/o-prestaties zien als dat gebeurt.

* Lsv2-gebruikers moeten niet vertrouwen op de NUMA-informatie (alle 0) die vanuit de VM wordt gerapporteerd voor gegevensstations om de NUMA-affiniteit voor hun apps te bepalen. De aanbevolen manier voor betere prestaties is om workloads zo mogelijk over CPU's te spreiden.

* De maximale ondersteunde wachtrijdiepte per I/O-wachtrijpaar voor Lsv2 VM NVMe-apparaat is 1024 (vs. Amazon i3 QD 32-limiet). Lsv2-gebruikers moeten hun (synthetische) benchmarkingworkloads beperken tot wachtrijdiepte 1024 of lager om te voorkomen dat de volledige wachtrijwordt geactiveerd, wat de prestaties kan verminderen.

## <a name="utilizing-local-nvme-storage"></a>Gebruik maken van lokale NVMe-opslag

Lokale opslag op de 1,92 TB NVMe-schijf op alle Lsv2 VM's is vluchtig. Tijdens een succesvolle standaard reboot van de VM blijven de gegevens op de lokale NVMe-schijf bestaan. De gegevens blijven niet bestaan op de NVMe als de VM opnieuw wordt geïmplementeerd, uitgetrokken of verwijderd. Gegevens blijven niet bestaan als een ander probleem ervoor zorgt dat de VM of de hardware waarop deze wordt uitgevoerd, ongezond wordt. Wanneer dit gebeurt, worden alle gegevens over de oude host veilig gewist.

Er zullen ook gevallen zijn waarin de VM moet worden verplaatst naar een andere hostmachine, bijvoorbeeld tijdens een geplande onderhoudsbewerking. Geplande onderhoudsbewerkingen en sommige hardwarefouten kunnen worden verwacht met [geplande gebeurtenissen.](scheduled-events.md) Geplande gebeurtenissen moeten worden gebruikt om op de hoogte te blijven van alle voorspelde onderhouds- en herstelbewerkingen.

In het geval dat een geplande onderhoudsgebeurtenis vereist dat de VM opnieuw wordt gemaakt op een nieuwe host met lege lokale schijven, moeten de gegevens opnieuw worden gesynchroniseerd (nogmaals, waarbij alle gegevens over de oude host veilig worden gewist). Dit gebeurt omdat Vm's uit de LSV2-serie momenteel geen live migratie op de lokale NVMe-schijf ondersteunen.

Er zijn twee modi voor gepland onderhoud.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standaard VM klantgestuurd onderhoud

- De VM wordt tijdens een periode van 30 dagen verplaatst naar een bijgewerkte host.
- Lsv2 lokale opslaggegevens kunnen verloren gaan, dus back-upgegevens voorafgaand aan de gebeurtenis worden aanbevolen.

### <a name="automatic-maintenance"></a>Automatisch onderhoud

- Treedt op als de klant geen klantgestuurd onderhoud uitvoert, of in het geval van noodprocedures zoals een zero-day gebeurtenis voor beveiliging.
- Bedoeld om klantgegevens te bewaren, maar er is een klein risico van een VM-blokkering of reboot.
- Lsv2 lokale opslaggegevens kunnen verloren gaan, dus back-upgegevens voorafgaand aan de gebeurtenis worden aanbevolen.

Voor alle komende servicegebeurtenissen gebruikt u het gecontroleerde onderhoudsproces om een tijdstip te selecteren dat u het meest geschikt maakt voor de update. Voorafgaand aan het evenement u een back-up maken van uw gegevens in premium opslag. Nadat het onderhoudsevenement is voltooid, u uw gegevens retourneren naar de vernieuwde Lsv2 Vm's lokale NVMe-opslag.

Scenario's die gegevens over lokale NVMe-schijven bijhouden, zijn onder andere:

- De VM is actief en gezond.
- De VM wordt opnieuw opgestart (door u of Azure).
- De VM wordt onderbroken (gestopt zonder detoewijzing).
- Het merendeel van de geplande onderhoudswerkzaamheden.

Scenario's die gegevens veilig wissen om de klant te beschermen, zijn onder andere:

- De VM wordt opnieuw geïmplementeerd, gestopt (verwijderd) of verwijderd (door u).
- De VM wordt ongezond en moet service genezen naar een ander knooppunt als gevolg van een hardware probleem.
- Een klein aantal van de geplande onderhoudswerkzaamheden waarvoor de VM moet worden toegewezen aan een andere host voor onderhoud.

Zie [Back-up en noodherstel voor Azure IaaS-schijven voor](backup-and-disaster-recovery-for-azure-iaas-disks.md)meer informatie over opties voor het maken van back-ups in lokale opslag.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

* **Hoe begin ik met het implementeren van Lsv2-serie VM's?**  
   Net als elke andere VM, gebruik maken van de [Portal](quick-create-portal.md), [Azure CLI,](quick-create-cli.md)of [PowerShell](quick-create-powershell.md) om een VM te maken.

* **Zal een enkele NVMe-schijffout ervoor zorgen dat alle VM's op de host mislukken?**  
   Als er een schijffout wordt gedetecteerd op het hardwareknooppunt, is de hardware in een niet-dane status. Wanneer dit gebeurt, worden alle VM's op het knooppunt automatisch verwijderd en verplaatst naar een gezond knooppunt. Voor VMs uit de Lsv2-serie betekent dit dat de gegevens van de klant op het falende knooppunt ook veilig worden gewist en door de klant op het nieuwe knooppunt opnieuw moeten worden gemaakt. Zoals opgemerkt, voordat live migratie beschikbaar komt op Lsv2, zullen de gegevens op het falende knooppunt proactief worden verplaatst met de VM's terwijl ze worden overgebracht naar een ander knooppunt.

* **Moet ik rq_affinity aanpassen voor de prestaties?**  
   De rq_affinity instelling is een kleine aanpassing bij het gebruik van de absolute maximale input/output bewerkingen per seconde (IOPS). Zodra alles goed werkt, probeer dan om rq_affinity op 0 om te zien of het een verschil maakt.

* **Moet ik de blk_mq instellingen wijzigen?**  
   RHEL/CentOS 7.x maakt automatisch gebruik van blk-mq voor de NVMe-apparaten. Er zijn geen configuratiewijzigingen of -instellingen nodig. De instelling scsi_mod.use_blk_mq is alleen voor SCSI en werd gebruikt tijdens Lsv2 Preview omdat de NVMe-apparaten zichtbaar waren in de vm's van de gast als SCSI-apparaten. Momenteel zijn de NVMe-apparaten zichtbaar als NVMe-apparaten, dus de SCSI blk-mq-instelling is niet relevant.

* **Moet ik "fio" veranderen?**  
   Om maximale IOPS te krijgen met een prestatiemeetinstrument zoals 'fio' in de L64v2- en L80v2 VM-formaten, stelt u "rq_affinity" in op 0 op elk NVMe-apparaat.  Deze opdrachtregel stelt bijvoorbeeld 'rq_affinity' in op nul voor alle 10 NVMe-apparaten in een L80v2-vm:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Merk ook op dat de beste prestaties wordt verkregen wanneer I / O wordt rechtstreeks gedaan om elk van de ruwe NVMe-apparaten zonder partitionering, geen bestandssystemen, geen RAID 0 config, enz. Voordat u een testsessie start, moet u ervoor zorgen `blkdiscard` dat de configuratie in een bekende frisse/schone toestand is door op elk van de NVMe-apparaten te worden uitgevoerd.
   
## <a name="next-steps"></a>Volgende stappen

* Bekijk specificaties voor alle [VM's die zijn geoptimaliseerd voor opslagprestaties](sizes-storage.md) op Azure
