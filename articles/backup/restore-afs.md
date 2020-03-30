---
title: Azure-bestandsshares herstellen
description: Meer informatie over het gebruik van de Azure-portal om een volledige bestandsshare of specifieke bestanden te herstellen vanaf een herstelpunt dat is gemaakt door Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586846"
---
# <a name="restore-azure-file-shares"></a>Azure-bestandsshares herstellen

In dit artikel wordt uitgelegd hoe u de Azure-portal gebruiken om een volledige bestandsshare of specifieke bestanden te herstellen vanaf een herstelpunt dat is gemaakt door [Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview)

In dit artikel leer je hoe je:

* Een volledig Azure-bestandsshare herstellen.
* Afzonderlijke bestanden of mappen herstellen.
* Houd de status van herstelbewerking bij.

## <a name="steps-to-perform-a-restore-operation"></a>Stappen om een herstelbewerking uit te voeren

Voer deze stappen uit om een herstelbewerking uit te voeren.

### <a name="select-the-file-share-to-restore"></a>Selecteer de bestandsshare die u wilt herstellen

1. Open in de [Azure-portal](https://portal.azure.com/)de kluis Recovery Services die u hebt gebruikt om back-ups voor het bestandsaandeel te configureren.

1. Selecteer **back-upitems** in het overzichtsvenster onder de sectie **Beveiligde items.**

    ![Back-upitems selecteren](./media/restore-afs/backup-items.png)

1. Nadat u **Back-upitems**hebt geselecteerd, wordt een nieuw deelvenster met alle typen back-upbeheer naast het overzichtsvenster geopend.

    ![Back-upbeheertypen](./media/restore-afs/backup-management.png)

1. Selecteer **Azure Storage (Azure Files)** in het deelvenster **Back-upitems** onder **Type Back-upbeheer**. U ziet een lijst met alle bestandsshares en de bijbehorende opslagaccounts waarvan een back-up wordt gemaakt met behulp van deze kluis.

    ![Lijst van alle bestandsaandelen](./media/restore-afs/file-shares.png)

1. Selecteer in de lijst met Azure-bestandsshares de bestandsshare waarvoor u de herstelbewerking wilt uitvoeren.

### <a name="full-share-recovery"></a>Volledig herstel van het aandeel

U deze hersteloptie gebruiken om de volledige bestandsshare op de oorspronkelijke locatie of een alternatieve locatie te herstellen.

1. Selecteer de optie **Delen herstellen** in het deelvenster **Back-upitem** dat wordt weergegeven nadat u de bestandsshare hebt geselecteerd om te herstellen in stap 5 van de sectie [Het bestand delen selecteren om te herstellen.](#select-the-file-share-to-restore)

   ![Aandeel herstellen selecteren](./media/restore-afs/restore-share.png)

1. Nadat u **Delen herstellen hebt**geselecteerd, wordt het deelvenster **Herstel** geopend met een menu **Herstelpunt** met een lijst met herstelpunten die beschikbaar zijn voor de geselecteerde bestandsshare.

1. Selecteer het herstelpunt dat u wilt gebruiken om de herstelbewerking uit te voeren en selecteer **OK**.

    ![Herstelpunt selecteren](./media/restore-afs/restore-point.png)

1. Nadat u **OK hebt**geselecteerd, schakelt het menu Van het deelvenster **Herstellen** over naar Locatie **herstellen**. Geef **in Locatie herstellen**op waar of hoe u de gegevens herstellen. Selecteer een van de volgende twee opties:

    * **Oorspronkelijke locatie:** het volledige bestandsaandeel herstellen naar dezelfde locatie als de oorspronkelijke bron.
    * **Alternatieve locatie:** herstel de volledige bestandsshare naar een alternatieve locatie en houd de oorspronkelijke bestandsshare zoals het is.

#### <a name="restore-to-the-original-location"></a>Herstellen naar de oorspronkelijke locatie

1. Selecteer **Oorspronkelijke locatie** als **herstelbestemming**en selecteer of u wilt overslaan of overschrijven als er conflicten zijn. Nadat u de juiste selectie hebt gemaakt, selecteert u **OK**.

    ![Oorspronkelijke locatie selecteren](./media/restore-afs/original-location.png)

1. Selecteer **Herstellen** om de herstelbewerking te starten.

    ![Herstellen selecteren om te starten](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Herstellen naar een alternatieve locatie

1. Selecteer **Alternatieve locatie** als **herstelbestemming**.
1. Selecteer het doelopslagaccount waar u de back-upinhoud wilt herstellen in de vervolgkeuzelijst **Opslagaccount.**
1. In de vervolgkeuzelijst **Bestandsaandeel selecteren** worden de bestandsshares weergegeven die aanwezig zijn in het opslagaccount dat u in stap 2 hebt geselecteerd. Selecteer de bestandsshare waar u de inhoud met een back-up wilt herstellen.
1. Geef in het vak **Mapnaam** een mapnaam op die u wilt maken in het doelbestand delen met de herstelde inhoud.
1. Selecteer of u wilt overslaan of overschrijven als er conflicten zijn.
1. Nadat u de juiste waarden in alle vakken hebt ingevoerd, selecteert u **OK**.

    ![Alternatieve locatie selecteren](./media/restore-afs/alternate-location.png)

1. Selecteer **Herstellen** om de herstelbewerking te starten.

    ![Herstellen selecteren om te starten](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Herstel op itemniveau

U deze hersteloptie gebruiken om afzonderlijke bestanden of mappen op de oorspronkelijke locatie of een alternatieve locatie te herstellen.

1. Selecteer de optie **Bestandsherstel** in het deelvenster **Back-upitem** dat wordt weergegeven nadat u de bestandsshare hebt geselecteerd om te herstellen in stap 5 van de sectie [Het bestand delen selecteren om te herstellen.](#select-the-file-share-to-restore)

    ![Bestandherstel selecteren](./media/restore-afs/file-recovery.png)

1. Nadat u **Bestandsherstel**hebt geselecteerd, wordt het deelvenster **Herstel** geopend met een menu **Herstelpunt** met een lijst met herstelpunten die beschikbaar zijn voor de geselecteerde bestandsshare.

1. Selecteer het herstelpunt dat u wilt gebruiken om de herstelbewerking uit te voeren en selecteer **OK**.

    ![Herstelpunt selecteren](./media/restore-afs/restore-point.png)

1. Nadat u **OK hebt**geselecteerd, schakelt het menu van het herstelvenster over naar Locatie **herstellen**. Geef **in Locatie herstellen**op waar of hoe u de gegevens herstellen. Selecteer een van de volgende twee opties:

    * **Oorspronkelijke locatie:** herstel geselecteerde bestanden of mappen naar dezelfde bestandsshare als de oorspronkelijke bron.
    * **Alternatieve locatie:** herstel geselecteerde bestanden of mappen naar een alternatieve locatie en bewaar de oorspronkelijke inhoud van bestandsshare zoals die is.

#### <a name="restore-to-the-original-location"></a>Herstellen naar de oorspronkelijke locatie

1. Selecteer **Oorspronkelijke locatie** als **herstelbestemming**en selecteer of u wilt overslaan of overschrijven als er conflicten zijn.

    ![Oorspronkelijke locatie voor itemniveauherstel](./media/restore-afs/original-location-item-level.png)

1. Kies **Bestand selecteren** om de bestanden of mappen te selecteren die u wilt herstellen.

    ![Selecteer Bestand selecteren](./media/restore-afs/select-file.png)

1. Nadat u **Bestand selecteren**hebt gekozen , wordt in een deelvenster voor bestandsshare de inhoud weergegeven van het herstelpunt voor bestandsdelen dat u hebt geselecteerd voor herstel.

1. Schakel het selectievakje in dat overeenkomt met het bestand of de map die u wilt herstellen en kies **Selecteren**.

    ![Bestand of map selecteren](./media/restore-afs/select-file-folder.png)

1. Herhaal stap 2 tot en met 4 om meerdere bestanden of mappen te selecteren om te herstellen.
1. Nadat u alle items hebt geselecteerd die u wilt herstellen, selecteert u **OK**.

    ![Nadat u alle items hebt geselecteerd die u wilt herstellen, selecteert u OK](./media/restore-afs/after-selecting-items.png)

1. Selecteer **Herstellen** om de herstelbewerking te starten.

    ![Herstellen selecteren om te starten](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Herstellen naar een alternatieve locatie

1. Selecteer **Alternatieve locatie** als **herstelbestemming**.
1. Selecteer het doelopslagaccount waar u de back-upinhoud wilt herstellen in de vervolgkeuzelijst **Opslagaccount.**
1. In de vervolgkeuzelijst **Bestandsaandeel selecteren** worden de bestandsshares weergegeven die aanwezig zijn in het opslagaccount dat u in stap 2 hebt geselecteerd. Selecteer de bestandsshare waar u de inhoud met een back-up wilt herstellen.
1. Geef in het vak **Mapnaam** een mapnaam op die u wilt maken in het doelbestand delen met de herstelde inhoud.
1. Selecteer of u wilt overslaan of overschrijven als er conflicten zijn.
1. Kies **Bestand selecteren** om de bestanden of mappen te selecteren die u wilt herstellen.

    ![Items selecteren die u wilt herstellen naar alternatieve locatie](./media/restore-afs/restore-to-alternate-location.png)

1. Wanneer u **Bestand selecteren**kiest , wordt in een deelvenster voor bestandsshare de inhoud weergegeven van het herstelpunt voor bestandsshare dat u hebt geselecteerd voor herstel.
1. Schakel het selectievakje in dat overeenkomt met het bestand of de map die u wilt herstellen en kies **Selecteren**.

    ![Herstelbestemming selecteren](./media/restore-afs/recovery-destination.png)

1. Herhaal stap 6 tot en met 8 om meerdere bestanden of mappen te selecteren om te herstellen.
1. Nadat u alle items hebt geselecteerd die u wilt herstellen, selecteert u **OK**.

    ![Ok selecteren nadat u alle bestanden hebt geselecteerd](./media/restore-afs/after-selecting-all-items.png)

1. Selecteer **Herstellen** om de herstelbewerking te starten.

## <a name="track-a-restore-operation"></a>Een herstelbewerking bijhouden

Nadat u de herstelbewerking hebt geactiveerd, maakt de back-upservice een taak voor het bijhouden. Azure Backup geeft meldingen weer over de taak in de portal. Als u bewerkingen voor de taak wilt weergeven, selecteert u de hyperlink met meldingen.

![Hyperlink voor meldingen selecteren](./media/restore-afs/notifications-link.png)

U ook de herstelvoortgang vanuit de kluis Van Herstelservices controleren:

1. Open de kluis Recovery Services van waaruit u de herstelbewerking hebt geactiveerd.
1. Selecteer **back-uptaken** in het overzichtsvenster onder de sectie **Controle** om de status te zien van bewerkingen die worden uitgevoerd tegen verschillende workloads.

    ![Back-uptaken selecteren](./media/restore-afs/backup-jobs.png)

1. Selecteer de werkbelastingnaam die overeenkomt met uw bestandsshare om meer details over de herstelbewerking weer te geven, zoals **gegevens overdracht** en **aantal herstelde bestanden**.

    ![Bekijk herstelde details](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van back-ups voor azure-bestanden](manage-afs-backup.md).
