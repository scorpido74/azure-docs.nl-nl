---
title: Gebruikers maken - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: In dit artikel wordt beschreven hoe u nieuwe gebruikersaccounts maken om te communiceren met een Azure Database voor PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484924"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Gebruikers maken in Azure Database voor PostgreSQL - Hyperscale (Citus)

> [!NOTE]
> De term "gebruikers" verwijst naar gebruikers binnen een Hyperscale (Citus) servergroep. Ga naar het [RBAC-artikel (Azure role based access control)](../role-based-access-control/built-in-roles.md) of bekijk [hoe u rollen aanpassen](../role-based-access-control/custom-roles.md)om in plaats daarvan meer te weten te komen over gebruikers van Azure-abonnementen en hun bevoegdheden.

## <a name="the-server-admin-account"></a>Het beheerdersaccount voor de server

De PostgreSQL-engine gebruikt [rollen](https://www.postgresql.org/docs/current/sql-createrole.html) om de toegang tot databaseobjecten te beheren en een nieuw gemaakte Hyperscale-servergroep (Citus) wordt geleverd met verschillende rollen die vooraf zijn gedefinieerd:

* De [standaardPostgreSQL-rollen](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Aangezien Hyperscale een beheerde PaaS-service is, `postgres` kan alleen Microsoft zich aanmelden met de rol van de supergebruiker. Voor beperkte administratieve toegang biedt `citus` Hyperscale de rol.

Machtigingen voor `citus` de rol:

* Lees alle configuratievariabelen, zelfs variabelen die normaal alleen zichtbaar zijn voor supergebruikers.
* Lees alle\_\_ \* pg stat weergaven en gebruik verschillende statistieken-gerelateerde extensies - zelfs weergaven of extensies normaal alleen zichtbaar voor supergebruikers.
* Voer bewakingsfuncties uit die ACCESS SHARE-vergrendelingen op tabellen kunnen gebruiken, mogelijk voor een lange tijd.
* [Maak PostgreSQL-extensies](concepts-hyperscale-extensions.md) (omdat de `azure_pg_admin`rol lid is van ).

Met name `citus` de rol heeft een aantal beperkingen:

* Kan geen rollen maken
* Kan geen databases maken

## <a name="how-to-create-additional-user-roles"></a>Extra gebruikersrollen maken

Zoals gezegd `citus` heeft het beheerdersaccount geen toestemming om extra gebruikers te maken. Als u een gebruiker wilt toevoegen, gebruikt u de Azure-portalinterface.

1. Ga naar de pagina **Rollen** voor uw hyperscale-servergroep en klik op **+ Toevoegen:**

   ![De rollenspagina](media/howto-hyperscale-create-users/1-role-page.png)

2. Voer de naam en het wachtwoord van de rol in. Klik op **Opslaan**.

   ![Rol toevoegen](media/howto-hyperscale-create-users/2-add-user-fields.png)

De gebruiker wordt gemaakt op het coördinatorknooppunt van de servergroep en wordt gepropageerd naar alle werknemersknooppunten. Rollen die via de `LOGIN` Azure-portal zijn gemaakt, hebben het kenmerk, wat betekent dat het echte gebruikers zijn die zich kunnen aanmelden bij de database.

## <a name="how-to-modify-privileges-for-user-role"></a>Bevoegdheden voor gebruikersrol wijzigen

Nieuwe gebruikersrollen worden vaak gebruikt om databasetoegang te bieden met beperkte bevoegdheden. Als u gebruikersrechten wilt wijzigen, gebruikt u standaard PostgreSQL-opdrachten met behulp van een tool zoals PgAdmin of psql. (Zie [verbinding maken met psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) in de Quickstart Hyperscale (Citus).

Bijvoorbeeld, om `db_user` toe `mytable`te staan om te lezen , verlenen van de toestemming:

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperscale (Citus) propageert subsidie-instructies met één tabel door het hele cluster en past deze toe op alle werknemersknooppunten. Grants die systeembreed zijn (bijvoorbeeld voor alle tabellen in een schema) moeten echter op elk datumknooppunt worden uitgevoerd.  Gebruik `run_command_on_workers()` de helperfunctie:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Een gebruikersrol verwijderen of hun wachtwoord wijzigen

Als u een gebruiker wilt bijwerken, gaat u naar de pagina **Rollen** voor uw hyperscale-servergroep en klikt u op de ellipsen **...** naast de gebruiker. De ellipsen openen een menu om de gebruiker te verwijderen of hun wachtwoord opnieuw in te stellen.

   ![Een rol bewerken](media/howto-hyperscale-create-users/edit-role.png)

De `citus` rol is geprivilegieerd en kan niet worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Open de firewall voor de IP-adressen van de machines van de nieuwe gebruikers om verbinding te maken: [Hyperscale -firewallregels maken en beheren met behulp van de Azure-portal.](howto-hyperscale-manage-firewall-using-portal.md)

Zie PostgreSQL-productdocumentatie voor meer informatie over accountbeheer van databasegebruikers:

* [Databaserollen en -bevoegdheden](https://www.postgresql.org/docs/current/static/user-manag.html)
* [AS GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Bevoegdheden](https://www.postgresql.org/docs/current/static/ddl-priv.html)
