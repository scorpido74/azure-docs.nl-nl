---
title: Prestatierichtlijnen voor SQL Server in Azure | Microsoft Documenten
description: Biedt richtlijnen voor het optimaliseren van SQL Server-prestaties in Microsoft Azure VM's.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650534"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Prestatierichtlijnen voor SQL Server in Azure Virtual Machines

## <a name="overview"></a>Overzicht

In dit artikel vindt u richtlijnen voor het optimaliseren van sql server-prestaties in Microsoft Azure Virtual Machine. We raden u aan om tijdens het uitvoeren van SQL Server in Azure Virtual Machines dezelfde opties voor afstemmen te gebruiken die van toepassing zijn op SQL Server in een on-premises serveromgeving. De prestaties van een relationele database in een openbare cloud zijn echter afhankelijk van vele factoren, zoals de grootte van een virtuele machine en de configuratie van de gegevensschijven.

[SQL Server-afbeeldingen die in de Azure-portal zijn ingericht,](quickstart-sql-vm-create-portal.md) volgen aanbevolen procedures voor algemene opslagconfiguratie (zie [Opslagconfiguratie voor SQL Server VM's](virtual-machines-windows-sql-server-storage-configuration.md)voor meer informatie over hoe opslag is geconfigureerd). Na het inrichten, overweeg dan het toepassen van andere optimalisaties besproken in dit artikel. Baseer uw keuzes op uw werkbelasting en verifieer door middel van testen.

> [!TIP]
> Er is meestal een trade-off tussen het optimaliseren voor kosten en optimaliseren voor prestaties. Dit artikel is gericht op het verkrijgen van de *beste* prestaties voor SQL Server op Azure VM's. Als uw werklast minder veeleisend is, hoeft u mogelijk niet elke optimalisatie hieronder te maken. Houd rekening met uw prestatiebehoeften, kosten en werkbelastingspatronen terwijl u deze aanbevelingen evalueert.

## <a name="quick-check-list"></a>Snelcontrolelijst

Hieronder volgt een snelle controlelijst voor optimale prestaties van SQL Server op Azure Virtual Machines:

| Onderwerp | Optimalisaties |
| --- | --- |
| [VM-grootte](#vm-size-guidance) | - Gebruik VM-formaten met 4 of meer vCPU's zoals [E4S_v3](../../ev3-esv3-series.md) of hoger, of [DS12_v2](../../dv2-dsv2-series-memory.md) of hoger.<br/><br/> - [De ES-, Eas-, Ds- en Das-serie](../../sizes-general.md) bieden de optimale geheugen-vCPU-verhouding die nodig is voor oltp-werkbelastingprestaties. <br/><br/> - [De M-serie](../../m-series.md) biedt de hoogste geheugen-vCPU-verhouding die nodig is voor bedrijfskritieke prestaties en is ideaal voor datawarehouseworkloads. <br/><br/> - Verzamel de [IOPS-,](../premium-storage-performance.md#iops) [doorvoer-](../premium-storage-performance.md#throughput) en [latentievereisten](../premium-storage-performance.md#latency) van de doelworkload op piekmomenten door de [checklist voor toepassingsprestatievereisten](../premium-storage-performance.md#application-performance-requirements-checklist) te volgen en selecteer vervolgens de [VM-grootte](../sizes-general.md) die kan worden geschaald naar de prestatievereisten van uw werkbelasting.|
| [Opslag](#storage-guidance) | - Raadpleeg voor gedetailleerde tests van SQL Server-prestaties op Azure VM's met TPC-E en TPC_C benchmarks de blog [Optimize OLTP-prestaties.](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) <br/><br/> - Gebruik [premium SSD's](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) voor de beste prijs-prestatievoordelen. Configureer [ReadOnly-cache](../premium-storage-performance.md#disk-caching) voor gegevensbestanden en geen cache voor het logboekbestand. <br/><br/> - Gebruik [Ultra-schijven](../disks-types.md#ultra-disk) als de werkbelasting minder dan 1 ms-opslaglatencies vereist hebben. <br/><br/> - Verzamel de vereisten voor opslaglatentie voor SQL Server-gegevens, logboek- en Temp DB-bestanden door [de toepassing te controleren](../premium-storage-performance.md#application-performance-requirements-checklist) voordat u het schijftype kiest. Als <1ms opslaglatencies vereist zijn, gebruik dan Ultra Schijven, anders gebruik je premium SSD. Als er alleen lage latencies nodig zijn voor het logboekbestand en niet voor gegevensbestanden, [voorziet u de Ultra Disk](../disks-enable-ultra-ssd.md) op vereiste IOPS- en doorvoerniveaus alleen voor het logboekbestand. <br/><br/> -  [Premium bestandsshares](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) worden aanbevolen als gedeelde opslag voor een SQL Server-failoverclusterinstantie. Premium bestandsshares ondersteunen geen caching en bieden beperkte prestaties in vergelijking met premium SSD-schijven. Kies premium SSD-beheerde schijven boven premium bestandsshares voor zelfstandige SQL-exemplaren; maar maak gebruik van premium bestandsshares voor gedeelde opslag van failover-clusterinstance voor het gemak van onderhoud en flexibele schaalbaarheid. <br/><br/> - Standaardopslag wordt alleen aanbevolen voor ontwikkelings- en testdoeleinden of voor back-upbestanden en mag niet worden gebruikt voor productieworkloads. <br/><br/> - Bewaar het [opslagaccount](../../../storage/common/storage-create-storage-account.md) en SQL Server VM in dezelfde regio.<br/><br/> - Azure [georedundante opslag](../../../storage/common/storage-redundancy.md) (geo-replicatie) uitschakelen op het opslagaccount.  |
| [Schijven](#disks-guidance) | - Gebruik minimaal 2 [premium SSD-schijven](../disks-types.md#premium-ssd) (1 voor logbestand en 1 voor gegevensbestanden). <br/><br/> - Voor workloads die <1 ms IO-latencies vereisen, schakelt u schrijfversneller in voor M-serie in en overweeg u Ultra SSD-schijven te gebruiken voor Es- en DS-reeksen. <br/><br/> - Alleen [lezen op](../premium-storage-performance.md#disk-caching) de schijf(en) inschakelen voor het hosten van de gegevensbestanden.<br/><br/> - Voeg extra 20% premium IOPS/doorvoercapaciteit toe die uw workload vereist bij [het configureren van opslag voor SQL Server-gegevens, logboek- en TempDB-bestanden](virtual-machines-windows-sql-server-storage-configuration.md) <br/><br/> - Vermijd het gebruik van besturingssysteem of tijdelijke schijven voor database opslag of logging.<br/><br/> - Schakel het incachen op schijf(en) als host van het logboekbestand niet in.  **Belangrijk:** stop de SQL Server-service bij het wijzigen van de cache-instellingen voor een Azure VM-schijf.<br/><br/> - Streep meerdere Azure-gegevensschijven om een hogere opslagdoorvoer te krijgen.<br/><br/> - Formaat met gedocumenteerde toewijzingsgroottes. <br/><br/> - Plaats TempDB op `D:\` de lokale SSD-schijf voor bedrijfskritieke SQL Server-workloads (na het kiezen van de juiste VM-grootte). Als u de VM maakt vanuit Azure-portal- of Azure-quickstartsjablonen en [Temp DB op de lokale schijf plaatst,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) hebt u geen verdere actie nodig. voor alle andere gevallen volg de stappen in de blog voor [het gebruik van SSD's om TempDB op te slaan](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) om fouten te voorkomen na opnieuw opstarten. Als de capaciteit van de lokale schijf is niet genoeg voor uw Temp DB grootte, plaats Temp DB op een opslag pool [gestript](../premium-storage-performance.md) op premium SSD schijven met [alleen-lezen caching](../premium-storage-performance.md#disk-caching). |
| [I/o](#io-guidance) |- Compressie van databasepagina inschakelen.<br/><br/> - Direct bestand initialisatie inschakelen voor gegevensbestanden.<br/><br/> - Beperk de automatische groei van de database.<br/><br/> - Autoshrink van de database uitschakelen.<br/><br/> - Verplaats alle databases naar gegevensschijven, inclusief systeemdatabases.<br/><br/> - Sql Server-foutlogboeken en traceerbestandsmappen naar gegevensschijven.<br/><br/> - Standaardback-up- en databasebestandslocaties configureren.<br/><br/> - [Vergrendelde pagina's in het geheugen inschakelen](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - SQL Server-prestatiecorrecties toepassen. |
| [Functiespecifiek](#feature-specific-guidance) | - Maak direct een back-up naar blobopslag.<br/><br/>- Gebruik [bestandsmomentopnamen](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) voor databases groter dan 12 TB. <br/><br/>- Gebruik meerdere Temp DB-bestanden, 1 bestand per kern, maximaal 8 bestanden.<br/><br/>- Stel het maximale servergeheugen in op 90% of tot 50 GB over voor het besturingssysteem. <br/><br/>- Schakel zachte NUMA in. |

Voor meer informatie over *hoe* en *waarom* deze optimalisaties te maken, bekijk de details en richtlijnen in de volgende secties.

## <a name="vm-size-guidance"></a>Richtlijnen voor VM-grootte

Begin met het verzamelen van de cpu-, geheugen- en opslagdoorvoervereisten van de werkbelasting op piekmomenten. \LogicalDisk\Disk Reads/Sec en \LogicalDisk\Disk Writes/Sec performance counters can be used to collect read and write IOPS requirements and \LogicalDisk\Disk Bytes/Sec counter can be used to collect [storage throughput requirements](../premium-storage-performance.md#disk-caching) for Data, Log, and Temp DB files. Nadat IOPS en doorvoervereisten op het hoogtepunt zijn gedefinieerd, evalueert u de VM-formaten die capaciteit. Als uw werkbelasting bijvoorbeeld 20 K iOPS en 10K IOPS op het hoogtepunt moet lezen, u kiezen voor E16s_v3 (met maximaal 32 K-cached en 25600 IOPS zonder cache) of M16_s (met maximaal 20 K-cache en 10K iOPS zonder cache) met 2 P30-schijven. Zorg ervoor dat u zowel de doorvoer- als de IOPS-vereisten van de werkbelasting begrijpt, aangezien VM's verschillende schaallimieten hebben voor IOPS en doorvoer.<br/><br/>[DSv_3-](../../dv3-dsv3-series.md) [en Es_v3-serie](../../ev3-esv3-series.md) worden gehost op hardware voor algemeen gebruik met Intel Haswell- of Broadwell-processors. [De M-serie](../../m-series.md) biedt het hoogste aantal vCPU's en geheugen voor de grootste SQL Server-workloads en wordt gehost op geheugengeoptimaliseerde hardware met Skylake-processorfamilie. Deze VM-serie ondersteunt premium opslag, die wordt aanbevolen voor de beste prestaties met leescache op hostniveau. Zowel Es_v3- als M-serie zijn ook beschikbaar in [beperkte kerngroottes,](../../windows/constrained-vcpu.md)wat geld bespaart voor workloads met lagere reken- en hoge opslagcapaciteitsvereisten. 

## <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

Raadpleeg de blog [Optimize OLTP-prestaties](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)voor gedetailleerde tests van SQL Server-prestaties op Azure VM's met TPC-E- en TPC_C-benchmarks. 

Azure blob cache met premium SSDs wordt aanbevolen voor alle productieworkloads. 

> [!WARNING]
> Standaard HDD's en SSD's hebben verschillende latencies en bandbreedte en worden alleen aanbevolen voor dev/test workloads. Productieworkloads moeten premium SSD's gebruiken.

Daarnaast raden we u aan uw Azure-opslagaccount te maken in hetzelfde datacenter als uw virtuele SQL Server-machines om overdrachtsvertragingen te verminderen. Schakel bij het maken van een opslagaccount georeplicatie uit als consistente schrijfvolgorde voor meerdere schijven niet is gegarandeerd. In plaats daarvan u overwegen een SQL Server-noodhersteltechnologie tussen twee Azure-datacenters te configureren. Zie [Hoge beschikbaarheid en noodherstel voor SQL Server in Azure Virtual Machines voor](virtual-machines-windows-sql-high-availability-dr.md)meer informatie.

## <a name="disks-guidance"></a>Richtlijnen voor schijven

Er zijn drie hoofdschijftypen op een Azure-vm:

* **OS-schijf:** Wanneer u een Azure Virtual Machine maakt, koppelt het platform ten minste één schijf (gelabeld als het **C-station)** aan de VM voor uw besturingssysteemschijf. Deze schijf is een VHD die is opgeslagen als een paginablob in opslag.
* **Tijdelijke schijf**: Azure virtuele machines bevatten een andere schijf genaamd de tijdelijke schijf (gelabeld als de **D:** drive). Dit is een schijf op het knooppunt die kan worden gebruikt voor krasruimte.
* **Gegevensschijven:** U ook extra schijven aan uw virtuele machine koppelen als gegevensschijven, en deze worden opgeslagen in opslag als paginablobs.

In de volgende secties worden aanbevelingen beschreven voor het gebruik van deze verschillende schijven.

### <a name="operating-system-disk"></a>Besturingssysteemschijf

Een besturingssysteemschijf is een VHD die u opstarten en monteren als een lopende versie van een besturingssysteem en wordt aangeduid als **C-station.**

Standaardcachebeleid op de schijf van het besturingssysteem is **Lezen/schrijven**. Voor prestatiegevoelige toepassingen raden we u aan gegevensschijven te gebruiken in plaats van de schijf van het besturingssysteem. Zie de sectie over gegevensschijven hieronder.

### <a name="temporary-disk"></a>Tijdelijke schijf


Het tijdelijke opslagstation, gelabeld **D** als het D-station, blijft niet bestaan in Azure blob-opslag. Sla uw gebruikersdatabasebestanden of gebruikerstransactielogboekbestanden niet op het **station D**op.

Plaats TempDB op `D:\` de lokale SSD-schijf voor bedrijfskritieke SQL Server-workloads (na het kiezen van de juiste VM-grootte). Als u de VM maakt vanuit Azure-portal- of Azure-quickstartsjablonen en [Temp DB op de lokale schijf plaatst,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)hebt u geen verdere actie nodig; voor alle andere gevallen volg de stappen in de blog voor [het gebruik van SSD's om TempDB op te slaan](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) om fouten te voorkomen na opnieuw opstarten. Als de capaciteit van de lokale schijf is niet genoeg voor uw Temp DB grootte, plaats Temp DB op een opslag pool [gestript](../premium-storage-performance.md) op premium SSD schijven met [alleen-lezen caching](../premium-storage-performance.md#disk-caching).

Voor VM's die premium SSD's ondersteunen, u TempDB ook opslaan op een schijf die premium SSD's ondersteunt met leescaching ingeschakeld.


### <a name="data-disks"></a>Gegevensschijven

* **Gebruik premium SSD-schijven voor gegevens en logboekbestanden:** Als u geen schijfstriping gebruikt, gebruikt u twee premium SSD-schijven waarbij de ene schijf het logboekbestand bevat en de andere de gegevens. Elke premium SSD biedt een aantal IOPS en bandbreedte (MB/s) afhankelijk van de grootte, zoals afgebeeld in het artikel, [Selecteer een schijftype](../disks-types.md). Als u een schijfstriptechniek gebruikt, zoals opslagruimten, bereikt u optimale prestaties door twee pools te hebben, een voor het logboekbestand(en) en de andere voor de gegevensbestanden. Als u echter van plan bent SQL Server-failoverclusterexemplaren (FCI) te gebruiken, moet u één groep configureren of in plaats daarvan [premium bestandsshares](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) gebruiken.

   > [!TIP]
   > - Zie het volgende blogbericht: [Richtlijnen voor opslagconfiguratie voor SQL Server op Azure VM voor](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)testresultaten op verschillende schijf- en workloadconfiguraties.
   > - Voor bedrijfskritieke prestaties voor SQL-servers die ~ 50.000 IOPS nodig hebben, u overwegen om 10 -P30-schijven te vervangen door een Ultra SSD. Zie voor meer informatie de volgende blogpost: [Bedrijfskritische prestaties met Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Wanneer u een SQL Server VM inde portal indient, u uw opslagconfiguratie bewerken. Afhankelijk van uw configuratie configureert Azure een of meer schijven. Meerdere schijven worden gecombineerd tot één opslagpool met striping. Zowel de gegevens als de logbestanden bevinden zich samen in deze configuratie. Zie [Opslagconfiguratie voor SQL Server VM's voor](virtual-machines-windows-sql-server-storage-configuration.md)meer informatie.

* **Schijfstriping:** voor meer doorvoer u extra gegevensschijven toevoegen en Schijfstriping gebruiken. Om het aantal gegevensschijven te bepalen, moet u het aantal IOPS en bandbreedte analyseren dat nodig is voor uw logbestand(en) en voor uw gegevens en TempDB-bestand(en). Merk op dat verschillende VM-formaten verschillende limieten hebben voor het aantal iops en bandbreedte dat wordt ondersteund, zie de tabellen op IOPS per [VM-grootte](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Volg de volgende richtlijnen:

  * Gebruik [opslagruimten met](https://technet.microsoft.com/library/hh831739.aspx) de volgende richtlijnen voor Windows 8/Windows Server 2012 of hoger:

      1. Stel de interleave (streepgrootte) in op 64 KB (65.536 bytes) voor OLTP-workloads en 256 KB (262.144 bytes) voor workloads voor gegevensopslag om prestatie-impact als gevolg van foutie van de partitie te voorkomen. Dit moet worden ingesteld met PowerShell.
      2. Stel kolomtelling in = aantal fysieke schijven. Gebruik PowerShell bij het configureren van meer dan 8 schijven (niet de gebruikersinterface van Serverbeheer). 

    Met de volgende PowerShell wordt bijvoorbeeld een nieuwe opslaggroep gemaakt met de interleave-grootte tot 64 KB en het aantal kolommen tot 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Voor Windows 2008 R2 of eerder u dynamische schijven (os gestreepte volumes) gebruiken en de streepgrootte is altijd 64 KB. Deze optie is afgeschaft vanaf Windows 8/Windows Server 2012. Zie de ondersteuningsinstructie bij Virtual Disk Service voor meer informatie [over de overgang naar Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Als u [Storage Spaces Direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) gebruikt met [SQL Server Failover Cluster Instances,](virtual-machines-windows-portal-sql-create-failover-cluster.md)moet u één groep configureren. Hoewel er verschillende volumes kunnen worden gemaakt op die ene groep, hebben ze allemaal dezelfde kenmerken, zoals hetzelfde cachingbeleid.

  * Bepaal het aantal schijven dat aan uw opslaggroep is gekoppeld op basis van uw belastingsverwachtingen. Houd er rekening mee dat verschillende VM-formaten verschillende aantallen gekoppelde gegevensschijven toestaan. Zie [Grootte voor virtuele machines voor](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie .

  * Als u geen premium SSD's (dev/testscenario's) gebruikt, is de aanbeveling om het maximum aantal gegevensschijven toe te voegen dat wordt ondersteund door uw [VM-grootte](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en schijfstriping te gebruiken.

* **Caching-beleid:** let op de volgende aanbevelingen voor het cachingbeleid, afhankelijk van uw opslagconfiguratie.

  * Als u afzonderlijke schijven gebruikt voor gegevens en logboekbestanden, schakelt u leescache in op de gegevensschijven die uw gegevensbestanden en TempDB-gegevensbestanden hosten. Dit kan resulteren in een aanzienlijk prestatievoordeel. Schakel het caching op de schijf met het logboekbestand niet in, omdat dit een kleine daling van de prestaties veroorzaakt.

  * Als u schijfstriping gebruikt in één opslaggroep, profiteren de meeste workloads van leescaching. Als u afzonderlijke opslaggroepen voor de logboek- en gegevensbestanden hebt, schakelt u leescache alleen in op de opslaggroep voor de gegevensbestanden. In bepaalde zware schrijfworkloads kunnen betere prestaties worden bereikt zonder caching. Dit kan alleen worden bepaald door middel van testen.

  * De eerdere aanbevelingen zijn van toepassing op premium-SSD's. Als u geen premium SSD's gebruikt, schakelt u geen caching in op gegevensschijven.

  * Zie de volgende artikelen voor instructies voor het configureren van schijfcache. Zie voor het klassieke ASM-implementatiemodel: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) en [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Zie voor het implementatiemodel Azure Resource Manager: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) en [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Stop de SQL Server-service wanneer u de cache-instelling van Azure VM-schijven wijzigt om beschadiging van de database te voorkomen.

* **NTFS-toewijzingseenheidsgrootte:** Bij het opmaken van de gegevensschijf wordt aanbevolen dat u een 64-KB-toewijzingseenheidsgrootte gebruikt voor gegevens en logboekbestanden en TempDB. Als TempDB op de tijdelijke schijf wordt geplaatst (D:\ schijf) de prestaties opgedaan door gebruik te maken van deze schijf opweegt tegen de noodzaak van een 64K toewijzing eenheid grootte. 

* **Aanbevolen procedures voor schijfbeheer:** Wanneer u een gegevensschijf verwijdert of het cachetype wijzigt, stopt u de SQL Server-service tijdens de wijziging. Wanneer de cache-instellingen op de OS-schijf worden gewijzigd, stopt Azure de VM, wijzigt het cachetype en start de VM opnieuw. Wanneer de cache-instellingen van een gegevensschijf worden gewijzigd, wordt de VM niet gestopt, maar wordt de gegevensschijf tijdens de wijziging losgekoppeld van de vm en vervolgens opnieuw bevestigd.

  > [!WARNING]
  > Als u de SQL Server-service niet stopt tijdens deze bewerkingen, kan dit leiden tot beschadiging van de database.


## <a name="io-guidance"></a>I/O-richtlijnen

* De beste resultaten met premium SSD's worden bereikt wanneer u uw toepassing en aanvragen parallelloopt. Premium SSD's zijn ontworpen voor scenario's waarbij de io-wachtrijdiepte groter is dan 1, zodat u weinig of geen prestatiewinst ziet voor seriële aanvragen met één thread (zelfs als ze opslagintensief zijn). Dit kan bijvoorbeeld gevolgen hebben voor de single-threaded testresultaten van prestatieanalysetools, zoals SQLIO.

* Overweeg het gebruik van [databasepaginacompressie,](https://msdn.microsoft.com/library/cc280449.aspx) omdat dit kan helpen bij het verbeteren van de prestaties van I/O-intensieve workloads. De gegevenscompressie kan echter het CPU-verbruik op de databaseserver verhogen.

* Overweeg directe bestandsinitialisatie in te schakelen om de tijd te verkorten die nodig is voor de eerste bestandstoewijzing. Als u wilt profiteren van directe bestandsinitialisatie, verleent u het SQL Server-serviceaccount (MSSQLSERVER) met SE_MANAGE_VOLUME_NAME en voegt u het toe aan het beveiligingsbeleid **Voor volumeonderhoudstaken uitvoeren.** Als u een SQL Server-platformafbeelding voor Azure gebruikt, wordt het standaardserviceaccount (NT Service\MSSQLSERVER) niet toegevoegd aan het beveiligingsbeleid **Volumeonderhoudstaken uitvoeren.** Met andere woorden, directe bestandsinitialisatie is niet ingeschakeld in een SQL Server Azure-platformafbeelding. Nadat u het SQL Server-serviceaccount hebt toegevoegd aan het beveiligingsbeleid **Volumeonderhoudstaken uitvoeren,** start u de SQL Server-service opnieuw. Er kunnen beveiligingsoverwegingen zijn voor het gebruik van deze functie. Zie [Initialisatie databasebestand](https://msdn.microsoft.com/library/ms175935.aspx)voor meer informatie .

* **autogrow** wordt beschouwd als slechts een onvoorziene voor onverwachte groei. Beheer uw gegevens niet en log de groei niet dagelijks in met autogrow. Als autogrow wordt gebruikt, wordt het bestand vooraf gekweekmet de schakelaar Grootte.

* Zorg ervoor dat **autoshrink** is uitgeschakeld om onnodige overhead te voorkomen die de prestaties negatief kan beïnvloeden.

* Alle databases verplaatsen naar gegevensschijven, inclusief systeemdatabases. Zie [Systeemdatabases verplaatsen](https://msdn.microsoft.com/library/ms345408.aspx)voor meer informatie .

* Sql Server-foutlogboeken en traceer bestandsmappen naar gegevensschijven. Dit kan in SQL Server Configuration Manager door met de rechtermuisknop op uw SQL Server-exemplaar te klikken en eigenschappen te selecteren. De instellingen voor foutlogboeken en traceerbestanden kunnen worden gewijzigd op het tabblad **Opstartparameters.** De dumpmap is opgegeven op het tabblad **Geavanceerd.** In de volgende schermafbeelding ziet u waar u de opstartparameter voor foutlogboeken moet zoeken.

    ![SQL-foutlogschermafbeelding](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Stel standaard back-up- en databasebestandslocaties in. Gebruik de aanbevelingen in dit artikel en breng de wijzigingen aan in het venster Server-eigenschappen. Zie De [standaardlocaties voor gegevens en logboekbestanden (SQL Server Management Studio) weergeven of wijzigen voor](https://msdn.microsoft.com/library/dd206993.aspx)instructies. De volgende screenshot laat zien waar deze wijzigingen aan te brengen.

    ![SQL-gegevenslogboek- en back-upbestanden](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Schakel vergrendelde pagina's in om IO en eventuele paging-activiteiten te verminderen. Zie [Pagina's vergrendelen in geheugenoptie (Windows) inschakelen](https://msdn.microsoft.com/library/ms190730.aspx)voor meer informatie.

* Als u SQL Server 2012 uitvoert, installeert u Service Pack 1 Cumulatieve update 10. Deze update bevat de oplossing voor slechte prestaties op I/O wanneer u select uitvoert in tijdelijke tabelinstructie in SQL Server 2012. Zie voor informatie dit [kennisbankartikel.](https://support.microsoft.com/kb/2958012)

* Overweeg om gegevensbestanden te comprimeren bij het overbrengen van in/uit Azure.

## <a name="feature-specific-guidance"></a>Functiespecifieke richtlijnen

Sommige implementaties kunnen extra prestatievoordelen opleveren met behulp van meer geavanceerde configuratietechnieken. In de volgende lijst worden enkele SQL Server-functies belicht die u kunnen helpen betere prestaties te bereiken:

### <a name="back-up-to-azure-storage"></a>Een back-up maken van Azure Storage
Wanneer u back-ups uitvoert voor SQL Server die in virtuele Azure-machines wordt uitgevoerd, u [SQL Server Backup gebruiken om URL te gebruiken.](https://msdn.microsoft.com/library/dn435916.aspx) Deze functie is beschikbaar vanaf SQL Server 2012 SP1 CU2 en wordt aanbevolen voor het maken van back-ups naar de aangesloten gegevensschijven. Wanneer u een back-up maakt/herstelt van/naar Azure-opslag, volgt u de aanbevelingen van [SQL Server Backup naar URL Best Practices and Troubleshooting and Restore from Backups Stored in Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). U deze back-ups ook automatiseren met [geautomatiseerde back-upvoor SQL Server in Azure Virtual Machines.](virtual-machines-windows-sql-automated-backup.md)

Vóór SQL Server 2012 u [SQL Server Backup naar Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740)gebruiken. Deze tool kan helpen om de back-updoorvoer te verhogen met behulp van meerdere back-up streep doelen.

### <a name="sql-server-data-files-in-azure"></a>SQL Server-gegevensbestanden in Azure

Deze nieuwe functie, [SQL Server Data Files in Azure,](https://msdn.microsoft.com/library/dn385720.aspx)is beschikbaar vanaf SQL Server 2014. Het uitvoeren van SQL Server met gegevensbestanden in Azure toont vergelijkbare prestatiekenmerken als het gebruik van Azure-gegevensschijven.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Failoverclusterinstantie en opslagruimten

Als u opslagruimten gebruikt, schakelt u bij het toevoegen van knooppunten aan het cluster op de pagina **Bevestiging** het selectievakje Alle **in aanmerking komende opslag toevoegen aan het cluster uit.** 

![In aanmerking komende opslag uitvinken](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Als u opslagruimten gebruikt en het selectievakje **Alle in aanmerking komende opslag toevoegen aan het cluster**niet uitschakelt, ontkoppelt Windows de virtuele schijven tijdens het clusterproces. Als gevolg hiervan worden ze niet weergegeven in Schijfbeheer of Explorer totdat de opslagruimten uit het cluster zijn verwijderd en opnieuw zijn gekoppeld met PowerShell. Opslagruimten groepeert meerdere schijven in opslaggroepen. Zie [Opslagruimten voor](/windows-server/storage/storage-spaces/overview)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Richtlijnen voor opslagconfiguratie voor SQL Server op Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) voor meer informatie over opslag en prestaties.

Zie [Beveiligingsoverwegingen voor SQL Server in Azure Virtual Machines voor](virtual-machines-windows-sql-security.md)aanbevolen procedures voor beveiliging.

Bekijk andere SQL Server Virtual Machine-artikelen op [SQL Server op Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).
