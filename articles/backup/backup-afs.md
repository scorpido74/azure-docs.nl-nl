---
title: Back-ups maken van Azure-bestands shares in de Azure Portal
description: Meer informatie over het gebruik van de Azure Portal voor het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294511"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Back-ups maken van Azure-bestands shares in een Recovery Services kluis

In dit artikel wordt uitgelegd hoe u de Azure Portal gebruikt voor het maken van back-ups van [Azure-bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

In deze handleiding leert u het volgende:

* Een Recovery Services-kluis maken
* Bestands shares detecteren en back-ups configureren
* Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken

## <a name="prerequisites"></a>Vereisten

* Identificeer of maak een [Recovery Services kluis](#create-a-recovery-services-vault) in dezelfde regio als het opslag account dat als host fungeert voor de bestands share.

* Zorg ervoor dat de bestands share aanwezig is in een van de [ondersteunde typen opslag accounts](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Beperkingen voor het maken van back-ups van Azure-bestandsshares in de preview-versie

Back-up voor Azure-bestandsshares is in preview. Azure-bestandsshares worden ondersteund voor de accounts voor algemeen gebruik v1 en v2. Dit zijn de beperkingen voor het maken van back-ups van Azure-bestands shares:

* Ondersteuning voor het maken van back-ups van Azure-bestands shares in opslag accounts met [zone redundant Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) replicatie is momenteel beperkt tot [deze regio's](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup ondersteunt momenteel het configureren van geplande eenmalige back-ups van Azure-bestands shares.
* Het maximumaantal geplande back-ups per dag is één.
* Het maximumaantal on-demand back-ups per dag is vier.
* Gebruik [resourcevergrendelingen](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) voor het opslagaccount om per ongeluk verwijderen van back-ups uit de Recovery Services-kluis te voorkomen.
* Verwijder geen momentopnamen die met Azure Backup zijn gemaakt. Het verwijderen van momentopnamen kan leiden tot het verlies van herstelpunten en/of herstelfouten.
* Verwijder geen bestandsshares die zijn beveiligd met Azure Backup. Met de huidige oplossing worden alle moment opnamen verwijderd die zijn gemaakt door Azure Backup zodra de bestands share is verwijderd en dus alle herstel punten verloren gaan.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Opslag replicatie wijzigen

Standaard gebruiken kluizen de [geo-redundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.

* U kunt [lokaal redundante opslag (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken als een goedkope optie.

Wijzig het type opslag replicatie als volgt:

1. Klik in de nieuwe kluis op **Eigenschappen** in de sectie **instellingen** .

2. In **Eigenschappen**, onder **back-upconfiguratie**, klikt u op **bijwerken**.

3. Selecteer het type opslag replicatie en klik op **Opslaan**.

    ![Back-upconfiguratie bijwerken](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> U kunt het type opslag replicatie niet wijzigen nadat de kluis is ingesteld en back-upitems bevat. Als u dit wilt doen, moet u de kluis opnieuw maken.
>

## <a name="discover-file-shares-and-configure-backup"></a>Bestands shares detecteren en back-up configureren

1. Open in de [Azure Portal](https://portal.azure.com/)de Recovery Services kluis die u wilt gebruiken om een back-up te maken van de bestands share.

2. Selecteer **+ Backup**In het **Recovery Services kluis** -dash board.

   ![Recovery Services-kluis](./media/backup-afs/recovery-services-vault.png)

   a. In **back-updoel**stelt u in **waar wordt uw workload uitgevoerd?** naar **Azure**.

    ![Een Azure-bestands share kiezen als back-updoel](./media/backup-afs/backup-goal.png)

    b.    Selecteer in **waarvan wilt u een back-up maken**, de optie **Azure-bestands share** in de vervolg keuzelijst.

    c.    Klik op **back-up** om de extensie van de Azure-bestands share in de kluis te registreren.

      ![Klik op back-up om de Azure-bestands share te koppelen aan de kluis](./media/backup-afs/register-extension.png)

3. Zodra u op **back-up**klikt, wordt de Blade back-up geopend en wordt u gevraagd een opslag account te selecteren in een lijst met gedetecteerde ondersteunde opslag accounts. Ze zijn gekoppeld aan deze kluis of bevinden zich in dezelfde regio als de kluis, maar nog niet gekoppeld aan een Recovery Services kluis.

   ![Opslagaccount selecteren](./media/backup-afs/select-storage-account.png)

4. Selecteer een account in de lijst met gedetecteerde opslag accounts en klik op **OK**. Azure zoekt naar het opslagaccount voor bestandsshares waarvan een back-up kan worden gemaakt. Als u onlangs uw bestands shares hebt toegevoegd en deze niet in de lijst ziet, moet u enige tijd opgeven dat de bestands shares worden weer gegeven.

    ![Bestands shares detecteren](./media/backup-afs/discovering-file-shares.png)

5. Selecteer in de lijst **Bestands shares** een of meer van de bestands shares waarvan u een back-up wilt maken en klik op **OK**.

6. Nadat u de bestands shares hebt gekozen, verandert het **back** -upmenu in **back-upbeleid**. In dit menu selecteert u een bestaand back-upbeleid of maakt u een nieuwe en klikt u vervolgens op **back-up inschakelen**.

    ![Back-upbeleid selecteren](./media/backup-afs/select-backup-policy.png)

Wanneer er een back-upbeleid is ingesteld, wordt er een momentopname van de bestandsshares genomen op het geplande tijdstip en wordt het herstelpunt gedurende de gekozen periode bewaard.

## <a name="create-an-on-demand-backup"></a>Een back-up op aanvraag maken

In sommige gevallen wilt u mogelijk een back-upmomentopname of een herstel punt genereren, buiten het tijdstip dat is gepland in het back-upbeleid. Een veelvoorkomende reden voor het genereren van een back-up op aanvraag is direct nadat u het back-upbeleid hebt geconfigureerd. Afhankelijk van de planning in het back-upbeleid, kan het uren of dagen duren totdat er een momentopname wordt gemaakt. Als u uw gegevens wilt beschermen totdat het back-upbeleid actief wordt, start u een back-up op aanvraag. Het maken van een back-up op aanvraag is vaak vereist voordat u geplande wijzigingen aanbrengt aan uw bestands shares.

### <a name="to-create-an-on-demand-backup"></a>Een back-up op aanvraag maken

1. Open de Recovery Services kluis die u hebt gebruikt voor het maken van een back-up van uw bestands share en klik op **Back-upitems** in de sectie **beveiligde items** van de Blade **overzicht** .

   ![Klik op Back-upitems](./media/backup-afs/backup-items.png)

2. Zodra u op **Back-upitems**klikt, wordt een nieuwe blade met alle **typen back-upbeheer** als volgt weer gegeven naast de Blade **overzicht** :

   ![Lijst met typen back-upbeheer](./media/backup-afs/backup-management-types.png)

3. Selecteer **Azure Storage (Azure files)** in de lijst met **typen back-upbeheer**. U ziet een lijst met alle bestands shares en de bijbehorende opslag accounts waarvan een back-up is gemaakt met behulp van deze kluis.

   ![Back-upitems van Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

4. Selecteer de gewenste bestandsshare uit de lijst met Azure-bestandsshares. De details van het **back-upitem** worden weer gegeven. Klik in het menu **back-upitem** op **Nu back-up maken**. Omdat dit een back-uptaak op aanvraag is, is er geen bewaarbeleid gekoppeld aan het herstelpunt.

   ![Klik op nu back-up maken](./media/backup-afs/backup-now.png)

5. De Blade **Nu back-up** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren. U kunt een maximale Bewaar periode van tien jaar voor een back-up op aanvraag hebben.

   ![Bewaar datum kiezen](./media/backup-afs/retention-date.png)

6. Klik op **OK** om de taak voor het uitvoeren van de back-up op aanvraag te bevestigen.

7. Controleer de portal meldingen om het volt ooien van de back-uptaak uit te voeren. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** > worden **uitgevoerd**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van Azure-bestands shares](restore-afs.md)

* Meer informatie over het [beheren van back-ups van Azure-bestands shares](manage-afs-backup.md)
