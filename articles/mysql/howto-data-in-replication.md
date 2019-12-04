---
title: Replicatie van gegevens configureren-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u Replicatie van inkomende gegevens instelt voor Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: eaebcf50084223e1c1f4df30294bece96cffda6d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774293"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL Replicatie van inkomende gegevens configureren

In dit artikel vindt u informatie over het instellen van Replicatie van inkomende gegevens in de Azure Database for MySQL-service door de Master-en replica servers te configureren. Met Replicatie van inkomende gegevens kunt u gegevens synchroniseren vanaf een MySQL-hoofd server die on-premises wordt uitgevoerd, in virtuele machines of database services die worden gehost door andere cloud providers in een replica in de Azure Database for MySQL-service. 

In dit artikel wordt ervan uitgegaan dat u ten minste een eerdere ervaring hebt met MySQL-servers en-data bases.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Een MySQL-server maken die als replica moet worden gebruikt

1. Een nieuwe Azure Database for MySQL-server maken

   Een nieuwe MySQL-server maken (bijvoorbeeld "replica.mysql.database.azure.com"). Raadpleeg [een Azure database for mysql-server maken met behulp van de Azure portal voor het](quickstart-create-mysql-server-database-using-azure-portal.md) maken van de server. Deze server is de replica-server in Replicatie van inkomende gegevens.

   > [!IMPORTANT]
   > De Azure Database for MySQL-server moet worden gemaakt in de Algemeen of de prijs categorie geoptimaliseerd voor geheugen.
   > 

2. Dezelfde gebruikers accounts en bijbehorende bevoegdheden maken

   Gebruikers accounts worden niet gerepliceerd van de hoofd server naar de replica server. Als u van plan bent gebruikers toegang te geven tot de replica-server, moet u hand matig alle accounts en de bijbehorende bevoegdheden maken op deze nieuw gemaakte Azure Database for MySQL server.

## <a name="configure-the-master-server"></a>De hoofd server configureren
De volgende stappen maken en configureren van de MySQL-server die on-premises wordt gehost, op een virtuele machine of database service die wordt gehost door andere cloud providers voor Replicatie van inkomende gegevens. Deze server is de ' Master ' in replicatie van gegevens. 

1. Binaire logboek registratie inschakelen

   Controleer of binaire logboek registratie is ingeschakeld op de Master door de volgende opdracht uit te voeren: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Als de variabele [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) wordt geretourneerd met de waarde ' on ', wordt de binaire logboek registratie ingeschakeld op de server. 

   Als `log_bin` wordt geretourneerd met de waarde uit, schakelt u binaire logboek registratie in door het bestand My. cnf te bewerken, zodat `log_bin=ON` en de server opnieuw op om de wijziging door te voeren.

2. Instellingen van de hoofd server

   Replicatie van inkomende gegevens moet para meter `lower_case_table_names` consistent zijn tussen de Master-en replica servers. Deze para meter is standaard 1 in Azure Database for MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Een nieuwe replicatie functie maken en machtigingen instellen

   Maak een gebruikers account op de hoofd server die is geconfigureerd met replicatie bevoegdheden. Dit kan worden gedaan via SQL-opdrachten of een hulp programma zoals MySQL Workbench. Overweeg of u van plan bent te repliceren met SSL, omdat dit moet worden opgegeven bij het maken van de gebruiker. Raadpleeg de MySQL-documentatie voor meer informatie over het [toevoegen van gebruikers accounts](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) op uw hoofd server. 

   In de onderstaande opdrachten is de nieuwe replicatie functie die is gemaakt, in staat om toegang te krijgen tot de Master vanaf elke computer, niet alleen de computer die als host fungeert voor het model zelf. Dit doet u door ' syncuser@ '% ' op te geven in de opdracht gebruiker maken. Raadpleeg de MySQL-documentatie voor meer informatie over het [opgeven van account namen](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **SQL-opdracht**

   *Replicatie met SSL*

   Als u SSL wilt vereisen voor alle gebruikers verbindingen, gebruikt u de volgende opdracht om een gebruiker te maken: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicatie zonder SSL*

   Als SSL niet is vereist voor alle verbindingen, gebruikt u de volgende opdracht om een gebruiker te maken:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Als u de replicatie functie in MySQL Workbench wilt maken, opent u het paneel **gebruikers en bevoegdheden** vanuit het deel venster **beheer** . Klik vervolgens op **account toevoegen**. 
 
   ![Gebruikers en bevoegdheden](./media/howto-data-in-replication/users_privileges.png)

   Typ de gebruikers naam in het veld **aanmeldings naam** . 

   ![Gebruiker synchroniseren](./media/howto-data-in-replication/syncuser.png)
 
   Klik op het paneel **beheerders rollen** en selecteer vervolgens **replicatie-slave** in de lijst met **globale bevoegdheden**. Klik vervolgens op **Toep assen** om de replicatie functie te maken.

   ![Replicatie-slave](./media/howto-data-in-replication/replicationslave.png)


4. De master-server instellen op de modus alleen-lezen

   Voordat u begint met het dumpen van de data base, moet de server in de modus alleen-lezen worden geplaatst. In de modus alleen-lezen kan de Master geen schrijf transacties verwerken. Beoordeel de impact op uw bedrijf en plan het alleen-lezen venster in een rustige tijd, indien nodig.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Naam en offset van binair logboek bestand ophalen

   Voer de [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) opdracht uit om te bepalen van de huidige binaire logboek bestandsnaam en-offset.
    
   ```sql
   show master status;
   ```
   De resultaten moeten er als volgt uitzien. Noteer de naam van het binaire bestand zoals deze wordt gebruikt in latere stappen.

   ![Resultaten van de hoofd status](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Hoofd server dumpen en herstellen

1. Alle data bases van de hoofd server dumpen

   U kunt mysqldump gebruiken om data bases van uw Master te dumpen. Raadpleeg [Dump & herstellen](concepts-migrate-dump-restore.md)voor meer informatie. Het is niet nodig om MySQL-bibliotheek en test bibliotheek te dumpen.

2. De modus lezen/schrijven instellen voor de hoofd server

   Wanneer de data base is gedumpt, wijzigt u de Master MySQL-server weer in de modus lezen/schrijven.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Dump bestand herstellen naar nieuwe server

   Herstel het dump bestand naar de server die in de Azure Database for MySQL-service is gemaakt. Raadpleeg [dump & herstellen](concepts-migrate-dump-restore.md) voor het herstellen van een dump bestand naar een MySQL-server. Als het dump bestand groot is, uploadt u het naar een virtuele machine in azure binnen dezelfde regio als de replica server. Herstel het op de Azure Database for MySQL-server vanaf de virtuele machine.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Master-en replica servers koppelen om Replicatie van inkomende gegevens te starten

1. Hoofd server instellen

   Alle Replicatie van inkomende gegevens-functies worden uitgevoerd door opgeslagen procedures. U kunt alle procedures vinden op [replicatie van inkomende gegevens opgeslagen procedures](reference-data-in-stored-procedures.md). De opgeslagen procedures kunnen worden uitgevoerd in de MySQL-shell of MySQL Workbench. 

   Als u twee servers wilt koppelen en replicatie wilt starten, meldt u zich aan bij de doel replica server in de Azure DB voor MySQL-service en stelt u het externe exemplaar in als de hoofd server. Dit wordt gedaan met behulp van de `mysql.az_replication_change_master` opgeslagen procedure op de Azure DB voor MySQL-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: de hostnaam van de hoofd server
   - master_user: gebruikers naam voor de hoofd server
   - master_password: wacht woord voor de hoofd server
   - master_log_file: de naam van een binair logboek bestand van wordt uitgevoerd `show master status`
   - master_log_pos: de positie van het binaire logboek van het uitvoeren van `show master status`
   - master_ssl_ca: de context van het CA-certificaat. Als SSL niet wordt gebruikt, geeft u een lege teken reeks door.
       - U wordt aangeraden deze para meter door te geven als een variabele. Raadpleeg de volgende voor beelden voor meer informatie.

> [!NOTE]
> Als de hoofd server wordt gehost in een Azure-VM, stelt u ' toegang tot Azure-Services toestaan ' in om de Master-en replica servers met elkaar te laten communiceren. Deze instelling kan worden gewijzigd via de **beveiligings** opties voor de verbinding. Raadpleeg de [firewall regels beheren via de portal](howto-manage-firewall-using-portal.md) voor meer informatie.

   **Voorbeelden**

   *Replicatie met SSL*

   De variabele `@cert` wordt gemaakt door de volgende MySQL-opdrachten uit te voeren: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replicatie met SSL wordt ingesteld tussen een hoofd server die wordt gehost in het domein ' companya.com ' en een replica server die wordt gehost in Azure Database for MySQL. Deze opgeslagen procedure wordt uitgevoerd op de replica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replicatie zonder SSL*

   Replicatie zonder SSL wordt ingesteld tussen een hoofd server die wordt gehost in het domein ' companya.com ' en een replica server die wordt gehost in Azure Database for MySQL. Deze opgeslagen procedure wordt uitgevoerd op de replica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Replicatie starten

   Roep de `mysql.az_replication_start` opgeslagen procedure aan om de replicatie te initiëren.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Replicatie status controleren

   Roep de [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) opdracht op de replica server om de replicatie status weer te geven.
    
   ```sql
   show slave status;
   ```

   Als de status van `Slave_IO_Running` en `Slave_SQL_Running` Ja zijn en de waarde van `Seconds_Behind_Master` 0 is, werkt de replicatie goed. `Seconds_Behind_Master` geeft aan hoe laat de replica. Als de waarde niet 0 is, betekent dit dat de replica updates verwerkt. 

## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

### <a name="stop-replication"></a>Replicatie stoppen

Gebruik de volgende opgeslagen procedure om de replicatie tussen de hoofd-en replica server te stoppen:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Replicatie relatie verwijderen

Als u de relatie tussen Master-en replica server wilt verwijderen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Replicatie fout overs Laan

Als u een replicatie fout wilt overs Laan en de replicatie wilt laten door gaan, gebruikt u de volgende opgeslagen procedure:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [replicatie van inkomende gegevens](concepts-data-in-replication.md) voor Azure database for MySQL. 
