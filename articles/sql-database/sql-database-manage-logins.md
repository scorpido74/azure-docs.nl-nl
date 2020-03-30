---
title: Server- en databasetoegang autoriseren met aanmeldings- en gebruikersaccounts
description: Meer informatie over hoe Azure SQL Database en Azure Synapse Analytics gebruikers verifiëren voor toegang met behulp van aanmeldingen en gebruikersaccounts. Lees ook hoe u rollen in de database en expliciete machtigingen voor het autoriseren van aanmeldingen en gebruikers om acties en querygegevens uit te voeren.
keywords: sql-databasebeveiliging,beheer databasebeveiliging,aanmeldingsbeveiliging,databasebeveiliging,databasetoegang
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124812"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Databasetoegang tot geverifieerde gebruikers toestaan tot SQL Database en Azure Synapse Analytics met aanmeldings- en gebruikersaccounts

In dit artikel leert u over:

- Opties voor het configureren van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) om gebruikers in staat te stellen administratieve taken uit te voeren en toegang te krijgen tot de gegevens die in deze databases zijn opgeslagen.
- De configuratie van toegang en autorisatie na het in eerste instantie maken van een nieuwe Azure SQL-database
- Aanmeldingen en gebruikersaccounts toevoegen aan de hoofddatabase en gebruikersaccounts en deze beheerdersmachtigingen voor accounts verlenen
- Gebruikersaccounts toevoegen aan gebruikersdatabases, gekoppeld aan aanmeldingen of als opgenomen gebruikersaccounts
- Gebruikersaccounts configureren met machtigingen in gebruikersdatabases met behulp van databaserollen en expliciete machtigingen

> [!IMPORTANT]
> Databases in Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) worden in de rest van dit artikel gezamenlijk aangeduid als databases of azure SQL (voor eenvoud).

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

[**Authenticatie**](sql-database-security-overview.md#authentication) is het proces om te bewijzen dat de gebruiker is wie hij beweert te zijn. Een gebruiker maakt verbinding met een database met een gebruikersaccount.
Wanneer een gebruiker probeert verbinding te maken met een database, worden er gebruikersaccount- en verificatiegegevens verstrekt. De gebruiker wordt geverifieerd met een van de volgende twee verificatiemethoden:

- [SQL-verificatie](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Met deze verificatiemethode verzendt de gebruiker een gebruikersnaam en bijbehorend wachtwoord om een verbinding tot stand te brengen. Dit wachtwoord wordt opgeslagen in de hoofddatabase voor gebruikersaccounts die zijn gekoppeld aan een aanmelding of zijn opgeslagen in de database met het gebruikersaccount voor gebruikersaccounts die niet aan een aanmelding zijn gekoppeld.
- [Azure Active Directory-verificatie](sql-database-aad-authentication.md)

  Met deze verificatiemethode dient de gebruiker een gebruikersnaam in en verzoekt de service de referentiegegevens te gebruiken die zijn opgeslagen in Azure Active Directory.

**Aanmeldingen en gebruikers**: In Azure SQL kan een gebruikersaccount in een database worden gekoppeld aan een aanmelding die is opgeslagen in de hoofddatabase of een gebruikersnaam kan zijn die is opgeslagen in een afzonderlijke database.

- Een **login** is een individueel account in de hoofddatabase, waaraan een gebruikersaccount in een of meer databases kan worden gekoppeld. Bij een login worden de inloggegevens voor het gebruikersaccount opgeslagen bij de login.
- Een **gebruikersaccount** is een individueel account in een database die mogelijk wel gekoppeld hoeft te worden aan een login. Bij een gebruikersaccount dat niet is gekoppeld aan een aanmelding, worden de referentiegegevens opgeslagen bij het gebruikersaccount.

[**Autorisatie**](sql-database-security-overview.md#authorization) voor toegang tot gegevens en het uitvoeren van verschillende acties worden beheerd met behulp van databaserollen en expliciete machtigingen. Autorisatie verwijst naar de machtigingen die aan een gebruiker zijn toegewezen en bepaalt wat die gebruiker mag doen. Autorisatie wordt beheerd door de [databaserollidmaatschappen](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) van uw gebruikersaccount en [machtigingen op objectniveau.](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Bestaande aanmeldingen en gebruikersaccounts na het maken van een nieuwe database

Wanneer u uw eerste Azure SQL-implementatie maakt, geeft u een aanmelding voor beheerders en een bijbehorend wachtwoord op voor die aanmelding. Dit beheerdersaccount heet **Serverbeheerder**. De volgende configuratie van aanmeldingen en gebruikers in de hoofd- en gebruikersdatabases vindt plaats tijdens de implementatie:

- Er wordt een SQL-login met beheerdersrechten gemaakt met behulp van de opgegeven inlognaam. Een [login](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) is een individuele gebruikersaccount voor het inloggen op SQL Database.
- Deze aanmelding krijgt volledige administratieve machtigingen voor alle databases als [hoofdopvoor serverniveau.](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) Deze aanmelding heeft alle beschikbare machtigingen binnen SQL Database en kan niet worden beperkt. In een beheerde instantie wordt deze aanmelding toegevoegd aan de [vaste serverrol van sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (deze rol bestaat niet met afzonderlijke of samengevoegde databases).
- Voor deze `dbo` aanmelding in elke gebruikersdatabase wordt een [gebruikersaccount](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) aangemaakt. De [dbo-gebruiker](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) heeft alle databasemachtigingen in de `db_owner` database en is toegewezen aan de vaste databaserol. Aanvullende vaste databaserollen worden later in dit artikel besproken.

Als u de beheerdersaccounts voor een database wilt identificeren, opent u de Azure-portal en navigeert u naar het tabblad **Eigenschappen** van uw server of beheerde instantie.

![SQL Server-beheerders](media/sql-database-manage-logins/sql-admins.png)

![SQL Server-beheerders](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> De naam van de aanmelding van de beheerder kan niet worden gewijzigd nadat deze is gemaakt. Als u het wachtwoord voor de logische serverbeheerder opnieuw wilt instellen, gaat u naar de [Azure-portal,](https://portal.azure.com)klikt u op **SQL-servers,** selecteert u de server in de lijst en klikt u op **Wachtwoord opnieuw instellen**. Als u het wachtwoord voor een beheerde instantieserver opnieuw wilt instellen, gaat u naar de Azure-portal, klikt u op de instantie en klikt u op **Wachtwoord opnieuw instellen**. U ook PowerShell of de Azure CLI gebruiken.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Extra aanmeldingen maken en gebruikers met beheerdersmachtigingen

Op dit moment is uw Azure SQL-exemplaar alleen geconfigureerd voor toegang met één SQL-aanmeldings- en gebruikersaccount. Als u extra aanmeldingen wilt maken met volledige of gedeeltelijke beheerdersmachtigingen, hebt u de volgende opties (afhankelijk van de implementatiemodus):

- **Een Azure Active Directory-beheerdersaccount maken met volledige beheerdersmachtigingen**

  Enable Azure Active Directory authentication and create an Azure AD administrator login. Eén Azure Active Directory-account kan worden geconfigureerd als beheerder van de SQL-database-implementatie met volledige beheerdersmachtigingen. Dit account kan een individueel account of een beveiligingsgroep account zijn. Een Azure AD-beheerder **moet** zijn geconfigureerd als u Azure AD-accounts wilt gebruiken om verbinding te maken met SQL Database. Zie de volgende artikelen voor gedetailleerde informatie over het inschakelen van Azure AD-verificatie voor alle SQL Database-implementatietypen:

  - [Azure Active Directory-verificatie gebruiken voor verificatie met SQL](sql-database-aad-authentication.md)
  - [Verificatie van Azure Active Directory configureren en beheren met SQL](sql-database-aad-authentication-configure.md)

- **Maak SQL-aanmeldingen met volledige beheerdersmachtigingen in een beheerde instantie**

  - Een extra SQL Server-aanmelding maken in de beheerde instantie
  - Voeg de aanmelding toe aan de [vaste serverrol van de sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) met de [functie-instructie ALTER SERVER.](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) Deze aanmelding heeft volledige administratieve machtigingen.
  - U ook een [Azure AD-aanmelding maken](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) met de <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">syntaxis AANMELDING MAKEN.</a>

- **Maak SQL-aanmeldingen met beperkte administratieve machtigingen in één of samengevoegde implementatie**

  - Een extra SQL-aanmelding maken in de hoofddatabase voor een enkele of gepoolde database-implementatie of een beheerde instantie-implementatie
  - Een gebruikersaccount maken in de hoofddatabase die is gekoppeld aan deze nieuwe aanmelding
  - Voeg het gebruikersaccount `dbmanager`toe `loginmanager` aan de rol `master` of beide in de database met de [functie-instructie ALTER SERVER](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (gebruik voor Azure Synapse Analytics de [sp_addrolemember-instructie).](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

  > [!NOTE]
  > `dbmanager`en `loginmanager` rollen hebben **geen** betrekking op beheerde instantieimplementaties.

  Leden van deze [speciale hoofddatabaserollen](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) voor afzonderlijke of gepoolde databases stellen de gebruikers in staat om databases te maken en te beheren of aanmeldingen te maken en te beheren. In databases die zijn gemaakt door `dbmanager` een gebruiker die lid is `db_owner` van de rol, wordt het lid `dbo` toegewezen aan de vaste databaserol en kan het inloggen en beheren van die database met behulp van het gebruikersaccount. Deze rollen hebben geen expliciete machtigingen buiten de hoofddatabase.

  > [!IMPORTANT]
  > U geen extra SQL-login maken met volledige beheerdersmachtigingen in één of samengevoegde database.

## <a name="create-accounts-for-non-administrator-users"></a>Accounts maken voor niet-beheerdersgebruikers

U accounts maken voor niet-administratieve gebruikers met behulp van een van de twee methoden:

- **Een aanmelding maken**

  Maak een SQL-login in de hoofddatabase. Maak vervolgens een gebruikersaccount aan in elke database waartoe die gebruiker toegang nodig heeft en koppel het gebruikersaccount aan die aanmelding. Deze aanpak heeft de voorkeur wanneer de gebruiker toegang moet krijgen tot meerdere databases en u de wachtwoorden gesynchroniseerd wilt houden. Deze benadering heeft echter complexiteit bij geo-replicatie, omdat de login moet worden gemaakt op zowel de primaire server als de secundaire server(s). Zie [Azure SQL Database-beveiliging configureren en beheren voor geoherstel of failover](sql-database-geo-replication-security-config.md)voor meer informatie.
- **Een gebruikersaccount maken**

  Maak een gebruikersaccount in de database waartoe een gebruiker toegang nodig heeft (ook wel een [opgenomen gebruiker](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)genoemd).

  - Met één of samengevoegde database u dit type gebruikersaccount altijd maken.
  - Met een beheerde instantiedatabase die geen [Azure AD-serverprincipals](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)ondersteunt, u dit type gebruikersaccount alleen maken in een [opgenomen database.](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) Als beheerde instantie [Azure AD-serverprincipals](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)ondersteunt, u gebruikersaccounts maken om te verifiëren aan de beheerde instantie zonder dat databasegebruikers moeten worden gemaakt als een opgenomen databasegebruiker.

  Met deze aanpak wordt de gebruikersverificatiegegevens in elke database opgeslagen en automatisch gerepliceerd naar geo-gerepliceerde databases. Als hetzelfde account echter in meerdere databases bestaat en u SQL-verificatie gebruikt, moet u de wachtwoorden handmatig gesynchroniseerd houden. Bovendien, als een gebruiker een account heeft in verschillende databases met verschillende wachtwoorden, kan het onthouden van die wachtwoorden een probleem worden.

> [!IMPORTANT]
> Als u opgenomen gebruikers wilt maken die zijn toegewezen aan Azure AD-identiteiten, moet u zijn aangemeld met een Azure AD-account dat een beheerder is in de SQL-database. In beheerde instantie kan `sysadmin` een SQL-login met machtigingen ook een Azure AD-aanmelding of -gebruiker maken.

Zie voor voorbeelden die laten zien hoe u aanmeldingen en gebruikers maakt:

- [Aanmelding maken voor afzonderlijke of samengevoegde databases](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Login maken voor beheerde instantiedatabase](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Login maken voor Azure Synapse Analytics-database](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Gebruiker maken](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD-gebruikers maken](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Zie [Zelfstudie: Een enkele of samengevoegde database](sql-database-security-tutorial.md)beveiligen voor een beveiligingszelfstudie met sql server maken van een opgenomen gebruikers in één of samengevoegde database.

## <a name="using-fixed-and-custom-database-roles"></a>Vaste en aangepaste databaserollen gebruiken

Nadat u een gebruikersaccount in een database hebt gemaakt, hetzij op basis van een aanmelding, hetzij als een opgenomen gebruiker, u die gebruiker machtigen om verschillende acties uit te voeren en toegang te krijgen tot gegevens in een bepaalde database. U de volgende methoden gebruiken om toegang te autoriseren:

- **Vaste databaserollen**

  Voeg het gebruikersaccount toe aan een [vaste databaserol.](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) Er zijn 9 vaste databaserollen, elk met een gedefinieerde set machtigingen. De meest voorkomende vaste databaserollen zijn: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**en **db_denydatareader**. **db_owner** wordt doorgaans gebruikt voor het verlenen van volledige machtigingen aan slechts enkele gebruikers. De andere vaste databaserollen zijn handig voor het snel verkrijgen van een eenvoudige database voor ontwikkeldoeleinden, maar worden niet aanbevolen voor de meeste productiedatabases. De **db_datareader** vaste databaserol geeft bijvoorbeeld leestoegang tot elke tabel in de database, wat meer is dan strikt noodzakelijk is.

  - Ga als lid van de gebruiker over naar een vaste databaserol:

    - Gebruik in Azure SQL Database de instructie [ALTER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) Zie VOORBEELDEN [VAN ALTER ROLE-voorbeelden](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples) voor voorbeelden
    - Azure Synapse Analytics, gebruik de [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) verklaring. Zie voorbeelden [sp_addrolemember voorbeelden](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Aangepaste databaserol**

  Maak een aangepaste databaserol met de instructie [ROL MAKEN.](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) Met een aangepaste rol u uw eigen door de gebruiker gedefinieerde databaserollen maken en elke rol zorgvuldig de minste machtigingen verlenen die nodig zijn voor de bedrijfsbehoefte. U vervolgens gebruikers toevoegen aan de aangepaste rol. Als een gebruiker lid is van meerdere rollen, worden de machtigingen van alle rollen samengevoegd.
- **Machtigingen rechtstreeks verlenen**

  Geef de gebruikersaccount [rechtstreeks machtigingen.](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) Er zijn meer dan 100 machtigingen die afzonderlijk kunnen worden verleend of geweigerd in SQL Database. Veel van deze machtigingen zijn genest. De machtiging `UPDATE` voor een schema bevat bijvoorbeeld de machtiging `UPDATE` voor elke tabel binnen dat schema. Net als bij de meeste machtigingssystemen gaat de weigering van een machtiging vóór toestemming. Vanwege de geneste aard en het aantal machtigingen kan een nauwkeurig onderzoek nodig zijn om een geschikt machtigingssysteem te ontwerpen voor een goede bescherming van uw database. Start met de lijst van machtigingen in [Machtigingen (Database-engine)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) en controleer de [afbeelding op postergrootte](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) van de machtigingen.

## <a name="using-groups"></a>Groepen gebruiken

Efficiënt toegangsbeheer maakt gebruik van machtigingen die zijn toegewezen aan Active Directory-beveiligingsgroepen en vaste of aangepaste rollen in plaats van aan individuele gebruikers.

- Wanneer u Azure Active Directory-verificatie gebruikt, plaatst u Azure Active Directory-gebruikers in een Azure Active Directory-beveiligingsgroep. Maak voor de groep een ingesloten databasegebruiker. Plaats een of meer databasegebruikers in een aangepaste databaserol met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- Maak bij het gebruik van SQL-verificatie opgenomen databasegebruikers in de database. Plaats een of meer databasegebruikers in een aangepaste databaserol met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

  > [!NOTE]
  > U ook groepen gebruiken voor niet-opgenomen databasegebruikers.

Zorg ervoor dat u de volgende functies kunt gebruiken voor het beperken of het verhogen van machtigingen:

- [Imitatie](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) en [module-ondertekening](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) kunnen worden gebruikt om machtigingen tijdelijk veilig te verhogen.
- [Beveiliging op rijniveau](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan worden gebruikt om de rijen waartoe een gebruiker toegang heeft te beperken.
- [Gegevensmaskering](sql-database-dynamic-data-masking-get-started.md) kan worden gebruikt om de weergave van gevoelige gegevens te beperken.
- [Opgeslagen procedures](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) kunnen worden gebruikt om de acties die op de database kunnen worden uitgevoerd te beperken.

## <a name="next-steps"></a>Volgende stappen

Zie [SQL security overview](sql-database-security-overview.md) (SQL-beveiligingsoverzicht) voor een overzicht van alle beveiligingsfuncties van SQL Database.
