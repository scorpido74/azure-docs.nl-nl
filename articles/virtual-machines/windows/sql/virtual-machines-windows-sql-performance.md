---
title: Richt lijnen voor prestaties voor SQL Server in azure | Microsoft Docs
description: Biedt richt lijnen voor het optimaliseren van SQL Server prestaties in Microsoft Azure Vm's.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6a386096d8a94c240e9a00457d87d04254e02920
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102011"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Richt lijnen voor prestaties voor SQL Server in azure Virtual Machines

## <a name="overview"></a>Overzicht

Dit artikel bevat richt lijnen voor het optimaliseren van SQL Server prestaties in Microsoft Azure virtuele machine. Bij het uitvoeren van SQL Server in azure Virtual Machines raden we u aan dezelfde opties voor het afstemmen van de prestaties van de data base te blijven gebruiken die van toepassing zijn op SQL Server in een on-premises server omgeving. De prestaties van een relationele database in een openbare cloud zijn echter afhankelijk van vele factoren, zoals de grootte van een virtuele machine en de configuratie van de gegevensschijven.

[SQL Server installatie kopieën die zijn ingericht in de Azure Portal](quickstart-sql-vm-create-portal.md) algemene aanbevolen procedures voor opslag configuratie (Zie [opslag configuratie voor SQL Server vm's](virtual-machines-windows-sql-server-storage-configuration.md)) voor meer informatie over het configureren van opslag. Na het inrichten kunt u overwegen om andere optimalisaties toe te passen die in dit artikel worden besproken. Baseer uw keuzes op uw werk belasting en controleer door testen.

> [!TIP]
> Er is doorgaans een afweging tussen het optimaliseren van kosten en het optimaliseren van de prestaties. Dit artikel is gericht op het ophalen van de *beste* prestaties voor SQL Server op Azure-vm's. Als uw werk belasting minder veeleisend is, is het mogelijk dat u niet elke optimalisatie nodig hebt die hieronder wordt vermeld. Houd rekening met de prestatie behoeften, kosten en werkbelasting patronen wanneer u deze aanbevelingen evalueert.

## <a name="quick-check-list"></a>Lijst met snelle controles

Hier volgt een snelle controle lijst voor de optimale prestaties van SQL Server op Azure Virtual Machines:

| Onderwerp | Optimalisaties |
| --- | --- |
| [VM-grootte](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) of hoger voor SQL Enter prise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) of hoger voor SQL Standard en Web Editions. |
| [Storage](#storage-guidance) | - [Premium-ssd's](../disks-types.md)gebruiken. Standaard opslag wordt alleen aanbevolen voor dev/test.<br/><br/> -Houd het [opslag account](../../../storage/common/storage-create-storage-account.md) en SQL Server virtuele machine in dezelfde regio.<br/><br/> * Schakel Azure [geo-redundante opslag](../../../storage/common/storage-redundancy.md) (geo-replicatie) uit voor het opslag account. |
| [Schijven](#disks-guidance) | -Gebruik Mini maal 2 [P30-schijven](../disks-types.md#premium-ssd) (1 voor logboek bestanden en 1 voor gegevens bestanden, waaronder TempDB). Voor werk belastingen waarvoor ~ 50.000 IOPS is vereist, kunt u overwegen een Ultra-SSD te gebruiken. <br/><br/> -Vermijd het gebruik van besturings systeem of tijdelijke schijven voor database opslag of logboek registratie.<br/><br/> -Lees cache inschakelen op de schijven die als host dienen voor de gegevens bestanden en TempDB-gegevens bestanden.<br/><br/> -Schakel caching niet in op een of meer schijven die als host optreden voor het logboek bestand.  **Belang rijk**: Stop de SQL Server-service wanneer u de cache-instellingen voor een Azure VM-schijf wijzigt.<br/><br/> -Meerdere Azure-gegevens schijven Stripe om een grotere IO-door voer te krijgen.<br/><br/> -Indeling met gedocumenteerde toewijzings grootten. <br/><br/> -Sla TempDB op het lokale SSD `D:\` -station op voor essentiële SQL Server workloads (nadat u de juiste VM-grootte hebt gekozen). Meer informatie in de blog [met ssd's voor het opslaan van tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).  |
| [I/O](#io-guidance) |-Database pagina compressie inschakelen.<br/><br/> -Schakel de initialisatie van Instant file in voor gegevens bestanden.<br/><br/> -Beperk de automatische groei van de data base.<br/><br/> -Autoverkleinen van de data base uitschakelen.<br/><br/> -Alle data bases verplaatsen naar gegevens schijven, inclusief systeem databases.<br/><br/> -Verplaats SQL Server fouten logboek en traceer bestands mappen naar gegevens schijven.<br/><br/> -Standaard back-up en bestands locaties voor de data base instellen.<br/><br/> -Vergrendelde pagina's inschakelen.<br/><br/> -SQL Server prestatie verbeteringen Toep assen. |
| [Functie-specifiek](#feature-specific-guidance) | -Maak rechtstreeks een back-up naar Blob Storage. |

Raadpleeg de details en richt lijnen in de volgende secties voor meer informatie over *hoe* en *Waarom* u deze optimalisaties wilt maken.

## <a name="vm-size-guidance"></a>Richt lijnen voor VM-grootte

Voor prestatie gevoelige toepassingen is het raadzaam om de volgende grootten voor [virtuele machines](../sizes.md)te gebruiken:

* **SQL Server Enterprise-editie**: DS3_v2 of hoger
* **SQL Server Standard-en Web-edities**: DS2_v2 of hoger

[DSv2-serie](../sizes-general.md#dsv2-series) Vm's ondersteunen Premium-opslag, die wordt aanbevolen voor de beste prestaties. De grootten die hier worden aanbevolen, zijn basis lijnen, maar de werkelijke computer grootte die u selecteert, is afhankelijk van de vereisten van uw werk belasting. Vm's uit de DSv2-serie zijn algemene Vm's die geschikt zijn voor diverse werk belastingen, terwijl andere grootten van machines zijn geoptimaliseerd voor specifieke werkbelasting typen. De [M-serie](../sizes-memory.md#m-series) biedt bijvoorbeeld het hoogste aantal vCPU en het geheugen voor de grootste SQL Server werk belastingen. De [GS-serie](../sizes-previous-gen.md#gs-series) en de [DSv2-serie 11-15](../sizes-memory.md#dsv2-series-11-15) zijn geoptimaliseerd voor grote geheugen vereisten. Beide reeksen zijn ook beschikbaar in [beperkte core](../../windows/constrained-vcpu.md)-grootten, waardoor geld wordt bespaard op workloads met een lagere reken capaciteit. De computers uit de [LS-serie](../sizes-storage.md) zijn geoptimaliseerd voor de hoge doorvoer capaciteit van de schijf en IO. Het is belang rijk om uw specifieke SQL Server werk belasting te overwegen en dit toe te passen op uw selectie van een VM-serie en-grootte.

## <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

DS-serie (samen met Vm's uit de DSv2-serie en GS-serie) bieden ondersteuning voor [Premium ssd's](../disks-types.md). Premium Ssd's wordt aanbevolen voor alle productie werkbelastingen.

> [!WARNING]
> Standaard Hdd's en Ssd's hebben verschillende latenties en band breedte en worden alleen aanbevolen voor dev/test-workloads. Werk belastingen voor productie moeten premium-Ssd's gebruiken.

Daarnaast raden wij u aan uw Azure Storage-account te maken in hetzelfde Data Center als uw SQL Server virtuele machines om de overdrachts vertraging te verminderen. Wanneer u een opslag account maakt, moet u geo-replicatie uitschakelen als consistente schrijf volgorde op meerdere schijven niet gegarandeerd. In plaats daarvan kunt u overwegen een SQL Server nood herstel technologie te configureren tussen twee Azure-data centers. Zie voor meer informatie [hoge Beschik baarheid en herstel na nood gevallen voor SQL Server in Azure virtual machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Richt lijnen voor schijven

Er zijn drie hoofd typen schijven op een virtuele Azure-machine:

* **Besturingssysteem schijf**: Wanneer u een virtuele machine van Azure maakt, wordt er ten minste één schijf (gelabeld als station **C** ) aan de VM gekoppeld voor de schijf van het besturings systeem. Deze schijf is een VHD die is opgeslagen als een pagina-Blob in de opslag.
* **Tijdelijke schijf**: Virtuele Azure-machines bevatten een andere schijf, de tijdelijke schijf (met de naam **D**: station). Dit is een schijf op het knoop punt dat kan worden gebruikt voor Scratch ruimte.
* **Gegevens schijven**: U kunt ook extra schijven als gegevens schijven aan uw virtuele machine koppelen. deze worden opgeslagen als pagina-blobs in de opslag ruimte.

In de volgende secties worden aanbevelingen beschreven voor het gebruik van deze verschillende schijven.

### <a name="operating-system-disk"></a>Besturingssysteemschijf

Een besturingssysteem schijf is een VHD die u kunt opstarten en koppelen als een actieve versie van een besturings systeem en wordt aangeduid als station **C** .

Het standaard beleid voor opslaan in cache op de besturingssysteem schijf is **lezen/schrijven**. Voor prestatie gevoelige toepassingen raden we u aan om gegevens schijven te gebruiken in plaats van de besturingssysteem schijf. Zie de sectie op gegevens schijven hieronder.

### <a name="temporary-disk"></a>Tijdelijke schijf

Het tijdelijke opslag station, met het label **D**: station, wordt niet persistent gemaakt in Azure Blob-opslag. Sla uw gebruikers database bestanden of gebruikers transactie logboek bestanden niet op in het station **D**:.

Voor Vm's uit de D-serie, dv2-serie en G-serie is het tijdelijke station op deze Vm's gebaseerd op SSD. Als uw werk belasting intensief gebruik maakt van TempDB (zoals tijdelijke objecten of complexe samen voegingen), kan TempDB op het **D** -station worden opgeslagen en kan dit leiden tot een hogere TempDB-door Voer en een lagere TempDB-latentie. Zie voor een voorbeeld scenario de TempDB-bespreking in het volgende blog bericht: [Richt lijnen voor opslag configuratie voor SQL Server op Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

Voor virtuele machines die ondersteuning bieden voor Premium Ssd's (DS-serie, DSv2-Series en GS-serie), raden we u aan TempDB op te slaan op een schijf die ondersteuning biedt voor Premium Ssd's met lees cache ingeschakeld.

Er is één uitzonde ring op deze aanbeveling: _als het gebruik van tempdb write-intensief is, kunt u betere prestaties krijgen door TempDB op te slaan op het lokale **D** -station, dat ook op SSD is gebaseerd op deze computer grootten._ Raadpleeg de blog [using ssd's with TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) voor meer informatie. 

### <a name="data-disks"></a>Gegevensschijven

* **Gegevens schijven gebruiken voor gegevens en logboek bestanden**: Als u schijf striping niet gebruikt, gebruikt u twee Premium SSD P30-schijven waarbij de ene schijf de logboek bestanden bevat en de andere de gegevens en TempDB bevatten (met uitzonde ring van essentiële en schrijf bare werk belastingen zoals hierboven vermeld). Elk Premium SSD biedt een aantal IOPs en band breedte (MB/s), afhankelijk van de grootte, zoals in het artikel wordt weer gegeven, [selecteert u een schijf type](../disks-types.md). Als u een schijf Stripe-techniek gebruikt, zoals opslag ruimten, verkrijgt u optimale prestaties door twee groepen te hebben, één voor de logboek bestanden en de andere voor de gegevens bestanden. Als u echter van plan bent SQL Server failover cluster instances (FCI) te gebruiken, moet u één groep configureren.

   > [!TIP]
   > - Zie het volgende blog bericht voor test resultaten op verschillende schijf-en werkbelasting configuraties: [Richt lijnen voor opslag configuratie voor SQL Server op Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Voor essentiële prestaties voor SQL-servers die ~ 50.000 IOPS nodig hebben, kunt u overwegen om tien P30 schijven te vervangen door een Ultra-SSD. Zie voor meer informatie het volgende blog bericht: [Essentiële prestaties met ultra-SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Wanneer u in de portal een SQL Server virtuele machine inricht, hebt u de mogelijkheid om uw opslag configuratie te bewerken. Afhankelijk van uw configuratie, configureert Azure een of meer schijven. Meerdere schijven worden gecombineerd tot één opslag groep met striping. De gegevens en logboek bestanden bevinden zich samen in deze configuratie. Zie [opslag configuratie voor SQL Server vm's](virtual-machines-windows-sql-server-storage-configuration.md)voor meer informatie.

* **Schijf striping**: U kunt extra gegevens schijven toevoegen en schijf striping gebruiken voor meer door voer. Als u het aantal gegevens schijven wilt bepalen, moet u het aantal IOPS en band breedte analyseren dat vereist is voor uw logboek bestand (en) en voor uw gegevens en TempDB-bestand (en). U ziet dat verschillende VM-grootten verschillende limieten hebben voor het aantal IOPs en bandbreedte dat wordt ondersteund. Zie de tabellen op IOPS per [VM-grootte](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Gebruik de volgende richt lijnen:

  * Voor Windows 8/Windows Server 2012 of hoger gebruikt u [opslag ruimten](https://technet.microsoft.com/library/hh831739.aspx) met de volgende richt lijnen:

      1. Stel de Interleave (Stripe-grootte) in op 64 KB (65536 bytes) voor OLTP-workloads en 256 KB (262144 bytes) voor werk belastingen voor gegevens opslag om prestaties te voor komen door een onjuiste uitlijning van de partitie. Dit moet worden ingesteld met Power shell.
      2. Aantal kolommen instellen = aantal fysieke schijven. Gebruik Power shell bij het configureren van meer dan 8 schijven (niet Serverbeheer gebruikers interface). 

    Met de volgende Power shell wordt bijvoorbeeld een nieuwe opslag groep gemaakt met de Interleave-grootte tot 64 KB en het aantal kolommen op 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Voor Windows 2008 R2 of eerder kunt u dynamische schijven (door het besturings systeem gestripde volumes) gebruiken en de Stripe-grootte altijd 64 KB. Houd er rekening mee dat deze optie is afgeschaft vanaf Windows 8/Windows Server 2012. Zie de ondersteunings verklaring op [Virtual Disk service overgang naar Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)(Engelstalig) voor meer informatie.

  * Als u [opslagruimten direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) gebruikt met [SQL Server failoverclusterknooppunten](virtual-machines-windows-portal-sql-create-failover-cluster.md), moet u één groep configureren. Hoewel er verschillende volumes kunnen worden gemaakt op die ene groep, delen ze allemaal dezelfde kenmerken, zoals hetzelfde cache beleid.

  * Bepaal het aantal schijven dat is gekoppeld aan uw opslag groep op basis van de belasting verwachtingen. Houd er wel bij dat verschillende VM-grootten verschillende aantallen gekoppelde gegevens schijven toestaan. Zie [grootten voor virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.

  * Als u geen Premium-Ssd's (dev/test-scenario's) gebruikt, is het raadzaam om het maximum aantal gegevens schijven toe te voegen dat wordt ondersteund door de [VM-grootte](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en schijf striping te gebruiken.

* **Cache beleid**: Houd rekening met de volgende aanbevelingen voor cache beleid, afhankelijk van uw opslag configuratie.

  * Als u afzonderlijke schijven voor gegevens-en logboek bestanden gebruikt, schakelt u de optie Lees cache opslaan in op de gegevens schijven die als host fungeren voor uw gegevens bestanden en TempDB-gegevens bestanden. Dit kan leiden tot een aanzienlijk prestatie voordeel. Schakel caching niet in op de schijf die het logboek bestand bewaart omdat dit een kleine afname van de prestaties veroorzaakt.

  * Als u schijf striping in één opslag groep gebruikt, kunnen de meeste werk belastingen gebruikmaken van de Lees cache. Als u afzonderlijke opslag groepen voor de logboek-en gegevens bestanden hebt, schakelt u alleen lees cache in voor de opslag groep voor de gegevens bestanden. In bepaalde zware schrijf workloads kunnen betere prestaties worden behaald zonder caching. Dit kan alleen worden bepaald door middel van testen.

  * De bovenstaande aanbevelingen zijn van toepassing op Premium-Ssd's. Als u geen gebruik maakt van Premium Ssd's, moet u geen caching inschakelen op gegevens schijven.

  * Raadpleeg de volgende artikelen voor instructies over het configureren van schijf cache gebruik. Zie voor het klassieke (ASM)-implementatie model: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) en [set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Zie voor het Azure Resource Manager-implementatie model: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) en [set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Stop de SQL Server-service bij het wijzigen van de cache-instelling van Azure VM-schijven om te voor komen dat de data base wordt beschadigd.

* **NTFS-Allocation Unit Size**: Bij het format teren van de gegevens schijf wordt aanbevolen een 64-KB-Allocation Unit Size te gebruiken voor gegevens-en logboek bestanden en TempDB.

* **Aanbevolen procedures voor schijf beheer**: Wanneer u een gegevens schijf verwijdert of het cache type wijzigt, stopt u de SQL Server-service tijdens de wijziging. Wanneer de cache-instellingen worden gewijzigd op de besturingssysteem schijf, stopt Azure de virtuele machine, wijzigt het cache type en start de VM opnieuw op. Wanneer de cache-instellingen van een gegevens schijf zijn gewijzigd, wordt de VM niet gestopt, maar wordt de gegevens schijf losgekoppeld van de virtuele machine tijdens de wijziging en vervolgens opnieuw gekoppeld.

  > [!WARNING]
  > Als de SQL Server-service niet kan worden gestopt tijdens deze bewerkingen, kan de data base beschadigd raken.


## <a name="io-guidance"></a>I/O-richt lijnen

* De beste resultaten met Premium-Ssd's worden behaald wanneer u uw toepassing en aanvragen parallelliseren. Premium-Ssd's zijn ontworpen voor scenario's waarbij de IO-wachtrij diepte groter is dan 1, zodat u weinig of geen prestatie verbeteringen zult zien voor seriële aanvragen met één thread (zelfs als ze intensief zijn voor opslag). Dit kan bijvoorbeeld van invloed zijn op de test resultaten met één thread van prestatie analyse Programma's, zoals SQLIO.

* U kunt [database pagina compressie](https://msdn.microsoft.com/library/cc280449.aspx) gebruiken om de prestaties van I/O-intensieve workloads te verbeteren. De gegevens compressie kan echter het CPU-verbruik op de database server verhogen.

* U kunt de initialisatie van het directe bestand inschakelen om de benodigde tijd voor de eerste bestands toewijzing te verminderen. Als u gebruik wilt maken van de initialisatie van expres bestanden, verleent u het SQL Server (MSSQLserver)-service account met SE_MANAGE_VOLUME_NAME en voegt u het toe aan het beveiligings beleid **volume onderhouds taken uitvoeren** . Als u een SQL Server platform installatie kopie voor Azure gebruikt, wordt het standaard service account (NT Service\MSSQLSERVER) niet toegevoegd aan het beveiligings beleid **volume onderhouds taken uitvoeren** . Met andere woorden, de initialisatie van chat bestanden is niet ingeschakeld in een SQL Server Azure-platform installatie kopie. Nadat u het SQL Server-service account hebt toegevoegd aan het beveiligings beleid **volume onderhouds taken uitvoeren** , start u de SQL Server-service opnieuw. Er kunnen beveiligings overwegingen zijn voor het gebruik van deze functie. Zie [initialisatie van database bestanden](https://msdn.microsoft.com/library/ms175935.aspx)voor meer informatie.

* **autogrow** wordt gezien als slechts een onverwachtheid voor onverwachte groei. Beheer uw gegevens niet en meld de groei per dag aan met autogrow. Als autogrow wordt gebruikt, moet u het bestand vooraf verg Roten met de schakel optie grootte.

* Zorg ervoor dat autoshrink is uitgeschakeld om onnodige overhead te voor komen die de prestaties negatief kan beïnvloeden.

* Verplaats alle data bases naar gegevens schijven, inclusief systeem databases. Zie [systeem databases verplaatsen](https://msdn.microsoft.com/library/ms345408.aspx)voor meer informatie.

* Verplaats SQL Server fouten logboek en traceer bestands mappen naar gegevens schijven. U kunt dit doen in SQL Server Configuration Manager door met de rechter muisknop op uw SQL Server-exemplaar te klikken en eigenschappen te selecteren. De instellingen voor het fouten logboek en het tracerings bestand kunnen worden gewijzigd op het tabblad **opstart parameters** . De map dump is opgegeven op het tabblad **Geavanceerd** . In de volgende scherm afbeelding ziet u waar de opstart parameter van het fouten logboek moet worden gezocht.

    ![Scherm opname van SQL-fouten logboek](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Standaard back-up en bestands locaties voor de data base instellen. Gebruik de aanbevelingen in dit artikel en breng de wijzigingen aan in het venster Server eigenschappen. Zie [de standaard locaties voor gegevens en logboek bestanden weer geven of wijzigen (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)voor instructies. De volgende scherm afbeelding laat zien hoe u deze wijzigingen aanbrengt.

    ![SQL-gegevens logboek en back-upbestanden](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Schakel vergrendelde pagina's in om de i/o en eventuele wissel activiteiten te verminderen. Zie voor meer informatie [de optie voor het vergren delen van pagina's in het geheugen (Windows) inschakelen](https://msdn.microsoft.com/library/ms190730.aspx).

* Als u SQL Server 2012 gebruikt, installeert u de cumulatieve update van Service Pack 1. Deze update bevat de oplossing voor slechte prestaties bij I/O wanneer u selecteren in een tijdelijke tabel instructie in SQL Server 2012 uitvoert. Zie dit [Knowledge Base-artikel](https://support.microsoft.com/kb/2958012)voor meer informatie.

* Overweeg het comprimeren van gegevens bestanden bij het overdragen van en naar Azure.

## <a name="feature-specific-guidance"></a>Functie-specifieke richt lijnen

Sommige implementaties kunnen extra prestatie voordelen bieden met behulp van geavanceerde configuratie technieken. De volgende lijst geeft een overzicht van enkele SQL Server functies die u kunnen helpen betere prestaties te behaalen:

### <a name="backup-to-azure-storage"></a>Back-up naar Azure Storage
Bij het uitvoeren van back-ups voor SQL Server die worden uitgevoerd op virtuele machines van Azure, kunt u [SQL Server back-up naar URL](https://msdn.microsoft.com/library/dn435916.aspx)gebruiken. Deze functie is beschikbaar vanaf SQL Server 2012 SP1 CU2 en wordt aanbevolen voor het maken van een back-up naar de gekoppelde gegevens schijven. Wanneer u een back-up maakt/terugzet naar/van Azure Storage, volgt u de aanbevelingen op [SQL Server back-up naar aanbevolen procedures voor URL en probleem oplossing en herstel van back-ups die zijn opgeslagen in azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). U kunt deze back-ups ook automatiseren met behulp [van automatische back-up voor SQL Server in Azure virtual machines](virtual-machines-windows-sql-automated-backup.md).

Vóór SQL Server 2012 kunt u [SQL Server back-up naar Azure-hulp programma](https://www.microsoft.com/download/details.aspx?id=40740)gebruiken. Met dit hulp programma kunt u de back-updoorvoer verhogen met meerdere back-upstripe-doelen.

### <a name="sql-server-data-files-in-azure"></a>SQL Server gegevens bestanden in azure

Deze nieuwe functie, [SQL Server gegevens bestanden in azure](https://msdn.microsoft.com/library/dn385720.aspx), is beschikbaar vanaf SQL Server 2014. Als SQL Server met gegevens bestanden in azure wordt uitgevoerd, worden vergelijk bare prestatie kenmerken gedemonstreerd als met behulp van Azure-gegevens schijven.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Failover-cluster exemplaar en opslag ruimten

Als u opslag ruimten gebruikt, schakelt u bij het toevoegen van knoop punten aan het cluster op de pagina **bevestiging** het selectie vakje **alle in aanmerking komende opslag toevoegen aan het cluster**uit. 

![De selectie van de in aanmerking komende opslag uitschakelen](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Als u opslag ruimten gebruikt en de optie **alle in aanmerking komende opslag toevoegen**niet uitschakelt aan het cluster, worden de virtuele schijven tijdens het cluster losgekoppeld. Als gevolg hiervan worden ze niet weer gegeven in schijf beheer of Explorer totdat de opslag ruimten uit het cluster worden verwijderd en opnieuw zijn gekoppeld met Power shell. Met opslag ruimten worden meerdere schijven in opslag groepen gegroepeerd. Zie [opslag ruimten](/windows-server/storage/storage-spaces/overview)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over opslag en prestaties [opslag configuratie richtlijnen voor SQL Server op Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Zie [beveiligings overwegingen voor SQL Server in Azure virtual machines](virtual-machines-windows-sql-security.md)voor aanbevolen beveiligings procedures.

Bekijk andere artikelen over SQL Server virtuele machines op [SQL Server in Azure virtual machines overzicht](virtual-machines-windows-sql-server-iaas-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](virtual-machines-windows-sql-server-iaas-faq.md).
