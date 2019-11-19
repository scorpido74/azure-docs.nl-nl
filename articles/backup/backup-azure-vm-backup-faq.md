---
title: 'Veelgestelde vragen: back-ups maken van virtuele Azure-machines'
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van back-ups van virtuele Azure-machines met de Azure Backup-service.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 30036d6cf241e1ac840b2be67ca78fbda6c60061
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172562"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Veelgestelde vragen: back-ups maken van virtuele Azure-machines

In dit artikel vindt u antwoorden op veelgestelde vragen over het maken van back-ups van virtuele Azure-machines met de [Azure backup](backup-introduction-to-azure-backup.md) -service.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Welke VM-installatie kopieën kunnen worden ingeschakeld voor back-up wanneer ik ze Maak?

Wanneer u een virtuele machine maakt, kunt u back-ups inschakelen voor Vm's met [ondersteunde besturings systemen](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Worden de kosten voor back-ups opgenomen in de VM-kosten?

Nee. De back-upkosten zijn gescheiden van de kosten van de virtuele machine. Meer informatie over [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Welke machtigingen zijn vereist voor het inschakelen van een back-up voor een virtuele machine?

Als u een VM-bijdrager bent, kunt u back-up inschakelen op de VM. Als u een aangepaste rol gebruikt, hebt u de volgende machtigingen nodig voor het inschakelen van back-ups op de VM:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Als uw Recovery Services kluis en VM verschillende resource groepen hebben, moet u ervoor zorgen dat u schrijf machtigingen hebt in de resource groep voor de Recovery Services kluis.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Maakt een back-uptaak op aanvraag gebruik van hetzelfde Bewaar schema als geplande back-ups?

Nee. Geef de Bewaar termijn op voor een back-uptaak op aanvraag. Standaard wordt deze 30 dagen bewaard bij het activeren van de portal.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ik heb onlangs Azure Disk Encryption ingeschakeld op een aantal virtuele machines. Worden mijn back-ups gewoon uitgevoerd?

Machtigingen opgeven voor Azure Backup toegang tot Key Vault. Geef de machtigingen in Power shell op zoals beschreven in de sectie **back-up inschakelen** in de [Azure backup Power shell](backup-azure-vms-automation.md) -documentatie.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Ik heb VM-schijven naar Managed disks gemigreerd. Worden mijn back-ups gewoon uitgevoerd?

Ja, back-ups werken probleemloos. U hoeft niets opnieuw te configureren.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Waarom zie ik mijn VM niet in de wizard voor Back-up configureren?

De wizard bevat alleen Vm's in dezelfde regio als de kluis en er wordt nog geen back-up van gemaakt.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Mijn VM is afgesloten. Werkt een on-demand of een geplande back-up?

Ja. Back-ups worden uitgevoerd wanneer een machine wordt afgesloten. Het herstel punt is gemarkeerd als crash consistent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan ik een back-uptaak in uitvoering annuleren?

Ja. U kunt de back-uptaak annuleren in een status voor het **maken van moment opnamen** . U kunt een taak niet annuleren als de gegevens overdracht van de moment opname wordt uitgevoerd.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Ik heb de vergren deling ingeschakeld voor de resource groep die is gemaakt door Azure Backup Service (dat wil zeggen `AzureBackupRG_<geo>_<number>`) zullen mijn back-ups blijven werken?

Als u de resource groep die is gemaakt met Azure Backup-Service vergrendelt, mislukken back-ups als er een maximum limiet van 18 herstel punten is.

De gebruiker moet de vergren deling verwijderen en de herstel punt verzameling wissen van die resource groep om de toekomstige back-ups te kunnen volt ooien. [Volg deze stappen](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) om de verzameling herstel punten te verwijderen.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Ondersteunt Azure Backup standaard SSD-Managed disks?

Ja, Azure Backup ondersteunt [Standard SSD Managed disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kan ik een back-up maken van een VM met een Write Accelerator (WA) ingeschakelde schijf?

Er kunnen geen moment opnamen worden gemaakt op de schijf met WA-functionaliteit. De Azure Backup-service kan de op WA ingeschakelde schijf echter uitsluiten van een back-up.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Ik heb een virtuele machine met Write Accelerator (WA) schijven en SAP HANA geïnstalleerd. Hoe kan ik back-up?

Azure Backup kan geen back-up maken van de op WA ingeschakelde schijf, maar deze kan wel worden uitgesloten van een back-up. De back-up biedt echter geen consistentie van de data base, omdat er geen back-up wordt gemaakt van de gegevens op de schijf met WA. U kunt met deze configuratie back-ups maken van schijven als u back-ups wilt maken van de besturingssysteem schijf en een back-up wilt maken van schijven waarop geen WA is ingeschakeld.

Er wordt een persoonlijke preview uitgevoerd voor een SAP HANA back-up met een RPO van 15 minuten. Het is gebaseerd op een soort gelijke manier als SQL DB-back-up en gebruikt de backInt-interface voor oplossingen van derden die door SAP HANA zijn gecertificeerd. Als u geïnteresseerd bent, kunt u ons een e-mail sturen naar `AskAzureBackupTeam@microsoft.com` met het onderwerp **Aanmelden voor persoonlijke Preview voor het maken van back-ups van SAP Hana in azure-vm's**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Wat is de maximale vertraging die ik kan verwachten in de start tijd van de back-up vanaf de geplande back-uptijd die ik heb ingesteld in mijn VM-back-upbeleid?

De geplande back-up wordt binnen twee uur na de geplande back-uptijd geactiveerd. Als 100 Vm's bijvoorbeeld de begin tijd van de back-up hebben gepland om 2:00 uur, wordt de back-uptaak uitgevoerd op Maxi maal 4:00 uur op alle virtuele machines van de 100. Als geplande back-ups zijn onderbroken wegens onderbrekingen en hervat/opnieuw proberen, kan de back-up worden gestart buiten deze geplande periode van twee uur.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Wat is de mini maal toegestane Bewaar termijn voor dagelijks back-uppunt?

Het back-upbeleid van Azure Virtual Machine ondersteunt een minimale Bewaar termijn van zeven dagen tot 9999 dagen. Voor elke wijziging in een bestaand back-upbeleid voor de VM met minder dan zeven dagen is een update vereist om te voldoen aan de minimale Bewaar termijn van zeven dagen.

## <a name="restore"></a>Herstellen

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hoe kan ik bepalen of u alleen schijven of een volledige virtuele machine wilt herstellen?

U kunt een VM-herstel bewerking beschouwen als een snelle optie voor het maken van een virtuele Azure-machine. Met deze optie wijzigt u de schijf namen, containers die worden gebruikt door de schijven, open bare IP-adressen en netwerk interface namen. De wijziging houdt unieke resources bij wanneer een virtuele machine wordt gemaakt. De virtuele machine is niet toegevoegd aan een beschikbaarheidsset.

U kunt de optie voor het herstellen van de schijf gebruiken als u het volgende wilt doen:

- Pas de virtuele machine aan die wordt gemaakt. Wijzig bijvoorbeeld de grootte.
- Configuratie-instellingen toevoegen die niet aanwezig zijn op het moment van de back-up.
- Bepaal de naamgevings regels voor resources die worden gemaakt.
- Voeg de virtuele machine toe aan een beschikbaarheidsset.
- Voeg een andere instelling toe die moet worden geconfigureerd met Power shell of een sjabloon.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan ik back-ups van niet-beheerde VM-schijven herstellen na een upgrade naar Managed disks?

Ja, u kunt back-ups gebruiken die zijn gemaakt voordat schijven zijn gemigreerd van niet-beheerd naar beheerd.

- Een VM-taak herstellen maakt standaard een niet-beheerde virtuele machine.
- U kunt echter schijven herstellen en gebruiken om een beheerde virtuele machine te maken.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hoe herstel ik een VM naar een herstelpunt vóór de migratie van de VM naar beheerde schijven?

Een VM-taak herstellen maakt standaard een virtuele machine met onbeheerde schijven. Een virtuele machine maken met Managed disks:

1. [Herstellen naar niet-beheerde schijven](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Converteer de herstelde schijven naar Managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Maak een virtuele machine met Managed disks](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

Meer [informatie hierover vindt](backup-azure-vms-automation.md#restore-an-azure-vm) u in Power shell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Kan ik de verwijderde VM herstellen?

Ja. Zelfs als u de virtuele machine verwijdert, kunt u naar het bijbehorende back-upitem in de kluis gaan en het herstellen vanaf een herstel punt.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Hoe kan ik een virtuele machine herstellen naar dezelfde beschikbaarheids sets?

Voor de virtuele machine van de beheerde schijf wordt het herstellen naar de beschikbaarheids sets ingeschakeld door een optie in de sjabloon op te geven tijdens het herstellen als beheerde schijven. Deze sjabloon bevat de invoer parameter met de naam **beschikbaarheids sets**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hoe worden de prestaties sneller teruggezet?

Met de functie voor [direct terugzetten](backup-instant-restore-capability.md) kunt u sneller back-ups maken en direct herstellen vanuit de moment opnamen.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Wat gebeurt er wanneer de sleutel kluis instellingen voor de versleutelde VM worden gewijzigd?

Nadat u de instellingen voor de sleutel kluis voor de verwerkte virtuele machine hebt gewijzigd, blijven back-ups werken met de nieuwe set details. Na het herstellen van een herstel punt vóór de wijziging, moet u echter de geheimen in een sleutel kluis herstellen voordat u de virtuele machine kunt maken. Raadpleeg dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret) voor meer informatie

Voor bewerkingen zoals geheime/sleutel roll-over is deze stap niet vereist en dezelfde sleutel kluis kan na het herstellen worden gebruikt.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Kan ik toegang krijgen tot de virtuele machine nadat deze is hersteld vanwege een verbroken relatie met de domein controller?

Ja, u kunt de VM na het herstellen openen als gevolg van een virtuele machine met een verbroken relatie met de domein controller. Raadpleeg dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) voor meer informatie

## <a name="manage-vm-backups"></a>Back-ups van uw virtuele machine beheren

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Wat gebeurt er als ik een back-upbeleid Wijzig?

Er wordt een back-up van de virtuele machine gemaakt met behulp van de instellingen voor planning en bewaren in het gewijzigde of nieuwe beleid.

- Als de retentie is uitgebreid, worden de bestaande herstel punten gemarkeerd en bewaard op basis van het nieuwe beleid.
- Als de retentie wordt beperkt, worden herstel punten gemarkeerd voor verwijdering in de volgende opschoon taak en vervolgens verwijderd.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hoe kan ik een VM waarvan een back-up is gemaakt door Azure Backup naar een andere resource groep?

1. De back-up tijdelijk stoppen en back-upgegevens behouden.
2. Verplaats de virtuele machine naar de doel resource groep.
3. Maak de back-up in dezelfde of nieuwe kluis opnieuw.

U kunt de virtuele machine herstellen vanaf beschik bare herstel punten die zijn gemaakt voor de verplaatsings bewerking.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Geldt er een limiet voor het aantal Vm's dat kan worden gekoppeld aan een back-upbeleid?

Ja, er is een limiet van 100 Vm's die kan worden gekoppeld aan hetzelfde back-upbeleid vanuit de portal. We raden u aan meer dan 100 Vm's te maken meerdere back-upbeleid met dezelfde planning of een ander schema.
