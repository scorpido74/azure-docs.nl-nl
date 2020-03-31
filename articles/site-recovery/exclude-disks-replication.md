---
title: Schijven uitsluiten van replicatie met Azure Site Recovery
description: Schijven uitsluiten van replicatie naar Azure met Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281845"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Schijven uitsluiten van noodherstel

In dit artikel wordt beschreven hoe u schijven uitsluiten van replicatie tijdens noodherstel van on-premises naar Azure met [Azure Site Recovery](site-recovery-overview.md). U schijven om een aantal redenen uitsluiten van replicatie:

- Zodat onbelangrijke gegevens die op de uitgesloten schijf worden gekarnd niet worden gerepliceerd.
- Als u de verbruikte replicatiebandbreedte of doelbronnen wilt optimaliseren.
- Als u opslag- en netwerkbronnen wilt opslaan door gegevens die u niet nodig hebt, niet te repliceren.
- Azure VM's hebben replicatielimieten voor siteherstel bereikt.


## <a name="supported-scenarios"></a>Ondersteunde scenario's

U schijven uitsluiten van replicatie zoals samengevat in de tabel.

**Azure naar Azure** | **VMware naar Azure** | **Hyper-V naar Azure** 
--- | --- | ---
Ja (powershell gebruiken) | Ja | Ja 

## <a name="exclude-limitations"></a>Beperkingen uitsluiten

**Beperking** | **Azure VM's** | **Virtuele VMware-machines** | **Virtuele Hyper-V-machines**
--- | --- | ---
**Schijftypen** | U basisschijven uitsluiten van replicatie.<br/><br/> U schijven van het besturingssysteem of dynamische schijven niet uitsluiten. Tijdelijke schijven zijn standaard uitgesloten. | U basisschijven uitsluiten van replicatie.<br/><br/> U schijven van het besturingssysteem of dynamische schijven niet uitsluiten. | U basisschijven uitsluiten van replicatie.<br/><br/> Besturingssysteemschijven kunt u niet uitsluiten. We raden u aan geen dynamische schijven uit te sluiten. Siteherstel kan niet identificeren welke VHS basis- of dynamisch is in de gast-VM. Als alle afhankelijke dynamische volumeschijven niet zijn uitgesloten, wordt de beveiligde dynamische schijf een mislukte schijf op een failover-vm en zijn de gegevens op die schijf niet toegankelijk.
**Replicerende schijf** | U een schijf die wordt gerepliceerd niet uitsluiten.<br/><br/> Replicatie voor de virtuele machine uitschakelen en opnieuw inschakelen. |  U een schijf die wordt gerepliceerd niet uitsluiten. |  U een schijf die wordt gerepliceerd niet uitsluiten.
**Mobiliteitsservice (VMware)** | Niet relevant | U schijven alleen uitsluiten op VM's waarop de Mobiliteitsservice is geïnstalleerd.<br/><br/> Dit betekent dat u de Mobility-service handmatig moet installeren op de VM's waarvoor u schijven wilt uitsluiten. U het push-installatiemechanisme niet gebruiken omdat de Mobiliteitsservice pas wordt geïnstalleerd nadat replicatie is ingeschakeld. | Niet relevant.
**Toevoegen/verwijderen** | U schijven toevoegen en verwijderen op Azure VM's met beheerde schijven. | U geen schijven toevoegen of verwijderen nadat replicatie is ingeschakeld. Schakel replicatie uit en schakel de replicatie vervolgens opnieuw toe om een schijf toe te voegen. | U geen schijven toevoegen of verwijderen nadat replicatie is ingeschakeld. Replicatie uitschakelen en vervolgens opnieuw inschakelen.
**Failover** | Als een app een schijf nodig heeft die u hebt uitgesloten, moet u de schijf na failover handmatig maken, zodat de gerepliceerde app kan worden uitgevoerd.<br/><br/> U de schijf ook maken tijdens vm-failover door Azure-automatisering te integreren in een herstelplan. | Als u een schijf uitsluit die een app nodig heeft, maakt u deze handmatig in Azure na een failover. | Als u een schijf uitsluit die een app nodig heeft, maakt u deze handmatig in Azure na een failover.
**On-premises failback-schijven die handmatig zijn gemaakt** | Niet relevant | **Windows VM's:** schijven die handmatig in Azure zijn gemaakt, worden niet teruggezet. Als u bijvoorbeeld meer dan drie schijven niet hebt en twee schijven rechtstreeks op een Azure-vm maakt, worden alleen de drie schijven die zijn mislukt, vervolgens weer mislukt.<br/><br/> **Linux VM's:** Schijven die handmatig in Azure zijn gemaakt, zijn weer mislukt. Als u bijvoorbeeld meer dan drie schijven niet hebt en twee schijven op een Azure-vm maakt, worden alle vijf weer mislukt. U kunt geen schijven die handmatig zijn gemaakt, uitsluiten van failback. | Schijven die handmatig in Azure zijn gemaakt, worden niet teruggezet. Als u bijvoorbeeld meer dan drie schijven niet hebt en twee schijven rechtstreeks op een Azure-vm maakt, worden slechts drie schijven die zijn mislukt, teruggestuurd.
**On-premises failback-uitgesloten schijven** | Niet relevant | Als u niet teruggaat naar de oorspronkelijke machine, bevat de configuratie van de failback-VM-schijf niet de uitgesloten schijven. Schijven die zijn uitgesloten van VMware naar Azure-replicatie zijn niet beschikbaar op de failback-vm. | Wanneer failback is op de oorspronkelijke Hyper-V-locatie, blijft de failback VM-schijfconfiguratie hetzelfde als die van de oorspronkelijke bron-VM-schijf. Schijven die zijn uitgesloten van Hyper-V-site naar Azure-replicatie zijn beschikbaar op de failback-vm.



## <a name="typical-scenarios"></a>Typische scenario's

Voorbeelden van gegevensverloop die grote kandidaten zijn voor uitsluiting, zijn schrijft naar een paging-bestand (pagefile.sys) en schrijft naar het tempdb-bestand van Microsoft SQL Server. Afhankelijk van de werkbelasting en het subsysteem opslag kunnen de paging- en tempdb-bestanden een aanzienlijke hoeveelheid churn registreren. Het repliceren van dit type gegevens naar Azure is resource-intensief.

- Als u de replicatie voor een virtuele machine wilt optimaliseren met één virtuele schijf die zowel het besturingssysteem als het paging-bestand bevat, u het als nog even volhouden:
    1. Splits de virtuele schijf in twee virtuele schijven. De ene virtuele schijf bevat het besturingssysteem en de andere het wisselbestand.
    2. Sluit de wisselbestandschijf uit van replicatie.

- Als u de replicatie wilt optimaliseren voor een schijf die zowel het Microsoft SQL Server tempdb-bestand als het systeemdatabasebestand bevat, u het als nog even zeggen:
    1. Zet het systeemdatabasebestand en het tempdb-bestand op twee verschillende schijven.
    2. Sluit de tempdb-schijf uit van replicatie.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Voorbeeld 1: de tempdb-schijf in SQL Server uitsluiten

Laten we eens kijken hoe om te gaan met schijfuitsluiting, failover en failover voor een bron SQL Server Windows VM - **SalesDB***, waarvoor we tempdb willen uitsluiten. 

### <a name="exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

We hebben deze schijven op de bron Windows VM SalesDB.

**Schijfnaam** | **Gastosschijf** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | --- | ---
DB-Disk0-OS | Schijf0 | C:\ | Schijf van het besturingssysteem.
DB-Disk1| Disk1 | D:\ | SQL-systeemdatabase en gebruikersdatabase1.
DB-Disk2 (de schijf is uitgesloten van beveiliging) | Disk2 | E:\ | Tijdelijke bestanden.
DB-Disk3 (de schijf is uitgesloten van beveiliging) | Disk3 | F:\ | SQL tempdb-database.<br/><br/> Mappad - F:\MSSQL\Data\. Noteer het mappad voordat het mislukt.
DB-Disk4 | Disk4 |G:\ | Gebruikersdatabase2

1. We maken replicatie voor de SalesDB VM mogelijk.
2. We sluiten Disk2 en Disk3 uit van replicatie omdat het verloop van gegevens op die schijven tijdelijk is. 


### <a name="handle-disks-during-failover"></a>Schijven verwerken tijdens failover

Aangezien schijven niet worden gerepliceerd, zijn deze schijven niet aanwezig op de Azure VM die is gemaakt na een failover. De Azure VM heeft de schijven samengevat in deze tabel.

**Gastosschijf** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | ---
Schijf0 | C:\ | Schijf van het besturingssysteem.
Disk1 | E:\ | Tijdelijke opslag<br/><br/>Azure voegt deze schijf toe. Omdat Disk2 en Disk3 zijn uitgesloten van replicatie, is E: de eerste stationsletter van de beschikbare lijst. Azure wijst E: toe aan het tijdelijke opslagvolume. Andere stationsletters voor gerepliceerde schijven blijven hetzelfde.
Disk2 | D:\ | SQL-systeemdatabase en gebruikersdatabase 1
Disk3 | G:\ | Gebruikersdatabase2

In ons voorbeeld, aangezien Disk3, de SQL tempdb-schijf, is uitgesloten van replicatie en niet beschikbaar is op de Azure VM, is de SQL-service in een gestopte status en heeft deze het F:\MSSQL\Data-pad nodig. U dit pad op een aantal manieren maken: 

- Voeg een nieuwe schijf toe na een failover en wijs het tempdb-mappad toe.
- Gebruik een bestaande tijdelijke opslagschijf voor het tempdb-mappad.

#### <a name="add-a-new-disk-after-failover"></a>Een nieuwe schijf toevoegen na failover

1. Noteer de paden van SQL-tempdb.mdf en tempdb.ldf voordat de failover wordt uitgevoerd.
2. Voeg vanuit de Azure-portal een nieuwe schijf toe aan de failover Azure VM. De schijf moet dezelfde grootte (of groter) hebben als de bron SQL tempdb-schijf (Disk3).
3. Meld u aan bij de Azure VM.
4. Initialiseer en formatteer de nieuw toegevoegde schijf via de schijfbeheerconsole (diskmgmt.msc). 
5. Dezelfde stationsletter toewijzen die is gebruikt door de SQL tempdb-schijf (F:)
6. Maak een tempdb-map op de F:-schijf (F:\MSSQL\Data).
7. Start de SQL-service via de serviceconsole.

#### <a name="use-an-existing-temporary-storage-disk"></a>Een bestaande tijdelijke opslagschijf gebruiken 

1. Open een opdrachtprompt.
2. Voer SQL Server in de herstelmodus uit via de opdrachtprompt.

        Net start MSSQLSERVER /f / T3608

3. Voer de volgende sqlcmd uit om het tempdb-pad in een nieuw pad te wijzigen.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Stop de Microsoft SQL Server-service.

        Net stop MSSQLSERVER
5. Start de Microsoft SQL Server-service.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VM's: schijven tijdens failback naar oorspronkelijke locatie

Laten we nu eens kijken hoe u schijven op VMware VM's verwerken wanneer u niet terugkeert naar uw oorspronkelijke on-premises locatie.

- **Schijven die zijn gemaakt in Azure:** Aangezien in ons voorbeeld een Windows-vm wordt gebruikt, worden schijven die u handmatig in Azure maakt, niet teruggerepliceerd naar uw site wanneer u een VM niet terugbeschermt of opnieuw beschermt.
- **Tijdelijke opslagschijf in Azure:** de tijdelijke opslagschijf wordt niet gerepliceerd naar on-premises hosts.
- **Uitgesloten schijven:** schijven die zijn uitgesloten van VMware naar Azure-replicatie zijn niet beschikbaar op de on-premises VM na failback.

Voordat u de VMware VM's niet terughaalt naar de oorspronkelijke locatie, zijn de Azure VM-schijfinstellingen als volgt.

**Gastosschijf** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | ---
Schijf0 | C:\ | Schijf van het besturingssysteem.
Disk1 | E:\ | Tijdelijke opslag.
Disk2 | D:\ | SQL-systeemdatabase en gebruikersdatabase1.
Disk3 | G:\ | Gebruikersdatabase2.

Na failback heeft de Vm VMware op de oorspronkelijke locatie de schijven samengevat in de tabel.

**Gastosschijf** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | ---
Schijf0 | C:\ | Schijf van het besturingssysteem.
Disk1 | D:\ | SQL-systeemdatabase en gebruikersdatabase1.
Disk2 | G:\ | Gebruikersdatabase2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V VM's: schijven tijdens failback naar oorspronkelijke locatie

Laten we nu eens kijken hoe u schijven op Hyper-V VM's verwerken wanneer u niet terugkeert naar uw oorspronkelijke on-premises locatie.

- **Schijven die zijn gemaakt in Azure:** schijven die u handmatig in Azure maakt, worden niet teruggerepliceerd naar uw site wanneer u een vm niet meer beschermt of een vm opnieuw beschermt.
- **Tijdelijke opslagschijf in Azure:** de tijdelijke opslagschijf wordt niet gerepliceerd naar on-premises hosts.
- **Uitgesloten schijven**: Na failback is de VM-schijfconfiguratie hetzelfde als de oorspronkelijke VM-schijfconfiguratie. Schijven die zijn uitgesloten van replicatie van Hyper-V naar Azure zijn beschikbaar op de failback-vm.

Voordat u de Hyper-V VM's niet terugnaar de oorspronkelijke locatie haalt, zijn de Azure VM-schijfinstellingen als volgt.

**Gastosschijf** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | ---
Schijf0 | C:\ | Schijf van het besturingssysteem.
Disk1 | E:\ | Tijdelijke opslag.
Disk2 | D:\ | SQL-systeemdatabase en gebruikersdatabase1.
Disk3 | G:\ | Gebruikersdatabase2.

Na geplande failover (failover) van Azure naar on-premises Hyper-V, heeft de Hyper-V VM op de oorspronkelijke locatie de schijven samengevat in de tabel.

**Schijfnaam** | **Gastbesturingssysteem schijf#** | **Stationsletter** | **Schijfgegevenstype**
 --- | --- | --- | ---
DB-Disk0-OS | Schijf0 |   C:\ | Schijf van het besturingssysteem.
DB-Disk1 | Disk1 | D:\ | SQL-systeemdatabase en gebruikersdatabase1.
DB-Disk2 (uitgesloten schijf) | Disk2 | E:\ | Tijdelijke bestanden.
DB-Disk3 (uitgesloten schijf) | Disk3 | F:\ | SQL tempdb-database<br/><br/> Mappad (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | Gebruikersdatabase2


## <a name="example-2-exclude-the-paging-file-disk"></a>Voorbeeld 2: De paging-bestandsschijf uitsluiten

Laten we eens kijken hoe om te gaan met schijfuitsluiting, failover en failover voor een bron Windows VM, waarvoor we de pagefile.sys-bestandsschijf op zowel het D-station als een alternatief station willen uitsluiten.


### <a name="paging-file-on-the-d-drive"></a>Paging-bestand op het D-station

We hebben deze schijven op de bron VM.

**Schijfnaam** | **Gastosschijf** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | --- | ---
DB-Disk0-OS | Schijf0 | C:\ | Besturingssysteemschijf
DB-Disk1 (Uitsluiten van replicatie) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

Onze paging-bestandsinstellingen op de bron-VM zijn als volgt:

![Instellingen voor het wisselbestand op de virtuele bronmachine](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. We maken replicatie voor de VM mogelijk.
2. We sluiten DB-Disk1 uit van replicatie.

#### <a name="disks-after-failover"></a>Schijven na failover

Na failover heeft de Azure VM de schijven samengevat in de tabel.

**Schijfnaam** | **Gastbesturingssysteemschijf#** | **Stationsletter** | **Gegevenstype op de schijf**
--- | --- | --- | ---
DB-Disk0-OS | Schijf0 | C:\ | Besturingssysteemschijf
DB-Disk1 | Disk1 | D:\ | Tijdelijke opslag/pagefile.sys <br/><br/> Omdat DB-Disk1 (D:) was uitgesloten, D: is de eerste drive letter van de beschikbare lijst.<br/><br/> Azure wijst D: toe aan het tijdelijke opslagvolume.<br/><br/> Omdat D: beschikbaar is, blijft de vm-paging-bestandsinstelling hetzelfde).
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

Onze paging-bestandsinstellingen op de Azure VM zijn als volgt:

![Instellingen voor het wisselbestand op de virtuele Azure-machine](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Paging bestand op een ander station (niet D:)

Laten we eens kijken naar voorbeeld waarin de paging bestand is niet op de D-station.  

We hebben deze schijven op de bron VM.

**Schijfnaam** | **Gastosschijf** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | --- | ---
DB-Disk0-OS | Schijf0 | C:\ | Besturingssysteemschijf
DB-Disk1 (Uitsluiten van replicatie) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

Onze paging-bestandsinstellingen op de on-premises VM zijn als volgt:

![Instellingen voor het wisselbestand op de on-premises virtuele machine](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. We maken replicatie voor de VM mogelijk.
2. We sluiten DB-Disk1 uit van replicatie.

#### <a name="disks-after-failover"></a>Schijven na failover

Na failover heeft de Azure VM de schijven samengevat in de tabel.

**Schijfnaam** | **Gastbesturingssysteem schijf#** | **Stationsletter** | **Schijfgegevenstype**
--- | --- | --- | ---
DB-Disk0-OS | Schijf0  |C:\ | Besturingssysteemschijf
DB-Disk1 | Disk1 | D:\ | Tijdelijke opslag<br/><br/> Aangezien D: de eerste letter in de lijst is, wijst Azure de letter D: toe aan het tijdelijke opslagvolume.<br/><br/> De stationsletter is voor alle gerepliceerde schijven hetzelfde.<br/><br/> Omdat de G: schijf niet beschikbaar is, gebruikt het systeem de C: schijf voor het paging-bestand.
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

Onze paging-bestandsinstellingen op de Azure VM zijn als volgt:

![Instellingen voor het wisselbestand op de virtuele Azure-machine](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over richtlijnen voor de tijdelijke opslagschijf:
    - [Meer informatie over](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) het gebruik van SSD's in Azure VM's om SQL Server TempDB- en BufferPool-extensies op te slaan
    - [Bekijk](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) aanbevolen procedures voor de prestaties van SQL Server in Azure VM's.
- Wanneer uw implementatie actief is, kunt u [hier](failover-failback-overview.md) meer lezen over de verschillende soorten failovers.

