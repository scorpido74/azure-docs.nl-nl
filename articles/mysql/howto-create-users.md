---
title: Gebruikers maken - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u nieuwe gebruikersaccounts maken om te communiceren met een Azure Database voor MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c4e95164badaf0b255f5ee76d0fec7686c2abf8b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382865"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Gebruikers maken in Azure Database voor MySQL-server

In dit artikel wordt beschreven hoe u gebruikers maken in een Azure Database voor MySQL-server.

Toen u uw Azure Database voor MySQL voor het eerst hebt gemaakt, hebt u een gebruikersnaam en wachtwoord voor het inloggen van een serverbeheerder opgegeven. Voor meer informatie u de [Quickstart](quickstart-create-mysql-server-database-using-azure-portal.md)volgen. U de gebruikersnaam van uw serverbeheerder inlogfunctie vinden via de Azure-portal.

De serverbeheerder krijgt bepaalde bevoegdheden voor uw server zoals vermeld: SELECT, INSERT, UPDATE, Delete, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, WEERGAVE MAKEN, WEERGAVE WEERGEVEN, ROUTINE MAKEN, ROUTINE WIJZIGEN, GEBRUIKER MAKEN, GEBEURTENIS, TRIGGER

Zodra de Azure Database voor MySQL-server is gemaakt, u het eerste gebruikersaccount voor serverbeheerders gebruiken om extra gebruikers te maken en beheerderstoegang te verlenen. Het serverbeheeraccount kan ook worden gebruikt om minder bevoorrechte gebruikers te maken die toegang hebben tot afzonderlijke databaseschema's.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Extra beheerdersgebruikers maken in Azure Database voor MySQL

1. Download de verbindingsgegevens en de gebruikersnaam van de beheerder.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U de servernaam en aanmeldingsgegevens eenvoudig vinden op de pagina **serveroverzicht** of de pagina **Eigenschappen** in de Azure-portal.

2. Gebruik het beheerdersaccount en het wachtwoord om verbinding te maken met uw databaseserver. Gebruik uw voorkeursclienttool, zoals MySQL Workbench, mysql.exe, HeidiSQL of anderen.
   Zie [MySQL Workbench gebruiken om verbinding te maken en querygegevens op te vragen](./connect-workbench.md) als u niet zeker weet hoe u verbinding maken.

3. Bewerk en voer de volgende SQL-code uit. Vervang uw nieuwe gebruikersnaam voor `new_master_user`de waarde van de tijdelijke aanduiding . Met deze syntaxis worden de vermelde bevoegdheden voor alle databaseschema *'s*( . ) toegewezen aan de gebruikersnaam (new_master_user in dit voorbeeld).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Controleer de subsidies

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Databasegebruikers maken in Azure Database voor MySQL

1. Download de verbindingsgegevens en de gebruikersnaam van de beheerder.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U de servernaam en aanmeldingsgegevens eenvoudig vinden op de pagina **serveroverzicht** of de pagina **Eigenschappen** in de Azure-portal.

2. Gebruik het beheerdersaccount en het wachtwoord om verbinding te maken met uw databaseserver. Gebruik uw voorkeursclienttool, zoals MySQL Workbench, mysql.exe, HeidiSQL of anderen.
   Zie [MySQL Workbench gebruiken om verbinding te maken en querygegevens op te vragen](./connect-workbench.md) als u niet zeker weet hoe u verbinding maken.

3. Bewerk en voer de volgende SQL-code uit. Vervang de tijdelijke `db_user` aanduidingswaarde door uw beoogde nieuwe `testdb` gebruikersnaam en tijdelijke waarde door uw eigen databasenaam.

   Met deze syntaxis van sql-code wordt bijvoorbeeld een nieuwe database met de naam testdb gemaakt. Vervolgens wordt een nieuwe gebruiker in de MySQL-service gemaakt en worden\*alle bevoegdheden toegekend aan het nieuwe databaseschema (testdb. ) voor die gebruiker.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Controleer de subsidies in de database.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Meld u aan bij de server en geef de aangewezen database op met de nieuwe gebruikersnaam en het nieuwe wachtwoord. In dit voorbeeld wordt de opdrachtregel mysql weergegeven. Met deze opdracht wordt u gevraagd om het wachtwoord voor de gebruikersnaam. Vervang uw eigen servernaam, databasenaam en gebruikersnaam.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de machines van de nieuwe gebruikers om verbinding te maken: [Azure Database voor MySQL-firewallregels maken en beheren met behulp van de Azure-portal](howto-manage-firewall-using-portal.md) of [Azure CLI](howto-manage-firewall-using-cli.md).

Zie MySQL-productdocumentatie voor [gebruikersaccountbeheer](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [GRANT-syntaxis](https://dev.mysql.com/doc/refman/5.7/en/grant.html)en [Bevoegdheden](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)voor meer informatie over gebruikersaccountbeheer .
