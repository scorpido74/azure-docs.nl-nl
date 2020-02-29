---
title: Azure Active Directory
description: Meer informatie over het gebruik van Azure Active Directory voor verificatie met SQL Database, een beheerd exemplaar en Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 818337f478a506f80ba87979aa1915d54e6457cf
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78193778"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Azure Active Directory authenticatie gebruiken voor verificatie met SQL

Azure Active Directory-verificatie is een mechanisme om verbinding te maken met Azure [SQL database](sql-database-technical-overview.md), een [beheerd exemplaar](sql-database-managed-instance.md)en [Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in azure Active Directory (Azure AD). 

> [!NOTE]
> Dit onderwerp is van toepassing op Azure SQL Server en op zowel SQL Database als Azure Synapse die zijn gemaakt op de Azure SQL-Server. SQL Database wordt gebruikt bij het verwijzen naar SQL Database en Azure Synapse voor eenvoud.

Met Azure AD-verificatie kunt u de identiteit van databasegebruikers en andere Microsoft-services op één locatie centraal beheren. Centraal-ID-beheer biedt één locatie voor het beheren van database gebruikers en het vereenvoudigt het beheer van machtigingen. Dit biedt verschillende voordelen, zoals:

- Het biedt een alternatief voor SQL Server-verificatie.
- Helpt de verspreiding van gebruikers identiteiten over database servers te stoppen.
- Hiermee staat u het draaien van wacht woorden op één plek toe.
- Klanten kunnen database machtigingen beheren met externe groepen (Azure AD).
- Hiermee kan worden voor komen dat wacht woorden worden opgeslagen door geïntegreerde Windows-verificatie en andere vormen van verificatie die door Azure Active Directory worden ondersteund, in te scha kelen.
- Voor Azure AD-verificatie worden de Inge sloten database gebruikers gebruikt voor het verifiëren van identiteiten op database niveau.
- Azure AD biedt ondersteuning voor verificatie op basis van tokens voor toepassingen die verbinding maken met SQL Database.
- Azure AD-verificatie ondersteunt ADFS (Domain Federation) of systeem eigen gebruikers-en wachtwoord verificatie voor een lokale Azure Active Directory zonder domein synchronisatie.
- Azure AD ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory Universal-verificatie, waaronder Multi-Factor Authentication (MFA).  MFA omvat robuuste verificatie met een scala aan gebruikersvriendelijke verificatieopties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app. Zie [SSMS-ondersteuning voor Azure AD MFA met SQL database en Azure Synapse](sql-database-ssms-mfa-authentication.md)voor meer informatie.
- Azure AD biedt ondersteuning voor vergelijkbare verbindingen van SQL Server Data Tools (SSDT) die gebruikmaken van Active Directory Interactive Authentication. Zie [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory) (Ondersteuning van Azure Active Directory in SQL Server Data Tools (SSDT)) voor meer informatie.

> [!NOTE]  
> Het is niet mogelijk om verbinding te maken met SQL Server die worden uitgevoerd op een virtuele Azure-machine met een Azure Active Directory-account. Gebruik in plaats daarvan een domein Active Directory account.  

De configuratie stappen omvatten de volgende procedures voor het configureren en gebruiken van Azure Active Directory-verificatie.

1. Azure AD maken en vullen.
2. Optioneel: koppel of wijzig de Active Directory die momenteel aan uw Azure-abonnement is gekoppeld.
3. Maak een Azure Active Directory beheerder voor de Azure SQL Database-Server, het beheerde exemplaar of de [SQL-groep](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configureer uw client computers.
5. Maak Inge sloten database gebruikers in uw data base die zijn toegewezen aan Azure AD-identiteiten.
6. Maak verbinding met uw data base met behulp van Azure AD-identiteiten.

> [!NOTE]
> Zie [Azure AD configureren met Azure SQL database](sql-database-aad-authentication-configure.md)voor meer informatie over het maken en vullen van Azure AD en het configureren van Azure ad met Azure SQL database, een beheerd exemplaar en een SQL-groep.

## <a name="trust-architecture"></a>Vertrouwens architectuur

In het volgende diagram op hoog niveau wordt een overzicht gegeven van de oplossings architectuur van het gebruik van Azure AD-verificatie met Azure SQL Database. Dezelfde concepten zijn van toepassing op Azure Synapse. Ter ondersteuning van het native gebruikers wachtwoord van Azure AD worden alleen het Cloud gedeelte en Azure AD/Azure SQL Database beschouwd. Voor de ondersteuning van federatieve verificatie (of gebruiker/wacht woord voor Windows-referenties), is de communicatie met ADFS-blok vereist. De pijlen geven communicatie paden aan.

![Aad-verificatie diagram][1]

In het volgende diagram worden de relaties Federatie, vertrouwen en hosting aangegeven waarmee een client verbinding kan maken met een Data Base door een token in te dienen. Het token wordt geverifieerd door een Azure AD en wordt vertrouwd door de data base. Klant 1 kan een Azure Active Directory met systeem eigen gebruikers of een Azure AD met federatieve gebruikers vertegenwoordigen. Klant 2 is een mogelijke oplossing met inbegrip van geïmporteerde gebruikers; in dit voor beeld afkomstig van een federatieve Azure Active Directory met ADFS gesynchroniseerd met Azure Active Directory. Het is belang rijk om te begrijpen dat toegang tot een Data Base met behulp van Azure AD-verificatie vereist dat het hosting-abonnement is gekoppeld aan Azure AD. Hetzelfde abonnement moet worden gebruikt voor het maken van de SQL Server die als host fungeert voor de Azure SQL Database of Azure Synapse.

![abonnements relatie][2]

## <a name="administrator-structure"></a>Beheerder structuur

Wanneer u Azure AD-verificatie gebruikt, zijn er twee beheerders accounts voor de SQL Database-Server en het beheerde exemplaar. de oorspronkelijke SQL Server beheerder en de Azure AD-beheerder. Dezelfde concepten zijn van toepassing op Azure Synapse. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-database gebruiker in een gebruikers database maken. De aanmelding van de Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groeps account is, kan dit worden gebruikt door elk groepslid om meerdere Azure AD-beheerders in te scha kelen voor het SQL Server-exemplaar. Het gebruik van groeps account als beheerder verbetert de beheer baarheid, zodat u groeps leden in azure AD centraal kunt toevoegen en verwijderen zonder dat u de gebruikers of machtigingen in SQL Database hoeft te wijzigen. Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd.

![beheerder structuur][3]

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt maken, moet u de machtiging `ALTER ANY USER` hebben in de-data base. De `ALTER ANY USER` machtiging kan worden verleend aan elke database gebruiker. De `ALTER ANY USER` machtigingen worden ook bewaard door de beheerders accounts van de server en database gebruikers met de `CONTROL ON DATABASE`-of `ALTER ON DATABASE`-machtiging voor die data base, en door leden van de `db_owner`-databaserol.

Als u een Inge sloten database gebruiker wilt maken in Azure SQL Database, een beheerd exemplaar of Azure Synapse, moet u verbinding maken met de data base of het exemplaar met behulp van een Azure AD-identiteit. Als u de eerste Inge sloten database gebruiker wilt maken, moet u verbinding maken met de data base met behulp van een Azure AD-beheerder (die de eigenaar van de data base is). Dit wordt geïllustreerd in [Azure Active Directory authenticatie configureren en beheren met SQL database of Azure Synapse](sql-database-aad-authentication-configure.md). Een Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Azure SQL Database of Azure Synapse server. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory gebruikers die eerder in SQL Server zijn gemaakt, geen verbinding meer maken met de data base met behulp van hun Azure Active Directory referenties.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-functies en-beperkingen

- De volgende leden van Azure AD kunnen worden ingericht in Azure SQL Server of Azure Synapse:

  - Systeem eigen leden: een lid dat is gemaakt in azure AD in het beheerde domein of in een klant domein. Zie [uw eigen domein naam toevoegen aan Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)voor meer informatie.
  - Federatieve domein leden: een lid dat is gemaakt in azure AD met een federatief domein. Zie [Microsoft Azure nu ondersteuning biedt voor Federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)voor meer informatie.
  - Geïmporteerde leden van andere Azure AD-personen die systeem eigen of federatieve domein leden zijn.
  - Active Directory groepen die zijn gemaakt als beveiligings groepen.

- Azure AD-gebruikers die deel uitmaken van een groep met `db_owner` serverrol, kunnen de syntaxis **[Create Data Base scoped CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** niet gebruiken voor Azure SQL database en Azure Synapse. U ziet de volgende fout:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Verleen de `db_owner` rol rechtstreeks aan de individuele Azure AD-gebruiker om het probleem met de referentie voor het maken van de **Data Base** te beperken.

- Deze systeem functies retour neren NULL-waarden wanneer ze worden uitgevoerd onder Azure AD-principals:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Beheerde exemplaren

- Azure AD-server-principals (aanmeldingen) en gebruikers worden ondersteund als een preview-functie voor [beheerde exemplaren](sql-database-managed-instance.md).
- Het instellen van Azure AD-server principals (aanmeldingen) die zijn toegewezen aan een Azure AD-groep als de data base-eigenaar, wordt niet ondersteund in [beheerde exemplaren](sql-database-managed-instance.md).
    - Een uitbrei ding hiervan is dat wanneer een groep wordt toegevoegd als onderdeel van de `dbcreator` serverrol, gebruikers uit deze groep verbinding kunnen maken met het beheerde exemplaar en nieuwe data bases kunnen worden gemaakt, maar geen toegang tot de data base hebben. Dit is omdat de nieuwe data base-eigenaar SA is en niet de Azure AD-gebruiker. Dit probleem treedt niet op als de afzonderlijke gebruiker wordt toegevoegd aan de server functie `dbcreator`.
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

De volgende verificatie methoden worden ondersteund voor Azure AD server-principals (aanmeldingen) (**open bare preview**):

- Azure Active Directory wacht woord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA


### <a name="additional-considerations"></a>Aanvullende overwegingen

- Voor een betere beheer baarheid raden wij u aan een exclusieve Azure AD-groep in te richten als beheerder.   
- Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd voor een Azure SQL Database-Server of Azure-Synapse.
  - Het toevoegen van Azure AD-server-principals (aanmeldingen) voor beheerde instanties (**open bare preview**) biedt de mogelijkheid om meerdere Azure ad server-principals (aanmeldingen) te maken die kunnen worden toegevoegd aan de `sysadmin` rol.
- Alleen een Azure AD-beheerder voor SQL Server kan in eerste instantie verbinding maken met de Azure SQL Database-Server, het beheerde exemplaar of Azure SQL Synapse via een Azure Active Directory-account. De beheerder van de Active Directory kan nieuwe Azure AD-database gebruikers configureren.   
- Het is raadzaam om de time-out voor de verbinding in te stellen op 30 seconden.   
- SQL Server 2016 Management Studio en SQL Server Data Tools voor Visual Studio 2015 (versie 14.0.60311.1 2016 april of hoger) ondersteunen Azure Active Directory-verificatie. (Azure AD-verificatie wordt ondersteund door de **.NET Framework gegevens provider voor sqlserver**; ten minste versie .NET Framework 4,6). Daarom zijn de nieuwste versies van deze hulpprogram ma's en gegevenslaag toepassingen (DAC en. BACPAC) kan gebruikmaken van Azure AD-verificatie.   
- Vanaf versie 15.0.1, [sqlcmd utility](/sql/tools/sqlcmd-utility) en [bcp Utility](/sql/tools/bcp-utility) ondersteunen Active Directory interactieve verificatie met MFA.
- SQL Server Data Tools voor Visual Studio 2015 vereist ten minste de versie van april 2016 van de gegevens Hulpprogramma's (versie 14.0.60311.1). Momenteel worden Azure AD-gebruikers niet weer gegeven in SSDT Objectverkenner. Als tijdelijke oplossing kunt u de gebruikers weer geven in [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Micro soft JDBC-stuur programma 6,0 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ondersteunt Azure AD-verificatie. Zie ook [de verbindings eigenschappen instellen](https://msdn.microsoft.com/library/ms378988.aspx).   
- Poly Base kan niet worden geverifieerd met behulp van Azure AD-verificatie.   
- Azure AD-verificatie wordt ondersteund voor SQL Database door de Blades Azure Portal **Data Base importeren** en **Data Base exporteren** . Importeren en exporteren met behulp van Azure AD-verificatie wordt ook ondersteund door de Power shell-opdracht.   
- Azure AD-verificatie wordt ondersteund voor SQL Database, een beheerd exemplaar en Azure Synapse met behulp van CLI. Zie [Azure Active Directory-verificatie configureren en beheren met SQL database of Azure Synapse](sql-database-aad-authentication-configure.md) en [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Voor informatie over het maken en vullen van Azure AD en het configureren van Azure AD met Azure SQL Database of Azure Synapse, raadpleegt u [Azure Active Directory-verificatie configureren en beheren met SQL database, beheerd exemplaar of Azure Synapse](sql-database-aad-authentication-configure.md).
- Voor een zelf studie over het gebruik van Azure AD server-principals (aanmeldingen) met beheerde exemplaren raadpleegt u [Azure ad server-principals (aanmeldingen) met beheerde instanties](sql-database-managed-instance-aad-security-tutorial.md)
- Zie [SQL Database-toegang en -beheer](sql-database-control-access.md) voor een overzicht van toegang en beheer in SQL Database.
- Zie [Aanmeldingen, gebruikers en databaserollen](sql-database-manage-logins.md) voor een overzicht van aanmeldingen, gebruikers en databaserollen in SQL Database.
- Zie [Principals](https://msdn.microsoft.com/library/ms181127.aspx) voor meer informatie over database-principals.
- Zie [Databaserollen](https://msdn.microsoft.com/library/ms189121.aspx) voor meer informatie over databaserollen.
- Zie [login maken](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)voor een syntaxis voor het maken van Azure ad-server principals (aanmeldingen) voor beheerde exemplaren.
- Zie [SQL Database-firewallregels](sql-database-firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
