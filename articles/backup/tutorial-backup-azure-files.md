---
title: Zelfstudie - Back-ups maken van bestandsshares van Azure Files
description: Lees in deze zelfstudie hoe u de Azure-portal gebruiken om een vault voor Herstelservices te configureren en een back-up te maken van Azure-bestandsshares.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293926"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Back-ups maken van Azure-bestandsshares in de Azure-portal

In deze zelfstudie wordt uitgelegd hoe u de Azure-portal gebruiken om een back-up te maken van [Azure-bestandsshares.](../storage/files/storage-files-introduction.md)

In deze handleiding leert u het volgende:
> [!div class="checklist"]
>
> * Een Recovery Services-kluis configureren om een back-up te maken van Azure-bestanden
> * Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken

## <a name="prerequisites"></a>Vereisten

Voordat u een back-up van een Azure-bestandsshare probeert te maken, moet u nagaan of deze zich in een van de [ondersteunde typen opslagaccounts](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) bevindt. Zodra u dit hebt bevestigd, kunt u uw bestandsshares beveiligen.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Beperkingen voor back-up van Azure-bestandsshare tijdens preview

Back-up voor Azure-bestandsshares is in preview. Azure-bestandsshares worden ondersteund voor de accounts voor algemeen gebruik v1 en v2. De volgende back-upscenario's worden niet ondersteund voor Azure-bestandsshares:

* Er is geen CLI beschikbaar voor het beveiligen van Azure Files met behulp van Azure Backup.
* Het maximumaantal geplande back-ups per dag is één.
* Het maximumaantal on-demand back-ups per dag is vier.
* Gebruik [bronvergrendelingen](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) op het opslagaccount om te voorkomen dat back-ups per ongeluk worden verwijderd in uw vault van Recovery Services.
* Verwijder geen momentopnamen die met Azure Backup zijn gemaakt. Het verwijderen van momentopnamen kan leiden tot het verlies van herstelpunten en/of herstelfouten.
* Verwijder geen bestandsshares die zijn beveiligd met Azure Backup. Met de huidige oplossing worden alle momentopnamen die zijn gemaakt met Azure Backup, verwijderd zodra de bestandsshare wordt verwijderd. Alle herstelpunten gaan dan dus verloren

Back-ups maken voor Azure File Shares in Storage Accounts met ZRS-replicatie [(Zone redundant storage)](../storage/common/storage-redundancy-zrs.md) zijn momenteel alleen beschikbaar in Centraal-VS (CUS), Oost-VS (EUS), Oost-VS 2 (EUS2), Noord-Europa (NE), Zuidoost-Azië (SEA), West-Europa (WE) en West US 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Back-up voor Azure-bestandsshare configureren

In deze zelfstudie wordt ervan uitgegaan dat u al een Azure-bestandsshare hebt gemaakt. Ga als volgt te werk om een back-up van uw Azure-bestandsshare te maken:

1. Maak een Recovery Services-kluis in dezelfde regio als waarin de bestandsshare zich bevindt. Als u al een kluis hebt, opent u de overzichtspagina van uw kluis en klikt u op **Back-up**.

    ![Klik op Back-up op de overzichtspagina van uw kluis](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. Kies in het menu **Back-updoel** in **Wat wilt u een back-up maken?**

    ![Kies Azure-bestandsshare als back-updoel](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Klik **op Back-up** om het Azure-bestandsaandeel te configureren voor de kluis Van Herstelservices.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Zodra de kluis is gekoppeld aan het Azure-bestandsaandeel, wordt het menu Back-up geopend en wordt u gevraagd een opslagaccount te selecteren. In het menu worden alle ondersteunde opslagaccounts weergegeven in het gebied waar uw kluis bestaat en die nog niet zijn gekoppeld aan een kluis van Herstelservices.

   ![Selecteer uw opslagaccount](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. Selecteer een account in de lijst met opslagaccounts en klik op **OK**. Azure zoekt naar het opslagaccount voor bestandsshares waarvan een back-up kan worden gemaakt. Als u uw bestandsshares recentelijk hebt toegevoegd en ze niet in de lijst worden weergegeven, kan het helpen om even te wachten.

   ![Bestandsshares worden ontdekt](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. Selecteer in de lijst **Bestandsshares** een of meer van de bestandsshares waarvan u een back-up wilt maken en klik op **OK**.

6. Nadat u de bestandsshares hebt geselecteerd, schakelt het menu Back-up over naar het **Back-upbeleid**. In dit menu selecteert u een bestaand back-upbeleid of maakt u een nieuwe. Vervolgens klikt u op **Back-up inschakelen**.

   ![Selecteer een back-upbeleid of maak een nieuw beleid](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    Wanneer er een back-upbeleid is ingesteld, wordt er een momentopname van de bestandsshares genomen op het geplande tijdstip en wordt het herstelpunt gedurende de gekozen periode bewaard.

## <a name="create-an-on-demand-backup"></a>Een back-up op aanvraag maken

Nadat u het back-upbeleid hebt geconfigureerd, wilt u een on-demand back-up maken om ervoor te zorgen dat uw gegevens worden beschermd tot de volgende geplande back-up.

### <a name="to-create-an-on-demand-backup"></a>Een back-up op aanvraag maken

1. Open de Recovery Services-kluis waarin de herstelpunten van de bestandsshares staan en klik op **Back-upitems**. De lijst met typen back-upitems wordt weergegeven.

   ![Lijst met back-upitems](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. Selecteer **Azure Storage (Azure-bestanden)** in de lijst. De lijst met Azure-bestandsshares wordt weergegeven.

   ![Lijst met Azure-bestandsshares](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. Selecteer de gewenste bestandsshare uit de lijst met Azure-bestandsshares. Het menu Back-upitem wordt geopend voor de geselecteerde bestandsshare.

   ![Menu Back-upitem voor de geselecteerde bestandsshare](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. Klik in het menu Back-upitem op **Nu back-up maken**. Omdat dit een back-uptaak op aanvraag is, is er geen bewaarbeleid gekoppeld aan het herstelpunt. Het dialoogvenster **Nu back-up maken** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren.

   ![Kies datum voor het behoud van herstelpunten](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Portal gebruikt voor het volgende:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis configureren om een back-up te maken van Azure-bestanden
> * Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken

Ga verder naar het volgende artikel om te herstellen van een back-up van een Azure-bestandsshare.

> [!div class="nextstepaction"]
> [Herstellen van back-up van Azure-bestandsshares](restore-afs.md)
