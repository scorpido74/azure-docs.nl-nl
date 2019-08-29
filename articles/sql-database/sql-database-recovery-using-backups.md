---
title: Een Azure-SQL database herstellen vanuit een back-up | Microsoft Docs
description: Meer informatie over tijdstippen herstellen, waarmee u een Azure SQL Database naar een eerder tijdstip kunt terugdraaien (tot 35 dagen).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: 00982ea837783a7e7a9dca257f04c77d48aceef2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103105"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Een Azure-SQL database herstellen met behulp van automatische database back-ups

SQL Database back-ups worden standaard opgeslagen in Blob-opslag met geo-replicatie (RA-GRS). De volgende opties zijn beschikbaar voor database herstel met behulp van [geautomatiseerde database back-ups](sql-database-automated-backups.md):

- Maak een nieuwe Data Base op dezelfde SQL Database Server die binnen de retentie periode is hersteld naar een bepaald tijdstip.
- Maak op dezelfde SQL Database Server een Data Base die is hersteld naar de verwijderings tijd voor een verwijderde data base.
- Maak een nieuwe Data Base op een SQL Database Server in dezelfde regio die is hersteld naar het punt van de meest recente back-ups.
- Maak een nieuwe Data Base op een SQL Database Server in een andere regio die is hersteld naar het punt van de meest recente gerepliceerde back-ups.

Als u [back-up lange termijn](sql-database-long-term-retention.md)retentie hebt geconfigureerd, kunt u ook een nieuwe data base maken van een LTR-back-up op een SQL database server.

> [!IMPORTANT]
> U kunt een bestaande data base niet overschrijven tijdens het terugzetten.

Wanneer u de service laag Standard of Premium gebruikt, maakt een herstelde data base een extra opslag kosten in de volgende omstandigheden:

- Herstellen van P11 – P15 naar S4-S12 of P1-P6 als de maximale grootte van de data base groter is dan 500 GB.
- Herstellen van P1 – P6 naar S4-S12 als de maximale grootte van de data base groter is dan 250 GB.

De extra kosten worden in rekening gebracht wanneer de maximale grootte van de herstelde data base groter is dan de hoeveelheid opslag die is opgenomen in de servicelaag en het prestatie niveau van de doel database. De extra opslag ruimte die boven de inbegrepen hoeveelheid is ingericht, wordt extra in rekening gebracht. Zie de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag. Als de daad werkelijke hoeveelheid gebruikte ruimte kleiner is dan de hoeveelheid inbegrepen opslag, kunt u deze extra kosten voor komen door de maximale database grootte in te stellen op de opgenomen hoeveelheid.

> [!NOTE]
> [Automatische database back-ups](sql-database-automated-backups.md) worden gebruikt wanneer u een [kopie van een Data Base](sql-database-copy.md)maakt.

## <a name="recovery-time"></a>Herstel tijd

De herstel tijd voor het herstellen van een Data Base met behulp van automatische database back-ups wordt beïnvloed door verschillende factoren:

- De grootte van de data base
- De reken grootte van de data base
- Het aantal transactie logboeken dat betrokken is
- De hoeveelheid activiteit die opnieuw moet worden afgespeeld om het herstel punt te herstellen
- De netwerk bandbreedte als het terugzetten naar een andere regio gaat
- Het aantal gelijktijdige herstel aanvragen dat wordt verwerkt in de doel regio

Voor een grote en/of zeer actieve Data Base kan het herstellen enkele uren duren. Als er sprake is van langdurige onderbreking in een regio, is het mogelijk dat er een groot aantal aanvragen voor geo-herstel wordt verwerkt door andere regio's. Wanneer er veel aanvragen zijn, kan de herstel tijd worden verhoogd voor data bases in die regio. De meeste data base-herstel bewerkingen zijn in minder dan 12 uur voltooid.

Voor één abonnement gelden beperkingen voor het aantal gelijktijdige herstel aanvragen.  Deze beperkingen zijn van toepassing op een wille keurige combi natie van herstel op tijdstippen, geo-herstel en herstel bewerkingen van back-ups voor lange termijn retentie):

| | **Maximum aantal gelijktijdige aanvragen dat wordt verwerkt** | **Maximum aantal gelijktijdige aanvragen dat wordt verzonden** |
| :--- | --: | --: |
|Eén data base (per abonnement)|10|60|
|Elastische pool (per groep)|4|200|
||||

Momenteel is er geen ingebouwde methode om de volledige server te herstellen. De [Azure SQL database: Volledig server herstel](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script is een voor beeld van hoe u deze taak kunt uitvoeren.

> [!IMPORTANT]
> Als u wilt herstellen met behulp van geautomatiseerde back-ups, moet u lid zijn van de rol SQL Server Inzender in het abonnement of de [eigenaar van het abonnement zijn. Zie RBAC: Ingebouwde rollen](../role-based-access-control/built-in-roles.md). U kunt herstellen met behulp van Azure Portal, PowerShell of de REST-API. U kunt Transact-SQL niet gebruiken.

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

U kunt een zelfstandige, gepoolde of exemplaar database herstellen naar een eerder tijdstip met behulp van de Azure Portal, [Power shell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)of de [rest API](https://docs.microsoft.com/rest/api/sql/databases). De aanvraag kan een servicelaag of reken grootte voor de herstelde data base opgeven. Zorg ervoor dat u voldoende resources hebt op de server waarop u de Data Base wilt herstellen. Zodra het proces is voltooid, wordt er een nieuwe data base gemaakt op dezelfde server als de oorspronkelijke data base. De herstelde data base wordt in rekening gebracht tegen normale tarieven op basis van de servicelaag en de reken grootte. Er worden geen kosten in rekening gebracht totdat het herstellen van de data base is voltooid.

Over het algemeen herstelt u een Data Base naar een eerder tijdstip voor herstel doeleinden. U kunt de herstelde data base behandelen als een vervanging voor de oorspronkelijke data base of als bron gegevens gebruiken om de oorspronkelijke Data Base bij te werken.

- **Database vervanging**

  Als de herstelde data base is bedoeld als vervanging voor de oorspronkelijke Data Base, moet u de reken grootte en de servicelaag van de oorspronkelijke data base opgeven. U kunt de naam van de oorspronkelijke data base wijzigen en de herstelde data base de oorspronkelijke naam geven met behulp van de opdracht [ALTER data base](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Gegevens herstellen**

  Als u van plan bent om gegevens op te halen uit de herstelde data base om te herstellen van een gebruiker of toepassings fout, moet u een gegevens herstel script schrijven en uitvoeren waarmee gegevens uit de herstelde data base worden geëxtraheerd en toegepast op de oorspronkelijke data base. Hoewel het herstellen van de herstel bewerking veel tijd kan duren, is de data base herstellen tijdens het herstel proces zichtbaar in de lijst met data bases. Als u de data base verwijdert tijdens het herstellen, wordt de herstel bewerking geannuleerd en worden er geen kosten in rekening gebracht voor de data base die het herstellen niet heeft voltooid.

Als u een enkele, gepoolde of exemplaar database wilt herstellen naar een bepaald tijdstip met behulp van de Azure Portal, opent u de pagina voor uw data base en klikt u op de werk balk op **herstellen** .

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Zie [programmatisch herstel uitvoeren met automatische back-ups](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups) om een Data Base programmatisch te herstellen via een back-up

## <a name="deleted-database-restore"></a>Het terugzetten van de data base is verwijderd

U kunt een verwijderde data base herstellen naar de verwijderings tijd of een eerder tijdstip op dezelfde SQL Database Server met behulp van de Azure Portal, [Power shell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)of de [rest (CreateMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). U kunt [Verwijderde data base op een beheerd exemplaar herstellen met behulp van Power shell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Zie [een SQL database herstellen met Power shell](scripts/sql-database-restore-database-powershell.md)voor een voor beeld van een Power shell-script dat laat zien hoe u een verwijderde data base kunt herstellen.
> [!IMPORTANT]
> Als u een Azure SQL Database Server exemplaar verwijdert, worden alle bijbehorende data bases ook verwijderd en kunnen ze niet worden hersteld. Er is momenteel geen ondersteuning voor het herstellen van een verwijderde server.

### <a name="deleted-database-restore-using-the-azure-portal"></a>De data base is verwijderd met behulp van de Azure Portal

Als u een verwijderde data base wilt herstellen met behulp van de Azure Portal, opent u de pagina voor uw server en klikt u in het gebied bewerkingen op **Verwijderde data bases**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Zie [programmatisch herstel uitvoeren met automatische back-ups](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups) om een verwijderde data base programmatisch te herstellen

## <a name="geo-restore"></a>Geo-herstel

U kunt een SQL database op elke server in een Azure-regio herstellen vanuit de meest recente geo-gerepliceerde back-ups. Geo-Restore maakt gebruik van een geo-gerepliceerde back-up als bron. Dit kan ook worden aangevraagd als de data base of het Data Center niet toegankelijk is vanwege een storing.

Geo-Restore is de standaard herstel optie wanneer uw data base niet beschikbaar is vanwege een incident in de hosting regio. U kunt de data base herstellen naar een server in een andere regio. Er is een vertraging tussen het moment waarop een back-up wordt gemaakt en wanneer deze geo-gerepliceerd wordt naar een Azure-Blob in een andere regio. Als gevolg hiervan kan de herstelde data base Maxi maal één uur achter de oorspronkelijke Data Base zijn. In de volgende afbeelding ziet u het herstellen van de data base van de laatste beschik bare back-up in een andere regio.

![Geo-herstel](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Zie [een SQL database herstellen met Power shell](scripts/sql-database-restore-database-powershell.md)voor een voor beeld van een Power shell-script dat laat zien hoe u geo-herstel uitvoert.

Herstel naar een bepaald tijdstip op een geo-secundair wordt momenteel niet ondersteund. Herstel naar een bepaald tijdstip kan alleen worden uitgevoerd op een primaire data base. Zie [herstellen vanaf een storing](sql-database-disaster-recovery.md)voor gedetailleerde informatie over het gebruik van geo-Restore om te herstellen na een storing.

> [!IMPORTANT]
> Geo-Restore is de meest eenvoudige oplossing voor herstel na nood gevallen die beschikbaar is in SQL Database. Hierbij wordt gebruikgemaakt van automatisch gemaakte geo-gerepliceerde back-ups met RPO = 1 uur en de geschatte herstel tijd van Maxi maal 12 uur. Het biedt geen garantie dat de doel regio de capaciteit heeft om uw data base (s) na een regionale ourage te herstellen, omdat een sterke toename van de vraag waarschijnlijk is. Voor niet-bedrijfs kritieke toepassingen die gebruikmaken van relatief kleine data bases is geo-Restore een geschikte oplossing voor herstel na nood gevallen. Voor bedrijfskritische toepassingen die gebruikmaken van grote data bases en die bedrijfs continuïteit moeten garanderen, moet u [groepen voor automatische failover](sql-database-auto-failover-group.md)gebruiken. Het biedt een veel lager RPO en RTO en de capaciteit is altijd gegarandeerd. Zie [overzicht van bedrijfs continuïteit](sql-database-business-continuity.md)voor meer informatie over de mogelijkheden voor bedrijfs continuïteit.

### <a name="geo-restore-using-the-azure-portal"></a>Geo-herstel met behulp van de Azure Portal

Als u geografisch een Data Base wilt herstellen met behulp van de Azure Portal, opent u de pagina SQL-data bases en klikt u vervolgens op **toevoegen**. Selecteer in het tekstvak **bron selecteren** de optie **back-up**. Geef de back-up op van waaruit het herstel moet worden uitgevoerd in de regio en op de server van uw keuze.

> [!Note]
> Geo-Restore met behulp van de Azure Portal is niet beschikbaar in een beheerd exemplaar. Gebruik in plaats daarvan Power shell.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programmatisch herstel uitvoeren met behulp van automatische back-ups

Zoals eerder besproken, kan database herstel naast het Azure Portal ook programmatisch worden uitgevoerd met behulp van Azure PowerShell of de REST API. De volgende tabellen bevatten een beschrijving van de beschik bare opdrachten.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

- Als u een zelfstandige of gegroepeerde Data Base wilt herstellen, raadpleegt u [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Description |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hiermee haalt u een of meer databases op. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hiermee haalt u een verwijderde database die u kunt herstellen op. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hiermee haalt u een geografisch redundante back-up van een database op. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Hiermee herstelt u een SQL-database. |

  > [!TIP]
  > Zie [een SQL database herstellen met Power shell](scripts/sql-database-restore-database-powershell.md)voor een voor beeld van een Power shell-script dat laat zien hoe u een herstel bewerking naar een bepaald tijdstip uitvoert.

- Zie [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)voor informatie over het herstellen van een beheerde exemplaar database.

  | Cmdlet | Description |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Hiermee worden een of meer beheerde exemplaren opgehaald. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Hiermee haalt u een exemplaar database op. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Hiermee wordt een exemplaar database hersteld. |

### <a name="rest-api"></a>REST-API

Een enkele of gegroepeerde Data Base herstellen met behulp van de REST API:

| API | Description |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Hiermee herstelt u een Data Base |
| [Database status van maken of bijwerken ophalen](https://docs.microsoft.com/rest/api/sql/operations) |Hiermee wordt de status geretourneerd tijdens een herstel bewerking |

### <a name="azure-cli"></a>Azure-CLI

- Zie [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore)voor meer informatie over het herstellen van een enkele of gegroepeerde Data Base met behulp van Azure cli.
- Zie [AZ SQL DEELB Restore](/cli/azure/sql/midb#az-sql-midb-restore) voor meer informatie over het herstellen van een beheerd exemplaar met behulp van Azure cli

## <a name="summary"></a>Samenvatting

Met automatische back-ups worden uw data bases beschermd tegen gebruikers-en toepassings fouten, onbedoeld verwijderen van een Data Base en langdurige uitval. Deze ingebouwde mogelijkheid is beschikbaar voor alle service lagen en reken grootten.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van bedrijfs continuïteit](sql-database-business-continuity.md)voor een overzicht en scenario's voor bedrijfs continuïteit.
- Zie [SQL database automatische back-ups](sql-database-automated-backups.md)voor meer informatie over Azure SQL database automatische back-ups.
- Zie [lange termijn](sql-database-long-term-retention.md)retentie voor meer informatie over lange termijn retentie.
- Zie [actieve geo-replicatie](sql-database-active-geo-replication.md) of [groepen voor automatische failover](sql-database-auto-failover-group.md)voor meer informatie over snellere herstel opties.
