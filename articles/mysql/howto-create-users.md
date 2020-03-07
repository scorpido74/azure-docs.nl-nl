---
title: Gebruikers maken-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u nieuwe gebruikers accounts kunt maken om te communiceren met een Azure Database for MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e76e63030cc8e10c857d361cca69e1d35ba8c2c1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385397"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Gebruikers maken op Azure Database for MySQL server 
In dit artikel wordt beschreven hoe u gebruikers kunt maken in een Azure Database for MySQL-server.

Wanneer u uw Azure Database for MySQL voor het eerst hebt gemaakt, hebt u de gebruikers naam en het wacht woord van de server beheerder aangemeld. Voor meer informatie kunt u de [Snelstartgids](quickstart-create-mysql-server-database-using-azure-portal.md)volgen. U kunt de gebruikers naam van de server beheerder aanmelden via de Azure Portal.

De gebruiker van de server beheerder heeft bepaalde bevoegdheden voor uw server als vermeld: selecteren, invoegen, bijwerken, verwijderen, maken, verwijderen, opnieuw laden, verwerken, verwijzingen, INDEX, wijzigen, data BASEs weer geven, tijdelijke tabellen maken, vergren delen, tabellen, uitvoeren, replicatie-slave, replicatie CLIENT, WEER GAVE MAKEN, WEER GAVE TONEN, ROUTINE MAKEN, ALTER ROUTINE, GEBRUIKER MAKEN, GEBEURTENIS, TRIGGER

Zodra de Azure Database for MySQL-server is gemaakt, kunt u het eerste gebruikers account van de server beheerder gebruiken om extra gebruikers te maken en beheerders toegang te verlenen. Het account voor de server beheerder kan ook worden gebruikt om gebruikers met minder bevoegdheden te maken die toegang hebben tot afzonderlijke database schema's.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Aanvullende gebruikers met beheerders rechten maken in Azure Database for MySQL
1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal. 

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals MySQL Workbench, mysql. exe, HeidiSQL of anderen. 
   Als u niet zeker weet hoe u verbinding kunt maken, raadpleegt u [MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen](./connect-workbench.md)

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de nieuwe gebruikers naam voor de waarde voor de tijdelijke aanduiding `new_master_user`. Met deze syntaxis worden de vermelde bevoegdheden voor alle database schema's ( *.* ) verleend aan de gebruikers naam (new_master_user in dit voor beeld). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. De subsidies verifiëren 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Database gebruikers maken in Azure Database for MySQL

1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal. 

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals MySQL Workbench, mysql. exe, HeidiSQL of anderen. 
   Als u niet zeker weet hoe u verbinding kunt maken, raadpleegt u [MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen](./connect-workbench.md)

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de waarde van de tijdelijke aanduiding `db_user` door de gewenste nieuwe gebruikers naam en de tijdelijke aanduiding `testdb` met uw eigen database naam.

   Deze SQL-code syntaxis maakt bijvoorbeeld een nieuwe Data Base met de naam testdb. Vervolgens wordt er een nieuwe gebruiker in de MySQL-service gemaakt en worden alle bevoegdheden verleend aan het nieuwe database schema (testdb.\*) voor die gebruiker. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Controleer de subsidies in de data base.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Meld u aan bij de server, waarbij u de aangewezen data base opgeeft, met de nieuwe gebruikers naam en wacht woord. In dit voor beeld wordt de MySQL-opdracht regel weer gegeven. Met deze opdracht wordt u gevraagd om het wacht woord voor de gebruikers naam. Vervang uw eigen server naam, database naam en gebruikers naam.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Volgende stappen
Open de firewall voor de IP-adressen van de computers van de nieuwe gebruikers zodat ze verbinding kunnen maken: [Azure database for MySQL firewall regels maken en beheren met behulp van de Azure Portal](howto-manage-firewall-using-portal.md) of [Azure cli](howto-manage-firewall-using-cli.md).

Zie voor meer informatie over beheer van gebruikers accounts MySQL-product documentatie voor [Gebruikersaccountbeheer](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [syntaxis van Grant](https://dev.mysql.com/doc/refman/5.7/en/grant.html)en [bevoegdheden](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
