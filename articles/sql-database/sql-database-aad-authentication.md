---
title: Azure Active Directory
description: Meer informatie over het gebruik van Azure Active Directory voor verificatie met SQL Database, beheerde instantie en Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421111"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Azure Active Directory-verificatie gebruiken voor verificatie met SQL

Azure Active Directory-verificatie is een mechanisme om verbinding te maken met Azure [SQL Database,](sql-database-technical-overview.md) [beheerde instantie](sql-database-managed-instance.md)en [Azure Synapse Analytics (voorheen Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in Azure Active Directory (Azure AD). 

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database als Azure Synapse. Voor de eenvoud wordt SQL Database gebruikt bij het verwijzen naar zowel SQL Database als Azure Synapse.

Met Azure AD-verificatie kunt u de identiteit van databasegebruikers en andere Microsoft-services op één locatie centraal beheren. Centraal identiteitsbeheer biedt één plek voor het beheren van databasegebruikers en vereenvoudigt het machtigingenbeheer. Dit biedt verschillende voordelen, zoals:

- Het is een alternatief voor SQL Server-verificatie
- Helpt de verspreiding van gebruikersidentiteiten tussen databaseservers te stoppen.
- Maakt rotatie van wachtwoorden op één plek mogelijk
- Klanten kunnen databasemachtigingen beheren met behulp van externe groepen (van Azure AD)
- Wachtwoorden hoeven niet meer te worden opgeslagen door het inschakelen van geïntegreerde Windows-verificatie en andere vormen van authenticatie die worden ondersteund door Azure Active Directory
- Azure AD-verificatie gebruikt ingesloten databasegebruikers voor het verifiëren van identiteiten op databaseniveau
- Azure AD biedt ondersteuning voor verificatie op basis van tokens voor toepassingen die verbinding maken met SQL Database
- Azure AD-verificatie ondersteunt:
  - Azure AD-cloud-identiteiten
  - Hybride azure-identiteiten van AD die ondersteuning bieden voor:
    - Cloudverificatie met twee opties in combinatie met naadloze single sign-on (SSO): **pass-through** authenticatie en **wachtwoordhash** authenticatie
    - Federatieve verificatie
  - Zie het volgende artikel voor meer informatie over Azure AD-verificatiemethoden en welke te kiezen:
    - [De juiste verificatiemethode kiezen voor uw azure Active Directory hybride identiteitsoplossing](../active-directory/hybrid/choose-ad-authn.md)
- Azure AD ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory Universal-verificatie, waaronder Multi-Factor Authentication (MFA).  MFA omvat robuuste verificatie met een scala aan gebruikersvriendelijke verificatieopties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app. Zie [SSMS-ondersteuning voor Azure AD MFA met SQL Database en Azure Synapse voor](sql-database-ssms-mfa-authentication.md) meer informatie
- Azure AD biedt ondersteuning voor vergelijkbare verbindingen van SQL Server Data Tools (SSDT) die gebruikmaken van Active Directory Interactive Authentication. Zie [Azure Active Directory-ondersteuning in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory) voor meer informatie.

> [!NOTE]  
> Verbinding maken met SQL Server die wordt uitgevoerd op een Azure VM wordt niet ondersteund met een Azure Active Directory-account. Gebruik in plaats daarvan een Active Directory-account voor een domein.  

De configuratiestappen omvatten de volgende procedures voor het configureren en gebruiken van Azure Active Directory-verificatie.

1. Azure AD maken en invullen.
2. Optioneel: de active directory koppelen of wijzigen die momenteel is gekoppeld aan uw Azure-abonnement.
3. Maak een Azure Active Directory-beheerder voor de Azure SQL Database-server, de beheerde instantie of [Azure Synapse.](https://azure.microsoft.com/services/sql-data-warehouse/)
4. Configureer uw clientcomputers.
5. Contactpersonen maken voor databasegebruikers in uw database die zijn toegewezen aan Azure AD-identiteiten.
6. Maak verbinding met uw database met Azure AD-identiteiten.

> [!NOTE]
> Zie Azure AD configureren met Azure [SQL Database](sql-database-aad-authentication-configure.md)voor meer informatie over het maken en invullen van Azure AD met Azure SQL Database, beheerde instantie en Azure Synapse.

## <a name="trust-architecture"></a>Vertrouwensarchitectuur

- Als u het native gebruikerswachtwoord van Azure AD wilt ondersteunen, wordt alleen het cloudgedeelte en azure AD/Azure SQL Database in aanmerking genomen.
- Als u windows-inloggegevens (of gebruikers/wachtwoord voor Windows-referenties) wilt ondersteunen, gebruikt u Azure Active Directory-referenties van een federatief of beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding voor verificatie van doorgeef- en wachtwoordhash. Zie [Azure Active Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.
- Om Federated-verificatie (of gebruiker/wachtwoord voor Windows-referenties) te ondersteunen, is de communicatie met ADFS-blokkering vereist.

Zie de volgende artikelen voor meer informatie over hybride azure-identiteiten van AD, de installatie en synchronisatie:

- Wachtwoordhashverificatie - [Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Verificatie doorgeven - [Azure Active Directory Pass-Through-verificatie](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federatieve verificatie - [Active Directory Federation Services implementeren in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) en Azure AD Connect en [federatie](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Zie het onderstaande diagram voor een voorbeeldfederated authentication met ADFS-infrastructuur (of gebruiker/wachtwoord voor Windows-referenties). De pijlen geven communicatietrajecten aan.

![aad auth-diagram][1]

In het volgende diagram worden de federatie-, vertrouwens- en hostingrelaties weergegeven waarmee een client verbinding kan maken met een database door een token in te dienen. Het token wordt geverifieerd door een Azure AD en wordt vertrouwd door de database. Klant 1 kan een Azure Active Directory vertegenwoordigen met native gebruikers of een Azure AD met federatieve gebruikers. Klant 2 vertegenwoordigt een mogelijke oplossing inclusief geïmporteerde gebruikers; in dit voorbeeld afkomstig van een gefedereerde Azure Active Directory met ADFS die wordt gesynchroniseerd met Azure Active Directory. Het is belangrijk om te begrijpen dat toegang tot een database met Azure AD-verificatie vereist dat het hostingabonnement is gekoppeld aan het Azure AD. Hetzelfde abonnement moet worden gebruikt om de SQL Server te maken die de Azure SQL Database of Azure Synapse host.

![abonnementsrelatie][2]

## <a name="administrator-structure"></a>Beheerdersstructuur

Bij het gebruik van Azure AD-verificatie zijn er twee administratoraccounts voor de SQL Database-server en beheerde instantie. de oorspronkelijke SQL Server-beheerder en de Azure AD-beheerder. Dezelfde concepten zijn van toepassing op Azure Synapse. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-databasegebruiker maken in een gebruikersdatabase. De aanmelding van Azure AD-beheerders kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groepsaccount is, kan het door elk groepslid worden gebruikt, waardoor meerdere Azure AD-beheerders voor het SQL Server-exemplaar worden inschakelt. Het gebruik van groepsaccount als beheerder verbetert de beheerbaarheid doordat u groepsleden centraal toevoegen en verwijderen in Azure AD zonder de gebruikers of machtigingen in SQL Database te wijzigen. Slechts één Azure AD-beheerder (een gebruiker of groep) kan op elk gewenst moment worden geconfigureerd.

![beheerstructuur][3]

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt `ALTER ANY USER` maken, moet u de toestemming in de database hebben. De `ALTER ANY USER` toestemming kan worden verleend aan elke databasegebruiker. De `ALTER ANY USER` machtiging is ook in het bezit van `CONTROL ON DATABASE` de `ALTER ON DATABASE` serverbeheerdersaccounts en databasegebruikers `db_owner` met de of toestemming voor die database en door leden van de databaserol.

Als u een opgenomen databasegebruiker wilt maken in Azure SQL Database, beheerde instantie of Azure Synapse, moet u verbinding maken met de database of instantie met behulp van een Azure AD-identiteit. Als u de eerste databasegebruiker wilt maken, moet u verbinding maken met de database met behulp van een Azure AD-beheerder (de eigenaar van de database). Dit wordt gedemonstreerd in [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse.](sql-database-aad-authentication-configure.md) Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Azure SQL Database of Azure Synapse. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory-gebruikers die eerder in SQL Server zijn gemaakt, geen verbinding meer maken met de database met behulp van hun Azure Active Directory-referenties.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-functies en -beperkingen

- De volgende leden van Azure AD kunnen worden ingericht in Azure SQL-server of Azure Synapse:

  - Native leden: een lid dat is gemaakt in Azure AD in het beheerde domein of in een klantdomein. Zie [Uw eigen domeinnaam toevoegen aan Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)voor meer informatie.
  - Leden van een Active Directory-domein met Azure Active Directory op een beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding met verificatie van doorgeef- of wachtwoordhash. Zie [Microsoft Azure ondersteunt nu federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) en Azure Active Directory Seamless Single [Sign-On](../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.
  - Geïmporteerde leden van andere Azure-AD's die native of federatieve domeinleden zijn.
  - Active Directory-groepen die zijn gemaakt als beveiligingsgroepen.

- Azure AD-gebruikers die deel uitmaken `db_owner` van een groep met serverrol kunnen de syntaxis VAN DE **[REFERENTIEVAN DE DATABASESCOPED MAKEN](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** niet gebruiken voor Azure SQL Database en Azure Synapse. U ziet de volgende fout:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Geef `db_owner` de rol rechtstreeks toe aan de afzonderlijke Azure AD-gebruiker om het probleem met de **ontgeldende referentieprobleem VOOR DE DATABASE MAKEN** te beperken.

- Met deze systeemfuncties worden NULL-waarden weergegeven wanneer deze worden uitgevoerd onder Azure AD-principals:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Beheerde exemplaren

- Azure AD-serverprincipals (aanmeldingen) en gebruikers worden ondersteund als voorbeeldfunctie voor [beheerde exemplaren.](sql-database-managed-instance.md)
- Azure AD-serverprincipals (aanmeldingen) instellen die zijn toegewezen aan een Azure AD-groep als database-eigenaar wordt niet ondersteund in [beheerde instanties](sql-database-managed-instance.md).
    - Een uitbreiding hiervan is dat wanneer een groep `dbcreator` wordt toegevoegd als onderdeel van de serverrol, gebruikers uit deze groep verbinding kunnen maken met de beheerde instantie en nieuwe databases kunnen maken, maar geen toegang hebben tot de database. Dit komt omdat de nieuwe database-eigenaar SA is en niet de Azure AD-gebruiker. Dit probleem manifesteert zich niet als `dbcreator` de individuele gebruiker wordt toegevoegd aan de serverrol.
- SQL Agent-beheer en uitvoering van taken worden ondersteund voor Azure AD-serverprincipals (aanmeldingen).
- Database-back-up en herstelbewerkingen kunnen worden uitgevoerd door Azure AD-server-principals (aanmeldingen).
- Controle van alle instructies met betrekking tot Azure AD-serverprincipals (aanmeldingen) en verificatiegebeurtenissen wordt ondersteund.
- Er wordt ondersteuning ondersteund voor een speciale beheerdersverbinding voor Azure AD-serverprincipals (aanmeldingen) die lid zijn van de sysadmin-serverrol.
    - Ondersteund via SQLCMD Utility en SQL Server Management Studio.
- Aanmeldingstriggers worden ondersteund voor aanmeldingsgebeurtenissen die afkomstig zijn van Azure AD-server-principal s(aanmeldingen).
- Service Broker en DB-mail kunnen worden ingesteld met behulp van een Azure AD-serverprincipal (login).


## <a name="connecting-using-azure-ad-identities"></a>Verbinding maken met Azure AD-identiteiten

Azure Active Directory-verificatie ondersteunt de volgende methoden om verbinding te maken met een database met Azure AD-identiteiten:

- Azure Active Directory-wachtwoord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA
- Verificatie van toepassingstoken gebruiken

De volgende verificatiemethoden worden ondersteund voor Azure AD-serverprincipals (aanmeldingen):

- Azure Active Directory-wachtwoord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA


### <a name="additional-considerations"></a>Aanvullende overwegingen

- Om de beheerbaarheid te verbeteren, raden we u aan een speciale Azure AD-groep in te richten als beheerder.   
- Slechts één Azure AD-beheerder (een gebruiker of groep) kan op elk gewenst moment worden geconfigureerd voor een Azure SQL Database-server of Azure Synapse.
  - De toevoeging van Azure AD-serverprincipals (aanmeldingen) voor beheerde exemplaren maakt het mogelijk om meerdere `sysadmin` Azure AD-serverprincipals (aanmeldingen) te maken die aan de rol kunnen worden toegevoegd.
- Alleen een Azure AD-beheerder voor SQL Server kan in eerste instantie verbinding maken met de Azure SQL Database-server, beheerde instantie of Azure Synapse met behulp van een Azure Active Directory-account. De Active Directory-beheerder kan volgende Azure AD-databasegebruikers configureren.   
- We raden u aan de time-out van de verbinding in te stellen op 30 seconden.   
- SQL Server 2016 Management Studio en SQL Server Data Tools voor Visual Studio 2015 (versie 14.0.60311.1 april 2016 of hoger) ondersteunen Azure Active Directory-verificatie. (Azure AD-verificatie wordt ondersteund door de **.NET Framework Data Provider voor SqlServer**; ten minste versie .NET Framework 4.6). Daarom de nieuwste versies van deze tools en data-tier toepassingen (DAC en . BACPAC) kan Azure AD-verificatie gebruiken.   
- Beginnend met versie 15.0.1 ondersteunen [sqlcmd utility](/sql/tools/sqlcmd-utility) en [bcp utility](/sql/tools/bcp-utility) Active Directory Interactive-authenticatie met MFA.
- SQL Server Data Tools voor Visual Studio 2015 vereist ten minste de april 2016-versie van de Data Tools (versie 14.0.60311.1). Momenteel worden Azure AD-gebruikers niet weergegeven in SSDT Object Explorer. Bekijk de gebruikers in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)als tijdelijke oplossing.   
- [Microsoft JDBC Driver 6.0 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ondersteunt Azure AD-verificatie. Zie Ook [de verbindingseigenschappen instellen](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase kan zich niet verifiëren met Azure AD-verificatie.   
- Azure AD-verificatie wordt voor SQL Database ondersteund door de azure-portal **Import Database** en **Export Database** blades. Importeren en exporteren met Azure AD-verificatie wordt ook ondersteund vanuit de opdracht PowerShell.   
- Azure AD-verificatie wordt ondersteund voor SQL Database, beheerde instantie en Azure Synapse met behulp van CLI. Zie [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse](sql-database-aad-authentication-configure.md) en [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Active Directory-verificatie configureren en beheren met SQL Database, beheer van beheerde instantie of Azure Synapse](sql-database-aad-authentication-configure.md)voor meer informatie over het maken en invullen van Azure AD en vervolgens Azure AD configureren met Azure SQL Database of Azure Synapse.
- Zie [Azure AD-serverprincipals (aanmeldingen) met beheerde instanties voor](sql-database-managed-instance-aad-security-tutorial.md) een zelfstudie met Azure AD-serverprincipals (aanmeldingen) met beheerde instanties
- Zie Aanmeldingen, gebruikers, databaserollen en machtigingen in SQL Database voor een overzicht van [aanmeldingen, gebruikers, databaserollen en machtigingen.](sql-database-manage-logins.md)
- Zie [Principals](https://msdn.microsoft.com/library/ms181127.aspx) voor meer informatie over database-principals.
- Zie [Databaserollen](https://msdn.microsoft.com/library/ms189121.aspx) voor meer informatie over databaserollen.
- Zie [AANMAKEN INLOGEN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)voor syntaxis voor het maken van Azure AD-serverprincipals (aanmeldingen) voor beheerde instanties.
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
