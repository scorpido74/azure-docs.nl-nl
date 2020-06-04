---
title: Multi-factor Azure Active Directory-verificatie gebruiken
description: Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics ondersteunen verbindingen van SQL Server Management Studio (SSMS) met behulp van Active Directory universele verificatie.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 04/23/2020
tags: azure-synapse
ms.openlocfilehash: 607644a67fad966be25915e55a1bec83d70a1fe0
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344812"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Multi-factor Azure Active Directory-verificatie gebruiken
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure Managed instance en Azure Synapse Analytics ondersteunen verbindingen van SQL Server Management Studio (SSMS) met behulp van *Azure Active Directory-Universal met MFA-* verificatie. In dit artikel worden de verschillen beschreven tussen de verschillende verificatie-opties, evenals de beperkingen van universele verificatie.

**Download de nieuwste versie van SSMS** Download op de clientcomputer de meest recente versie van SSMS. Ga hiervoor naar de pagina [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio (SSMS) downloaden).

Voor alle functies die in dit artikel worden besproken, hebt u ten minste versie 17.2 van juli 2017 nodig. Het meest recente dialoogvenster Verbinding maken met server moet er ongeveer uitzien als in de volgende afbeelding:

  ![1mfa-universal-connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Vult het vak Gebruikersnaam in.")  

## <a name="the-five-authentication-options"></a>De vijf verificatieopties  

Universele verificatie met Active Directory ondersteunt de twee niet-interactieve verificatiemethoden:
    - `Azure Active Directory - Password`
    - `Azure Active Directory - Integrated`

Er zijn ook twee niet-interactieve verificatiemodellen, die kunnen worden gebruikt in allerlei verschillende toepassingen (ADO.NET, JDCB, ODC, enzovoort). Voor deze twee methoden worden nooit pop-upvensters weergegeven:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

De interactieve methode die ook Azure Multi-Factor Authentication (MFA) ondersteunt:`Active Directory - Universal with MFA`

Azure MFA helpt bij het bewaken van de toegang tot uw gegevens en toepassingen en komt tegemoet aan de wensen van gebruikers die een eenvoudige aanmeldprocedure willen. Azure MFA biedt krachtige verificatie met een scala aan gebruikersvriendelijke verificatieopties (zoals telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app), zodat gebruikers de gewenste methode kunnen kiezen. Bij interactieve MFA met Azure AD kunnen er pop-upvensters worden weergegeven voor validatie.

Zie [multi-factor Authentication](../../active-directory/authentication/multi-factor-authentication.md)voor een beschrijving van Azure multi-factor Authentication.
Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](authentication-mfa-ssms-configure.md)voor configuratie stappen.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domeinnaam of tenant-id als parameter

Vanaf [SSMS versie 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) kunnen gebruikers die vanuit andere Azure Active Directory's zijn geïmporteerd als gastgebruiker, de naam van het Azure AD-domein of hun tenant-id opgeven wanneer ze verbinding willen maken. Gastgebruikers zijn gebruikers die zijn uitgenodigd bij andere Azure ADs, Microsoft-accounts zoals outlook.com, hotmail.com, live.com of andere accounts zoals gmail.com. Aan de hand van deze gegevens kan de verificatiemethode **Active Directory Universeel met MFA** de juiste verificatie-instantie identificeren. Deze optie is ook vereist voor de ondersteuning van Microsoft-accounts (MSA) zoals outlook.com, hotmail.com, live.com, of niet-MSA-accounts. Al deze gebruikers die willen worden geverifieerd met behulp van universele verificatie, moeten hun Azure AD-domeinnaam of tenant-id invoeren. Deze para meter vertegenwoordigt de huidige naam van het Azure AD-domein of de Tenant-ID, de Azure-server is gekoppeld aan. Als de Azure server bijvoorbeeld is gekoppeld aan het Azure AD-domein `contosotest.onmicrosoft.com` waar de gebruiker `joe@contosodev.onmicrosoft.com` wordt gehost als een geïmporteerde gebruiker uit het Azure AD-domein `contosodev.onmicrosoft.com`, is `contosotest.onmicrosoft.com` de domeinnaam die vereist is om deze gebruiker te verifiëren. Als de gebruiker een systeemeigen gebruiker is van de Azure AD die is gekoppeld aan de Azure-server, en geen MSA-account is, hoeft er geen domeinnaam of tenant-id te worden ingevoerd. Om de para meter in te voeren (te beginnen met SSMS versie 17,2), in het dialoog venster **verbinding maken met data base** vult u het dialoog venster in, selecteert u **Active Directory-Universal met MFA-** verificatie, klikt u op **Opties**, vult u het vak **gebruikers naam** in en klikt u vervolgens op het tabblad **verbindings eigenschappen** . Controleer het vak **AD-domein naam of Tenant-id** en verstrek de verificatie autoriteit, zoals de domein naam (**contosotest.onmicrosoft.com**) of de GUID van de Tenant-  
   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Als u SSMS 18. x of hoger uitvoert, is de AD-domein naam of Tenant-ID niet langer nodig voor gast gebruikers omdat 18. x of hoger deze automatisch herkent.

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Ondersteuning voor Azure AD B2B

Azure AD-gebruikers die worden ondersteund voor Azure AD B2B-scenario's als gast gebruikers (Zie [Wat is Azure B2B Collaboration](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kunnen alleen verbinding maken met SQL database en Azure-Synapse als onderdeel van een groep die is gemaakt in huidige Azure AD en hand matig is toegewezen met de Transact-SQL- `CREATE USER` instructie in een bepaalde data base. Als bijvoorbeeld `steve@gmail.com` wordt uitgenodigd voor Azure AD `contosotest` (met het Azure AD `contosotest.onmicrosoft.com` -domein), wordt een Azure AD-groep, zoals `usergroup` moet worden gemaakt in de Azure AD die het `steve@gmail.com` lid bevat. Vervolgens moet deze groep worden gemaakt voor een specifieke data base (MyDatabase) door Azure AD SQL-beheerder of Azure AD DBO door een Transact-SQL-instructie uit te voeren `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . Nadat de databasegebruiker is gemaakt, kan de gebruiker `steve@gmail.com` zich aanmelden bij `MyDatabase` met behulp van de SSMS-verificatieoptie `Active Directory – Universal with MFA support`. De gebruikersgroep heeft standaard alleen de machtiging om verbinding te maken en alle verdere gegevenstoegang moet op de gebruikelijke manier worden verleend. De gebruiker `steve@gmail.com` moet als gastgebruiker het selectievakje inschakelen en de AD-domeinnaam `contosotest.onmicrosoft.com` toevoegen in het dialoogvenster **Verbindingseigenschap** van SSMS. De optie **AD-domeinnaam of tenant-id** wordt alleen ondersteund voor de verbindingsopties van Universeel met MFA; anders wordt deze grijs weergegeven.

## <a name="universal-authentication-limitations"></a>Beperkingen voor universele verificatie

- SSMS en SqlPackage.exe zijn de enige hulpprogramma's die momenteel zijn ingeschakeld voor MFA via universele verificatie van Active Directory.
- SSMS versie 17.2 ondersteunt gelijktijdige toegang door meerdere gebruikers met behulp van universele verificatie met MFA. Versie 17,0 en 17,1, beperkt een aanmelding voor een exemplaar van SSMS met behulp van universele verificatie voor één Azure Active Directory account. Als u zich als een ander Azure AD-account wilt aanmelden, moet u een ander exemplaar van SSMS gebruiken. (Deze beperking is beperkt tot Active Directory universele verificatie. u kunt u aanmelden bij een andere server met behulp van Active Directory wachtwoord verificatie, Active Directory geïntegreerde verificatie of SQL Server verificatie).
- SSMS ondersteunt universele verificatie van Active Directory voor Objectverkenner, Query-editor en visualisatie van Query Store.
- SSMS versie 17.2 biedt ondersteuning voor de DacFx-wizard voor het exporteren/extraheren/implementeren van databases. Zodra een specifieke gebruiker is geverifieerd via het dialoogvenster voor initiële verificatie met behulp van universele verificatie, werkt de DacFx-wizard op dezelfde manier als voor alle andere verificatiemethoden.
- De tabelontwerpfunctie van SSMS biedt geen ondersteuning voor universele verificatie.
- Er zijn geen aanvullende softwarevereisten voor universele verificatie van Active Directory, behalve dat u een ondersteunde versie van SSMS moet gebruiken.  
- De versie van Active Directory Authentication Library (ADAL) voor universele verificatie is bijgewerkt naar de nieuwste versie van ADAL.dll, te weten 3.13.9. Zie [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) voor meer informatie.  

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](authentication-mfa-ssms-configure.md)voor configuratie stappen.
- Anderen toegang verlenen tot uw Data Base: [SQL database-verificatie en-autorisatie: toegang verlenen](logins-create-manage.md)  
- Zorg ervoor dat anderen verbinding kunnen maken via de firewall: [Configureer een firewall regel op server niveau met behulp van de Azure Portal](firewall-configure.md)  
- [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse](authentication-aad-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Een BACPAC-bestand importeren in een nieuwe data base](database-import.md)  
- [Database naar een BACPAC-bestand exporteren](database-export.md)  
- Interface van C# interface [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Wanneer u **Active Directory-universele met MFA-** verificatie gebruikt, is ADAL tracering beschikbaar vanaf [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Standaard uitgeschakeld, u kunt ADAL tracering inschakelen via het menu **extra**, **Opties** , onder **Azure-Services**, Azure- **Cloud**, **ADAL uitvoervenster traceer niveau**, gevolgd door **uitvoer** in te scha kelen in het menu **weer gave** . De traceringen zijn beschikbaar in het uitvoer venster wanneer u **Azure Active Directory optie**selecteert.  
