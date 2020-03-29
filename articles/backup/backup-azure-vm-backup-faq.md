---
title: Veelgestelde vragen - Back-ups maken van Azure VM's
description: Ontdek in dit artikel antwoorden op veelgestelde vragen over het maken van back-ups van Azure VM's met de Azure Backup-service.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 5d2f702b49e1e7aeb2ab33008556e91264b39427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705408"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Veelgestelde vragen-Back-up s-up Azure VM's

In dit artikel worden veelgestelde vragen beantwoord over het maken van back-ups van Azure VM's met de [Azure Backup-service.](backup-introduction-to-azure-backup.md)

## <a name="backup"></a>Back-up

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Welke VM-afbeeldingen kunnen worden ingeschakeld voor back-ups wanneer ik ze maak?

Wanneer u een VM maakt, u back-ups inschakelen voor VM's waarop [ondersteunde besturingssystemen worden](backup-support-matrix-iaas.md#supported-backup-actions) uitgevoerd

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Zijn de back-upkosten inbegrepen in de VM-kosten?

Nee. Back-upkosten staan los van de kosten van een VM. Meer informatie over [azure backup-prijzen](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Welke machtigingen zijn vereist om back-ups voor een vm in te schakelen?

Als u een vm-bijdrager bent, u een back-up inschakelen op de vm. Als u een aangepaste rol gebruikt, hebt u de volgende machtigingen nodig om een back-up op de VM in te schakelen:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locaties/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Als uw kluis herstelservices en VM verschillende resourcegroepen hebben, controleert u of u schrijfmachtigingen hebt in de brongroep voor de kluis Herstelservices.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Gebruikt een on-demand back-uptaak hetzelfde bewaarschema als geplande back-ups?

Nee. Geef het bewaarbereik op voor een on-demand back-uptaak. Standaard wordt het 30 dagen bewaard wanneer deze vanuit de portal wordt geactiveerd.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ik heb onlangs Azure Disk Encryption ingeschakeld op een aantal virtuele machines. Worden mijn back-ups gewoon uitgevoerd?

Geef machtigingen voor Azure Backup om toegang te krijgen tot Key Vault. Geef de machtigingen in PowerShell op zoals beschreven in de sectie **Back-up inschakelen** in de [Azure Backup PowerShell-documentatie.](backup-azure-vms-automation.md)

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Ik heb VM-schijven gemigreerd naar beheerde schijven. Worden mijn back-ups gewoon uitgevoerd?

Ja, back-ups werken naadloos. Het is niet nodig om iets opnieuw te configureren.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Waarom zie ik mijn VM niet in de wizard voor Back-up configureren?

De wizard geeft alleen VM's op in dezelfde regio als de kluis en er wordt nog geen back-up van gemaakt.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Mijn VM is uitgeschakeld. Werkt een on-demand of een geplande back-up?

Ja. Back-ups worden uitgevoerd wanneer een machine wordt uitgeschakeld. Het herstelpunt is gemarkeerd als crash consistent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan ik een back-uptaak annuleren?

Ja. U een back-uptaak annuleren in een **momentopnamestatus maken.** U een taak niet annuleren als de gegevensoverdracht van de momentopname aan de gang is.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Ik heb de vergrendeling van de brongroep die is gemaakt door Azure Backup Service ingeschakeld (d.w.z. `AzureBackupRG_<geo>_<number>`), blijven mijn back-ups werken?

Als u de brongroep vergrendelt die is gemaakt door Azure Backup Service, worden back-ups mislukt omdat er een maximumlimiet van 18 herstelpunten is.

De gebruiker moet de vergrendeling verwijderen en de verzameling herstelpunten uit die brongroep wissen om de toekomstige back-ups succesvol te maken, [volg deze stappen](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) om de verzameling herstelpunten te verwijderen.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Ondersteunt Azure-back-up standaard ssd-beheerde schijven?

Ja, Azure Backup ondersteunt [standaard SSD-beheerde schijven.](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kunnen we een back-up maken van een VM met een Write Accelerator (WA)-enabled schijf?

Momentopnamen kunnen niet worden gemaakt op de SCHIJF met WA-ingeschakelde schijf. De Azure Backup-service kan de SCHIJF met WA echter uitsluiten van back-ups.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Ik heb een VM met Write Accelerator (WA) schijven en SAP HANA geïnstalleerd. Hoe maak ik een back-up?

Azure Backup kan geen back-up maken van de SCHIJF met WA, maar kan deze uitsluiten van back-ups. De back-up biedt echter geen databaseconsistentie omdat er geen back-up wordt gemaakt van informatie op de schijf met INGESCHAKELDE WA.The backup won echter niet van de database, omdat er geen back-up van informatie op de schijf met INGESCHAKELDE WA wordt gemaakt. U een back-up maken van schijven met deze configuratie als u een back-up wilt maken van de schijfvan het besturingssysteem en een back-up wilt maken van schijven die niet zijn ingeschakeld voor WA.

We draaien een privépreview voor een SAP HANA back-up met een RPO van 15 minuten. Het is gebouwd op een vergelijkbare manier als SQL DB back-up, en maakt gebruik van de backInt-interface voor oplossingen van derden gecertificeerd door SAP HANA. Als u geïnteresseerd bent, `AskAzureBackupTeam@microsoft.com` kunt u ons een e-mail sturen met het onderwerp **Aanmelden voor een privépreview voor back-ups van SAP HANA in Azure VM's.**

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Wat is de maximale vertraging die ik kan verwachten in de begintijd van de back-up van de geplande back-uptijd die ik in mijn VM-back-upbeleid heb ingesteld?

De geplande back-up wordt geactiveerd binnen 2 uur na de geplande back-uptijd. Als bijvoorbeeld 100 VM's hun begintijd voor back-ups hebben gepland om 02:00 uur, hebben maximaal 04:00 uur alle 100 VM's een back-uptaak in uitvoering. Als geplande back-ups zijn onderbroken vanwege een storing en zijn hervat/opnieuw geprobeerd, kan de back-up buiten dit geplande venster van twee uur worden gestart.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Wat is het minimaal toegestane bewaarbereik voor het dagelijkse back-uppunt?

Azure Virtual Machine-back-upbeleid ondersteunt een minimumbewaarbereik van zeven dagen tot 9999 dagen. Elke wijziging in een bestaand VM-back-upbeleid met minder dan zeven dagen vereist een update om te voldoen aan het minimale bewaarbereik van zeven dagen.

### <a name="can-i-backup-or-restore-selective-disks-attached-to-a-vm"></a>Kan ik selectieve schijven die aan een vm zijn gekoppeld, een back-up maken of herstellen?

Azure Backup ondersteunt nu selectieve schijfback-up en herstel met behulp van de Azure Virtual Machine-back-upoplossing.

Tegenwoordig ondersteunt Azure Backup een back-up van alle schijven (besturingssysteem en gegevens) in een VM samen met behulp van de back-upoplossing voor virtuele machines. Met de functionaliteit van de uitgesloten schijf krijgt u een optie om een of enkele back-ups te maken van de vele gegevensschijven in een vm. Dit biedt een efficiënte en kosteneffectieve oplossing voor uw back-up- en herstelbehoeften. Elk herstelpunt bevat gegevens van de schijven die zijn opgenomen in de back-upbewerking, waarmee u verder een subset van schijven laten herstellen vanaf het gegeven herstelpunt tijdens de herstelbewerking. Dit geldt voor het herstellen van zowel de momentopname als de kluis.

Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Herstellen

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hoe bepaal ik of ik alleen schijven of een volledige VM wil herstellen?

Zie een VM-herstel als een optie voor snel maken voor een Azure-vm. Met deze optie worden schijfnamen, containers die door de schijven worden gebruikt, openbare IP-adressen en namen van de netwerkinterface gewijzigd. De wijziging behoudt unieke bronnen wanneer een vm wordt gemaakt. De VM wordt niet toegevoegd aan een beschikbaarheidsset.

U de optie Schijf herstellen gebruiken als u dat wilt:

- Pas de VM aan die wordt gemaakt. Wijzig bijvoorbeeld de grootte.
- Voeg configuratie-instellingen toe die er op het moment van de back-up niet waren.
- Beheer de naamgevingsconventie voor resources die zijn gemaakt.
- Voeg de VM toe aan een beschikbaarheidsset.
- Voeg alle andere instellingen toe die moeten worden geconfigureerd met PowerShell of een sjabloon.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan ik back-ups van onbeheerde VM-schijven herstellen nadat ik ben geüupgradet naar beheerde schijven?

Ja, u back-ups gebruiken die zijn gemaakt voordat schijven zijn gemigreerd van onbeheerd naar beheerd.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hoe herstel ik een VM naar een herstelpunt vóór de migratie van de VM naar beheerde schijven?

Het herstelproces blijft hetzelfde. Als het herstelpunt een point-in-time is waarop VM onbeheerde schijven had, u [schijven herstellen als onbeheerd](tutorial-restore-disk.md#unmanaged-disks-restore). Als de VM schijven vervolgens heeft beheerd, u [schijven herstellen als beheerde schijven](tutorial-restore-disk.md#managed-disk-restore). Vervolgens u [een VM maken van die schijven.](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)

[Meer informatie](backup-azure-vms-automation.md#restore-an-azure-vm) over dit doen in PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Kan ik de verwijderde VM herstellen?

Ja. Zelfs als u de vm verwijdert, u naar het bijbehorende back-upitem in de kluis gaan en herstellen vanaf een herstelpunt.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Hoe u een VM herstellen naar dezelfde beschikbaarheidssets?

Voor Azure VM beheerde schijf wordt herstellen naar de beschikbaarheidssets ingeschakeld door een optie in sjabloon op te geven terwijl u herstelt als beheerde schijven. Deze sjabloon heeft de invoerparameter **Availability sets**genaamd .

### <a name="how-do-we-get-faster-restore-performances"></a>Hoe krijgen we snellere herstelprestaties?

[Instant Restore-mogelijkheden](backup-instant-restore-capability.md) helpen bij snellere back-ups en directe herstelvan de momentopnamen.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Wat gebeurt er als we de instellingen voor de sleutelkluis voor de versleutelde vm wijzigen?

Nadat u de KeyVault-instellingen voor de versleutelde vm hebt gewijzigd, blijven back-ups werken met de nieuwe set details. Echter, na het herstel van een herstelpunt voorafgaand aan de wijziging, moet u de geheimen in een KeyVault herstellen voordat u de VM ervan maken. Voor meer informatie, raadpleeg dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)

Bewerkingen zoals geheime/sleutelroll-over vereisen deze stap niet en dezelfde KeyVault kan worden gebruikt na herstel.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Kan ik toegang krijgen tot de VM nadat deze is hersteld omdat een VM de relatie met de domeincontroller heeft verbroken?

Ja, u hebt toegang tot de VM nadat deze is hersteld omdat een vm de relatie met de domeincontroller heeft verbroken. Voor meer informatie, raadpleeg dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Back-ups van uw virtuele machine beheren

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Wat gebeurt er als ik een back-upbeleid wijzig?

Er wordt een back-up van de vm gemaakt met behulp van de plannings- en bewaarinstellingen in het gewijzigde of nieuwe beleid.

- Als de retentie wordt verlengd, worden bestaande herstelpunten gemarkeerd en bewaard in overeenstemming met het nieuwe beleid.
- Als de retentie wordt verminderd, worden herstelpunten gemarkeerd voor snoeien in de volgende opruimtaak en vervolgens verwijderd.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hoe verplaats ik een vm waareen een back-up van maakt door Azure Backup naar een andere brongroep?

1. Stop de back-up tijdelijk en bewaar back-upgegevens.
2. Verplaats de VM naar de doelgroep.
3. Back-up opnieuw ingeschakeld in dezelfde of nieuwe kluis.

U de VM herstellen vanaf de beschikbare herstelpunten die vóór de verplaatsingsbewerking zijn gemaakt.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Is er een limiet voor het aantal VM's dat kan worden gekoppeld aan hetzelfde back-upbeleid?

Ja, er is een limiet van 100 VM's die kunnen worden gekoppeld aan hetzelfde back-upbeleid van portal. We raden u aan voor meer dan 100 VM's meerdere back-upbeleidsregels met hetzelfde schema of een ander schema te maken.
