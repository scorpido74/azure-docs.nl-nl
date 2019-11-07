---
title: Azure SQL Database beveiliging configureren voor nood herstel
description: Meer informatie over de beveiligings overwegingen voor het configureren en beheren van beveiliging na het herstellen van een Data Base of een failover naar een secundaire server.
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
ms.openlocfilehash: 3c08ba1a37d7b0d16042d6496c27e0de8d070b75
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689967"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Azure SQL Database beveiliging configureren en beheren voor geo-herstel of failover

In dit artikel worden de verificatie vereisten beschreven voor het configureren en beheren van [actieve geo-replicatie](sql-database-active-geo-replication.md) en [groepen met automatische failover](sql-database-auto-failover-group.md). Het bevat ook de vereiste stappen voor het instellen van gebruikers toegang tot de secundaire data base. Ten slotte wordt ook beschreven hoe u toegang tot de herstelde data base inschakelt nadat u [geo-herstel](sql-database-recovery-using-backups.md#geo-restore)hebt gebruikt. Zie [overzicht van bedrijfs continuïteit](sql-database-business-continuity.md)voor meer informatie over herstel opties.

## <a name="disaster-recovery-with-contained-users"></a>Herstel na nood geval met Inge sloten gebruikers

In tegens telling tot traditionele gebruikers, die moeten worden toegewezen aan aanmeldingen in de hoofd database, wordt een Inge sloten gebruiker volledig beheerd door de data base zelf. Dit heeft twee voor delen. In het scenario voor herstel na nood gevallen kunnen de gebruikers blijven verbinding maken met de nieuwe primaire data base of de data base die is hersteld met behulp van geo-Restore zonder aanvullende configuratie, omdat de gebruikers door de Data Base worden beheerd. Er zijn ook mogelijke mogelijkheden voor schaal baarheid en prestaties van deze configuratie vanuit een aanmeldings perspectief. Zie [Ingesloten databasegebruikers: een draagbare database maken](https://msdn.microsoft.com/library/ff929188.aspx) voor meer informatie.

De belangrijkste afweging is dat het beheer van herstel na nood gevallen een lastigere uitbreider is. Wanneer u meerdere data bases hebt die dezelfde aanmelding gebruiken, kan het onderhouden van de referenties met Inge sloten gebruikers in meerdere data bases leiden tot de voor delen van opgenomen gebruikers. Het beleid voor het roteren van wacht woorden vereist bijvoorbeeld dat wijzigingen consistent worden aangebracht in meerdere data bases in plaats van het wacht woord voor de aanmelding eenmaal in de hoofd database te wijzigen. Als u meerdere data bases hebt die dezelfde gebruikers naam en hetzelfde wacht woord gebruiken, wordt het gebruik van Inge sloten gebruikers niet aanbevolen.

## <a name="how-to-configure-logins-and-users"></a>Aanmeldingen en gebruikers configureren

Als u aanmeldingen en gebruikers (in plaats van opgenomen gebruikers) gebruikt, moet u extra stappen uitvoeren om ervoor te zorgen dat dezelfde aanmeldingen aanwezig zijn in de data base Master. In de volgende secties vindt u een overzicht van de stappen en aanvullende overwegingen.

  >[!NOTE]
  > Het is ook mogelijk om de aanmeldingen van Azure Active Directory (AAD) te gebruiken voor het beheren van uw data bases. Zie [Azure SQL-aanmeldingen en-gebruikers](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)voor meer informatie.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Gebruikers toegang instellen voor een secundaire of herstelde data base

De secundaire data base kan alleen worden gebruikt als een secundaire data base met het kenmerk alleen-lezen, en om ervoor te zorgen dat de data base van de doel server met behulp van geo-Restore goed toegankelijk is voor de nieuwe primaire data base. de configuratie is aanwezig vóór het herstel.

De specifieke machtigingen voor elke stap worden verderop in dit onderwerp beschreven.

Het voorbereiden van de gebruikers toegang tot een secundaire geo-replicatie moet worden uitgevoerd als onderdeel van het configureren van geo-replicatie. Het voorbereiden van de gebruikers toegang tot de geo-teruggezette data bases moet op elk gewenst moment worden uitgevoerd wanneer de oorspronkelijke server online is (bijvoorbeeld als onderdeel van de DR-analyse).

> [!NOTE]
> Als u een failover wilt uitvoeren of geo-herstellen naar een server die niet de juiste geconfigureerde aanmeldingen heeft, wordt de toegang tot deze beperkt tot het account van de server beheerder.

Het instellen van aanmeldingen op de doel server omvat drie stappen die hieronder worden beschreven:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. aanmeldingen met toegang tot de primaire data base bepalen

De eerste stap van het proces is om te bepalen welke aanmeldingen moeten worden gedupliceerd op de doel server. Dit wordt bereikt met een paar SELECT-instructies, een in de logische hoofd database op de bron server en een van de primaire data base zelf.

Alleen de server beheerder of een lid van de **rol Login Manager** -serverrol kan de aanmeldingen op de bron server bepalen met de volgende SELECT-instructie.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Alleen een lid van de databaserol db_owner, de dbo-gebruiker of de server beheerder kan alle data base-gebruikers principals in de primaire data base bepalen.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Zoek de SID voor de aanmeldingen die in stap 1 zijn geïdentificeerd

Door de uitvoer van de query's uit de vorige sectie te vergelijken en te voldoen aan de Sid's, kunt u de aanmelding van de server toewijzen aan de database gebruiker. Aanmeldingen met een database gebruiker met een overeenkomende SID hebben gebruikers toegang tot die data base als de gebruikers-principal van de data base.

De volgende query kan worden gebruikt om alle gebruikers-principals en hun Sid's in een-Data Base weer te geven. Deze query kan alleen worden uitgevoerd door een lid van de databaserol db_owner of de server beheerder.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> De **INFORMATION_SCHEMA** -en **sys** -gebruikers hebben *Null* -sid's en de **gast** -sid is **0x00**. De **dbo** -sid kan beginnen met *0x01060000000001648000000000048454*, als de maker van de data base de server beheerder is in plaats van een lid van **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. de aanmeldingen maken op de doel server

De laatste stap is om naar de doel server of servers te gaan en de aanmeldingen te genereren met de juiste Sid's. De basis syntaxis is als volgt.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Als u gebruikers toegang wilt verlenen tot de secundaire, maar niet tot de primaire, kunt u dit doen door de gebruikers aanmelding op de primaire server te wijzigen met de volgende syntaxis.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> Als u uitschakelen inschakelt, wordt het wacht woord niet gewijzigd, zodat u het altijd kunt inschakelen als dat nodig is.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over het beheren van database toegang en-aanmeldingen raadpleegt u [SQL database beveiliging: toegang tot data base beheren en beveiliging voor aanmelden](sql-database-manage-logins.md).
* Zie [Inge sloten database gebruikers-uw data base draagbaar maken](https://msdn.microsoft.com/library/ff929188.aspx)voor meer informatie over Inge sloten database gebruikers.
* Zie [actieve geo-replicatie](sql-database-active-geo-replication.md)voor meer informatie over actieve geo-replicatie.
* Zie [groepen met automatische failover](sql-database-auto-failover-group.md)voor meer informatie over groepen met automatische failover.
* Zie [geo-Restore](sql-database-recovery-using-backups.md#geo-restore) voor meer informatie over het gebruik van geo-Restore
