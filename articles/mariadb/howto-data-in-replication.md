---
title: Gegevens-in-replicatie configureren - Azure-database voor MariaDB
description: In dit artikel wordt beschreven hoe u Gegevens-in-replicatie in Azure-database instelt voor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/30/2020
ms.openlocfilehash: 332feffead74174ba0b9b278d8de1c5957d5b9e6
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422474"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Gegevens-in-replicatie configureren in Azure-database voor MariaDB

In dit artikel wordt beschreven hoe u Gegevens-in-replicatie in Azure Database voor MariaDB instelt door de hoofd- en replicaservers te configureren. In dit artikel wordt ervan uitgegaan dat u enige ervaring hebt met MariaDB-servers en -databases.

Als u een replica wilt maken in de Azure Database for MariaDB-service, synchroniseert Gegevens-in-replicatie gegevens van een on-premises master MariaDB-server, in virtuele machines (VM's) of in clouddatabaseservices.

Bekijk de [beperkingen en vereisten](concepts-data-in-replication.md#limitations-and-considerations) van de replicatie van gegevens voordat u de stappen in dit artikel uitvoert.

> [!NOTE]
> Als uw hoofdserver versie 10.2 of nieuwer is, raden we u aan Gegevens-in-replicatie in te stellen met behulp van [Global Transaction ID](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Een MariaDB-server maken om als replica te gebruiken

1. Maak een nieuwe Azure Database voor MariaDB-server (bijvoorbeeld replica.mariadb.database.azure.com). De server is de replicaserver in Gegevens-in-replicatie.

    Zie [Een Azure Database voor MariaDB-server maken voor](quickstart-create-mariadb-server-database-using-azure-portal.md)meer informatie over het maken van servers met behulp van de Azure-portal.

   > [!IMPORTANT]
   > U moet de Azure Database voor MariaDB-server maken in de prijzenlagen Algemeen Doel of Geheugengeoptimaliseerd.

2. Maak identieke gebruikersaccounts en bijbehorende bevoegdheden.
    
    Gebruikersaccounts worden niet gerepliceerd van de hoofdserver naar de replicaserver. Als u gebruikers toegang wilt bieden tot de replicaserver, moet u handmatig alle accounts en bijbehorende bevoegdheden maken op de nieuw gemaakte Azure Database voor MariaDB-server.

3. Voeg het IP-adres van de hoofdserver toe aan de firewallregels van de replica. 

   Firewallregels bijwerken met de [Azure-portal](howto-manage-firewall-portal.md) of [Azure CLI](howto-manage-firewall-cli.md).

## <a name="configure-the-master-server"></a>De hoofdserver configureren

De volgende stappen bereiden en configureren van de on-premises MariaDB-server, in een vm of in een clouddatabaseservice voor gegevensreplicatie. De MariaDB-server is de stramien in gegevensreplicatie.

1. Controleer de vereisten van de [hoofdserver](concepts-data-in-replication.md#requirements) voordat u verdergaat. 

   Zorg er bijvoorbeeld voor dat de hoofdserver zowel binnenkomend als uitgaand verkeer toestaat op poort 3306 en dat de hoofdserver een **openbaar IP-adres**heeft, dat de DNS openbaar toegankelijk is of een volledig gekwalificeerde domeinnaam (FQDN) heeft. 
   
   Test de connectiviteit met de hoofdserver door te proberen verbinding te maken vanuit een hulpprogramma zoals de MySQL-opdrachtregel die wordt gehost op een andere machine of vanuit de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) die beschikbaar is in de Azure-portal.

2. Schakel binaire logboekregistratie in.
    
    Voer de volgende opdracht in om te zien of binaire logboekregistratie is ingeschakeld op het stramien:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Als de [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) variabele `ON`de waarde retourneert, is binaire logboekregistratie ingeschakeld op uw server.

   Als `log_bin` de `OFF`waarde wordt geretourneerd, bewerk het `log_bin=ON` **bestand my.cnf** zodat binaire logboekregistratie wordt ingeschakeld. Start de server opnieuw op om de wijziging van kracht te laten worden.

3. Hoofdserverinstellingen configureren.

    Met replicatie in gegevens `lower_case_table_names` moet de parameter consistent zijn tussen de stramien- en replicaservers. De `lower_case_table_names` parameter is `1` standaard ingesteld op Azure Database voor MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Maak een nieuwe replicatierol en stel machtigingen in.

   Maak een gebruikersaccount op de hoofdserver die is geconfigureerd met replicatiebevoegdheden. U een account maken met SQL-opdrachten of MySQL Workbench. Als u van plan bent te repliceren met SSL, moet u dit opgeven wanneer u het gebruikersaccount maakt.
   
   Zie de [MariaDB-documentatie](https://mariadb.com/kb/en/library/create-user/)voor meer informatie over het toevoegen van gebruikersaccounts op uw hoofdserver.

   Door de volgende opdrachten te gebruiken, heeft de nieuwe replicatierol toegang tot het model vanaf elke machine, niet alleen de machine die de master zelf host. Geef voor deze toegang **de synchronisatiegebruiker\@'%'** op in de opdracht om een gebruiker te maken.
   
   Zie [Accountnamen opgeven](https://mariadb.com/kb/en/library/create-user/#account-names)voor meer informatie over MariaDB-documentatie.

   **SQL, opdracht**

   - Replicatie met SSL

       Als u SSL voor alle gebruikersverbindingen wilt vereisen, voert u de volgende opdracht in om een gebruiker te maken:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicatie zonder SSL

       Als SSL niet voor alle verbindingen vereist is, voert u de volgende opdracht in om een gebruiker te maken:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Als u de replicatierol wilt maken in MySQL Workbench, selecteert u in het deelvenster **Beheer** de optie **Gebruikers en bevoegdheden**. Selecteer vervolgens **Account toevoegen**.
 
   ![Gebruikers en bevoegdheden](./media/howto-data-in-replication/users_privileges.png)

   Voer een gebruikersnaam in het veld **Aanmeldingsnaam in.**

   ![Gebruiker synchroniseren](./media/howto-data-in-replication/syncuser.png)
 
   Selecteer het deelvenster **Beheerrollen** en selecteer vervolgens in de lijst **met globale bevoegdheden**de optie **Replicatieslave**. Selecteer **Toepassen** om de replicatierol te maken.

   ![Replicatieslave](./media/howto-data-in-replication/replicationslave.png)


5. Stel de hoofdserver in op alleen-lezen modus.

   Voordat u een database dumpt, moet de server in de alleen-lezen modus worden geplaatst. In de alleen-lezenmodus kan de master geen schrijftransacties verwerken. Om de impact van het bedrijf te voorkomen, plant u het alleen-lezen venster tijdens een daluren.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Download de huidige binaire logboekbestandsnaam en -verschuiving.

   Voer de opdracht [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)uit om de huidige binaire logboekbestandsnaam en -verschuiving te bepalen.
    
   ```sql
   show master status;
   ```
   De resultaten moeten vergelijkbaar zijn met de volgende tabel:
   
   ![Resultaten van masterstatus](./media/howto-data-in-replication/masterstatus.png)

   Let op de binaire bestandsnaam, omdat deze in latere stappen wordt gebruikt.
   
7. Krijg de GTID-positie (optioneel, nodig voor replicatie met GTID).

   Voer de [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) functie uit om de GTID-positie voor de bijbehorende binlog-bestandsnaam en -verschuiving te krijgen.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>De hoofdserver dumpen en herstellen

1. Dump alle databases van de hoofdserver.

   Gebruik mysqldump om alle databases van de hoofdserver te dumpen. Het is niet nodig om de MySQL-bibliotheek en testbibliotheek te dumpen.

    Zie [Dumpen en herstellen voor](howto-migrate-dump-restore.md)meer informatie.

2. Stel de hoofdserver in op de lees-/schrijfmodus.

   Nadat de database is gedumpt, wijzigt u de master MariaDB-server terug naar de lees-/schrijfmodus.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Het dumpbestand terugzetten naar de nieuwe server.

   Het dumpbestand herstellen naar de server die is gemaakt in de Azure Database for MariaDB-service. Zie [& herstellen plaatsen](howto-migrate-dump-restore.md) voor het herstellen van een dumpbestand naar een MariaDB-server.

   Als het dumpbestand groot is, uploadt u het naar een VM in Azure binnen dezelfde regio als uw replicaserver. Herstel deze naar de Azure Database voor MariaDB-server vanaf de VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>De hoofd- en replicaservers koppelen aan het starten van gegevens-in-replicatie

1. Stel de hoofdserver in.

   Alle gegevens-in replicatiefuncties worden uitgevoerd volgens opgeslagen procedures. U alle procedures vinden bij [Gegevens-in Replicatie opgeslagen procedures](reference-data-in-stored-procedures.md). Opgeslagen procedures kunnen worden uitgevoerd in de MySQL-shell of MySQL Workbench.

   Als u twee servers wilt koppelen en replicatie wilt starten, meldt u zich aan bij de doelreplicaserver in de Azure DB voor MariaDB-service. Stel vervolgens de externe instantie in als `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` hoofdserver met behulp van de of opgeslagen procedure op de Azure DB voor MariaDB-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   of
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: hostnaam van de hoofdserver
   - master_user: gebruikersnaam voor de hoofdserver
   - master_password: wachtwoord voor de hoofdserver
   - master_log_file: binaire logboekbestandsnaam van het uitvoeren`show master status`
   - master_log_pos: binaire logboekpositie vanaf het uitvoeren`show master status`
   - master_gtid_pos: GTID-positie van het lopen`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: de context van het CA-certificaat. Als u SSL niet gebruikt, geeft u een lege tekenreeks door.*
    
    
    *We raden aan om de parameter master_ssl_ca als variabele door te geven. Zie de volgende voorbeelden voor meer informatie.

   **Voorbeelden**

   - Replicatie met SSL

       Maak de `@cert` variabele door de volgende opdrachten uit te voeren:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replicatie met SSL is ingesteld tussen een hoofdserver die wordt gehost in het domein companya.com en een replicaserver die wordt gehost in Azure Database voor MariaDB. Deze opgeslagen procedure wordt uitgevoerd op de replica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicatie zonder SSL

       Replicatie zonder SSL is ingesteld tussen een hoofdserver die wordt gehost in het domein companya.com en een replicaserver die wordt gehost in Azure Database voor MariaDB. Deze opgeslagen procedure wordt uitgevoerd op de replica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Begin met replicatie.

   Bel `mysql.az_replication_start` de opgeslagen procedure om de replicatie te starten.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Controleer de replicatiestatus.

   Roep [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) de opdracht op de replicaserver aan om de replicatiestatus weer te geven.
    
   ```sql
   show slave status;
   ```

   Als `Slave_IO_Running` `Slave_SQL_Running` en zijn `yes`in de status, en de waarde van `Seconds_Behind_Master` is `0`, replicatie werkt. `Seconds_Behind_Master`geeft aan hoe laat de replica is. Als de waarde `0`dat niet is, verwerkt de replica updates.

4. Werk de bijbehorende servervariabelen bij om de replicatie van gegevens in te maken (alleen vereist voor replicatie zonder GTID).
    
    Vanwege een native replicatiebeperking in MariaDB, [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) moet u replicatie instellen en variabelen instellen [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) zonder het GTID-scenario.

    Controleer de gegevensservers `sync_master_info` `sync_relay_log_info` en variabelen van uw slave-server om te controleren of `1`de replicatie van de gegevens stabiel is en stel de variabelen in op .
    
## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

### <a name="stop-replication"></a>Replicatie stoppen

Als u de replicatie tussen de stramien en de replicaserver wilt stoppen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>De replicatierelatie verwijderen

Als u de relatie tussen de hoofdstram en de replicaserver wilt verwijderen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>De replicatiefout overslaan

Als u een replicatiefout wilt overslaan en replicatie wilt toestaan, gebruikt u de volgende opgeslagen procedure:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Gegevens-in-replicatie](concepts-data-in-replication.md) voor Azure Database voor MariaDB.
