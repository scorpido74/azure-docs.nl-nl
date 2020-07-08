---
title: Replicatie van gegevens configureren-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u Replicatie van inkomende gegevens instelt voor Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/11/2020
ms.openlocfilehash: 7b66f227469328767f23c6858fda15803832704b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737560"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL Replicatie van inkomende gegevens configureren

In dit artikel wordt beschreven hoe u [replicatie van inkomende gegevens](concepts-data-in-replication.md) instelt in azure database for MySQL door de hoofd-en replica servers te configureren. In dit artikel wordt ervan uitgegaan dat u een eerdere ervaring hebt met MySQL-servers en-data bases.

> [!NOTE]
> Afwijking-vrije communicatie
>
> Micro soft biedt ondersteuning voor een gevarieerde en inbegrips omgeving. Dit artikel bevat verwijzingen naar het woord _Slave_. De micro soft- [stijl gids voor beschik bare communicatie](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) herkent deze als een uitsluitend woord. Het woord wordt in dit artikel gebruikt voor consistentie omdat het momenteel het woord is dat wordt weer gegeven in de software. Wanneer de software is bijgewerkt om het woord te verwijderen, wordt dit artikel zodanig bijgewerkt dat het in uitlijning is.
>

Voor het maken van een replica in de Azure Database for MySQL-service, [replicatie van inkomende gegevens](concepts-data-in-replication.md) synchroniseert gegevens van een hoofd-mysql-server on-premises, in virtuele machines (vm's) of in Cloud database services. Replicatie van binnenkomende gegevens is gebaseerd op het binaire logbestand (binlog) met replicatie op basis van positie eigen aan MySQL. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Bekijk de [beperkingen en vereisten](concepts-data-in-replication.md#limitations-and-considerations) van gegevens replicatie voordat u de stappen in dit artikel uitvoert.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Een MySQL-server maken die als replica moet worden gebruikt

1. Een nieuwe Azure Database for MySQL-server maken

   Een nieuwe MySQL-server maken (bijvoorbeeld "replica.mysql.database.azure.com"). Raadpleeg [een Azure database for mysql-server maken met behulp van de Azure portal voor het](quickstart-create-mysql-server-database-using-azure-portal.md) maken van de server. Deze server is de replica-server in Replicatie van inkomende gegevens.

   > [!IMPORTANT]
   > De Azure Database for MySQL-server moet worden gemaakt in de Algemeen of de prijs categorie geoptimaliseerd voor geheugen.
   > 

2. Dezelfde gebruikers accounts en bijbehorende bevoegdheden maken

   Gebruikers accounts worden niet gerepliceerd van de hoofd server naar de replica server. Als u van plan bent gebruikers toegang te geven tot de replica-server, moet u hand matig alle accounts en de bijbehorende bevoegdheden maken op deze nieuw gemaakte Azure Database for MySQL server.

3. Voeg het IP-adres van de hoofd server toe aan de firewall regels van de replica. 

   Firewallregels bijwerken met de [Azure-portal](howto-manage-firewall-using-portal.md) of [Azure CLI](howto-manage-firewall-using-cli.md).

## <a name="configure-the-master-server"></a>De hoofd server configureren
De volgende stappen maken en configureren van de MySQL-server die on-premises wordt gehost, op een virtuele machine of database service die wordt gehost door andere cloud providers voor Replicatie van inkomende gegevens. Deze server is de ' Master ' in replicatie van gegevens.


1. Controleer de [vereisten van de hoofd server](concepts-data-in-replication.md#requirements) voordat u doorgaat. 

   Zorg er bijvoorbeeld voor dat de hoofd server zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat en dat de hoofd server een **openbaar IP-adres**heeft, de DNS openbaar toegankelijk is of een Fully QUALIFIED domain name (FQDN) heeft. 
   
   Test de verbinding met de hoofd server door te proberen verbinding te maken vanaf een hulp programma zoals de MySQL-opdracht regel die wordt gehost op een andere computer of op basis van het [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview) dat beschikbaar is in de Azure Portal.

2. Binaire logboek registratie inschakelen

   Controleer of binaire logboek registratie is ingeschakeld op de Master door de volgende opdracht uit te voeren: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Als de variabele [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) wordt geretourneerd met de waarde ' on ', wordt de binaire logboek registratie ingeschakeld op de server. 

   Als `log_bin` wordt geretourneerd met de waarde uit, schakelt u binaire logboek registratie in door het bestand My. cnf te bewerken, zodat de `log_bin=ON` server opnieuw wordt opgestart om de wijziging van kracht te laten worden.

3. Instellingen van de hoofd server

   Replicatie van inkomende gegevens vereist dat para meters `lower_case_table_names` consistent zijn tussen de Master-en replica-servers. Deze para meter is standaard 1 in Azure Database for MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Een nieuwe replicatie functie maken en machtigingen instellen

   Maak een gebruikers account op de hoofd server die is geconfigureerd met replicatie bevoegdheden. Dit kan worden gedaan via SQL-opdrachten of een hulp programma zoals MySQL Workbench. Overweeg of u van plan bent te repliceren met SSL, omdat dit moet worden opgegeven bij het maken van de gebruiker. Raadpleeg de MySQL-documentatie voor meer informatie over het [toevoegen van gebruikers accounts](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) op uw hoofd server. 

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


5. De master-server instellen op de modus alleen-lezen

   Voordat u begint met het dumpen van de data base, moet de server in de modus alleen-lezen worden geplaatst. In de modus alleen-lezen kan de Master geen schrijf transacties verwerken. Beoordeel de impact op uw bedrijf en plan het alleen-lezen venster in een rustige tijd, indien nodig.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Naam en offset van binair logboek bestand ophalen

   Voer de [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) opdracht uit om de huidige binaire logboek bestandsnaam en-offset te bepalen.
    
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

   Als u twee servers wilt koppelen en replicatie wilt starten, meldt u zich aan bij de doel replica server in de Azure DB voor MySQL-service en stelt u het externe exemplaar in als de hoofd server. Dit wordt gedaan met behulp van de `mysql.az_replication_change_master` opgeslagen procedure op de Azure DB voor mysql-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: de hostnaam van de hoofd server
   - master_user: gebruikers naam voor de hoofd server
   - master_password: wacht woord voor de hoofd server
   - master_log_file: de naam van het binaire logboek bestand is niet actief`show master status`
   - master_log_pos: de positie van het binaire logboek van wordt uitgevoerd`show master status`
   - master_ssl_ca: de context van het CA-certificaat. Als SSL niet wordt gebruikt, geeft u een lege teken reeks door.
       - U wordt aangeraden deze para meter door te geven als een variabele. Raadpleeg de volgende voor beelden voor meer informatie.

> [!NOTE]
> Als de hoofd server wordt gehost in een Azure-VM, stelt u ' toegang tot Azure-Services toestaan ' in om de Master-en replica servers met elkaar te laten communiceren. Deze instelling kan worden gewijzigd via de **beveiligings** opties voor de verbinding. Raadpleeg de [firewall regels beheren via de portal](howto-manage-firewall-using-portal.md) voor meer informatie.

   **Voorbeelden**

   *Replicatie met SSL*

   De variabele `@cert` wordt gemaakt door de volgende mysql-opdrachten uit te voeren: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
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

   Als de status van `Slave_IO_Running` en `Slave_SQL_Running` Ja is en de waarde van `Seconds_Behind_Master` is 0, werkt replicatie goed. `Seconds_Behind_Master`Hiermee wordt aangegeven hoe laat de replica. Als de waarde niet 0 is, betekent dit dat de replica updates verwerkt. 

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
