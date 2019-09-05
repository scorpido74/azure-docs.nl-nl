---
title: Multi-factor AAD-verificatie gebruiken met Azure SQL Database en Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Database en Azure SQL Data Warehouse ondersteunen verbindingen van SQL Server Management Studio (SSMS) met behulp van Active Directory universele authenticatie.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/08/2018
ms.openlocfilehash: 7add55380f2f7b3ef70db0603fe2c26127db8a78
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566450"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Multi-factor AAD-verificatie gebruiken met Azure SQL Database en Azure SQL Data Warehouse (SSMS-ondersteuning voor MFA)
Azure SQL Database en Azure SQL Data Warehouse ondersteunen verbindingen van SQL Server Management Studio (SSMS) met behulp van *Active Directory universele authenticatie*. In dit artikel worden de verschillen tussen de verschillende verificatie opties beschreven, evenals de beperkingen die zijn gekoppeld aan het gebruik van universele authenticatie. 

**Down load de nieuwste SSMS** -op de client computer, down load de meest recente versie van SSMS, van [down load SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Voor alle functies die in dit artikel worden besproken, gebruikt u ten minste juli 2017, versie 17,2.  Het dialoog venster meest recente verbinding moet er ongeveer uitzien als in de volgende afbeelding:
 
  ![1mfa-Universal-Connect] Hiermee wordt (./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "het vak gebruikers naam ingevuld.")  

## <a name="the-five-authentication-options"></a>De vijf verificatie opties  

Active Directory universele verificatie ondersteunt de twee niet-interactieve verificatie methoden:
    - `Active Directory - Password`verificatie
    - `Active Directory - Integrated`verificatie

Er zijn ook twee niet-interactieve verificatie modellen, die kunnen worden gebruikt in veel verschillende toepassingen (ADO.NET, JDCB, ODC, enzovoort). Deze twee methoden resulteren nooit in pop-updialoogvensters: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

De interactieve methode biedt ook ondersteuning voor Azure multi-factor Authentication (MFA): 
- `Active Directory - Universal with MFA` 


Azure MFA helpt bij het bewaken van de toegang tot uw gegevens en toepassingen en komt tegemoet aan de wensen van gebruikers die een eenvoudige aanmeldprocedure willen. Het biedt krachtige verificatie met een scala aan eenvoudige verificatie opties (telefoon oproep, tekst bericht, Smart Cards met pincode of mobiele app-melding), zodat gebruikers de gewenste methode kunnen kiezen. Interactieve MFA met Azure AD kan resulteren in een pop-upvenster voor validatie.

Zie [multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md)voor een beschrijving van multi-factor Authentication.
Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md)voor configuratie stappen.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domein naam of Tenant-ID-para meter   

Vanaf [SSMS versie 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kunnen gebruikers die in de huidige Active Directory van andere Azure Active Directory-gebruikers worden geïmporteerd, de naam van het Azure AD-domein of de Tenant-id opgeven wanneer ze verbinding maken. Gast gebruikers bevatten gebruikers die zijn uitgenodigd van andere Azure ADs, micro soft-accounts zoals outlook.com, hotmail.com, live.com of andere accounts, zoals gmail.com. Met deze informatie kan **Active Directory universeel met MFA-verificatie** de juiste verificatie-instantie identificeren. Deze optie is ook vereist voor de ondersteuning van micro soft-accounts (MSA) zoals outlook.com, hotmail.com, live.com of niet-MSA-accounts. Al deze gebruikers die willen worden geverifieerd met behulp van universele verificatie, moeten hun Azure AD-domein naam of Tenant-ID invoeren. Deze para meter vertegenwoordigt de huidige naam van het Azure AD-domein/de Tenant-ID waaraan de Azure-server is gekoppeld. Als Azure server bijvoorbeeld is gekoppeld aan een Azure AD-domein `contosotest.onmicrosoft.com` waarbij de `joe@contosodev.onmicrosoft.com` gebruiker wordt gehost als een geïmporteerde gebruiker vanuit een Azure `contosodev.onmicrosoft.com`AD-domein, is `contosotest.onmicrosoft.com`de domein naam vereist om deze gebruiker te verifiëren. Als de gebruiker een systeem eigen gebruiker is van de Azure AD die is gekoppeld aan Azure server en geen MSA-account is, is er geen domein naam of Tenant-ID vereist. Als u de para meter wilt invoeren (vanaf SSMS versie 17,2), vult u in het dialoog venster **verbinding maken met data base** het dialoog venster in, selecteert u **Active Directory-universeel met MFA-** verificatie, klikt u op **Opties**, de **gebruikers naam volt ooien** en klik vervolgens op het tabblad **verbindings eigenschappen** . Schakel het selectie vakje **AD-domein naam of Tenant-id** in en geef de verificatie autoriteit, zoals de domein naam (**contosotest.onmicrosoft.com**) of de GUID van de Tenant-id op.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Ondersteuning voor Azure AD Business naar Business   
Azure AD-gebruikers die worden ondersteund voor Azure AD B2B-scenario's als gast gebruikers (Zie [Wat is Azure B2B Collaboration](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kunnen verbinding maken met SQL Database en SQL Data Warehouse alleen als onderdeel van de leden van een groep die in de huidige Azure AD is gemaakt en hand matig zijn toegewezen met behulp van Transact-SQL `CREATE USER` -instructie in een bepaalde data base. Als `steve@gmail.com` bijvoorbeeld wordt uitgenodigd voor Azure AD `contosotest` (met het Azure AD-domein `contosotest.onmicrosoft.com`) `usergroup` , wordt een Azure AD-groep, zoals moet worden gemaakt in de Azure AD die het `steve@gmail.com` lid bevat. Vervolgens moet deze groep worden gemaakt voor een specifieke data base (MyDatabase) door Azure AD SQL-beheerder of Azure AD dbo door een Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` -instructie uit te voeren. Nadat de database gebruiker is gemaakt, kan de gebruiker `steve@gmail.com` zich aanmelden `MyDatabase` met de verificatie optie `Active Directory – Universal with MFA support`SSMS. De usergroup heeft standaard alleen de machtiging Connect en alle verdere gegevens toegang die op de normale manier moeten worden verleend. Houd er rekening `steve@gmail.com` mee dat gebruiker als gast gebruiker het vakje moet inschakelen en voeg de naam `contosotest.onmicrosoft.com` van het AD-domein toe in het dialoog venster SSMS- **verbindings eigenschap** . De optie **AD-domein naam of Tenant-id** wordt alleen ondersteund voor de Universal met MFA-verbindings opties, anders wordt deze grijs weer gegeven.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Beperkingen voor universele verificatie voor SQL Database en SQL Data Warehouse
- SSMS en SqlPackage. exe zijn de enige hulpprogram ma's die momenteel zijn ingeschakeld voor MFA via Active Directory universele verificatie.
- SSMS versie 17,2 ondersteunt gelijktijdige toegang met meerdere gebruikers met behulp van universele verificatie met MFA. Versie 17,0 en 17,1, beperkt een aanmelding voor een exemplaar van SSMS met behulp van universele verificatie voor één Azure Active Directory account. Als u zich als een ander Azure AD-account wilt aanmelden, moet u een ander exemplaar van SSMS gebruiken. (Deze beperking is beperkt tot Active Directory universele verificatie. u kunt zich aanmelden bij verschillende servers met behulp van Active Directory wachtwoord verificatie, Active Directory geïntegreerde verificatie of SQL Server verificatie).
- SSMS ondersteunt Active Directory universele verificatie voor Objectverkenner, de query-editor en de visualisatie van het query archief.
- SSMS versie 17,2 biedt ondersteuning voor de DacFx-wizard voor het exporteren/extra heren/implementeren van gegevens database. Zodra een specifieke gebruiker is geverifieerd via het dialoog venster voor initiële verificatie met behulp van universele verificatie, werkt de wizard DacFx op dezelfde manier als voor alle andere verificatie methoden.
- De SSMS-tabelontwerpfunctie biedt geen ondersteuning voor universele verificatie.
- Er zijn geen aanvullende software vereisten voor Active Directory universele verificatie, behalve dat u een ondersteunde versie van SSMS moet gebruiken.  
- De versie van Active Directory Authentication Library (ADAL) voor universele verificatie is bijgewerkt naar de nieuwste versie van ADAL. dll 3.13.9 beschikbaar uitgebracht. Zie [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md)voor configuratie stappen.
- Anderen toegang verlenen tot uw Data Base: [Verificatie en autorisatie SQL Database: Toegang verlenen](sql-database-manage-logins.md)  
- Controleer of anderen verbinding kunnen maken via de firewall: [Een Azure SQL Database firewall regel op server niveau configureren met behulp van de Azure Portal](sql-database-configure-firewall-settings.md)  
- [Azure Active Directory-verificatie configureren en beheren met SQL Database of SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Framework voor Gegevenslaagtoepassing (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Een BACPAC-bestand importeren in een nieuwe Azure SQL-database](../sql-database/sql-database-import.md)  
- [Een Azure SQL-database exporteren naar een BACPAC-bestand](../sql-database/sql-database-export.md)  
- C#interface [IUniversalAuthProvider interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Wanneer u **Active Directory-universele met MFA-** verificatie gebruikt, is ADAL tracering beschikbaar vanaf [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Standaard uitgeschakeld, u kunt ADAL tracering inschakelen via het menu **extra**, **Opties** , onder **Azure-Services**, Azure- **Cloud**, **ADAL uitvoervenster traceer niveau**, gevolgd door **uitvoer** in te scha kelen in het menu **weer gave** . De traceringen zijn beschikbaar in het uitvoer venster wanneer u **Azure Active Directory optie**selecteert.  
