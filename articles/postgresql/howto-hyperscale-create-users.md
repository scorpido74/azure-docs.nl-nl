---
title: Gebruikers maken in Azure Database for PostgreSQL-grootschalige (Citus)
description: In dit artikel wordt beschreven hoe u nieuwe gebruikers accounts kunt maken om te communiceren met een Azure Database for PostgreSQL-grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515939"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Gebruikers maken in Azure Database for PostgreSQL-grootschalige (Citus)

In dit artikel wordt beschreven hoe u gebruikers kunt maken binnen een grootschalige-Server groep (Citus). Voor meer informatie over Azure-abonnements gebruikers en hun bevoegdheden gaat u naar het [artikel op basis van Azure Role Access Control (RBAC)](../role-based-access-control/built-in-roles.md) of raadpleegt u [How to Customize roles](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Het beheerdersaccount voor de server

Een nieuw gemaakte grootschalige-Server groep (Citus) wordt geleverd met verschillende rollen vooraf gedefinieerd:

* De [standaard postgresql-rollen](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

De gebruiker van de server beheerder, *Citus*, is lid van de *azure_pg_admin* -rol.
Het maakt echter geen deel uit van de rol *post gres* (super gebruiker).  Omdat grootschalige een beheerde PaaS-service is, maakt alleen micro soft deel uit van de rol van super gebruiker.

De PostgreSQL-engine gebruikt bevoegdheden om de toegang tot database objecten te beheren, zoals beschreven in de [product documentatie van postgresql](https://www.postgresql.org/docs/current/static/sql-createrole.html).
De gebruiker van de server beheerder heeft in Azure Database for PostgreSQL de volgende bevoegdheden verleend: aanmelding, gebruiker, overnemen, CREATEDB, CREATEROLE, replicatie

## <a name="how-to-create-additional-users"></a>Aanvullende gebruikers maken

Het *Citus* -beheerders account heeft geen machtiging voor het maken van extra gebruikers. Als u een gebruiker wilt toevoegen, gebruikt u de Azure Portal in plaats daarvan.

1. Ga naar de pagina **rollen** voor uw grootschalige-Server groep en klik op **+ toevoegen**:

   ![De pagina rollen](media/howto-hyperscale-create-users/1-role-page.png)

2. Voer de rolnaam en het wacht woord in. Klik op **Opslaan**.

   ![Rol toevoegen](media/howto-hyperscale-create-users/2-add-user-fields.png)

De gebruiker wordt gemaakt op het coördinator knooppunt van de Server groep en door gegeven aan alle worker-knoop punten.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Een gebruiker verwijderen of het wacht woord wijzigen

Ga naar de pagina **rollen** voor uw grootschalige-Server groep en klik op de weglatings tekens **...** naast een gebruiker. De weglatings tekens openen een menu om de gebruiker te verwijderen of hun wacht woord opnieuw in te stellen.

   ![Een rol bewerken](media/howto-hyperscale-create-users/edit-role.png)

De rol *Citus* is bevoegd en kan niet worden verwijderd.

## <a name="how-to-modify-privileges-for-role"></a>Bevoegdheden voor een rol wijzigen

Nieuwe rollen worden meestal gebruikt om toegang tot data bases te bieden met beperkte bevoegdheden. Als u gebruikers bevoegdheden wilt wijzigen, gebruikt u de standaard PostgreSQL-opdrachten met een hulp programma zoals PgAdmin of psql. (Zie [verbinding maken met psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) in de grootschalige (Citus) Quick Start.)

Als u bijvoorbeeld wilt toestaan dat *db_user* *myTable*lezen, moet u de machtiging verlenen:

```sql
GRANT SELECT ON mytable TO db_user;
```

Met grootschalige (Citus) worden GRANT-instructies met één tabel via het hele cluster door gegeven en toegepast op alle worker-knoop punten. Subsidies die volledig zijn voor het hele systeem (bijvoorbeeld voor alle tabellen in een schema), moeten op elk datum knooppunt worden uitgevoerd.  Gebruik de hulp functie *run_command_on_workers ()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de computers van de nieuwe gebruikers zodat ze verbinding kunnen maken: [grootschalige (Citus) firewall regels maken en beheren met behulp van de Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Zie PostgreSQL-product documentatie voor meer informatie over het beheer van database gebruikers accounts:

* [Database rollen en-bevoegdheden](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Syntaxis van GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Bevoegdheden](https://www.postgresql.org/docs/current/static/ddl-priv.html)
