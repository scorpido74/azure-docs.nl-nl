---
title: Migreren met dumpen en herstellen - Azure Database voor MariaDB
description: In dit artikel worden twee veelvoorkomende manieren uitgelegd om back-ups te maken en databases in uw Azure-database voor MariaDB te herstellen, met behulp van hulpprogramma's zoals mysqldump, MySQL Workbench en PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 72735e83af97fde8377e27daa45501704ef5a3c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164539"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Uw MariaDB-database migreren naar Azure Database voor MariaDB met behulp van dump en herstel
In dit artikel worden twee veelvoorkomende manieren uitgelegd om back-ups te maken en databases in uw Azure-database voor MariaDB te herstellen
- Dumpen en herstellen vanaf de opdrachtregel (met mysqldump) 
- Dumpen en herstellen met PHPMyAdmin

## <a name="before-you-begin"></a>Voordat u begint
Om deze handleiding door te nemen, moet je het volgende hebben:
- [Azure-database maken voor MariaDB-server - Azure-portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) command-line utility geïnstalleerd op een machine.
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) of een ander MySQL-hulpprogramma van derden om opdrachten te dumpen en te herstellen.

## <a name="use-common-tools"></a>Algemene gereedschappen gebruiken
Gebruik algemene hulpprogramma's en hulpprogramma's zoals MySQL Workbench of mysqldump om op afstand gegevens te verbinden en te herstellen in Azure Database voor MariaDB. Gebruik dergelijke hulpprogramma's op uw clientmachine met een internetverbinding om verbinding te maken met de Azure Database voor MariaDB. Gebruik een SSL-versleutelde verbinding voor de beste beveiligingsprocedures, zie ook [SSL-connectiviteit configureren in Azure Database voor MariaDB.](concepts-ssl-connection-security.md) U hoeft de dumpbestanden niet te verplaatsen naar een speciale cloudlocatie wanneer u migreert naar Azure Database voor MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Veelvoorkomende toepassingen voor dumpen en herstellen
U MySQL-hulpprogramma's zoals mysqldump en mysqlpump gebruiken om databases in verschillende veelvoorkomende scenario's te dumpen en te laden in een Azure-database voor MariaDB-server. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Gebruik databasedumps wanneer u de hele database migreert. Deze aanbeveling geldt bij het verplaatsen van een grote hoeveelheid gegevens of wanneer u de onderbreking van de service voor live sites of toepassingen wilt minimaliseren. 
-  Zorg ervoor dat alle tabellen in de database de InnoDB-opslag-engine gebruiken bij het laden van gegevens in Azure Database for MariaDB. Azure Database voor MariaDB ondersteunt alleen InnoDB Storage engine en ondersteunt daarom geen alternatieve opslagengines. Als uw tabellen zijn geconfigureerd met andere opslagengines, converteert u ze naar de InnoDB-engineindeling voordat u naar Azure Database voor MariaDB wordt gestroomd.
   Als u bijvoorbeeld een WordPress of WebApp hebt met de MyISAM-tabellen, converteert u deze tabellen eerst door te migreren naar de InnoDB-indeling voordat u deze herstelt naar Azure Database voor MariaDB. Gebruik de `ENGINE=InnoDB` clausule om de engine in te stellen die wordt gebruikt bij het maken van een nieuwe tabel en vervolgens de gegevens vóór het herstellen naar de compatibele tabel over te zetten. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- U kunt compatibiliteitsproblemen voorkomen door ervoor te zorgen dat dezelfde versie van MariaDB wordt gebruikt in het bron- en doelsysteem bij het dumpen van databases. Als uw bestaande MariaDB-server bijvoorbeeld versie 10.2 is, moet u migreren naar Azure Database voor MariaDB die is geconfigureerd om versie 10.2 uit te voeren. De `mysql_upgrade` opdracht werkt niet in een Azure Database voor MariaDB-server en wordt niet ondersteund. Als u moet upgraden naar MariaDB-versies, dump of exporteert u uw onderste versiedatabase naar een hogere versie van MariaDB in uw eigen omgeving. Voer `mysql_upgrade`vervolgens uit voordat u probeert te migreren naar een Azure-database voor MariaDB.

## <a name="performance-considerations"></a>Prestatieoverwegingen
Om de prestaties te optimaliseren, let u op deze overwegingen bij het dumpen van grote databases:
-   Gebruik `exclude-triggers` de optie in mysqldump bij het dumpen van databases. Sluit triggers uit dumpbestanden uit om te voorkomen dat de triggeropdrachten tijdens het herstellen van de gegevens worden geactiveerd. 
-   Gebruik `single-transaction` de optie om de transactieisolatiemodus in te stellen op HERHAALBAAR LEZEN en stuurt een SQL-instructie starttransactie naar de server voordat gegevens worden gedumpt. Als u veel tabellen binnen één transactie dumpt, wordt er extra opslagruimte verbruikt tijdens het herstellen. De `single-transaction` optie `lock-tables` en de optie sluiten elkaar uit omdat LOCK TABLES ervoor zorgt dat lopende transacties impliciet worden vastgelegd. Als u grote tafels `single-transaction` wilt `quick` dumpen, combineert u de optie met de optie. 
-   Gebruik `extended-insert` de syntaxis met meerdere rijen die verschillende waardelijsten bevat. Dit resulteert in een kleiner dumpbestand en versnelt inserts wanneer het bestand opnieuw wordt geladen.
-  Gebruik `order-by-primary` de optie in mysqldump bij het dumpen van databases, zodat de gegevens in primaire sleutelvolgorde worden gescript.
-   Gebruik `disable-keys` de optie in mysqldump bij het dumpen van gegevens om externe belangrijke beperkingen voor het laden uit te schakelen. Het uitschakelen van buitenlandse key checks zorgt voor prestatiewinst. Schakel de beperkingen in en controleer de gegevens na de belasting om de referentiële integriteit te garanderen.
-   Gebruik indien van toepassing partitietabellen.
-   Gegevens parallel laden. Vermijd te veel parallellisme waardoor u een resourcelimiet bereikt en beheer resources met behulp van de statistieken die beschikbaar zijn in de Azure-portal. 
-   Gebruik `defer-table-indexes` de optie in mysqlpump bij het dumpen van databases, zodat indexcreatie plaatsvindt nadat tabellengegevens zijn geladen.
-   Kopieer de back-upbestanden naar een Azure blob/store en voer het herstel vanaf daar uit, wat een stuk sneller zou moeten zijn dan het herstellen op internet uitvoeren.

## <a name="create-a-backup-file"></a>Een back-upbestand maken
Als u een back-up wilt maken van een bestaande MariaDB-database op de lokale on-premises server of in een virtuele machine, voert u de volgende opdracht uit met mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De parameters die moeten worden opteis, zijn:
- [uname] Uw databasegebruikersnaam 
- [pass] Het wachtwoord voor uw database (let op dat er geen ruimte is tussen -p en het wachtwoord) 
- [dbname] De naam van uw database 
- [backupfile.sql] De bestandsnaam voor uw databaseback-up 
- [--opt] De mysqldump-optie 

Als u bijvoorbeeld een back-up wilt maken van een database met de naam 'testdb' op uw MariaDB-server met de gebruikersnaam 'testuser' en zonder wachtwoord voor een bestand testdb_backup.sql, gebruikt u de volgende opdracht. De opdracht maakt `testdb` een back-up `testdb_backup.sql`van de database in een bestand met de naam , dat alle SQL-instructies bevat die nodig zijn om de database opnieuw te maken. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Als u specifieke tabellen in uw database wilt selecteren om een back-up te maken, geeft u de tabelnamen aan die zijn gescheiden door spaties. Als u bijvoorbeeld een back-up maakt van tabel1- en tabel2tabellen uit de 'testdb', volgt u het volgende voorbeeld: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Als u een back-up wilt maken van meer dan één database tegelijk, gebruikt u de databaseschakelaar en vermeldt u de databasenamen die worden gescheiden door spaties. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Een database maken op de doelserver
Maak een lege database op de doelazure-database voor MariaDB-server waar u de gegevens wilt migreren. Gebruik een tool zoals MySQL Workbench om de database te maken. De database kan dezelfde naam hebben als de database die de gedumpte gegevens bevat of u een database maken met een andere naam.

Zoek de verbindingsgegevens in het **overzicht** van uw Azure-database voor MariaDB om verbinding te maken.

![De verbindingsgegevens zoeken in de Azure-portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Voeg de verbindingsgegevens toe aan uw MySQL Workbench.

![MySQL-werkbankverbindingstekenreeks](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Uw MariaDB-database herstellen
Zodra u de doeldatabase hebt gemaakt, u de opdracht mysql of MySQL Workbench gebruiken om de gegevens te herstellen naar de specifieke nieuw gemaakte database vanuit het dumpbestand.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In dit voorbeeld u de gegevens herstellen naar de nieuw gemaakte database op de doelazure-database voor MariaDB-server.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exporteren met PHPMyAdmin
Om te exporteren, u de algemene tool phpMyAdmin gebruiken, die u mogelijk al lokaal in uw omgeving hebt geïnstalleerd. Ga als een export van uw MariaDB-database met PHPMyAdmin:
1. Open phpMyAdmin.
2. Selecteer uw database. Klik op de databasenaam in de lijst aan de linkerkant. 
3. Klik op de koppeling **Exporteren.** Er verschijnt een nieuwe pagina om de dump van de database te bekijken.
4. Klik in het gebied Exporteren op de koppeling **Alles selecteren** om de tabellen in uw database te kiezen. 
5. Klik in het gebied met SQL-opties op de juiste opties. 
6. Klik **op** de optie Opslaan als bestand en de bijbehorende compressieoptie en klik vervolgens op de knop **Gaan.** Er moet een dialoogvenster verschijnen waarin u wordt gevraagd het bestand lokaal op te slaan.

## <a name="import-using-phpmyadmin"></a>Importeren met PHPMyAdmin
Het importeren van uw database is vergelijkbaar met exporteren. Doe de volgende acties:
1. Open phpMyAdmin. 
2. Klik op de installatiepagina phpMyAdmin op **Toevoegen** om uw Azure-database voor MariaDB-server toe te voegen. Geef de verbindingsgegevens en inloggegevens op.
3. Maak een database met de juiste naam en selecteer deze aan de linkerkant van het scherm. Als u de bestaande database wilt herschrijven, klikt u op de databasenaam, schakelt u alle selectievakjes naast de tabelnamen in en selecteert u **Neerzetten** om de bestaande tabellen te verwijderen. 
4. Klik op de **SQL-koppeling** om de pagina weer te geven waar u SQL-opdrachten typen of uw SQL-bestand uploaden. 
5. Gebruik de **knop Bladeren** om het databasebestand te vinden. 
6. Klik op de knop **Start** om de back-up te exporteren, de SQL-opdrachten uit te voeren en uw database opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen
- [Maak toepassingen verbinden met Azure Database voor MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
