---
title: Best practices voor beveiliging Playbook voor Azure SQL Database | Microsoft Docs
description: Dit artikel bevat algemene richt lijnen voor aanbevolen beveiligings procedures in Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 12/23/2019
ms.reviewer: ''
ms.openlocfilehash: f93ab61fcba53ebf39adf8ad56137f4a1df7d5fd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615030"
---
# <a name="azure-sql-database-security-best-practices-playbook"></a>Azure SQL Database best practices voor Playbook-beveiliging

## <a name="overview"></a>Overzicht

Dit document bevat richt lijnen voor het oplossen van algemene beveiligings vereisten voor nieuwe of bestaande toepassingen met behulp van Azure SQL Database. Het is ingedeeld op beveiligings gebieden op hoog niveau. Raadpleeg de sectie [veelvoorkomende beveiligings Risico's en mogelijke oplossingen](#common-security-threats-and-potential-mitigations) voor meer informatie over het adresseren van specifieke bedreigingen. Hoewel enkele van de gepresenteerde aanbevelingen van toepassing zijn bij het migreren van toepassingen van on-premises naar Azure, zijn migratie scenario's niet de focus van dit document.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Azure SQL Database implementatie aanbiedingen die in deze hand leiding worden behandeld

- [SQL-data bases](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): [afzonderlijke data bases](sql-database-single-database.md) en [elastische Pools](sql-database-elastic-pool.md) in [Azure SQL database servers](sql-database-servers.md)
- [Beheerde instanties](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>SQL-implementatie aanbiedingen die niet worden behandeld in deze hand leiding

- Azure SQL Data Warehouse
- Azure SQL-Vm's (IaaS)
- On-premises SQL Server

### <a name="audience"></a>Doelgroep

De beoogde doel groepen voor deze hand leiding zijn klanten die vragen hebben over het beveiligen van Azure SQL Database. De functies die zijn geïnteresseerd in dit best practice artikel zijn onder andere:

- Beveiligings architecten
- Beveiligings beheerders
- Nalevings ambtenaren
- Privacybeleid
- Beveiligings technici

### <a id="using"></a>Deze hand leiding gebruiken

Dit document is bedoeld als een aanvulling op onze bestaande [Azure SQL database Security](sql-database-security-overview.md) -documentatie.

Tenzij anders vermeld, raden we u aan alle aanbevolen procedures in elke sectie uit te voeren om het respectievelijke doel of vereiste te verzorgen. Om aan specifieke nalevings normen of aanbevolen procedures te voldoen, worden belang rijke compliance-nalevings controles vermeld in de sectie vereisten of doel stellingen, waar van toepassing. Dit zijn de beveiligings standaarden en-voor Schriften waarnaar in dit artikel wordt verwezen:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [Soc](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, crypto grafie
- [Micro soft Operational Security Assurance (OSA)-procedures](https://www.microsoft.com/en-us/securityengineering/osa/practices): Oefen #1-6 en #9
- [Specifieke NIST-publicatie 800-53 beveiligings controles](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Feedback

We gaan nu verder met het bijwerken van de aanbevelingen en aanbevolen procedures die hier worden vermeld. Geef een invoer of eventuele correcties voor dit document op met behulp van de **feedback** koppeling onder aan dit artikel.

## <a name="authentication"></a>Verificatie

Verificatie is het proces waarbij de gebruiker wordt geclaimd. Azure SQL Database ondersteunt twee typen verificatie:

- SQL-verificatie
- Azure Active Directory-authenticatie

### <a name="central-management-for-identities"></a>Centraal beheer voor identiteiten

Centraal identiteits beheer biedt de volgende voor delen:

- Groeps accounts beheren en gebruikers machtigingen controleren zonder dat aanmeldingen worden gedupliceerd over Azure SQL Database servers en data bases.
- Vereenvoudigd en flexibel machtigings beheer.
- Beheer van toepassingen op schaal.

**Implementeren**:

- Gebruik Azure Active Directory-verificatie (Azure AD) voor gecentraliseerd identiteits beheer.

**Aanbevolen procedures**:

- Maak een Azure AD-Tenant en [Maak gebruikers](../active-directory/fundamentals/add-users-azure-active-directory.md) voor het vertegenwoordigen van personen en het maken van [service-principals](../active-directory/develop/app-objects-and-service-principals.md) voor apps, services en hulpprogram ma's voor automatisering. Service-principals zijn gelijk aan service accounts in Windows en Linux. 

- Toegangs rechten toewijzen aan resources voor Azure AD-principals via groeps toewijzing: Azure AD-groepen maken, toegang verlenen aan groepen en afzonderlijke leden toevoegen aan de groepen. Maak in uw data base Inge sloten database gebruikers die uw Azure AD-groepen toewijzen. 
  - Zie de artikelen [Azure Active Directory verificatie met SQL configureren en beheren](sql-database-aad-authentication-configure.md) en [Azure AD gebruiken voor verificatie met SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > In een beheerd exemplaar kunt u ook aanmeldingen maken die worden toegewezen aan Azure AD-principals in de hoofd database. Zie [login maken (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Het gebruik van Azure AD-groepen vereenvoudigt het beheer van machtigingen en zowel de eigenaar van de groep als de eigenaar van de resource kan leden toevoegen aan of verwijderen uit de groep. 

- Maak een afzonderlijke groep voor Azure AD-Administrator voor SQL DB-servers.

  - Zie het artikel [een Azure Active Directory beheerder inrichten voor uw Azure SQL database-server](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Wijzigingen in azure AD-groepslid maatschap bewaken met Azure AD-controle activiteiten rapporten. 

- Voor een beheerd exemplaar is een afzonderlijke stap vereist voor het maken van een Azure AD-beheerder. 
  - Zie het artikel [een Azure Active Directory beheerder inrichten voor uw beheerde exemplaar](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - Azure AD-verificatie wordt vastgelegd in Azure SQL-audit logboeken, maar niet in Logboeken van Azure AD-aanmelding.
> - RBAC-machtigingen die in Azure worden verleend, zijn niet van toepassing op Azure SQL DB-machtigingen. Dergelijke machtigingen moeten hand matig worden gemaakt/toegewezen in SQL DB met behulp van bestaande SQL-machtigingen.
> - Aan de client zijde van Azure AD-verificatie moet toegang tot internet of via door de gebruiker gedefinieerde route (UDR) worden gemaakt voor een VNet.
> - Het Azure AD-toegangs token wordt opgeslagen in de cache aan de client zijde en de levens duur is afhankelijk van de configuratie van het token. Zie het artikel [levens duur van Configureer bare tokens in azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> [!NOTE]
> Vermeld in: OSA-Oefen #2, ISO-Access Control (AC)

Azure Multi-Factor Authentication (MFA) helpt extra beveiliging te bieden doordat er meer dan één vorm van verificatie is vereist.

**Implementeren**:

- [Schakel MFA](../active-directory/authentication/concept-mfa-howitworks.md) in azure AD in met behulp van voorwaardelijke toegang en gebruik interactieve verificatie. 

- Het alternatief is MFA in te scha kelen voor het hele Azure AD-of AD-domein.

**Aanbevolen procedures**:

- Voorwaardelijke toegang activeren in azure AD (Premium-abonnement vereist). 
  - Zie het artikel [voorwaardelijke toegang in azure AD](../active-directory/conditional-access/overview.md).  

- Maak een Azure AD-groep (en) en schakel MFA-beleid in voor geselecteerde groepen met behulp van voorwaardelijke toegang voor Azure AD. 
  - Zie het artikel de [implementatie van voorwaardelijke toegang plannen](../active-directory/conditional-access/plan-conditional-access.md). 

- MFA kan worden ingeschakeld voor de hele Azure AD of voor de hele Active Directory federatieve met Azure AD. 

- Interactieve verificatie modus van Azure AD gebruiken voor SQL data base waarbij een wacht woord interactief wordt aangevraagd, gevolgd door MFA-verificatie:      
  - Gebruik universele verificatie in SSMS. Zie het artikel [using multi-factor Aad-verificatie met Azure SQL database en Azure SQL Data Warehouse (SSMS-ondersteuning voor MFA)](sql-database-ssms-mfa-authentication.md).
  - Interactieve verificatie gebruiken die wordt ondersteund in SQL Server Data Tools (SSDT). Zie het artikel [Azure Active Directory ondersteuning in SQL Server Data tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Gebruik andere SQL-hulpprogram ma's die MFA ondersteunen. 
    - Ondersteuning van de wizard SSMS voor het exporteren/extra heren/implementeren van de data base  
    - [sqlpackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage): optie '/UA ' 
    - [Sqlcmd-hulp programma](https://docs.microsoft.com/sql/tools/sqlcmd-utility): Option-G (interactief)
    - [bcp-hulp programma](https://docs.microsoft.com/sql/tools/bcp-utility): Option-G (interactief) 

- Implementeer uw toepassingen om verbinding te maken met Azure SQL Database met behulp van interactieve verificatie met MFA-ondersteuning. 
  - Zie het artikel [verbinding maken met Azure SQL database met Azure multi-factor Authentication](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Deze verificatie modus vereist identiteiten op basis van gebruikers. In gevallen waarin een vertrouwd identiteits model wordt gebruikt dat individuele gebruikers verificatie van Azure AD overs Laan (bijvoorbeeld door beheerde identiteiten te gebruiken voor Azure-resources), is MFA niet van toepassing.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Het gebruik van verificatie op basis van wacht woorden minimaliseren voor gebruikers

> [!NOTE]
> Vermeld in: OSA-oefen #4, ISO-Access Control (AC)

Verificatie methoden op basis van wacht woorden zijn een zwakkere vorm van verificatie. Referenties kunnen worden aangetast of per ongeluk worden verwijderd.

**Implementeren**:

- Gebruik een geïntegreerde Azure AD-verificatie waarmee het gebruik van wacht woorden wordt voor komen.

**Aanbevolen procedures**:

- Gebruik verificatie op basis van eenmalige aanmelding met Windows-referenties. Verdeel het on-premises AD-domein met Azure AD en gebruik geïntegreerde Windows-verificatie (voor computers die lid zijn van een domein met Azure AD).
  - Zie het artikel [SSMS-ondersteuning voor Azure AD Integrated-verificatie](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Het gebruik van verificatie op basis van wacht woorden minimaliseren voor toepassingen 

> [!NOTE]
> Vermeld in: OSA-oefen #4, ISO-Access Control (AC)

**Implementeren**:

- Hiermee wordt beheerde identiteit van Azure ingeschakeld. U kunt ook geïntegreerde of op certificaten gebaseerde verificatie gebruiken. 

**Aanbevolen procedures**:

- [Beheerde identiteiten gebruiken voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).
  - [Door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Door de gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Azure SQL Database van app service met beheerde identiteit gebruiken (zonder code wijzigingen)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Authenticatie op basis van certificaten voor een toepassing gebruiken. 
  - Zie dit [code voorbeeld](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Gebruik Azure AD-verificatie voor geïntegreerde federatieve domein-en domein computers (zie bovenstaande sectie).
  - Zie de [voorbeeld toepassing voor geïntegreerde verificatie](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Wacht woorden en geheimen beveiligen

Als wacht woorden niet kunnen worden voor komen, moet u ervoor zorgen dat deze zijn beveiligd.

**Implementeren**:

- Gebruik Azure Key Vault om wacht woorden en geheimen op te slaan. Gebruik MFA voor Azure SQL Database met Azure AD-gebruikers, indien van toepassing.

**Aanbevolen procedures**:

- Als u geen wacht woorden of geheimen kunt voor komen, slaat u gebruikers wachtwoorden en toepassings geheimen op in Azure Key Vault en beheert u de toegang via Key Vault toegangs beleid. 

- Verschillende frameworks voor het ontwikkelen van apps bieden mogelijk ook Framework-specifieke mechanismen voor het beveiligen van geheimen in de app. Bijvoorbeeld: [ASP.net core-app](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>SQL-verificatie gebruiken voor oudere toepassingen 

SQL-verificatie verwijst naar de verificatie van een gebruiker bij het verbinden met Azure SQL Database met behulp van de gebruikers naam en het wacht woord. Voor elke SQL Database-Server of een beheerd exemplaar moet een aanmelding worden gemaakt en een gebruiker die in elke Data Base is gemaakt.

**Implementeren**:

- Gebruik SQL-verificatie.

**Aanbevolen procedures**:

- Als Server beheerder maakt u aanmeldingen en gebruikers. Tenzij u Inge sloten database gebruikers met wacht woorden gebruikt, worden alle wacht woorden opgeslagen in de data base Master.
  - Zie het artikel een [database toegang beheren en verlenen aan SQL database en SQL Data Warehouse](sql-database-manage-logins.md).

- Volg de aanbevolen procedures voor wachtwoord beheer:
  - Geef een complex wacht woord op, bestaande uit Latijnse hoofd-en kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (zoals $,!, # of%).
  - Gebruik langere wachtwoord zinnen in plaats van minder wille keurig geselecteerde tekens.
  - Hand matig wijzigen van het wacht woord afdwingen ten minste elke 90 dagen.

## <a name="access-management"></a>Toegangsbeheer

Toegangs beheer is het proces van het beheren en beheren van de toegang en bevoegdheden van geautoriseerde gebruikers tot Azure SQL Database.

### <a name="implement-principle-of-least-privilege"></a>Het principe van minimale bevoegdheden implementeren

> [!NOTE]
> Vermeld in: FedRamp Controls AC-06, NIST: AC-6, OSA Practice #3

Het principe van de minimale bevoegdheid geeft aan dat gebruikers niet meer bevoegdheden moeten hebben dan nodig zijn om hun taken uit te voeren. Zie het artikel [net genoeg beheer](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)voor meer informatie.

**Implementeren**:

Wijs alleen de benodigde [machtigingen](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) toe om de vereiste taken uit te voeren:

- In SQL data-vlak: 
    - Gedetailleerde machtigingen en door de gebruiker gedefinieerde database rollen (of Server-rollen in MI) gebruiken: 
        1. De vereiste rollen maken
            - [ROL MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [SERVERROL MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Vereiste gebruikers maken
            - [GEBRUIKER MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Gebruikers als leden toevoegen aan rollen 
            - [ROL WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [SERVER FUNCTIE WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Wijs vervolgens machtigingen toe aan rollen. 
            - [Geef](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Zorg ervoor dat u geen gebruikers toewijst aan overbodige rollen.

- In Azure Resource Manager:
  - Gebruik Builtin-roles als beschikbaar of aangepast RBAC-rollen en wijs de benodigde machtigingen toe.
    - [Ingebouwde rollen voor Azure](../role-based-access-control/built-in-roles.md) 
    - [Aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)

**Aanbevolen procedures**:

De volgende aanbevolen procedures zijn optioneel, maar leiden tot betere beheersbaarheid en ondersteuning van uw beveiligings strategie: 

- Als dat mogelijk is, begint u met de minst mogelijke set machtigingen en begint u met het toevoegen van machtigingen één voor één als er sprake is van een echte nood zaak (en motivering), in tegens telling tot de tegenovergestelde benadering: de stap voor het maken van machtigingen. 

- U hoeft geen machtigingen voor afzonderlijke gebruikers toe te wijzen. Gebruik in plaats daarvan functies (Data Base-of Server functies). Rollen helpen de machtigingen voor rapportage en probleem oplossing aanzienlijk te verbeteren. (Azure RBAC ondersteunt alleen machtigingen toewijzing via rollen.) 

- Gebruik ingebouwde rollen wanneer de machtigingen van de rollen overeenkomen met de vereiste machtigingen voor de gebruiker. U kunt gebruikers toewijzen aan meerdere rollen. 

- Aangepaste rollen maken en gebruiken wanneer ingebouwde rollen te veel of onvoldoende machtigingen verlenen. Typische rollen die in de praktijk worden gebruikt: 
  - Beveiligings implementatie 
  - Beheerder 
  - Developer 
  - Ondersteunings personeel 
  - Auditor 
  - Geautomatiseerde processen 
  - Eindgebruiker 

- Houd er rekening mee dat machtigingen in SQL Server data base-engine kunnen worden toegepast op de volgende bereiken. Hoe kleiner het bereik, hoe kleiner de impact van de verleende machtigingen: 
  - Azure SQL Database-Server (speciale rollen in de hoofd database) 
  - Database 
  - Schema (Zie ook: [schema-ontwerp voor SQL Server: aanbevelingen voor schema ontwerp met beveiliging in het oog](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Object (tabel, weer gave, procedure, enz.) 
  > [!NOTE]
  > Het is niet raadzaam om machtigingen op het object niveau toe te passen, omdat dit niveau onnodig ingewikkeld wordt toegevoegd aan de algemene implementatie. Als u besluit machtigingen op object niveau te gebruiken, moeten deze duidelijk worden gedocumenteerd. Hetzelfde geldt voor machtigingen op kolom niveau, die voor dezelfde redenen nog minder worden aanbevolen. De standaard regels voor [weigeren](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) zijn niet van toepassing op kolommen.

- Voer regel matig controles uit met behulp van [evaluatie van beveiligings problemen (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) om te controleren of er te veel machtigingen zijn.

### <a name="implement-separation-of-duties"></a>Schei ding van taken implementeren

> [!NOTE]
> Vermeld in: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Schei ding van taken, ook wel schei ding van taken genoemd, beschrijft de vereiste om gevoelige taken te splitsen in meerdere taken die aan verschillende gebruikers worden toegewezen, meestal om te voor komen dat er gegevens worden geschonden.

**Implementeren**:

- Bepaal het vereiste niveau van schei ding van taken. Voorbeelden: 
  - Tussen ontwikkel-en test-en productie omgevingen 
  - Beveiligings beleid gevoelige taken versus taken van het beheer niveau van de database beheerder (DBA) versus ontwikkelaars taken. 
    - Voor beelden: auditor, het maken van beveiligings beleid voor beveiliging op rol niveau, het implementeren van SQL Database objecten met DDL-machtigingen.

- Identificeer een uitgebreide hiërarchie van gebruikers (en geautomatiseerde processen) die toegang hebben tot het systeem.

- Rollen maken op basis van de benodigde gebruikers groepen en machtigingen toewijzen aan rollen. 
  - Voor taken op beheer niveau in Azure Portal of via Power shell-Automation kunt u RBAC-rollen gebruiken. Zoek een ingebouwde rol die overeenkomt met de vereiste of maak een aangepaste RBAC-rol met behulp van de beschik bare machtigingen 
  - Server functies maken voor Server-brede taken (het maken van nieuwe aanmeldingen, data bases) in een beheerd exemplaar. 
  - Maak database rollen voor taken op database niveau.

- Voor bepaalde gevoelige taken kunt u speciale opgeslagen procedures maken die zijn ondertekend door een certificaat om de taken namens de gebruikers uit te voeren. 
  - Voor beeld: [zelf studie: opgeslagen procedures ondertekenen met een certificaat](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementeer Transparent Data Encryption (TDE) met door de klant beheerde sleutels in Azure Key Vault om schei ding van taken mogelijk te maken tussen de eigenaar van de gegevens en de eigenaar van de beveiliging. 
  - Zie het artikel een door [de klant beheerde sleutels configureren voor Azure Storage versleuteling van de Azure Portal](../storage/common/storage-encryption-keys-portal.md). 

- Om ervoor te zorgen dat een DBA geen gegevens kan zien die als zeer gevoelig worden beschouwd en nog steeds DBA-taken kunnen uitvoeren, kunt u Always Encrypted gebruiken met functie scheiding. 
  - Zie de artikelen, [overzicht van sleutel beheer voor always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), het [inrichten van sleutels met functie scheiding](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)en het [draaien van kolom hoofd sleutels met functie scheiding](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- In gevallen waarin het niet haalbaar is om ten minste geen grote kosten en inspanningen te geven die het systeem in de buurt van onbruikbaar kunnen genereren, kunnen er inbreuken worden gemaakt en verholpen door het gebruik van compenserende controles, zoals: 
  - Menselijke tussen komst in processen. 
  - Controle spoor – Zie [essentiële beveiligings gebeurtenissen controleren](#audit-critical-security-events)voor meer informatie over de controle.

**Aanbevolen procedures**:

- Zorg ervoor dat er verschillende accounts worden gebruikt voor ontwikkelings-en test-en productie omgevingen. Verschillende accounts helpen te voldoen aan de schei ding van test & productie systemen.

- U hoeft geen machtigingen voor afzonderlijke gebruikers toe te wijzen. Gebruik in plaats daarvan functies (Data Base-of Server functies). Met rollen kunt u de machtigingen voor rapportage en probleem oplossing aanzienlijk verbeteren.

- Gebruik ingebouwde rollen wanneer de machtigingen precies overeenkomen met de benodigde machtigingen. als de samen voeging van alle machtigingen van meerdere ingebouwde rollen leidt tot een overeenkomst van 100%, kunt u ook meerdere rollen tegelijk toewijzen. 

- Aangepaste rollen maken en gebruiken wanneer ingebouwde rollen te veel machtigingen of onvoldoende machtigingen verlenen. 

- Roltoewijzingen kunnen ook tijdelijk worden uitgevoerd, ook wel bekend als dynamische schei ding van taken (DSD), binnen SQL Agent-taak stappen in T-SQL of met behulp van Azure PIM voor RBAC-rollen. 

- Zorg ervoor dat Dba's geen toegang hebben tot de versleutelings sleutels of sleutel archieven en dat beveiligings beheerders met toegang tot de sleutels op zijn beurt geen toegang tot de data base hebben. 

- Zorg ervoor dat u altijd een audittrail voor beveiligings acties hebt. 

- U kunt de definitie van de ingebouwde RBAC-rollen ophalen om te zien welke machtigingen worden gebruikt en een aangepaste rol maken op basis van fragmenten en cumulaties van deze via Power shell 

- Omdat elk lid van de databaserol db_owner beveiligings instellingen kan wijzigen, zoals Transparent Data Encryption (TDE), of de SLO kan wijzigen, moet dit lidmaatschap worden verleend. Aan de andere kant hebben veel taken, zoals het wijzigen van een Data Base-instelling, zoals het wijzigen van de database opties, db_owner bevoegdheden nodig. Controle speelt een belang rijke rol in een oplossing.

- Het is niet mogelijk om een db_owner gebruikers gegevens alleen met machtigingen te laten weer geven. Als er zeer gevoelige gegevens in een Data Base zijn, kunnen Always Encrypted worden gebruikt om te voor komen dat db_owners of een andere DBA deze weergeeft.

> [!NOTE]
> Het bereiken van een schei ding van taken (SoD) is lastig voor beveiligings-of probleemoplossings taken. Andere gebieden, zoals ontwikkel-en eindgebruikers rollen, zijn gemakkelijker te scheiden. De meeste aan naleving gerelateerde besturings elementen staan het gebruik van alternatieve besturings functies, zoals controle, toe als andere oplossingen niet praktisch zijn.

Voor de lezers die dieper willen zijn in SoD, raden we de volgende bronnen aan: 

- Voor Azure SQL Database:  
  - [Database toegang beheren en verlenen aan SQL Database en SQL Data Warehouse](sql-database-manage-logins.md)
  - [Schei ding van functies voor de toepassings ontwikkelaar](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Schei ding van taken in SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Opgeslagen procedures ondertekenen in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Voor Azure resource management:
  - [Ingebouwde rollen voor Azure](../role-based-access-control/built-in-roles.md) 
  - [Aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)
  - [Azure AD Privileged Identity Management gebruiken voor verhoogde toegang](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Reguliere code beoordelingen uitvoeren

> [!NOTE]
> Vermeld in: PCI: 6.3.2, SOC: SDL-3 

Schei ding van taken is niet beperkt tot de gegevens in de data base, maar bevat toepassings code. Schadelijke code kan beveiligings controles mogelijk omzeilen. Voordat u aangepaste code naar productie implementeert, is het essentieel om te controleren wat er wordt geïmplementeerd.

**Implementeren**:

- Gebruik een Data Base-hulp programma zoals Azure Data Studio dat broncode beheer ondersteunt. 

- Implementeer een proces voor het implementeren van een schei ding code.

- Voordat de hoofd vertakking wordt doorgevoerd, moet een persoon (met uitzonde ring van de auteur van de code zelf) de code controleren op mogelijke verhoging van de bevoegdheids Risico's en kwaad aardige gegevens wijzigingen om te beschermen tegen fraude en Rogue-toegang. Dit kan worden gedaan met behulp van broncode beheer mechanismen.

**Aanbevolen procedures**:

- Standardization: het helpt bij het implementeren van een standaard procedure die moet worden gevolgd voor code-updates. 

- De evaluatie van beveiligings problemen bevat regels die controleren op overmatige machtigingen, het gebruik van oude versleutelings algoritmen en andere beveiligings Risico's binnen een database schema. 

- Verdere controles kunnen worden uitgevoerd in een QA-of test omgeving met behulp van geavanceerde beveiliging tegen bedreigingen waarmee wordt gescand op code die kwetsbaar is voor SQL-injectie.

- Voor beelden van wat er moet worden gezocht voor: 
  - Het maken van een gebruiker of het wijzigen van de beveiligings instellingen vanuit een automatische SQL-code-update-implementatie. 
  - Een opgeslagen procedure, die, afhankelijk van de opgegeven para meters, een geld waarde in een cel in een niet-conforme manier bijwerkt. 

- Zorg ervoor dat degene die de beoordeling uitvoert, een andere persoon is dan de oorspronkelijke code auteur en kennis in code beoordelingen en veilige code ring.

- Zorg ervoor dat u alle bron code-wijzigingen kent: code kan zich in T-SQL-scripts bevinden. Dit kan ad-hoc opdrachten zijn om uit te voeren of te worden geïmplementeerd in de vorm van weer gaven, functies, triggers en opgeslagen procedures. Het kan ook deel uitmaken van SQL Agent-taak definities (stappen), worden uitgevoerd vanuit SSIS-pakketten, Azure Data Factory en andere services.

## <a name="data-protection"></a>Databeveiliging

Gegevens beveiliging is een reeks mogelijkheden voor het beveiligen van belang rijke informatie tegen inbreuk door versleuteling of het afwijzen van een schijf.

> [!NOTE]
> Micro soft certificeert Azure SQL Database als compatibel met FIPS 140-2 level 1. Dit wordt gedaan na het controleren van het strikte gebruik van FIPS 140-2 level 1 aanvaard bare algoritmen en FIPS 140-2 level 1 gevalideerde instanties van deze algoritmen, inclusief consistentie met vereiste sleutel lengten, sleutel beheer, sleutel genereren en sleutel opslag. Deze verklaring is bedoeld om onze klanten in staat te stellen te voldoen aan de behoeften of vereisten voor het gebruik van gevalideerde FIPS 140-2 level 1-exemplaren in de verwerking van gegevens of levering van systemen of toepassingen. We definiëren de termen ' compatibel met FIPS 140-2 level 1 ' en ' FIPS 140-2 level 1-naleving ' die in de bovenstaande verklaring worden gebruikt om de beoogde toepas baarheid te demonstreren voor het Amerikaanse en het Canadese overheids gebruik van de verschillende term ' FIPS 140-2 level 1 Validated '. 


### <a name="encrypt-data-in-transit"></a>Gegevens in transit versleutelen

> [!NOTE]
> Vermeld in: OSA-Oefen #6, ISO-controle groep: crypto grafie

Beveiligt uw gegevens terwijl gegevens tussen uw client en server worden verplaatst. Raadpleeg de [netwerk beveiliging](#network-security).

### <a name="encrypt-data-at-rest"></a>Data-at-rest versleutelen

> [!NOTE]
> Vermeld in: OSA-Oefen #6, ISO-controle groep: crypto grafie

Versleuteling op rest is de cryptografische beveiliging van gegevens wanneer deze persistent is in de data base, het logboek en de back-upbestanden.

**Implementeren**:

- [Transparent data base Encryption (TDE)](transparent-data-encryption-azure-sql.md) met door service beheerde sleutels zijn standaard ingeschakeld voor data bases die zijn gemaakt na 2017 in Azure SQL database.
- Als de data base wordt gemaakt als gevolg van een herstel bewerking van een on-premises server, wordt in een beheerd exemplaar de TDE-instelling van de oorspronkelijke data base nageleefd. Als TDE niet is ingeschakeld voor de oorspronkelijke Data Base, is het raadzaam om TDE hand matig in te scha kelen voor het beheerde exemplaar.

**Aanbevolen procedures**:

- Sla geen gegevens op waarvoor versleuteling is vereist in de data base Master. De hoofd database kan niet worden versleuteld met TDE.

- Gebruik door de klant beheerde sleutels in Azure Key Vault als u meer transparantie en gedetailleerde controle over de TDE-beveiliging nodig hebt. Met Azure Key Vault kunt u op elk gewenst moment machtigingen intrekken om de data base niet toegankelijk te maken. U kunt TDE-beveiligingen samen met andere sleutels centraal beheren of de TDE-protector op uw eigen schema draaien met behulp van Azure Key Vault.

- Als u door de klant beheerde sleutels gebruikt in Azure Key Vault, volgt u de artikelen, [richt lijnen voor het configureren van TDe met Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) en [het configureren van geo-Dr met Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Beschermen van gevoelige gegevens die worden gebruikt door gebruikers met een hoge privilege en zonder toestemming

Gegevens in gebruik zijn de gegevens die in het geheugen van het database systeem zijn opgeslagen tijdens de uitvoering van SQL-query's. Als uw data base gevoelige gegevens opslaat, kan uw organisatie verplicht zijn om ervoor te zorgen dat gebruikers met verhoogde bevoegdheden, zoals micro soft-Opera tors of Dba's in uw organisatie, niet in staat zijn om de gegevens te extra heren uit het geheugen van het SQL Server proces en niet de Lees bare gegevens weer geven tijdens het uitvoeren van een query op de data base.

**Implementeren**:

- Gebruik [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) om ervoor te zorgen dat gevoelige gegevens niet worden weer gegeven in niet-gecodeerde tekst in Azure SQL database, zelfs in het geheugen/in gebruik. Dit beveiligt de gegevens van database beheerders (Dba's) en Cloud beheerders (of ongeldige Actors die hoge privileges, maar niet-gemachtigde gebruikers kunnen imiteren) en geeft u meer controle over wie toegang heeft tot uw gegevens.

**Aanbevolen procedures**:

- Always Encrypted is geen vervanging voor het versleutelen van gegevens in rust (TDE) of in transit (SSL/TLS), en mag niet worden gebruikt voor niet-gevoelige gegevens om de prestaties en functionaliteits impact te minimaliseren. Het gebruik van Always Encrypted in combi natie met TDE en TLS wordt aanbevolen voor een uitgebreide beveiliging van gegevens die onderweg zijn, in-transit en in gebruik. 

- Beheer Always Encrypted sleutels met functie scheiding als u Always Encrypted gebruikt voor het beveiligen van gegevens van kwaad aardige Dba's. Met functie scheiding maakt een beveiligings beheerder de fysieke sleutels en een DBA maakt kolom hoofd sleutel en meta gegevens objecten van de kolom versleutelings sleutel, met een beschrijving van de fysieke sleutels in de-data base. Tijdens dit proces heeft de beveiligings beheerder geen toegang nodig tot de data base. de DBA heeft geen toegang tot de fysieke sleutels nodig als tekst zonder opmaak. 
  - Zie het artikel [sleutels beheren met functie scheiding](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) voor meer informatie. 

- Sla de kolom hoofd sleutels op in Azure Key Vault voor eenvoudig beheer. Vermijd het gebruik van Windows-certificaat archief (en algemene oplossingen voor gedistribueerd sleutel archief, in tegens telling tot centrale oplossingen voor het beheer van sleutels) waarmee sleutel beheer moeilijk wordt. 

- Denk goed na over de nadelen van het gebruik van meerdere sleutels (kolom hoofd sleutel of kolom versleutelings sleutels). Bewaar het aantal kleine toetsen om de kosten van het sleutel beheer te reduceren. Eén kolom hoofd sleutel en één kolom versleutelings sleutel per data base is doorgaans voldoende in de stationaire omgevingen (niet in het midden van een sleutel rotatie), tenzij u verschillende gebruikers groepen hebt, elk met verschillende sleutels en toegang tot verschillende gegevens.  

- Draai kolom hoofd sleutels volgens uw nalevings vereisten. Als u ook de kolom versleutelings sleutels wilt draaien, kunt u online versleuteling gebruiken om de uitval tijd van toepassingen te minimaliseren. 
  - Zie het artikel, [overwegingen met betrekking tot prestaties en beschik baarheid](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Gebruik deterministische versleuteling als berekeningen (gelijkheid) op gegevens moeten worden ondersteund. Anders gebruikt u wille keurige code ring. Vermijd het gebruik van deterministische versleuteling voor gegevens sets met een lage entropie of gegevens sets met openbaar bekende distributie. 

- Als u zich zorgen maakt over de toegang van uw gegevens door derden zonder uw toestemming, moet u ervoor zorgen dat alle toepassingen en hulpprogram ma's die toegang hebben tot de sleutels en gegevens in ongecodeerde tekst, buiten Microsoft Azure Cloud worden uitgevoerd. Zonder toegang tot de sleutels heeft de derde partij geen enkele manier om de gegevens te ontsleutelen, tenzij ze de versleuteling omzeilen.

- Always Encrypted biedt geen ondersteuning voor het verlenen van tijdelijke toegang tot de sleutels (en de beveiligde gegevens). Als u bijvoorbeeld de sleutels met een DBA wilt delen, zodat de DBA enkele opschoon bewerkingen kan uitvoeren op gevoelige en versleutelde gegevens, is de enige manier om de toegang tot de gegevens van de DBA te intrekken, om zowel de kolom versleutelings sleutels als de colu te draaien. MN hoofd sleutels beveiligen de gegevens, wat een dure bewerking is. 

- Om toegang te krijgen tot de ongecodeerde waarden in versleutelde kolommen, moet een gebruiker toegang hebben tot de CMK en de kolommen beveiligen, die zijn geconfigureerd in de sleutel opslag met de CMK. Daarnaast moet de gebruiker de definitie van een kolom hoofd sleutel weer geven en alle database machtigingen voor een kolom versleutelings sleutel weer geven.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>De toegang tot gevoelige gegevens via versleuteling beheren met toepassings gebruikers

Versleuteling kan worden gebruikt als een manier om ervoor te zorgen dat alleen specifieke toepassings gebruikers die toegang hebben tot cryptografische sleutels en de gegevens kunnen weer geven of bijwerken.

**Implementeren**:

- Gebruik versleuteling op mapniveau (CLE). Zie het artikel [een kolom met gegevens versleutelen](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) voor meer informatie. 
- U kunt ook Always Encrypted gebruiken, maar houd rekening met de beperkingen die hieronder worden weer gegeven.

**Aanbevolen procedures**

Wanneer u CLE gebruikt:

- Toegang tot sleutels beheren via SQL-machtigingen en-rollen. 

- Gebruik AES (AES 256 aanbevolen) voor gegevens versleuteling. Algoritmen, zoals RC4, DES en TripleDES, zijn afgeschaft en mogen niet worden gebruikt vanwege bekende beveiligings problemen. 

- Beveilig symmetrische sleutels met asymmetrische sleutels/certificaten (geen wacht woorden) om te voor komen dat 3DES wordt gebruikt. 

- Wees voorzichtig bij het migreren van een Data Base met behulp van versleuteling op basis van een cel-niveau via exporteren/importeren (Bacpac-bestanden). 
  - Zie het artikel [aanbevelingen voor het gebruik van versleuteling op basis van een mapniveau in Azure SQL database](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) over het voor komen van verbroken-sleutels bij het migreren van gegevens en voor andere best practice richt lijnen.

Wanneer u Always Encrypted gebruikt, moet u er rekening mee gehouden dat Always Encrypted voornamelijk is ontworpen voor het beveiligen van gevoelige gegevens die worden gebruikt door gebruikers met een hoge bevoegdheid van Azure SQL Database (Cloud operators, Dba's). Zie [gevoelige gegevens beveiligen in gebruik van met hoge bevoegdheden onbevoegde gebruikers](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Houd rekening met de volgende uitdagingen wanneer u Always Encrypted gebruikt voor het beveiligen van gegevens van toepassings gebruikers:

- Zodra u een gebruiker toegang verleent tot gevoelige gegevens door de gebruikers machtigingen te geven voor het verkrijgen van toegang tot de kolom versleutelings sleutel en de kolom hoofd sleutel, kunt u de kolom versleutelings sleutel alleen op een betrouw bare wijze intrekken, wat een dure bewerking is die vereist dat alle kolommen opnieuw versleutelen, de kolom versleutelings sleutel wordt beveiligd. 

- Standaard worden alle micro soft-client Stuur Programma's die Always Encrypted ondersteunen, een globaal (één per toepassing) cache van kolom versleutelings sleutels onderhouden. Wanneer een client stuur programma een versleutelings sleutel voor een lees bare kolom verkrijgt door contact op te nemen met een sleutel archief met een kolom hoofd sleutel, wordt de versleutelings sleutel voor de Lees bare kolom in de cache opgeslagen, waardoor het isoleren van gegevens van gebruikers van een toepassing voor meerdere gebruikers lastig is. Als uw toepassing eind gebruikers imiteert bij interactie met een sleutel archief (zoals Azure Key Vault), nadat de query van een gebruiker de cache heeft gevuld met een kolom versleutelings sleutel, wordt een volgende query die dezelfde sleutel vereist, maar door een andere gebruiker geactiveerd, gebruikt de de sleutel in de cache. Het stuur programma roept de sleutel opslag niet aan en er wordt niet gecontroleerd of de tweede gebruiker gemachtigd is om de kolom versleutelings sleutel te openen. Als gevolg hiervan kan de gebruiker de versleutelde gegevens weer geven, zelfs als de gebruiker geen toegang tot de sleutels heeft. Als u gebruikers binnen een toepassing voor meerdere gebruikers wilt isoleren, moet u mogelijk de kolom versleutelings sleutel opslaan in cache uitschakelen, wat leidt tot extra prestatie overhead, omdat het stuur programma voor elke gegevens versleuteling of ontsleuteling contact moet opnemen met de sleutel opslag. schijf.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Gegevens beveiligen tegen niet-geautoriseerde weer gave door toepassings gebruikers, met behoud van de gegevens indeling
Een andere techniek om te voor komen dat niet-geautoriseerde gebruikers gegevens weer geven, is om de gegevens te bedekken of te maskeren en de gegevens typen en-indelingen te behouden, zodat gebruikers toepassingen kunnen door gaan met het verwerken en weer geven van de gegevens.

**Implementeren**:

- Gebruik [dynamische gegevens maskering voor het afdekken](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) van tabel kolommen.

> [!NOTE]
> Always Encrypted werkt niet met dynamische gegevens maskering. Het is niet mogelijk om dezelfde kolom te versleutelen en te maskeren, wat impliceert dat u de gegevens die in gebruik zijn, moet bepalen of de gegevens voor uw app-gebruikers worden gemaskeerd via dynamische gegevens maskering.

**Aanbevolen procedures**:

> [!NOTE]
> Dynamische gegevens maskering kan niet worden gebruikt voor het beveiligen van gegevens van gebruikers met hoge bevoegdheden. Maskerings beleid is niet van toepassing op gebruikers met beheerders toegang, zoals db_owner.

- Sta niet toe dat app-gebruikers ad-hoc query's uitvoeren (aangezien ze mogelijk kunnen werken met dynamische gegevens maskering).  
  - Zie het artikel [over het overs laan van maskering met behulp](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) van de technieken voor insluiting of brute kracht voor meer informatie.  

- Gebruik het juiste toegangs beheer beleid (via SQL-machtigingen, rollen, beveiliging op rijniveau) om gebruikers machtigingen te beperken om updates in de gemaskeerde kolommen te maken. Het maken van een masker voor een kolom voor komt niet dat de kolom wordt bijgewerkt. Hoewel gebruikers gemaskeerde gegevens ontvangen tijdens het uitvoeren van query's op de gemaskeerde kolom, kunnen ze de gegevens daarom bijwerken als ze schrijf machtigingen hebben.    

-  Met dynamische gegevens maskering blijven de statistische eigenschappen van de gemaskeerde waarden niet behouden. Dit kan van invloed zijn op query resultaten (bijvoorbeeld query's met filter predikaten of lid worden van de gemaskeerde gegevens).

## <a name="network-security"></a>Netwerkbeveiliging
Netwerk beveiliging heeft betrekking op toegangs beheer en aanbevolen procedures voor het beveiligen van uw gegevens in door voer naar Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Mijn client zo configureren dat deze veilig verbinding maakt met Azure SQL Database 
Voor komen dat client computers en toepassingen verbinding maken met Azure SQL Database van bekende beveiligings problemen als gevolg van de afhankelijkheid van oudere protocollen en coderings suites.

**Implementeren**:

- Zorg ervoor dat client computers die verbinding maken met Azure SQL Database, gebruikmaken van [Transport Layer Security (TLS)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit).

**Aanbevolen procedures**:

- Al uw apps en hulpprogram ma's configureren om verbinding te maken met SQL data base met versleuteling ingeschakeld 
  - Versleutelen = on, TrustServerCertificate = uit (of gelijkwaardig met stuur Programma's die niet van micro soft zijn). 

- Als uw app gebruikmaakt van een stuur programma dat geen ondersteuning biedt voor TLS of een oudere versie van TLS ondersteunt, moet u, indien mogelijk, het stuur programma vervangen. Als dat niet mogelijk is, kunt u de beveiligings Risico's zorgvuldig evalueren. 

- Verminder aanvals vectoren via beveiligings problemen in SSL 2,0, SSL 3,0, TLS 1,0 en TLS 1,1 door ze uit te scha kelen op client computers die verbinding maken met Azure SQL Database per [Transport Layer Security (TLS) register instellingen](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10). 

- Controleer de coderings suites die beschikbaar zijn op de client per [coderings suites in TLS/SSL (Schannel-SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel) en schakel 3DES specifiek in op de [volg orde van TLS-coderings Suite configureren](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- Als u een beheerd exemplaar gebruikt, gebruikt u het **proxy** verbindings type (standaard), omdat hiermee versleuteling wordt afgedwongen van de server zijde. Het verbindings type **omleiding** biedt momenteel geen ondersteuning voor het afdwingen van versleuteling en is alleen beschikbaar voor particuliere IP-verbindingen. 
  - Zie [Azure SQL-connectiviteits architectuur-verbindings beleid](sql-database-connectivity-architecture.md#connection-policy)voor meer informatie.
  - Versleuteling wordt afgedwongen voor de verbindings typen proxy en omleiding. 

### <a name="minimize-attack-surface"></a>Kwets baarheid beperken
Minimaliseer het aantal functies dat door een kwaadwillende gebruiker kan worden aangevallen door netwerk toegangs beheer voor Azure SQL Database te implementeren.

> [!NOTE]
> Vermeld in: OSA-Oefen #5

**Implementeren**:

In een Azure SQL Database-Server (die Singleton-data base of elastische Pools bevat):
- Stel toegang tot Azure-Services toestaan in op uit.

- Gebruik service-eind punten voor virtuele netwerken en firewall regels voor virtuele netwerken.

- Persoonlijke koppeling gebruiken (preview-versie).

In een beheerd exemplaar:
- Volg de richt lijnen in [netwerk vereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Aanbevolen procedures**:

- Het beperken van de toegang tot Azure SQL Database door verbinding te maken met een persoonlijk eind punt (bijvoorbeeld met behulp van een pad naar een privé-data): 
  - Een beheerd exemplaar kan worden geïsoleerd in een virtueel netwerk om externe toegang te voor komen. Toepassingen en hulpprogram ma's die zich in hetzelfde virtuele netwerk bevinden in dezelfde regio, hebben rechtstreeks toegang tot de toepassing. Toepassingen en hulpprogram ma's die zich in verschillende regio's bevinden, kunnen gebruikmaken van VNet-naar-VNet-verbinding of ExpressRoute-circuit peering om verbinding te maken. De klant moet netwerk beveiligings groepen (NSG) gebruiken om de toegang via poort 1433 alleen te beperken tot bronnen die toegang nodig hebben tot een beheerd exemplaar 
  - Voor een SQL Database-Server (met afzonderlijke data bases of elastische Pools) gebruikt u de functie voor [persoonlijke koppelingen](sql-database-private-endpoint-overview.md) waarmee een speciaal privé IP-adres voor de SQL database server in het virtuele netwerk wordt verstrekt. U kunt ook [service-eind punten van Virtual Network met de firewall regels van het virtuele netwerk](sql-database-vnet-service-endpoint-rule-overview.md) gebruiken om de toegang tot uw SQL database-servers te beperken.
  - Mobiele gebruikers moeten gebruikmaken van punt-naar-site-VPN-verbindingen om verbinding te maken via het gegevenspad.
  - Gebruikers die verbinding hebben met hun on-premises netwerk, moeten gebruikmaken van site-naar-site VPN-verbinding of ExpressRoute om verbinding te maken via het gegevenspad.

- U kunt toegang krijgen tot Azure SQL Database door verbinding te maken met een openbaar eind punt (bijvoorbeeld met behulp van een openbaar gegevenspad). U moet rekening houden met de volgende aanbevolen procedures: 
  - Gebruik [IP-firewall regels](sql-database-firewall-configure.md) voor een SQL database-server om de toegang tot alleen geautoriseerde IP-adressen te beperken.
  - Gebruik voor een beheerd exemplaar netwerk beveiligings groepen (NSG) om de toegang tot de vereiste bronnen te beperken via poort 3342. Zie [een Azure SQL database Managed instance veilig met open bare eind punten gebruiken](sql-database-managed-instance-public-endpoint-securely.md)voor meer informatie. 

> [!NOTE]
> Een openbaar eind punt voor een beheerd exemplaar is niet standaard ingeschakeld en moet expliciet worden ingeschakeld. Als het bedrijfs beleid het gebruik van open bare eind punten niet toestaat, gebruikt u [Azure Policy](../governance/policy/overview.md) om te voor komen dat open bare eind punten in de eerste plaats worden ingeschakeld.

- Azure-netwerk onderdelen instellen: 
  - Volg de [Aanbevolen procedures voor Azure voor netwerk beveiliging](../security/fundamentals/network-best-practices.md).
  - Plan Virtual Network (VNet)-configuratie volgens aanbevolen procedures die worden beschreven in [Azure Virtual Network Veelgestelde vragen](../virtual-network/virtual-networks-faq.md) en abonnementen. 
  - Een VNet in meerdere subnetten segmenteren en resources voor een vergelijk bare rol toewijzen aan hetzelfde subnet (bijvoorbeeld front-end VS back-end-resources).
  - Gebruik [netwerk beveiligings groepen (nsg's)](../virtual-network/security-overview.md) om verkeer tussen subnetten binnen de Azure VNet-grens te beheren.
  - Schakel [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) in voor uw abonnement om binnenkomend en uitgaand netwerk verkeer te bewaken.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Power BI configureren voor beveiligde verbindingen met Azure SQL Database

**Aanbevolen procedures**:

- Gebruik waar mogelijk het pad voor persoonlijke gegevens voor Power BI Desktop. 

- Zorg ervoor dat Power BI Desktop verbinding maakt met behulp van TLS 1.2 door de register sleutel op de client computer in te stellen op basis van de Register instellingen per [Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) . 

- Beperk de toegang tot gegevens voor specifieke gebruikers via beveiliging op rijniveau [met Power bi](https://docs.microsoft.com/power-bi/service-admin-rls). 

- Voor Power BI service gebruikt u de [on-premises gegevens gateway](https://docs.microsoft.com/power-bi/service-gateway-onprem)en houdt u rekening met [beperkingen en overwegingen](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>App Service configureren voor beveiligde verbindingen met Azure SQL Database

**Aanbevolen procedures**:

- Voor een eenvoudige web-app moet u voor het verbinden van het open bare eind punt **Azure-Services toestaan** in te stellen op. 

- [Integreer uw app met een Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md) voor de connectiviteit van een persoonlijk gegevenspad naar een beheerd exemplaar. U kunt eventueel ook een web-app implementeren met [app service omgevingen (ASE)](../app-service/environment/intro.md). 

- Voor web-apps met ASE of VNet geïntegreerde web-app die verbinding maakt met een data base in SQL Database Server, kunt u [vnet-service-eind punten en vnet-firewall regels](sql-database-vnet-service-endpoint-rule-overview.md) gebruiken om de toegang vanaf een specifiek VNet en subnet te beperken en vervolgens instellen **dat Azure-Services kunnen** worden uitgeschakeld. U kunt ASE ook verbinden met een beheerd exemplaar via een persoonlijk gegevenspad.  

- Zorg ervoor dat uw web-app is geconfigureerd volgens het artikel, [Aanbevolen procedures voor het beveiligen van PaaS web-en mobiele toepassingen met Azure app service](../security/security-paas-applications-using-app-services.md). 

- Installeer [Web Application firewall (WAF)](../application-gateway/waf-overview.md) om uw web-app te beschermen tegen veelvoorkomende aanvallen en beveiligings problemen.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Azure VM-hosting configureren voor beveiligde verbindingen met Azure SQL Database

**Aanbevolen procedures**:

- Gebruik een combi natie van regels voor toestaan en weigeren op de Nsg's van Azure-Vm's om te bepalen welke regio's toegankelijk zijn vanaf de virtuele machine.

- Zorg ervoor dat uw virtuele machine is geconfigureerd volgens het artikel, [Aanbevolen procedures voor beveiliging voor IaaS-workloads in azure](../security/azure-security-iaas.md).

- Zorg ervoor dat alle Vm's zijn gekoppeld aan een specifiek VNet en subnet. 

- Evalueren of u de standaard route 0.0.0.0/Internet nodig hebt volgens de richt lijnen in het geval [van geforceerde tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - Zo ja, bijvoorbeeld een front-end-subnet en de standaard route blijven gebruiken.
  - Als dit niet het geval is, bijvoorbeeld een middelste laag of een back-end-subnet, schakelt u geforceerde Tunneling in, zodat er geen verkeer meer via internet wordt verzonden om on-premises (a. k. een cross-premises) te bereiken. 

- Implementeer [optionele standaard routes](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) als u peering gebruikt of verbinding maakt met on-premises. 

- Door de [gebruiker gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md#user-defined) implementeren als u al het verkeer in het VNet naar een virtueel netwerk apparaat moet verzenden voor pakket inspectie. 

- [VNet-service-eind punten](sql-database-vnet-service-endpoint-rule-overview.md) gebruiken voor beveiligde toegang tot PaaS-Services, zoals Azure Storage via het Azure-backbone-netwerk. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Beveiligen tegen DDoS-aanvallen (Distributed Denial of service)
DDoS-aanvallen (Distributed Denial of service) zijn pogingen door een kwaadwillende gebruiker om een flood van het netwerk verkeer naar Azure SQL Database te sturen, met als doel de Azure-infra structuur te vervolledigen en ervoor te zorgen dat deze geldige aanmeldingen en workload afwijzen.

> [!NOTE]
> Vermeld in: OSA-Oefen #9

**Implementeren**:

DDoS Protection wordt automatisch ingeschakeld als onderdeel van het Azure-platform. Het bevat permanente verkeers bewaking en real-time beperking van aanvallen op netwerk niveau op open bare eind punten. 

- Gebruik [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) voor het bewaken van open bare IP-adressen die zijn gekoppeld aan resources die in virtuele netwerken zijn geïmplementeerd.

- Gebruik [Advanced Threat Protection voor Azure SQL database](sql-database-threat-detection-overview.md) om DOS-aanvallen (Denial of service) te detecteren op data bases.

**Aanbevolen procedures**:

- Volg de procedures die zijn beschreven in [minimaliseren](#minimize-attack-surface) kwets baarheid, waarmee bedreigingen van DDoS-aanvallen tot een minimum worden beperkt. 

- Met de geavanceerde beveiliging van beveiligings verificatie met behulp van **SQL-referenties** wordt de aanvals aanval gedetecteerd. In sommige gevallen kan de waarschuwing zelfs verschillende werk belastingen voor indringings tests onderscheiden. 

- Voor Azure VM-hosting toepassingen die verbinding maken met SQL Database: 
  - Volg de aanbeveling om de toegang te beperken via Internet gerichte eind punten in Azure Security Center. 
  - Gebruik schaal sets voor virtuele machines om meerdere exemplaren van uw toepassing op Azure-Vm's uit te voeren. 
  - Schakel RDP en SSH van Internet uit om beveiligings aanvallen te voor komen. 

## <a name="monitoring-logging-and-auditing"></a>Bewaking, logboek registratie en controle  
Deze sectie heeft betrekking op mogelijkheden voor het detecteren van afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. Ook worden aanbevolen procedures beschreven voor het configureren van database controle om database gebeurtenissen bij te houden en vast te leggen.

### <a name="protect-databases-against-attacks"></a>Data bases beveiligen tegen aanvallen 
Met geavanceerde beveiliging tegen bedreigingen kunt u potentiële bedreigingen detecteren en erop reageren wanneer deze optreden door beveiligings waarschuwingen te bieden voor afwijkende activiteiten.

**Implementeren**:

- [Geavanceerde bedreigings beveiliging voor SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) gebruiken om ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot data bases, waaronder:
  - SQL-injectie aanval.
  - Referenties dief stal/lekkage.
  - Misbruik van bevoegdheden.
  - Data exfiltration.

**Aanbevolen procedures**:

- Configureer [geavanceerde gegevens beveiliging](sql-database-advanced-data-security.md#getting-started-with-ads) voor Azure SQL database voor een specifieke SQL database-server of een beheerd exemplaar of voor alle SQL database servers en beheerde exemplaren in een abonnement door over te scha kelen naar [Azure Security Center Standard-laag](../security-center/security-center-pricing.md). 

- Voor een volledige onderzoek is het raadzaam om [SQL database controle](sql-database-auditing.md) in te scha kelen om database gebeurtenissen bij te houden en te schrijven naar een audit logboek in een Azure Storage-account of Azure log Analytics-werk ruimte. 

### <a name="audit-critical-security-events"></a>Kritieke beveiligings gebeurtenissen controleren
Het bijhouden van database gebeurtenissen helpt u bij het begrijpen van database activiteiten en inzicht te krijgen in verschillen en afwijkingen die kunnen wijzen op problemen in verband met het bedrijf of vermoedelijke beveiligings schendingen. Het biedt ook de mogelijkheid om te voldoen aan nalevings normen. 

**Implementeren**:

- Schakel [SQL database controle](sql-database-auditing.md) in om database gebeurtenissen bij te houden en te schrijven naar een audit logboek in uw Azure Storage-account Log Analytics-werk ruimte (preview) of event hubs (preview). 

- Audit logboeken kunnen worden geschreven naar een Azure Storage-account, naar een Log Analytics-werk ruimte voor gebruik door Azure Monitor Logboeken of om Event Hub te gebruiken voor het gebruik van Event Hub. U kunt een wille keurige combi natie van deze opties configureren en er worden controle logboeken naar elke optie geschreven. 

**Aanbevolen procedures**:

- Door [SQL database controle](sql-database-auditing.md) op de database server te configureren om gebeurtenissen te controleren, worden alle bestaande en nieuw gemaakte data bases op die server gecontroleerd.
- Standaard bevat het controle beleid alle acties (query's, opgeslagen procedures en geslaagde en mislukte aanmeldingen) voor de data bases, wat kan leiden tot grote hoeveel heden audit Logboeken. Het is raadzaam dat klanten [controle configureren voor verschillende soorten acties en actie groepen met behulp van Power shell](sql-database-auditing.md#subheading-7), om het aantal gecontroleerde acties te beheren en het risico op gebeurtenis verlies te minimaliseren. Dit biedt klanten de mogelijkheid om alleen de werkelijk benodigde controle gegevens vast te leggen.
- Audit logboeken kunnen rechtstreeks worden gebruikt in de [Azure Portal](https://portal.azure.com/), of vanaf de opslag locatie die is geconfigureerd. 


> [!NOTE]
> Als u controle inschakelt op Log Analytics, worden er kosten in rekening gebracht op basis van opname tarieven. Houd rekening met de gekoppelde kosten met behulp van deze [optie](https://azure.microsoft.com/pricing/details/monitor/)of overweeg de audit logboeken op te slaan in een Azure-opslag account. 

**Meer bronnen**:

- [SQL Database controle](sql-database-auditing.md)
- [SQL Server controle](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Controle logboeken beveiligen
Ter ondersteuning van de schei ding van taken en het scheiden van DBA van Audi Tors, is het belang rijk om maat regelen te nemen om de toegang tot het opslag account te beperken. 

**Implementeren**:

- Bij het opslaan van audit Logboeken in Azure Storage moet u ervoor zorgen dat toegang tot het opslag account is beperkt tot de minimale beveiligings principes door het beheren van de toegang tot het opslag account.

- Zie [toegang tot Azure Storage autoriseren](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie.

**Aanbevolen procedures**:

- Het beheren van de toegang tot het controle doel is een belang rijk concept voor het scheiden van de DBA van Audi tors. 

- Wanneer u de toegang tot gevoelige gegevens controleert, kunt u de gegevens beveiligen met gegevens versleuteling om te voor komen dat gegevens naar de auditor worden gelekt. Zie voor meer informatie de sectie [gevoelige gegevens beveiligen tegen zeer privileged en niet-geautoriseerde gebruikers](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Beveiligings beheer

In deze sectie worden de verschillende aspecten en aanbevolen procedures voor het beheren van uw data bases-beveiligings postuur beschreven. Het bevat aanbevolen procedures om ervoor te zorgen dat uw data bases worden geconfigureerd om te voldoen aan de beveiligings normen, voor detectie en het classificeren en bijhouden van toegang tot mogelijk gevoelige gegevens in uw data bases. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Zorg ervoor dat de data base (s) zijn geconfigureerd om te voldoen aan de aanbevolen beveiligings procedures 

Verbeter uw database beveiliging proactief door mogelijke beveiligings lekken in de Data Base op te sporen en te verhelpen.

**Implementeren**:

- Schakel [evaluatie van SQL-beveiligings](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) problemen (VA) in om uw data base te scannen op beveiligings kwesties en om automatisch periodiek te worden uitgevoerd op uw data bases.

**Aanbevolen procedures**:

- Voer in eerste instantie VA uit op uw data bases en herhaal dit door mislukte controles op te lossen die de best practices voor beveiliging en het instellen van basis lijnen voor aanvaard bare configuraties hebben totdat de scan ' clean ' is (alle controles zijn geslaagd).  

- Periodiek terugkerende scans configureren om één keer per week uit te voeren en de relevante persoon configureren voor het ontvangen van overzichts-e-mails. 

- Bekijk de samen vatting van het VA-overzicht na elke wekelijkse scan. Voor alle gedetecteerde beveiligings problemen evalueert u de drift van het vorige scan resultaat en bepaalt u of de controle moet worden opgelost, of dat er een geldige reden is voor de wijziging in de configuratie.   

- Los de controles op en update basis lijnen waar relevant zijn, of maak ticket items voor het oplossen van acties en volg deze totdat ze zijn opgelost. 

**Meer bronnen**:

- [Evaluatie van SQL-beveiligings problemen](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [De evaluatie service van SQL-beveiligings problemen helpt u bij het identificeren van database lekken](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Gevoelige gegevens identificeren en labelen 

Detecteer kolommen die mogelijk gevoelige gegevens bevatten. Classificeer de kolommen om gebruik te maken van geavanceerde bewakings-en beveiligings scenario's op basis van gevoeligheid. 

**Implementeren**:

- Gebruik [SQL-gegevens detectie en-classificatie](sql-database-data-discovery-and-classification.md) voor het detecteren, classificeren, labelen en beveiligen van gevoelige gegevens in uw data bases. 
  - Bekijk de classificatie aanbevelingen die zijn gemaakt door de geautomatiseerde detectie in het dash board voor SQL-gegevens detectie en-classificatie en accepteer de relevante classificaties, zodat uw gevoelige gegevens permanent zijn gelabeld met classificatie Labels. 
  - Hand matig classificaties toevoegen voor aanvullende gevoelige gegevens velden die niet door het geautomatiseerde mechanisme zijn gedetecteerd. 
- Zie voor meer informatie de [classificatie van SQL-gegevens detectie &](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Aanbevolen procedures**:

- Bewaak het classificatie dashboard regel matig voor een nauw keurige evaluatie van de classificatie status van de data base. Een rapport over de status van de database classificatie kan worden geëxporteerd of afgedrukt voor naleving en controle doeleinden.

- Doorlopend de status van aanbevolen gevoelige gegevens in SQL-evaluatie van beveiligings problemen controleren door de detectie regel voor gevoelige gegevens bij te houden en een drift in de aanbevolen kolommen voor classificatie te identificeren.  

- Gebruik classificatie op een manier die is afgestemd op de specifieke behoeften van uw organisatie. Pas uw Information Protection-beleid (gevoeligheid, gegevens typen, detectie logica) aan in het [SQL Information Protection](../security-center/security-center-info-protection-policy.md) -beleid in azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Toegang tot gevoelige gegevens bijhouden 
Bewaak wie gevoelige gegevens opent en query's vastlegt op gevoelige gegevens in audit Logboeken.

**Implementeren**:

- Gebruik de SQL-controle en de gegevens classificatie in combi natie. 
  - In uw [SQL database controle](sql-database-auditing.md) logboek kunt u de toegang tot gevoelige gegevens bijhouden en informatie weer geven zoals de gegevens die zijn geopend, evenals het gevoeligheids label (Zie [toegang tot gevoelige gegevens controleren](sql-database-data-discovery-and-classification.md#subheading-3)). 

**Aanbevolen procedures**:

- Zie Aanbevolen procedures voor de secties auditing en gegevens classificatie: 
  - [Kritieke beveiligings gebeurtenissen controleren](#audit-critical-security-events) 
  - [Gevoelige gegevens identificeren en labelen](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Beveiligings-en nalevings status visualiseren 

Gebruik een systeem voor beveiligings beheer met geïntegreerde infra structuur dat de beveiligings postuur van uw Data Centers (inclusief SQL-data bases) versterkt. Bekijk een lijst met aanbevelingen met betrekking tot de beveiliging van uw data bases en de nalevings status.

**Implementeren**:

- Bewaak SQL-gerelateerde beveiligings aanbevelingen en actieve bedreigingen in [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Veelvoorkomende beveiligings Risico's en mogelijke oplossingen

Deze sectie helpt u bij het vinden van beveiligings maatregelen om te beschermen tegen bepaalde aanvals vectoren. Het is raadzaam om de meeste oplossingen te bereiken door een of meer van de bovenstaande beveiligings richtlijnen te volgen.

### <a name="security-threat-data-exfiltration"></a>Beveiligings risico: gegevens exfiltration

Data exfiltration is het niet-geautoriseerd kopiëren, overdragen of ophalen van gegevens van een computer of server. Zie een definitie voor [Data exfiltration](https://en.wikipedia.org/wiki/Data_exfiltration) op Wikipedia.

Als u verbinding maakt met Azure SQL Database Server via een openbaar eind punt, wordt een gegevens exfiltration risico als klanten vereist zijn om hun firewalls te openen voor open bare Ip's.  

**Scenario 1**: een toepassing op een virtuele Azure-machine maakt verbinding met een data base in een Azure SQL database-server. Een Rogue actor krijgt toegang tot de virtuele machine en brengt deze in de orde. In dit scenario betekent data exfiltration dat een externe entiteit die de Rogue-VM gebruikt verbinding maakt met de data base, persoonlijke gegevens kopieert en opslaat in een Blob-opslag of een andere SQL Database in een ander abonnement.

**Scenario 2**: een Rouge-DBA. Dit scenario wordt vaak veroorzaakt door beveiligings gevoelige klanten van gereguleerde industrieën. In dit scenario kan een gebruiker met een hoge bevoegdheid gegevens kopiëren van Azure SQL Database naar een ander abonnement dat niet wordt beheerd door de eigenaar van de gegevens.

**Mogelijke oplossingen**:

Momenteel biedt Azure SQL Database de volgende technieken voor het beperken van gegevens exfiltration bedreigingen: 

- Gebruik een combi natie van regels voor toestaan en weigeren op de Nsg's van Azure-Vm's om te bepalen welke regio's toegankelijk zijn vanaf de virtuele machine. 
- Als u een Azure SQL Database-Server gebruikt (die Singleton-data bases of elastische Pools bevat), stelt u het volgende in:
  - Hiermee staat u Azure-Services uit.
  - Alleen verkeer toestaan van het subnet met uw Azure-VM door een VNet-firewall regel in te stellen.
  - [Persoonlijke koppeling](sql-database-private-endpoint-overview.md) gebruiken
- Voor een beheerd exemplaar wordt met behulp van privé-IP-toegang standaard de eerste gegevens exfiltration bezorgdheid over een Rogue-VM. Schakel de functie voor het delegeren van subnetten in op een subnet dat wordt verwerkt om automatisch het meest beperkende beleid in te stellen op een subnet voor een beheerd exemplaar.
- Het probleem van de Rogue-DBA is meer beschikbaar met een beheerd exemplaar, omdat het een grotere surface area en netwerk vereisten voor klanten zichtbaar zijn. De beste beperking hiervoor is het Toep assen van alle procedures in deze beveiligings handleiding om te voor komen dat het Rogue DBA-scenario in de eerste plaats (niet alleen voor gegevens exfiltration). Always Encrypted is een methode om gevoelige gegevens te beveiligen door deze te versleutelen en de sleutel niet toegankelijk te maken voor de DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Beveiligings aspecten van bedrijfs continuïteit en beschik baarheid

De meeste beveiligings standaarden zijn van toepassing op de beschik baarheid van gegevens in het kader van operationele continuïteit, behaald door de implementatie van redundantie en failover om individuele storings punten te voor komen. Voor scenario's met nood gevallen is het een veelvoorkomende manier om back-ups van gegevens en logboek bestanden te maken. De volgende sectie bevat een overzicht van de mogelijkheden die zijn ingebouwd in azure, evenals aanvullende opties die kunnen worden geconfigureerd om te voldoen aan specifieke vereisten: 

- Azure biedt ingebouwde hoge Beschik baarheid: [hoge Beschik baarheid en Azure SQL database](sql-database-high-availability.md) 

- De laag Bedrijfskritiek bevat failover-groepen, zones voor meerdere Beschik baarheid, volledige en differentiële logboek back-ups en herstel naar een bepaald tijdstip, back-ups die standaard zijn ingeschakeld:  
  - [Redundante configuratie met hoge Beschik baarheid en Azure SQL Database zone](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatische back-ups](sql-database-automated-backups.md)
  - [Een Azure SQL Database herstellen met behulp van geautomatiseerde back-ups van data bases-herstel punt in tijd](sql-database-recovery-using-backups.md#point-in-time-restore)

- Aanvullende functies voor bedrijfs continuïteit, zoals groepen voor automatische failover in verschillende Azure geografische gebieden, kunnen worden geconfigureerd zoals hier wordt beschreven: [overzicht van bedrijfs continuïteit met Azure SQL database](sql-database-business-continuity.md)

## <a name="next-steps"></a>Volgende stappen

- Bekijk [een overzicht van Azure SQL database beveiligings mogelijkheden](sql-database-security-overview.md)