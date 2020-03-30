---
title: Een database herstellen vanuit een back-up
description: Meer informatie over point-in-time restore, waarmee u een Azure SQL-database tot 35 dagen terugdraaien.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268741"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Een Azure SQL-database herstellen met behulp van geautomatiseerde databaseback-ups

Standaard worden Azure SQL Database-back-ups opgeslagen in geo-gerepliceerde blobopslag (RA-GRS-opslagtype). De volgende opties zijn beschikbaar voor databaseherstel met behulp van [geautomatiseerde databaseback-ups.](sql-database-automated-backups.md) U kunt:

- Maak een nieuwe database op dezelfde SQL Database-server, hersteld tot een opgegeven tijdbinnen de bewaarperiode.
- Maak een database op dezelfde SQL Database-server, hersteld tot de verwijderingstijd voor een verwijderde database.
- Maak een nieuwe database op elke SQL Database-server in dezelfde regio, hersteld tot het punt van de meest recente back-ups.
- Maak een nieuwe database op een SQL Database-server in een andere regio, hersteld tot het punt van de meest recente gerepliceerde back-ups.

Als u [een back-up](sql-database-long-term-retention.md)voor lange termijn hebt geconfigureerd, u ook een nieuwe database maken op basis van een back-up voor lange termijn op een SQL Database-server.

> [!IMPORTANT]
> U een bestaande database niet overschrijven tijdens het herstellen.

Wanneer u de servicelagen Standard of Premium gebruikt, kan het herstellen van uw database extra opslagkosten met zich meebrengen. De extra kosten worden gemaakt wanneer de maximale grootte van de herstelde database groter is dan de hoeveelheid opslagruimte die is opgenomen in de servicelaag en het prestatieniveau van de doeldatabase. Zie de [prijspagina sql database](https://azure.microsoft.com/pricing/details/sql-database/)voor prijsdetails van extra opslag. Als de werkelijke hoeveelheid gebruikte ruimte kleiner is dan de hoeveelheid opslagruimte die is opgenomen, u deze extra kosten vermijden door de maximale databasegrootte in te stellen op het opgenomen bedrag.

## <a name="recovery-time"></a>Hersteltijd

De hersteltijd om een database te herstellen met behulp van geautomatiseerde databaseback-ups wordt beïnvloed door verschillende factoren:

- De grootte van de database.
- De rekengrootte van de database.
- Het aantal transactielogboeken.
- De hoeveelheid activiteit die moet worden afgespeeld om te herstellen naar het herstelpunt.
- De netwerkbandbreedte als de herstellocatie naar een andere regio is.
- Het aantal gelijktijdige herstelaanvragen dat wordt verwerkt in het doelgebied.

Voor een grote of zeer actieve database kan het herstel enkele uren duren. Als er een langdurige uitval in een regio is, is het mogelijk dat een groot aantal aanvragen voor geoherstel wordt gestart voor herstel na noodgevallen. Wanneer er veel aanvragen zijn, kan de hersteltijd voor afzonderlijke databases toenemen. De meeste database herstelt voltooid in minder dan 12 uur.

Voor één abonnement zijn er beperkingen op het aantal gelijktijdige herstelaanvragen. Deze beperkingen zijn van toepassing op elke combinatie van point-in-time herstelt, geo-herstelt, en herstelt van lange termijn retentie back-up.

| | **Maximum aantal gelijktijdige aanvragen die worden verwerkt** | **Max aantal gelijktijdige aanvragen die worden ingediend** |
| :--- | --: | --: |
|Eén database (per abonnement)|10|60|
|Elastische pool (per pool)|4|200|
||||

Er is geen ingebouwde methode om de hele server te herstellen. Zie [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)voor een voorbeeld van hoe u deze taak uitvoeren.

> [!IMPORTANT]
> Als u wilt herstellen met behulp van geautomatiseerde back-ups, moet u lid zijn van de rol van SQL Server-bijdrager in het abonnement of de eigenaar van het abonnement zijn. Zie [RBAC: Ingebouwde rollen](../role-based-access-control/built-in-roles.md)voor meer informatie. U herstellen met behulp van de Azure-portal, PowerShell of de REST API. U Transact-SQL niet gebruiken.

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

U een zelfstandige, samengevoegde of instantiedatabase herstellen naar een eerder tijdstip met behulp van de Azure-portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)of de [REST API.](https://docs.microsoft.com/rest/api/sql/databases) De aanvraag kan elke servicelaag of rekengrootte voor de herstelde database opgeven. Zorg ervoor dat u voldoende resources hebt op de server waarop u de database herstelt. Wanneer het herstel is voltooid, wordt een nieuwe database gemaakt op dezelfde server als de oorspronkelijke database. De herstelde database wordt in rekening gebracht tegen normale tarieven, op basis van de servicelaag en de rekengrootte. U brengt pas kosten in rekening als het herstel van de database is voltooid.

U herstelt een database over het algemeen naar een eerder punt voor hersteldoeleinden. U de herstelde database behandelen als een vervanging voor de oorspronkelijke database of deze gebruiken als gegevensbron om de oorspronkelijke database bij te werken.

- **Database vervangen**

  Als u van plan bent dat de herstelde database een vervanging is voor de oorspronkelijke database, moet u de rekengrootte en servicelaag van de oorspronkelijke database opgeven. U vervolgens de naam van de oorspronkelijke database wijzigen en de herstelde database de oorspronkelijke naam geven met de opdracht [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Gegevensherstel**

  Als u van plan bent gegevens uit de herstelde database op te halen om te herstellen van een gebruiker of toepassingsfout, moet u een gegevensherstelscript schrijven en uitvoeren dat gegevens uit de herstelde database haalt en van toepassing is op de oorspronkelijke database. Hoewel het herstellen van de bewerking lang kan duren, is de hersteldatabase zichtbaar in de databaselijst tijdens het herstelproces. Als u de database verwijdert tijdens het herstellen, wordt de herstelbewerking geannuleerd en worden er geen kosten in rekening gebracht voor de database die het herstel niet heeft voltooid.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Point-in-time herstellen met Azure-portal

U één SQL-database of instantiedatabase herstellen naar een tijdstip van het overzichtsblad van de database die u wilt herstellen in de Azure-portal.

#### <a name="single-azure-sql-database"></a>Eén Azure SQL-database

Als u een enkele of samengevoegde database wilt herstellen naar een tijdstip met behulp van de Azure-portal, opent u de pagina databaseoverzicht en selecteert **u Herstellen** op de werkbalk. Kies de back-upbron en selecteer het point-in-time back-uppunt van waaruit een nieuwe database wordt gemaakt. 

  ![Schermafbeelding van opties voor het herstellen van gegevens](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Beheerde instantiedatabase

Als u een beheerde instantiedatabase wilt herstellen naar een tijdstip met de Azure-portal, opent u de pagina databaseoverzicht en selecteert u **Herstellen** op de werkbalk. Kies het point-in-time back-uppunt van waaruit een nieuwe database wordt gemaakt. 

  ![Schermafbeelding van opties voor het herstellen van gegevens](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Zie [Programmatisch uitvoeren van herstel met behulp van geautomatiseerde back-ups](sql-database-recovery-using-backups.md)als u een database programmatisch wilt herstellen vanuit een back-up.

## <a name="deleted-database-restore"></a>Verwijderde database herstellen

U een verwijderde database herstellen naar de verwijderingstijd of een eerder tijdstip op dezelfde SQL Database-server of dezelfde beheerde instantie. U dit bereiken via de Azure-portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)of de [REST (createMode=Restore).](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) U herstelt een verwijderde database door een nieuwe database te maken vanuit de back-up.

> [!IMPORTANT]
> Als u een Azure SQL Database-server of beheerde instantie verwijdert, worden alle databases ook verwijderd en kunnen ze niet worden hersteld. U een verwijderde server of beheerde instantie niet herstellen.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Verwijderde databaseherstel met behulp van de Azure-portal

U herstelt verwijderde databases uit de Azure-portal van de server en instantiebron.

#### <a name="single-azure-sql-database"></a>Eén Azure SQL-database

Als u een enkele of samengevoegde verwijderde database wilt herstellen naar de verwijderingstijd met behulp van de Azure-portal, opent u de pagina serveroverzicht en selecteert u **Verwijderde databases**. Selecteer een verwijderde database die u wilt herstellen en typ de naam voor de nieuwe database die wordt gemaakt met gegevens die zijn hersteld vanuit de back-up.

  ![Schermafbeelding van het herstellen van verwijderde Azure SQL-database](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Beheerde instantiedatabase

Als u een beheerde database wilt herstellen met behulp van de Azure-portal, opent u de overzichtspagina beheerde instantie en selecteert u **Verwijderde databases**. Selecteer een verwijderde database die u wilt herstellen en typ de naam voor de nieuwe database die wordt gemaakt met gegevens die zijn hersteld vanuit de back-up.

  ![Schermafbeelding van de verwijderde Azure SQL-instantiedatabase herstellen](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Verwijderde database herstellen met PowerShell

Gebruik de volgende voorbeeldscripts om een verwijderde database voor Azure SQL Database en een beheerde instantie te herstellen met PowerShell.

#### <a name="single-azure-sql-database"></a>Eén Azure SQL-database

Zie [Een SQL-database herstellen met PowerShell](scripts/sql-database-restore-database-powershell.md)voor een voorbeeld van PowerShell-script waarin wordt weergegeven hoe u een verwijderde Azure SQL-database herstellen.

#### <a name="managed-instance-database"></a>Beheerde instantiedatabase

Zie [Verwijderde database herstellen op beheerde instantie met PowerShell-script](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database) voor een voorbeeld van PowerShell-script waarin wordt weergegeven hoe u een verwijderde instantiedatabase herstellen

> [!TIP]
> Zie [Programmatisch uitvoeren van herstel met behulp van geautomatiseerde back-ups](sql-database-recovery-using-backups.md)als u een verwijderde database programmatisch wilt herstellen.

## <a name="geo-restore"></a>Geo-herstel

U een SQL-database op elke server in een Azure-regio herstellen vanuit de meest recente geo-gerepliceerde back-ups. Geo-restore maakt gebruik van een geo-gerepliceerde back-up als bron. U geo-restore aanvragen, zelfs als de database of het datacenter niet toegankelijk is vanwege een storing.

Geo-restore is de standaardhersteloptie wanneer uw database niet beschikbaar is vanwege een incident in het hostinggebied. U kunt de database herstellen naar een server in elke andere regio. Er is een vertraging tussen het moment waarop een back-up wordt gemaakt en wanneer deze wordt gegeo-gerepliceerd naar een Azure-blob in een andere regio. Hierdoor kan de herstelde database maximaal een uur achterlopen op de oorspronkelijke database. In de volgende afbeelding ziet u een databaseherstel van de laatst beschikbare back-up in een andere regio.

![Afbeelding van geo-restore](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-herstel met behulp van de Azure-portal

Vanuit de Azure-portal maakt u een nieuwe afzonderlijke of beheerde instantiedatabase en selecteert u een beschikbare geo-herstelback-up. De nieuw gemaakte database bevat de geo-herstelde back-upgegevens.

#### <a name="single-azure-sql-database"></a>Eén Azure SQL-database

Voer de volgende stappen uit om één SQL-database te herstellen vanuit de Azure-portal in de regio en de server van uw keuze:

1. Selecteer**SQL-database** **maken** > in **dashboard**. Voer op het tabblad **Basisbeginselen** de vereiste gegevens in.
2. Selecteer **Extra instellingen**.
3. Selecteer **Back-up**voor **het gebruik van bestaande gegevens**.
4. Selecteer **voor back-up**een back-up in de lijst met beschikbare back-ups voor geoherstel.

    ![Schermafbeelding van SQL-databaseopties maken](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Voltooi het proces van het maken van een nieuwe database vanuit de back-up. Wanneer u de enige Azure SQL-database maakt, bevat deze de herstelde geo-herstelback-up.

#### <a name="managed-instance-database"></a>Beheerde instantiedatabase

Als u een beheerde instantiedatabase wilt geo-herstellen vanuit de Azure-portal naar een bestaand beheerd exemplaar in een gebied naar keuze, selecteert u een beheerde instantie waarop u een database wilt herstellen. Volg deze stappen:

1. Selecteer **Nieuwe database**.
2. Typ een gewenste databasenaam.
3. Selecteer **Back-up** **onder Bestaande gegevens gebruiken**.
4. Selecteer een back-up in de lijst met beschikbare back-ups voor geoherstel.

    ![Schermafbeelding van nieuwe databaseopties](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Voltooi het proces van het maken van een nieuwe database. Wanneer u de instantiedatabase maakt, bevat deze de herstelde geoherstelback-back-up.

### <a name="geo-restore-by-using-powershell"></a>Geo-restore met PowerShell

#### <a name="single-azure-sql-database"></a>Eén Azure SQL-database

Zie PowerShell gebruiken [om een Azure SQL-enkele database te herstellen naar een eerder tijdstip voor](scripts/sql-database-restore-database-powershell.md)een PowerShell-script met de manier waarop u geo-herstel voor één SQL-database uitvoert.

#### <a name="managed-instance-database"></a>Beheerde instantiedatabase

Zie PowerShell gebruiken [om een beheerde instantiedatabase te herstellen naar een andere georegio voor](scripts/sql-managed-instance-restore-geo-backup.md)een PowerShell-script met een voorbeeldscript.

### <a name="geo-restore-considerations"></a>Overwegingen voor geoherstel

U geen point-in-time herstel uitvoeren in een geosecundaire database. U dit alleen doen in een primaire database. Zie Herstellen van een storing voor gedetailleerde informatie over het gebruik van geoherstel om te herstellen van [een storing.](sql-database-disaster-recovery.md)

> [!IMPORTANT]
> Geo-restore is de meest elementaire oplossing voor noodherstel die beschikbaar is in SQL Database. Het is gebaseerd op automatisch gemaakte geo-gerepliceerde back-ups met herstelpunt doelstelling (RPO) gelijk aan 1 uur, en de geschatte hersteltijd van maximaal 12 uur. Het garandeert niet dat de doelregio de capaciteit heeft om uw databases te herstellen na een regionale storing, omdat een sterke toename van de vraag waarschijnlijk is. Als uw toepassing relatief kleine databases gebruikt en niet essentieel is voor het bedrijf, is geo-restore een geschikte oplossing voor noodherstel. Voor bedrijfskritieke toepassingen waarvoor grote databases nodig zijn en die de bedrijfscontinuïteit moeten waarborgen, gebruikt u [Auto-failovergroepen.](sql-database-auto-failover-group.md) Het biedt een veel lagere RPO en hersteltijd doelstelling, en de capaciteit is altijd gegarandeerd. Zie Overzicht van bedrijfscontinuïteit voor meer informatie over de keuzevan de [bedrijfscontinuïteit.](sql-database-business-continuity.md)

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Programmatisch uitvoeren van herstel met behulp van geautomatiseerde back-ups

U Azure PowerShell of de REST API ook gebruiken voor herstel. In de volgende tabellen worden de beschikbare set opdrachten beschreven.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. Argumenten voor de opdrachten in de Az-module en in AzureRm-modules zijn voor een groot deel identiek.

#### <a name="single-azure-sql-database"></a>Eén Azure SQL-database

Zie [Herstellen-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)als u een zelfstandige of gepoolde database wilt herstellen.

  | Cmdlet | Beschrijving |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hiermee haalt u een of meer databases op. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hiermee haalt u een verwijderde database die u kunt herstellen op. |
  | [Get-azsqldatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hiermee haalt u een geografisch redundante back-up van een database op. |
  | [Herstel-azsqldatabase](/powershell/module/az.sql/restore-azsqldatabase) |Hiermee herstelt u een SQL-database. |

  > [!TIP]
  > Zie [Een SQL-database herstellen met PowerShell](scripts/sql-database-restore-database-powershell.md)voor een voorbeeld van PowerShell-script waarin wordt weergegeven hoe u een point-in-time-herstel van een database uitvoeren.

#### <a name="managed-instance-database"></a>Beheerde instantiedatabase

Zie [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)als u een beheerde instantiedatabase wilt herstellen.

  | Cmdlet | Beschrijving |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Krijgt een of meer beheerde exemplaren. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Krijgt een instantiedatabase. |
  | [Database restore-azsqlinstancedatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Hiermee herstelt u een instantiedatabase. |

### <a name="rest-api"></a>REST API

Ga als volgt te werk om een enkele of samengevoegde database te herstellen met de REST API:

| API | Beschrijving |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Hiermee herstelt u een database. |
| [Databasestatus maken of bijwerken](https://docs.microsoft.com/rest/api/sql/operations) |Geeft als resultaat de status tijdens een herstelbewerking. |

### <a name="azure-cli"></a>Azure-CLI

#### <a name="single-azure-sql-database"></a>Eén Azure SQL-database

Zie [az sql db restore](/cli/azure/sql/db#az-sql-db-restore)als u een enkele of gepoolde database wilt herstellen met de Azure CLI.

#### <a name="managed-instance-database"></a>Beheerde instantiedatabase

Zie [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)als u een beheerde instantiedatabase wilt herstellen met behulp van de Azure CLI.

## <a name="summary"></a>Samenvatting

Automatische back-ups beschermen uw databases tegen gebruikers- en toepassingsfouten, het per ongeluk verwijderen van databases en langdurige uitval. Deze ingebouwde mogelijkheid is beschikbaar voor alle servicelagen en rekenformaten.

## <a name="next-steps"></a>Volgende stappen

- [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md)
- [Geautomatiseerde back-ups van SQL Database](sql-database-automated-backups.md)
- [Lange bewaartermijn](sql-database-long-term-retention.md)
- Zie [Actieve georeplicatie-](sql-database-active-geo-replication.md) of [Auto-failovergroepen](sql-database-auto-failover-group.md)voor meer informatie over snellere herstelopties.
