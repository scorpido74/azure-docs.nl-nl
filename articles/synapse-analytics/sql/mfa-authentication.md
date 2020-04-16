---
title: Multi-factor AAD-verificatie gebruiken
description: Synapse SQL-ondersteuningsverbindingen van SQL Server Management Studio (SSMS) met behulp van Active Directory Universal Authentication.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 8e8f17f54f52aef6f552b7c211aa8f0e498b94df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424921"
---
# <a name="using-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Multi-factor AAD-verificatie gebruiken met Synapse SQL (SSMS-ondersteuning voor MFA)

Synapse SQL-ondersteuningsverbindingen van SQL Server Management Studio (SSMS) met behulp van *Active Directory Universal Authentication*. 

In dit artikel worden de verschillen tussen de verschillende verificatieopties en ook de beperkingen van het gebruik van universele verificatie besproken. 

**Download de nieuwste SSMS** - Download op de clientcomputer de nieuwste versie van SSMS, van [Download SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/mt238290.aspx) 

**Download de nieuwste SSMS** - Download op de clientcomputer de nieuwste versie van SSMS, van [Download SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Voor alle functies die in dit artikel worden besproken, gebruikt u ten minste juli 2017, versie 17.2.  Het meest recente dialoogvenster verbinding moet er hetzelfde uitzien als de volgende afbeelding:

![1mfa-universal-connect](./media/mfa-authentication/1mfa-universal-connect.png "Vult het vak Gebruikersnaam in.")  

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

Zie [Multifactorauthenticatie](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor een beschrijving van multifactorauthenticatie.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domeinnaam of tenant-id-parameter

Vanaf [SSMS-versie 17](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)kunnen gebruikers die als gastgebruikers in de huidige Active Directory worden geïmporteerd als gastgebruikers, de Azure AD-domeinnaam of de tenant-id opgeven wanneer ze verbinding maken. 

Gastgebruikers zijn gebruikers die zijn uitgenodigd uit andere Azure-a-d's, Microsoft-accounts zoals outlook.com, hotmail.com, live.com of andere accounts zoals gmail.com. Met deze informatie kan **Active Directory Universal met MFA-verificatie** de juiste authenticerende autoriteit identificeren. Deze optie is ook vereist om Microsoft-accounts (MSA) te ondersteunen, zoals outlook.com, hotmail.com, live.com of niet-MSA-accounts. 

Al deze gebruikers die willen worden geverifieerd met behulp van Universele verificatie, moeten hun Azure AD-domeinnaam of tenant-id invoeren. Deze parameter vertegenwoordigt de huidige Azure AD-domeinnaam/-tenant-id waarmee Azure Server is gekoppeld. 

Als Azure Server bijvoorbeeld is gekoppeld `contosotest.onmicrosoft.com` aan `joe@contosodev.onmicrosoft.com` het Azure AD-domein waarbij de `contosodev.onmicrosoft.com`gebruiker wordt gehost als geïmporteerde gebruiker uit Azure AD-domein, is de domeinnaam die nodig is `contosotest.onmicrosoft.com`om deze gebruiker te verifiëren . 

Wanneer de gebruiker een native gebruiker is van het Azure AD dat is gekoppeld aan Azure Server en geen MSA-account is, is er geen domeinnaam of tenant-id vereist. 

Als u de parameter (te beginnen met SSMS-versie 17.2) wilt invoeren, vult u in het dialoogvenster **Verbinding maken met database** het dialoogvenster in, selecteert u Active Directory - Universeel met **MFA-verificatie,** klikt u op **Opties,** vult u het vak **Gebruikersnaam** in en klikt u vervolgens op het tabblad **Verbindingseigenschappen.** 

Schakel het **selectievakje AD-domeinnaam of tenant-id** in en geef een verificatiebevoegdheid op, zoals de domeinnaam **(contosotest.onmicrosoft.com)** of de GUID van de tenant-id.  

   ![mfa-tenant-ssms](./media/mfa-authentication/mfa-tenant-ssms.png)

Als u SSMS 18.x of hoger uitvoert, is de AD-domeinnaam of tenant-id niet langer nodig voor gastgebruikers, omdat 18.x of hoger deze automatisch herkent.

   ![mfa-tenant-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Ondersteuning voor Azure AD-business naar business   
Azure AD-gebruikers die worden ondersteund voor Azure AD B2B-scenario's als gastgebruikers (zie [Wat is Azure B2B-samenwerking,](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) kan alleen verbinding maken met `CREATE USER` Synapse SQL als onderdeel van leden van een groep die is gemaakt in het huidige Azure AD en handmatig in kaart zijn gebracht met de transact-SQL-instructie in een bepaalde database. 

`steve@gmail.com` Als u bijvoorbeeld wordt uitgenodigd `contosotest` voor Azure AD `contosotest.onmicrosoft.com`(met het Azure `usergroup` Ad-domein), moet een Azure `steve@gmail.com` AD-groep, zoals moet worden gemaakt in het Azure AD dat het lid bevat, worden gemaakt. Vervolgens moet deze groep worden gemaakt voor een specifieke database (dat wil zeggen MyDatabase) door Azure AD `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` SQL-beheerder of Azure AD DBO door een Transact-SQL-instructie uit te voeren. 

Nadat de databasegebruiker is gemaakt, kan de gebruiker `steve@gmail.com` zich aanmelden bij `MyDatabase` de optie `Active Directory – Universal with MFA support`SSMS-verificatie. 

De gebruikersgroep heeft standaard alleen de verbindingsmachtiging en eventuele verdere gegevenstoegang die op de normale manier moet worden verleend. 

Houd er `steve@gmail.com` rekening mee dat de gebruiker als gastgebruiker het selectievakje moet inschakelen en de AD-domeinnaam `contosotest.onmicrosoft.com` moet toevoegen in het dialoogvenster SSMS-verbindingseigenschap. **Connection Property** De **optie AD-domeinnaam of tenant-id** wordt alleen ondersteund voor de universele met MFA-verbindingsopties, anders wordt deze grijs weergegeven.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Universele verificatiebeperkingen voor Synapse SQL

- SSMS en SqlPackage.exe zijn de enige tools die momenteel zijn ingeschakeld voor MFA via Active Directory Universal Authentication.
- SSMS versie 17.2 ondersteunt gelijktijdige toegang voor meerdere gebruikers met behulp van universele verificatie met MFA. Versie 17.0 en 17.1 beperkten een aanmelding voor een instantie van SSMS met behulp van universele verificatie tot één Azure Active Directory-account. Als u zich wilt aanmelden als een ander Azure AD-account, moet u een ander exemplaar van SSMS gebruiken. (Deze beperking is beperkt tot Active Directory Universal Authentication; u zich aanmelden bij verschillende servers met Active Directory Password Authentication, Active Directory Integrated Authentication of SQL Server Authentication).
- SSMS ondersteunt Active Directory Universal Authentication voor Object Explorer, Query Editor en Query Store visualisatie.
- SSMS versie 17.2 biedt DacFx Wizard-ondersteuning voor export/extract/deploy-gegevensdatabase. Zodra een specifieke gebruiker is geverifieerd via het dialoogvenster eerste verificatie met behulp van Universele verificatie, functioneert de DacFx Wizard op dezelfde manier als voor alle andere verificatiemethoden.
- De SSMS Table Designer biedt geen ondersteuning voor universele verificatie.
- Er zijn geen aanvullende softwarevereisten voor Active Directory Universal Authentication, behalve dat u een ondersteunde versie van SSMS moet gebruiken.  
- De ADAL-versie (Active Directory Authentication Library) voor universele verificatie is bijgewerkt naar de nieuwste adal.dll 3.13.9 beschikbare versie. Zie [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Volgende stappen
Zie het artikel [Verbinding maken met Synapse SQL met SQL Server Management Studio](get-started-ssms.md) voor meer informatie. 

