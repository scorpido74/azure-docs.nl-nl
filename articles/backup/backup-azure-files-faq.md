---
title: Veelgestelde vragen over het maken van een back-up van Azure-bestanden
description: Dit artikel bevat informatie over het beveiligen van uw Azure-bestandsshares.
author: dcurwin
ms.author: dacurwin
ms.date: 07/29/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 05b591137a53e60b3197feb7f57564a8d4af7a44
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624284"
---
# <a name="questions-about-backing-up-azure-files"></a>Vragen over het maken back-ups van Azure-bestanden
In dit artikel vindt u antwoorden op veelgestelde vragen over het maken back-ups van Azure-bestanden. Sommige antwoorden bevatten koppelingen naar artikelen met uitgebreide informatie over het onderwerp. U kunt ook in het [discussieforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) vragen over de Azure Backup-service plaatsen.

Als u kort de secties in dit artikel wilt bekijken, gebruikt u de koppelingen aan de rechterkant, onder **In dit artikel**.

## <a name="configuring-the-backup-job-for-azure-files"></a>De back-uptaak configureren voor Azure-bestanden

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Waarom zie ik een aantal van de opslagaccounts niet die ik wil beveiligen, hoewel ze geldige Azure-bestandsshares bevatten? <br/>
Tijdens de preview-fase worden niet alle typen opslagaccounts ondersteund bij het maken van back-ups voor Azure-bestandsshares. Raadpleeg [deze](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview) lijst met ondersteunde opslagaccounts. Het is ook mogelijk dat het opslagaccount dat u zoekt al is beveiligd of bij een andere kluis is geregistreerd. [Hef](troubleshoot-azure-files.md#configuring-backup) de registratie van de kluis op om het opslagaccount in andere beveiligingskluizen te detecteren.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Waarom zie ik een aantal van mijn Azure-bestandsshares in het opslagaccount niet als ik mijn back-up configureer? <br/>
Controleer of de Azure-bestandsshare al in dezelfde Recovery Services-kluis wordt beveiligd en of de bestandsshare onlangs wellicht is verwijderd.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Kan ik bestandsshares die met een synchronisatiegroep in Azure File Sync zijn verbonden beveiligen? <br/>
Ja. De beveiliging van Azure-bestandsshares die zijn verbonden met synchronisatiegroepen is ingeschakeld en maakt onderdeel uit van een openbare preview.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Ik wilde een back-up van bestandsshares maken, en ik klikte op een opslagaccount om de bestandsshares daarin te detecteren. Ik heb ze echter niet beveiligt. Hoe kan ik deze bestandsshares met een andere kluis beveiligen?
Als u een back-up gaat maken en het opslagaccount selecteert waarin u bestandsshares wit detecteren, wordt het opslagaccount geregistreerd bij de kluis waarin dit wordt gedaan. Als u de bestandsshares wilt beveiligen met een andere kluis, moet u de [registratie ongedaan maken](troubleshoot-azure-files.md#configuring-backup) van het betreffende opslagaccount voor deze kluis.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Kan ik de kluis wijzigen waarnaar ik een back-up van mijn bestands shares Maak?
Ja. U moet echter wel de [beveiliging stoppen](backup-azure-files.md#stop-protecting-an-azure-file-share) vanuit de gekoppelde kluis, de [registratie ongedaan maken](troubleshoot-azure-files.md#configuring-backup) van dit opslagaccount, en het account vervolgens met een andere kluis beveiligen.

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>In welke geografische gebieden kan ik back-ups maken van Azure-bestandsshares? <br/>
Het maken van back-ups van Azure-bestandsshares is momenteel in de preview-fase en is alleen beschikbaar in de volgende geografische gebieden:
- Australië - oost (AE)
- Australië - zuidoost (ASE)
- Brazilië - zuid (BRS)
- Canada centraal (CNC)
- Canada - oost (CE)
- US - centraal (CUS)
- Azië - oost (EA)
- US - oost (EUS)
- US - oost 2 (EUS2)
- Japan - oost (JPE)
- Japan - west (JPW)
- India - centraal (INC)
- India - zuid (INS)
- Korea - centraal (KRC)
- Korea - zuid (KRS)
- US - noord-centraal (NCUS)
- Europa - noord (NE)
- US - zuid-centraal (SCUS)
- Azië - zuidoost (SEA)
- Verenigd Koninkrijk zuid (UKS)
- Verenigd Koninkrijk west (UKW)
- Europa - west (WE)
- US - west (WUS)
- US - west-centraal (WCUS)
- US - west 2 (WUS 2)
- US Gov - Arizona (UGA)
- US Gov - Texas (UGT)
- US Gov - Virginia (UGV)

Schrijf naar [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) als u deze functie wilt gebruiken in een specifiek geografisch gebied dat hierboven niet wordt vermeld.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Hoeveel Azure-bestandsshares kan ik beveiligen in een kluis?<br/>
Met de preview-versie kunt u Azure-bestandsshares van maximaal 50 opslagaccounts per kluis beveiligen. U kunt ook tot 200 Azure-bestandsshares in een enkele kluis beveiligen.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Kan ik twee verschillende bestandsshares van hetzelfde opslagaccount in verschillende kluizen beveiligen?
Nee. Alle bestandsshares in een opslagaccount kunnen alleen in dezelfde kluis worden beveiligd.

## <a name="backup"></a>Back-up

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>Hoeveel geplande back-ups kan ik per bestands share configureren?
Azure Backup ondersteunt momenteel het configureren van geplande eenmalige back-ups van Azure-bestands shares. 

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Hoeveel back-ups op aanvraag kan ik maken per bestandsshare? <br/>
U kunt op elk moment maximaal 200 momentopnamen voor een bestandsshare hebben. Deze limiet is inclusief momentopnamen die zijn gemaakt met Azure Backup zoals gedefinieerd in uw beleid. Als uw back-ups mislukken nadat de limiet is bereikt, verwijdert u herstelpunten op aanvraag om ervoor te zorgen dat toekomstige back-ups slagen.


## <a name="restore"></a>Herstellen

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>Kan ik gegevens herstellen vanuit een verwijderde Azure-bestandsshare? <br/>
Wanneer een Azure-bestandsshare wordt verwijderd, krijgt u de lijst met back-ups te zien die ook worden verwijderd, en wordt u gevraagd dit te bevestigen. Een verwijderde Azure-bestandsshare kan niet worden hersteld.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Kan ik gegevens herstellen vanuit back-ups als ik ben gestopt met de beveiliging van een Azure-bestandsshare? <br/>
Ja. Als u **Back-upgegevens behouden** hebt gekozen toen u met de beveiliging stopte, kunt u vanaf alle bestaande herstelpunten iets terugzetten.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Wat gebeurt er als ik een actieve herstel taak Annuleer?
Als een actieve herstel taak wordt geannuleerd, wordt het herstel proces gestopt en alle bestanden die vóór de annulering zijn teruggezet, blijven de geconfigureerde bestemming (oorspronkelijke of alternatieve locatie) zonder terugdraai bewerkingen. 


## <a name="manage-backup"></a>Het maken van back-ups beheren

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares-br"></a>Kan ik PowerShell gebruiken voor het configureren/beheren/terugzetten van back-ups van Azure-bestandsshares? <br/>
Ja. Raadpleeg de gedetailleerde documentatie [hier](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Heb ik toegang tot de momentopnamen die door Azure Backup is gemaakt en deze koppelen? <br/>
Alle momentopnamen die zijn gemaakt met Azure Backup, zijn toegankelijk via Momentopnamen weergeven in de portal, in PowerShell of in CLI. Meer informatie over momentopnamen van Azure-bestandsshares vindt u in [Overzicht van share-momentopnamen voor Azure Files (preview)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Wat is de maximale bewaartermijn die ik voor back-ups kan configureren? <br/>
Voor de back-ups voor Azure-bestandsshares kunt u beleidsregels configureren. Back-ups kunnen tot 180 dagen worden bewaard. Met de optie [On-demand backup (Back-ups on-demand) in PowerShell](backup-azure-afs-automation.md#trigger-an-on-demand-backup) kunt u herstelpunten echter tot zelfs tien jaar bewaren.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Wat gebeurt er wanneer ik het back-upbeleid voor een Azure-bestandsshare wijzig? <br/>
Wanneer er op een of meer bestandsshares een nieuw beleid wordt toegepast, worden het schema en de retentie van het nieuwe beleid gevolgd. Als de retentie is uitgebreid, worden de bestaande herstelpunten gemarkeerd voor bewaring conform het nieuwe beleid. Als de retentie wordt beperkt, worden de bestaande herstelpunten gemarkeerd voor verwijdering in de eerstvolgende opschoontaak, waarna ze worden verwijderd.

## <a name="see-also"></a>Zie ook
Deze informatie heeft alleen betrekking op het maken van back-ups van Azure-bestanden. Voor meer informatie over andere gebieden van Azure Backup, wordt u aangeraden een aantal van een van deze overige veelgestelde vragen over het maken van back-ups te raadplegen:
-  [Veelgestelde vragen over Recovery Services-kluis](backup-azure-backup-faq.md)
-  [Veelgestelde vragen over Azure VM-back-ups](backup-azure-vm-backup-faq.md)
-  [Veelgestelde vragen over Azure Backup-agent](backup-azure-file-folder-backup-faq.md)