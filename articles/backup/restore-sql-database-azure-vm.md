---
title: SQL Server-databases herstellen op een Azure VM
description: In dit artikel wordt beschreven hoe SQL Server-databases die op een Azure-vm worden uitgevoerd en waarvan een back-up wordt gemaakt met Azure Backup, kunnen worden hersteld.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252452"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>SQL Server-databases herstellen in Azure VM's

In dit artikel wordt beschreven hoe u een SQL Server-database herstelt die wordt uitgevoerd op een virtuele Azure-machine (VM) waarvan de [Azure Backup-service een back-up](backup-overview.md) heeft gemaakt naar een azure backup recovery services-vault.

In dit artikel wordt beschreven hoe SQL Server-databases kunnen worden hersteld. Zie [Back-ups van SQL Server-databases maken in Azure VM's](backup-azure-sql-database.md)voor meer informatie.

## <a name="restore-to-a-time-or-a-recovery-point"></a>Herstellen naar een tijd of een herstelpunt

Azure Backup kan SQL Server-databases die op Azure VM's worden uitgevoerd als volgt herstellen:

- Herstellen naar een specifieke datum of tijd (naar de tweede) met behulp van back-ups van het transactielogboek. Azure Backup bepaalt automatisch de juiste volledige differentiële back-up en de keten van logboekback-ups die moeten worden hersteld op basis van de geselecteerde tijd.
- Herstel een specifieke volledige of differentiële back-up om te herstellen naar een specifiek herstelpunt.

## <a name="prerequisites"></a>Vereisten

Noteer het volgende voordat u een database herstelt:

- U kunt de database herstellen naar een exemplaar van SQL Server in dezelfde Azure-regio.
- De doelserver moet zijn geregistreerd bij dezelfde kluis als de bron.
- Als u een door TDE versleutelde database wilt herstellen naar een andere SQL Server, moet u het certificaat eerst [herstellen naar de doelserver.](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)
- Voordat u de 'master'-database herstelt, start u de SQL Server-instantie in de modus met één gebruiker met de opstartoptie **-m AzureWorkloadBackup.**
  - De waarde voor **-m** is de naam van de klant.
  - Alleen de opgegeven clientnaam kan de verbinding openen.
- Voor alle systeemdatabases (model, master, msdb) stopt u de SQL Server Agent-service voordat u het herstel activeert.
- Sluit alle toepassingen die een verbinding met een van deze databases kunnen maken.
- Als u meerdere exemplaren op een server hebt, moeten alle exemplaren actief zijn, anders wordt de server niet weergegeven in de lijst met doelservers waarnaar u de database herstellen.

## <a name="restore-a-database"></a>Een database herstellen

Als u wilt herstellen, hebt u de volgende machtigingen nodig:

- **Machtigingen voor back-upoperatoren** in de kluis waar u het herstel doet.
- **Inzender (schrijf)** toegang tot de bron-VM waar een back-up van is.
- **Contribuant (schrijf)** toegang tot de doel-VM:
  - Als u herstelt naar dezelfde vm, is dit de bron-vm.
  - Als u herstelt naar een alternatieve locatie, is dit de nieuwe doel-vm.

Als volgt herstellen:

1. Open de kluis waarin de SQL Server VM is geregistreerd.
2. Selecteer **back-upitems**onder Gebruik onder **Gebruik**.
3. Selecteer SQL in Azure VM **in** **Back-upitems**onder **Type Back-upbeheer**.

    ![SQL in Azure VM selecteren](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecteer de database die u wilt herstellen.

    ![De te herstellen database selecteren](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Bekijk het databasemenu. Het biedt informatie over de database back-up, waaronder:

    - De oudste en meest recente herstelpunten.
    - De status van logboekback-up voor de afgelopen 24 uur voor databases die zich in de volledige en bulkgeregistreerde herstelmodus bevinden en die zijn geconfigureerd voor transactionele logboekback-ups.

6. Selecteer **Terugzetten**.

    ![Herstellen selecteren](./media/backup-azure-sql-database/restore-db.png)

7. Geef in **Herstelconfiguratie**op waar (of hoe) de gegevens moeten worden hersteld:
   - **Alternatieve locatie:** herstel de database naar een alternatieve locatie en bewaar de oorspronkelijke brondatabase.
   - **DB overschrijven:** de gegevens herstellen naar dezelfde SQL Server-instantie als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke database overschrijft.

    > [!IMPORTANT]
    > Als de geselecteerde database deel uitmaakt van een groep Beschikbaarheid always on, staat SQL Server niet toe dat de database wordt overschreven. Alleen **alternatieve locatie** is beschikbaar.
    >
   - **Herstellen als bestanden:** In plaats van als database te herstellen, herstelt u de back-upbestanden die later als database kunnen worden hersteld op elke machine waar de bestanden aanwezig zijn met SQL Server Management Studio.
     ![Het menu Configuratie herstellen](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Herstellen naar een alternatieve locatie

1. Selecteer **Alternatieve locatie**in het menu **Configuratie herstellen** onder Waar **te herstellen**.
2. Selecteer de SQL Server-naam en -instantie waaraan u de database wilt herstellen.
3. Geef in het vak **Herstelde databasenaam** de naam van de doeldatabase op.
4. Selecteer indien van toepassing **Overschrijven als de DB met dezelfde naam al bestaat op geselecteerde SQL-instantie**.
5. Selecteer **OK**.

    ![Waarden opgeven voor het menu Configuratie herstellen](./media/backup-azure-sql-database/restore-configuration.png)

6. Selecteer in **Herstelpunt selecteren**of u wilt [herstellen naar een bepaald punt in de tijd](#restore-to-a-specific-point-in-time) of wilt herstellen naar een specifiek [herstelpunt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Het in-time herstel is alleen beschikbaar voor logboekback-ups voor databases die zich in de volledige en bulkaangemelde herstelmodus bevinden.

### <a name="restore-and-overwrite"></a>Herstellen en overschrijven

1. Selecteer in het menu **Configuratie herstellen** onder **Waar herstellen**de optie DB > OK **overschrijven**.**OK**

    ![DB overschrijven selecteren](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Selecteer in **Herstelpunt selecteren**de optie **Logboeken (Punt in tijd)** om te [herstellen naar een specifiek punt in de tijd](#restore-to-a-specific-point-in-time). Of selecteer **Volledig & differentieel** om te herstellen naar een specifiek [herstelpunt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Het in-time herstel is alleen beschikbaar voor logboekback-ups voor databases die zich in de volledige en bulkaangemelde herstelmodus bevinden.

### <a name="restore-as-files"></a>Herstellen als bestanden

Als u de back-upgegevens wilt herstellen als .bak-bestanden in plaats van een database, kiest u **Herstellen als bestanden**. Zodra de bestanden op een bepaald pad zijn gedumpt, u deze bestanden naar elke machine brengen waar u ze als database wilt herstellen. Door de mogelijkheid om deze bestanden te verplaatsen naar elke machine, u nu herstellen van de gegevens in abonnementen en regio's.

1. Selecteer herstellen **als bestanden**in het menu **Configuratie herstellen** onder Waar **herstellen**.
2. Selecteer de SQL Server-naam waarop u de back-upbestanden wilt herstellen.
3. Voer **in** het pad Doel op de server het mappad in op de server die in stap 2 is geselecteerd. Dit is de locatie waar de service alle benodigde back-upbestanden dumpt. Doorgaans maakt een pad voor netwerkshare of pad van een opgezetazure-bestandsshare wanneer het is opgegeven als het doelpad, eenvoudiger toegang tot deze bestanden door andere machines in hetzelfde netwerk of met dezelfde Azure-bestandsshare die erop is gemonteerd.<BR>

    >Als u de databaseback-upbestanden wilt herstellen op een Azure File Share die is gemonteerd op de doelgeregistreerde VM, controleert u of NT AUTHORITY\SYSTEM toegang heeft tot de bestandsshare. U de onderstaande stappen uitvoeren om de lees-/schrijfmachtigingen toe te kennen aan de AFS die op de VM is gemonteerd:
    >
    >- Uitvoeren `PsExec -s cmd` om de shell NT AUTHORITY\SYSTEM binnen te gaan
    >   - Voer `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` uit
    >   - Toegang verifiëren met`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Start een herstel als bestanden van `\\<storageacct>.file.core.windows.net\<filesharename>` de Backup Vault naar als het pad<BR>
    U Psexec downloaden via<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Selecteer **OK**.

    ![Herstellen als bestanden selecteren](./media/backup-azure-sql-database/restore-as-files.png)

5. Selecteer het **herstelpunt** waarop alle beschikbare .bak-bestanden worden hersteld.

    ![Een herstelpunt selecteren](./media/backup-azure-sql-database/restore-point.png)

6. Alle back-upbestanden die aan het geselecteerde herstelpunt zijn gekoppeld, worden in het doelpad gedumpt. U de bestanden herstellen als een database op elke machine waarop ze aanwezig zijn met SQL Server Management Studio.

    ![Herstelde back-upbestanden in bestemmingspad](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Herstellen naar een specifiek tijdstip

Als u **Logboeken (Punt in tijd)** als hersteltype hebt geselecteerd, gaat u als volgt te werk:

1. Open de agenda **onder Datum/tijd herstellen.** In de agenda worden de datums met herstelpunten in vettype weergegeven en wordt de huidige datum gemarkeerd.
1. Selecteer een datum met herstelpunten. U geen datums selecteren die geen herstelpunten hebben.

    ![De agenda openen](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Als u een datum hebt geselecteerd, worden de beschikbare herstelpunten in chronologische volgorde weergegeven in de tijdlijngrafiek.
1. Geef een tijd op voor het herstel in de tijdlijngrafiek of selecteer een tijd. Selecteer vervolgens **OK**.

    ![Een hersteltijd selecteren](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. Als u de database na het herstellen niet-operationeel wilt houden, schakelt u **herstellen met NORECOVERY**in in het menu **Geavanceerde configuratie.**
1. Als u de herstellocatie op de doelserver wilt wijzigen, voert u een nieuw doelpad in.
1. Selecteer **OK**.

    ![Het menu Geavanceerde configuratie](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.
1. Houd de voortgang van het herstellen bij in het gebied **Meldingen** of houd deze bij door **Taken herstellen** te selecteren in het databasemenu.

    ![Voortgang hersteltaak](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Herstellen naar een specifiek herstelpunt

Als u **Volledig & differentieel** als hersteltype hebt geselecteerd, gaat u als volgt te werk:

1. Selecteer een herstelpunt in de lijst en selecteer **OK** om de procedure voor het herstelpunt te voltooien.

    ![Een volledig herstelpunt kiezen](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Standaard worden herstelpunten van de afgelopen 30 dagen weergegeven. U herstelpunten ouder dan 30 dagen weergeven door op **Filter** te klikken en een aangepast bereik te selecteren.

1. Als u de database na het herstellen niet-operationeel wilt houden, schakelt u **herstellen met NORECOVERY**in in het menu **Geavanceerde configuratie.**
1. Als u de herstellocatie op de doelserver wilt wijzigen, voert u een nieuw doelpad in.
1. Selecteer **OK**.

    ![Het menu Geavanceerde configuratie](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.
1. Houd de voortgang van het herstellen bij in het gebied **Meldingen** of houd deze bij door **Taken herstellen** te selecteren in het databasemenu.

    ![Voortgang hersteltaak](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Databases herstellen met een groot aantal bestanden

Als de totale tekenreeksgrootte van bestanden in een database groter is dan een [bepaalde limiet,](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)slaat Azure Backup de lijst met databasebestanden op in een andere pitcomponent, zodat u het doelherstelpad niet instellen tijdens de herstelbewerking. De bestanden worden in plaats daarvan hersteld naar het SQL-standaardpad.

  ![Database herstellen met groot bestand](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Volgende stappen

[Beheren en controleren](manage-monitor-sql-database-backup.md) SQL Server-databases waarvan een back-up wordt gemaakt door Azure Backup.
