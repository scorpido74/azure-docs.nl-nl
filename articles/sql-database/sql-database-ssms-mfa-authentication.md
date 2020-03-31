---
title: Multi-factor AAD-verificatie gebruiken
description: Azure SQL Database en Azure Synapse ondersteunen verbindingen vanuit SQL Server Management Studio (SSMS) met Active Directory Universal Authentication.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255871"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>Multi-factor AAD-verificatie gebruiken met Azure SQL Database en Azure Synapse Analytics (SSMS-ondersteuning voor MFA)
Azure SQL Database en Azure Synapse ondersteunen verbindingen vanuit SQL Server Management Studio (SSMS) met behulp van *Active Directory Universal Authentication*. In dit artikel worden de verschillen tussen de verschillende verificatieopties en ook de beperkingen van het gebruik van universele verificatie besproken. 

**Download de nieuwste SSMS** - Download op de clientcomputer de nieuwste versie van SSMS, van [Download SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/mt238290.aspx) 


Voor alle functies die in dit artikel worden besproken, gebruikt u ten minste juli 2017, versie 17.2.  Het meest recente dialoogvenster verbinding moet er hetzelfde uitzien als de volgende afbeelding:
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Vult het vak Gebruikersnaam in.")  

## <a name="the-five-authentication-options"></a>De vijf verificatieopties  

Active Directory Universal Authentication ondersteunt de twee niet-interactieve verificatiemethoden:
    - `Active Directory - Password`Verificatie
    - `Active Directory - Integrated`Verificatie

Er zijn ook twee niet-interactieve authenticatiemodellen, die in veel verschillende toepassingen kunnen worden gebruikt (ADO.NET, JDCB, ODC, enz.). Deze twee methoden resulteren nooit in pop-updialoogvensters: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

De interactieve methode is dat ondersteunt ook Azure multi-factor authenticatie (MFA) is: 
- `Active Directory - Universal with MFA` 


Azure MFA helpt bij het bewaken van de toegang tot uw gegevens en toepassingen en komt tegemoet aan de wensen van gebruikers die een eenvoudige aanmeldprocedure willen. Het levert sterke authenticatie met een reeks eenvoudige verificatieopties (telefoongesprek, sms, smartcards met pin of mobiele app-melding), zodat gebruikers de methode kunnen kiezen die ze verkiezen. Interactieve MFA met Azure AD kan resulteren in een pop-updialoogvenster voor validatie.

Zie [Multifactorauthenticatie](../active-directory/authentication/multi-factor-authentication.md)voor een beschrijving van multifactorauthenticatie.
Zie [Multifactorverificatie van Azure SQL Database configureren voor SQL Server Management Studio voor](sql-database-ssms-mfa-authentication-configure.md)configuratiestappen.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domeinnaam of tenant-id-parameter   

Vanaf [SSMS-versie 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kunnen gebruikers die als gastgebruikers in de huidige Active Directory worden geïmporteerd als gastgebruikers, de Azure AD-domeinnaam of de tenant-id opgeven wanneer ze verbinding maken. Gastgebruikers zijn gebruikers die zijn uitgenodigd uit andere Azure-a-d's, Microsoft-accounts zoals outlook.com, hotmail.com, live.com of andere accounts zoals gmail.com. Met deze informatie kan **Active Directory Universal met MFA-verificatie** de juiste authenticerende autoriteit identificeren. Deze optie is ook vereist om Microsoft-accounts (MSA) te ondersteunen, zoals outlook.com, hotmail.com, live.com of niet-MSA-accounts. Al deze gebruikers die willen worden geverifieerd met behulp van Universele verificatie, moeten hun Azure AD-domeinnaam of tenant-id invoeren. Deze parameter vertegenwoordigt de huidige Azure AD-domeinnaam/-tenant-id waarmee Azure Server is gekoppeld. Als Azure Server bijvoorbeeld is gekoppeld `contosotest.onmicrosoft.com` aan `joe@contosodev.onmicrosoft.com` het Azure AD-domein waarbij de `contosodev.onmicrosoft.com`gebruiker wordt gehost als geïmporteerde gebruiker uit Azure AD-domein, is de domeinnaam die nodig is `contosotest.onmicrosoft.com`om deze gebruiker te verifiëren . Wanneer de gebruiker een native gebruiker is van het Azure AD dat is gekoppeld aan Azure Server en geen MSA-account is, is er geen domeinnaam of tenant-id vereist. Als u de parameter (te beginnen met SSMS-versie 17.2) wilt invoeren, voert u in het dialoogvenster Verbinding maken met **database** het dialoogvenster In, **AD domain name or tenant ID** selecteert u **Active Directory - Universeel met MFA-verificatie,** klikt u op **Opties,** vult u het vak **Gebruikersnaam** in en klikt u vervolgens op het tabblad **Verbindingseigenschappen** **contosotest.onmicrosoft.com.**  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Als u SSMS 18.x of hoger uitvoert, is de AD-domeinnaam of tenant-id niet langer nodig voor gastgebruikers, omdat 18.x of hoger deze automatisch herkent.

   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Ondersteuning voor Azure AD-business naar business   
Azure AD-gebruikers die worden ondersteund voor Azure AD B2B-scenario's als gastgebruikers (zie [Wat is Azure B2B-samenwerking)](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)kunnen alleen verbinding maken met SQL Database `CREATE USER` en Azure Synapse als onderdeel van leden van een groep die is gemaakt in het huidige Azure AD en handmatig in kaart zijn gebracht met de transact-SQL-instructie in een bepaalde database. `steve@gmail.com` Als u bijvoorbeeld wordt uitgenodigd `contosotest` voor Azure AD `contosotest.onmicrosoft.com`(met het Azure `usergroup` Ad-domein), moet een Azure `steve@gmail.com` AD-groep, zoals moet worden gemaakt in het Azure AD dat het lid bevat, worden gemaakt. Vervolgens moet deze groep worden gemaakt voor een specifieke database (dat wil zeggen MyDatabase) door Azure AD `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` SQL-beheerder of Azure AD DBO door een Transact-SQL-instructie uit te voeren. Nadat de databasegebruiker is gemaakt, kan de gebruiker `steve@gmail.com` zich aanmelden bij `MyDatabase` de optie `Active Directory – Universal with MFA support`SSMS-verificatie. De gebruikersgroep heeft standaard alleen de verbindingsmachtiging en eventuele verdere gegevenstoegang die op de normale manier moet worden verleend. Houd er `steve@gmail.com` rekening mee dat de gebruiker als gastgebruiker het selectievakje moet inschakelen en de AD-domeinnaam `contosotest.onmicrosoft.com` moet toevoegen in het dialoogvenster SSMS-verbindingseigenschap. **Connection Property** De **optie AD-domeinnaam of tenant-id** wordt alleen ondersteund voor de universele met MFA-verbindingsopties, anders wordt deze grijs weergegeven.

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>Universele verificatiebeperkingen voor SQL Database en Azure Synapse
- SSMS en SqlPackage.exe zijn de enige tools die momenteel zijn ingeschakeld voor MFA via Active Directory Universal Authentication.
- SSMS versie 17.2 ondersteunt gelijktijdige toegang voor meerdere gebruikers met behulp van universele verificatie met MFA. Versie 17.0 en 17.1 beperkten een aanmelding voor een instantie van SSMS met behulp van universele verificatie tot één Azure Active Directory-account. Als u zich wilt aanmelden als een ander Azure AD-account, moet u een ander exemplaar van SSMS gebruiken. (Deze beperking is beperkt tot Active Directory Universal Authentication; u zich aanmelden bij verschillende servers met Active Directory Password Authentication, Active Directory Integrated Authentication of SQL Server Authentication).
- SSMS ondersteunt Active Directory Universal Authentication voor Object Explorer, Query Editor en Query Store visualisatie.
- SSMS versie 17.2 biedt DacFx Wizard-ondersteuning voor export/extract/deploy-gegevensdatabase. Zodra een specifieke gebruiker is geverifieerd via het dialoogvenster eerste verificatie met behulp van Universele verificatie, functioneert de DacFx Wizard op dezelfde manier als voor alle andere verificatiemethoden.
- De SSMS Table Designer biedt geen ondersteuning voor universele verificatie.
- Er zijn geen aanvullende softwarevereisten voor Active Directory Universal Authentication, behalve dat u een ondersteunde versie van SSMS moet gebruiken.  
- De ADAL-versie (Active Directory Authentication Library) voor universele verificatie is bijgewerkt naar de nieuwste adal.dll 3.13.9 beschikbare versie. Zie [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Volgende stappen

- Zie [Multifactorverificatie van Azure SQL Database configureren voor SQL Server Management Studio voor](sql-database-ssms-mfa-authentication-configure.md)configuratiestappen.
- Anderen toegang verlenen tot uw database: [SQL Database Authentication and Authorization: Access](sql-database-manage-logins.md)  
- Zorg ervoor dat anderen verbinding kunnen maken via de firewall: [een firewallregel op Azure SQL Database-serverniveau configureren met behulp van de Azure-portal](sql-database-configure-firewall-settings.md)  
- [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Een BACPAC-bestand importeren in een nieuwe Azure SQL-database](../sql-database/sql-database-import.md)  
- [Een Azure SQL-database exporteren naar een BACPAC-bestand](../sql-database/sql-database-export.md)  
- C# interface [IUniversalAuthProvider Interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Bij gebruik van **Active Directory- Universal met MFA-verificatie** is ADAL-tracering beschikbaar vanaf [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Standaard u ADAL-tracering inschakelen met het menu **Extra**, **Opties,** onder **Azure Services**, **Azure Cloud**, **ADAL Output Window Trace Level,** gevolgd door **Uitvoer** in te schakelen in het menu **Weergave.** De traces zijn beschikbaar in het uitvoervenster bij het selecteren van **de Azure Active Directory-optie**.  
