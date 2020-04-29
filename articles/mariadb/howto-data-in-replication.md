---
title: Replicatie van gegevens configureren-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u Replicatie van inkomende gegevens instelt in Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/30/2020
ms.openlocfilehash: 332feffead74174ba0b9b278d8de1c5957d5b9e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422474"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Replicatie van inkomende gegevens configureren in Azure Database for MariaDB

In dit artikel wordt beschreven hoe u Replicatie van inkomende gegevens instelt in Azure Database for MariaDB door de hoofd-en replica servers te configureren. In dit artikel wordt ervan uitgegaan dat u een eerdere ervaring hebt met MariaDB-servers en-data bases.

Voor het maken van een replica in de Azure Database for MariaDB-service, Replicatie van inkomende gegevens synchroniseert gegevens van een Master MariaDB-server on-premises, in virtuele machines (Vm's) of in Cloud database services.

Bekijk de [beperkingen en vereisten](concepts-data-in-replication.md#limitations-and-considerations) van gegevens replicatie voordat u de stappen in dit artikel uitvoert.

> [!NOTE]
> Als uw hoofd Server versie 10,2 of nieuwer is, raden we u aan om Replicatie van inkomende gegevens in te stellen met behulp van de [algemene trans actie-id](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Een MariaDB-server maken om te gebruiken als replica

1. Maak een nieuwe Azure Database for MariaDB-server (bijvoorbeeld replica.mariadb.database.azure.com). De server is de replica server in Replicatie van inkomende gegevens.

    Zie [een Azure database for MariaDB-server maken met behulp van de Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)voor meer informatie over het maken van een server.

   > [!IMPORTANT]
   > U moet de Azure Database for MariaDB-server maken in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen.

2. Maak identieke gebruikers accounts en bijbehorende bevoegdheden.
    
    Gebruikers accounts worden niet gerepliceerd van de hoofd server naar de replica server. Als u gebruikers toegang wilt geven tot de replica-server, moet u hand matig alle accounts en de bijbehorende bevoegdheden maken op de nieuw gemaakte Azure Database for MariaDB-server.

3. Voeg het IP-adres van de hoofd server toe aan de firewall regels van de replica. 

   Firewallregels bijwerken met de [Azure-portal](howto-manage-firewall-portal.md) of [Azure CLI](howto-manage-firewall-cli.md).

## <a name="configure-the-master-server"></a>De hoofd server configureren

In de volgende stappen wordt de MariaDB-server die on-premises, in een VM of in een Cloud database service voor Replicatie van inkomende gegevens wordt gehost, voor bereid en geconfigureerd. De MariaDB-server is de Master in Replicatie van inkomende gegevens.

1. Controleer de [vereisten van de hoofd server](concepts-data-in-replication.md#requirements) voordat u doorgaat. 

   Zorg er bijvoorbeeld voor dat de hoofd server zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat en dat de hoofd server een **openbaar IP-adres**heeft, de DNS openbaar toegankelijk is of een Fully QUALIFIED domain name (FQDN) heeft. 
   
   Test de verbinding met de hoofd server door te proberen verbinding te maken vanaf een hulp programma zoals de MySQL-opdracht regel die wordt gehost op een andere computer of op basis van het [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview) dat beschikbaar is in de Azure Portal.

2. Schakel binaire logboek registratie in.
    
    Voer de volgende opdracht in om te controleren of binaire logboek registratie is ingeschakeld op de Master:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Als de variabele [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) de waarde `ON`retourneert, wordt de binaire logboek registratie ingeschakeld op de server.

   Als `log_bin` de waarde `OFF`wordt geretourneerd, bewerkt u het bestand **My. cnf** zodat binaire logboek registratie wordt `log_bin=ON` ingeschakeld. Start de server opnieuw op om de wijziging van kracht te laten worden.

3. Configureer de instellingen van de hoofd server.

    Replicatie van inkomende gegevens moet de para `lower_case_table_names` meter consistent zijn tussen de Master-en replica-servers. De `lower_case_table_names` para meter wordt standaard `1` ingesteld op Azure database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Maak een nieuwe replicatie functie en stel machtigingen in.

   Maak een gebruikers account op de hoofd server die is geconfigureerd met replicatie bevoegdheden. U kunt een account maken met behulp van SQL-opdrachten of MySQL Workbench. Als u van plan bent om met SSL te repliceren, moet u dit opgeven wanneer u het gebruikers account maakt.
   
   Zie de [MariaDB-documentatie](https://mariadb.com/kb/en/library/create-user/)voor meer informatie over het toevoegen van gebruikers accounts op uw hoofd server.

   Door de volgende opdrachten te gebruiken, heeft de nieuwe replicatie functie vanaf elke computer toegang tot de Master, niet alleen de computer die als host fungeert voor het model zelf. Geef voor deze toegang **syncuser\@'% '** op in de opdracht om een gebruiker te maken.
   
   Zie [account namen opgeven](https://mariadb.com/kb/en/library/create-user/#account-names)voor meer informatie over MariaDB-documentatie.

   **SQL-opdracht**

   - Replicatie met SSL

       Als u SSL wilt vereisen voor alle gebruikers verbindingen, voert u de volgende opdracht in om een gebruiker te maken:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicatie zonder SSL

       Als SSL niet is vereist voor alle verbindingen, voert u de volgende opdracht in om een gebruiker te maken:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Als u de replicatie functie in MySQL Workbench wilt maken, selecteert u in het deel venster **beheer** de optie **gebruikers en bevoegdheden**. Selecteer vervolgens **account toevoegen**.
 
   ![Gebruikers en bevoegdheden](./media/howto-data-in-replication/users_privileges.png)

   Voer een gebruikers naam in het veld **aanmeldings naam** in.

   ![Gebruiker synchroniseren](./media/howto-data-in-replication/syncuser.png)
 
   Selecteer het paneel **beheerders rollen** en selecteer in de lijst met **globale bevoegdheden** **replicatie-slave**. Selecteer **Toep assen** om de replicatie functie te maken.

   ![Replicatie-slave](./media/howto-data-in-replication/replicationslave.png)


5. Stel de hoofd server in op de modus alleen-lezen.

   Voordat u een Data Base dumpt, moet de server in de modus alleen-lezen worden geplaatst. In de modus alleen-lezen kan de Master geen schrijf transacties verwerken. Als u bedrijfs impact wilt voor komen, moet u het alleen-lezen venster tijdens een rustige tijd plannen.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. De huidige binaire logboek bestandsnaam en-offset ophalen.

   Voer de opdracht [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)uit om de naam van het huidige binaire logboek bestand en-offset te bepalen.
    
   ```sql
   show master status;
   ```
   De resultaten moeten er ongeveer uitzien als in de volgende tabel:
   
   ![Resultaten van de hoofd status](./media/howto-data-in-replication/masterstatus.png)

   Noteer de naam van het binaire bestand, omdat deze wordt gebruikt in latere stappen.
   
7. De GTID-positie ophalen (optioneel, vereist voor replicatie met GTID).

   Voer de functie [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) uit om de GTID-positie voor de bijbehorende binlog-bestands naam en-offset op te halen.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>De master server dumpen en herstellen

1. Alle data bases van de hoofd server dumpen.

   Gebruik mysqldump om alle data bases van de hoofd server te dumpen. Het is niet nodig om de MySQL-bibliotheek en test bibliotheek te dumpen.

    Zie [dump and Restore](howto-migrate-dump-restore.md)(Engelstalig) voor meer informatie.

2. Stel de hoofd server in op de modus lezen/schrijven.

   Nadat de data base is gedumpt, wijzigt u de Master MariaDB-server weer in de modus lezen/schrijven.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Herstel het dump bestand naar de nieuwe server.

   Herstel het dump bestand naar de server die in de Azure Database for MariaDB-service is gemaakt. Zie [dump & herstellen](howto-migrate-dump-restore.md) voor het herstellen van een dump bestand naar een MariaDB-server.

   Als het dump bestand groot is, uploadt u het naar een virtuele machine in azure binnen dezelfde regio als de replica server. Zet het bestand terug naar de Azure Database for MariaDB-server vanuit de VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>De Master-en replica servers koppelen om Replicatie van inkomende gegevens te starten

1. Stel de hoofd server in.

   Alle Replicatie van inkomende gegevens-functies worden uitgevoerd door opgeslagen procedures. U kunt alle procedures vinden op [replicatie van inkomende gegevens opgeslagen procedures](reference-data-in-stored-procedures.md). Opgeslagen procedures kunnen worden uitgevoerd in de MySQL-shell of MySQL Workbench.

   Als u twee servers wilt koppelen en replicatie wilt starten, meldt u zich aan bij de doel replica server in de Azure DB for MariaDB-service. Stel vervolgens het externe exemplaar in als de hoofd server met behulp `mysql.az_replication_change_master` van `mysql.az_replication_change_master_with_gtid` de of opgeslagen procedure op de Azure DB for MariaDB-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   of
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: de hostnaam van de hoofd server
   - master_user: gebruikers naam voor de hoofd server
   - master_password: wacht woord voor de hoofd server
   - master_log_file: de naam van het binaire logboek bestand is niet actief`show master status`
   - master_log_pos: de positie van het binaire logboek van wordt uitgevoerd`show master status`
   - master_gtid_pos: GTID positie van uitvoering`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: de context van het CA-certificaat. Als u geen gebruik maakt van SSL, geeft u een lege teken reeks door. *
    
    
    * We raden u aan de para meter master_ssl_ca aan te geven als een variabele. Zie de volgende voor beelden voor meer informatie.

   **Voorbeelden**

   - Replicatie met SSL

       Maak de variabele `@cert` door de volgende opdrachten uit te voeren:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replicatie met SSL wordt ingesteld tussen een hoofd server die wordt gehost in het domein companya.com en een replica server gehost in Azure Database for MariaDB. Deze opgeslagen procedure wordt uitgevoerd op de replica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicatie zonder SSL

       Replicatie zonder SSL wordt ingesteld tussen een hoofd server die wordt gehost in het domein companya.com en een replica server gehost in Azure Database for MariaDB. Deze opgeslagen procedure wordt uitgevoerd op de replica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Replicatie starten.

   Roep de `mysql.az_replication_start` opgeslagen procedure aan om de replicatie te starten.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Controleer de replicatie status.

   Roep de [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) opdracht op de replica server om de replicatie status weer te geven.
    
   ```sql
   show slave status;
   ```

   `Slave_SQL_Running` Als `Slave_IO_Running` de status `yes`en de waarde van `Seconds_Behind_Master` is `0`, werkt de replicatie. `Seconds_Behind_Master`Hiermee wordt aangegeven hoe laat de replica. Als dat niet `0`het geval is, verwerkt de replica de updates.

4. Werk de bijbehorende server variabelen bij om de replicatie van gegevens te beveiligen (alleen vereist voor replicatie zonder GTID).
    
    Vanwege een systeem eigen replicatie beperking in MariaDB moet u voor- [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) en [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) -variabelen instellen voor replicatie zonder het GTID-scenario.

    Controleer de en `sync_master_info` `sync_relay_log_info` de variabelen van uw slave-server om er zeker van te zijn dat de replicatie van de gegevens stabiel is `1`en stel de variabelen in op.
    
## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

### <a name="stop-replication"></a>Replicatie stoppen

Gebruik de volgende opgeslagen procedure om de replicatie tussen de hoofd-en replica server te stoppen:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>De replicatie relatie verwijderen

Als u de relatie tussen de Master-en replica server wilt verwijderen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>De replicatie fout overs Laan

Als u een replicatie fout wilt overs Laan en replicatie wilt toestaan, gebruikt u de volgende opgeslagen procedure:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [replicatie van inkomende gegevens](concepts-data-in-replication.md) voor Azure database for MariaDB.
