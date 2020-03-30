---
title: Back-ups maken van Azure-bestandsshares in de Azure-portal
description: Meer informatie over het gebruik van de Azure-portal om een back-up te maken van Azure-bestandsshares in de kluis Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938247"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Back-ups maken van Azure-bestandsshares in een vault van Recovery Services

In dit artikel wordt uitgelegd hoe u de Azure-portal gebruiken om een back-up te maken van [Azure-bestandsshares](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

In dit artikel leer je hoe je:

* Maak een Recovery Services-kluis.
* Ontdek bestandsshares en configureer back-ups.
* Voer een on-demand back-uptaak uit om een herstelpunt te maken.

## <a name="prerequisites"></a>Vereisten

* Een kluis [van Recovery Services](#create-a-recovery-services-vault) identificeren of maken in dezelfde regio als het opslagaccount waarmee het bestandsshare wordt host.
* Controleer of de bestandsshare aanwezig is in een van de [ondersteunde opslagaccounttypen](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Beperkingen voor back-up van Azure-bestandsshare tijdens preview

Back-up voor Azure-bestandsshares is in preview. Azure-bestandsshares worden ondersteund voor de accounts voor algemeen gebruik v1 en v2. Dit zijn de beperkingen voor het maken van back-ups van Azure-bestandsshares:

* Ondersteuning voor back-ups van Azure-bestandsshares in opslagaccounts met [ZRS-replicatie (Zone redundante opslag)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) is momenteel beperkt tot [deze regio's.](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)
* Azure Backup ondersteunt momenteel het configureren van geplande eenmaal dagelijkse back-ups van Azure-bestandsshares.
* Het maximumaantal geplande back-ups per dag is één.
* Het maximumaantal on-demand back-ups per dag is vier.
* Gebruik [bronvergrendelingen](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) op het opslagaccount om te voorkomen dat back-ups per ongeluk worden verwijderd in uw vault van Recovery Services.
* Verwijder geen momentopnamen die zijn gemaakt door Azure Backup. Het verwijderen van momentopnamen kan leiden tot verlies van herstelpunten of het herstellen van fouten.
* Verwijder geen bestandsshares die zijn beveiligd met Azure Backup. De huidige oplossing verwijdert alle momentopnamen die zijn gemaakt door Azure Backup nadat het bestandsaandeel is verwijderd, zodat alle herstelpunten verloren gaan.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Opslagreplicatie wijzigen

Standaard gebruiken kluizen [georedundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U [lokaal redundante opslag (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken als een goedkope optie.

Ga als lid van het type opslagreplicatie:

1. Selecteer **eigenschappen** in de nieuwe kluis onder de sectie **Instellingen.**

1. Selecteer Op de pagina **Eigenschappen** onder **Back-upconfiguratie**de optie **Bijwerken**.

1. Selecteer het type opslagreplicatie en selecteer **Opslaan**.

    ![Back-upconfiguratie bijwerken](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> U het type opslagreplicatie niet wijzigen nadat de kluis is ingesteld en bevat back-upitems. Als u dit wilt doen, moet u de kluis opnieuw maken.
>

## <a name="discover-file-shares-and-configure-backup"></a>Bestandsshares ontdekken en back-upconfigureren

1. Open in de [Azure-portal](https://portal.azure.com/)de kluis Recovery Services die u wilt gebruiken om een back-up van het bestandsaandeel te maken.

1. Selecteer **+Back-up**in het **kluisdashboard van Herstelservices** .

   ![Recovery Services-kluis](./media/backup-afs/recovery-services-vault.png)

    a. Stel in **Back-updoel**de set **Azure** **Waar wordt uw werkbelasting uitgevoerd?**

    ![Azure File Share kiezen als back-updoel](./media/backup-afs/backup-goal.png)

    b.  Selecteer Azure File Share in **Wat wilt u een back-up**maken **in** de vervolgkeuzelijst.

    c.  Selecteer **Back-up** om de Azure-bestandsshare-extensie in de kluis te registreren.

    ![Selecteer Back-up om de Azure-bestandsshare te koppelen aan de kluis](./media/backup-afs/register-extension.png)

1. Nadat u **Back-up hebt**geselecteerd, wordt het deelvenster **Back-up** geopend en wordt u gevraagd een opslagaccount te selecteren in een lijst met gedetecteerde ondersteunde opslagaccounts. Ze zijn gekoppeld aan deze kluis of aanwezig in dezelfde regio als de kluis, maar nog niet gekoppeld aan een kluis van Recovery Services.

   ![Opslagaccount selecteren](./media/backup-afs/select-storage-account.png)

1. Selecteer in de lijst met gedetecteerde opslagaccounts een account en selecteer **OK**. Azure zoekt in het opslagaccount naar bestandsshares waarvan een back-up kan worden gemaakt. Als u onlangs uw bestandsshares hebt toegevoegd en deze niet in de lijst ziet, moet u de bestandsshares enige tijd toestaan om de bestandsshares weer te geven.

    ![Bestandsshares ontdekken](./media/backup-afs/discovering-file-shares.png)

1. Selecteer in de lijst **Bestandsshares** een of meer van de bestandsshares waarvan u een back-up wilt maken. Selecteer **OK**.

1. Nadat u uw bestandsshares hebt gekozen, schakelt het menu **Back-up** over naar **Back-upbeleid**. Selecteer in dit menu een bestaand back-upbeleid of maak een nieuw back-upbeleid. Selecteer vervolgens **Back-up inschakelen**.

    ![Back-upbeleid selecteren](./media/backup-afs/select-backup-policy.png)

Nadat u een back-upbeleid hebt ingesteld, wordt een momentopname van de bestandsshares gemaakt op het geplande tijdstip. Het herstelpunt blijft ook behouden voor de gekozen periode.

## <a name="create-an-on-demand-backup"></a>Een back-up op aanvraag maken

Af en toe wilt u mogelijk een back-upmomentopname of herstelpunt genereren buiten de tijden die zijn gepland in het back-upbeleid. Een veelvoorkomende reden om een on-demand back-up te genereren, is direct nadat u het back-upbeleid hebt geconfigureerd. Op basis van de planning in het back-upbeleid kan het uren of dagen duren voordat een momentopname is gemaakt. Als u uw gegevens wilt beschermen totdat het back-upbeleid actief wordt, start u een back-up op aanvraag. Het maken van een on-demand back-up is vaak vereist voordat u geplande wijzigingen aanbrengt in uw bestandsshares.

### <a name="create-a-backup-job-on-demand"></a>Een back-uptaak op aanvraag maken

1. Open de kluis Van Recovery Services die u hebt gebruikt om een back-up van uw bestandsshare te maken. Selecteer **back-upitems** in het deelvenster **Overzicht** onder de sectie **Beveiligde items.**

   ![Back-upitems selecteren](./media/backup-afs/backup-items.png)

1. Nadat u **Back-upitems**hebt geselecteerd, wordt er een nieuw deelvenster weergegeven met alle **back-upbeheertypen** naast het deelvenster **Overzicht.**

   ![Lijst met back-upbeheertypen](./media/backup-afs/backup-management-types.png)

1. Selecteer **Azure Storage (Azure Files)** in de lijst **Type back-upbeheer.** U ziet een lijst met alle bestandsshares en de bijbehorende opslagaccounts waarvan met deze kluis een back-up wordt gemaakt.

   ![Azure Storage-back-upitems (Azure Storage)](./media/backup-afs/azure-files-backup-items.png)

1. Selecteer in de lijst met Azure-bestandsshares de gewenste bestandsshare. De gegevens **van het back-upitem** worden weergegeven. Selecteer **nu back-up**in het menu **Back-upitem** . Omdat deze back-uptaak op aanvraag is, is er geen bewaarbeleid gekoppeld aan het herstelpunt.

   ![Selecteer nu Back-up](./media/backup-afs/backup-now.png)

1. Het deelvenster **Back-up nu** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren. U maximaal 10 jaar vasthouden voor een on-demand back-up.

   ![Bewaardatum kiezen](./media/backup-afs/retention-date.png)

1. Selecteer **OK** om de on-demand back-uptaak te bevestigen die wordt uitgevoerd.

1. Controleer de portalmeldingen om de voltooiing van de back-uptaak bij te houden. U de voortgang van de taak in het dashboard van de kluis volgen. Selecteer **Back-uptaken** > **in uitvoering**.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:
* [Azure-bestandsshares herstellen](restore-afs.md)
* [Back-ups voor Azure-bestandsshare beheren](manage-afs-backup.md)
