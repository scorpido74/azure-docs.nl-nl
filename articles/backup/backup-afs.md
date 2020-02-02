---
title: Back-ups maken van Azure-bestands shares in de Azure Portal
description: Meer informatie over het gebruik van de Azure Portal voor het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938247"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Back-ups maken van Azure-bestands shares in een Recovery Services kluis

In dit artikel wordt uitgelegd hoe u de Azure Portal gebruikt voor het maken van back-ups van [Azure-bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

In dit artikel leert u het volgende:

* Maak een Recovery Services-kluis.
* Ontdek bestands shares en Configureer back-ups.
* Voer een back-uptaak op aanvraag uit om een herstel punt te maken.

## <a name="prerequisites"></a>Vereisten

* Identificeer of maak een [Recovery Services kluis](#create-a-recovery-services-vault) in dezelfde regio als het opslag account dat als host fungeert voor de bestands share.
* Zorg ervoor dat de bestands share aanwezig is in een van de [ondersteunde typen opslag accounts](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Beperkingen voor back-ups van Azure-bestands share tijdens preview

Back-up voor Azure-bestandsshares is in preview. Azure-bestandsshares worden ondersteund voor de accounts voor algemeen gebruik v1 en v2. Hier volgen de beperkingen voor het maken van back-ups van Azure-bestands shares:

* Ondersteuning voor het maken van back-ups van Azure-bestands shares in opslag accounts met ZRS-replicatie ( [zone-redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) ) is momenteel beperkt tot [deze regio's](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup ondersteunt momenteel het configureren van geplande eenmalige back-ups van Azure-bestands shares.
* Het maximumaantal geplande back-ups per dag is één.
* Het maximumaantal on-demand back-ups per dag is vier.
* Gebruik [resourcevergrendelingen](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) voor het opslagaccount om per ongeluk verwijderen van back-ups uit de Recovery Services-kluis te voorkomen.
* Verwijder geen moment opnamen die zijn gemaakt door Azure Backup. Het verwijderen van moment opnamen kan leiden tot verlies van herstel punten of herstel fouten.
* Verwijder geen bestands shares die worden beveiligd door Azure Backup. De huidige oplossing verwijdert alle moment opnamen die zijn gemaakt door Azure Backup nadat de bestands share is verwijderd, zodat alle herstel punten verloren gaan.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Opslag replicatie wijzigen

Standaard gebruiken kluizen de [geo-redundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U kunt [lokaal redundante opslag (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken als een goedkope optie.

Het type opslag replicatie wijzigen:

1. Selecteer in de nieuwe kluis **Eigenschappen** onder de sectie **instellingen** .

1. Selecteer op de pagina **Eigenschappen** onder **back-upconfiguratie**de optie **bijwerken**.

1. Selecteer het type opslag replicatie en selecteer **Opslaan**.

    ![Back-upconfiguratie bijwerken](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> U kunt het type opslag replicatie niet wijzigen nadat de kluis is ingesteld en back-upitems bevat. Als u dit wilt doen, moet u de kluis opnieuw maken.
>

## <a name="discover-file-shares-and-configure-backup"></a>Bestands shares detecteren en back-up configureren

1. Open in de [Azure Portal](https://portal.azure.com/)de Recovery Services kluis die u wilt gebruiken om een back-up te maken van de bestands share.

1. Selecteer **+ Backup**In het **Recovery Services kluis** -dash board.

   ![Recovery Services-kluis](./media/backup-afs/recovery-services-vault.png)

    a. In **back-updoel**stelt u in **waar wordt uw workload uitgevoerd?** naar **Azure**.

    ![Een Azure-bestands share kiezen als back-updoel](./media/backup-afs/backup-goal.png)

    b.  Selecteer in **waarvan wilt u een back-up maken?** de optie **Azure-bestands share** in de vervolg keuzelijst.

    c.  Selecteer **back-up** om de extensie van de Azure-bestands share in de kluis te registreren.

    ![Selecteer back-up om de Azure-bestands share te koppelen aan de kluis](./media/backup-afs/register-extension.png)

1. Nadat u **back-up**hebt geselecteerd, wordt het deel venster **back-up** geopend en wordt u gevraagd een opslag account te selecteren in een lijst met gedetecteerde ondersteunde opslag accounts. Ze zijn gekoppeld aan deze kluis of bevinden zich in dezelfde regio als de kluis, maar nog niet gekoppeld aan een Recovery Services kluis.

   ![Opslagaccount selecteren](./media/backup-afs/select-storage-account.png)

1. Selecteer een account in de lijst met gedetecteerde opslag accounts en selecteer **OK**. Azure zoekt naar het opslag account voor bestands shares waarvan een back-up kan worden gemaakt. Als u onlangs uw bestands shares hebt toegevoegd en deze niet in de lijst ziet, moet u enige tijd toestaan dat de bestands shares worden weer gegeven.

    ![Bestands shares detecteren](./media/backup-afs/discovering-file-shares.png)

1. Selecteer in de lijst **Bestands shares** een of meer van de bestands shares waarvan u een back-up wilt maken. Selecteer **OK**.

1. Nadat u de bestands shares hebt gekozen, wordt het menu **back-** up overgeschakeld naar het **back-upbeleid**. In dit menu selecteert u een bestaand back-upbeleid of maakt u een nieuwe. Selecteer vervolgens **back-up inschakelen**.

    ![Back-upbeleid selecteren](./media/backup-afs/select-backup-policy.png)

Nadat u een back-upbeleid hebt ingesteld, wordt er op het geplande tijdstip een moment opname van de bestands shares gemaakt. Het herstel punt wordt ook voor de gekozen periode bewaard.

## <a name="create-an-on-demand-backup"></a>Een back-up op aanvraag maken

In sommige gevallen wilt u mogelijk een back-upmomentopname of een herstel punt genereren, buiten het tijdstip dat is gepland in het back-upbeleid. Een veelvoorkomende reden voor het genereren van een back-up op aanvraag is direct nadat u het back-upbeleid hebt geconfigureerd. Op basis van de planning in het back-upbeleid, kan het uren of dagen duren totdat er een moment opname wordt gemaakt. Als u uw gegevens wilt beschermen totdat het back-upbeleid actief wordt, start u een back-up op aanvraag. Het maken van een back-up op aanvraag is vaak vereist voordat u geplande wijzigingen aanbrengt aan uw bestands shares.

### <a name="create-a-backup-job-on-demand"></a>Een back-uptaak op aanvraag maken

1. Open de Recovery Services kluis die u hebt gebruikt voor het maken van een back-up van uw bestands share. Selecteer in het deel venster **overzicht** de optie **Back-upitems** onder de sectie **beveiligde items** .

   ![Back-upitems selecteren](./media/backup-afs/backup-items.png)

1. Nadat u **Back-upitems**hebt geselecteerd, wordt er een nieuw deel venster met alle **typen back-upbeheer** weer gegeven naast het deel venster **overzicht** .

   ![Lijst met typen back-upbeheer](./media/backup-afs/backup-management-types.png)

1. Selecteer **Azure Storage (Azure files)** in de lijst **type back-upbeheer** . U ziet een lijst met alle bestands shares en de bijbehorende opslag accounts waarvan een back-up is gemaakt met behulp van deze kluis.

   ![Back-upitems van Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Selecteer de gewenste bestands share uit de lijst met Azure-bestands shares. De details van het **back-upitem** worden weer gegeven. Selecteer in het menu **back-upitem** **Nu back-up maken**. Omdat deze back-uptaak op aanvraag is, is er geen Bewaar beleid gekoppeld aan het herstel punt.

   ![Selecteer nu back-up](./media/backup-afs/backup-now.png)

1. Het deel venster **Nu back-up** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren. U kunt een maximale Bewaar periode van tien jaar voor een back-up op aanvraag hebben.

   ![Bewaar datum kiezen](./media/backup-afs/retention-date.png)

1. Selecteer **OK** om te bevestigen dat de back-uptaak op aanvraag wordt uitgevoerd.

1. Controleer de portal meldingen om het volt ooien van de back-uptaak uit te voeren. U kunt de voortgang van de taak in het kluis dashboard bewaken. Selecteer **back-uptaken** > worden **uitgevoerd**.

## <a name="next-steps"></a>Volgende stappen

Procedures voor:
* [Azure-bestands shares herstellen](restore-afs.md)
* [Back-ups van Azure-bestands shares beheren](manage-afs-backup.md)
