---
title: Back-ups maken van Azure Files bestands shares met de Azure Backup-Service
description: In deze zelf studie leert u hoe u de Azure Portal gebruikt voor het configureren van een Recovery Services kluis en het maken van back-ups van Azure-bestands shares.
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: a8b08f87441f9b4c67f718dfe9f0c894d0730a5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747050"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Back-ups maken van Azure-bestands shares in de Azure Portal

In deze zelf studie wordt uitgelegd hoe u de Azure Portal gebruikt voor het maken van back-ups van [Azure-bestands shares](../storage/files/storage-files-introduction.md).

In deze handleiding leert u het volgende:
> [!div class="checklist"]
>
> * Een Recovery Services-kluis configureren om een back-up te maken van Azure-bestanden
> * Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken

## <a name="prerequisites"></a>Vereisten

Voordat u een back-up van een Azure-bestandsshare probeert te maken, moet u nagaan of deze zich in een van de [ondersteunde typen opslagaccounts](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) bevindt. Zodra u dit hebt bevestigd, kunt u uw bestandsshares beveiligen.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Beperkingen voor back-ups van Azure-bestands share tijdens preview

Back-up voor Azure-bestandsshares is in preview. Azure-bestandsshares worden ondersteund voor de accounts voor algemeen gebruik v1 en v2. De volgende back-upscenario's worden niet ondersteund voor Azure-bestandsshares:

* U kunt Azure-bestandsshares niet beveiligen in opslagaccounts waarvoor virtuele netwerken zijn of een firewall is ingeschakeld.
* Er is geen CLI beschikbaar voor het beveiligen van Azure Files met behulp van Azure Backup.
* Het maximumaantal geplande back-ups per dag is één.
* Het maximumaantal on-demand back-ups per dag is vier.
* Gebruik [resourcevergrendelingen](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) voor het opslagaccount om per ongeluk verwijderen van back-ups uit de Recovery Services-kluis te voorkomen.
* Verwijder geen momentopnamen die met Azure Backup zijn gemaakt. Het verwijderen van momentopnamen kan leiden tot het verlies van herstelpunten en/of herstelfouten.
* Verwijder geen bestandsshares die zijn beveiligd met Azure Backup. Met de huidige oplossing worden alle momentopnamen die zijn gemaakt met Azure Backup, verwijderd zodra de bestandsshare wordt verwijderd. Alle herstelpunten gaan dan dus verloren

Back-ups maken voor Azure-bestands shares in opslag accounts met [zone redundant Storage](../storage/common/storage-redundancy-zrs.md) (ZRS) replicatie is momenteel alleen beschikbaar in de centrale VS (cus), VS-Oost (Eus), VS-Oost 2 (EUS2), Europa-Noord (ne), Zuidoost-Azië (Sea), Europa-West (we) en VS-West 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Back-up voor Azure-bestandsshare configureren

In deze zelfstudie wordt ervan uitgegaan dat u al een Azure-bestandsshare hebt gemaakt. Ga als volgt te werk om een back-up van uw Azure-bestandsshare te maken:

1. Maak een Recovery Services-kluis in dezelfde regio als waarin de bestandsshare zich bevindt. Als u al een kluis hebt, opent u de overzichtspagina van uw kluis en klikt u op **Back-up**.

    ![Klik op back-up op de overzichts pagina van uw kluis](./media/backup-file-shares/overview-backup-page.png)

2. Kies in het menu doel van de **back-up** van waaruit wilt **u een back-up maken?** de optie Azure-bestands share.

    ![Kies Azure-bestandsshare als back-updoel](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Klik op **Back-up** om de Azure-bestandsshare naar de Recovery Services-kluis te configureren.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/set-backup-goal.png)

    Zodra de kluis is gekoppeld aan de Azure-bestands share, wordt het menu back-up geopend en wordt u gevraagd een opslag account te selecteren. In het menu worden alle ondersteunde opslag accounts weer gegeven in de regio waar uw kluis zich bevindt die nog niet is gekoppeld aan een Recovery Services kluis.

   ![Uw opslag account selecteren](./media/backup-file-shares/list-of-storage-accounts.png)

4. Selecteer een account in de lijst met opslagaccounts en klik op **OK**. Azure zoekt naar het opslagaccount voor bestandsshares waarvan een back-up kan worden gemaakt. Als u uw bestandsshares recentelijk hebt toegevoegd en ze niet in de lijst worden weergegeven, kan het helpen om even te wachten.

   ![Bestands shares worden gedetecteerd](./media/backup-file-shares/discover-file-shares.png)

5. Selecteer in de lijst **Bestands shares** een of meer van de bestands shares waarvan u een back-up wilt maken en klik op **OK**.

6. Nadat u de bestandsshares hebt geselecteerd, schakelt het menu Back-up over naar het **Back-upbeleid**. In dit menu selecteert u een bestaand back-upbeleid of maakt u een nieuwe. Vervolgens klikt u op **Back-up inschakelen**.

   ![Een back-upbeleid selecteren of een nieuwe maken](./media/backup-file-shares/apply-backup-policy.png)

    Wanneer er een back-upbeleid is ingesteld, wordt er een momentopname van de bestandsshares genomen op het geplande tijdstip en wordt het herstelpunt gedurende de gekozen periode bewaard.

## <a name="create-an-on-demand-backup"></a>Een back-up op aanvraag maken

Nadat u het back-upbeleid hebt geconfigureerd, moet u een back-up op aanvraag maken om ervoor te zorgen dat uw gegevens worden beveiligd tot de volgende geplande back-up.

### <a name="to-create-an-on-demand-backup"></a>Een back-up op aanvraag maken

1. Open de Recovery Services-kluis waarin de herstelpunten van de bestandsshares staan en klik op **Back-upitems**. De lijst met typen back-upitems wordt weergegeven.

   ![Lijst met back-upitems](./media/backup-file-shares/list-of-backup-items.png)

2. Selecteer **Azure Storage (Azure-bestanden)** in de lijst. De lijst met Azure-bestandsshares wordt weergegeven.

   ![Lijst met Azure-bestands shares](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Selecteer de gewenste bestandsshare uit de lijst met Azure-bestandsshares. Het menu Back-upitem wordt geopend voor de geselecteerde bestandsshare.

   ![Menu back-upitem voor de geselecteerde bestands share](./media/backup-file-shares/backup-item-menu.png)

4. Klik in het menu Back-upitem op **Nu back-up maken**. Omdat dit een back-uptaak op aanvraag is, is er geen bewaarbeleid gekoppeld aan het herstelpunt. Het dialoogvenster **Nu back-up maken** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren.

   ![Kies een datum voor de Bewaar periode van het herstel punt](./media/backup-file-shares/backup-now-menu.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Portal gebruikt voor het volgende:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis configureren om een back-up te maken van Azure-bestanden
> * Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken

Ga door naar het volgende artikel om een back-up van een Azure-bestands share te herstellen.

> [!div class="nextstepaction"]
> [Herstellen vanaf een back-up van Azure-bestands shares](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
