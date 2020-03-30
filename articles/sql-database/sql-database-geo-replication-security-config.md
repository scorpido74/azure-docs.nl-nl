---
title: Beveiliging configureren voor herstel na noodgevallen
description: Meer informatie over de beveiligingsoverwegingen voor het configureren en beheren van beveiliging na het herstellen van een database of een failover naar een secundaire server.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 9d628583168883276e67d9e2f2fcafdce292769e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807500"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Azure SQL Database-beveiliging configureren en beheren voor geo-herstel of failover

In dit artikel worden de verificatievereisten beschreven voor het configureren en beheren van [actieve georeplicatie-](sql-database-active-geo-replication.md) en [autofailovergroepen.](sql-database-auto-failover-group.md) Het biedt ook de stappen die nodig zijn om gebruikerstoegang tot de secundaire database in te stellen. Ten slotte wordt ook beschreven hoe u toegang tot de herstelde database inschakelen na het gebruik van [geo-restore.](sql-database-recovery-using-backups.md#geo-restore) Zie [Overzicht bedrijfscontinuïteit](sql-database-business-continuity.md)voor meer informatie over herstelopties.

## <a name="disaster-recovery-with-contained-users"></a>Herstel na noodgevallen met ingeperkte gebruikers

In tegenstelling tot traditionele gebruikers, die moeten worden toegewezen aan aanmeldingen in de hoofddatabase, wordt een opgenomen gebruiker volledig beheerd door de database zelf. Dit heeft twee voordelen. In het scenario voor noodherstel kunnen de gebruikers verbinding blijven maken met de nieuwe primaire database of de database die is hersteld met behulp van geoherstel zonder extra configuratie, omdat de database de gebruikers beheert. Er zijn ook potentiële schaalbaarheid en prestatievoordelen van deze configuratie vanuit een login perspectief. Zie [Ingesloten databasegebruikers: een draagbare database maken](https://msdn.microsoft.com/library/ff929188.aspx) voor meer informatie.

De belangrijkste afweging is dat het beheren van het disaster recovery proces op schaal uitdagender is. Wanneer u meerdere databases hebt die dezelfde aanmelding gebruiken, kan het onderhouden van de referenties met behulp van opgenomen gebruikers in meerdere databases de voordelen van opgenomen gebruikers tenietdoen. Het wachtwoordrotatiebeleid vereist bijvoorbeeld dat wijzigingen consistent worden aangebracht in meerdere databases in plaats van het wachtwoord voor de aanmelding eenmaal in de hoofddatabase te wijzigen. Als u daarom meerdere databases hebt die dezelfde gebruikersnaam en hetzelfde wachtwoord gebruiken, wordt het gebruik van aanbevolen gebruikers niet aanbevolen.

## <a name="how-to-configure-logins-and-users"></a>Aanmeldingen en gebruikers configureren

Als u aanmeldingen en gebruikers gebruikt (in plaats van gebruikers), moet u extra stappen ondernemen om ervoor te zorgen dat dezelfde aanmeldingen in de hoofddatabase bestaan. In de volgende secties worden de betrokken stappen en aanvullende overwegingen beschreven.

  >[!NOTE]
  > Het is ook mogelijk om AAD-aanmeldingen (Azure Active Directory) te gebruiken om uw databases te beheren. Zie [Azure SQL-aanmeldingen en gebruikers](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)voor meer informatie.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Gebruikerstoegang instellen tot een secundaire of herstelde database

Om ervoor te zorgen dat de secundaire database bruikbaar is als een alleen-lezen secundaire database en om een goede toegang tot de nieuwe primaire database of de database te garanderen die met geo-restore wordt hersteld, moet de hoofddatabase van de doelserver over de juiste beveiliging beschikken configuratie op zijn plaats vóór het herstel.

De specifieke machtigingen voor elke stap worden later in dit onderwerp beschreven.

Het voorbereiden van gebruikerstoegang tot een secundaire georeplicatie moet worden uitgevoerd als onderdeel van het configureren van georeplicatie. Het voorbereiden van gebruikerstoegang tot de geoherstelde databases moet worden uitgevoerd op elk moment dat de oorspronkelijke server online is (bijvoorbeeld als onderdeel van de DR-drill).

> [!NOTE]
> Als u niet overgaat of geo-restore naar een server die niet goed geconfigureerde aanmeldingen heeft, is de toegang tot deze server beperkt tot het serverbeheerdersaccount.

Het instellen van aanmeldingen op de doelserver omvat drie stappen hieronder beschreven:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Aanmeldingen bepalen met toegang tot de primaire database

De eerste stap van het proces is om te bepalen welke aanmeldingen moeten worden gedupliceerd op de doelserver. Dit wordt bereikt met een paar SELECT-instructies, een in de logische hoofddatabase op de bronserver en een in de primaire database zelf.

Alleen de serverbeheerder of een lid van de serverrol **LoginManager** kan de aanmeldingen op de bronserver bepalen met de volgende SELECT-instructie.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Alleen een lid van de db_owner databaserol, de dbo-gebruiker of serverbeheerder, kan alle aangevers van de databasegebruiker in de primaire database bepalen.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Zoek de SID voor de logins geïdentificeerd in stap 1

Door de uitvoer van de query's uit de vorige sectie te vergelijken en de SID's te matchen, u de serverlogin toewijzen aan de databasegebruiker. Aanmeldingen met een databasegebruiker met een overeenkomende SID hebben gebruikerstoegang tot die database als hoofdvan de databasegebruiker.

De volgende query kan worden gebruikt om alle gebruikersprincipals en hun ID's in een database te zien. Alleen een lid van de db_owner databaserol of serverbeheerder kan deze query uitvoeren.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> De **INFORMATION_SCHEMA** en **sys** gebruikers hebben *NULL* SIDs, en de **gast** SID is **0x00**. De **dbo** SID kan beginnen met *0x0106000000001648000000000000048454*, als de database maker was de server admin in plaats van een lid van **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Maak de aanmeldingen op de doelserver

De laatste stap is om naar de doelserver of servers te gaan en de aanmeldingen te genereren met de juiste SID's. De basissyntaxis is als volgt.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Als u gebruikers toegang wilt verlenen tot de secundaire, maar niet tot de primaire, u dat doen door de gebruikersaanmelding op de primaire server te wijzigen met behulp van de volgende syntaxis.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> UITSCHAKELEN verandert het wachtwoord niet, dus u het altijd inschakelen als dat nodig is.

## <a name="next-steps"></a>Volgende stappen

* Zie [SQL Database-beveiliging beheren: Databasetoegang beheren en beveiliging van de login beheren](sql-database-manage-logins.md)voor meer informatie over het beheren van databasetoegang en aanmeldingen.
* Zie [Gegevensrijke databasegebruikers](https://msdn.microsoft.com/library/ff929188.aspx)- Uw database draagbaar maken voor meer informatie over gegevensgebruikers.
* Zie [Actieve georeplicatie](sql-database-active-geo-replication.md)voor meer informatie over actieve georeplicatie .
* Zie [Groepen automatisch failoveren](sql-database-auto-failover-group.md)voor meer informatie over groepen voor automatisch failoveren .
* Zie [geo-restore](sql-database-recovery-using-backups.md#geo-restore) voor informatie over het gebruik van geo-restore
