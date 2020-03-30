---
title: Gegevens-in-replicatie configureren - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u Gegevens-in-replicatie instelt voor Azure Database voor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 2148ce41267627d9d6e0437897a99a8dbdbe0746
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382763"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database configureren voor MySQL-gegevens-in-replicatie

In dit artikel leert u hoe u Gegevens-in-replicatie inde Azure Database for MySQL-service instelt door de hoofd- en replicaservers te configureren. Met data-in-replicatie u gegevens van een hoofd-MySQL-server die on-premises, in virtuele machines of databaseservices wordt gehost door andere cloudproviders, synchroniseren met een replica in de Azure Database for MySQL-service. 

In dit artikel wordt ervan uitgegaan dat u ten minste enige eerdere ervaring hebt met MySQL-servers en -databases.

Bekijk de [beperkingen en vereisten](concepts-data-in-replication.md#limitations-and-considerations) van de replicatie van gegevens voordat u de stappen in dit artikel uitvoert.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Een MySQL-server maken die als replica kan worden gebruikt

1. Een nieuwe Azure-database voor MySQL-server maken

   Maak een nieuwe MySQL-server (bijvoorbeeld. "replica.mysql.database.azure.com"). Raadpleeg [Een Azure Database voor MySQL-server maken met behulp van de Azure-portal](quickstart-create-mysql-server-database-using-azure-portal.md) voor het maken van de server. Deze server is de 'replica'-server in gegevensreplicatie.

   > [!IMPORTANT]
   > De Azure Database voor MySQL-server moet worden gemaakt in de prijzenslagen Algemeen Doel of Geheugengeoptimaliseerd.
   > 

2. Dezelfde gebruikersaccounts en bijbehorende bevoegdheden maken

   Gebruikersaccounts worden niet gerepliceerd van de hoofdserver naar de replicaserver. Als u van plan bent gebruikers toegang te bieden tot de replicaserver, moet u handmatig alle accounts en bijbehorende bevoegdheden maken op deze nieuw gemaakte Azure Database voor MySQL-server.

3. Voeg het IP-adres van de hoofdserver toe aan de firewallregels van de replica. 

   Firewallregels bijwerken met de [Azure-portal](howto-manage-firewall-using-portal.md) of [Azure CLI](howto-manage-firewall-using-cli.md).

## <a name="configure-the-master-server"></a>De hoofdserver configureren
De volgende stappen bereiden en configureren van de MySQL-server die on-premises wordt gehost, in een virtuele machine of databaseservice die wordt gehost door andere cloudproviders voor gegevensreplicatie. Deze server is de 'master' in Data-in replicatie.


1. Controleer de vereisten van de [hoofdserver](concepts-data-in-replication.md#requirements) voordat u verdergaat. 

   Zorg er bijvoorbeeld voor dat de hoofdserver zowel binnenkomend als uitgaand verkeer toestaat op poort 3306 en dat de hoofdserver een **openbaar IP-adres**heeft, dat de DNS openbaar toegankelijk is of een volledig gekwalificeerde domeinnaam (FQDN) heeft. 
   
   Test de connectiviteit met de hoofdserver door te proberen verbinding te maken vanuit een hulpprogramma zoals de MySQL-opdrachtregel die wordt gehost op een andere machine of vanuit de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) die beschikbaar is in de Azure-portal 

2. Binaire logboekregistratie inschakelen

   Controleer of binaire logboekregistratie is ingeschakeld op het stramien door de volgende opdracht uit te voeren: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Als de [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) variabele wordt geretourneerd met de waarde 'AAN', is binaire logboekregistratie ingeschakeld op uw server. 

   Als `log_bin` wordt geretourneerd met de waarde "UIT", schakel dan binaire logboekregistratie `log_bin=ON` in door uw my.cnf-bestand te bewerken, zodat en uw server opnieuw op te starten voordat de wijziging van kracht wordt.

3. Hoofdserverinstellingen

   Voor replicatie met `lower_case_table_names` gegevens moet de parameter consistent zijn tussen de hoofd- en replicaservers. Deze parameter is standaard 1 in Azure Database voor MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Een nieuwe replicatierol maken en machtigingen instellen

   Maak een gebruikersaccount op de hoofdserver dat is geconfigureerd met replicatiebevoegdheden. Dit kan via SQL-opdrachten of een tool zoals MySQL Workbench. Overweeg of u van plan bent te repliceren met SSL, omdat dit moet worden opgegeven bij het maken van de gebruiker. Raadpleeg de MySQL-documentatie om te begrijpen hoe [u gebruikersaccounts toevoegt](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) aan uw hoofdserver. 

   In de onderstaande opdrachten heeft de nieuwe replicatierol die is gemaakt toegang tot het model vanaf elke machine, niet alleen de machine die de master zelf host. Dit wordt gedaan door "syncuser@'%'" op te geven in de opdracht Gebruiker maken. Zie de MySQL-documentatie voor meer informatie over [het opgeven van accountnamen](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **SQL, opdracht**

   *Replicatie met SSL*

   Als u SSL voor alle gebruikersverbindingen wilt vereisen, gebruikt u de volgende opdracht om een gebruiker te maken: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicatie zonder SSL*

   Als SSL niet voor alle verbindingen vereist is, gebruikt u de volgende opdracht om een gebruiker te maken:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Als u de replicatierol wilt maken in MySQL Workbench, opent u het deelvenster **Gebruikers en bevoegdheden** in het deelvenster **Beheer.** Klik vervolgens op **Account toevoegen**. 
 
   ![Gebruikers en bevoegdheden](./media/howto-data-in-replication/users_privileges.png)

   Typ de gebruikersnaam in het veld **Aanmeldingsnaam.** 

   ![Gebruiker synchroniseren](./media/howto-data-in-replication/syncuser.png)
 
   Klik op het deelvenster **Beheerdersrollen** en selecteer **Replicatieslave** in de lijst **met globale bevoegdheden**. Klik vervolgens op **Toepassen** om de replicatierol te maken.

   ![Replicatieslave](./media/howto-data-in-replication/replicationslave.png)


5. De hoofdserver instellen op alleen-lezen modus

   Voordat u begint met het dumpen van de database, moet de server in de alleen-lezen modus worden geplaatst. In de alleen-lezenmodus kan de master geen schrijftransacties verwerken. Evalueer de impact op uw bedrijf en plan indien nodig het alleen-lezen venster in een daluren.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Binaire logboekbestandsnaam en -verschuiving en verschuiving downloaden

   Voer [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) de opdracht uit om de huidige binaire logboekbestandsnaam en -verschuiving te bepalen.
    
   ```sql
   show master status;
   ```
   De resultaten moeten zijn als volgende. Zorg ervoor dat u de binaire bestandsnaam nota als het zal worden gebruikt in latere stappen.

   ![Resultaten van masterstatus](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Hoofdserver dumpen en herstellen

1. Alle databases van hoofdserver dumpen

   U mysqldump gebruiken om databases van uw master te dumpen. Zie [Dump & Restore](concepts-migrate-dump-restore.md)voor meer informatie. Het is niet nodig om MySQL-bibliotheek en testbibliotheek te dumpen.

2. Hoofdserver instellen op lees-/schrijfmodus

   Zodra de database is gedumpt, wijzigt u de hoofdMySQL-server terug naar de lees-/schrijfmodus.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Dumpbestand naar nieuwe server herstellen naar nieuwe server

   Het dumpbestand herstellen naar de server die is gemaakt in de Azure Database for MySQL-service. Raadpleeg [& Herstellen voor](concepts-migrate-dump-restore.md) het herstellen van een dumpbestand naar een MySQL-server. Als het dumpbestand groot is, uploadt u het naar een virtuele machine in Azure binnen dezelfde regio als uw replicaserver. Herstel deze naar de Azure Database for MySQL-server vanaf de virtuele machine.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Master- en replicaservers koppelen om gegevens-in-replicatie te starten

1. Hoofdserver instellen

   Alle gegevens-in replicatiefuncties worden uitgevoerd volgens opgeslagen procedures. U alle procedures vinden bij [Gegevens-in Replicatie opgeslagen procedures](reference-data-in-stored-procedures.md). De opgeslagen procedures kunnen worden uitgevoerd in de MySQL-shell of MySQL Workbench. 

   Als u twee servers wilt koppelen en replicatie wilt starten, logt u in bij de doelreplicaserver in de Azure DB voor MySQL-service en stelt u de externe instantie in als hoofdserver. Dit gebeurt met `mysql.az_replication_change_master` behulp van de opgeslagen procedure op de Azure DB voor MySQL-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: hostnaam van de hoofdserver
   - master_user: gebruikersnaam voor de hoofdserver
   - master_password: wachtwoord voor de hoofdserver
   - master_log_file: binaire logboekbestandsnaam van het uitvoeren`show master status`
   - master_log_pos: binaire logboekpositie vanaf het uitvoeren`show master status`
   - master_ssl_ca: de context van het CA-certificaat. Als u SSL niet gebruikt, geeft u de lege tekenreeks door.
       - Het wordt aanbevolen om deze parameter als variabele in te geven. Zie de volgende voorbeelden voor meer informatie.

> [!NOTE]
> Als de hoofdserver wordt gehost in een Azure VM, stelt u 'Toegang tot Azure-services toestaan' in op 'AAN' zodat de hoofd- en replicaservers met elkaar kunnen communiceren. Deze instelling kan worden gewijzigd vanuit de **beveiligingsopties verbinding.** Raadpleeg [firewallregels beheren met portal](howto-manage-firewall-using-portal.md) voor meer informatie.

   **Voorbeelden**

   *Replicatie met SSL*

   De `@cert` variabele wordt gemaakt door de volgende MySQL-opdrachten uit te voeren: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replicatie met SSL is ingesteld tussen een hoofdserver die wordt gehost in het domein 'companya.com' en een replicaserver die wordt gehost in Azure Database voor MySQL. Deze opgeslagen procedure wordt uitgevoerd op de replica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replicatie zonder SSL*

   Replicatie zonder SSL is ingesteld tussen een hoofdserver die wordt gehost in het domein 'companya.com' en een replicaserver die wordt gehost in Azure Database voor MySQL. Deze opgeslagen procedure wordt uitgevoerd op de replica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Replicatie starten

   Roep `mysql.az_replication_start` de opgeslagen procedure aan om replicatie te starten.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Replicatiestatus controleren

   Roep [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) de opdracht op de replicaserver aan om de replicatiestatus weer te geven.
    
   ```sql
   show slave status;
   ```

   Als de `Slave_IO_Running` status `Slave_SQL_Running` van en zijn "ja" en de waarde van `Seconds_Behind_Master` is "0", replicatie werkt goed. `Seconds_Behind_Master`geeft aan hoe laat de replica is. Als de waarde niet '0' is, betekent dit dat de replica updates verwerkt. 

## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

### <a name="stop-replication"></a>Replicatie stoppen

Als u de replicatie tussen de stramien en de replicaserver wilt stoppen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Replicatierelatie verwijderen

Als u de relatie tussen hoofd- en replicaserver wilt verwijderen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Replicatiefout overslaan

Als u een replicatiefout wilt overslaan en replicatie wilt laten doorgaan, gebruikt u de volgende opgeslagen procedure:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Gegevens-in-replicatie](concepts-data-in-replication.md) voor Azure Database voor MySQL. 
