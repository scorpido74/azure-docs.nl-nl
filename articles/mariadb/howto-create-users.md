---
title: Gebruikers maken - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u nieuwe gebruikersaccounts maken om te communiceren met een Azure Database voor MariaDB-server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 1b79a49b2fb87ebf180aaaa40447f40c5a982c2e
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632291"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Create users in Azure Database for MariaDB (Gebruikers maken in Azure Database for MariaDB) 
In dit artikel wordt beschreven hoe u gebruikers maken in Azure Database voor MariaDB.

Toen u uw Azure Database voor MariaDB voor het eerst hebt gemaakt, hebt u een gebruikersnaam en wachtwoord voor het inloggen van een serverbeheerder opgegeven. Voor meer informatie u de [Quickstart](quickstart-create-mariadb-server-database-using-azure-portal.md)volgen. U de gebruikersnaam van uw serverbeheerder inlogfunctie vinden via de Azure-portal.

De serverbeheerder krijgt bepaalde bevoegdheden voor uw server zoals vermeld: SELECT, INSERT, UPDATE, Delete, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, CREATE, EVENT, TRIGGER

Zodra de Azure Database voor MariaDB-server is gemaakt, u het eerste gebruikersaccount voor serverbeheerders gebruiken om extra gebruikers te maken en beheerders toegang tot deze server te verlenen. Het serverbeheeraccount kan ook worden gebruikt om minder bevoorrechte gebruikers te maken die toegang hebben tot afzonderlijke databaseschema's.

> [!NOTE]
> De SUPER privilege en DBA rol worden niet ondersteund. Bekijk de [bevoegdheden](concepts-limits.md#privilege-support) in het artikel beperkingen om te begrijpen wat er niet wordt ondersteund in de service.

## <a name="create-additional-admin-users"></a>Extra beheerdersgebruikers maken
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

## <a name="create-database-users"></a>Databasegebruikers maken

1. Download de verbindingsgegevens en de gebruikersnaam van de beheerder.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U de servernaam en aanmeldingsgegevens eenvoudig vinden op de pagina **serveroverzicht** of de pagina **Eigenschappen** in de Azure-portal. 

2. Gebruik het beheerdersaccount en het wachtwoord om verbinding te maken met uw databaseserver. Gebruik uw voorkeursclienttool, zoals MySQL Workbench, mysql.exe, HeidiSQL of anderen. 
   Zie [MySQL Workbench gebruiken om verbinding te maken en querygegevens op te vragen](./connect-workbench.md) als u niet zeker weet hoe u verbinding maken.

3. Bewerk en voer de volgende SQL-code uit. Vervang de tijdelijke `db_user` aanduidingswaarde door uw beoogde nieuwe `testdb` gebruikersnaam en tijdelijke waarde door uw eigen databasenaam.

   Met deze syntaxis van sql-code wordt bijvoorbeeld een nieuwe database met de naam testdb gemaakt. Vervolgens wordt een nieuwe gebruiker gemaakt in de Azure Database for MariaDB-service en\*worden alle bevoegdheden toegekend aan het nieuwe databaseschema (testdb. ) voor die gebruiker. 

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

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Zie MariaDB-documentatie voor [gebruikersaccountbeheer](https://mariadb.com/kb/en/library/user-account-management/), [SUBSIDIEsyntaxis](https://mariadb.com/kb/en/library/grant/)en [Bevoegdheden](https://mariadb.com/kb/en/library/grant/#privilege-levels)voor meer informatie over gebruikersaccountbeheer .

## <a name="next-steps"></a>Volgende stappen
Open de firewall voor de IP-adressen van de machines van de nieuwe gebruikers om verbinding te maken: [Azure Database maken en beheren voor MariaDB-firewallregels met behulp van de Azure-portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
