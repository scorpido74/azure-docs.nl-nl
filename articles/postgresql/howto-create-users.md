---
title: Gebruikers maken - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u nieuwe gebruikersaccounts maken om te communiceren met een Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 127d484d6cfc35368803069f9c3d602e787baa56
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384344"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Gebruikers maken in Azure Database voor PostgreSQL - Single Server

In dit artikel wordt beschreven hoe u gebruikers maken binnen een Azure Database voor PostgreSQL-server.

Als u meer wilt weten over het maken en beheren van Azure-abonnementsgebruikers en hun bevoegdheden, u het [RBAC-artikel (Azure role based access control)](../role-based-access-control/built-in-roles.md) bezoeken of bekijken [hoe u rollen aanpassen.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>Het beheerdersaccount voor de server

Toen u uw Azure Database voor PostgreSQL voor het eerst maakte, hebt u een gebruikersnaam en wachtwoord voor serverbeheerder opgegeven. Voor meer informatie u de [Quickstart](quickstart-create-server-database-portal.md) volgen om de stapsgewijze aanpak te zien. Aangezien de gebruikersnaam van de serverbeheerder een aangepaste naam is, u de gekozen gebruikersnaam van de serverbeheerder zoeken vanuit de Azure-portal.

De Azure Database voor PostgreSQL-server wordt gemaakt met de 3 standaardrollen gedefinieerd. U deze rollen bekijken door de opdracht uit te voeren:`SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- uw serverbeheerder

Uw serverbeheerder is lid van de azure_pg_admin rol. Het serverbeheerdersaccount maakt echter geen deel uit van de azure_superuser rol. Aangezien deze service een beheerde PaaS-service is, maakt alleen Microsoft deel uit van de rol van de supergebruiker.

De PostgreSQL-engine gebruikt bevoegdheden om de toegang tot databaseobjecten te beheren, zoals besproken in de [PostgreSQL-productdocumentatie.](https://www.postgresql.org/docs/current/static/sql-createrole.html) In Azure Database voor PostgreSQL krijgt de serverbeheerder deze bevoegdheden: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATERole, NOREPLICATION

Het gebruikersaccount van de serverbeheerder kan worden gebruikt om extra gebruikers te maken en deze gebruikers in de azure_pg_admin rol toe te kennen. Het serverbeheeraccount kan ook worden gebruikt om minder bevoorrechte gebruikers en rollen te maken die toegang hebben tot afzonderlijke databases en schema's.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Extra beheerdersgebruikers maken in Azure Database voor PostgreSQL

1. Download de verbindingsgegevens en de gebruikersnaam van de beheerder.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U de servernaam en aanmeldingsgegevens eenvoudig vinden op de pagina **serveroverzicht** of de pagina **Eigenschappen** in de Azure-portal.

2. Gebruik het beheerdersaccount en het wachtwoord om verbinding te maken met uw databaseserver. Gebruik de voorkeursclienttool, zoals pgAdmin of psql.
   Als u niet zeker weet hoe u verbinding maken, raadpleegt u [de snelle start](./quickstart-create-server-database-portal.md)

3. Bewerk en voer de volgende SQL-code uit. Vervang uw nieuwe gebruikersnaam voor de tijdelijke waarde <new_user> en vervang het tijdelijke wachtwoord door uw eigen sterke wachtwoord. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Databasegebruikers maken in Azure Database voor PostgreSQL

1. Download de verbindingsgegevens en de gebruikersnaam van de beheerder.
   Voor verbinding met uw databaseserver moet u beschikken over de volledige servernaam en aanmeldingsreferenties van de beheerder. U de servernaam en aanmeldingsgegevens eenvoudig vinden op de pagina **serveroverzicht** of de pagina **Eigenschappen** in de Azure-portal.

2. Gebruik het beheerdersaccount en het wachtwoord om verbinding te maken met uw databaseserver. Gebruik de voorkeursclienttool, zoals pgAdmin of psql.

3. Bewerk en voer de volgende SQL-code uit. Vervang de tijdelijke `<db_user>` aanduidingswaarde door uw beoogde nieuwe `<newdb>` gebruikersnaam en tijdelijke waarde door uw eigen databasenaam. Vervang het tijdelijke wachtwoord door uw eigen sterke wachtwoord.

   Met deze syntaxis van sql-code wordt bijvoorbeeld een nieuwe database met de naam testdb gemaakt. Vervolgens wordt een nieuwe gebruiker in de PostgreSQL-service gemaakt en worden verbindingsrechten toegekend aan de nieuwe database voor die gebruiker.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Met behulp van een beheerdersaccount moet u mogelijk extra bevoegdheden verlenen om de objecten in de database te beveiligen. Raadpleeg de [PostgreSQL-documentatie](https://www.postgresql.org/docs/current/static/ddl-priv.html) voor meer informatie over databaserollen en -bevoegdheden. Bijvoorbeeld:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Meld u aan bij uw server en geef de aangewezen database op met de nieuwe gebruikersnaam en het nieuwe wachtwoord. In dit voorbeeld wordt de psql-opdrachtregel weergegeven. Met deze opdracht wordt u gevraagd om het wachtwoord voor de gebruikersnaam. Vervang uw eigen servernaam, databasenaam en gebruikersnaam.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de machines van de nieuwe gebruikers om verbinding te maken: [Azure Database maken en beheren voor PostgreSQL-firewallregels met behulp van de Azure-portal](howto-manage-firewall-using-portal.md) of Azure [CLI](howto-manage-firewall-using-cli.md).

Zie PostgreSQL-productdocumentatie voor [Databaserollen en -bevoegdheden](https://www.postgresql.org/docs/current/static/user-manag.html), [SYNTAXIS](https://www.postgresql.org/docs/current/static/sql-grant.html)en [Bevoegdheden](https://www.postgresql.org/docs/current/static/ddl-priv.html)voor meer informatie over gebruikersaccountbeheer .
