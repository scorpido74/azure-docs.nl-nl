---
title: Back-ups van Azure-bestands shares maken en herstellen
description: In dit artikel wordt uitgelegd hoe u een back-up maakt van uw Azure-bestandsshares en deze herstelt. Ook wordt er uitleg gegeven over beheertaken.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 59dd46c42cdf94f06709d95f68cfb8d8693b9cce
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173095"
---
# <a name="back-up-and-restore-azure-file-shares"></a>Back-ups maken van Azure-bestandsshares en deze terugzetten

In dit artikel wordt uitgelegd hoe u met Azure Portal back-ups kunt maken van [Azure-bestandsshares](../storage/files/storage-files-introduction.md) en deze kunt herstellen.

In deze handleiding leert u het volgende:
> [!div class="checklist"]
>
> * Een Recovery Services-kluis configureren om een back-up te maken van Azure-bestanden
> * Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken
> * Een of meer bestanden herstellen vanaf een herstelpunt
> * Back-uptaken beheren
> * De beveiliging voor Azure-bestanden stoppen
> * Back-upgegevens verwijderen

## <a name="prerequisites"></a>Vereisten

Voordat u een back-up van een Azure-bestandsshare probeert te maken, moet u nagaan of deze zich in een van de [ondersteunde typen opslagaccounts](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) bevindt. Zodra u dit hebt bevestigd, kunt u uw bestandsshares beveiligen.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Beperkingen voor het maken van back-ups van Azure-bestandsshares in de preview-versie

Back-up voor Azure-bestandsshares is in preview. Azure-bestandsshares worden ondersteund voor de accounts voor algemeen gebruik v1 en v2. De volgende back-upscenario's worden niet ondersteund voor Azure-bestandsshares:

* Ondersteuning voor het maken van back-ups van Azure-bestands shares in opslag accounts met [zone redundant Storage](../storage/common/storage-redundancy-zrs.md) (ZRS) replicatie is momenteel beperkt tot [deze regio's](backup-azure-files-faq.md#in-which-geos-can-i-back-up-azure-file-shares).
* Er is geen CLI beschikbaar voor het beveiligen van Azure Files met behulp van Azure Backup.
* Azure Backup ondersteunt momenteel het configureren van geplande eenmalige back-ups van Azure-bestands shares.
* Het maximumaantal geplande back-ups per dag is één.
* Het maximumaantal on-demand back-ups per dag is vier.
* Gebruik [resourcevergrendelingen](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) voor het opslagaccount om per ongeluk verwijderen van back-ups uit de Recovery Services-kluis te voorkomen.
* Verwijder geen momentopnamen die met Azure Backup zijn gemaakt. Het verwijderen van momentopnamen kan leiden tot het verlies van herstelpunten en/of herstelfouten.
* Verwijder geen bestandsshares die zijn beveiligd met Azure Backup. Met de huidige oplossing worden alle moment opnamen verwijderd die zijn gemaakt door Azure Backup zodra de bestands share is verwijderd, waardoor alle herstel punten verloren gaan.

## <a name="configuring-backup-for-an-azure-file-share"></a>Back-up voor Azure-bestandsshare configureren

In dit artikel wordt ervan uitgegaan dat u al een Azure-bestands share hebt gemaakt. Ga als volgt te werk om een back-up van uw Azure-bestandsshare te maken:

1. Maak een Recovery Services-kluis in dezelfde regio als waarin de bestandsshare zich bevindt. Als u al een kluis hebt, opent u de overzichtspagina van uw kluis en klikt u op **Back-up**.

    ![Kies Azure-bestandsshare als back-updoel](./media/backup-file-shares/overview-backup-page.png)

2. Kies in het menu doel van de **back-up** van waaruit wilt **u een back-up maken?** de optie Azure-bestands share.

    ![Kies Azure-bestandsshare als back-updoel](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Klik op **Back-up** om de Azure-bestandsshare naar de Recovery Services-kluis te configureren.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/set-backup-goal.png)

    Nadat de kluis is gekoppeld aan de Azure-bestandsshare, wordt het menu Back-up geopend en wordt u gevraagd een opslagaccount te selecteren. Alle ondersteunde opslagaccounts die nog niet zijn gekoppeld aan een Recovery Services-kluis worden door het menu weergegeven in de regio waarin uw kluis zich bevindt.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/list-of-storage-accounts.png)

4. Selecteer een account in de lijst met opslagaccounts en klik op **OK**. Azure zoekt naar het opslagaccount voor bestandsshares waarvan een back-up kan worden gemaakt. Als u uw bestandsshares recentelijk hebt toegevoegd en ze niet in de lijst worden weergegeven, kan het helpen om even te wachten.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/discover-file-shares.png)

5. Selecteer in de lijst **Bestands shares** een of meer van de bestands shares waarvan u een back-up wilt maken en klik op **OK**.

6. Nadat u de bestandsshares hebt geselecteerd, schakelt het menu Back-up over naar het **Back-upbeleid**. In dit menu selecteert u een bestaand back-upbeleid of maakt u een nieuwe. Vervolgens klikt u op **Back-up inschakelen**.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/apply-backup-policy.png)

    Wanneer er een back-upbeleid is ingesteld, wordt er een momentopname van de bestandsshares genomen op het geplande tijdstip en wordt het herstelpunt gedurende de gekozen periode bewaard.

## <a name="create-an-on-demand-backup"></a>Een back-up op aanvraag maken

Misschien wilt u zo nu en dan een momentopname van of herstelpunt voor een back-up maken buiten de tijden die zijn vastgelegd in het back-upbeleid. Een veelvoorkomend moment voor het genereren van een back-up op aanvraag, is vlak nadat u het back-upbeleid hebt geconfigureerd. Afhankelijk van de planning in het back-upbeleid, kan het uren of dagen duren totdat er een momentopname wordt gemaakt. Als u uw gegevens wilt beschermen totdat het back-upbeleid actief wordt, start u een back-up op aanvraag. Het maken van een back-up op aanvraag is vaak vereist voordat u geplande wijzigingen aanbrengt aan uw bestands shares.

### <a name="to-create-an-on-demand-backup"></a>Een back-up op aanvraag maken

1. Open de Recovery Services-kluis waarin de herstelpunten van de bestandsshares staan en klik op **Back-upitems**. De lijst met typen back-upitems wordt weergegeven.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/list-of-backup-items.png)

2. Selecteer **Azure Storage (Azure-bestanden)** in de lijst. De lijst met Azure-bestandsshares wordt weergegeven.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Selecteer de gewenste bestandsshare uit de lijst met Azure-bestandsshares. De details van het **back-Upitem** worden weer gegeven. Klik in het menu **back-upitem** op **Nu back-up maken**. Omdat dit een back-uptaak op aanvraag is, is er geen bewaarbeleid gekoppeld aan het herstelpunt.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/backup-item-menu.png)

4. Het dialoogvenster **Nu back-up maken** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/backup-now-menu.png)

5. Klik op **OK** om de back-uptaak op aanvraag te bevestigen.

## <a name="restore-from-backup-of-azure-file-share"></a>Herstellen vanuit back-up van Azure-bestandsshare

Als u een volledige bestandsshare of afzonderlijke bestanden of mappen van een herstelpunt wilt herstellen, leest u over Back-upitem in het vorige gedeelte. Kies **Share herstellen** om een volledige bestandsshare vanaf een gewenst moment te herstellen. Selecteer in de lijst met herstelpunten die wordt weergegeven een herstelpunt dat uw huidige bestandsshare kan overschrijven of dat de bestandsshare naar een alternatieve bestandsshare in dezelfde regio kan herstellen.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Afzonderlijke bestanden of mappen terugzetten vanuit een back-up van Azure-bestandsshares

Azure Backup biedt de mogelijkheid om naar een herstelpunt te bladeren binnen Azure Portal. Klik op de pagina Back-upitem op Bestandsherstel en kies uit de lijst met herstelpunten om een bestand of map te herstellen. Selecteer het Hersteldoel en klik op **Bestand selecteren** om door het herstelpunt te bladeren. Selecteer het bestand of de map en klik op **Herstellen**.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Back-ups van Azure-bestandsshares beheren

U kunt verschillende beheertaken voor back-ups van bestandsshares uitvoeren op de pagina **Back-uptaken**, met inbegrip van:

* [Taken controleren](backup-azure-files.md#monitor-jobs)
* [Een nieuw beleid maken](backup-azure-files.md#create-a-new-policy)
* [De beveiliging voor een bestandsshare stoppen](backup-azure-files.md#stop-protecting-an-azure-file-share)
* [De beveiliging voor een bestandsshare hervatten](backup-azure-files.md#resume-protection-for-azure-file-share)
* [Back-upgegevens verwijderen](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Taken controleren

U kunt de voortgang van alle taken bijhouden op de pagina **Back-uptaken**.

De pagina **Back-uptaken** openen:

* Open de Recovery Services-kluis die u wilt controleren en klik in het menu Recovery Services-kluis op **Taken** en vervolgens op **Back-uptaken**.

   ![Selecteer de taak die u wilt controleren](./media/backup-file-shares/open-backup-jobs.png)

    De lijst met back-uptaken en de status van deze taken wordt weergegeven.

    ![Selecteer de taak die u wilt controleren](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Een nieuw beleid maken

U kunt vanuit **Back-upbeleid** van de Recovery Services-kluis een nieuw beleid maken voor het maken van back-ups van Azure-bestandsshares. Alle beleidsregels die worden gemaakt tijdens het configureren van Back-up maken voor bestandsshares, worden weergegeven met het type beleid Azure-bestandsshare.

Het bestaande back-upbeleid weergeven:

* Open de gewenste Recovery Services-kluis en klik in het menu Recovery Services-kluis op **Back-upbeleid**. Alle back-upbeleidsregels worden vermeld.

   ![Selecteer de taak die u wilt controleren](./media/backup-file-shares/list-of-backup-policies.png)

Een nieuw back-upbeleid maken:

1. Klik in het menu Recovery Services-kluis op **Back-upbeleid**.
2. Klik in de lijst met back-upbeleidsregels op **Toevoegen**.

   ![Selecteer de taak die u wilt controleren](./media/backup-file-shares/new-backup-policy.png)

3. Selecteer in het menu **Toevoegen** de optie **Azure-bestandsshare**. Het menu Back-upbeleid voor een Azure-bestandsshare wordt geopend. Geef de naam voor het beleid, de back-upfrequentie en de bewaartermijn voor de herstelpunten op. Klik op OK als u het beleid hebt gedefinieerd.

   ![Selecteer de taak die u wilt controleren](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>De beveiliging van een Azure-bestandsshare stoppen

Als u wilt stoppen met het beveiligen van een Azure-bestandsshare, wordt u gevraagd of u de herstelpunten wilt bewaren. Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen
* Alle toekomstige back-uptaken stoppen maar de herstelpunten behouden

Mogelijk zijn er kosten verbonden aan het blijven opslaan van de herstelpunten, aangezien de onderliggende momentopnamen die zijn gemaakt door Azure Backup worden behouden. Het voordeel van het bewaren van de herstelpunten is dat u de bestandsshare later kunt herstellen indien gewenst. Bekijk de prijsgegevens voor informatie over de kosten voor het behouden van de herstelpunten. Als u alle herstelpunten verwijdert, kunt u de bestandsshare niet terugzetten.

Ga als volgt te werk om de beveiliging van een Azure-bestandsshare te stoppen:

1. Open de Recovery Services-kluis waarin de herstelpunten van de bestandsshares staan en klik op **Back-upitems**. De lijst met typen back-upitems wordt weergegeven.

   ![Klik op Back-up om de Azure-bestandsshare aan de kluis te koppelen](./media/backup-file-shares/list-of-backup-items.png)

2. In de lijst **Type back-upbeheer** selecteert u **Azure Storage (Azure-bestanden)** . De lijst met back-upitems voor (Azure Storage (Azure-bestanden)) wordt weergegeven.

   ![Klik op het item om het extra menu te openen](./media/backup-file-shares/azure-file-share-backup-items.png)

3. Selecteer in de lijst met back-upitems (Azure Storage (Azure-bestanden)) het back-upitem dat u wilt stoppen.

4. Klik in de Azure-bestandsshares op het menu **Meer** en selecteer **Back-up stoppen**.

   ![Klik op het item om het extra menu te openen](./media/backup-file-shares/stop-backup.png)

5. Kies in het menu Back-up stoppen de optie **Behouden** of **Back-upgegevens verwijderen** en klik op **Back-up stoppen**.

   ![Klik op het item om het extra menu te openen](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>De beveiliging van een Azure-bestandsshare hervatten

Als u de optie Back-upgegevens behouden hebt gekozen bij het stoppen van de beveiliging voor de bestandsshare, is het mogelijk om de beveiliging te hervatten. Als u de optie **Back-upgegevens verwijderen** hebt gekozen, kan de beveiliging voor de bestandsshare niet worden hervat.

Als u de beveiliging voor de bestandsshare wilt hervatten, gaat u naar het back-upitem en klikt u op Back-up hervatten. Het back-upbeleid wordt geopend en u kunt een beleid kiezen voor het hervatten van de back-up.

   ![Selecteer de taak die u wilt controleren](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Back-upgegevens verwijderen

U kunt de back-up van een bestands share verwijderen tijdens de taak back-up stoppen of op elk moment nadat u de beveiliging hebt gestopt. Het kan zelfs handig zijn om een aantal dagen of weken te wachten voordat u de herstelpunten verwijdert. Wanneer u back-upgegevens verwijdert, kunt u niet specifieke herstelpunten kiezen om te verwijderen, in tegenstelling tot bij het herstellen van herstelpunten. Als uw back-gegevens verwijdert, verwijdert u alle herstelpunten die aan het item zijn gekoppeld.

Bij de volgende procedure wordt ervan uitgegaan dat de back-uptaak voor de virtuele machine is gestopt. Nadat de back-uptaak is gestopt, zijn de opties Back-up hervatten en Back-upgegevens verwijderen beschikbaar in het dashboard Back-upitem. Klik op Back-upgegevens verwijderen en typ de naam van de bestandsshare om het verwijderen te bevestigen. Geef desgewenst een reden voor verwijdering of een opmerking op.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Azure-bestands shares.

* [Vragen over back-ups maken van Azure-bestandsshares](backup-azure-files-faq.md)
* [Problemen oplossen met back-ups maken van Azure-bestandsshares](troubleshoot-azure-files.md)
