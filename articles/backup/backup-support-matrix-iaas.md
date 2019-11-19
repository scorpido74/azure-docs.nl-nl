---
title: Ondersteunings matrix voor Azure VM-back-up
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen bij het maken van back-ups van virtuele Azure-machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 8f84d7fefd2affc3a3c47227ab6f2a2d0b325f4e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172094"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Ondersteunings matrix voor Azure VM-back-up

U kunt de [Azure backup-service](backup-overview.md) gebruiken voor het maken van een back-up van on-premises machines en werk belastingen, en virtuele machines van Azure (vm's). Dit artikel bevat een overzicht van de ondersteunings instellingen en beperkingen bij het maken van back-ups van virtuele Azure-machines met Azure Backup.

Andere ondersteunings matrices:

- [Algemene ondersteunings matrix](backup-support-matrix.md) voor Azure backup
- [Ondersteunings matrix](backup-support-matrix-mabs-dpm.md) voor back-up van Azure Backup Server/System Center-Data Protection Manager (DPM)
- [Ondersteunings matrix](backup-support-matrix-mars-agent.md) voor back-up met de Microsoft Azure Recovery Services-agent (Mars)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Hier vindt u informatie over het maken van back-ups en het herstellen van virtuele Azure-machines met de Azure Backup-service.

**Scenario** | **Een back-up maken** | **Agent** |**Herstellen**
--- | --- | --- | ---
Directe back-ups van virtuele Azure-machines  | Maak een back-up van de volledige VM.  | Er is geen agent nodig op de Azure-VM. Azure Backup installeert en gebruikt een uitbrei ding van de [Azure VM-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) die op de virtuele machine wordt uitgevoerd. | Herstel als volgt:<br/><br/> - **een basis-VM maken**. Dit is handig als de VM geen speciale configuratie heeft, zoals meerdere IP-adressen.<br/><br/> **de VM-schijf - herstellen**. Herstel de schijf. Koppel deze vervolgens aan een bestaande virtuele machine of maak een nieuwe virtuele machine op basis van de schijf met behulp van Power shell.<br/><br/> - **VM-schijf vervangen**. Als er een virtuele machine bestaat en beheerde schijven (niet-versleuteld) worden gebruikt, kunt u een schijf herstellen en deze gebruiken om een bestaande schijf op de virtuele machine te vervangen.<br/><br/> - **specifieke bestanden/mappen herstellen**. U kunt bestanden/mappen herstellen vanaf een virtuele machine in plaats van vanaf de hele VM.
Directe back-ups van virtuele Azure-machines (alleen Windows)  | Maak een back-up van specifieke bestanden/mappen/volume. | Installeer de [Azure Recovery Services-agent](backup-azure-file-folder-backup-faq.md).<br/><br/> U kunt de MARS-agent naast de back-upextensie voor de Azure VM-agent uitvoeren om een back-up te maken van de virtuele machine op het niveau van het bestand of de map. | Specifieke mappen/bestanden herstellen.
Back-up van Azure VM maken op back-upserver  | Back-ups maken van bestanden/mappen/volumes; systeem status/bare metal bestanden; App-gegevens naar System Center DPM of naar Microsoft Azure Backup-Server (MABS).<br/><br/> DPM-MABS maakt vervolgens een back-up van de back-upkluis. | Installeer de DPM/MABS Protection-agent op de VM. De MARS-agent is geïnstalleerd op DPM-MABS.| Bestanden/mappen/volumes herstellen; systeem status/bare metal bestanden; App-gegevens.

Meer informatie over back-ups [met behulp van een back-upserver](backup-architecture.md#architecture-back-up-to-dpmmabs) en over [ondersteunings vereisten](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Ondersteunde back-upacties

**Actie** | **Ondersteuning**
--- | ---
Back-up inschakelen wanneer u een Windows Azure-VM maakt | Ondersteund voor: <br/><br/> -Windows Server 2019 (Data Center/Data Center core/Standard) <br/><br/> -Windows Server 2016 (Data Center/Data Center core/Standard) <br/><br/> -Windows Server 2012 R2 (Data Center/Standard) <br/><br/> -Windows Server 2008 R2 (RTM en SP1 Standard)
Back-up inschakelen bij het maken van een virtuele Linux-machine | Ondersteund voor:<br/><br/> -Ubuntu Server: 18,04, 17,10, 17,04, 16,04 (LTS), 14,04 (LTS)<br/><br/> -Red Hat: RHEL 6,7, 6,8, 6,9, 7,2, 7,3, 7,4<br/><br/> -SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> -Debian: 8, 9<br/><br/> -CentOS: 6,9, 7,3<br/><br/> -Oracle Linux: 6,7, 6,8, 6,9, 7,2, 7,3
Een back-up maken van een VM die wordt afgesloten/offline VM | Ondersteund.<br/><br/> Moment opname is alleen vastlopen consistent, niet voor app-consistent.
Back-ups maken van schijven na migratie naar Managed disks | Ondersteund.<br/><br/> De back-up blijft werken. Geen actie vereist.
Back-up van beheerde schijven maken na inschakelen van vergren deling van resource groep | Wordt niet ondersteund.<br/><br/> Azure Backup kunt de oudere herstel punten niet verwijderen, waardoor de back-ups mislukken wanneer de maximum limiet van herstel punten is bereikt.
Back-upbeleid voor een VM wijzigen | Ondersteund.<br/><br/> Er wordt een back-up van de VM gemaakt met behulp van de instellingen voor het plannen en bewaren van het nieuwe beleid. Als de Bewaar instellingen worden uitgebreid, worden de bestaande herstel punten gemarkeerd en bewaard. Als ze worden gereduceerd, worden de bestaande herstel punten verwijderd bij de volgende opschoon taak en uiteindelijk gewist.
Een back-uptaak annuleren| Ondersteund tijdens het momentopname proces.<br/><br/> Niet ondersteund wanneer de moment opname wordt overgedragen naar de kluis.
Back-ups maken van de VM naar een andere regio of een ander abonnement |Wordt niet ondersteund.
Back-ups per dag (via de Azure VM-extensie) | Eén geplande back-up per dag.<br/><br/> De service staat Maxi maal negen back-ups op aanvraag per dag toe, maar micro soft raadt u aan om Maxi maal vier dagelijkse back-ups op aanvraag te ondersteunen.
Back-ups per dag (via de MARS-agent) | Drie geplande back-ups per dag.
Back-ups per dag (via DPM/MABS) | Twee geplande back-ups per dag.
Maandelijkse/jaarlijkse back-up| Niet ondersteund bij het maken van een back-up met de Azure VM-extensie. Alleen dagelijks en wekelijks worden ondersteund.<br/><br/> U kunt het beleid zo instellen dat dagelijks/wekelijks back-ups worden bewaard voor een maandelijks/jaarlijks Bewaar periode.
Automatische aanpassing van de klok | Wordt niet ondersteund.<br/><br/> Azure Backup wordt niet automatisch aangepast aan de zomer-en winter tijd wanneer een back-up van een virtuele machine wordt gemaakt.<br/><br/>  Pas het beleid zo nodig hand matig aan.
[Beveiligings functies voor hybride back-ups](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Het uitschakelen van beveiligings functies wordt niet ondersteund.
Maak een back-up van de VM waarvan de machine tijd is gewijzigd | Wordt niet ondersteund.<br/><br/> Als de machine tijd wordt gewijzigd in een toekomstige datum-tijd na het inschakelen van de back-up voor die VM; Maar zelfs als de tijd wijziging wordt teruggedraaid, wordt een geslaagde back-up niet gegarandeerd.  

## <a name="operating-system-support-windows"></a>Ondersteuning voor besturings systeem (Windows)

De volgende tabel bevat een overzicht van de ondersteunde besturings systemen bij het maken van een back-up van virtuele Windows Azure-machines.

**Scenario** | **Ondersteuning voor besturings systeem**
--- | ---
Back-up maken met extensie van Azure VM-agent | -Windows 10-client (slechts 64 bits) <br/><br/>-Windows Server 2019 (Data Center/Data Center core/Standard) <br/><br/> -Windows Server 2016 (Data Center/Data Center core/Standard) <br/><br/> -Windows Server 2012 R2 (Data Center/Standard) <br/><br/> -Windows Server 2008 R2 (RTM en SP1 Standard)
Back-ups maken met MARS-agent | [Ondersteunde](backup-support-matrix-mars-agent.md#support-for-direct-backups) besturings systemen.
Back-up maken met DPM-MABS | Ondersteunde besturings systemen voor back-up met [MABS](backup-mabs-protection-matrix.md) en [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Azure Backup biedt geen ondersteuning voor 32-bits besturings systemen.

## <a name="support-for-linux-backup"></a>Ondersteuning voor Linux-back-up

Dit wordt ondersteund als u een back-up wilt maken van Linux-machines.

**Actie** | **Ondersteuning**
--- | ---
Back-ups van virtuele Linux-machines maken met de Azure VM-agent van Linux | Bestands consistente back-up.<br/><br/> App-consistente back-up met behulp van [aangepaste scripts](backup-azure-linux-app-consistent.md).<br/><br/> Tijdens het herstellen kunt u een nieuwe virtuele machine maken, een schijf herstellen en gebruiken om een virtuele machine te maken of een schijf herstellen en gebruiken om een schijf op een bestaande virtuele machine te vervangen. U kunt ook afzonderlijke bestanden en mappen herstellen.
Back-ups maken van virtuele Linux-machines met een MARS-agent | Wordt niet ondersteund.<br/><br/> De MARS-agent kan alleen worden geïnstalleerd op Windows-machines.
Back-ups maken van virtuele Linux-machines met DPM/MABS | Wordt niet ondersteund.

## <a name="operating-system-support-linux"></a>Ondersteuning voor besturings systeem (Linux)

Voor Azure VM Linux-back-ups ondersteunt Azure Backup de lijst met Linux- [distributies die zijn goedgekeurd door Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Houd rekening met het volgende:

- Azure Backup biedt geen ondersteuning voor kern besturingssysteem Linux.
- Azure Backup biedt geen ondersteuning voor 32-bits besturings systemen.
- Andere uw eigen Linux-distributies kunnen werken zolang de [Azure VM-agent voor Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) beschikbaar is op de virtuele machine en zolang python wordt ondersteund.
- Azure Backup biedt geen ondersteuning voor een door een proxy geconfigureerde Linux-VM als er geen python-versie 2,7 is geïnstalleerd.

## <a name="backup-frequency-and-retention"></a>Back-upfrequentie en retentie

**Instelling** | **Limieten**
--- | ---
Maximum aantal herstel punten per beveiligd exemplaar (computer/werk belasting) | 9999.
Maximale verloop tijd voor een herstel punt | Geen limiet.
Maximale back-upfrequentie naar kluis (Azure VM-extensie) | Eenmaal per dag.
Maximale back-upfrequentie voor de kluis (MARS-agent) | Drie back-ups per dag.
Maximale back-upfrequentie naar DPM/MABS | Om de 15 minuten voor SQL Server.<br/><br/> Eenmaal per uur voor andere werk belastingen.
Bewaarperiode van herstelpunt | Dagelijks, wekelijks, maandelijks en jaarlijks.
Maximale bewaarperiode | Afhankelijk van back-upfrequentie.
Herstelpunten op DPM-/MABS-schijf | 64 voor bestands servers en 448 voor app-servers.<br/><br/> Tapeherstelpunten zijn onbeperkt voor on-premises DPM.

## <a name="supported-restore-methods"></a>Ondersteunde herstel methoden

**Herstel methode** | **Details**
--- | ---
Een nieuwe VM maakt | U kunt tijdens het herstel proces een virtuele machine maken. <br/><br/> Met deze optie wordt een virtuele standaard-VM actief. U kunt de VM-naam, de resource groep, het virtuele netwerk, het subnet en de opslag opgeven.  
Een schijf terugzetten | U kunt een schijf herstellen en gebruiken om een virtuele machine te maken.<br/><br/> Wanneer u deze optie selecteert Azure Backup, worden gegevens uit de kluis gekopieerd naar een opslag account dat u selecteert. Met de herstel taak wordt een sjabloon gegenereerd. U kunt deze sjabloon downloaden, gebruiken om aangepaste VM-instellingen op te geven en een virtuele machine te maken.<br/><br/> Met deze optie kunt u meer instellingen opgeven die de vorige optie heeft voor het maken van een virtuele machine.<br/><br/>
Een bestaande schijf vervangen | U kunt een schijf herstellen en vervolgens de herstelde schijf gebruiken om een schijf te vervangen die zich op een virtuele machine bevindt.
Bestanden herstellen | U kunt bestanden herstellen vanaf een geselecteerd herstel punt. U downloadt een script om de VM-schijf van het herstel punt te koppelen. Vervolgens bladert u door de schijf volumes om te zoeken naar de bestanden/mappen die u wilt herstellen en ontkoppelen van de schijf wanneer u klaar bent.

## <a name="support-for-file-level-restore"></a>Ondersteuning voor herstel op bestands niveau

**Herstellen** | **Ondersteund**
--- | ---
Bestanden herstellen over besturings systemen | U kunt bestanden herstellen op elke computer die hetzelfde (of compatibel) besturings systeem heeft als de back-up van de virtuele machine. Zie de [tabel met compatibele besturings systemen](backup-azure-restore-files-from-vm.md#system-requirements).
Bestanden herstellen op klassieke Vm's | Wordt niet ondersteund.
Bestanden herstellen van versleutelde Vm's | Wordt niet ondersteund.
Bestanden herstellen van opslag accounts met netwerk beperkingen | Wordt niet ondersteund.
Bestanden op Vm's terugzetten met behulp van Windows-opslag ruimten | Terugzetten wordt niet ondersteund op dezelfde VM.<br/><br/> In plaats daarvan herstelt u de bestanden op een compatibele VM.
Bestanden herstellen op een Linux-VM met behulp van LVM/RAID-matrices | Terugzetten wordt niet ondersteund op dezelfde VM.<br/><br/> Herstel op een compatibele VM.
Bestanden herstellen met speciale netwerk instellingen | Terugzetten wordt niet ondersteund op dezelfde VM. <br/><br/> Herstel op een compatibele VM.

## <a name="support-for-vm-management"></a>Ondersteuning voor VM-beheer

De volgende tabel bevat een overzicht van de ondersteuning voor back-ups tijdens taken voor VM-beheer, zoals het toevoegen of vervangen van VM-schijven.

**Herstellen** | **Ondersteund**
--- | ---
Herstellen in het abonnement/de regio/zone. | Wordt niet ondersteund.
Herstellen naar een bestaande virtuele machine | Gebruik de optie schijf vervangen.
Schijf herstellen met een opslag account dat is ingeschakeld voor Azure Storage-service versleuteling (SSE) | Wordt niet ondersteund.<br/><br/> Herstel naar een account waarvoor geen SSE is ingeschakeld.
Herstellen naar gemengde opslag accounts |Wordt niet ondersteund.<br/><br/> Op basis van het type opslag account zijn alle herstelde schijven Premium of Standard en niet gemengd.
Herstel de VM rechtstreeks in een beschikbaarheidsset | Voor Managed disks kunt u de schijf herstellen en de optie beschikbaarheidsset gebruiken in de sjabloon.<br/><br/> Niet ondersteund voor niet-beheerde schijven. Voor niet-beheerde schijven herstelt u de schijf en maakt u vervolgens een virtuele machine in de beschikbaarheidsset.
Back-ups van niet-beheerde Vm's herstellen na een upgrade naar een beheerde VM| Ondersteund.<br/><br/> U kunt schijven herstellen en vervolgens een beheerde virtuele machine maken.
Herstel de VM naar een herstel punt voordat de virtuele machine is gemigreerd naar Managed disks | Ondersteund.<br/><br/> U kunt herstellen naar onbeheerde schijven (standaard), de herstelde schijven converteren naar beheerde schijven en een virtuele machine maken met de beheerde schijven.
Een VM herstellen die is verwijderd. | Ondersteund.<br/><br/> U kunt de virtuele machine herstellen vanaf een herstel punt.
Een domein controller (DC)-VM herstellen die deel uitmaakt van een configuratie met meerdere domein controllers via de portal | Ondersteund als u de schijf herstelt en een virtuele machine maakt met behulp van Power shell.
VM herstellen in een ander virtueel netwerk |Ondersteund.<br/><br/> Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden.

## <a name="vm-compute-support"></a>Ondersteuning voor VM-berekening

**Computing** | **Ondersteuning**
--- | ---
VM-grootte |Een Azure VM-grootte met ten minste twee CPU-kernen en 1 GB RAM-geheugen.<br/><br/> [Meer informatie.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Back-ups maken van Vm's in [beschikbaarheids sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Ondersteund.<br/><br/> U kunt een virtuele machine niet herstellen in een beschik bare set met behulp van de optie om snel een virtuele machine te maken. In plaats daarvan moet u, wanneer u de virtuele machine herstelt, de schijf herstellen en gebruiken om een virtuele machine te implementeren, of een schijf herstellen en gebruiken om een bestaande schijf te vervangen.
Back-ups maken van Vm's die zijn geïmplementeerd met het [voor deel voor hybride gebruik (hub)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Ondersteund.
Back-ups maken van virtuele machines die zijn geïmplementeerd in een [schaalset](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Wordt niet ondersteund.
Back-ups maken van Vm's die zijn geïmplementeerd vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Gepubliceerd door micro soft, derden) |Ondersteund.<br/><br/> Op de VM moet een ondersteund besturings systeem worden uitgevoerd.<br/><br/> Bij het herstellen van bestanden op de VM kunt u alleen herstellen naar een compatibel besturings systeem (geen besturings systeem dat ouder of later is). De Azure Marketplace-Vm's die worden ondersteund als Vm's, worden niet hersteld, omdat deze nodig zijn om informatie te kopen, maar alleen als schijven.
Back-ups maken van Vm's die zijn geïmplementeerd vanuit een aangepaste installatie kopie (derden) |Ondersteund.<br/><br/> Op de VM moet een ondersteund besturings systeem worden uitgevoerd.<br/><br/> Bij het herstellen van bestanden op de VM kunt u alleen herstellen naar een compatibel besturings systeem (geen besturings systeem dat ouder of later is).
Back-ups maken van virtuele machines die zijn gemigreerd naar Azure| Ondersteund.<br/><br/> Als u een back-up wilt maken van de VM, moet de VM-agent op de gemigreerde computer zijn geïnstalleerd.
Back-up van multi-VM-consistentie | Azure Backup biedt geen consistentie van gegevens en toepassingen op meerdere Vm's.
Back-up met [Diagnostische instellingen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Niet-ondersteunde. <br/><br/> Als het herstellen van de virtuele machine van Azure met Diagnostische instellingen wordt geactiveerd met de optie [nieuwe maken](backup-azure-arm-restore-vms.md#create-a-vm) , mislukt de herstel bewerking.
Herstellen van op zones vastgemaakte Vm's | Ondersteund (voor VM waarvan een back-up is gemaakt na 2019 januari en waar de [beschikbaarheids zone](https://azure.microsoft.com/global-infrastructure/availability-zones/) beschikbaar is).<br/><br/>We ondersteunen momenteel het herstellen naar dezelfde zone die is vastgemaakt in Vm's. Als de zone echter niet beschikbaar is, mislukt de herstel bewerking.
Gen2 Vm's | Ondersteund <br> Azure Backup ondersteunt het maken van back-ups en het herstellen van [Gen2-vm's](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Wanneer deze Vm's worden hersteld vanaf het herstel punt, worden ze teruggezet als [Gen2 vm's](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Ondersteuning voor VM-opslag

**Onderdeel** | **Ondersteuning**
--- | ---
Azure VM-gegevensschijven | Maak een back-up van een virtuele machine met 16 of minder gegevens schijven.
Grootte van de gegevens schijf | De afzonderlijke schijf grootte kan Maxi maal 32 TB en Maxi maal 256 TB gecombineerd voor alle schijven in een VM zijn.
Opslagtype | Standard-HDD, Standard-SSD Premium-SSD.
Managed Disks | Ondersteund.
Versleutelde schijven | Ondersteund.<br/><br/> Voor Azure-Vm's met Azure Disk Encryption kan een back-up worden gemaakt (met of zonder de Azure AD-app).<br/><br/> Versleutelde Vm's kunnen niet worden hersteld op het niveau van het bestand of de map. U moet de volledige VM herstellen.<br/><br/> U kunt versleuteling inschakelen voor virtuele machines die al worden beveiligd door Azure Backup.
Schijven waarop Write Accelerator is ingeschakeld | Wordt niet ondersteund.<br/><br/> Azure backup sluit automatisch de schijven uit met Write Accelerator ingeschakeld tijdens het maken van een back-up. Omdat er geen back-up is gemaakt, kunt u deze schijven niet herstellen vanaf herstel punten van de virtuele machine.
Back-up maken & ontdubbelde Vm's/schijven herstellen | Azure Backup biedt geen ondersteuning voor ontdubbeling. Raadpleeg dit [artikel](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) voor meer informatie <br/> <br/>  -Azure Backup wordt niet gedupliceerd over Vm's in de Recovery Services kluis <br/> <br/>  -Als er Vm's in de ontdubbelings status zijn tijdens het herstellen, kunnen de bestanden niet worden hersteld omdat de kluis de indeling niet begrijpt
Schijf toevoegen aan beveiligde VM | Ondersteund.
Grootte van schijf op beveiligde virtuele machine wijzigen | Ondersteund.
Gedeelde opslag| Het is niet raadzaam om back-ups te maken van virtuele machines met behulp van Cluster Shared Volume (CSV) of Scale-out bestandsserver. CSV-schrijvers mislukken waarschijnlijk tijdens het maken van de back-up. Bij het terugzetten kunnen schijven met CSV-volumes mogelijk niet worden opgehaald.

## <a name="vm-network-support"></a>VM-netwerk ondersteuning

**Onderdeel** | **Ondersteuning**
--- | ---
Aantal netwerk interfaces (Nic's) | Maxi maal maximum aantal Nic's dat wordt ondersteund voor een specifieke Azure VM-grootte.<br/><br/> Nic's worden gemaakt wanneer de virtuele machine tijdens het herstel proces wordt gemaakt.<br/><br/> Het aantal Nic's op de herstelde VM komt overeen met het aantal Nic's op de virtuele machine wanneer u beveiliging inschakelt. Wanneer u de beveiliging inschakelt, heeft dit geen invloed op het aantal.
Externe/interne load balancer |Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van vm's met speciale netwerk instellingen.
Meerdere gereserveerde IP-adressen |Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van vm's met speciale netwerk instellingen.
Vm's met meerdere netwerk adapters| Ondersteund. <br/><br/> [Meer informatie](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) over het herstellen van vm's met speciale netwerk instellingen.
Vm's met open bare IP-adressen| Ondersteund.<br/><br/> Een bestaand openbaar IP-adres koppelen aan de NIC, of een adres maken en dit koppelen aan de NIC nadat het terugzetten is voltooid.
Netwerk beveiligings groep (NSG) op NIC/subnet. |Ondersteund.
Statisch IP-adres | Wordt niet ondersteund.<br/><br/> Er wordt een dynamisch IP-adres toegewezen aan een nieuwe virtuele machine die is gemaakt op basis van een herstel punt.<br/><br/> Voor klassieke Vm's kunt u geen back-up maken van een virtuele machine met een gereserveerd IP-adres en geen gedefinieerd eind punt.
Dynamisch IP-adres |Ondersteund.<br/><br/> Als de NIC op de bron-VM gebruikmaakt van dynamische IP-adres Sering, wordt deze ook gebruikt door de NIC op de herstelde VM.
Azure Traffic Manager| Ondersteund.<br/><br/>Als de back-up van de virtuele machine is Traffic Manager, voegt u de herstelde VM hand matig toe aan hetzelfde Traffic Manager exemplaar.
Azure DNS |Ondersteund.
Aangepaste DNS |Ondersteund.
Uitgaande connectiviteit via HTTP-proxy | Ondersteund.<br/><br/> Een geverifieerde proxy wordt niet ondersteund.
Service-eindpunten voor virtueel netwerk| Ondersteund.<br/><br/> De instellingen voor het opslag account van de firewall en het virtuele netwerk moeten toegang toestaan vanuit alle netwerken.

## <a name="vm-security-and-encryption-support"></a>Ondersteuning voor VM-beveiliging en-versleuteling

Azure Backup ondersteunt versleuteling voor in-transit- en inactieve gegevens:

Netwerkverkeer naar Azure:

- Back-upverkeer van servers naar de Recovery Services-kluis wordt versleuteld met behulp van Advanced Encryption Standard 256.
- Back-upgegevens worden verzonden via een beveiligde HTTPS-koppeling.
- De back-upgegevens worden versleuteld opgeslagen in de Recovery Services-kluis.
- Alleen u beschikt over de wachtwoordzin waarmee deze gegevens kunnen worden ontgrendeld. De back-upgegevens kunnen niet door Microsoft ontsleuteld.

  > [!WARNING]
  > Nadat u de kluis hebt ingesteld, hebt u alleen toegang tot de versleutelings sleutel. Microsoft bewaart nooit een kopie en heeft geen toegang tot de sleutel. Als de sleutel verkeerd wordt geplaatst, kan Microsoft de back-upgegevens niet herstellen.

Gegevensbeveiliging:

- Bij het maken van een back-up van virtuele Azure-machines moet u versleuteling instellen *binnen* de VM.
- Azure Backup ondersteunt Azure Disk Encryption, dat gebruikmaakt van BitLocker op virtuele Windows-machines en US **DM-cryptografie** op virtuele Linux-machines.
- Op de backend maakt Azure Backup gebruik van [Azure Storage Service-versleuteling](../storage/common/storage-service-encryption.md), waarmee data-at-rest worden beschermd.

**Machine** | **In-transit** | **Inactief**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | ![Ja][green] | ![Ja][green]
Azure-VM's | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Ondersteuning voor VM-compressie

Backup ondersteunt de compressie van het back-upverkeer, zoals wordt beschreven in de volgende tabel. Houd rekening met het volgende:

- Voor virtuele Azure-machines leest de VM-extensie de gegevens rechtstreeks vanuit het Azure Storage-account via het opslag netwerk. Het is niet nodig om dit verkeer te comprimeren.
- Als u DPM of MABS gebruikt, kunt u band breedte besparen door de gegevens te comprimeren voordat er een back-up wordt gemaakt naar DPM/MABS.

**Machine** | **Comprimeren naar MABS/DPM (TCP)** | **Comprimeren naar kluis (HTTPS)**
--- | --- | ---
On-premises Windows-machines zonder DPM/MABS | N.v.t. | ![Ja][green]
Azure-VM's | N.v.t. | N.v.t.
On-premises/Azure VM's met DPM | ![Ja][green] | ![Ja][green]
On-premises/Azure VM's met MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Volgende stappen

- [Maak een back-up van virtuele Azure-machines](backup-azure-arm-vms-prepare.md).
- [Rechtstreeks een back-up maken van Windows-machines](tutorial-backup-windows-server-to-azure.md), zonder een back-upserver.
- [MABS instellen](backup-azure-microsoft-azure-backup.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar MABS maken.
- [DPM instellen](backup-azure-dpm-introduction.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar DPM maken.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
