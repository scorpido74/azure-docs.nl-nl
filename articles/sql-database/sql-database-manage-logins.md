---
title: Server-en database toegang autoriseren met behulp van aanmeldingen en gebruikers accounts
description: Meer informatie over hoe Azure SQL Database en Azure Synapse Analytics gebruikers voor toegang verifieert met behulp van aanmeldingen en gebruikers accounts. Meer informatie over de database rollen en expliciete machtigingen om aanmeldingen en gebruikers te autoriseren om acties uit te voeren en gegevens op te vragen.
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
ms.openlocfilehash: 0f1611e6d3524cc78fc20fed9d1aac6f3fd453fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106437"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Toegang tot data bases verlenen aan geverifieerde gebruikers SQL Database en Azure Synapse Analytics met behulp van aanmeldingen en gebruikers accounts

In dit artikel vindt u meer informatie over:

- Opties voor het configureren van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) om gebruikers in staat te stellen beheer taken uit te voeren en toegang te krijgen tot de gegevens die zijn opgeslagen in deze data bases.
- De configuratie voor toegang en autorisatie na het maken van een nieuwe Azure SQL Database
- Aanmeldingen en gebruikers accounts toevoegen aan de hoofd database en gebruikers accounts en vervolgens deze accounts beheer machtigingen verlenen
- Gebruikers accounts toevoegen in gebruikers databases, gekoppeld aan aanmeldingen of als opgenomen gebruikers accounts
- Gebruikers accounts met machtigingen in gebruikers databases configureren met behulp van database rollen en expliciete machtigingen

> [!IMPORTANT]
> Data bases in Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) worden gezamenlijk in de rest van dit artikel genoemd als data bases of als Azure SQL (ter vereenvoudiging).

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

[**Verificatie**](sql-database-security-overview.md#authentication) is het proces waarbij de gebruiker wordt geclaimd. Een gebruiker maakt verbinding met een Data Base met behulp van een gebruikers account.
Wanneer een gebruiker probeert verbinding te maken met een Data Base, bieden ze een gebruikers account en verificatie gegevens. De gebruiker wordt geverifieerd met een van de volgende twee verificatie methoden:

- [SQL-verificatie](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Met deze verificatie methode verzendt de gebruiker de naam van een gebruikers account en het bijbehorende wacht woord om een verbinding tot stand te brengen. Dit wacht woord wordt opgeslagen in de hoofd database voor gebruikers accounts die zijn gekoppeld aan een aanmelding of worden opgeslagen in de data base met de gebruikers accounts die *niet* aan een aanmelding zijn gekoppeld.
- [Azure Active Directory-verificatie](sql-database-aad-authentication.md)

  Met deze verificatie methode verzendt de gebruiker de naam van een gebruikers account en vraagt hij of de service de referentie gegevens gebruikt die zijn opgeslagen in Azure Active Directory.

**Aanmeldingen en gebruikers**: in Azure SQL kan een gebruikers account in een Data Base worden gekoppeld aan een aanmelding die is opgeslagen in de hoofd database of kan een gebruikers naam zijn die is opgeslagen in een afzonderlijke data base.

- Een **aanmelding** is een afzonderlijk account in de hoofd database, waaraan een gebruikers account in een of meer data bases kan worden gekoppeld. Bij een aanmelding worden de referentie gegevens voor het gebruikers account bij de aanmelding opgeslagen.
- Een **gebruikers account** is een afzonderlijk account in een Data Base die mogelijk niet aan een aanmelding is gekoppeld. Met een gebruikers account dat niet is gekoppeld aan een aanmelding, worden de referentie gegevens opgeslagen met het gebruikers account.

[**Autorisatie**](sql-database-security-overview.md#authorization) voor toegang tot gegevens en het uitvoeren van verschillende acties wordt beheerd met behulp van database rollen en expliciete machtigingen. Autorisatie verwijst naar de machtigingen die zijn toegewezen aan een gebruiker en bepaalt wat die gebruiker mag doen. Autorisatie wordt beheerd door de databaserol [lidmaatschappen](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) en [object machtigingen](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)van uw gebruikers account. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Bestaande aanmeldingen en gebruikers accounts na het maken van een nieuwe data base

Wanneer u uw eerste Azure SQL-implementatie maakt, geeft u een beheerders aanmelding en een bijbehorend wacht woord op voor die aanmelding. Dit beheerders account wordt **Server beheerder**genoemd. De volgende configuratie van aanmeldingen en gebruikers in de hoofd-en gebruikers databases vindt plaats tijdens de implementatie:

- Er wordt een SQL-aanmelding met Administrator bevoegdheden gemaakt met behulp van de aanmeldings naam die u hebt opgegeven. Een [aanmelding](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) is een afzonderlijke gebruikers account voor aanmelding bij SQL database.
- Aan deze aanmelding worden volledige beheerders machtigingen voor alle data bases verleend als [principal op server niveau](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Deze aanmelding heeft alle beschik bare machtigingen binnen SQL Database en kan niet worden beperkt. In een beheerd exemplaar wordt deze aanmelding toegevoegd aan de [vaste serverrol sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (deze rol bestaat niet met één of gegroepeerde Data bases).
- Een [gebruikers account](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) met `dbo` de naam wordt gemaakt voor deze aanmelding in elke gebruikers database. De [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) -gebruiker heeft alle database machtigingen in de data base en is toegewezen `db_owner` aan de vaste databaserol. Aanvullende vaste database rollen worden verderop in dit artikel besproken.

Als u de beheerders accounts voor een Data Base wilt identificeren, opent u de Azure Portal en navigeert u naar het tabblad **Eigenschappen** van de server of het beheerde exemplaar.

![SQL Server-beheerders](media/sql-database-manage-logins/sql-admins.png)

![SQL Server-beheerders](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> De aanmeldings naam van de beheerder kan niet worden gewijzigd nadat deze is gemaakt. Als u het wacht woord voor de beheerder van de logische server opnieuw wilt instellen, gaat u naar de [Azure Portal](https://portal.azure.com), klikt u op **SQL-servers**, selecteert u de server in de lijst en klikt u vervolgens op **wacht woord opnieuw instellen**. Als u het wacht woord voor een Managed instance server opnieuw wilt instellen, gaat u naar de Azure Portal, klikt u op het exemplaar en klikt u op **wacht woord opnieuw instellen**. U kunt ook Power shell of de Azure CLI gebruiken.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Extra aanmeldingen en gebruikers met beheerders machtigingen maken

Op dit moment wordt uw Azure SQL-exemplaar alleen geconfigureerd voor toegang met behulp van één SQL-aanmeldings-en-gebruikers account. Als u extra aanmeldingen wilt maken met volledige of gedeeltelijke beheerders machtigingen, hebt u de volgende opties (afhankelijk van de implementatie modus):

- **Een Azure Active Directory beheerders account maken met volledige beheerders machtigingen**

  Schakel Azure Active Directory-verificatie in en maak een aanmelding voor Azure AD-beheerders. Een Azure Active Directory-account kan worden geconfigureerd als beheerder van de SQL Database-implementatie met volledige beheerders machtigingen. Dit account kan een individuele account of een beveiligings groep zijn. Een Azure AD-beheerder **moet** worden geconfigureerd als u Azure AD-accounts wilt gebruiken om verbinding te maken met SQL database. Raadpleeg de volgende artikelen voor meer informatie over het inschakelen van Azure AD-verificatie voor alle SQL Database implementatie typen:

  - [Azure Active Directory authenticatie gebruiken voor verificatie met SQL](sql-database-aad-authentication.md)
  - [Verificatie van Azure Active Directory configureren en beheren met SQL](sql-database-aad-authentication-configure.md)

- **Maak in de implementatie van een beheerd exemplaar SQL-aanmeldingen met volledige beheerders machtigingen**

  - Een aanvullend SQL Server-aanmelding maken in het beheerde exemplaar
  - Voeg de aanmelding toe aan de [vaste serverrol sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) met de instructie [ALTER server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) . Deze aanmelding heeft volledige beheerders machtigingen.
  - U kunt ook een [Azure AD-aanmelding](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) maken met de syntaxis voor het maken van een <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">aanmelding</a> .

- **Maak in een enkele of gegroepeerde implementatie SQL-aanmeldingen met beperkte beheerders machtigingen**

  - Een extra SQL-aanmelding in de hoofd database maken voor een implementatie van één of gegroepeerde Data Base, of voor een implementatie van een beheerd exemplaar
  - Een gebruikers account maken in de hoofd database die is gekoppeld aan deze nieuwe aanmelding
  - Voeg het gebruikers account toe aan `dbmanager`de, `loginmanager` de-rol of beide in `master` de-data base met behulp van de instructie [ALTER server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (voor Azure Synapse Analytics gebruikt u de instructie [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > `dbmanager`en `loginmanager` rollen hebben **geen** betrekking op implementaties van beheerde exemplaren.

  Leden van deze [speciale hoofd database rollen](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) voor één of gegroepeerde Data bases kunnen de gebruikers een bevoegdheid bieden voor het maken en beheren van data bases of voor het maken en beheren van aanmeldingen. In data bases die zijn gemaakt door een gebruiker die lid `dbmanager` is van de rol, wordt het lid `db_owner` toegewezen aan de vaste databaserol en kan die data base worden aangemeld en `dbo` beheerd met behulp van het gebruikers account. Deze rollen hebben geen expliciete machtigingen buiten de hoofd database.

  > [!IMPORTANT]
  > U kunt geen extra SQL-aanmelding met volledige beheerders machtigingen maken in een enkele of gegroepeerde Data Base.

## <a name="create-accounts-for-non-administrator-users"></a>Accounts maken voor gebruikers die geen beheerder zijn

U kunt met een van de volgende twee methoden accounts maken voor gebruikers die geen beheerder zijn:

- **Een aanmelding maken**

  Maak een SQL-aanmelding in de hoofd database. Maak vervolgens een gebruikers account in elke Data Base waartoe die gebruiker toegang nodig heeft en koppel het gebruikers account aan die aanmelding. Deze methode verdient de voor keur wanneer de gebruiker toegang moet krijgen tot meerdere data bases en u de wacht woorden gesynchroniseerd wilt laten blijven. Deze aanpak heeft echter complexer wanneer deze wordt gebruikt met geo-replicatie, aangezien de aanmelding moet worden gemaakt op zowel de primaire server als op de secundaire server (s). Zie [Azure SQL database beveiliging configureren en beheren voor geo-herstel of failover](sql-database-geo-replication-security-config.md)voor meer informatie.
- **Een gebruikersaccount maken**

  Maak een gebruikers account in de data base waartoe een gebruiker toegang nodig heeft (ook wel een [Inge sloten gebruiker](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)genoemd).

  - Met een enkele of gegroepeerde Data Base kunt u altijd dit type gebruikers account maken.
  - Met een beheerde exemplaar database die geen ondersteuning biedt voor [Azure ad server-principals](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), kunt u dit type gebruikers account alleen maken in een [Inge sloten data base](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Met een beheerd exemplaar dat [Azure ad-server-principals](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)ondersteunt, kunt u gebruikers accounts maken om te verifiëren bij het beheerde exemplaar zonder dat database gebruikers als Inge sloten database gebruiker moeten worden gemaakt.

  Met deze methode worden de verificatie gegevens van de gebruiker opgeslagen in elke Data Base en automatisch gerepliceerd naar geo-gerepliceerde data bases. Als hetzelfde account echter bestaat in meerdere data bases en u SQL-verificatie gebruikt, moet u de wacht woorden hand matig synchroniseren. Als een gebruiker ook een account in verschillende data bases met verschillende wacht woorden heeft, kan het onthouden van die wacht woorden een probleem worden.

> [!IMPORTANT]
> Als u Inge sloten gebruikers wilt maken die zijn toegewezen aan Azure AD-identiteiten, moet u zijn aangemeld met een Azure AD-account dat een beheerder is in de SQL Database. In een beheerd exemplaar kan een SQL- `sysadmin` aanmelding met machtigingen ook een Azure AD-aanmelding of-gebruiker maken.

Voor voor beelden van het maken van aanmeldingen en gebruikers raadpleegt u:

- [Aanmelding voor één of gegroepeerde Data bases maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Aanmelding maken voor de data base van het beheerde exemplaar](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Aanmelding voor Azure Synapse Analytics-Data Base maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Gebruiker maken](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [In azure AD opgenomen gebruikers maken](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Zie [zelf studie: een enkele of gegroepeerde Data Base beveiligen](sql-database-security-tutorial.md)voor een beveiligings zelfstudie die het maken SQL Server een Inge sloten gebruiker bevat in één of een gegroepeerde Data Base.

## <a name="using-fixed-and-custom-database-roles"></a>Vaste en aangepaste database rollen gebruiken

Nadat u een gebruikers account in een Data Base hebt gemaakt, op basis van een aanmelding of als een Inge sloten gebruiker, kunt u die gebruiker machtigen om verschillende acties uit te voeren en toegang te krijgen tot gegevens in een bepaalde data base. U kunt de volgende methoden gebruiken om toegang te verlenen:

- **Vaste database rollen**

  Voeg het gebruikers account toe aan een [vaste databaserol](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Er zijn negen vaste database rollen, elk met een gedefinieerde set machtigingen. De meest voorkomende vaste database rollen zijn: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**en **db_denydatareader**. **db_owner** wordt doorgaans gebruikt voor het verlenen van volledige machtigingen aan slechts enkele gebruikers. De andere vaste databaserollen zijn handig voor het snel verkrijgen van een eenvoudige database voor ontwikkeldoeleinden, maar worden niet aanbevolen voor de meeste productiedatabases. Met de **db_datareader** vaste databaserol wordt bijvoorbeeld lees toegang verleend aan elke tabel in de data base, wat meer is dan strikt nood zakelijk is.

  - Een gebruiker toevoegen aan een vaste databaserol:

    - Gebruik in Azure SQL Database de instructie [ALTER Role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) . Zie voor voor beelden [ALTER Role-voor beelden](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Gebruik de [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) -instructie om Azure Synapse Analytics te gebruiken. Zie [sp_addrolemember](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)-voor beelden voor voor beelden.

- **Aangepaste databaserol**

  Maak een aangepaste databaserol met behulp van de instructie [Create Role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) . Met een aangepaste rol kunt u uw eigen door de gebruiker gedefinieerde database rollen maken en elke rol altijd de minste machtigingen verlenen die nodig zijn voor de bedrijfs behoefte. U kunt vervolgens gebruikers toevoegen aan de aangepaste rol. Als een gebruiker lid is van meerdere rollen, worden de machtigingen van alle rollen samengevoegd.
- **Machtigingen rechtstreeks verlenen**

  Verleen de [machtigingen](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) van het gebruikers account rechtstreeks. Er zijn meer dan 100 machtigingen die afzonderlijk kunnen worden verleend of geweigerd in SQL Database. Veel van deze machtigingen zijn genest. De machtiging `UPDATE` voor een schema bevat bijvoorbeeld de machtiging `UPDATE` voor elke tabel binnen dat schema. Net als bij de meeste machtigingssystemen gaat de weigering van een machtiging vóór toestemming. Vanwege de geneste aard en het aantal machtigingen kan een nauwkeurig onderzoek nodig zijn om een geschikt machtigingssysteem te ontwerpen voor een goede bescherming van uw database. Start met de lijst van machtigingen in [Machtigingen (Database-engine)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) en controleer de [afbeelding op postergrootte](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) van de machtigingen.

## <a name="using-groups"></a>Groepen gebruiken

Efficiënt toegangs beheer maakt gebruik van machtigingen die zijn toegewezen aan Active Directory beveiligings groepen en vaste of aangepaste rollen in plaats van aan individuele gebruikers.

- Wanneer u Azure Active Directory-verificatie gebruikt, moet u Azure Active Directory gebruikers in een Azure Active Directory beveiligings groep plaatsen. Maak voor de groep een ingesloten databasegebruiker. Plaats een of meer database gebruikers in een aangepaste databaserol met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- Bij het gebruik van SQL-verificatie maakt u Inge sloten database gebruikers in de-data base. Plaats een of meer database gebruikers in een aangepaste databaserol met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

  > [!NOTE]
  > U kunt ook groepen gebruiken voor niet-Inge sloten database gebruikers.

Zorg ervoor dat u de volgende functies kunt gebruiken voor het beperken of het verhogen van machtigingen:

- [Imitatie](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) en [module-ondertekening](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) kunnen worden gebruikt om machtigingen tijdelijk veilig te verhogen.
- [Beveiliging op rijniveau](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan worden gebruikt om de rijen waartoe een gebruiker toegang heeft te beperken.
- [Gegevensmaskering](sql-database-dynamic-data-masking-get-started.md) kan worden gebruikt om de weergave van gevoelige gegevens te beperken.
- [Opgeslagen procedures](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) kunnen worden gebruikt om de acties die op de database kunnen worden uitgevoerd te beperken.

## <a name="next-steps"></a>Volgende stappen

Zie [SQL security overview](sql-database-security-overview.md) (SQL-beveiligingsoverzicht) voor een overzicht van alle beveiligingsfuncties van SQL Database.
