---
title: Azure Active Directory
description: Meer informatie over het gebruik van Azure Active Directory voor verificatie met Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 04/23/2020
ms.openlocfilehash: a1d6ea37c162a5b2bbe9066e25f80f972ce897ee
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041400"
---
# <a name="use-azure-active-directory-authentication"></a>Azure Active Directory-verificatie gebruiken
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory-verificatie is een mechanisme om verbinding te maken met [Azure SQL database](sql-database-paas-overview.md), [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md)en [azure Synapse Analytics (voorheen Azure SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in azure Active Directory (Azure AD).

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL Database, SQL Managed instance en Azure Synapse. Voor eenvoud wordt Azure SQL gebruikt bij het verwijzen naar de drie.

Met Azure AD-verificatie kunt u de identiteit van databasegebruikers en andere Microsoft-services op één locatie centraal beheren. Centraal identiteitsbeheer biedt één plek voor het beheren van databasegebruikers en vereenvoudigt het machtigingenbeheer. Dit biedt verschillende voordelen, zoals:

- Het is een alternatief voor SQL Server-verificatie
- Helpt de verspreiding van gebruikers identiteiten op meerdere servers te stoppen
- Maakt rotatie van wachtwoorden op één plek mogelijk
- Klanten kunnen databasemachtigingen beheren met behulp van externe groepen (van Azure AD)
- Wachtwoorden hoeven niet meer te worden opgeslagen door het inschakelen van geïntegreerde Windows-verificatie en andere vormen van authenticatie die worden ondersteund door Azure Active Directory
- Azure AD-verificatie gebruikt ingesloten databasegebruikers voor het verifiëren van identiteiten op databaseniveau
- Azure AD biedt ondersteuning voor verificatie op basis van tokens voor toepassingen die verbinding maken met Azure SQL
- Azure AD-verificatie ondersteunt:
  - Alleen Azure AD-Cloud-identiteiten
  - Hybride Azure AD-identiteiten die ondersteuning bieden voor:
    - Cloud verificatie met twee opties in combi natie met naadloze eenmalige aanmelding (SSO): **Pass Through-** verificatie en verificatie van **wacht woord-hash**
    - Federatieve verificatie
  - Zie het volgende artikel voor meer informatie over Azure AD-verificatie methoden en welke u er één kunt kiezen:
    - [Kies de juiste verificatie methode voor uw Azure Active Directory hybride identiteits oplossing](../../active-directory/hybrid/choose-ad-authn.md)
- Azure AD ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory Universal-verificatie, waaronder Multi-Factor Authentication (MFA). MFA omvat robuuste verificatie met een scala aan gebruikersvriendelijke verificatieopties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app. Zie [SSMS-ondersteuning voor Azure AD MFA met Azure SQL database, SQL Managed instance en Azure Synapse](authentication-mfa-ssms-overview.md) voor meer informatie.
- Azure AD biedt ondersteuning voor vergelijkbare verbindingen van SQL Server Data Tools (SSDT) die gebruikmaken van Active Directory Interactive Authentication. Zie [Azure Active Directory-ondersteuning in SQL Server Data tools (SSDT)](/sql/ssdt/azure-active-directory) voor meer informatie.

> [!NOTE]  
> Het is niet mogelijk om verbinding te maken met SQL Server die worden uitgevoerd op een virtuele Azure-machine met een Azure Active Directory-account. Gebruik in plaats hiervan een Active Directory-domeinaccount.  

De configuratie stappen omvatten de volgende procedures voor het configureren en gebruiken van Azure Active Directory-verificatie.

1. Azure AD maken en vullen.
2. Optioneel: koppel of wijzig de Active Directory die momenteel aan uw Azure-abonnement is gekoppeld.
3. Maak een Azure Active Directory-beheerder.
4. Configureer uw client computers.
5. Maak Inge sloten database gebruikers in uw data base die zijn toegewezen aan Azure AD-identiteiten.
6. Maak verbinding met uw data base met behulp van Azure AD-identiteiten.

> [!NOTE]
> Zie [Azure AD configureren met Azure SQL database](authentication-aad-configure.md)voor meer informatie over het maken en vullen van Azure AD en het configureren van Azure ad met Azure SQL database, SQL Managed instance en Azure Synapse.

## <a name="trust-architecture"></a>Vertrouwens architectuur

- Ter ondersteuning van het native gebruikers wachtwoord van Azure AD worden alleen het Cloud gedeelte en Azure AD/Azure SQL in overweging genomen.
- Als u referenties voor eenmalige aanmelding (of gebruiker/wacht woord voor Windows-referenties) voor Windows wilt ondersteunen, gebruikt u Azure Active Directory referenties van een federatief of beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding voor Pass-Through-en wacht woord-hash-verificatie. Zie [Azure Active Directory naadloze eenmalige aanmelding](../../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.
- Voor de ondersteuning van federatieve verificatie (of gebruiker/wacht woord voor Windows-referenties), is de communicatie met ADFS-blok vereist.

Raadpleeg de volgende artikelen voor meer informatie over hybride Azure AD-identiteiten, de instellingen en synchronisatie:

- Wachtwoord verificatie voor wacht woord- [hash-synchronisatie implementeren met Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Pass-Through-verificatie- [Azure Active Directory Pass-Through-verificatie](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federatieve verificatie- [Active Directory Federation Services implementeren in azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) en [Azure AD Connect en Federatie](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Zie het onderstaande diagram voor een voor beeld Federated Authentication met ADFS-infra structuur (of gebruiker/wacht woord voor Windows-referenties). De pijlen geven communicatie paden aan.

![Aad-verificatie diagram][1]

In het volgende diagram worden de relaties Federatie, vertrouwen en hosting aangegeven waarmee een client verbinding kan maken met een Data Base door een token in te dienen. Het token wordt geverifieerd door een Azure AD en wordt vertrouwd door de data base. Klant 1 kan een Azure Active Directory met systeem eigen gebruikers of een Azure AD met federatieve gebruikers vertegenwoordigen. Klant 2 is een mogelijke oplossing met inbegrip van geïmporteerde gebruikers; in dit voor beeld afkomstig van een federatieve Azure Active Directory met ADFS gesynchroniseerd met Azure Active Directory. Het is belang rijk om te begrijpen dat toegang tot een Data Base met behulp van Azure AD-verificatie vereist dat het hosting-abonnement is gekoppeld aan Azure AD. Hetzelfde abonnement moet worden gebruikt om de Azure SQL Database, het SQL Managed instance of Azure Synapse-resources te maken.

![abonnements relatie][2]

## <a name="administrator-structure"></a>Beheerder structuur

Wanneer u Azure AD-verificatie gebruikt, zijn er twee beheerders accounts. de oorspronkelijke SQL-beheerder en de Azure AD-beheerder. Dezelfde concepten zijn van toepassing op Azure Synapse. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-database gebruiker in een gebruikers database maken. De aanmelding van de Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groeps account is, kan dit worden gebruikt door elk groepslid om meerdere Azure AD-beheerders in te scha kelen voor het SQL Server-exemplaar. Het gebruik van groeps account als beheerder verbetert de beheer baarheid, zodat u groeps leden in azure AD centraal kunt toevoegen en verwijderen zonder de gebruikers of machtigingen in Azure SQL te wijzigen. Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd.

![beheerder structuur][3]

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt maken, moet u de `ALTER ANY USER` machtiging hebben in de-data base. De `ALTER ANY USER` machtiging kan worden verleend aan elke database gebruiker. De `ALTER ANY USER` machtigingen worden ook opgeslagen door de beheerders accounts van de server en database gebruikers met `CONTROL ON DATABASE` de `ALTER ON DATABASE` machtiging of de machtigingen voor die data base, en door leden van de databaserol `db_owner` .

Als u een Inge sloten database gebruiker wilt maken in Azure SQL Database, SQL Managed instance of Azure Synapse, moet u verbinding maken met de data base of het exemplaar met behulp van een Azure AD-identiteit. Als u de eerste Inge sloten database gebruiker wilt maken, moet u verbinding maken met de data base met behulp van een Azure AD-beheerder (die de eigenaar van de data base is). Dit wordt geïllustreerd in [Azure Active Directory authenticatie configureren en beheren met SQL database of Azure Synapse](authentication-aad-configure.md). Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Azure SQL Database, een beheerd exemplaar van SQL of Azure Synapse. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory gebruikers die eerder in SQL Server zijn gemaakt, geen verbinding meer maken met de data base met behulp van hun Azure Active Directory referenties.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-functies en-beperkingen

- De volgende leden van Azure AD kunnen worden ingericht voor Azure SQL:

  - Systeem eigen leden: een lid dat is gemaakt in azure AD in het beheerde domein of in een klant domein. Zie [uw eigen domein naam toevoegen aan Azure AD](../../active-directory/fundamentals/add-custom-domain.md)voor meer informatie.
  - Leden van een Active Directory domein Federated met Azure Active Directory op een beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding met pass-through of wacht woord-hash-verificatie. Zie [Microsoft Azure nu ondersteuning biedt voor Federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) en [Azure Active Directory naadloze eenmalige aanmelding](../../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.
  - Geïmporteerde leden van andere Azure AD-personen die systeem eigen of federatieve domein leden zijn.
  - Active Directory groepen die zijn gemaakt als beveiligings groepen.

- Azure AD-gebruikers die deel uitmaken van een groep met `db_owner` serverrol, kunnen de **[referentie Create Data Base scoped CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** niet gebruiken voor Azure SQL database en Azure Synapse. U ziet de volgende fout:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Verleen de `db_owner` rol rechtstreeks aan de individuele Azure AD-gebruiker om het probleem met de **referentie Create Data Base scoped** te beperken.

- Deze systeem functies retour neren NULL-waarden wanneer ze worden uitgevoerd onder Azure AD-principals:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL Managed Instance

- Azure AD-server-principals (aanmeldingen) en gebruikers worden ondersteund voor door [SQL beheerd exemplaar](../managed-instance/sql-managed-instance-paas-overview.md).
- Het instellen van Azure AD-server principals (aanmeldingen) die zijn toegewezen aan een Azure AD-groep als de data base-eigenaar, wordt niet ondersteund in [SQL Managed instances](../managed-instance/sql-managed-instance-paas-overview.md).
  - Een uitbrei ding hiervan is dat wanneer een groep wordt toegevoegd als onderdeel van de `dbcreator` Server functie, gebruikers van deze groep verbinding kunnen maken met het SQL Managed instance en nieuwe data bases kunnen maken, maar geen toegang tot de data base hebben. Dit is omdat de nieuwe data base-eigenaar SA is en niet de Azure AD-gebruiker. Dit probleem treedt niet op als de afzonderlijke gebruiker wordt toegevoegd aan de `dbcreator` Server functie.
- De uitvoering van SQL Agent-beheer en-taken wordt ondersteund voor Azure AD-server-principals (aanmeldingen).
- Database-back-up en herstelbewerkingen kunnen worden uitgevoerd door Azure AD-server-principals (aanmeldingen).
- Controle van alle-instructies met betrekking tot Azure AD-server-principals (aanmeldingen) en verificatie gebeurtenissen wordt ondersteund.
- Exclusieve beheerders verbinding voor Azure AD-server-principals (aanmeldingen) die lid zijn van de serverrol sysadmin wordt ondersteund.
  - Ondersteund via SQLCMD-hulp programma en SQL Server Management Studio.
- Aanmeldingstriggers worden ondersteund voor aanmeldingsgebeurtenissen die afkomstig zijn van Azure AD-server-principal s(aanmeldingen).
- Service Broker en DB-mail kunnen worden ingesteld met behulp van een Azure AD server-principal (aanmelden).

## <a name="connecting-using-azure-ad-identities"></a>Verbinding maken met Azure AD-identiteiten

Azure Active Directory-verificatie ondersteunt de volgende methoden om verbinding te maken met een Data Base met behulp van Azure AD-identiteiten:

- Azure Active Directory wacht woord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA
- Verificatie van toepassings token gebruiken

De volgende verificatie methoden worden ondersteund voor Azure AD-server-principals (aanmeldingen):

- Azure Active Directory wacht woord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA

### <a name="additional-considerations"></a>Aanvullende overwegingen

- Voor een betere beheer baarheid raden wij u aan een exclusieve Azure AD-groep in te richten als beheerder.
- Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd voor een server in SQL Database of Azure Synapse.
  - Het toevoegen van Azure AD-server-principals (aanmeldingen) voor SQL Managed instances maakt het mogelijk om meerdere Azure AD server-principals (aanmeldingen) te maken die aan de rol kunnen worden toegevoegd `sysadmin` .
- Alleen een Azure AD-beheerder voor de server kan voor het eerst verbinding maken met de server of het beheerde exemplaar met behulp van een Azure Active Directory-account. De beheerder van de Active Directory kan nieuwe Azure AD-database gebruikers configureren.
- Het is raadzaam om de time-out voor de verbinding in te stellen op 30 seconden.
- SQL Server 2016 Management Studio en SQL Server Data Tools voor Visual Studio 2015 (versie 14.0.60311.1 2016 april of hoger) ondersteunen Azure Active Directory-verificatie. (Azure AD-verificatie wordt ondersteund door de **.NET Framework gegevens provider voor sqlserver**; ten minste versie .NET Framework 4,6). Daarom zijn de nieuwste versies van deze hulpprogram ma's en gegevenslaag toepassingen (DAC en. BACPAC) kan gebruikmaken van Azure AD-verificatie.
- Vanaf versie 15.0.1, [sqlcmd utility](/sql/tools/sqlcmd-utility) en [bcp Utility](/sql/tools/bcp-utility) ondersteunen Active Directory interactieve verificatie met MFA.
- SQL Server Data Tools voor Visual Studio 2015 vereist ten minste de versie van april 2016 van de gegevens Hulpprogramma's (versie 14.0.60311.1). Op dit moment worden Azure AD-gebruikers niet weer gegeven in SSDT Objectverkenner. Als tijdelijke oplossing kunt u de gebruikers weer geven in [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Micro soft JDBC-stuur programma 6,0 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ondersteunt Azure AD-verificatie. Zie ook [de verbindings eigenschappen instellen](/sql/connect/jdbc/setting-the-connection-properties).
- Poly Base kan niet worden geverifieerd met behulp van Azure AD-verificatie.
- Azure AD-verificatie wordt ondersteund voor Azure SQL Database en Azure Synapse via de Blade Azure Portal **Data Base importeren** en **Data Base exporteren** . Importeren en exporteren met behulp van Azure AD-verificatie wordt ook ondersteund vanuit een Power shell-opdracht.
- Azure AD-verificatie wordt ondersteund voor SQL Database, SQL Managed instance en Azure Synapse met behulp van CLI. Zie [Azure Active Directory-verificatie configureren en beheren met SQL database of Azure Synapse](authentication-aad-configure.md) en [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het maken en vullen van Azure AD en vervolgens Azure AD configureren met Azure SQL Database, SQL Managed instance of Azure Synapse, [Azure Active Directory authenticatie configureren en beheren met SQL database, SQL Managed instance of Azure Synapse](authentication-aad-configure.md).
- Voor een zelf studie over het gebruik van Azure AD server-principals (aanmeldingen) met SQL Managed instances raadpleegt u [Azure ad server-principals (aanmeldingen) met door SQL beheerde exemplaren](../managed-instance/aad-security-configure-tutorial.md)
- Zie [aanmeldingen, gebruikers, database rollen en machtigingen](logins-create-manage.md)voor een overzicht van aanmeldingen, gebruikers, database rollen en machtigingen in SQL database.
- Zie [Principals](https://msdn.microsoft.com/library/ms181127.aspx) voor meer informatie over database-principals.
- Zie [Databaserollen](https://msdn.microsoft.com/library/ms189121.aspx) voor meer informatie over databaserollen.
- Zie [login maken](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)voor de syntaxis voor het maken van Azure ad-server principals (aanmeldingen) voor door SQL beheerde exemplaren.
- Zie [SQL Database-firewallregels](firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
