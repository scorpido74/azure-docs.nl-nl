---
title: Back-ups maken van SQL Server werk belastingen op Azure Stack
description: In dit artikel vindt u informatie over het configureren van Microsoft Azure Backup Server (MABS) om SQL Server-data bases op Azure Stack te beveiligen.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: b2d41bdccd67539205b74a0ce277b3b01a685c6c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84192984"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Back-up maken van SQL Server op Azure Stack

Gebruik dit artikel om Microsoft Azure Backup Server (MABS) te configureren om SQL Server-data bases op Azure Stack te beveiligen.

Het beheer van SQL Server database back-up naar Azure en het herstel van Azure bestaat uit drie stappen:

1. Een back-upbeleid maken om SQL Server-data bases te beveiligen
2. Back-ups op aanvraag maken
3. De data base herstellen vanaf schijven en Azure

## <a name="before-you-start"></a>Voordat u begint

[Azure backup server installeren en voorbereiden](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Een back-upbeleid maken om SQL Server data bases te beveiligen in azure

1. Klik in de Azure Backup Server gebruikers interface op de werk ruimte **beveiliging** .

2. Klik op het lint met hulp middelen op **Nieuw** om een nieuwe beveiligings groep te maken.

    ![Beveiligings groep maken](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server start de wizard beveiligings groep, die u helpt bij het maken van een **beveiligings groep**. Klik op **Volgende**.

3. Selecteer in het scherm **type beveiligings groep selecteren** de optie **servers**.

    ![Type beveiligings groep selecteren-' servers '](./media/backup-azure-backup-sql/pg-servers.png)

4. In het scherm **groeps leden selecteren** worden de verschillende gegevens bronnen weer gegeven in de lijst met beschik bare leden. Klik **+** om een map uit te vouwen en de submappen weer te geven. Klik op het selectie vakje om een item te selecteren.

    ![SQL-data base selecteren](./media/backup-azure-backup-sql/pg-databases.png)

    Alle geselecteerde items worden weer gegeven in de lijst geselecteerde leden. Klik op **volgende**nadat u de servers of data bases hebt geselecteerd die u wilt beveiligen.

5. Geef in het scherm **methode voor gegevens beveiliging selecteren** een naam op voor de beveiligings groep en schakel het selectie vakje **Ik wil online beveiliging** in.

    ![Methode voor gegevens beveiliging-korte termijn schijf & online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. In het scherm doelen voor de **korte termijn opgeven** , neemt u de benodigde invoer op voor het maken van back-uppunten op schijf en klikt u op **volgende**.

    In het voor beeld is de **Bewaar** termijn **5 dagen**, de **synchronisatie frequentie** is elke **15 minuten**, wat de back-upfrequentie is. **Snelle volledige back-up** is ingesteld op **8:00 P. M**.

    ![Doelen voor de korte termijn](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In het voor beeld dat wordt weer gegeven, om 8:00 uur elke dag een back-uppunt wordt gemaakt door de gewijzigde gegevens van de vorige dag van het 8:00 PM-back-uppunt te verplaatsen. Dit proces heet **snelle volledige back-up**. Transactie logboeken worden elke 15 minuten gesynchroniseerd. Als u de Data Base op 9:00 uur moet herstellen, wordt het punt gemaakt op basis van de logboeken van het laatste snelle volledige back-uppunt (8 P.M. in dit geval).
   >
   >

7. Controleer op het scherm **schijf toewijzing controleren** de totale beschik bare opslag ruimte en de mogelijke schijf ruimte. Klik op **Volgende**.

8. Kies in de methode voor het maken van een **replica kiezen**hoe u uw eerste herstel punt maakt. U kunt de eerste back-up hand matig overdragen (uit het netwerk) om te voor komen dat er band breedte overbelast of via het netwerk. Als u wilt wachten op het overdragen van de eerste back-up, kunt u de tijd voor de eerste overdracht opgeven. Klik op **Volgende**.

    ![Methode van initiële replicatie](./media/backup-azure-backup-sql/pg-manual.png)

    Voor de eerste back-upkopie moet de volledige gegevens bron (SQL Server-Data Base) van de productie server (SQL Server computer) naar Azure Backup Server worden overgedragen. Deze gegevens zijn mogelijk erg groot en het overdragen van de gegevens via het netwerk kan de band breedte overschrijden. Daarom kunt u ervoor kiezen om de eerste back-up te verplaatsen: **hand matig** (met behulp van Verwissel bare media) om te voor komen dat er band breedte overbelast wordt of **automatisch via het netwerk** (op een bepaald moment).

    Zodra de eerste back-up is voltooid, zijn de rest van de back-ups incrementele back-ups op de eerste back-upkopie. Incrementele back-ups zijn vaak klein en kunnen eenvoudig via het netwerk worden overgedragen.

9. Kies wanneer u wilt dat de consistentie controle wordt uitgevoerd en klik op **volgende**.

    ![Consistentie controle](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server voert een consistentie controle uit op de integriteit van het back-uppunt. Azure Backup Server berekent de controlesom van het back-upbestand op de productie server (SQL Server computer in dit scenario) en de gegevens van de back-up voor dat bestand. Als er een conflict optreedt, wordt ervan uitgegaan dat het back-upbestand op Azure Backup Server is beschadigd. Azure Backup Server verholpen de back-upgegevens door de blokken te verzenden die overeenkomen met de controlesom komen niet overeen. Omdat consistentie controles prestaties vergen, kunt u de consistentie controle plannen of automatisch uitvoeren.

10. Als u de online beveiliging van de gegevens bronnen wilt opgeven, selecteert u de data bases die moeten worden beveiligd met Azure en klikt u op **volgende**.

    ![Gegevens bronnen selecteren](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Kies een back-upschema en bewaar beleid dat aan het organisatie beleid voldoet.

    ![Plannen en bewaren](./media/backup-azure-backup-sql/pg-schedule.png)

    In dit voor beeld worden back-ups eenmaal per dag om 12:00 uur en 8 uur (onderste deel van het scherm) gemaakt.

    > [!NOTE]
    > Het is een goed idee om een aantal herstel punten voor de korte termijn te hebben op de schijf, voor snel herstel. Deze herstel punten worden gebruikt voor operationeel herstel. Azure fungeert als een goede externe locatie met hogere Sla's en gegarandeerde Beschik baarheid.
    >
    >

    **Aanbevolen procedure**: als u back-ups naar Azure plant om te starten nadat de back-ups van de lokale schijf zijn voltooid, worden de meest recente schijf back-ups altijd naar Azure gekopieerd.

12. Kies het schema voor het Bewaar beleid. De details over de manier waarop het Bewaar beleid werkt, vindt u in [gebruik Azure backup om uw tape-infrastructuur artikel te vervangen](backup-azure-backup-cloud-as-tape.md).

    ![Bewaarbeleid](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In dit voorbeeld:

    * Back-ups worden eenmaal per dag om 12:00 uur en 8 uur (onderste deel van het scherm) gemaakt en blijven 180 dagen bewaard.
    * De back-up op zaterdag om 12:00 uur wordt 104 weken bewaard
    * De back-up op de laatste zaterdag om 12:00 uur wordt gedurende 60 maanden bewaard
    * De back-up op de laatste zaterdag van maart om 12:00 uur wordt gedurende tien jaar bewaard
13. Klik op **volgende** en selecteer de juiste optie om de eerste back-up naar Azure te verplaatsen. U kunt **automatisch selecteren via het netwerk**

14. Wanneer u de details van het beleid in het scherm **samen vatting** hebt bekeken, klikt u op **groep maken** om de werk stroom te volt ooien. U kunt klikken op **sluiten** en de voortgang van de taak in de werk ruimte bewaking bewaken.

    ![Bezig met het maken van een beveiligings groep](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Back-ups op aanvraag van een SQL Server Data Base

Terwijl de vorige stappen een back-upbeleid hebben gemaakt, wordt er alleen een ' herstel punt ' gemaakt wanneer de eerste back-up wordt uitgevoerd. In plaats van te wachten tot de scheduler wordt gestart, activeren de volgende stappen om hand matig een herstel punt te maken.

1. Wacht totdat de status van de beveiligings groep op **OK** staat voor de Data Base voordat u het herstel punt maakt.

    ![Leden van beveiligings groep](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klik met de rechter muisknop op de data base en selecteer **herstel punt maken**.

    ![Online herstel punt maken](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Kies **online beveiliging** in de vervolg keuzelijst en klik op **OK** om te beginnen met het maken van een herstel punt in Azure.

    ![Herstelpunt maken](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Bekijk de voortgang van de taak in de werk ruimte **bewaking** .

    ![Bewakings console](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Een SQL Server-Data Base herstellen vanuit Azure

De volgende stappen zijn vereist om een beveiligde entiteit (SQL Server-Data Base) te herstellen vanuit Azure.

1. Open de Azure Backup Server-beheer console. Ga naar de **herstel** werkruimte waar u de beveiligde servers kunt zien. Blader door de vereiste data base (in dit geval Report Server $ MSDPM2012). Selecteer een **herstel** tijd die is opgegeven als een **online** punt.

    ![Herstel punt selecteren](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klik met de rechter muisknop op de naam van de data base en klik op **herstellen**.

    ![Herstellen vanuit Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS toont de details van het herstel punt. Klik op **Volgende**. Als u de Data Base wilt overschrijven, selecteert u het herstel type **herstellen naar het oorspronkelijke exemplaar van SQL Server**. Klik op **Volgende**.

    ![Herstellen naar de oorspronkelijke locatie](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In dit voor beeld herstelt MABS de Data Base naar een andere SQL Server-instantie of naar een zelfstandige netwerkmap.

4. In het scherm **herstel opties opgeven** kunt u de herstel opties zoals netwerk bandbreedte gebruiken selecteren om de band breedte te beperken die wordt gebruikt voor herstel. Klik op **Volgende**.

5. In het scherm **samen vatting** ziet u alle beschik bare herstel configuraties tot nu toe. Klik op **herstellen**.

    De herstel status toont de data base die wordt hersteld. U kunt op **sluiten** klikken om de wizard te sluiten en de voortgang in de werk ruimte **bewaking** weer te geven.

    ![Herstel proces initiëren](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Wanneer het herstel is voltooid, is de herstelde data base toepassings consistent.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [back-upbestanden en het toepassings](backup-mabs-files-applications-azure-stack.md) artikel.
Zie het artikel [back-upSharePoint on Azure stack](backup-mabs-sharepoint-azure-stack.md) .
