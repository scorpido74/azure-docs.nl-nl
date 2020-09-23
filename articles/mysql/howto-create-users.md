---
title: Data bases en gebruikers maken-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u nieuwe gebruikers accounts kunt maken om te communiceren met een Azure Database for MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/2/2020
ms.openlocfilehash: 9b79a0f21135e91ab72a4c8a9e604b84b67df0a9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902817"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql-server"></a>Data bases en gebruikers maken op Azure Database for MySQL server

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In dit artikel wordt beschreven hoe u gebruikers kunt maken in een Azure Database for MySQL-server.

> [!NOTE]
> Afwijking-vrije communicatie
>
> Micro soft biedt ondersteuning voor een gevarieerde en inbegrips omgeving. Dit artikel bevat verwijzingen naar het woord _Slave_. De micro soft- [stijl gids voor beschik bare communicatie](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) herkent deze als een uitsluitend woord. Het woord wordt in dit artikel gebruikt voor consistentie omdat het momenteel het woord is dat wordt weer gegeven in de software. Wanneer de software is bijgewerkt om het woord te verwijderen, wordt dit artikel zodanig bijgewerkt dat het in uitlijning is.
>

Wanneer u uw Azure Database for MySQL voor het eerst hebt gemaakt, hebt u de gebruikers naam en het wacht woord van de server beheerder aangemeld. Voor meer informatie kunt u de [Snelstartgids](quickstart-create-mysql-server-database-using-azure-portal.md)volgen. U kunt de gebruikers naam van de server beheerder aanmelden via de Azure Portal.

De gebruiker met de server beheerder heeft bepaalde bevoegdheden voor uw server als vermeld: 

   SELECTEREN, INVOEGEN, BIJWERKEN, VERWIJDEREN, MAKEN, NEERZETTEN, OPNIEUW LADEN, VERWERKEN, VERWIJZINGEN, INDEX, WIJZIGEN, DATA BASES WEER GEVEN, TIJDELIJKE TABELLEN MAKEN, TABELLEN VERGREN DELEN, UITVOEREN, REPLICATIE SLAVE, REPLICATIE CLIENT, WEER GAVE, GEBRUIKER WEER GEVEN, GEBEURTENIS, TRIGGER


Zodra de Azure Database for MySQL-server is gemaakt, kunt u het eerste gebruikers account van de server beheerder gebruiken om extra gebruikers te maken en beheerders toegang te verlenen. Het account voor de server beheerder kan ook worden gebruikt om gebruikers met minder bevoegdheden te maken die toegang hebben tot afzonderlijke database schema's.

> [!NOTE]
> De rol SUPER privilege en DBA worden niet ondersteund. Lees de [bevoegdheden](concepts-limits.md#privilege-support) in het artikel beperkingen om te begrijpen wat er niet wordt ondersteund in de service.

## <a name="how-to-create-database-with-non-admin-user-in-azure-database-for-mysql"></a>Data Base met niet-beheerder-gebruiker maken in Azure Database for MySQL

1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal.

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals MySQL Workbench, mysql.exe, HeidiSQL of anderen.
   Als u niet zeker weet hoe u verbinding kunt maken, raadpleegt u MySQL Workbench gebruiken om [verbinding te maken en gegevens op te vragen voor één server](./connect-workbench.md) of om [verbinding te maken en gegevens op te vragen voor een flexibele server](./flexible-server/connect-workbench.md)

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de waarde van de tijdelijke aanduiding door `db_user` de gewenste nieuwe gebruikers naam en de tijdelijke aanduiding voor de `testdb` naam van uw eigen data base.

   Deze SQL-code syntaxis maakt bijvoorbeeld een nieuwe Data Base met de naam testdb. Vervolgens wordt er een nieuwe gebruiker in de MySQL-service gemaakt en worden alle bevoegdheden verleend aan het nieuwe database schema (testdb. \* ) voor die gebruiker.

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

# <a name="single-server"></a>[Eén server](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
# <a name="flexible-server"></a>[Flexibele server](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Aanvullende gebruikers met beheerders rechten maken in Azure Database for MySQL

1. De verbindings gegevens en de gebruikers naam van de beheerder ophalen.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U kunt eenvoudig de server naam en aanmeldings gegevens vinden op de pagina **overzicht** van de server of op de pagina **eigenschappen** in de Azure Portal.

2. Gebruik het beheerders account en-wacht woord om verbinding te maken met uw database server. Gebruik het client hulpprogramma van uw voor keur, zoals MySQL Workbench, mysql.exe, HeidiSQL of anderen.
   Als u niet zeker weet hoe u verbinding kunt maken, raadpleegt u [MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen](./connect-workbench.md)

3. Bewerk de volgende SQL-code en voer deze uit. Vervang de nieuwe gebruikers naam voor de waarde van de tijdelijke aanduiding `new_master_user` . Met deze syntaxis worden de vermelde bevoegdheden voor alle database schema's (*.*) verleend aan de gebruikers naam (new_master_user in dit voor beeld).

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

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de computers van de nieuwe gebruikers zodat ze verbinding kunnen maken:
- [Firewall regels maken en beheren op één server](howto-manage-firewall-using-portal.md) 
- [ Firewall regels maken en beheren op een flexibele server](flexible-server/how-to-connect-tls-ssl.md)

Zie voor meer informatie over beheer van gebruikers accounts MySQL-product documentatie voor [Gebruikersaccountbeheer](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [syntaxis van Grant](https://dev.mysql.com/doc/refman/5.7/en/grant.html)en [bevoegdheden](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
