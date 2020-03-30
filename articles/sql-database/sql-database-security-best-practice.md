---
title: Draaiboek voor het aanpakken van gemeenschappelijke beveiligingsvereisten | Microsoft Documenten
titleSuffix: Azure SQL Database
description: In dit artikel vindt u algemene beveiligingsvereisten en aanbevolen procedures in Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: c18e1b1a1feba5c528a692b7d63287b3751b62cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77506223"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Playbook voor het aanpakken van algemene beveiligingsvereisten met Azure SQL Database

> [!NOTE]
> Dit document bevat aanbevolen procedures voor het oplossen van algemene beveiligingsvereisten. Niet alle vereisten zijn van toepassing op alle omgevingen en u dient uw database en beveiligingsteam te raadplegen over welke functies u moet implementeren.

## <a name="solving-common-security-requirements"></a>Algemene beveiligingsvereisten oplossen

Dit document biedt richtlijnen voor het oplossen van algemene beveiligingsvereisten voor nieuwe of bestaande toepassingen met Azure SQL Database. Het wordt georganiseerd door veiligheidsgebieden op hoog niveau. Raadpleeg voor het aanpakken van specifieke bedreigingen de sectie [Algemene bedreigingen van de veiligheid en mogelijke oplossingen.](#common-security-threats-and-potential-mitigations) Hoewel sommige van de gepresenteerde aanbevelingen van toepassing zijn bij het migreren van toepassingen van on-premises naar Azure, zijn migratiescenario's niet de focus van dit document.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Azure SQL Database-implementatieaanbiedingen die in deze handleiding worden behandeld

- [SQL-databases](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): [afzonderlijke databases](sql-database-single-database.md) en [elastische pools](sql-database-elastic-pool.md) in Azure SQL [Database-servers](sql-database-servers.md)
- [Beheerde exemplaren](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>SQL-implementatieaanbiedingen die niet in deze handleiding worden behandeld

- Azure SQL Data Warehouse
- Azure SQL VM's (IaaS)
- SQL Server on-premises

### <a name="audience"></a>Doelgroep

De beoogde doelgroepen voor deze handleiding zijn klanten die vragen krijgen over het beveiligen van Azure SQL Database. De rollen die geïnteresseerd zijn in dit best practice artikel omvatten, maar niet beperkt tot:

- Beveiligingsarchitecten
- Beveiligingsmanagers
- Compliance Officers
- Privacy Officers
- Beveiligingstechnici

### <a name="using-this-guide"></a><a id="using"></a>Deze handleiding gebruiken

Dit document is bedoeld als aanvulling op onze bestaande [Azure SQL Database-beveiligingsdocumentatie.](sql-database-security-overview.md)

Tenzij anders vermeld, raden we u aan alle aanbevolen procedures in elke sectie te volgen om het desbetreffende doel of vereiste te bereiken. Om te voldoen aan specifieke veiligheidsnormen of aanbevolen procedures, worden belangrijke nalevingscontroles voor de regelgeving weergegeven onder de sectie Vereisten of doelen, waar van toepassing. Dit zijn de veiligheidsnormen en -voorschriften waarnaar in dit document wordt verwezen:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Toegangscontrole, Cryptografie
- [Microsoft Operational Security Assurance (OSA) praktijken](https://www.microsoft.com/en-us/securityengineering/osa/practices): Praktijk #1-6 en #9
- [NIST Special Publication 800-53 Security Controls](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Feedback

We zijn van plan de aanbevelingen en best practices die hier worden vermeld, verder bij te werken. Geef invoer of correcties voor dit document met behulp van de **koppeling Feedback** onder aan dit artikel.

## <a name="authentication"></a>Authentication

Authenticatie is het proces om te bewijzen dat de gebruiker is wie hij beweert te zijn. Azure SQL Database ondersteunt twee soorten verificatie:

- SQL-verificatie
- Verificatie via Azure Active Directory

> [!NOTE]
> Azure Active Directory-verificatie wordt mogelijk niet ondersteund voor alle hulpprogramma's en toepassingen van derden.

### <a name="central-management-for-identities"></a>Centraal beheer voor identiteiten

Centraal identiteitsbeheer biedt de volgende voordelen:

- Beheer groepsaccounts en beheer gebruikersmachtigingen zonder aanmeldingen in Azure SQL Database-servers en -databases te dupliceren.
- Vereenvoudigd en flexibel machtigingsbeheer.
- Beheer van aanvragen op schaal.

**Hoe te implementeren:**

- Azure Active Directory-verificatie (Azure AD) gebruiken voor gecentraliseerd identiteitsbeheer.

**Aanbevolen procedures**:

- Maak een Azure AD-tenant en [maak gebruikers](../active-directory/fundamentals/add-users-azure-active-directory.md) om menselijke gebruikers te vertegenwoordigen en [maak serviceprincipals](../active-directory/develop/app-objects-and-service-principals.md) om apps, services en automatiseringshulpprogramma's weer te geven. Serviceprincipals zijn gelijk aan serviceaccounts in Windows en Linux. 

- Toegangsrechten toewijzen aan Azure AD-principals via groepstoewijzing: Azure AD-groepen maken, toegang verlenen tot groepen en afzonderlijke leden toevoegen aan de groepen. Maak in uw database opgenomen databasegebruikers die uw Azure AD-groepen toewijzen. Als u machtigingen in de database wilt toewijzen, plaatst u gebruikers in databaserollen met de juiste machtigingen.
  - Zie de [artikelen, Configureer en beheer Azure Active Directory-verificatie met SQL](sql-database-aad-authentication-configure.md) en [Gebruik Azure AD voor verificatie met SQL.](sql-database-aad-authentication.md)
  > [!NOTE]
  > In een beheerde instantie u ook aanmeldingen maken die worden toegewezen aan Azure AD-principals in de hoofddatabase. Zie [LOGIN MAKEN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Het gebruik van Azure AD-groepen vereenvoudigt het machtigingsbeheer en zowel de groepseigenaar als de eigenaar van de bron kan leden toevoegen/verwijderen aan/uit de groep. 

- Maak een aparte groep voor Azure AD-beheerder voor SQL DB-servers.

  - Zie het artikel, [Een Azure Active Directory-beheerder inrichten voor uw Azure SQL Database-server](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Wijzigingen in azure AD-groepslidmaatschap controleren met azure AD-controleactiviteitsrapporten. 

- Voor een beheerde instantie is een afzonderlijke stap vereist om Azure AD-beheerder te maken. 
  - Zie het artikel, [Een Azure Active Directory-beheerder inrichten voor uw beheerde instantie](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - Azure AD-verificatie wordt geregistreerd in Azure SQL-controlelogboeken, maar niet in azure AD-aanmeldingslogboeken.
> - RBAC-machtigingen die in Azure zijn verleend, zijn niet van toepassing op Azure SQL DB-machtigingen. Dergelijke machtigingen moeten handmatig worden gemaakt/toegewezen in SQL DB met behulp van bestaande SQL-machtigingen.
> - Aan de clientzijde heeft Azure AD-verificatie toegang nodig tot internet of via User Defined Route (UDR) tot een VNet.
> - Het Azure AD-toegangstoken wordt in de cache opgeslagen aan de clientzijde en de levensduur ervan is afhankelijk van de tokenconfiguratie. Zie het artikel [Configureerbare tokenlevensduur in Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Zie de volgende blog voor richtlijnen voor problemen met Azure AD-verificatie:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> Genoemd in: OSA Practice #2, ISO Access Control (AC)

Azure Multi-Factor Authentication (MFA) biedt extra beveiliging door meer dan één vorm van verificatie te vereisen.

**Hoe te implementeren:**

- [Schakel MFA](../active-directory/authentication/concept-mfa-howitworks.md) in Azure AD in met voorwaardelijke toegang en gebruik interactieve verificatie. 

- Het alternatief is om MFA in te schakelen voor het gehele Azure AD- of AD-domein.

**Aanbevolen procedures**:

- Voorwaardelijke toegang activeren in Azure AD (vereist Premium-abonnement). 
  - Zie het artikel [Voorwaardelijke toegang in Azure AD](../active-directory/conditional-access/overview.md).  

- Maak Azure AD-groep(s) en schakel MFA-beleid in voor geselecteerde groepen met Azure AD Conditional Access. 
  - Zie het artikel, [Implementatie voorwaardelijke toegang plannen](../active-directory/conditional-access/plan-conditional-access.md). 

- MFA kan worden ingeschakeld voor het gehele Azure AD of voor de hele Active Directory met Azure AD. 

- Gebruik de azure AD-verificatiemodus voor SQL DB waarbij een wachtwoord interactief wordt aangevraagd, gevolgd door MFA-verificatie:      
  - Gebruik universele verificatie in SSMS. Zie het artikel, [Multi-factor AAD-verificatie gebruiken met Azure SQL Database en Azure SQL Data Warehouse (SSMS-ondersteuning voor MFA).](sql-database-ssms-mfa-authentication.md)
  - Gebruik interactieve verificatie die wordt ondersteund in SQL Server Data Tools (SSDT). Zie het artikel Azure [Active Directory-ondersteuning in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Gebruik andere SQL-hulpprogramma's die MFA ondersteunen. 
    - Ondersteuning voor SSMS Wizard voor database exporteren/extraheren/implementeren  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): optie '/ua' 
    - [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility): optie -G (interactief)
    - [bcp Utility](https://docs.microsoft.com/sql/tools/bcp-utility): optie -G (interactief) 

- Implementeer uw toepassingen om verbinding te maken met Azure SQL Database met behulp van interactieve verificatie met MFA-ondersteuning. 
  - Zie het artikel, [Maak verbinding met Azure SQL Database met Azure Multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Deze verificatiemodus vereist gebruikersidentiteiten. In gevallen waarin een vertrouwd identiteitsmodel wordt gebruikt dat individuele Azure AD-gebruikersverificatie omzeilt (bijvoorbeeld met beheerde identiteit voor Azure-resources), is MFA niet van toepassing.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Het gebruik van verificatie op basis van wachtwoorden voor gebruikers minimaliseren

> Genoemd in: OSA Practice #4, ISO Access Control (AC)

Verificatiemethoden op basis van wachtwoorden zijn een zwakkere vorm van authenticatie. Referenties kunnen worden gecompromitteerd of per ongeluk worden weggegeven.

**Hoe te implementeren:**

- Gebruik een geïntegreerde azure-verificatie voor AD die het gebruik van wachtwoorden elimineert.

**Aanbevolen procedures**:

- Gebruik één aanmeldingsverificatie met Windows-referenties. Veroordeel het on-premises AD-domein met Azure AD en gebruik geïntegreerde Windows-verificatie (voor met domeinen verbonden machines met Azure AD).
  - Zie het artikel, [SSMS-ondersteuning voor Azure AD Integrated authentication](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Het gebruik van verificatie op basis van wachtwoorden voor toepassingen minimaliseren 

> Genoemd in: OSA Practice #4, ISO Access Control (AC)

**Hoe te implementeren:**

- Azure Managed Identity inschakelen. U ook geïntegreerde of op certificaten gebaseerde verificatie gebruiken. 

**Aanbevolen procedures**:

- Beheerde [identiteiten gebruiken voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).
  - [Door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Door een gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Azure SQL Database gebruiken vanuit app-service met beheerde identiteit (zonder codewijzigingen)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Gebruik verificatie op basis van certificaten voor een toepassing. 
  - Zie dit [codevoorbeeld](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Azure AD-verificatie gebruiken voor geïntegreerde federatieve domein- en domeinbeheermachine (zie sectie hierboven).
  - Zie de [voorbeeldtoepassing voor geïntegreerde verificatie](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Wachtwoorden en geheimen beveiligen

Voor gevallen waarin wachtwoorden niet kunnen worden vermeden, moet u ervoor zorgen dat ze zijn beveiligd.

**Hoe te implementeren:**

- Gebruik Azure Key Vault om wachtwoorden en geheimen op te slaan. Gebruik, indien van toepassing, MFA voor Azure SQL Database met Azure AD-gebruikers.

**Aanbevolen procedures**:

- Als het vermijden van wachtwoorden of geheimen niet mogelijk is, slaat u gebruikerswachtwoorden en toepassingsgeheimen op in Azure Key Vault en beheert u de toegang via het toegangsbeleid voor Key Vault. 

- Verschillende app-ontwikkelingsframeworks kunnen ook kaderspecifieke mechanismen bieden voor het beschermen van geheimen in de app. Bijvoorbeeld: [ASP.NET kern-app](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>SQL-verificatie gebruiken voor oudere toepassingen 

SQL-verificatie verwijst naar de verificatie van een gebruiker wanneer hij verbinding maakt met Azure SQL Database met gebruikersnaam en wachtwoord. Er moet een aanmelding worden gemaakt in elke SQL Database-server of een beheerde instantie en een gebruiker die in elke database is gemaakt.

**Hoe te implementeren:**

- SQL-verificatie gebruiken.

**Aanbevolen procedures**:

- Maak als serverbeheerder aanmeldingen en gebruikers. Tenzij het gebruik van opgenomen databasegebruikers met wachtwoorden, worden alle wachtwoorden opgeslagen in de hoofddatabase.
  - Zie het artikel, [Controle en het verlenen van databasetoegang tot SQL Database en SQL Data Warehouse.](sql-database-manage-logins.md)

## <a name="access-management"></a>Toegangsbeheer

Toegangsbeheer is het proces voor het beheren en beheren van de toegang en bevoegdheden van geautoriseerde gebruikers tot Azure SQL Database.

### <a name="implement-principle-of-least-privilege"></a>Implementeren principe van minste bevoegdheden

> Vermeld in: FedRamp controles AC-06, NIST: AC-6, OSA Practice #3

Het principe van de minste bevoegdheden stelt dat gebruikers niet meer bevoegdheden moeten hebben dan nodig is om hun taken te voltooien. Voor meer informatie, zie het artikel [Net genoeg administratie](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Hoe te implementeren:**

Alleen de benodigde [machtigingen](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) toewijzen om de vereiste taken te voltooien:

- In SQL-gegevensvlak: 
    - Gedetailleerde machtigingen en door de gebruiker gedefinieerde databaserollen (of serverrollen in MI) gebruiken: 
        1. De vereiste rollen maken
            - [ROL MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [SERVERROL MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Vereiste gebruikers maken
            - [GEBRUIKER MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Gebruikers toevoegen als leden aan rollen 
            - [VERANDERINGROL](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [SERVERROL WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Wijs vervolgens machtigingen toe aan rollen. 
            - [Verlenen](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Zorg ervoor dat u gebruikers niet aan onnodige rollen toewijst.

- In Azure Resource Manager:
  - Gebruik ingebouwde rollen indien beschikbaar of aangepaste RBAC-rollen en wijs de benodigde machtigingen toe.
    - [Ingebouwde rollen voor Azure](../role-based-access-control/built-in-roles.md) 
    - [Aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)

**Aanbevolen procedures**:

De volgende best practices zijn optioneel, maar resulteren in een betere beheerbaarheid en ondersteuning van uw beveiligingsstrategie: 

- Indien mogelijk, beginnen met de minst mogelijke set van machtigingen en beginnen met het toevoegen van machtigingen een voor een als er een echte noodzaak (en rechtvaardiging) - in tegenstelling tot de tegenovergestelde aanpak: het nemen van machtigingen weg stap voor stap. 

- Zich te onthouden van het toewijzen van machtigingen aan individuele gebruikers. Gebruik in plaats daarvan consequent rollen (database- of serverrollen). Rollen helpt enorm bij het melden en oplossen van problemen machtigingen. (Azure RBAC ondersteunt alleen machtigingstoewijzing via rollen.) 

- Aangepaste rollen maken en gebruiken met de exacte machtigingen die nodig zijn. Typische rollen die in de praktijk worden gebruikt: 
  - Beveiligingsimplementatie 
  - Beheerder 
  - Developer 
  - Ondersteunend personeel 
  - Auditor 
  - Geautomatiseerde processen 
  - Eindgebruiker 
  
- Gebruik ingebouwde rollen alleen wanneer de machtigingen van de rollen exact overeenkomen met de benodigde machtigingen voor de gebruiker. U gebruikers aan meerdere rollen toewijzen. 

- Houd er rekening mee dat machtigingen in SQL Server Database Engine kunnen worden toegepast op de volgende scopes. Hoe kleiner het bereik, hoe kleiner de impact van de verleende machtigingen: 
  - Azure SQL Database-server (speciale rollen in hoofddatabase) 
  - Database 
  - Schema
      - Het is een aanbevolen praktijk om schema's te gebruiken om machtigingen in een database te verlenen. (zie ook: [Schema-ontwerp voor SQL Server: aanbevelingen voor schemaontwerp met beveiliging in het achterhoofd)](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)
  - Object (tabel, weergave, procedure, enz.) 
  > [!NOTE]
  > Het wordt afgeraden om machtigingen toe te passen op objectniveau, omdat dit niveau onnodige complexiteit toevoegt aan de algehele implementatie. Als u besluit om machtigingen op objectniveau te gebruiken, moeten deze duidelijk worden gedocumenteerd. Hetzelfde geldt voor kolom-niveau-machtigingen, die nog minder aan te bevelen om dezelfde redenen. Houd er ook rekening mee dat een [weigering](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) op tabelniveau een subsidie op kolomniveau niet overschrijft. Hiervoor moeten de algemene criteria voor [serverconfiguratie](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) worden geactiveerd.

- Voer regelmatig controles uit met Behulp van [Vulnerability Assessment (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) om te testen op te veel machtigingen.

### <a name="implement-separation-of-duties"></a>Scheiding van taken uitvoeren

> Vermeld in: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Scheiding van taken, ook wel Segregatie van Taken genoemd, beschrijft de vereiste om gevoelige taken op te splitsen in meerdere taken die aan verschillende gebruikers zijn toegewezen. Scheiding van taken helpt datalekken te voorkomen.

**Hoe te implementeren:**

- Identificeer het vereiste niveau van scheiding van taken. Voorbeelden: 
  - Tussen ontwikkel-/test- en productieomgevingen 
  - Beveiligingsgevoelige taken vs DBA-beheerniveautaken vs ontwikkelaarstaken. 
    - Voorbeelden: Auditor, creatie van beveiligingsbeleid voor Role-level Security (RLS), SQL Database-objecten implementeren met DDL-machtigingen.

- Identificeer een uitgebreide hiërarchie van gebruikers (en geautomatiseerde processen) die toegang hebben tot het systeem.

- Maak rollen op basis van de benodigde gebruikersgroepen en wijs machtigingen toe aan rollen. 
  - Voor taken op managementniveau in azure portal of via PowerShell-automatisering gebruikt RBAC-rollen. Zoek een ingebouwde rol die overeenkomt met de vereiste of maak een aangepaste RBAC-rol met de beschikbare machtigingen 
  - Serverrollen maken voor serverbrede taken (het maken van nieuwe aanmeldingen, databases) in een beheerde instantie. 
  - Databaserollen maken voor taken op databaseniveau.

- Voor bepaalde gevoelige taken u overwegen speciale opgeslagen procedures te maken die door een certificaat zijn ondertekend om de taken namens de gebruikers uit te voeren. 
  - Voorbeeld: [zelfstudie: opgeslagen procedures ondertekenen met een certificaat](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementeer Transparent Data Encryption (TDE) met door de klant beheerde sleutels in Azure Key Vault om scheiding van taken tussen de eigenaar van gegevens en de eigenaar van de beveiliging mogelijk te maken. 
  - Zie het artikel, [Configureer door klanten beheerde sleutels voor Azure Storage-versleuteling vanuit de Azure-portal.](../storage/common/storage-encryption-keys-portal.md) 

- Om ervoor te zorgen dat een DBA geen gegevens kan zien die als zeer gevoelig worden beschouwd en nog steeds DBA-taken kunnen uitvoeren, u Always Encrypted gebruiken met rolscheiding. 
  - Zie de artikelen, [Overzicht van Sleutelbeheer voor altijd versleuteld,](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) [sleutelinrichting met rolscheiding](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)en [kolommastersleutelrotatie met rolscheiding](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- In gevallen waarin het niet haalbaar is, althans niet zonder grote kosten en inspanningen die het systeem in de buurt onbruikbaar kunnen maken, kunnen compromissen worden gemaakt en beperkt door het gebruik van compenserende controles zoals: 
  - Menselijke interventie in processen. 
  - Audittrails – voor meer informatie over controle, zie [kritieke beveiligingsgebeurtenissen controleren](#audit-critical-security-events).

**Aanbevolen procedures**:

- Zorg ervoor dat verschillende accounts worden gebruikt voor ontwikkel-/test- en productieomgevingen. Verschillende accounts helpen om te voldoen aan de scheiding van test- & productiesystemen.

- Zich te onthouden van het toewijzen van machtigingen aan individuele gebruikers. Gebruik in plaats daarvan consequent rollen (database- of serverrollen). Het hebben van rollen helpt enorm bij het melden en oplossen van problemen machtigingen.

- Gebruik ingebouwde rollen wanneer de machtigingen exact overeenkomen met de benodigde machtigingen: als de samenvoeging van alle machtigingen van meerdere ingebouwde rollen leidt tot een 100%-overeenkomst, u ook meerdere rollen tegelijk toewijzen. 

- Aangepaste rollen maken en gebruiken wanneer ingebouwde rollen te veel machtigingen of onvoldoende machtigingen verlenen. 

- Roltoewijzingen kunnen ook tijdelijk worden uitgevoerd, ook wel bekend als Dynamic Separation of Duties (DSD), hetzij binnen SQL Agent Job-stappen in T-SQL of met Behulp van Azure PIM voor RBAC-rollen. 

- Zorg ervoor dat DBA's geen toegang hebben tot de encryptiesleutels of sleutelopslag en beveiligingsbeheerders met toegang tot de sleutels hebben op hun beurt geen toegang tot de database. 

- Zorg er altijd voor dat u een audittrail hebt voor beveiligingsgerelateerde acties. 

- U de definitie van de ingebouwde RBAC-rollen ophalen om de gebruikte machtigingen te zien en een aangepaste rol maken op basis van fragmenten en cumulaties hiervan via PowerShell.

- Aangezien elk lid van de db_owner databaserol beveiligingsinstellingen zoals Transparent Data Encryption (TDE) kan wijzigen of de SLO kan wijzigen, moet dit lidmaatschap met zorg worden verleend. Er zijn echter veel taken waarvoor db_owner bevoegdheden nodig zijn. Taak zoals het wijzigen van een database-instelling, zoals het wijzigen van DB-opties. Auditing speelt een belangrijke rol in elke oplossing.

- Het is niet mogelijk om machtigingen voor een db_owner te beperken en zo te voorkomen dat een administratief account gebruikersgegevens bekijkt. Als er zeer gevoelige gegevens in een database zijn, kan Always Encrypted worden gebruikt om te voorkomen dat db_owners of een andere DBA deze niet kan bekijken.

> [!NOTE]
> Het bereiken van scheiding van taken (SoD) is een uitdaging voor beveiligingsgerelateerde of probleemoplossingstaken. Andere gebieden zoals ontwikkeling en rollen voor eindgebruikers zijn gemakkelijker te scheiden. De meeste compliance-gerelateerde besturingselementen maken het gebruik van alternatieve besturingsfuncties mogelijk, zoals Controle wanneer andere oplossingen niet praktisch zijn.

Voor de lezers die dieper in SoD willen duiken, raden we de volgende bronnen aan: 

- Voor Azure SQL Database:  
  - [Databasetoegang tot SQL Database en SQL Data Warehouse beheren en verlenen](sql-database-manage-logins.md)
  - [Motor scheiding van taken voor de applicatie-ontwikkelaar](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Scheiding van taken in SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Opgeslagen procedures ondertekenen in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Voor Azure Resource Management:
  - [Ingebouwde rollen voor Azure](../role-based-access-control/built-in-roles.md) 
  - [Aangepaste rollen voor Azure-resources](../role-based-access-control/custom-roles.md)
  - [Azure AD Privileged Identity Management gebruiken voor verhoogde toegang](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Regelmatige codebeoordelingen uitvoeren

> Vermeld in: PCI: 6.3.2, SOC: SDL-3 

Scheiding van taken is niet beperkt tot de gegevens in de database, maar bevat toepassingscode. Schadelijke code kan beveiligingscontroles mogelijk omzeilen. Voordat u aangepaste code implementeert in de productie, is het essentieel om te controleren wat er wordt geïmplementeerd.

**Hoe te implementeren:**

- Gebruik een databasetool zoals Azure Data Studio dat bronbeheer ondersteunt. 

- Implementeer een gesegregeerd codeimplementatieproces.

- Alvorens te plegen aan de belangrijkste tak, een persoon (andere dan de auteur van de code zelf) heeft om de code te inspecteren voor mogelijke verhoging van privileges risico's, alsmede kwaadaardige gegevens wijzigingen te beschermen tegen fraude en malafide toegang. Dit kan worden gedaan met behulp van broncontrolemechanismen.

**Aanbevolen procedures**:

- Standaardisatie: Het helpt bij het implementeren van een standaardprocedure die moet worden gevolgd voor eventuele code-updates. 

- Kwetsbaarheidsbeoordeling bevat regels die controleren op overmatige machtigingen, het gebruik van oude versleutelingsalgoritmen en andere beveiligingsproblemen binnen een databaseschema. 

- Verdere controles kunnen worden uitgevoerd in een QA- of testomgeving met behulp van Advanced Threat Protection die scant op code die kwetsbaar is voor SQL-injectie.

- Voorbeelden van waar je op moet letten: 
  - Het maken van een gebruiker of het wijzigen van beveiligingsinstellingen vanuit een geautomatiseerde SQL-code-update-implementatie. 
  - Een opgeslagen procedure, die, afhankelijk van de opgegeven parameters, een geldwaarde in een cel op een niet-conforme manier bijwerkt. 

- Zorg ervoor dat de persoon die de beoordeling uitvoert een andere persoon is dan de auteur van de oorspronkelijke code en goed geïnformeerd is in codebeoordelingen en veilige codering.

- Zorg ervoor dat u alle bronnen van code-wijzigingen kennen. Code kan in T-SQL Scripts zijn. Het kunnen ad-hocopdrachten zijn die moeten worden uitgevoerd of geïmplementeerd in vormen van weergaven, functies, triggers en opgeslagen procedures. Het kan deel uitmaken van SQL Agent Job-definities (Stappen). Het kan ook worden uitgevoerd vanuit SSIS-pakketten, Azure Data Factory en andere services.

## <a name="data-protection"></a>Gegevensbeveiliging

Gegevensbescherming is een reeks mogelijkheden om belangrijke informatie te beschermen tegen compromissen door versleuteling of verduistering.

> [!NOTE]
> Microsoft bevestigt dat Azure SQL Database FIPS 140-2 Level 1-compatibel is. Dit gebeurt na verificatie van het strikte gebruik van FIPS 140-2 Level 1 aanvaardbare algoritmen en FIPS 140-2 Level 1 valideerde exemplaren van deze algoritmen, waaronder consistentie met vereiste sleutellengtes, sleutelbeheer, sleutelgeneratie en sleutelopslag. Deze verklaring is bedoeld om onze klanten in staat te stellen te reageren op de behoefte of eis voor het gebruik van FIPS 140-2 Level 1 gevalideerde exemplaren bij de verwerking van gegevens of levering van systemen of toepassingen. We definiëren de termen "FIPS 140-2 Level 1 compliant" en "FIPS 140-2 Level 1 compliance" die in de bovenstaande verklaring worden gebruikt om hun beoogde toepasbaarheid aan te tonen voor het gebruik door de Amerikaanse en Canadese overheid van de verschillende term "FIPS 140-2 Level 1 gevalideerd." 


### <a name="encrypt-data-in-transit"></a>Gegevens versleutelen tijdens het transport

> Vermeld in: OSA Practice #6, ISO Control Family: Cryptography

Beschermt uw gegevens terwijl gegevens tussen uw client en server worden verplaatst. Raadpleeg [netwerkbeveiliging](#network-security).

### <a name="encrypt-data-at-rest"></a>Data-at-rest versleutelen

> Vermeld in: OSA Practice #6, ISO Control Family: Cryptography

Versleuteling in rust is de cryptografische bescherming van gegevens wanneer deze worden gehandhaafd in database-, log- en back-upbestanden.

**Hoe te implementeren:**

- [Transparent Database Encryption (TDE)](transparent-data-encryption-azure-sql.md) met servicebeheersleutels is standaard ingeschakeld voor databases die na 2017 in Azure SQL Database zijn gemaakt.
- Als de database in een beheerde instantie is gemaakt met behulp van een herstelbewerking met behulp van een on-premises server, wordt de TDE-instelling van de oorspronkelijke database gehonoreerd. Als de oorspronkelijke database geen TDE heeft ingeschakeld, raden we u aan om TDE handmatig in te schakelen voor de beheerde instantie.

**Aanbevolen procedures**:

- Sla geen gegevens op die versleuteling vereisen in de hoofddatabase. De hoofddatabase kan niet worden versleuteld met TDE.

- Gebruik door de klant beheerde sleutels in Azure Key Vault als u meer transparantie en gedetailleerde controle over de TDE-beveiliging nodig hebt. Azure Key Vault biedt de mogelijkheid om op elk gewenst moment machtigingen in te trekken om de database ontoegankelijk te maken. U TDE-beveiligingen centraal beheren samen met andere sleutels of de TDE-beveiliging volgens uw eigen schema roteren met Azure Key Vault.

- Als u door de klant beheerde sleutels gebruikt in Azure Key Vault, volgt u de artikelen, [richtlijnen voor het configureren van TDE met Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) en Hoe [geo-DR te configureren met Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Bescherm gevoelige gegevens die worden gebruikt tegen gebruikers met een hoge bevoorrechte, onbevoegde

Gegevens in gebruik zijn de gegevens die zijn opgeslagen in het geheugen van het databasesysteem tijdens de uitvoering van SQL-query's. Als uw database gevoelige gegevens opslaat, moet uw organisatie mogelijk ervoor zorgen dat gebruikers met een hoge bevoorrechte positie niet kunnen worden weergegeven om gevoelige gegevens in uw database te bekijken. Gebruikers met hoge bevoegdheden, zoals Microsoft-operators of DBA's in uw organisatie, moeten de database kunnen beheren, maar moeten voorkomen dat gevoelige gegevens uit het geheugen van het SQL Server-proces worden weergegeven en mogelijk worden geëstrerend of door de database op te vragen.

Het beleid dat bepaalt welke gegevens gevoelig zijn en of de gevoelige gegevens in het geheugen moeten worden versleuteld en niet toegankelijk zijn voor beheerders in plaintext, zijn specifiek voor uw organisatie en nalevingsvoorschriften waaraan u zich moet houden. Zie de bijbehorende vereiste: [Gevoelige gegevens identificeren en taggen](#identify-and-tag-sensitive-data).

**Hoe te implementeren:**

- Gebruik [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) om ervoor te zorgen dat gevoelige gegevens niet in plaintext worden weergegeven in Azure SQL Database, zelfs niet in het geheugen/in gebruik. Always Encrypted beschermt de gegevens tegen Database Administrators (DBA's) en cloudbeheerders (of slechte acteurs die zich kunnen voordoen als gebruikers met een hoge bevoorrechte maar onbevoegde gebruiker) en geeft u meer controle over wie toegang heeft tot uw gegevens.

**Aanbevolen procedures**:

- Always Encrypted is geen substituut om gegevens in rust (TDE) of onderweg (SSL/TLS) te versleutelen. Always Encrypted mag niet worden gebruikt voor niet-gevoelige gegevens om de impact op prestaties en functionaliteit te minimaliseren. Het gebruik van Always Encrypted in combinatie met TDE en Transport Layer Security (TLS) wordt aanbevolen voor uitgebreide bescherming van gegevens in rust, in-transit en in-gebruik. 

- Beoordeel de impact van het versleutelen van de geïdentificeerde gevoelige gegevenskolommen voordat u Always Encrypted implementeert in een productiedatabase. In het algemeen vermindert Always Encrypted de functionaliteit van query's op versleutelde kolommen en heeft het andere beperkingen, vermeld in [Always Encrypted - Feature Details](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Daarom moet u uw toepassing mogelijk opnieuw ontwerpen om de functionaliteit opnieuw te implementeren, een query ondersteunt deze niet aan de clientzijde of/en refactor uw databaseschema, inclusief de definities van opgeslagen procedures, functies, weergaven en triggers. Bestaande toepassingen werken mogelijk niet met versleutelde kolommen als ze zich niet houden aan de beperkingen en beperkingen van Always Encrypted. Terwijl het ecosysteem van Microsoft-tools, producten en services die Always Encrypted ondersteunen groeit, werken een aantal van hen niet met versleutelde kolommen. Het versleutelen van een kolom kan ook van invloed zijn op de queryprestaties, afhankelijk van de kenmerken van uw werkbelasting. 

- Beheer Always Encrypted-sleutels met rolscheiding als u Always Encrypted gebruikt om gegevens te beschermen tegen schadelijke DBA's. Met rolscheiding maakt een beveiligingsbeheerder de fysieke sleutels. De DBA maakt de kolomhoofdsleutel en kolomversleutelingssleutelmetagegevensobjecten, die de fysieke sleutels beschrijven, in de database. Tijdens dit proces heeft de beveiligingsbeheerder geen toegang tot de database nodig en heeft de DBA geen toegang tot de fysieke sleutels in plaintext nodig. 
  - Zie het artikel, [Sleutels beheren met rolscheiding](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) voor meer informatie. 

- Sla uw kolomhoofdsleutels op in Azure Key Vault voor eenvoudig beheer. Vermijd het gebruik van Windows Certificate Store (en in het algemeen gedistribueerde key store-oplossingen, in tegenstelling tot centrale key management oplossingen) die key management moeilijk maken. 

- Denk goed na over de afwegingen van het gebruik van meerdere sleutels (kolomhoofdsleutel of kolomversleutelingssleutels). Houd het aantal sleutels klein om de kosten voor sleutelbeheer te verlagen. Eén kolomhoofdsleutel en één kolomversleutelingssleutel per database is doorgaans voldoende in steady-state omgevingen (niet in het midden van een sleutelrotatie). Mogelijk hebt u extra sleutels nodig als u verschillende gebruikersgroepen hebt, elk met verschillende sleutels en toegang tot verschillende gegevens.  

- Kolommastersleutels roteren volgens uw nalevingsvereisten. Als u ook kolomversleutelingssleutels moet roteren, u overwegen om online versleuteling te gebruiken om de uitvaltijd van toepassingen te minimaliseren. 
  - Zie het artikel, [Prestatie- en Beschikbaarheidsoverwegingen](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Gebruik deterministische versleuteling als berekeningen (gelijkheid) op gegevens moeten worden ondersteund. Gebruik anders gerandomiseerde versleuteling. Vermijd het gebruik van deterministische versleuteling voor gegevenssets met lage entropie of gegevenssets met een algemeen bekende distributie. 

- Als u zich zorgen maakt over de toegang van derden tot uw gegevens zonder uw toestemming, moet u ervoor zorgen dat alle toepassingen en hulpprogramma's die toegang hebben tot de sleutels en gegevens in plaintext buiten Microsoft Azure Cloud worden uitgevoerd. Zonder toegang tot de sleutels, zal de derde partij geen manier hebben om de gegevens te decoderen, tenzij ze de versleuteling omzeilen.

- Always Encrypted ondersteunt niet gemakkelijk het verlenen van tijdelijke toegang tot de sleutels (en de beveiligde gegevens). Als u bijvoorbeeld de sleutels met een DBA moet delen om de DBA in staat te stellen bepaalde reinigingsbewerkingen uit te voeren op gevoelige en versleutelde gegevens. De enige manier om de toegang tot de gegevens van de DBA in te trekken, is door zowel de kolomversleutelingssleutels als de kolommastersleutels te roteren die de gegevens beschermen, wat een dure bewerking is. 

- Om toegang te krijgen tot de plaintext-waarden in versleutelde kolommen, moet een gebruiker toegang hebben tot de CMK die kolommen beschermt, die is geconfigureerd in het sleutelarchief met de CMK. De gebruiker moet ook de **definitie van een kolommastersleutel bekijken** en eventuele machtigingen voor de **kolomversleutelingssleuteldefinitie bekijken.**

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Controle over de toegang van gebruikers van toepassingen tot gevoelige gegevens via versleuteling

Versleuteling kan worden gebruikt als een manier om ervoor te zorgen dat alleen specifieke toepassingsgebruikers die toegang hebben tot cryptografische sleutels de gegevens kunnen bekijken of bijwerken.

**Hoe te implementeren:**

- Gebruik versleuteling op celniveau (CLE). Zie het artikel, [Een kolom met gegevens versleutelen](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) voor meer informatie. 
- Gebruik Altijd versleuteld, maar wees je bewust van de beperking ervan. De beperkingen staan hieronder vermeld.

**Aanbevolen procedures**

Bij gebruik van CLE:

- Beheer de toegang tot sleutels via SQL-machtigingen en -rollen. 

- Gebruik AES (AES 256 aanbevolen) voor gegevensversleuteling. Algoritmen, zoals RC4, DES en TripleDES, worden afgeschaft en mogen niet worden gebruikt vanwege bekende kwetsbaarheden. 

- Bescherm symmetrische sleutels met asymmetrische sleutels/certificaten (geen wachtwoorden) om te voorkomen dat 3DES wordt gebruikt. 

- Wees voorzichtig bij het migreren van een database met behulp van Cell-Level Encryption via export / import (bacpac bestanden). 
  - Zie het artikel [Aanbevelingen voor het gebruik van celniveauversleuteling in Azure SQL Database](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) over het voorkomen van het verliezen van sleutels bij het migreren van gegevens en voor andere richtlijnen voor best practices.

Houd er rekening mee dat Always Encrypted voornamelijk is ontworpen om gevoelige gegevens die worden gebruikt te beschermen tegen gebruikers met hoge bevoegdheden van Azure SQL Database (cloudoperators, DBA's) - zie [Gevoelige gegevens beschermen die worden gebruikt door gebruikers met een hoge bevoorrechte, onbevoegde bevoegdheid](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Wees u bewust van de volgende uitdagingen bij het gebruik van Always Encrypted om gegevens te beschermen tegen gebruikers van toepassingen:

- Standaard behouden alle Microsoft-clientstuurprogramma's die Always Encrypted ondersteunen een globale cache (één per toepassing) met kolomversleutelingssleutels. Zodra een clientstuurprogramma een versleutelingssleutel voor plaintext-kolommen verwerft door contact op te nemen met een sleutelarchief met een kolomhoofdsleutel, wordt de versleutelingssleutel voor de kolomindecache in de cache opgeslagen. Dit maakt het isoleren van gegevens van gebruikers van een multi-user applicatie uitdagend. Als uw toepassing zich voordoet als eindgebruikers wanneer ze interactie hebben met een sleutelarchief (zoals Azure Key Vault), wordt de cache na een query van een gebruiker gevuld met een kolomversleutelingssleutel, een volgende query die dezelfde sleutel vereist, maar wordt geactiveerd door een andere gebruiker, cached sleutel. Het stuurprogramma belt het sleutelarchief niet en controleert niet of de tweede gebruiker een toestemming heeft om toegang te krijgen tot de kolomversleutelingssleutel. Als gevolg hiervan kan de gebruiker de versleutelde gegevens zien, zelfs als de gebruiker geen toegang heeft tot de sleutels. Als u de isolatie van gebruikers binnen een toepassing voor meerdere gebruikers wilt bereiken, u kolomversleutelingssleutelcaches uitschakelen. Het uitschakelen van caching zal leiden tot extra overheadkosten, omdat het stuurprogramma contact moet opnemen met het sleutelarchief voor elke gegevensversleuteling of decryptiebewerking.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Bescherm gegevens tegen ongeautoriseerde weergave door gebruikers van toepassingen met behoud van de gegevensindeling
Een andere techniek om te voorkomen dat onbevoegde gebruikers gegevens bekijken, is het verdoezelen of maskeren van de gegevens met behoud van gegevenstypen en -indelingen om ervoor te zorgen dat gebruikerstoepassingen de gegevens kunnen blijven verwerken en weergeven.

**Hoe te implementeren:**

- Gebruik [Dynamic Data Masking](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) om tabelkolommen te verdoezelen.

> [!NOTE]
> Always Encrypted werkt niet met Dynamic Data Masking. Het is niet mogelijk om dezelfde kolom te versleutelen en te maskeren, wat betekent dat u prioriteit moet geven aan het beschermen van gegevens in gebruik versus het maskeren van de gegevens voor uw app-gebruikers via Dynamic Data Masking.

**Aanbevolen procedures**:

> [!NOTE]
> Dynamic Data Masking kan niet worden gebruikt om gegevens te beschermen tegen gebruikers met hoge bevoegdheden. Maskerbeleid is niet van toepassing op gebruikers met beheerderstoegang zoals db_owner.

- Sta app-gebruikers niet toe om ad-hocquery's uit te voeren (omdat ze mogelijk kunnen werken rond Dynamic Data Masking).  
  - Zie het artikel, [Het omzeilen van maskeren met behulp van gevolgtrekking of brute-force technieken](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) voor details.  

- Gebruik een goed toegangscontrolebeleid (via SQL-machtigingen, rollen, RLS) om gebruikersmachtigingen te beperken om updates in de gemaskerde kolommen uit te voeren. Als u een masker op een kolom maakt, worden updates van die kolom niet voorkomen. Gebruikers die gemaskerde gegevens ontvangen bij het opvragen van de gemaskerde kolom, kunnen de gegevens bijwerken als ze schrijfmachtigingen hebben.    

-  Dynamic Data Masking behoudt de statistische eigenschappen van de gemaskerde waarden niet. Dit kan gevolgen hebben voor queryresultaten (bijvoorbeeld query's die filteringspredicaten bevatten of joins op de gemaskerde gegevens).

## <a name="network-security"></a>Netwerkbeveiliging
Netwerkbeveiliging verwijst naar toegangsbesturingselementen en aanbevolen procedures om uw gegevens te beveiligen tijdens het transport naar Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Mijn client configureren om veilig verbinding te maken met Azure SQL Database 
Aanbevolen procedures voor het voorkomen dat clientmachines en toepassingen met bekende kwetsbaarheden (bijvoorbeeld met oudere TLS-protocollen en ciphersuites) verbinding maken met Azure SQL Database.

**Hoe te implementeren:**

- Controleer of clientmachines die verbinding maken met Azure SQL Database [tls (Transport Layer Security)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)gebruiken.

**Aanbevolen procedures**:

- Configureer al uw apps en hulpprogramma's om verbinding te maken met SQL DB met ingeschakelde versleuteling 
  - Versleutelen = Aan, TrustServerCertificate = Uit (of gelijkwaardig met niet-Microsoft-stuurprogramma's). 

- Als uw app een stuurprogramma gebruikt dat TLS niet ondersteunt of een oudere versie van TLS ondersteunt, vervangt u het stuurprogramma indien mogelijk. Indien niet mogelijk, zorgvuldig evalueren van de veiligheidsrisico's. 

- Verminder aanvalsvectoren via kwetsbaarheden in SSL 2.0, SSL 3.0, TLS 1.0 en TLS 1.1 door ze uit te schakelen op clientmachines die verbinding maken met [tls-registerinstellingen (Azure](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)SQL Database per Transport Layer Security). 

- Controleer cipher suites beschikbaar op de client: [Cipher Suites in TLS / SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). Schakel met name 3DES per [configuring TLS Cipher Suite Order](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)uit. 

- Voor Azure SQL Database wordt versleuteling afgedwongen voor zowel proxy- als omleidingsverbindingstypen. Als u een beheerde instantie gebruikt, gebruikt u het type **proxyverbinding** (standaard) omdat hiermee versleuteling van de serverzijde wordt afgedwongen. Het type **omleidingsverbinding** ondersteunt momenteel geen versleutelingshandhaving en is alleen beschikbaar op privé-IP-verbindingen. 

- Zie [Azure SQL Connectivity Architecture - Verbindingsbeleid](sql-database-connectivity-architecture.md#connection-policy)voor meer informatie.


### <a name="minimize-attack-surface"></a>Aanvalsoppervlak minimaliseren
Minimaliseer het aantal functies dat kan worden aangevallen door een kwaadwillende gebruiker. Netwerktoegangsbesturingselementen voor Azure SQL Database implementeren.

> Vermeld in: OSA Practice #5

**Hoe te implementeren:**

In een Azure SQL Database-server (met singleton-database of elastische pools):
- Stel Toegang tot Azure-services toestaan in in UIT.

- Gebruik VNet-serviceeindpunten en VNet Firewall-regels.

- Privékoppeling gebruiken (voorbeeld).

In een beheerde instantie:
- Volg de richtlijnen in [netwerkvereisten.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) 

**Aanbevolen procedures**:

- Toegang tot Azure SQL Database beperken door verbinding te maken op een privéeindpunt (bijvoorbeeld via een privégegevenspad): 
  - Een beheerde instantie kan worden geïsoleerd in een VNet om externe toegang te voorkomen. Toepassingen en hulpprogramma's die zich in dezelfde of peered VNet in dezelfde regio bevinden, kunnen er rechtstreeks toegang toe krijgen. Toepassingen en hulpprogramma's die zich in verschillende regio's bevinden, kunnen VNet-to-VNet-verbinding of ExpressRoute-circuitpeering gebruiken om verbinding te maken. De klant moet Network Security Groups (NSG) gebruiken om de toegang via poort 1433 alleen te beperken tot bronnen die toegang tot een beheerde instantie vereisen 
  - Voor een SQL Database-server (met enkele databases of elastische groepen) gebruikt u de [functie Private Link](sql-database-private-endpoint-overview.md) die een specifiek privé-IP biedt voor de SQL Database-server in uw VNet. U [vnetservice-eindpunten met VNet Firewall-regels](sql-database-vnet-service-endpoint-rule-overview.md) ook gebruiken om de toegang tot uw SQL Database-servers te beperken.
  - Mobiele gebruikers moeten point-to-site VPN-verbindingen gebruiken om verbinding te maken via het datapad.
  - Gebruikers die zijn verbonden met hun on-premises netwerk, moeten een VPN-verbinding van site-to-site of ExpressRoute gebruiken om verbinding te maken via het gegevenspad.

- U hebt toegang tot Azure SQL Database door verbinding te maken met een openbaar eindpunt (bijvoorbeeld via een openbaar gegevenspad). De volgende beste praktijken moeten in aanmerking worden genomen: 
  - Voor een SQL Database-server gebruikt u [IP-firewallregels](sql-database-firewall-configure.md) om de toegang tot alleen geautoriseerde IP-adressen te beperken.
  - Gebruik voor een beheerde instantie Network Security Groups (NSG) om de toegang via poort 3342 alleen te beperken tot vereiste resources. Zie [Een azure SQL Database-exemplaar dat wordt beheerd met openbare eindpunten veilig gebruiken voor](sql-database-managed-instance-public-endpoint-securely.md)meer informatie. 

> [!NOTE]
> Een beheerd openbaar eindpunt voor een instantie is niet standaard ingeschakeld en moet expliciet zijn ingeschakeld. Als het bedrijfsbeleid het gebruik van openbare eindpunten verbiedt, gebruikt u [Azure Policy](../governance/policy/overview.md) om te voorkomen dat openbare eindpunten in de eerste plaats worden in- of inschakelt.

- Azure Networking-componenten instellen: 
  - Volg [Azure best practices voor netwerkbeveiliging.](../security/fundamentals/network-best-practices.md)
  - Plan Virtual Network (VNet)-configuratie per aanbevolen procedures die worden beschreven in [Azure Virtual Network veelgestelde vragen (FAQ)](../virtual-network/virtual-networks-faq.md) en plannen. 
  - Segmenteer een VNet in meerdere subnetten en wijs resources voor vergelijkbare rol toe aan hetzelfde subnet (bijvoorbeeld front-end vs back-end resources).
  - Gebruik [NsGs (Network Security Groups)](../virtual-network/security-overview.md) om het verkeer tussen subnetten binnen de Azure VNet-grens te regelen.
  - Schakel [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) in voor uw abonnement om inkomend en uitgaand netwerkverkeer te controleren.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Power BI configureren voor beveiligde verbindingen met Azure SQL Database

**Aanbevolen procedures**:

- Gebruik voor Power BI Desktop waar mogelijk privégegevenspad. 

- Controleer of Power BI Desktop verbinding maakt met TLS1.2 door de registersleutel op de clientmachine in te stellen volgens [tls-registerinstellingen (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 

- Gegevenstoegang voor specifieke gebruikers beperken via [Row-level security (RLS) met Power BI.](https://docs.microsoft.com/power-bi/service-admin-rls) 

- Gebruik voor Power [BI-service de on-premises gegevensgateway,](https://docs.microsoft.com/power-bi/service-gateway-onprem)rekening houdend met [beperkingen en overwegingen.](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>App-service configureren voor beveiligde verbindingen met Azure SQL Database

**Aanbevolen procedures**:

- Voor een eenvoudige web-app vereist het maken van verbinding via een openbaar eindpunt **dat Azure Services** toestaan aan AAN. 

- [Integreer uw app met een Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md) voor verbinding met privégegevenspad met een beheerde instantie. Optioneel u ook een web-app met [App Service Environments (ASE)](../app-service/environment/intro.md)implementeren. 

- Voor Web App met ASE- of VNet Integrated Web App die verbinding maakt met een database in SQL Database-server, u [VNet Service-eindpunten en VNet Firewall-regels](sql-database-vnet-service-endpoint-rule-overview.md) gebruiken om de toegang vanaf een specifiek VNet en subnet te beperken. Stel Azure **Services vervolgens in** om uit te gaan. U ASE ook verbinden met een beheerde instantie via een privégegevenspad.  

- Controleer of uw web-app is geconfigureerd volgens het artikel, [Aanbevolen procedures voor het beveiligen van PaaS-web- en mobiele toepassingen met Azure App Service.](../security/security-paas-applications-using-app-services.md) 

- Installeer [Web Application Firewall (WAF)](../application-gateway/waf-overview.md) om uw web-app te beschermen tegen veelvoorkomende exploits en kwetsbaarheden.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Azure VM-hosting configureren voor beveiligde verbindingen met Azure SQL Database

**Aanbevolen procedures**:

- Gebruik een combinatie van Regels voor toestaan en weigeren op de NSG's van Azure VM's om te bepalen welke regio's vanaf de VM kunnen worden geopend.

- Controleer of uw VM is geconfigureerd volgens het artikel, [aanbevolen procedures voor beveiliging voor IaaS-workloads in Azure.](../security/azure-security-iaas.md)

- Zorg ervoor dat alle VM's zijn gekoppeld aan een specifiek VNet en subnet. 

- Evalueren of u de standaardroute 0.0.0.0/Internet per de richtlijnen nodig hebt bij [ongeveer gedwongen tunneling.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling) 
  - Zo ja , bijvoorbeeld front-end subnet - houd dan de standaard route.
  - Als er bijvoorbeeld geen middenlaag of back-endsubnet is – kun je forcetunneling inschakelen, zodat er geen verkeer via internet naar on-premises (alias cross-premises) gaat. 

- Implementeer [optionele standaardroutes](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) als u peering gebruikt of verbinding maakt met on-premises. 

- Implementeer [door de gebruiker gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md#user-defined) als u al het verkeer in het VNet naar een netwerkvirtueel toestel moet verzenden voor pakketinspectie. 

- Gebruik [VNet Service-eindpunten](sql-database-vnet-service-endpoint-rule-overview.md) voor veilige toegang tot PaaS-services zoals Azure Storage via het Azure-backbonenetwerk. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Beschermen tegen DDoS-aanvallen (Distributed Denial of Service)
Distributed Denial of Service (DDoS)-aanvallen zijn pogingen van een kwaadwillende gebruiker om een stortvloed aan netwerkverkeer naar Azure SQL Database te sturen met als doel de Azure-infrastructuur te overweldigen en geldige aanmeldingen en werkbelasting af te wijzen.

> Vermeld in: OSA Practice #9

**Hoe te implementeren:**

DDoS-beveiliging wordt automatisch ingeschakeld als onderdeel van het Azure-platform. Het omvat always-on verkeersbewaking en real-time mitigatie van aanvallen op netwerkniveau op openbare eindpunten. 

- Gebruik [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) om te controleren of openbare IP-adressen zijn gekoppeld aan resources die in VNets zijn geïmplementeerd.

- Gebruik [Advanced Threat Protection voor Azure SQL Database](sql-database-threat-detection-overview.md) om DoS-aanvallen (Denial of Service) op databases te detecteren.

**Aanbevolen procedures**:

- Volg de praktijken die in [Minimize Attack Surface](#minimize-attack-surface) worden beschreven en minimaliseert DDoS-aanvalsbedreigingen. 

- De Advanced Threat Protection **Brute force SQL-referenties** waarschuwing helpt bij het detecteren van brute force-aanvallen. In sommige gevallen kan de waarschuwing zelfs onderscheid maken tussen workloads voor penetratietests. 

- Voor Azure VM-hostingtoepassingen die verbinding maken met SQL Database: 
  - Volg aanbeveling om de toegang te beperken via op internet gerichte eindpunten in Azure Security Center. 
  - Gebruik virtuele machineschaalsets om meerdere exemplaren van uw toepassing uit te voeren op Azure VM's. 
  - Schakel RDP en SSH uit van internet om brute force-aanvallen te voorkomen. 

## <a name="monitoring-logging-and-auditing"></a>Controleren, loggen en controleren  
Deze sectie verwijst naar mogelijkheden om u te helpen bij het detecteren van afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen aangeven om toegang te krijgen tot databases of deze te exploiteren. Het beschrijft ook best practices voor het configureren van databasecontrole om databasegebeurtenissen bij te houden en vast te leggen.

### <a name="protect-databases-against-attacks"></a>Databases beschermen tegen aanvallen 
Geavanceerde bescherming van bedreigingen stelt u in staat om potentiële bedreigingen te detecteren en erop te reageren wanneer deze zich voordoen door beveiligingswaarschuwingen te verstrekken voor afwijkende activiteiten.

**Hoe te implementeren:**

- Gebruik [Advanced Threat Protection voor SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) om ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot of misbruik te maken van databases op te sporen, waaronder:
  - SQL-injectieaanval.
  - Referenties diefstal / lekken.
  - Privilege misbruik.
  - Data exfiltratie.

**Aanbevolen procedures**:

- Configureer [Advanced Data Security (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) voor Azure SQL Database voor een specifieke SQL Database-server of een beheerde instantie. U ook ADS configureren voor alle SQL Database-servers en beheerde exemplaren in een abonnement door over te schakelen naar [Azure Security Center Standard.](../security-center/security-center-pricing.md) 

- Voor een volledige onderzoekservaring wordt het aanbevolen om [SQL Database Auditing](sql-database-auditing.md)in te schakelen. Met controle u databasegebeurtenissen bijhouden en naar een controlelogboek in een Azure Storage-account of Azure Log Analytics-werkruimte schrijven. 

### <a name="audit-critical-security-events"></a>Kritieke beveiligingsgebeurtenissen controleren
Het bijhouden van databasegebeurtenissen helpt u inzicht te krijgen in de databaseactiviteit. U inzicht krijgen in discrepanties en afwijkingen die kunnen wijzen op zakelijke problemen of vermoedelijke beveiligingsschendingen. Het maakt ook naleving van nalevingsnormen mogelijk en vergemakkelijkt dit. 

**Hoe te implementeren:**

- Sql [Database Auditing](sql-database-auditing.md) inschakelen om databasegebeurtenissen bij te houden en deze te schrijven naar een auditlogboek in uw Azure Storage-account, Log Analytics-werkruimte (voorbeeld) of Gebeurtenishubs (voorbeeld). 

- Controlelogboeken kunnen worden geschreven naar een Azure Storage-account, naar een Log Analytics-werkruimte voor verbruik door Azure Monitor-logboeken of naar gebeurtenishub voor verbruik met gebeurtenishub. U elke combinatie van deze opties configureren en er worden controlelogboeken naar elk geschreven. 

**Aanbevolen procedures**:

- Door [SQL Database Auditing](sql-database-auditing.md) op uw databaseserver te configureren om gebeurtenissen te controleren, worden alle bestaande en nieuw gemaakte databases op die server gecontroleerd.
- Standaard controlebeleid omvat alle acties (query's, opgeslagen procedures en succesvolle en mislukte aanmeldingen) tegen de databases, wat kan resulteren in een groot aantal controlelogboeken. Het wordt aanbevolen voor klanten om controle te [configureren voor verschillende soorten acties en actiegroepen met PowerShell.](sql-database-auditing.md#subheading-7) Door dit te configureren, u het aantal gecontroleerde acties beheren en het risico op gebeurtenisverlies minimaliseren. Met een aangepaste controleconfiguratie kunnen klanten alleen de benodigde controlegegevens vastleggen.
- Controlelogboeken kunnen rechtstreeks worden verbruikt in de [Azure-portal](https://portal.azure.com/)of vanaf de opslaglocatie die is geconfigureerd. 


> [!NOTE]
> Als u controle inschakelt in Log Analytics, worden kosten verbonden op basis van opnamepercentages. Houd rekening met de bijbehorende kosten voor het gebruik van deze [optie](https://azure.microsoft.com/pricing/details/monitor/)of overweeg de controlelogboeken op te slaan in een Azure-opslagaccount. 

**Verdere middelen**:

- [SQL-databasecontrole](sql-database-auditing.md)
- [SQL Server-controle](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Beveiligde controlelogboeken
Beperk de toegang tot het opslagaccount ter ondersteuning van de scheiding van taken en om DBA van auditors te scheiden. 

**Hoe te implementeren:**

- Controleer bij het opslaan van controlelogboeken op Azure Storage of de toegang tot het opslagaccount beperkt is tot de minimale beveiligingsprincipes. Bepaal wie toegang heeft tot het opslagaccount.
    - Zie [Toegang tot Azure Storage toestaan](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie.

**Aanbevolen procedures**:

- Het beheren van de toegang tot de auditdoelstelling is een belangrijk concept bij het scheiden van DBA van auditors. 

- Bij het controleren van de toegang tot gevoelige gegevens u overwegen de gegevens te beveiligen met gegevensversleuteling om te voorkomen dat informatie naar de auditor wordt gelekt. Zie voor meer informatie de sectie [Gevoelige gegevens beveiligen die worden gebruikt van gebruikers met een hoge bevoorrechte, onbevoegde gebruikers.](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)

## <a name="security-management"></a>Beveiligingsbeheer

In dit gedeelte worden de verschillende aspecten en aanbevolen procedures voor het beheren van uw beveiligingshouding in databases beschreven. Het bevat aanbevolen procedures om ervoor te zorgen dat uw databases zijn geconfigureerd om te voldoen aan beveiligingsstandaarden, voor het ontdekken en bijhouden van toegang tot mogelijk gevoelige gegevens in uw databases. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Ervoor zorgen dat de database(s) zijn geconfigureerd om te voldoen aan de aanbevolen beveiligingsprocedures 

Verbeter uw databasebeveiliging proactief door potentiële databasekwetsbaarheden te ontdekken en te herstellen.

**Hoe te implementeren:**

- Schakel [SQL Vulnerability Assessment](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) in om uw database te scannen op beveiligingsproblemen en om automatisch periodiek op uw databases uit te voeren.

**Aanbevolen procedures**:

- Voer in eerste instantie VA uit op uw databases en verwerk door falende controles te verwerken die zich verzetten tegen best practices voor beveiliging. Stel basislijnen in voor acceptabele configuraties totdat de scan _schoon_uitkomt of alle controles zijn geslaagd.  

- Configureer periodieke terugkerende scans om één keer per week uit te voeren en configureer de relevante persoon om overzichtse-mails te ontvangen. 

- Bekijk de VA-samenvatting na elke wekelijkse scan. Voor eventuele gevonden kwetsbaarheden, evalueren van de afwijking van de vorige scan resultaat en bepalen of de controle moet worden opgelost. Controleer of er een legitieme reden is voor de wijziging in de configuratie.   

- Werk controles op en werk waar relevant basislijnen bij. Maak ticketitems voor het oplossen van acties en houd deze bij totdat ze zijn opgelost. 

**Verdere middelen**:

- [Evaluatie van SQL-beveiligingsproblemen](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [SQL Vulnerability Assessment-service helpt u bij het identificeren van databasekwetsbaarheden](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Gevoelige gegevens identificeren en taggen 

Ontdek kolommen die mogelijk gevoelige gegevens bevatten. Wat wordt beschouwd als gevoelige gegevens hangt sterk af van de klant, naleving regelgeving, enz., en moet worden geëvalueerd door de gebruikers die verantwoordelijk zijn voor die gegevens. Classificeer de kolommen om geavanceerde controle- en beveiligingsscenario's op basis van gevoeligheid te gebruiken. 

**Hoe te implementeren:**

- Gebruik [SQL-gegevensdetectie en -classificatie](sql-database-data-discovery-and-classification.md) om de gevoelige gegevens in uw databases te ontdekken, te classificeren, te labelen en te beschermen. 
  - Bekijk de classificatieaanbevelingen die zijn gemaakt door de geautomatiseerde detectie in het dashboard SQL Data Discovery en Classification. Accepteer de relevante classificaties, zodat uw gevoelige gegevens voortdurend worden getagd met classificatielabels. 
  - Voeg handmatig classificaties toe voor extra gevoelige gegevensvelden die niet door het geautomatiseerde mechanisme zijn ontdekt. 
- Zie [SQL Data Discovery & Classificatie](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)voor meer informatie .

**Aanbevolen procedures**:

- Controleer het classificatiedashboard regelmatig voor een nauwkeurige beoordeling van de classificatiestatus van de database. Een rapport over de databaseclassificatiestatus kan worden geëxporteerd of afgedrukt om te delen voor nalevings- en controledoeleinden.

- Controleer continu de status van aanbevolen gevoelige gegevens in SQL Vulnerability Assessment. Houd de regel voor het ontdekken van gevoelige gegevens bij en identificeer elke afwijking in de aanbevolen kolommen voor classificatie.  

- Gebruik classificatie op een manier die is afgestemd op de specifieke behoeften van uw organisatie. Pas uw beleid voor informatiebescherming (gevoeligheidslabels, informatietypen, detectielogica) aan in het [SQL Information Protection-beleid](../security-center/security-center-info-protection-policy.md) in Azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Toegang tot gevoelige gegevens bijhouden 
Controleer wie toegang heeft tot gevoelige gegevens en leg query's vast op gevoelige gegevens in auditlogs.

**Hoe te implementeren:**

- Gebruik SQL Audit en Data Classification in combinatie. 
  - In uw [SQL Database Audit-logboek](sql-database-auditing.md) u de toegang tot gevoelige gegevens specifiek bijhouden. U ook informatie bekijken, zoals de gegevens die zijn geopend, evenals het gevoeligheidslabel. Zie [Toegang tot gevoelige gegevens controleren voor](sql-database-data-discovery-and-classification.md#subheading-3)meer informatie . 

**Aanbevolen procedures**:

- Zie aanbevolen procedures voor de secties Auditing en gegevensclassificatie: 
  - [Kritieke beveiligingsgebeurtenissen controleren](#audit-critical-security-events) 
  - [Gevoelige gegevens identificeren en taggen](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Beveiligings- en nalevingsstatus visualiseren 

Gebruik een uniform systeem voor beveiligingsbeheer voor infrastructuur dat de beveiligingspositie van uw datacenters (inclusief SQL-databases) versterkt. Bekijk een lijst met aanbevelingen met betrekking tot de beveiliging van uw databases en de nalevingsstatus.

**Hoe te implementeren:**

- Monitor SQL-gerelateerde beveiligingsaanbevelingen en actieve bedreigingen in [Azure Security Center.](https://azure.microsoft.com/documentation/services/security-center/)

## <a name="common-security-threats-and-potential-mitigations"></a>Gemeenschappelijke bedreigingen van de veiligheid en mogelijke oplossingen

Met deze sectie u beveiligingsmaatregelen vinden om te beschermen tegen bepaalde aanvalsvectoren. De verwachting is dat de meeste oplossingen kunnen worden bereikt door het volgen van een of meer van de bovenstaande beveiligingsrichtlijnen.

### <a name="security-threat-data-exfiltration"></a>Bedreiging van de beveiliging: Gegevensexfiltratie

Gegevensexfiltratie is het onbevoegd kopiëren, overdragen of ophalen van gegevens van een computer of server. Zie een definitie voor [gegevensexfiltratie](https://en.wikipedia.org/wiki/Data_exfiltration) op Wikipedia.

Verbinding maken met Azure SQL Database-server via een openbaar eindpunt brengt een risico voor gegevensexfiltratie met zich mee, omdat klanten hun firewalls moeten openen voor openbare IP's.  

**Scenario 1**: Een toepassing op een Azure VM maakt verbinding met een database in een Azure SQL Database-server. Een malafide acteur krijgt toegang tot de VM en compromitteert deze. In dit scenario betekent gegevensexfiltratie dat een externe entiteit die de malafide VM gebruikt, verbinding maakt met de database, persoonlijke gegevens kopieert en opslaat in een blob-opslag of een andere SQL-database in een ander abonnement.

**Scenario 2**: Een Rouge DBA. Dit scenario wordt vaak aangekaart door beveiligingsgevoelige klanten uit gereguleerde industrieën. In dit scenario kan een gebruiker met hoge bevoegdheden gegevens uit Azure SQL Database kopiëren naar een ander abonnement dat niet door de gegevenseigenaar wordt beheerd.

**Mogelijke oplossingen:**

Tegenwoordig biedt Azure SQL Database de volgende technieken voor het beperken van bedreigingen voor gegevensexfiltratie: 

- Gebruik een combinatie van Regels voor toestaan en weigeren op de NSG's van Azure VM's om te bepalen welke regio's vanaf de VM kunnen worden geopend. 
- Als u een Azure SQL Database-server gebruikt (met singleton-databases of elastische groepen), stelt u de volgende opties in:
  - Azure Services toestaan om UIT te maken.
  - Sta alleen verkeer toe vanaf het subnet dat uw Azure VM bevat door een VNet Firewall-regel in te stellen.
  - [Privékoppeling gebruiken](sql-database-private-endpoint-overview.md)
- Voor een beheerde instantie, met behulp van private IP-toegang standaard adressen de eerste gegevens exfiltratie zorg van een malafide VM. Schakel de subnetdelegatiefunctie in op een subnet om automatisch het meest beperkende beleid in te stellen op een beheerd instantiesubnet.
- De Rogue DBA zorg is meer blootgesteld met een beheerde instantie als het heeft een groter oppervlak en netwerkeisen zichtbaar zijn voor klanten. De beste mitigatie hiervoor is het toepassen van alle praktijken in deze beveiligingsgids om het Rogue DBA-scenario in de eerste plaats te voorkomen (niet alleen voor gegevensexfiltratie). Always Encrypted is een methode om gevoelige gegevens te beschermen door deze te versleutelen en de sleutel ontoegankelijk te houden voor de DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Beveiligingsaspecten van bedrijfscontinuïteit en beschikbaarheid

De meeste beveiligingsstandaarden hebben betrekking op de beschikbaarheid van gegevens in termen van operationele continuïteit, bereikt door het implementeren van redundantie en fail-over mogelijkheden om single points of failure te voorkomen. Voor rampscenario's is het gebruikelijk om back-ups van gegevens- en logboekbestanden te bewaren.In de volgende sectie vindt u een overzicht op hoog niveau van de mogelijkheden die zijn ingebouwd in Azure. Het biedt ook extra opties die kunnen worden geconfigureerd om te voldoen aan specifieke behoeften: 

- Azure biedt ingebouwde hoge beschikbaarheid: [hoge beschikbaarheid en Azure SQL Database](sql-database-high-availability.md) 

- De laag Bedrijfskritieke omvat failovergroepen, multi-beschikbaarheidszones, volledige en differentiële logboekbackups en point-in-time-back-ups voor herstel die standaard zijn ingeschakeld:  
  - [Hoge beschikbaarheid en Azure SQL Database - Redundante zoneconfiguratie](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatische back-ups](sql-database-automated-backups.md)
  - [Een Azure SQL-database herstellen met geautomatiseerde databaseback-ups - Point-in-time restore](sql-database-recovery-using-backups.md#point-in-time-restore)

- Aanvullende functies voor bedrijfscontinuïteit, zoals automatische failovergroepen in verschillende Azure-geo's, kunnen worden geconfigureerd zoals hier beschreven: [Overzicht van bedrijfscontinuïteit met Azure SQL Database](sql-database-business-continuity.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [Een overzicht van azure SQL Database-beveiligingsmogelijkheden](sql-database-security-overview.md)
