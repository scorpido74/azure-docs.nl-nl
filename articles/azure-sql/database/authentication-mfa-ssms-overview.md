---
title: Multi-factor AAD-verificatie gebruiken
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
ms.openlocfilehash: 574999686c38ce4ce1e6d40b3148b70e3602fe00
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041887"
---
# <a name="using-multi-factor-aad-authentication"></a>Multi-factor AAD-verificatie gebruiken
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure Managed instance en Azure Synapse Analytics ondersteunen verbindingen van SQL Server Management Studio (SSMS) met behulp van *Azure Active Directory-Universal met MFA-* verificatie. In dit artikel worden de verschillen tussen de verschillende verificatie opties beschreven, evenals de beperkingen die zijn gekoppeld aan het gebruik van universele authenticatie.

**Down load de nieuwste SSMS** -op de client computer, down load de meest recente versie van SSMS, van [down load SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

Voor alle functies die in dit artikel worden besproken, gebruikt u ten minste juli 2017, versie 17,2. Het dialoog venster meest recente verbinding moet er ongeveer uitzien als in de volgende afbeelding:

  ![1mfa-Universal-Connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Hiermee wordt het vak gebruikers naam ingevuld.")  

## <a name="the-five-authentication-options"></a>De vijf verificatie opties  

Active Directory universele verificatie ondersteunt de twee niet-interactieve verificatie methoden:
    - `Azure Active Directory - Password`verificatie
    - `Azure Active Directory - Integrated`verificatie

Er zijn ook twee niet-interactieve verificatie modellen, die kunnen worden gebruikt in veel verschillende toepassingen (ADO.NET, JDCB, ODC, enzovoort). Deze twee methoden resulteren nooit in pop-updialoogvensters:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

De interactieve methode die ook Azure multi-factor Authentication (MFA) ondersteunt, is:`Active Directory - Universal with MFA`

Azure MFA helpt bij het bewaken van de toegang tot uw gegevens en toepassingen en komt tegemoet aan de wensen van gebruikers die een eenvoudige aanmeldprocedure willen. Het biedt krachtige verificatie met een scala aan eenvoudige verificatie opties (telefoon oproep, tekst bericht, Smart Cards met pincode of mobiele app-melding), zodat gebruikers de gewenste methode kunnen kiezen. Interactieve MFA met Azure AD kan resulteren in een pop-upvenster voor validatie.

Zie [multi-factor Authentication](../../active-directory/authentication/multi-factor-authentication.md)voor een beschrijving van multi-factor Authentication.
Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](authentication-mfa-ssms-configure.md)voor configuratie stappen.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domein naam of Tenant-ID-para meter

Vanaf [SSMS versie 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kunnen gebruikers die in de huidige Active Directory van andere Azure Active Directory-gebruikers worden geïmporteerd, de naam van het Azure AD-domein of de Tenant-id opgeven wanneer ze verbinding maken. Gast gebruikers bevatten gebruikers die zijn uitgenodigd van andere Azure ADs, micro soft-accounts zoals outlook.com, hotmail.com, live.com of andere accounts, zoals gmail.com. Met deze informatie kan **Active Directory universeel met MFA-verificatie** de juiste verificatie-instantie identificeren. Deze optie is ook vereist voor de ondersteuning van micro soft-accounts (MSA) zoals outlook.com, hotmail.com, live.com of niet-MSA-accounts. Al deze gebruikers die willen worden geverifieerd met behulp van universele verificatie, moeten hun Azure AD-domein naam of Tenant-ID invoeren. Deze para meter vertegenwoordigt de huidige naam van het Azure AD-domein of de Tenant-ID, de Azure-server is gekoppeld aan. Als Azure server bijvoorbeeld is gekoppeld aan een Azure AD-domein `contosotest.onmicrosoft.com` waarbij `joe@contosodev.onmicrosoft.com` de gebruiker wordt gehost als een geïmporteerde gebruiker vanuit een Azure AD `contosodev.onmicrosoft.com` -domein, is de domein naam vereist om deze gebruiker te verifiëren `contosotest.onmicrosoft.com` . Als de gebruiker een systeem eigen gebruiker is van de Azure AD die is gekoppeld aan Azure server en geen MSA-account is, is er geen domein naam of Tenant-ID vereist. Om de para meter in te voeren (te beginnen met SSMS versie 17,2), in het dialoog venster **verbinding maken met data base** vult u het dialoog venster in, selecteert u **Active Directory-Universal met MFA-** verificatie, klikt u op **Opties**, vult u het vak **gebruikers naam** in en klikt u vervolgens op het tabblad **verbindings eigenschappen** . Controleer het vak **AD-domein naam of Tenant-id** en verstrek de verificatie autoriteit, zoals de domein naam (**contosotest.onmicrosoft.com**) of de GUID van de Tenant-  
   ![MFA-Tenant-SSMS](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Als u SSMS 18. x of hoger uitvoert, is de AD-domein naam of Tenant-ID niet langer nodig voor gast gebruikers omdat 18. x of hoger deze automatisch herkent.

   ![MFA-Tenant-SSMS](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Ondersteuning voor Azure AD Business naar Business

Azure AD-gebruikers die worden ondersteund voor Azure AD B2B-scenario's als gast gebruikers (Zie [Wat is Azure B2B Collaboration](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kunnen alleen verbinding maken met SQL database en Azure-Synapse als onderdeel van een groep die is gemaakt in huidige Azure AD en hand matig is toegewezen met de Transact-SQL- `CREATE USER` instructie in een bepaalde data base. Als bijvoorbeeld `steve@gmail.com` wordt uitgenodigd voor Azure AD `contosotest` (met het Azure AD `contosotest.onmicrosoft.com` -domein), wordt een Azure AD-groep, zoals `usergroup` moet worden gemaakt in de Azure AD die het `steve@gmail.com` lid bevat. Vervolgens moet deze groep worden gemaakt voor een specifieke data base (MyDatabase) door Azure AD SQL-beheerder of Azure AD DBO door een Transact-SQL-instructie uit te voeren `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . Nadat de database gebruiker is gemaakt, kan de gebruiker `steve@gmail.com` zich aanmelden met `MyDatabase` de verificatie optie SSMS `Active Directory – Universal with MFA support` . De usergroup heeft standaard alleen de machtiging Connect en alle verdere gegevens toegang die op de normale manier moeten worden verleend. Houd er rekening mee dat gebruiker `steve@gmail.com` als gast gebruiker het vakje moet inschakelen en voeg de naam van het AD-domein toe `contosotest.onmicrosoft.com` in het dialoog venster SSMS- **verbindings eigenschap** . De optie **AD-domein naam of Tenant-id** wordt alleen ondersteund voor de Universal met MFA-verbindings opties, anders wordt deze grijs weer gegeven.

## <a name="universal-authentication-limitations"></a>Beperkingen voor universele verificatie

- SSMS en SqlPackage. exe zijn de enige hulpprogram ma's die momenteel zijn ingeschakeld voor MFA via Active Directory universele verificatie.
- SSMS versie 17,2 ondersteunt gelijktijdige toegang met meerdere gebruikers met behulp van universele verificatie met MFA. Versie 17,0 en 17,1, beperkt een aanmelding voor een exemplaar van SSMS met behulp van universele verificatie voor één Azure Active Directory account. Als u zich als een ander Azure AD-account wilt aanmelden, moet u een ander exemplaar van SSMS gebruiken. (Deze beperking is beperkt tot Active Directory universele verificatie. u kunt u aanmelden bij een andere server met behulp van Active Directory wachtwoord verificatie, Active Directory geïntegreerde verificatie of SQL Server verificatie).
- SSMS ondersteunt Active Directory universele verificatie voor Objectverkenner, de query-editor en de visualisatie van het query archief.
- SSMS versie 17,2 biedt ondersteuning voor de DacFx-wizard voor het exporteren/extra heren/implementeren van gegevens database. Zodra een specifieke gebruiker is geverifieerd via het dialoog venster voor initiële verificatie met behulp van universele verificatie, werkt de wizard DacFx op dezelfde manier als voor alle andere verificatie methoden.
- De SSMS-tabelontwerpfunctie biedt geen ondersteuning voor universele verificatie.
- Er zijn geen aanvullende software vereisten voor Active Directory universele verificatie, behalve dat u een ondersteunde versie van SSMS moet gebruiken.  
- De versie van Active Directory Authentication Library (ADAL) voor universele verificatie is bijgewerkt naar de nieuwste versie van ADAL. dll 3.13.9 beschikbaar uitgebracht. Zie [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](authentication-mfa-ssms-configure.md)voor configuratie stappen.
- Anderen toegang verlenen tot uw Data Base: [SQL database-verificatie en-autorisatie: toegang verlenen](logins-create-manage.md)  
- Zorg ervoor dat anderen verbinding kunnen maken via de firewall: [Configureer een firewall regel op server niveau met behulp van de Azure Portal](firewall-configure.md)  
- [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse](authentication-aad-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Een BACPAC-bestand importeren in een nieuwe Azure SQL-database](database-import.md)  
- [Een Azure SQL-database exporteren naar een BACPAC-bestand](database-export.md)  
- Interface van C# interface [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Wanneer u **Active Directory-universele met MFA-** verificatie gebruikt, is ADAL tracering beschikbaar vanaf [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Standaard uitgeschakeld, u kunt ADAL tracering inschakelen via het menu **extra**, **Opties** , onder **Azure-Services**, Azure- **Cloud**, **ADAL uitvoervenster traceer niveau**, gevolgd door **uitvoer** in te scha kelen in het menu **weer gave** . De traceringen zijn beschikbaar in het uitvoer venster wanneer u **Azure Active Directory optie**selecteert.  
