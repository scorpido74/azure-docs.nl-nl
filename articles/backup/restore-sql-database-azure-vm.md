---
title: SQL Server data bases herstellen op een virtuele Azure-machine
description: In dit artikel wordt beschreven hoe u SQL Server-data bases herstelt die worden uitgevoerd op een virtuele Azure-machine en waarvan een back-up is gemaakt met Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 2c3b81c4d0bc4c7548fec8ec131fea66684a7aa8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054577"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>SQL Server data bases herstellen op virtuele Azure-machines

In dit artikel wordt beschreven hoe u een SQL Server-Data Base die wordt uitgevoerd op een virtuele machine van Azure (VM) herstelt waarvan de [Azure backup](backup-overview.md) -service een back-up heeft gemaakt naar een Azure backup Recovery Services kluis.

In dit artikel wordt beschreven hoe u SQL Server-data bases herstelt. Zie back-ups [maken van SQL server-data bases op virtuele Azure-machines](backup-azure-sql-database.md)voor meer informatie.

## <a name="restore-to-a-time-or-a-recovery-point"></a>Herstellen naar een tijd of herstel punt

Azure Backup kunt SQL Server-data bases die worden uitgevoerd op virtuele Azure-machines als volgt herstellen:

- Herstel naar een specifieke datum of tijd (naar de seconde) met behulp van back-ups van transactie Logboeken. Azure Backup bepaalt automatisch de juiste volledige differentiële back-up en de keten van logboek back-ups die moeten worden hersteld op basis van de geselecteerde tijd.
- Herstel een specifieke volledige of differentiële back-up om te herstellen naar een specifiek herstel punt.

## <a name="prerequisites"></a>Vereisten

Let op het volgende voordat u een Data Base herstelt:

- U kunt de database herstellen naar een exemplaar van SQL Server in dezelfde Azure-regio.
- De doel server moet zijn geregistreerd bij dezelfde kluis als de bron.
- Als u een met TDE versleutelde data base wilt herstellen naar een andere SQL Server, moet u [het certificaat eerst herstellen naar de doel server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- Voordat u de data base ' Master ' herstelt, start u het SQL Server-exemplaar in de modus voor één gebruiker met behulp van de opstart optie **-m AzureWorkloadBackup**.
  - De waarde voor **-m** is de naam van de client.
  - Alleen de opgegeven client naam kan de verbinding openen.
- Voor alle systeem databases (model, Master, msdb) stopt u de SQL Server Agent-service voordat u de herstel bewerking start.
- Sluit alle toepassingen die mogelijk proberen verbinding te maken met een van deze data bases.
- Als er meerdere exemplaren worden uitgevoerd op een server, moeten alle instanties anders worden uitgevoerd. Als u de Data Base wilt herstellen, wordt de server niet weer gegeven in de lijst met doel servers.

## <a name="restore-a-database"></a>Een database herstellen

Als u wilt herstellen, hebt u de volgende machtigingen nodig:

- Machtigingen voor **Back-upoperators** in de kluis waar u de herstel bewerking uitvoert.
- **Inzender (write)** toegang tot de bron-VM waarvan een back-up is gemaakt.
- **Inzender (write)** toegang tot de doel-VM:
  - Als u naar dezelfde VM wilt herstellen, is dit de bron-VM.
  - Als u naar een alternatieve locatie wilt herstellen, is dit de nieuwe doel-VM.

Herstel als volgt:

1. Open de kluis waarin de SQL Server VM is geregistreerd.
2. Selecteer op het kluis dashboard onder **gebruik** **Back-upitems**.
3. In **Back-upitems**, onder **type back-upbeheer**, selecteert u **SQL in azure VM**.

    ![SQL in Azure VM selecteren](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecteer de data base die u wilt herstellen.

    ![De te herstellen database selecteren](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Bekijk het database menu. Het bevat informatie over de back-up van de data base, waaronder:

    - De oudste en meest recente herstelpunten.
    - De back-upstatus van het logboek voor de afgelopen 24 uur voor data bases die zich in de herstel modus volledig en bulksgewijs logboeken bevinden en die zijn geconfigureerd voor transactionele logboek back-ups.

6. Selecteer **Terugzetten**.

    ![Selecteer herstellen](./media/backup-azure-sql-database/restore-db.png)

7. Geef in **configuratie herstellen**op waar (of hoe) de gegevens moeten worden teruggezet:
   - **Alternatieve locatie**: herstel de Data Base op een andere locatie en behoud de oorspronkelijke bron database.
   - **Db overschrijven**: herstel de gegevens naar hetzelfde SQL Server exemplaar als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke database overschreven.

        > [!IMPORTANT]
        > Als de geselecteerde data base deel uitmaakt van een always on-beschikbaarheids groep, kan SQL Server de data base niet overschrijven. Er is alleen een **alternatieve locatie** beschikbaar.
        >
   - **Herstellen als bestanden**: herstel de back-upbestanden die kunnen worden hersteld als een Data Base op elke computer waarop de bestanden zich bevinden met SQL Server Management Studio in plaats van deze als Data Base te herstellen.
     ![Het menu Configuratie herstellen](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Herstellen naar een alternatieve locatie

1. Selecteer in het menu **herstellen configuratie** , onder **waar u wilt herstellen**, **alternatieve locatie**.
1. Selecteer de SQL Server naam en het exemplaar waarnaar u de Data Base wilt herstellen.
1. Geef in het vak **Herstelde databasenaam** de naam van de doeldatabase op.
1. Indien van toepassing, selecteert u **overschrijven als de data base met dezelfde naam al bestaat in het geselecteerde SQL-exemplaar**.
1. Selecteer **herstel punt**en selecteer of u wilt [herstellen naar een bepaald tijdstip](#restore-to-a-specific-point-in-time) of dat u wilt [herstellen naar een specifiek herstel punt](#restore-to-a-specific-restore-point).

    ![Herstel punt selecteren](./media/backup-azure-sql-database/select-restore-point.png)

    ![Herstellen naar een bepaald tijdstip](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. Ga als volgt te werk in het menu **Geavanceerde configuratie**:

    - Als u de data base na het herstellen niet meer wilt gebruiken, schakelt u **herstellen met norecovery in**.
    - Als u de herstel locatie op de doel server wilt wijzigen, voert u nieuwe doel paden in.

        ![Doel paden invoeren](./media/backup-azure-sql-database/target-paths.png)

1. Klik op **OK** om het herstellen te activeren. Volg de voortgang van de herstel bewerking in het gebied **meldingen** of houd deze bij in de weer gave **back-uptaken** in de kluis.

    > [!NOTE]
    > Het herstel naar een bepaald tijdstip is alleen beschikbaar voor logboek back-ups voor data bases in de modus volledig en in bulk logboek registratie.

### <a name="restore-and-overwrite"></a>Herstellen en overschrijven

1. Selecteer in het menu **configuratie herstellen** onder **herstel van herstellen**de optie **Data Base overschrijven**  >  **OK**.

    ![DB overschrijven selecteren](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Selecteer in **herstel punt selecteren** **Logboeken (tijdstip)** om [naar een bepaald punt in de tijd te herstellen](#restore-to-a-specific-point-in-time). Of selecteer **volledige & differentieel** om terug te zetten naar een [specifiek herstel punt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Het herstel naar een bepaald tijdstip is alleen beschikbaar voor logboek back-ups voor data bases in de modus volledig en in bulk logboek registratie.

### <a name="restore-as-files"></a>Herstellen als bestanden

Als u de back-upgegevens wilt herstellen als. bak-bestanden in plaats van een Data Base, kiest u **herstellen als bestanden**. Zodra de bestanden zijn gedumpt naar een opgegeven pad, kunt u deze bestanden op elke computer zetten waar u ze wilt herstellen als een Data Base. Omdat u deze bestanden op elke machine kunt verplaatsen, kunt u de gegevens in abonnementen en regio's nu herstellen.

1. Onder **waar en hoe u kunt herstellen**, selecteert u **herstellen als bestanden**.
1. Selecteer de SQL Server naam waarnaar u de back-upbestanden wilt herstellen.
1. Voer in het doelpad **op de server** het mappad in op de server die u hebt geselecteerd in stap 2. Dit is de locatie waar de service alle benodigde back-upbestanden zal dumpen. Normaal gesproken maakt een netwerksharepad of pad van een gekoppelde Azure-bestandsshare indien opgegeven als doelpad toegang tot deze bestanden door andere computers in hetzelfde netwerk of met dezelfde Azure-bestandsshare die eraan is gekoppeld eenvoudiger.<BR>

    >Als u de back-upbestanden van de Data Base wilt herstellen op een Azure-bestands share die is gekoppeld aan de geregistreerde virtuele machine van het doel, zorgt u ervoor dat NT AUTHORITY\SYSTEM toegang heeft tot de bestands share. U kunt de onderstaande stappen uitvoeren om de machtigingen lezen/schrijven toe te kennen aan de AFS die is gekoppeld aan de virtuele machine:
    >
    >- Voer uit `PsExec -s cmd` om in te voeren in de NT AUTHORITY\SYSTEM-shell
    >   - Voer `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` uit
    >   - Toegang controleren met`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Een terugzet bewerking starten als bestanden van de back-upkluis naar `\\<storageacct>.file.core.windows.net\<filesharename>` het pad<BR>
    U kunt PsExec downloaden van de [Sysinternals](/sysinternals/downloads/psexec) -pagina.

1. Selecteer **OK**.

    ![Herstellen als bestanden selecteren](./media/backup-azure-sql-database/restore-as-files.png)

1. Selecteer **herstel punt**en selecteer of u wilt [herstellen naar een bepaald tijdstip](#restore-to-a-specific-point-in-time) of dat u wilt [herstellen naar een specifiek herstel punt](#restore-to-a-specific-restore-point).

1. Alle back-upbestanden die zijn gekoppeld aan het geselecteerde herstel punt, worden in het doelpad gedumpt. U kunt de bestanden herstellen als een Data Base op elke computer waarop deze zich bevinden met behulp van SQL Server Management Studio.

    ![Back-upbestanden in doelpad herstellen](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Herstellen naar een bepaald punt in de tijd

Ga als volgt te werk als u **Logboeken** hebt geselecteerd (tijdstip) als herstel type:

1. Open de agenda onder **datum en tijd herstellen**. In de agenda worden de datums met herstel punten weer gegeven in vetgedrukte tekst en wordt de huidige datum gemarkeerd.
1. Selecteer een datum met herstel punten. U kunt geen datums selecteren die geen herstel punten hebben.

    ![De agenda openen](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Als u een datum hebt geselecteerd, worden de beschikbare herstelpunten in chronologische volgorde weergegeven in de tijdlijngrafiek.
1. Geef een tijd op voor het herstel in de tijdlijn grafiek of selecteer een tijd. Selecteer vervolgens **OK**.

### <a name="restore-to-a-specific-restore-point"></a>Herstellen naar een specifiek herstel punt

Ga als volgt te werk als u **volledige & differentieel** hebt geselecteerd als het herstel type:

1. Selecteer een herstelpunt in de lijst en selecteer **OK** om de procedure voor het herstelpunt te voltooien.

    ![Een volledig herstelpunt kiezen](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Standaard worden de herstel punten van de afgelopen 30 dagen weer gegeven. U kunt herstel punten ouder dan 30 dagen weer geven door te klikken op **filter** en een aangepast bereik te selecteren.

### <a name="restore-databases-with-large-number-of-files"></a>Data bases herstellen met een groot aantal bestanden

Als de totale grootte van de teken reeks van bestanden in een Data Base groter is dan een [bepaalde limiet](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), Azure backup slaat de lijst met database bestanden op in een andere pit-component, zodat u het pad voor het terugzetten van de doel groep niet kunt instellen tijdens de herstel bewerking. De bestanden worden in plaats daarvan teruggezet naar het standaardpad voor SQL.

  ![Data base herstellen met een groot bestand](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Volgende stappen

[Beheren en bewaken](manage-monitor-sql-database-backup.md) SQL Server data bases waarvan een back-up is gemaakt door Azure Backup.
