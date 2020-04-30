---
title: Azure Active Directory
description: Meer informatie over het gebruik van Azure Active Directory voor verificatie met SQL Database, een beheerd exemplaar en Synapse SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: db80c11c3b6eab3b7e682878e479729f4787a40b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82086093"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Azure Active Directory authenticatie gebruiken voor verificatie met Synapse SQL

Azure Active Directory-verificatie is een mechanisme voor het maken van verbinding met [Azure Synapse Analytics](../overview-faq.md) met behulp van identiteiten in azure Active Directory (Azure AD).

Met Azure AD-verificatie kunt u gebruikers identiteiten die toegang hebben tot Azure Synapse centraal beheren om het beheer van machtigingen te vereenvoudigen. Dit biedt verschillende voordelen, zoals:

- Het biedt een alternatief voor normale gebruikers naam-en wachtwoord verificatie.
- Helpt de verspreiding van gebruikers identiteiten over database servers te stoppen.
- Hiermee staat u het draaien van wacht woorden op één plek toe.
- Klanten kunnen machtigingen beheren via externe groepen (Azure AD).
- Hiermee kan worden voor komen dat wacht woorden worden opgeslagen door geïntegreerde Windows-verificatie en andere vormen van verificatie die door Azure Active Directory worden ondersteund, in te scha kelen.
- Azure AD biedt ondersteuning voor verificatie op basis van tokens voor toepassingen die verbinding maken met Azure Synapse.
- Azure AD-verificatie ondersteunt ADFS (Domain Federation) of systeem eigen gebruikers-en wachtwoord verificatie voor een lokale Azure Active Directory zonder domein synchronisatie.
- Azure AD ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory Universal-verificatie, waaronder Multi-Factor Authentication (MFA).  MFA omvat robuuste verificatie met een scala aan gebruikersvriendelijke verificatieopties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app. Zie [SSMS-ondersteuning voor Azure AD MFA met Synapse SQL](mfa-authentication.md)voor meer informatie.
- Azure AD biedt ondersteuning voor vergelijkbare verbindingen van SQL Server Data Tools (SSDT) die gebruikmaken van Active Directory Interactive Authentication. Zie [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Ondersteuning van Azure Active Directory in SQL Server Data Tools (SSDT)) voor meer informatie.

De configuratie stappen omvatten de volgende procedures voor het configureren en gebruiken van Azure Active Directory-verificatie.

1. Azure AD maken en vullen.
2. Een Azure Active Directory identiteit maken
3. Rol toewijzen aan gemaakte Azure Active Directory identiteit in Synapse-werk ruimte (preview)
4. Maak verbinding met Synapse Studio met behulp van Azure AD-identiteiten.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>AAD Pass-Through in azure Synapse Analytics

Met Azure Synapse Analytics kunt u toegang krijgen tot de gegevens in de data Lake met behulp van uw Azure Active Directory-identiteit.

Door toegangs rechten te definiëren voor de bestanden en gegevens die in verschillende data engines worden gerespecteerd, kan uw data Lake-oplossing worden vereenvoudigd door één plaats te hebben waar de machtigingen worden gedefinieerd, in plaats van dat ze op meerdere locaties moeten worden opgegeven.

## <a name="trust-architecture"></a>Vertrouwens architectuur

In het volgende diagram op hoog niveau wordt een overzicht gegeven van de oplossings architectuur van het gebruik van Azure AD-verificatie met Synapse SQL. Ter ondersteuning van het native gebruikers wachtwoord van Azure AD worden alleen het Cloud gedeelte en Azure AD/Synapse Synapse SQL beschouwd. Voor de ondersteuning van federatieve verificatie (of gebruiker/wacht woord voor Windows-referenties), is de communicatie met ADFS-blok vereist. De pijlen geven communicatie paden aan.

![Aad-verificatie diagram](./media/aad-authentication/1-active-directory-authentication-diagram.png)

In het volgende diagram worden de relaties Federatie, vertrouwen en hosting aangegeven waarmee een client verbinding kan maken met een Data Base door een token in te dienen. Het token wordt geverifieerd door een Azure AD en wordt vertrouwd door de data base. 

Klant 1 kan een Azure Active Directory met systeem eigen gebruikers of een Azure AD met federatieve gebruikers vertegenwoordigen. Klant 2 is een mogelijke oplossing met inbegrip van geïmporteerde gebruikers; in dit voor beeld afkomstig van een federatieve Azure Active Directory met ADFS gesynchroniseerd met Azure Active Directory. 

Het is belang rijk om te begrijpen dat toegang tot een Data Base met behulp van Azure AD-verificatie vereist dat het hosting-abonnement is gekoppeld aan Azure AD. Hetzelfde abonnement moet worden gebruikt voor het maken van de SQL Server die als host fungeert voor de Azure SQL Database of SQL-groep.

![abonnements relatie](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Beheerder structuur

Wanneer u Azure AD-verificatie gebruikt, zijn er twee beheerders accounts voor de Synapse SQL; de oorspronkelijke SQL Server beheerder en de Azure AD-beheerder. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-database gebruiker in een gebruikers database maken. 

De aanmelding van de Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groeps account is, kan dit worden gebruikt door elk groepslid om meerdere Azure AD-beheerders in te scha kelen voor het Synapse SQL-exemplaar. 

Het gebruik van groeps account als beheerder verbetert de beheer baarheid, zodat u groeps leden in azure AD centraal kunt toevoegen en verwijderen zonder de gebruikers of machtigingen in de Synapse Analytics-werk ruimte te wijzigen. Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd.

![beheerder structuur](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt maken, moet u `ALTER ANY USER` de machtiging hebben in de-data base. De `ALTER ANY USER` machtiging kan worden verleend aan elke database gebruiker. De `ALTER ANY USER` machtigingen worden ook opgeslagen door de beheerders accounts van de server en database gebruikers met `CONTROL ON DATABASE` de `ALTER ON DATABASE` machtiging of de machtigingen voor die data base, en `db_owner` door leden van de databaserol.

Als u een Inge sloten database gebruiker in Synapse SQL wilt maken, moet u verbinding maken met de data base of het exemplaar met behulp van een Azure AD-identiteit. Als u de eerste Inge sloten database gebruiker wilt maken, moet u verbinding maken met de data base met behulp van een Azure AD-beheerder (die de eigenaar van de data base is). 

Een Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Synapse SQL. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory gebruikers die eerder in Synapse SQL zijn gemaakt, geen verbinding meer maken met de data base met behulp van hun Azure Active Directory referenties.
 
## <a name="azure-ad-features-and-limitations"></a>Azure AD-functies en-beperkingen

- De volgende leden van Azure AD kunnen worden ingericht in Synapse SQL:

  - Systeem eigen leden: een lid dat is gemaakt in azure AD in het beheerde domein of in een klant domein. Zie [uw eigen domein naam toevoegen aan Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.
  - Federatieve domein leden: een lid dat is gemaakt in azure AD met een federatief domein. Zie [Microsoft Azure nu ondersteuning biedt voor Federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)voor meer informatie.
  - Geïmporteerde leden van andere Azure AD-personen die systeem eigen of federatieve domein leden zijn.
  - Active Directory groepen die zijn gemaakt als beveiligings groepen.

- Azure AD-gebruikers die deel uitmaken van een groep `db_owner` met serverrol, kunnen de syntaxis **[Create Data Base scoped CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** niet gebruiken voor Synapse SQL. U ziet de volgende fout:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Verleen `db_owner` de rol rechtstreeks aan de individuele Azure AD-gebruiker om het probleem met de **referentie Create Data Base scoped** te beperken.

- Deze systeem functies retour neren NULL-waarden wanneer ze worden uitgevoerd onder Azure AD-principals:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

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
- Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd voor Synapse SQL-pool.
  - Het toevoegen van Azure AD-server-principals (aanmeldingen) voor SQL on-demand (preview) biedt de mogelijkheid om meerdere Azure AD server-principals (aanmeldingen) te maken `sysadmin` die kunnen worden toegevoegd aan de rol.
- Alleen een Azure AD-beheerder voor Synapse SQL kan in eerste instantie verbinding maken met de Synapse SQL met een Azure Active Directory-account. De beheerder van de Active Directory kan nieuwe Azure AD-database gebruikers configureren.
- Het is raadzaam om de time-out voor de verbinding in te stellen op 30 seconden.
- SQL Server 2016 Management Studio en SQL Server Data Tools voor Visual Studio 2015 (versie 14.0.60311.1 2016 april of hoger) ondersteunen Azure Active Directory-verificatie. (Azure AD-verificatie wordt ondersteund door de **.NET Framework gegevens provider voor sqlserver**; ten minste versie .NET Framework 4,6). Daarom zijn de nieuwste versies van deze hulpprogram ma's en gegevenslaag toepassingen (DAC en. BACPAC) kan gebruikmaken van Azure AD-verificatie.
- Vanaf versie 15.0.1, [sqlcmd utility](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [bcp Utility](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ondersteunen Active Directory interactieve verificatie met MFA.
- SQL Server Data Tools voor Visual Studio 2015 vereist ten minste de versie van april 2016 van de gegevens Hulpprogramma's (versie 14.0.60311.1). Momenteel worden Azure AD-gebruikers niet weer gegeven in SSDT Objectverkenner. Als tijdelijke oplossing kunt u de gebruikers weer geven in [sys. database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- [Micro soft JDBC-stuur programma 6,0 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ondersteunt Azure AD-verificatie. Zie ook [de verbindings eigenschappen instellen](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Volgende stappen

- Zie voor een overzicht van toegang en beheer in Synapse SQL [Synapse SQL Access Control](../sql/access-control.md).
- Zie [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie over database-principals.
- Zie [Databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie over databaserollen.

 