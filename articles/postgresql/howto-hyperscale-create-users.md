---
title: Gebruikers maken-grootschalige (Citus)-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u nieuwe gebruikers accounts kunt maken om te communiceren met een Azure Database for PostgreSQL-grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484924"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Gebruikers maken in Azure Database for PostgreSQL-grootschalige (Citus)

> [!NOTE]
> De term ' gebruikers ' verwijst naar gebruikers in een grootschalige (Citus)-Server groep. Voor meer informatie over Azure-abonnements gebruikers en hun bevoegdheden gaat u naar het [artikel op basis van Azure Role Access Control (RBAC)](../role-based-access-control/built-in-roles.md) of raadpleegt u [How to Customize roles](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Het beheerdersaccount voor de server

De PostgreSQL-engine gebruikt [rollen](https://www.postgresql.org/docs/current/sql-createrole.html) om de toegang tot database objecten te beheren en een nieuw gemaakte grootschalige (Citus)-Server groep wordt geleverd met vooraf gedefinieerde rollen:

* De [standaard postgresql-rollen](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Omdat grootschalige een beheerde PaaS-service is, kan alleen micro soft zich aanmelden met de rol van `postgres` super gebruiker. Voor beperkte beheerders toegang biedt grootschalige de `citus` rol.

Machtigingen voor de `citus` rol:

* Lees alle configuratie variabelen, zelfs variabelen die normaal gesp roken alleen zichtbaar zijn voor supergebruikers.
* Lees alle pagina's\_stat\_\* weer gaven en gebruik verschillende statistieken-gerelateerde uitbrei dingen--zelfs weer gaven of extensies die normaal gesp roken alleen zichtbaar zijn voor supergebruikers.
* Voer bewakings functies uit die toegangs vergrendelingen kunnen hebben voor tabellen, mogelijk gedurende een lange periode.
* [Maak postgresql-extensies](concepts-hyperscale-extensions.md) (omdat de rol lid is van `azure_pg_admin`).

Met name de `citus` rol heeft enkele beperkingen:

* Kan geen rollen maken
* Kan geen data bases maken

## <a name="how-to-create-additional-user-roles"></a>Aanvullende gebruikers rollen maken

Zoals vermeld, heeft het `citus` beheerders account geen toestemming om extra gebruikers te maken. Gebruik de Azure Portal-interface om een gebruiker toe te voegen.

1. Ga naar de pagina **rollen** voor uw grootschalige-Server groep en klik op **+ toevoegen**:

   ![De pagina rollen](media/howto-hyperscale-create-users/1-role-page.png)

2. Voer de rolnaam en het wacht woord in. Klik op **Opslaan**.

   ![Rol toevoegen](media/howto-hyperscale-create-users/2-add-user-fields.png)

De gebruiker wordt gemaakt op het coördinator knooppunt van de Server groep en door gegeven aan alle worker-knoop punten. Rollen die zijn gemaakt via de Azure Portal hebben het kenmerk `LOGIN`. Dit betekent dat ze echte gebruikers zijn die zich kunnen aanmelden bij de data base.

## <a name="how-to-modify-privileges-for-user-role"></a>Bevoegdheden voor een gebruikersrol wijzigen

Nieuwe gebruikers rollen worden meestal gebruikt om toegang tot data bases te bieden met beperkte bevoegdheden. Als u gebruikers bevoegdheden wilt wijzigen, gebruikt u de standaard PostgreSQL-opdrachten met een hulp programma zoals PgAdmin of psql. (Zie [verbinding maken met psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) in de grootschalige (Citus) Quick Start.)

Als u bijvoorbeeld wilt toestaan dat `db_user` `mytable`lezen, moet u de machtiging verlenen:

```sql
GRANT SELECT ON mytable TO db_user;
```

Met grootschalige (Citus) worden GRANT-instructies met één tabel via het hele cluster door gegeven en toegepast op alle worker-knoop punten. Subsidies die voor het hele systeem zijn (bijvoorbeeld voor alle tabellen in een schema), moeten op elk datum knooppunt worden uitgevoerd.  Gebruik de `run_command_on_workers()` helper-functie:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Een gebruikersrol verwijderen of het wacht woord wijzigen

Als u een gebruiker wilt bijwerken, gaat u naar de pagina **rollen** voor uw grootschalige-Server groep en klikt u op de weglatings tekens **...** naast de gebruiker. De weglatings tekens openen een menu om de gebruiker te verwijderen of hun wacht woord opnieuw in te stellen.

   ![Een rol bewerken](media/howto-hyperscale-create-users/edit-role.png)

De rol `citus` is bevoegd en kan niet worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de computers van de nieuwe gebruikers zodat ze verbinding kunnen maken: [grootschalige (Citus) firewall regels maken en beheren met behulp van de Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Zie PostgreSQL-product documentatie voor meer informatie over het beheer van database gebruikers accounts:

* [Database rollen en-bevoegdheden](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Syntaxis van GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Bevoegdheden](https://www.postgresql.org/docs/current/static/ddl-priv.html)
