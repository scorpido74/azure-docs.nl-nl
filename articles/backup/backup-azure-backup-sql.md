---
title: Back-ups maken van SQL Server naar Azure als een DPM-workload
description: Een inleiding tot het maken van back-ups van SQL Server-data bases met de Azure Backup-Service
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: ea55081d6f3b58c6c64c16e64c7a9d0f673ec196
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025398"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Back-ups maken van SQL Server naar Azure als een DPM-workload

Dit artikel leidt u door de configuratie stappen voor het maken van back-ups van SQL Server-data bases met behulp van Azure Backup.

Als u back-ups wilt maken van SQL Server-data bases naar Azure, hebt u een Azure-account nodig. Als u geen account hebt, kunt u binnen slechts een paar minuten een gratis proef account maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

Het beheer van SQL Server database back-up naar Azure en het herstel van Azure bestaat uit drie stappen:

1. Maak een back-upbeleid om SQL Server data bases te beveiligen in Azure.
2. Back-ups op aanvraag maken naar Azure.
3. De data base herstellen vanuit Azure.

## <a name="before-you-start"></a>Voordat u begint

Voordat u begint, moet u ervoor zorgen dat aan alle [vereisten](backup-azure-dpm-introduction.md#prerequisites-and-limitations) voor het gebruik van Microsoft Azure backup voor het beveiligen van werk belastingen is voldaan. De vereisten omvatten taken zoals: het maken van een back-upkluis, het downloaden van kluis referenties, het installeren van de Azure Backup Agent en het registreren van de server met de kluis.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Een back-upbeleid maken om SQL Server data bases te beveiligen in azure

1. Klik op de DPM-server op de werk ruimte **beveiliging** .
2. Klik op het lint met hulp middelen op **Nieuw** om een nieuwe beveiligings groep te maken.

    ![Beveiligings groep maken](./media/backup-azure-backup-sql/protection-group.png)
3. DPM toont het Start scherm met de richt lijnen voor het maken van een **beveiligings groep**. Klik op **Volgende**.
4. Selecteer **servers**.

    ![Type beveiligings groep selecteren-' servers '](./media/backup-azure-backup-sql/pg-servers.png)
5. Vouw de SQL Server machine uit waarvan de data bases waarvan een back-up moet worden gemaakt, aanwezig zijn. DPM toont verschillende gegevens bronnen waarvan een back-up van die server kan worden gemaakt. Vouw de **alle SQL-shares** uit en selecteer de data bases (in dit geval hebben we Report Server $ MSDPM2012 en Report Server $ MSDPM2012TempDB) geselecteerd waarvan een back-up moet worden gemaakt. Klik op **Volgende**.

    ![SQL-data base selecteren](./media/backup-azure-backup-sql/pg-databases.png)
6. Geef een naam op voor de beveiligings groep en schakel het selectie vakje **Ik wil online beveiliging** in.

    ![Methode voor gegevens beveiliging-korte termijn schijf & online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. In het scherm doelen voor de **korte termijn opgeven** , neemt u de benodigde invoer op om back-uppunten te maken op schijf.

    Hier ziet u dat het **Bewaar bereik** is ingesteld op *5 dagen*, wordt de **synchronisatie frequentie** elke *15 minuten*ingesteld op de frequentie waarmee back-ups worden gemaakt. **Snelle volledige back-up** is ingesteld op *8:00 P. M*.

    ![Doelen voor de korte termijn](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Om 8:00 uur (volgens de scherm invoer) wordt er elke dag een back-uppunt gemaakt door de gegevens over te dragen die zijn gewijzigd sinds de vorige dag van het 8:00 PM-back-uppunt. Dit proces heet **snelle volledige back-up**. Hoewel de transactie Logboeken om de 15 minuten worden gesynchroniseerd, als de Data Base op 9:00 uur moet worden hersteld, wordt het punt gemaakt door de logboeken van de laatste snelle volledige back-uppunt (8 p.m. in dit geval) opnieuw af te spelen.
   >
   >

8. Klik op **Volgende**

    DPM toont de totale beschik bare opslag ruimte en het mogelijke schijfruimte gebruik.

    ![Schijf toewijzing](./media/backup-azure-backup-sql/pg-storage.png)

    Standaard maakt DPM één volume per gegevens bron (SQL Server-Data Base) die wordt gebruikt voor de eerste back-upkopie. Met deze methode wordt de DPM-beveiliging door de LDM (Logical Disk Manager) beperkt tot 300-gegevens bronnen (SQL Server-data bases). Als u deze beperking wilt omzeilen, selecteert u de optie **gegevens samen voegen in DPM-opslag groep**. Als u deze optie gebruikt, gebruikt DPM één volume voor meerdere gegevens bronnen, waardoor DPM Maxi maal 2000 SQL-data bases kan beveiligen.

    Als **de optie volumes automatisch verg Roten** is geselecteerd, kan DPM het verhoogde back-upvolume account maken naarmate de productie gegevens groeien. Als **de optie volumes automatisch verg Roten** is niet geselecteerd, beperkt DPM de back-upopslag die wordt gebruikt voor de gegevens bronnen in de beveiligings groep.
9. Beheerders krijgen de keuze om deze eerste back-up hand matig te verplaatsen (uit het netwerk) om te voor komen dat er band breedte overbelast of via het netwerk. Ze kunnen ook de tijd configureren waarop de eerste overdracht kan plaatsvinden. Klik op **Volgende**.

    ![Methode van initiële replicatie](./media/backup-azure-backup-sql/pg-manual.png)

    Voor de eerste back-up is overdracht van de volledige gegevens bron (SQL Server-Data Base) van de productie server (SQL Server machine) naar de DPM-server vereist. Deze gegevens zijn mogelijk erg groot en het overdragen van de gegevens via het netwerk kan de band breedte overschrijden. Daarom kunnen beheerders ervoor kiezen om de eerste back-up te verplaatsen: **hand matig** (met behulp van Verwissel bare media) om te voor komen dat er band breedte overbelast is of **automatisch via het netwerk** (op een bepaald moment).

    Zodra de eerste back-up is voltooid, zijn de rest van de back-ups incrementele back-ups op de eerste back-upkopie. Incrementele back-ups zijn vaak klein en kunnen eenvoudig via het netwerk worden overgedragen.
10. Kies wanneer u wilt dat de consistentie controle wordt uitgevoerd en klik op **volgende**.

    ![Consistentie controle](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan een consistentie controle uitvoeren om de integriteit van het back-uppunt te controleren. Het berekent de controlesom van het back-upbestand op de productie server (SQL Server machine in dit scenario) en de gegevens van de back-up voor dat bestand op DPM. Als er een conflict optreedt, wordt ervan uitgegaan dat het back-upbestand op DPM is beschadigd. DPM verholpen de back-upgegevens door de blokken te verzenden die overeenkomen met de controlesom komen niet overeen. Omdat de consistentie controle een prestatie-intensieve bewerking is, kunnen beheerders de consistentie controle plannen of automatisch uitvoeren.
11. Als u de online beveiliging van de gegevens bronnen wilt opgeven, selecteert u de data bases die moeten worden beveiligd met Azure en klikt u op **volgende**.

    ![Gegevens bronnen selecteren](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Beheerders kunnen back-upscheman en bewaar beleid voor hun organisatie beleid kiezen.

    ![Plannen en bewaren](./media/backup-azure-backup-sql/pg-schedule.png)

    In dit voor beeld worden back-ups eenmaal per dag om 12:00 uur en 8 uur (onderste deel van het scherm) gemaakt.

    > [!NOTE]
    > Het is een goed idee om een aantal herstel punten voor de korte termijn te hebben op de schijf, voor snel herstel. Deze herstel punten worden gebruikt voor ' operationeel herstel '. Azure fungeert als een goede externe locatie met hogere Sla's en gegarandeerde Beschik baarheid.
    >
    >

    **Best Practice**: Zorg ervoor dat de Azure-back-ups zijn gepland na het volt ooien van de back-ups van de lokale schijf met behulp van DPM. Hiermee wordt de meest recente schijf back-up naar Azure gekopieerd.

13. Kies het schema voor het Bewaar beleid. De details over de manier waarop het Bewaar beleid werkt, vindt u in [gebruik Azure backup om uw tape-infrastructuur artikel te vervangen](backup-azure-backup-cloud-as-tape.md).

    ![Bewaarbeleid](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In dit voorbeeld:

    * Back-ups worden eenmaal per dag om 12:00 uur en 8 uur (onderste deel van het scherm) gemaakt en blijven 180 dagen bewaard.
    * De back-up op zaterdag om 12:00 uur wordt 104 weken bewaard
    * De back-up op de laatste zaterdag om 12:00 uur wordt gedurende 60 maanden bewaard
    * De back-up op de laatste zaterdag van maart om 12:00 uur wordt gedurende tien jaar bewaard
14. Klik op **volgende** en selecteer de juiste optie om de eerste back-up naar Azure te verplaatsen. U kunt **automatisch kiezen via het netwerk** of **offline back-up**.

    * **Automatisch via het netwerk** worden de back-upgegevens overgebracht naar Azure volgens het schema dat voor back-up is gekozen.
    * Hoe **offline back-up** werkt, wordt uitgelegd in [overzicht van offline back-ups](offline-backup-overview.md).

    Kies het relevante overdrachts mechanisme om de eerste back-up naar Azure te verzenden en klik op **volgende**.
15. Wanneer u de details van het beleid in het scherm **samen vatting** hebt bekeken, klikt u op de knop **groep maken** om de werk stroom te volt ooien. U kunt op de knop **sluiten** klikken en de voortgang van de taak in de werk ruimte bewaking bewaken.

    ![Bezig met het maken van een beveiligings groep](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Back-ups op aanvraag van een SQL Server Data Base

Terwijl de vorige stappen een back-upbeleid hebben gemaakt, wordt er alleen een ' herstel punt ' gemaakt wanneer de eerste back-up wordt uitgevoerd. In plaats van te wachten tot de scheduler wordt gestart, activeren de volgende stappen om hand matig een herstel punt te maken.

1. Wacht totdat de status van de beveiligings groep op **OK** staat voor de Data Base voordat u het herstel punt maakt.

    ![Leden van beveiligings groep](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klik met de rechter muisknop op de data base en selecteer **herstel punt maken**.

    ![Online herstel punt maken](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Kies **online beveiliging** in de vervolg keuzelijst en klik op **OK**. Hiermee wordt het maken van een herstel punt in azure gestart.

    ![Herstel punt maken](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. U kunt de voortgang van de taak weer geven in de werk ruimte **bewaking** , waar u een taak in voortgang vindt, zoals in de volgende afbeelding.

    ![Bewakings console](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Een SQL Server-Data Base herstellen vanuit Azure

De volgende stappen zijn vereist om een beveiligde entiteit (SQL Server-Data Base) te herstellen vanuit Azure.

1. Open de beheer console van DPM-server. Ga naar de **herstel** werkruimte, waar u de servers kunt zien waarvan een back-up is gemaakt door DPM. Blader door de vereiste data base (in dit geval Report Server $ MSDPM2012). Selecteer een **herstel** tijd die eindigt op **online**.

    ![Herstel punt selecteren](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klik met de rechter muisknop op de naam van de data base en klik op **herstellen**.

    ![Herstellen vanuit Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM toont de details van het herstel punt. Klik op **Volgende**. Als u de Data Base wilt overschrijven, selecteert u het herstel type **herstellen naar het oorspronkelijke exemplaar van SQL Server**. Klik op **Volgende**.

    ![Herstellen naar de oorspronkelijke locatie](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In dit voor beeld staat DPM het herstellen van de Data Base naar een andere SQL Server exemplaar of naar een zelfstandige netwerkmap toe.
4. In het scherm **herstel opties opgeven** kunt u de herstel opties zoals netwerk bandbreedte gebruiken selecteren om de band breedte te beperken die wordt gebruikt voor herstel. Klik op **Volgende**.
5. In het scherm **samen vatting** ziet u alle beschik bare herstel configuraties tot nu toe. Klik op **herstellen**.

    De herstel status toont de data base die wordt hersteld. U kunt op **sluiten** klikken om de wizard te sluiten en de voortgang in de werk ruimte **bewaking** weer te geven.

    ![Herstel proces initiëren](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Wanneer het herstel is voltooid, is de herstelde data base toepassings consistent.

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over Azure Backup](backup-azure-backup-faq.md)
