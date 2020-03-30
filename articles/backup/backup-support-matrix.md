---
title: Ondersteuningsmatrix voor Azure Backup
description: Bevat een samenvatting van ondersteuningsinstellingen en -beperkingen voor de Azure Backup-service.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: d036e527880a98d323e8de2f3a8721d7e12dbb07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273265"
---
# <a name="support-matrix-for-azure-backup"></a>Ondersteuningsmatrix voor Azure Backup

U [Azure Backup](backup-overview.md) gebruiken om een back-up te maken van gegevens naar het Microsoft Azure-cloudplatform. In dit artikel worden de algemene ondersteuningsinstellingen en -beperkingen voor Azure Backup-scenario's en -implementaties samengevat.

Andere ondersteuning matrices zijn beschikbaar:

- [Vm-back-up (Supportmatrix) voor azure virtual machine (VM)](backup-support-matrix-iaas.md)
- Ondersteuningsmatrix voor back-up met Behulp van [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Ondersteuningsmatrix voor back-up met behulp van de [MARS-agent (Microsoft Azure Recovery Services)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Ondersteuning voor kluizen

Azure Backup gebruikt Vaults van Recovery Services om back-ups te orkestreren en te beheren. Het maakt ook gebruik van kluizen om back-upgegevens op te slaan.

In de volgende tabel worden de kenmerken van vaults van Recovery Services beschreven:

**Functie** | **Details**
--- | ---
**Kluizen in abonnement** | Maximaal 500 Recovery Services-kluizen in één abonnement.
**Machines in een kluis** | Maximaal 1.000 Azure VM's in één kluis.<br/><br/> Maximaal 50 MABS-servers kunnen in één kluis worden geregistreerd.
**Gegevensbronnen** | De maximale grootte van een afzonderlijke [gegevensbron](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) is 54.400 GB. Deze limiet is niet van toepassing op Azure VM-back-ups. Er zijn geen limieten van toepassing op de totale hoeveelheid gegevens die u back-ups maken naar de kluis.
**Back-ups naar kluis** | **Azure VM's:** Eén keer per dag.<br/><br/>**Machines beschermd door DPM/MABS:** Twee keer per dag.<br/><br/> **Machines die rechtstreeks worden geback-upt met behulp van het MARS-middel:** Drie keer per dag.
**Back-ups tussen kluizen** | Back-up bevindt zich in een regio.<br/><br/> U hebt een kluis nodig in elke Azure-regio die VM's bevat waarvan u een back-up wilt maken. Je niet terug naar een andere regio.
**Kluizen verplaatsen** | U [kluizen verplaatsen](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) tussen abonnementen of tussen resourcegroepen in hetzelfde abonnement. Het verplaatsen van kluizen in verschillende regio's wordt echter niet ondersteund.
**Gegevens verplaatsen tussen kluizen** | Het verplaatsen van back-upgegevens tussen kluizen wordt niet ondersteund.
**Type kluisopslag wijzigen** | U het type opslagreplicatie (georedundante opslag of lokaal redundante opslag) voor een kluis wijzigen voordat back-ups worden opgeslagen. Nadat een back-ups in de kluis is begonnen, kan het replicatietype niet meer worden gewijzigd.

## <a name="on-premises-backup-support"></a>On-premises ondersteuning voor back-ups

Dit wordt ondersteund als u een back-up wilt maken van on-premises machines:

**Machine** | **Wat is er back-up** | **Locatie** | **Functies**
--- | --- | --- | ---
**Directe back-up van Windows-machine met MARS-agent** | Bestanden, mappen, systeemstatus | Back-up naar Recovery Services kluis. | Drie keer per dag een back-up maken<br/><br/> Geen app-bewuste back-up<br/><br/> Bestand, map, volume herstellen
**Directe back-up van Linux-machine met MARS-agent** | Back-up wordt niet ondersteund
**Back-up naar DPM** | Bestanden, mappen, volumes, systeemstatus, app-gegevens | Back-up naar lokale DPM-opslag. DPM voert vervolgens een back-up uit naar de kluis. | App-bewuste momentopnamen<br/><br/> Volledige granulariteit voor back-up en herstel<br/><br/> Linux ondersteund voor VM's (Hyper-V/VMware)<br/><br/> Oracle wordt niet ondersteund
**Back-up naar MABS** | Bestanden, mappen, volumes, systeemstatus, app-gegevens | Maak een back-up naar mABS lokale opslag. MABS voert vervolgens een back-up uit naar de kluis. | App-bewuste momentopnamen<br/><br/> Volledige granulariteit voor back-up en herstel<br/><br/> Linux ondersteund voor VM's (Hyper-V/VMware)<br/><br/> Oracle wordt niet ondersteund

## <a name="azure-vm-backup-support"></a>Ondersteuning voor Azure VM-back-ups

### <a name="azure-vm-limits"></a>Limieten voor Azure VM's

**Limiet** | **Details**
--- | ---
**Azure VM-gegevensschijven** | Limiet van 16 <br> Als u zich wilt aanmelden voor de persoonlijke preview-versie van VM's met meer dan 16 schijven (maximaal 32 schijven), stuurt u een bericht naar AskAzureBackupTeam@microsoft.com
**Grootte Azure VM-gegevensschijven** | De individuele schijfgrootte kan maximaal 32 TB en maximaal 256 TB zijn gecombineerd voor alle schijven in een VM.

### <a name="azure-vm-backup-options"></a>Azure VM-back-upopties

Dit wordt u ondersteund als u een back-up wilt maken van Azure VM's:

**Machine** | **Wat is er back-up** | **Locatie** | **Functies**
--- | --- | --- | ---
**Azure VM-back-up met VM-extensie** | Hele VM | Terug naar de kluis. | Extensie geïnstalleerd wanneer u back-up saneren voor een virtuele machine inschakelt.<br/><br/> Eén keer per dag achteruit.<br/><br/> App-bewuste back-up voor Windows VM's; file-consistent back-up voor Linux VM's. U app-consistentie voor Linux-machines configureren met behulp van aangepaste scripts.<br/><br/> VM of schijf herstellen.<br/><br/> U geen back-ups maken van een Azure VM naar een on-premises locatie.
**Azure VM-back-up met MARS-agent** | Bestanden, mappen, systeemstatus | Terug naar de kluis. | Drie keer per dag achteruit.<br/><br/> Als u een back-up wilt maken van specifieke bestanden of mappen in plaats van de hele VM, kan de MARS-agent naast de VM-extensie worden uitgevoerd.
**Azure VM met DPM** | Bestanden, mappen, volumes, systeemstatus, app-gegevens | Maak een back-up naar de lokale opslag van Azure VM waarop DPM wordt uitgevoerd. DPM voert vervolgens een back-up uit naar de kluis. | App-bewuste snapshots.<br/><br/> Volledige granulariteit voor back-up en herstel.<br/><br/> Linux wordt ondersteund voor virtuele machines (Hyper-V-/VMware).<br/><br/> Oracle wordt niet ondersteund.
**Azure VM met MABS** | Bestanden, mappen, volumes, systeemstatus, app-gegevens | Maak een back-up naar de lokale opslag van Azure VM waarop MABS wordt uitgevoerd. MABS voert vervolgens een back-up uit naar de kluis. | App-bewuste snapshots.<br/><br/> Volledige granulariteit voor back-up en herstel.<br/><br/> Linux wordt ondersteund voor virtuele machines (Hyper-V-/VMware).<br/><br/> Oracle wordt niet ondersteund.

## <a name="linux-backup-support"></a>Ondersteuning voor Linux-back-ups

Dit is wat er wordt ondersteund als u een back-up wilt maken van Linux-machines:

**Back-uptype** | **Linux (goedgekeurd door Azure)**
--- | ---
**Directe back-up van on-premises machine waarop Linux wordt uitgevoerd** | Wordt niet ondersteund. De MARS-agent kan alleen op Windows-machines worden geïnstalleerd.
**Agent-extensie gebruiken om een back-up te maken van Azure VM waarop Linux wordt uitgevoerd** | App-consistente back-up met behulp van [aangepaste scripts](backup-azure-linux-app-consistent.md).<br/><br/> Herstel op bestandsniveau.<br/><br/> Herstellen door het maken van een VM vanaf een herstelpunt of schijf.
**DPM gebruiken om back-ups te maken van on-premises machines met Linux** | File-consistent back-up van Linux Guest VM's op Hyper-V en VMWare.<br/><br/> VM restauratie van Hyper-V en VMWare Linux Guest VM's.
**MABS gebruiken om back-ups te maken met on-premises machines met Linux** | File-consistent back-up van Linux Guest VM's op Hyper-V en VMWare.<br/><br/> VM restauratie van Hyper-V en VMWare Linux gast VM's.
**MABS of DPM gebruiken om een back-up te maken van Linux Azure VM's** | Wordt niet ondersteund.

## <a name="daylight-saving-time-support"></a>Ondersteuning voor zomertijd

Azure Backup biedt geen ondersteuning voor automatische klokaanpassing voor zomertijd voor Azure VM-back-ups. Het verschuift het uur van de back-up niet naar voren of naar achteren. Als u wilt dat de back-up op het gewenste moment wordt uitgevoerd, wijzigt u het back-upbeleid handmatig naar behoefte.

## <a name="disk-deduplication-support"></a>Ondersteuning voor schijfontdubbeling

Ondersteuning voor schijfontdubbeling is als volgt:

- Schijfontdubbeling wordt on-premises ondersteund wanneer u DPM of MAB's gebruikt om een back-up te maken van Hyper-V VM's waarop Windows wordt uitgevoerd. Windows Server voert gegevensontdubbeling (op hostniveau) uit op virtuele harde schijven (VHD's) die aan de VM zijn gekoppeld als back-upopslag.
- Ontdubbeling wordt niet ondersteund in Azure voor Backup-onderdelen. Wanneer DPM en MABS in Azure worden geïmplementeerd, kunnen de opslagschijven die aan de VM zijn gekoppeld, niet worden gededupliceerd.

## <a name="security-and-encryption-support"></a>Ondersteuning voor beveiliging en versleuteling

Azure Backup ondersteunt versleuteling voor binnenkomende en rustgegevens.

### <a name="network-traffic-to-azure"></a>Netwerkverkeer naar Azure

- Back-upverkeer van servers naar de kluis Van Recovery Services wordt versleuteld met behulp van Advanced Encryption Standard 256.
- Back-upgegevens worden verzonden via een beveiligde HTTPS-koppeling.
- Back-upgegevens worden in versleutelde vorm opgeslagen in de kluis Recovery Services.
- Alleen u beschikt over de wachtwoordzin waarmee deze gegevens kunnen worden ontgrendeld. De back-upgegevens kunnen niet door Microsoft ontsleuteld.

    > [!WARNING]
    > Nadat u de kluis hebt ingesteld, hebt alleen u toegang tot de versleutelingssleutel. Microsoft bewaart nooit een kopie en heeft geen toegang tot de sleutel. Als de sleutel verkeerd wordt geplaatst, kan Microsoft de back-upgegevens niet herstellen.

### <a name="data-security"></a>Gegevensbeveiliging

- Wanneer u een back-up maakt van Azure VM's, moet u versleuteling instellen *binnen* de virtuele machine.
- Azure Backup biedt ondersteuning voor Azure Disk Encryption, dat gebruikmaakt van BitLocker op virtuele Windows-machines en **dm-crypt** op virtuele Linux-machines.
- Aan de achterkant maakt Azure Backup gebruik van [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), die gegevens in rust beschermt.

**Machine** | **In-transit** | **Inactief**
--- | --- | ---
**On-premises Windows-machines zonder DPM/MABS** | ![Ja][green] | ![Ja][green]
**Azure VM's** | ![Ja][green] | ![Ja][green]
**On-premises Windows-machines of Azure VM's met DPM** | ![Ja][green] | ![Ja][green]
**On-premises Windows-machines of Azure VM's met MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Ondersteuning voor compressie

Back-up ondersteunt de compressie van back-upverkeer, zoals samengevat in de volgende tabel.

- Voor Azure VM's leest de VM-extensie de gegevens rechtstreeks vanuit het Azure-opslagaccount via het opslagnetwerk, zodat het niet nodig is om dit verkeer te comprimeren.
- Als u DPM of MABS gebruikt, u bandbreedte besparen door de gegevens te comprimeren voordat er een back-up van wordt gemaakt.

**Machine** | **Comprimeren naar MABS/DPM (TCP)** | **Comprimeren naar kluis (HTTPS)**
--- | --- | ---
**Directe back-up van on-premises Windows-machines** | N.v.t. | ![Ja][green]
**Back-up van Azure VM's met VM-extensie** | N.v.t. | N.v.t.
**Back-up op on-premises/Azure-machines met Behulp van MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Bewaarlimieten

**Instelling** | **Grenzen**
--- | ---
**Maximale herstelpunten per beveiligde instantie (machine of werkbelasting)** | 9,999
**Maximale verlooptijd voor een herstelpunt** | Geen limiet
**Maximale back-upfrequentie naar DPM/MABS** | Om de 15 minuten voor SQL Server<br/><br/> Eén keer per uur voor andere workloads
**Maximale back-upfrequentie naar kluis** | **On-premises Windows-machines of Azure VM's met MARS:** Drie per dag<br/><br/> **DPM/MABS:** Twee per dag<br/><br/> **Azure VM-back-up:** Eén per dag
**Herstelpunt behoud** | Dagelijks, wekelijks, maandelijks, jaarlijks
**Maximale bewaarperiode** | Afhankelijk van back-upfrequentie
**Herstelpunten op DPM-/MABS-schijf** | 64 voor bestandsservers; 448 voor app-servers <br/><br/>Onbeperkte tapeherstelpunten voor on-premises DPM

## <a name="cross-region-restore"></a>Regio-herstel

Azure Backup heeft de functie Cross Region Restore toegevoegd om de beschikbaarheid en tolerantie van gegevens te verbeteren, zodat klanten volledige controle hebben om gegevens naar een secundaire regio te herstellen. Ga naar het [artikel Cross Region Restore instellen](backup-create-rs-vault.md#set-cross-region-restore)om deze functie te configureren. Deze functie wordt ondersteund voor de volgende beheertypen:

| Type Back-upbeheer | Ondersteund                                                    | Ondersteunde regio's |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Ja. Openbare beperkte preview ondersteund voor versleutelde VM's en VM's met minder dan 4 TB-schijven | VS - west-centraal   |
| MARS-agent/on-premises | Nee                                                           | N.v.t.               |
| SQL /SAP HANA          | Nee                                                           | N.v.t.               |
| Afs                    | Nee                                                           | N.v.t.               |

## <a name="next-steps"></a>Volgende stappen

- [Controleer de ondersteuningsmatrix](backup-support-matrix-iaas.md) voor Azure VM-back-up.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
