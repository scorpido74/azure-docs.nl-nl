---
title: Back-ups van Azure-bestandsshares beheren
description: In dit artikel worden veelvoorkomende taken beschreven voor het beheren en bewaken van de Azure-bestandsshares waarvan een back-up wordt gemaakt door Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247655"
---
# <a name="manage-azure-file-share-backups"></a>Back-ups van Azure-bestandsshares beheren

In dit artikel worden veelvoorkomende taken beschreven voor het beheren en bewaken van de Azure-bestandsshares waarvan een back-up wordt gemaakt door [Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview) U leert hoe u beheertaken uitvoert in de kluis van Recovery Services.

## <a name="monitor-jobs"></a>Taken controleren

Wanneer u een back-up- of herstelbewerking activeert, maakt de back-upservice een taak voor het bijhouden. U kunt de voortgang van alle taken bijhouden op de pagina **Back-uptaken**.

De pagina **Back-uptaken** openen:

1. Open de kluis Recovery Services die u hebt gebruikt om back-ups voor uw bestandsshares te configureren. Selecteer **back-uptaken** in het deelvenster **Overzicht** onder de sectie **Controle.**

   ![Back-uptaken in de sectie Controle](./media/manage-afs-backup/backup-jobs.png)

1. Nadat u **OK hebt**geselecteerd, geeft het deelvenster **Back-uptaken** de status van alle taken weer. Selecteer de werkbelastingnaam die overeenkomt met het bestandsaandeel dat u wilt controleren.

   ![Naam werkbelasting](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Een nieuw beleid maken

U een nieuw beleid maken om back-ups te maken van Azure-bestandsshares vanuit het gedeelte **Back-upbeleid** van de kluis Recovery Services. Alle beleidsregels die zijn gemaakt wanneer u een back-up voor bestandsshares hebt geconfigureerd, worden weergegeven met het **beleidstype** als **Azure File Share**.

Ga als u het bestaande back-upbeleid weergeven:

1. Open de kluis Recovery Services die u hebt gebruikt om de back-up voor het bestandsaandeel te configureren. Selecteer **back-upbeleid** in het kluismenu Herstelservices onder de sectie **Beheren.** Alle back-upbeleidsregels die in de kluis zijn geconfigureerd, worden weergegeven.

   ![Alle back-upbeleid](./media/manage-afs-backup/all-backup-policies.png)

1. Als u beleid wilt weergeven dat specifiek is voor **Azure File Share,** selecteert u **Azure File Share** in de vervolgkeuzelijst rechtsboven.

   ![Azure-bestandsshare selecteren](./media/manage-afs-backup/azure-file-share.png)

Ga als lid van het volgende over een nieuw back-upbeleid:

1. Selecteer in het deelvenster **Back-upbeleid** de optie **+ Toevoegen**.

   ![Nieuw back-upbeleid](./media/manage-afs-backup/new-backup-policy.png)

1. Selecteer **Azure** File **Share** in het deelvenster Toevoegen als **beleidstype**. Het **deelvenster Back-upbeleid** voor **Azure File Share** wordt geopend. Geef de beleidsnaam, back-upfrequentie en het bewaarbereik voor de herstelpunten op. Nadat u het beleid hebt gedefinieerd, selecteert u **OK**.

   ![Het back-upbeleid definiëren](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Beleid wijzigen

U een back-upbeleid wijzigen om de back-upfrequentie of het bewaarbereik te wijzigen.

Ga als u een beleid wijzigen:

1. Open de kluis Recovery Services die u hebt gebruikt om de back-up voor het bestandsaandeel te configureren. Selecteer **back-upbeleid** in het kluismenu Herstelservices onder de sectie **Beheren.** Alle back-upbeleidsregels die in de kluis zijn geconfigureerd, worden weergegeven.

   ![Alle back-upbeleidsregels in de kluis](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Als u beleid wilt weergeven dat specifiek is voor een Azure-bestandsshare, selecteert u **Azure File Share** in de vervolgkeuzelijst rechtsboven. Selecteer het back-upbeleid dat u wilt wijzigen.

   ![Azure-bestandsshare te wijzigen](./media/manage-afs-backup/azure-file-share-modify.png)

1. Het deelvenster **Planning** wordt geopend. Bewerk het **back-upschema** en **het bewaarbereik** naar behoefte en selecteer **Opslaan**. U ziet een bericht 'Bijwerken in uitvoering' in het deelvenster. Nadat de beleidswijzigingen zijn bijgewerkt, ziet u het bericht 'Het back-upbeleid met succes hebben bijgewerkt'.

   ![Het gewijzigde beleid opslaan](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Stop alle toekomstige back-uptaken en *verwijder alle herstelpunten.*
* Stop alle toekomstige back-uptaken, maar *laat de herstelpunten achter.*

Er kunnen kosten verbonden zijn aan het verlaten van de herstelpunten in de opslag, omdat de onderliggende momentopnamen die zijn gemaakt door Azure Backup behouden blijven. Het voordeel van het verlaten van de herstelpunten is dat u het bestandsaandeel later herstellen. Zie de [prijsdetails](https://azure.microsoft.com/pricing/details/backup/)voor informatie over de kosten van het verlaten van de herstelpunten. Als u besluit om alle herstelpunten te verwijderen, u het bestandsaandeel niet herstellen.

Ga als volgt te werk om de beveiliging van een Azure-bestandsshare te stoppen:

1. Open de kluis Recovery Services met herstelpunten voor bestandsshare. Selecteer **Back-upitems** onder de sectie **Beveiligde items.** De lijst met typen back-upitems wordt weergegeven.

   ![Back-upitems maken](./media/manage-afs-backup/backup-items.png)

1. In de lijst **Type back-upbeheer** selecteert u **Azure Storage (Azure-bestanden)**. De lijst **Back-upitems (Azure Storage (Azure Files))** wordt weergegeven.

   ![Azure Storage selecteren (Azure-bestanden)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Selecteer in de lijst **Back-upitems (Azure Storage (Azure Files))** het back-upitem waarvoor u de beveiliging wilt stoppen.

1. Selecteer de optie **Back-up stoppen.**

   ![Back-up stoppen selecteren](./media/manage-afs-backup/stop-backup.png)

1. Selecteer **back-upgegevens behouden** of **Back-upgegevens verwijderen**in het deelvenster **Back-up** behouden. Selecteer vervolgens **Back-up stoppen**.

    ![Selecteer Back-upgegevens behouden of Back-upgegevens verwijderen](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare hervatten

Als de optie **Back-upgegevens behouden** is geselecteerd toen de beveiliging voor het bestandsaandeel is gestopt, is het mogelijk om de beveiliging te hervatten. Als de optie **Back-upgegevens verwijderen** is geselecteerd, kan de beveiliging voor het bestandsaandeel niet worden hervat.

Ga als het gaat om de beveiliging van de Azure-bestandsshare te hervatten:

1. Open de kluis Recovery Services met herstelpunten voor bestandsshare. Selecteer **Back-upitems** onder de sectie **Beveiligde items.** De lijst met typen back-upitems wordt weergegeven.

   ![Back-upitems voor cv](./media/manage-afs-backup/backup-items-resume.png)

1. In de lijst **Type back-upbeheer** selecteert u **Azure Storage (Azure-bestanden)**. De lijst **Back-upitems (Azure Storage (Azure Files))** wordt weergegeven.

   ![Lijst met Azure Storage (Azure-bestanden)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Selecteer in de lijst **Back-upitems (Azure Storage (Azure Files))** het back-upitem waarvoor u de beveiliging wilt hervatten.

1. Selecteer de optie **Back-up hervatten.**

   ![Back-up hervatten selecteren](./media/manage-afs-backup/resume-backup.png)

1. Het deelvenster **Back-upbeleid** wordt geopend. Selecteer een beleid van uw keuze om de back-up te hervatten.

1. Nadat u een back-upbeleid hebt geselecteerd, selecteert u **Opslaan**. U ziet een bericht 'Bijwerken in uitvoering' in de portal. Nadat de back-up is hervat, ziet u het bericht 'Updated backup policy for the Protected Azure File Share'.

   ![Back-upbeleid met succes bijgewerkt](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Back-upgegevens verwijderen

U de back-up van een bestandsshare verwijderen tijdens de **back-uptaak stoppen** of op elk gewenst moment nadat u de beveiliging hebt gestopt. Het kan nuttig zijn om dagen of zelfs weken te wachten voordat u de herstelpunten verwijdert. Wanneer u back-upgegevens verwijdert, u geen specifieke herstelpunten kiezen om te verwijderen. Als u besluit uw back-upgegevens te verwijderen, verwijdert u alle herstelpunten die aan het bestandsaandeel zijn gekoppeld.

De volgende procedure gaat ervan uit dat de beveiliging is gestopt voor het delen van bestanden.

Ga als het gaat om het verwijderen van back-upgegevens voor de Azure-bestandsshare:

1. Nadat de back-uptaak is gestopt, zijn de opties **Back-up hervatten** **en Back-upgegevens verwijderen** beschikbaar in het dashboard **Back-upitem.** Selecteer de optie **Back-upgegevens verwijderen.**

   ![Back-upgegevens verwijderen](./media/manage-afs-backup/delete-backup-data.png)

1. Het deelvenster **Back-upgegevens verwijderen** wordt geopend. Voer de naam van het bestandsaandeel in om verwijdering te bevestigen. Geef eventueel meer informatie in de vakken **Reden** of **Opmerkingen.** Nadat u zeker bent van het verwijderen van de back-upgegevens, selecteert u **Verwijderen**.

   ![Verwijdergegevens bevestigen](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Een opslagaccount uitschrijven

Als u uw bestandsaandelen in een bepaald opslagaccount wilt beschermen met behulp van een kluis met een andere herstelservices, stopt u eerst [de beveiliging van alle bestandsshares](#stop-protection-on-a-file-share) in die opslagaccount. Registreer het account vervolgens uit de kluis van de huidige herstelservices die wordt gebruikt voor bescherming.

De volgende procedure gaat ervan uit dat de beveiliging is gestopt voor alle bestandsshares in het opslagaccount dat u wilt uitschrijven.

Ga als u de registratievan het opslagaccount uitschrijven:

1. Open de kluis Van Herstelservices waar uw opslagaccount is geregistreerd.
1. Selecteer in het deelvenster **Overzicht** de optie **Back-upinfrastructuur** onder de sectie **Beheren.**

   ![Back-upinfrastructuur selecteren](./media/manage-afs-backup/backup-infrastructure.png)

1. Het deelvenster **Back-upinfrastructuur** wordt geopend. Selecteer **Opslagaccounts** onder de sectie **Azure Storage Accounts.**

   ![Opslagaccounts selecteren](./media/manage-afs-backup/storage-accounts.png)

1. Nadat u **Opslagaccounts hebt**geselecteerd, wordt een lijst met opslagaccounts weergegeven die bij de kluis zijn geregistreerd.
1. Klik met de rechtermuisknop op het opslagaccount dat u wilt uitschrijven en selecteer **Uitschrijven**.

   ![Registratie uitschrijven selecteren](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Problemen met azure-bestandsshares back-up oplossen](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)voor meer informatie.
