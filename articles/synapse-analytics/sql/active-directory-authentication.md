---
title: Azure Active Directory
description: Meer informatie over het gebruik van Azure Active Directory voor verificatie met SQL Database, Managed Instance en Synapse SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: db80c11c3b6eab3b7e682878e479729f4787a40b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086093"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Azure Active Directory Authentication gebruiken voor verificatie met Synapse SQL

Azure Active Directory-verificatie is een mechanisme om verbinding te maken met [Azure Synapse Analytics](../overview-faq.md) met behulp van identiteiten in Azure Active Directory (Azure AD).

Met Azure AD-verificatie u gebruikersidentiteiten die toegang hebben tot Azure Synapse centraal beheren om het machtigingsbeheer te vereenvoudigen. Dit biedt verschillende voordelen, zoals:

- Het biedt een alternatief voor regelmatige gebruikersnaam en wachtwoord authenticatie.
- Helpt de verspreiding van gebruikersidentiteiten tussen databaseservers te stoppen.
- Hiermee u wachtwoordrotatie op één plaats gebruiken.
- Klanten kunnen machtigingen beheren met externe (Azure AD)-groepen.
- Het kan het opslaan van wachtwoorden elimineren door geïntegreerde Windows-verificatie en andere vormen van verificatie in te schakelen die worden ondersteund door Azure Active Directory.
- Azure AD ondersteunt tokengebaseerde verificatie voor toepassingen die verbinding maken met Azure Synapse.
- Azure AD-verificatie ondersteunt ADFS -verificatie (domeinfederatie) of native user/password-verificatie voor een lokale Azure Active Directory zonder domeinsynchronisatie.
- Azure AD ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory Universal-verificatie, waaronder Multi-Factor Authentication (MFA).  MFA omvat robuuste verificatie met een scala aan gebruikersvriendelijke verificatieopties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app. Zie [SSMS-ondersteuning voor Azure AD MFA met Synapse SQL voor](mfa-authentication.md)meer informatie.
- Azure AD biedt ondersteuning voor vergelijkbare verbindingen van SQL Server Data Tools (SSDT) die gebruikmaken van Active Directory Interactive Authentication. Zie [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Ondersteuning van Azure Active Directory in SQL Server Data Tools (SSDT)) voor meer informatie.

De configuratiestappen omvatten de volgende procedures voor het configureren en gebruiken van Azure Active Directory-verificatie.

1. Azure AD maken en invullen.
2. Een Azure Active Directory-identiteit maken
3. Rol toewijzen aan gemaakte Azure Active Directory-identiteit in Synapse-werkruimte (voorbeeld)
4. Maak verbinding met Synapse Studio met Azure AD-identiteiten.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>AAD-doorloop in Azure Synapse Analytics

Met Azure Synapse Analytics heeft u toegang tot de gegevens in het gegevensmeer met uw Azure Active Directory-identiteit.

Het definiëren van toegangsrechten op de bestanden en gegevens die worden gerespecteerd in verschillende gegevensengines stelt u in staat om uw data lake-oplossingen te vereenvoudigen door één plaats te hebben waar de machtigingen worden gedefinieerd in plaats van ze op meerdere plaatsen te moeten definiëren.

## <a name="trust-architecture"></a>Vertrouwensarchitectuur

In het volgende diagram op hoog niveau wordt de oplossingsarchitectuur van het gebruik van Azure AD-verificatie met Synapse SQL samengevat. Als u het azure AD-native gebruikerswachtwoord wilt ondersteunen, wordt alleen het cloudgedeelte en Azure AD/Synapse Synapse SQL in aanmerking genomen. Om Federated-verificatie (of gebruiker/wachtwoord voor Windows-referenties) te ondersteunen, is de communicatie met ADFS-blokkering vereist. De pijlen geven communicatietrajecten aan.

![aad auth-diagram](./media/aad-authentication/1-active-directory-authentication-diagram.png)

In het volgende diagram worden de federatie-, vertrouwens- en hostingrelaties weergegeven waarmee een client verbinding kan maken met een database door een token in te dienen. Het token wordt geverifieerd door een Azure AD en wordt vertrouwd door de database. 

Klant 1 kan een Azure Active Directory vertegenwoordigen met native gebruikers of een Azure AD met federatieve gebruikers. Klant 2 vertegenwoordigt een mogelijke oplossing inclusief geïmporteerde gebruikers; in dit voorbeeld afkomstig van een gefedereerde Azure Active Directory met ADFS die wordt gesynchroniseerd met Azure Active Directory. 

Het is belangrijk om te begrijpen dat toegang tot een database met Azure AD-verificatie vereist dat het hostingabonnement is gekoppeld aan het Azure AD. Hetzelfde abonnement moet worden gebruikt om de SQL Server te maken die de Azure SQL Database of SQL-groep host.

![abonnementsrelatie](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Beheerdersstructuur

Bij het gebruik van Azure AD-verificatie zijn er twee administratoraccounts voor de Synapse SQL. de oorspronkelijke SQL Server-beheerder en de Azure AD-beheerder. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-databasegebruiker maken in een gebruikersdatabase. 

De aanmelding van Azure AD-beheerders kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groepsaccount is, kan het door elk groepslid worden gebruikt, waardoor meerdere Azure AD-beheerders voor de Synapse SQL-instantie worden inschakelen. 

Het gebruik van groepsaccount als beheerder verbetert de beheerbaarheid doordat u groepsleden centraal toevoegen en verwijderen in Azure AD zonder de gebruikers of machtigingen in de werkruimte Synapse Analytics te wijzigen. Slechts één Azure AD-beheerder (een gebruiker of groep) kan op elk gewenst moment worden geconfigureerd.

![beheerstructuur](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt `ALTER ANY USER` maken, moet u de toestemming in de database hebben. De `ALTER ANY USER` toestemming kan worden verleend aan elke databasegebruiker. De `ALTER ANY USER` machtiging is ook in het bezit van `CONTROL ON DATABASE` de `ALTER ON DATABASE` serverbeheerdersaccounts en databasegebruikers `db_owner` met de of toestemming voor die database en door leden van de databaserol.

Als u een opgenomen databasegebruiker wilt maken in Synapse SQL, moet u verbinding maken met de database of instantie met behulp van een Azure AD-identiteit. Als u de eerste databasegebruiker wilt maken, moet u verbinding maken met de database met behulp van een Azure AD-beheerder (de eigenaar van de database). 

Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Synapse SQL. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory-gebruikers die eerder in Synapse SQL zijn gemaakt, geen verbinding meer maken met de database met hun Azure Active Directory-referenties.
 
## <a name="azure-ad-features-and-limitations"></a>Azure AD-functies en -beperkingen

- De volgende leden van Azure AD kunnen worden ingericht in Synapse SQL:

  - Native leden: een lid dat is gemaakt in Azure AD in het beheerde domein of in een klantdomein. Zie [Uw eigen domeinnaam toevoegen aan Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.
  - Federatieve domeinleden: een lid dat is gemaakt in Azure AD met een federatief domein. Zie [Microsoft Azure ondersteunt nu federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)voor meer informatie.
  - Geïmporteerde leden van andere Azure-AD's die native of federatieve domeinleden zijn.
  - Active Directory-groepen die zijn gemaakt als beveiligingsgroepen.

- Azure AD-gebruikers die deel uitmaken `db_owner` van een groep met serverrol kunnen de syntaxis **[VAN DE REFERENTIEVAN DE DATABASESCOPED maken](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** niet gebruiken voor Synapse SQL. U ziet de volgende fout:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Geef `db_owner` de rol rechtstreeks toe aan de afzonderlijke Azure AD-gebruiker om het probleem met de **ontgeldende referentieprobleem VOOR DE DATABASE MAKEN** te beperken.

- Met deze systeemfuncties worden NULL-waarden weergegeven wanneer deze worden uitgevoerd onder Azure AD-principals:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Verbinding maken met Azure AD-identiteiten

Azure Active Directory-verificatie ondersteunt de volgende methoden om verbinding te maken met een database met Azure AD-identiteiten:

- Azure Active Directory-wachtwoord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA
- Verificatie van toepassingstoken gebruiken

De volgende verificatiemethoden worden ondersteund voor Azure AD-serverprincipals (aanmeldingen) (**openbare preview):**

- Azure Active Directory-wachtwoord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA

### <a name="additional-considerations"></a>Aanvullende overwegingen

- Om de beheerbaarheid te verbeteren, raden we u aan een speciale Azure AD-groep in te richten als beheerder.
- Slechts één Azure AD-beheerder (een gebruiker of groep) kan op elk gewenst moment worden geconfigureerd voor Synapse SQL-groep.
  - De toevoeging van Azure AD-serverprincipals (aanmeldingen) voor SQL on-demand (preview) maakt het mogelijk om meerdere `sysadmin` Azure AD-serverprincipals (aanmeldingen) te maken die aan de rol kunnen worden toegevoegd.
- Alleen een Azure AD-beheerder voor Synapse SQL kan in eerste instantie verbinding maken met de Synapse SQL met behulp van een Azure Active Directory-account. De Active Directory-beheerder kan volgende Azure AD-databasegebruikers configureren.
- We raden u aan de time-out van de verbinding in te stellen op 30 seconden.
- SQL Server 2016 Management Studio en SQL Server Data Tools voor Visual Studio 2015 (versie 14.0.60311.1 april 2016 of hoger) ondersteunen Azure Active Directory-verificatie. (Azure AD-verificatie wordt ondersteund door de **.NET Framework Data Provider voor SqlServer**; ten minste versie .NET Framework 4.6). Daarom de nieuwste versies van deze tools en data-tier toepassingen (DAC en . BACPAC) kan Azure AD-verificatie gebruiken.
- Beginnend met versie 15.0.1 ondersteunen [sqlcmd utility](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [bcp utility](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Active Directory Interactive-authenticatie met MFA.
- SQL Server Data Tools voor Visual Studio 2015 vereist ten minste de april 2016-versie van de Data Tools (versie 14.0.60311.1). Momenteel worden Azure AD-gebruikers niet weergegeven in SSDT Object Explorer. Bekijk de gebruikers in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)als tijdelijke oplossing.
- [Microsoft JDBC Driver 6.0 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ondersteunt Azure AD-verificatie. Zie Ook [de verbindingseigenschappen instellen](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Synapse SQL access control](../sql/access-control.md)voor een overzicht van toegang en controle in Synapse SQL.
- Zie [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie over database-principals.
- Zie [Databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie over databaserollen.

 