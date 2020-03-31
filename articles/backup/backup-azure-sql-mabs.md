---
title: Back-ups maken van SQL Server met Azure Backup Server
description: Leer in dit artikel de configuratie om een back-up te maken van SQL Server-databases met Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505940"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Back-ups maken van SQL Server naar Azure met Azure Backup Server

Met dit artikel u back-ups van SQL Server-databases instellen met Behulp van Microsoft Azure Backup Server (MABS).

Een back-up van een SQL Server-database maken en deze herstellen vanuit Azure:

1. Maak een back-upbeleid om SQL Server-databases in Azure te beschermen.
1. Maak on-demand back-upkopieën in Azure.
1. Herstel de database in Azure.

## <a name="before-you-start"></a>Voordat u begint

Controleer voordat u begint of u Azure Backup Server hebt [geïnstalleerd en voorbereid.](backup-azure-microsoft-azure-backup.md)

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid 

Als u SQL Server-databases in Azure wilt beveiligen, maakt u eerst een back-upbeleid:

1. Selecteer in Azure Backup Server de **werkruimte Beveiliging.**
1. Selecteer **Nieuw** om een beveiligingsgroep te maken.

    ![Een beveiligingsgroep maken in Azure Backup Server](./media/backup-azure-backup-sql/protection-group.png)
1. Bekijk op de startpagina de richtlijnen voor het maken van een beveiligingsgroep. Selecteer **vervolgens Volgende**.
1. Selecteer **Servers**voor het type beveiligingsgroep .

    ![Het groepstype Servers-beveiliging selecteren](./media/backup-azure-backup-sql/pg-servers.png)
1. Vouw de SQL Server-machine uit waar de databases zich bevinden waar de databases zich bevinden waaru een back-up wilt maken. U ziet de gegevensbronnen waarvan een back-up kan worden genomen vanaf die server. Vouw **alle SQL-shares** uit en selecteer vervolgens de databases waarvan u een back-up wilt maken. In dit voorbeeld selecteren we ReportServer$MSDPM2012 en ReportServer$MSDPM2012TempDB. Selecteer **Volgende**.

    ![Een SQL Server-database selecteren](./media/backup-azure-backup-sql/pg-databases.png)
1. Geef de beveiligingsgroep een naam en selecteer **I want online protection**.

    ![Kies een methode voor gegevensbescherming - schijfbeveiliging op korte termijn of online Azure-beveiliging](./media/backup-azure-backup-sql/pg-name.png)
1. Voeg op de pagina **Doelen voor korte termijn opgeven** de benodigde ingangen op om back-uppunten op de schijf te maken.

    In dit voorbeeld wordt **het bewaarbereik** ingesteld op *5 dagen*. De **back-upsynchronisatiefrequentie** is ingesteld op eens per *15 minuten.* **Express Full Backup** is ingesteld op *20:00 uur.*

    ![Kortetermijndoelen instellen voor back-upbeveiliging](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In dit voorbeeld wordt elke dag om 20:00 uur een back-uppunt gemaakt. De gegevens die zijn gewijzigd sinds het back-uppunt van de vorige dag om 20:00 uur worden overgedragen. Dit proces heet **Express Full Backup**. Hoewel de transactielogboeken elke 15 minuten worden gesynchroniseerd, als we de database om 21:00 uur moeten herstellen, wordt het punt gemaakt door de logboeken opnieuw af te spelen van het laatste volledige back-uppunt, dat in dit voorbeeld om 20:00 uur is.
   >
   >

1. Selecteer **Volgende**. MABS toont de totale beschikbare opslagruimte. Het toont ook de potentiële schijfruimte gebruik.

    ![Schijftoewijzing instellen in MABS](./media/backup-azure-backup-sql/pg-storage.png)

    MABS maakt standaard één volume per gegevensbron (SQL Server-database). Het volume wordt gebruikt voor de eerste back-up. In deze configuratie beperkt Logical Disk Manager (LDM) de MABS-beveiliging tot 300 gegevensbronnen (SQL Server-databases). Als u deze beperking wilt omzeilen, selecteert u **Gegevens co-zoeken in de DPM-opslaggroep**. Als u deze optie gebruikt, gebruikt MABS één volume voor meerdere gegevensbronnen. Met deze instelling kan MABS tot 2.000 SQL Server-databases beveiligen.

    Als u **De volumes automatisch vergroten,** kan MABS het verhoogde back-upvolume verantwoorden naarmate de productiegegevens toenemen. Als u de volumes niet automatisch wilt **laten groeien,** beperkt MABS de back-upopslag tot de gegevensbronnen in de beveiligingsgroep.
1. Als u een beheerder bent, u ervoor kiezen om deze initiële back-up **automatisch over het netwerk** over te zetten en het tijdstip van overdracht te kiezen. Of kies ervoor om de back-up **handmatig** over te dragen. Selecteer **vervolgens Volgende**.

    ![Een methode voor het maken van replica's kiezen in MABS](./media/backup-azure-backup-sql/pg-manual.png)

    De eerste back-up kopie vereist de overdracht van de gehele gegevensbron (SQL Server-database). De back-upgegevens worden verplaatst van de productieserver (SQL Server-machine) naar MABS. Als deze back-up groot is, kan het overbrengen van de gegevens over het netwerk bandbreedtecongestie veroorzaken. Daarom kunnen beheerders ervoor kiezen om verwisselbare media te gebruiken om de eerste back-up **handmatig**over te zetten. Of ze kunnen de gegevens **automatisch over het netwerk** overbrengen op een bepaald tijdstip.

    Nadat de eerste back-up is voltooid, worden back-ups stapsgewijs voortgezet op de eerste back-up. Incrementele back-ups zijn meestal klein en kunnen gemakkelijk over het netwerk worden overgedragen.
1. Kies wanneer u een consistentiecontrole wilt uitvoeren. Selecteer **vervolgens Volgende**.

    ![Kiezen wanneer u een consistentiecontrole wilt uitvoeren](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS kan een consistentiecontrole uitvoeren op de integriteit van het back-uppunt. Het berekent de checksum van het back-upbestand op de productieserver (de SQL Server-machine in dit voorbeeld) en de back-upgegevens voor dat bestand in MABS. Als bij de controle een conflict wordt gevonden, wordt aangenomen dat het back-upbestand in MABS beschadigd is. MABS lost de back-upgegevens op door de blokken te verzenden die overeenkomen met de mismatch van checksum. Omdat de consistentiecontrole een prestatie-intensieve bewerking is, kunnen beheerders ervoor kiezen om de consistentiecontrole te plannen of deze automatisch uit te voeren.
1. Selecteer de gegevensbronnen die u wilt beveiligen in Azure. Selecteer **vervolgens Volgende**.

    ![Selecteer gegevensbronnen die u wilt beveiligen in Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Als u een beheerder bent, u back-upschema's en bewaarbeleid kiezen dat past bij het beleid van uw organisatie.

    ![Schema's en bewaarbeleid kiezen](./media/backup-azure-backup-sql/pg-schedule.png)

    In dit voorbeeld worden dagelijks back-ups genomen om 12:00 en 20:00 uur.

    > [!TIP]
    > Voor snel herstel, houd een paar korte termijn herstelpunten op uw schijf. Deze herstelpunten worden gebruikt voor operationeel herstel. Azure dient als een goede offsite locatie, met hogere SLA's en gegarandeerde beschikbaarheid.
    >
    > Gebruik DPM (Data Protection Manager) om Azure Backups te plannen nadat de lokale schijfback-ups zijn voltooid. Wanneer u deze praktijk volgt, wordt de nieuwste schijfback-up gekopieerd naar Azure.
    >


1. Kies het bewaarbeleid. Zie [Azure Backup gebruiken om uw tapeinfrastructuur te vervangen](backup-azure-backup-cloud-as-tape.md)voor meer informatie over hoe het bewaarbeleid werkt.

    ![Kies een bewaarbeleid in MABS](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In dit voorbeeld:

    * Back-ups worden dagelijks genomen om 12:00 en 20:00 uur. Ze worden 180 dagen bewaard.
    * De back-up op zaterdag om 12:00 uur wordt 104 weken bewaard.
    * De back-up van de laatste zaterdag van de maand om 12:00 uur wordt 60 maanden bewaard.
    * De back-up van de laatste zaterdag van maart om 12:00 uur wordt 10 jaar bewaard.

    Nadat u een bewaarbeleid hebt gekozen, selecteert u **Volgende**.
1. Kies hoe u de eerste back-upkopie overzet naar Azure.

    * De optie **Automatisch via het netwerk** volgt uw back-upschema om de gegevens naar Azure over te zetten.
    * Zie Overzicht van Offline [Back-up](offline-backup-overview.md)voor meer informatie over Offline **Back-up.**

    Nadat u een overdrachtsmechanisme hebt gekozen, selecteert u **Volgende**.
1. Bekijk op de **pagina Overzicht** de beleidsdetails. Selecteer vervolgens **Groep maken**. U **Sluiten** selecteren en de voortgang van de taak bekijken in de **werkruimte Controle.**

    ![De vooruitgang van de oprichting van de beschermingsgroep](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>On-demand back-upkopieën van een SQL Server-database maken

Er wordt een herstelpunt gemaakt wanneer de eerste back-up plaatsvindt. In plaats van te wachten tot de planning is uitgevoerd, u handmatig het maken van een herstelpunt activeren:

1. Controleer in de beveiligingsgroep of de databasestatus **ok**is.

    ![Een beveiligingsgroep met de databasestatus](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Klik met de rechtermuisknop op de database en selecteer **Herstelpunt maken**.

    ![Kies ervoor om een online herstelpunt te maken](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Selecteer **Onlinebeveiliging**in de vervolgkeuzelijst. Selecteer vervolgens **OK** om het maken van een herstelpunt in Azure te starten.

    ![Een herstelpunt maken in Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. U de voortgang van de taak bekijken in de **werkruimte Controleren.** 

    ![Voortgang van de taak weergeven in de console Controle](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Een SQL Server-database herstellen vanuit Azure

Ga als een te werk om een beveiligde entiteit, zoals een SQL Server-database, uit Azure te herstellen:

1. Open de DPM-serverbeheerconsole. Ga naar de **werkruimte Herstel** om de servers te zien waarvan DPM een back-up maakt. Selecteer de database (in dit voorbeeld ReportServer$MSDPM2012). Selecteer een **hersteltijd** die eindigt met **Online**.

    ![Een herstelpunt selecteren](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Klik met de rechtermuisknop op de databasenaam en selecteer **Herstellen**.

    ![Een database herstellen vanuit Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM toont de details van het herstelpunt. Selecteer **Volgende**. Als u de database wilt overschrijven, selecteert u het hersteltype **Herstellen naar de oorspronkelijke instantie van SQL Server**. Selecteer **vervolgens Volgende**.

    ![Een database herstellen naar de oorspronkelijke locatie](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In dit voorbeeld maakt DPM het herstel van de database mogelijk naar een andere SQL Server-instantie of naar een zelfstandige netwerkmap.
1. Op de pagina **Herstelopties opgeven** u de herstelopties selecteren. U bijvoorbeeld **beperking van het gebruik van netwerkbandbreedte** kiezen om de bandbreedte die herstelgebruikt, te beperken. Selecteer **vervolgens Volgende**.
1. Op de pagina **Overzicht** ziet u de huidige herstelconfiguratie. Selecteer **Herstellen**.

    De herstelstatus geeft aan dat de database wordt hersteld. U **Sluiten** selecteren om de wizard te sluiten en de voortgang in de **werkruimte Controle weergeven.**

    ![Het herstelproces starten](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Wanneer het herstel is voltooid, is de herstelde database consistent met de toepassing.

### <a name="next-steps"></a>Volgende stappen

Zie [Veelgestelde vragen over Azure Backup voor](backup-azure-backup-faq.md)meer informatie.
