---
title: Back-ups maken van SQL Server-workloads op Azure Stack
description: Lees in dit artikel hoe u Microsoft Azure Backup Server (MABS) configureert om SQL Server-databases op Azure Stack te beveiligen.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 03211e1147f96429a8406c4c95654161ed2bf308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172316"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Back-ups maken van SQL Server op Azure Stack

Gebruik dit artikel om Microsoft Azure Backup Server (MABS) te configureren om SQL Server-databases in Azure Stack te beveiligen.

Het beheer van SQL Server-databaseback-up naar Azure en herstel van Azure omvat drie stappen:

1. Een back-upbeleid maken om SQL Server-databases te beveiligen
2. On-demand back-upkopieën maken
3. De database herstellen van schijven en vanuit Azure

## <a name="before-you-start"></a>Voordat u begint

[Azure Backup Server installeren en voorbereiden.](backup-mabs-install-azure-stack.md)

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Een back-upbeleid maken om SQL Server-databases te beschermen tegen Azure

1. Klik op de gebruikersinterface van Azure Backup Server op de **werkruimte Beveiliging.**

2. Klik op het gereedschapslint op **Nieuw** om een nieuwe beveiligingsgroep te maken.

    ![Beveiligingsgroep maken](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server start de wizard Beveiligingsgroep, die u door het maken van een **beveiligingsgroep**leidt. Klik op **Volgende**.

3. Selecteer Servers in het scherm **Type beveiligingsgroep** **selecteren**.

    ![Type beveiligingsgroep selecteren - 'Servers'](./media/backup-azure-backup-sql/pg-servers.png)

4. In het scherm **Groepsleden selecteren** worden in de lijst Beschikbare leden de verschillende gegevensbronnen weergegeven. Klik **+** hier om een map uit te vouwen en de submappen weer te geven. Klik op het selectievakje om een item te selecteren.

    ![SQL DB selecteren](./media/backup-azure-backup-sql/pg-databases.png)

    Alle geselecteerde items worden weergegeven in de lijst Geselecteerde leden. Nadat u de servers of databases hebt geselecteerd die u wilt beveiligen, klikt u op **Volgende**.

5. Geef in het scherm **Gegevensbeschermingsmethode selecteren** een naam op voor de beveiligingsgroep en schakel het selectievakje **Ik wil online beveiliging** in.

    ![Gegevensbeschermingsmethode - schijf op korte termijn & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Voeg in het scherm **Doelen voor korte termijn opgeven** de benodigde ingangen op om back-uppunten naar schijf te maken en klik op **Volgende**.

    In het voorbeeld is **het bewaarbereik** **5 dagen**, **synchronisatiefrequentie** is eens per **15 minuten**, wat de back-upfrequentie is. **Express Full Backup** is ingesteld op **20:00 uur.**

    ![Kortetermijndoelstellingen](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In het getoonde voorbeeld wordt elke dag om 20:00 uur een back-uppunt gemaakt door de gewijzigde gegevens over te dragen van het back-uppunt van de vorige dag. Dit proces heet **Express Full Backup**. Transactielogboeken worden elke 15 minuten gesynchroniseerd. Als u de database om 21:00 uur moet herstellen, wordt het punt gemaakt van de logboeken van het laatste uitdrukkelijke volledige back-uppunt (in dit geval 20.00 uur).
   >
   >

7. Controleer in het scherm **Schijftoewijzing controleren** of de totale beschikbare opslagruimte en de potentiële schijfruimte beschikbaar zijn. Klik op **Volgende**.

8. Kies in de **methode Replica's maken**kiezen hoe u uw eerste herstelpunt maakt. U de eerste back-up handmatig (buiten het netwerk) overbrengen om bandbreedtecongestie of via het netwerk te voorkomen. Als u ervoor kiest om te wachten met het overbrengen van de eerste back-up, u de tijd voor de eerste overdracht opgeven. Klik op **Volgende**.

    ![Eerste replicatiemethode](./media/backup-azure-backup-sql/pg-manual.png)

    De eerste back-upkopie vereist het overbrengen van de volledige gegevensbron (SQL Server-database) van productieserver (SQL Server-machine) naar Azure Backup Server. Deze gegevens kunnen groot zijn en de overdracht van de gegevens over het netwerk kan de bandbreedte overschrijden. Om deze reden u ervoor kiezen om de eerste back-up over te zetten: **handmatig** (met behulp van verwisselbare media) om bandbreedtecongestie te voorkomen, of **automatisch over het netwerk** (op een bepaald tijdstip).

    Zodra de eerste back-up is voltooid, de rest van de back-ups zijn incrementele back-ups op de eerste back-up kopie. Incrementele back-ups zijn meestal klein en kunnen gemakkelijk over het netwerk worden overgedragen.

9. Kies wanneer u de consistentiecontrole wilt uitvoeren en klik op **Volgende**.

    ![Consistentiecontrole](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server voert een consistentiecontrole uit op de integriteit van het back-uppunt. Azure Backup Server berekent de checksum van het back-upbestand op de productieserver (SQL Server-machine in dit scenario) en de back-upgegevens voor dat bestand. Als er een conflict is, wordt ervan uitgegaan dat het back-upbestand op Azure Backup Server beschadigd is. Azure Backup Server corrigeert de back-upgegevens door de blokken te verzenden die overeenkomen met de fout in checksum. Omdat consistentiecontroles prestatie-intensief zijn, u de consistentiecontrole plannen of deze automatisch uitvoeren.

10. Als u online bescherming van de gegevensbronnen wilt opgeven, selecteert u de databases die moeten worden beveiligd voor Azure en klikt u op **Volgende**.

    ![Gegevensbronnen selecteren](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Kies back-upschema's en bewaarbeleid dat past bij het organisatiebeleid.

    ![Plannen en retentie](./media/backup-azure-backup-sql/pg-schedule.png)

    In dit voorbeeld worden back-ups één keer per dag om 12:00 en 20:00 uur genomen (onderdeel van het scherm)

    > [!NOTE]
    > Het is een goede gewoonte om een paar korte termijn herstelpunten op schijf, voor snel herstel. Deze herstelpunten worden gebruikt voor operationeel herstel. Azure dient als een goede offsite locatie met hogere SLA's en gegarandeerde beschikbaarheid.
    >
    >

    **Aanbevolen procedures:** Als u back-ups naar Azure plant om te starten nadat de lokale schijfback-ups zijn voltooid, worden de nieuwste schijfback-ups altijd gekopieerd naar Azure.

12. Kies het bewaarbeleid. De details over hoe het bewaarbeleid werkt, worden verstrekt bij [Azure Backup gebruiken om uw tapeinfrastructuurartikel te vervangen.](backup-azure-backup-cloud-as-tape.md)

    ![Bewaarbeleid](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In dit voorbeeld:

    * Back-ups worden één keer per dag om 12:00 en 20:00 uur (onderaan het scherm) gemaakt en worden gedurende 180 dagen bewaard.
    * De back-up op zaterdag om 12:00 uur. wordt 104 weken bewaard
    * De back-up op afgelopen zaterdag om 12:00 uur. wordt 60 maanden bewaard
    * De back-up op zaterdag om 12:00 uur. wordt 10 jaar bewaard
13. Klik **op Volgende** en selecteer de juiste optie voor het overbrengen van de eerste back-upnaar Azure. U **automatisch kiezen via het netwerk**

14. Zodra u de beleidsdetails in het scherm **Overzicht** bekijkt, klikt u op **Groep maken** om de werkstroom te voltooien. U op **Sluiten** klikken en de voortgang van de taak in de werkruimte Bewaken controleren controleren controleren.

    ![Oprichting van beschermingsgroep in uitvoering](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>On-demand back-up van een SQL Server-database

Terwijl in de vorige stappen een back-upbeleid is gemaakt, wordt er alleen een 'herstelpunt' gemaakt wanneer de eerste back-up plaatsvindt. In plaats van te wachten tot de planner begint, activeren de onderstaande stappen het handmatig maken van een herstelpunt.

1. Wacht tot de status van de beveiligingsgroep **OK** voor de database wordt weergegeven voordat u het herstelpunt maakt.

    ![Leden van de beschermingsgroep](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klik met de rechtermuisknop op de database en selecteer **Herstelpunt maken**.

    ![Online herstelpunt maken](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Kies **Onlinebeveiliging** in het vervolgkeuzemenu en klik op **OK** om te beginnen met het maken van een herstelpunt in Azure.

    ![Herstelpunt maken](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Bekijk de voortgang van de taak in de **werkruimte Bewaken.**

    ![Bewakingsconsole](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Een SQL Server-database herstellen vanuit Azure

De volgende stappen zijn vereist om een beveiligde entiteit (SQL Server-database) uit Azure te herstellen.

1. Open de Azure Backup Server Management Console. Navigeer naar **de herstelwerkruimte** waar u de beveiligde servers zien. Blader door de vereiste database (in dit geval ReportServer$MSDPM2012). Selecteer een **herstel uit** de tijd die is opgegeven als **een onlinepunt.**

    ![Herstelpunt selecteren](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klik met de rechtermuisknop op de databasenaam en klik op **Herstellen**.

    ![Herstellen vanuit Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS toont de details van het herstelpunt. Klik op **Volgende**. Als u de database wilt overschrijven, selecteert u het hersteltype **Herstellen naar de oorspronkelijke instantie van SQL Server**. Klik op **Volgende**.

    ![Herstellen naar oorspronkelijke locatie](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In dit voorbeeld herstelt MABS de database naar een andere SQL Server-instantie of naar een zelfstandige netwerkmap.

4. In het scherm **Herstelopties opgeven** u de herstelopties selecteren, zoals beperking van het gebruik van netwerkbandbreedte om de bandbreedte die door herstel wordt gebruikt, te beperken. Klik op **Volgende**.

5. In het **scherm Overzicht** ziet u alle herstelconfiguraties die tot nu toe zijn geleverd. Klik **op Herstellen**.

    De status Herstel geeft aan dat de database wordt hersteld. U op **Sluiten** klikken om de wizard te sluiten en de voortgang in de **werkruimte Controle** weer te geven.

    ![Herstelproces starten](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Zodra het herstel is voltooid, is de herstelde database toepassingsconsistent.

## <a name="next-steps"></a>Volgende stappen

Zie het [artikel Back-upbestanden en toepassingen.](backup-mabs-files-applications-azure-stack.md)
Zie het artikel [SharePoint back-up op Azure Stack.](backup-mabs-sharepoint-azure-stack.md)
