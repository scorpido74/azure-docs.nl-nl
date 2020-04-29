---
title: Veelgestelde vragen over het maken van een back-up van Azure-bestanden
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het beveiligen van uw Azure-bestands shares met de Azure Backup-service.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105638"
---
# <a name="questions-about-backing-up-azure-files"></a>Vragen over het maken back-ups van Azure-bestanden

In dit artikel vindt u antwoorden op veelgestelde vragen over het maken back-ups van Azure-bestanden. Sommige antwoorden bevatten koppelingen naar artikelen met uitgebreide informatie over het onderwerp. U kunt in het [discussieforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) ook vragen over de Azure Backup-service plaatsen.

Als u kort de secties in dit artikel wilt bekijken, gebruikt u de koppelingen aan de rechterkant, onder **In dit artikel**.

## <a name="configuring-the-backup-job-for-azure-files"></a>De back-uptaak configureren voor Azure-bestanden

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Waarom zie ik een aantal van mijn opslag accounts die ik wil beveiligen, met geldige Azure-bestands shares?

Raadpleeg de [ondersteunings matrix voor Azure file shares backup](azure-file-share-support-matrix.md) om te controleren of het opslag account tot een van de ondersteunde typen opslag accounts behoort. Het is ook mogelijk dat het opslag account dat u zoekt al wordt beveiligd of is geregistreerd bij een andere kluis. [Hef de registratie van het opslag account](manage-afs-backup.md#unregister-a-storage-account) uit de kluis op om het opslag account in andere kluizen voor beveiliging te detecteren.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Waarom zie ik een aantal van mijn Azure-bestandsshares in het opslagaccount niet als ik mijn back-up configureer?

Controleer of de Azure-bestandsshare al in dezelfde Recovery Services-kluis wordt beveiligd en of de bestandsshare onlangs wellicht is verwijderd.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Kan ik bestandsshares die met een synchronisatiegroep in Azure File Sync zijn verbonden beveiligen?

Ja. De beveiliging van Azure-bestands shares die zijn verbonden met synchronisatie groepen is ingeschakeld.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Ik wilde een back-up van bestandsshares maken, en ik klikte op een opslagaccount om de bestandsshares daarin te detecteren. Ik heb ze echter niet beveiligd. Hoe kan ik deze bestands shares beveiligen met een andere kluis?

Wanneer u een back-up wilt maken, selecteert u een opslag account om bestands shares te detecteren in het opslag account met de kluis van waaruit dit wordt gedaan. Als u de bestands shares met een andere kluis wilt beveiligen, moet u de [registratie](manage-afs-backup.md#unregister-a-storage-account) van het geselecteerde opslag account bij deze kluis opheffen.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Kan ik de kluis wijzigen waarnaar ik een back-up van mijn bestands shares Maak?

Ja. U moet [de beveiliging van de bestands share](manage-afs-backup.md#stop-protection-on-a-file-share) van de verbonden kluis echter stoppen, de registratie van dit opslag account [ongedaan maken](manage-afs-backup.md#unregister-a-storage-account) en vervolgens beveiligen tegen een andere kluis.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Hoeveel Azure-bestandsshares kan ik beveiligen in een kluis?

U kunt Azure-bestands shares beveiligen tegen Maxi maal 50 opslag accounts per kluis. U kunt ook tot 200 Azure-bestandsshares in een enkele kluis beveiligen.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Kan ik twee verschillende bestandsshares van hetzelfde opslagaccount in verschillende kluizen beveiligen?

Nee. Alle bestandsshares in een opslagaccount kunnen alleen in dezelfde kluis worden beveiligd.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Wat moet ik doen als er een fout is opgetreden bij het starten van mijn back-ups omdat de maximum limiet is bereikt?

U kunt op elk moment maximaal 200 momentopnamen voor een bestandsshare hebben. Deze limiet is inclusief momentopnamen die zijn gemaakt met Azure Backup zoals gedefinieerd in uw beleid. Als uw back-ups mislukken nadat de limiet is bereikt, verwijdert u moment opnamen op aanvraag voor geslaagde toekomstige back-ups.

## <a name="restore"></a>Herstellen

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Kan ik gegevens herstellen vanuit een verwijderde Azure-bestandsshare?

Wanneer een Azure-bestands share wordt verwijderd, wordt de lijst met back-ups die worden verwijderd weer gegeven en wordt een bevestiging gevraagd. Op dit moment kan een verwijderde Azure-bestands share niet worden hersteld.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Kan ik gegevens herstellen vanuit back-ups als ik ben gestopt met de beveiliging van een Azure-bestandsshare?

Ja. Als u **Back-upgegevens behouden** hebt gekozen toen u met de beveiliging stopte, kunt u vanaf alle bestaande herstelpunten iets terugzetten.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Wat gebeurt er als ik een actieve herstel taak Annuleer?

Als een actieve herstel taak wordt geannuleerd, wordt het herstel proces gestopt en alle bestanden die vóór de annulering zijn teruggezet, blijven de geconfigureerde bestemming (oorspronkelijke of alternatieve locatie) zonder terugdraai bewerkingen.

## <a name="manage-backup"></a>Back-up beheren

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Kan ik PowerShell gebruiken voor het configureren/beheren/terugzetten van back-ups van Azure-bestandsshares?

Ja. Raadpleeg de [gedetailleerde documentatie voor meer informatie.](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Kan ik toegang krijgen tot de moment opnamen die zijn gemaakt door Azure backups en deze koppelen?

Alle moment opnamen die door Azure Backup worden gemaakt, zijn toegankelijk via moment opnamen weer geven in de portal, Power shell of CLI. Zie [overzicht van moment opnamen van shares voor Azure files](../storage/files/storage-snapshots-files.md)voor meer informatie over Azure files moment opnamen van shares.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Wat is de maximale Bewaar periode die ik voor back-ups kan configureren?

Raadpleeg de [ondersteunings matrix](azure-file-share-support-matrix.md) voor meer informatie over de maximale Bewaar periode. Azure Backup voert een realtime berekening uit van het aantal moment opnamen wanneer u de retentie waarden invoert tijdens het configureren van het back-upbeleid. Zodra het aantal moment opnamen dat overeenkomt met uw gedefinieerde Bewaar waarden 200 overschrijdt, wordt in de portal een waarschuwing weer gegeven waarin u wordt gevraagd uw Bewaar waarden aan te passen. Dat betekent dat u de limiet van het maximum aantal moment opnamen dat door Azure Files voor een bestands share op een wille keurig moment wordt ondersteund, niet overschrijdt.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Wat gebeurt er wanneer ik het back-upbeleid voor een Azure-bestandsshare wijzig?

Wanneer er op een of meer bestandsshares een nieuw beleid wordt toegepast, worden het schema en de retentie van het nieuwe beleid gevolgd. Als de retentie is uitgebreid, worden de bestaande herstelpunten gemarkeerd voor bewaring conform het nieuwe beleid. Als de retentie wordt beperkt, worden de bestaande herstelpunten gemarkeerd voor verwijdering in de eerstvolgende opschoontaak, waarna ze worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie enkele van deze veelgestelde vragen over back-ups voor meer informatie over andere gebieden van Azure Backup:

- [Veelgestelde vragen over Recovery Services-kluis](backup-azure-backup-faq.md)
- [Veelgestelde vragen over Azure VM-back-ups](backup-azure-vm-backup-faq.md)
- [Veelgestelde vragen over Azure Backup-agent](backup-azure-file-folder-backup-faq.md)
