---
title: Multi-Factor Authentication instellen
description: Meer informatie over het gebruik van Multi-Factored Authentication met SSMS voor SQL Database en SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 5d4d410f6fca566dab14e601972952b5996c331a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124893"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Meervoudige verificatie configureren voor SQL Server Management Studio en Azure AD

In dit onderwerp ziet u hoe u Azure Active Directory multi-factor authentication (MFA) gebruiken met SQL Server Management Studio. Azure AD MFA kan worden gebruikt bij het verbinden van SSMS of SqlPackage.exe met Azure [SQL Database](sql-database-technical-overview.md) en SQL [Data Warehouse.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Zie [Universele verificatie met SQL Database en SQL Data Warehouse (SSMS-ondersteuning voor MFA) voor](sql-database-ssms-mfa-authentication.md)een overzicht van Azure SQL Database multi-factor authenticatie.

> [!NOTE]
> Dit onderwerp is van toepassing op Azure SQL-servers en op SQL Database- en SQL Data Warehouse-databases die op deze Azure SQL-servers worden gemaakt. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

## <a name="configuration-steps"></a>Configuratiestappen

1. **Een Azure Active Directory configureren** - Voor meer informatie, zie Uw Azure [AD-map beheren](https://msdn.microsoft.com/library/azure/hh967611.aspx), Uw [on-premises identiteiten integreren met Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), Uw eigen domeinnaam toevoegen aan Azure [AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), Ondersteunt Microsoft Azure nu federatie met Windows Server [Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)en Beheer Azure AD met [Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **MFA configureren** - Zie Wat is [Azure Multi-Factor Authentication,](../active-directory/authentication/multi-factor-authentication.md) [Conditional Access (MFA) met Azure SQL Database en Data Warehouse](sql-database-conditional-access.md)voor stapsgewijze instructies. (Volledige voorwaardelijke toegang vereist een Premium Azure Active Directory (Azure AD). Beperkte MFA is beschikbaar met een standaard Azure AD.)
3. **SQL Database of SQL Data Warehouse configureren voor Azure AD-verificatie** - Zie [Verbinding maken met SQL Database of SQL Data Warehouse](sql-database-aad-authentication.md)voor stapsgewijze instructies.
4. **SSMS downloaden** - Download op de clientcomputer de nieuwste SSMS, van [Download SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/mt238290.aspx) Voor alle functies in dit onderwerp, gebruik ten minste juli 2017, versie 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Verbinding maken met universele verificatie met SSMS

In de volgende stappen wordt weergegeven hoe u verbinding maken met SQL Database of SQL Data Warehouse met behulp van de nieuwste SSMS.

1. Als u verbinding wilt maken met universele verificatie, selecteert u in het dialoogvenster **Verbinding maken met server** de optie Active Directory - Universal met **MFA-ondersteuning**. (Als u **Active Directory Universal Authentication ziet,** bevindt u zich niet op de nieuwste versie van SSMS.)  
   ![1mfa-universal-connect][1]  
2. Vul het vak **Gebruikersnaam** in met de Azure `user_name@domain.com`Active Directory-referenties in de indeling .  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Als u verbinding maakt als gastgebruiker, hoeft u het veld AD-domeinnaam of tenant-id voor gastgebruikers niet meer in te vullen omdat SSMS 18.x of hoger het automatisch herkent. Zie [Universele verificatie met SQL Database en SQL Data Warehouse (SSMS-ondersteuning voor MFA) voor](sql-database-ssms-mfa-authentication.md)meer informatie.
   ![mfa-no-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Als u echter verbinding maakt als gastgebruiker via SSMS 17.x of ouder, moet u op **Opties**en in het dialoogvenster **Verbindingseigenschap** klikken en het vak **AD-domeinnaam of tenant-id** voltooien.
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Zoals gebruikelijk voor SQL Database en SQL Data Warehouse moet u op **Opties** klikken en de database opgeven in het dialoogvenster **Opties.** (Als de verbonden gebruiker een gastgebruiker joe@outlook.comis ( d.w.z.), moet u het selectievakje inschakelen en de huidige AD-domeinnaam of tenant-id toevoegen als onderdeel van Opties. Zie [Universele verificatie met SQL Database en SQL Data Warehouse (SSMS-ondersteuning voor MFA)](sql-database-ssms-mfa-authentication.md). Klik vervolgens op **Verbinden**.  
5. Wanneer het dialoogvenster **Aanmelden bij uw account** wordt weergegeven, geeft u het account en wachtwoord van uw Azure Active Directory-identiteit op. Er is geen wachtwoord vereist als een gebruiker deel uitmaakt van een domein dat wordt gefedereerd met Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Voor Universele verificatie met een account waarvoor geen MFA vereist is, maakt u op dit moment verbinding. Ga door met de volgende stappen voor gebruikers die MFA nodig hebben:
   >  
   
6. Er kunnen twee dialoogvensters voor het instellen van MFA's worden weergegeven. Deze eenmalige bewerking is afhankelijk van de MFA-beheerdersinstelling en kan daarom optioneel zijn. Voor een MFA-domein is deze stap soms vooraf gedefinieerd (het domein vereist bijvoorbeeld dat gebruikers een smartcard en -pin moeten gebruiken).  
   ![3mfa-setup][3]  
7. In het tweede mogelijke eenmalige dialoogvenster u de details van uw verificatiemethode selecteren. De mogelijke opties worden geconfigureerd door uw beheerder.  
   ![4mfa-verify-1][4]  
8. De Azure Active Directory stuurt de bevestigingsgegevens naar u. Wanneer u de verificatiecode ontvangt, voert u deze in het vak **Verificatiecode invoeren** in en klikt u op **Aanmelden**.  
   ![5mfa-verify-2][5]  

Wanneer de verificatie is voltooid, verbindt SSMS normaal gesproken geldige referenties en firewalltoegang.

## <a name="next-steps"></a>Volgende stappen

- Zie Universele verificatie met SQL Database en SQL [Data Warehouse (SSMS-ondersteuning voor MFA) voor](sql-database-ssms-mfa-authentication.md)een overzicht van Azure SQL Database multi-factor authenticatie.  
- Anderen toegang verlenen tot uw database: [SQL Database Authentication and Authorization: Access](sql-database-manage-logins.md)  
- Zorg ervoor dat anderen verbinding kunnen maken via de firewall: [een firewallregel op Azure SQL Database-serverniveau configureren met behulp van de Azure-portal](sql-database-configure-firewall-settings.md)  
- Bij gebruik van **Active Directory- Universal met MFA-verificatie** is ADAL-tracering beschikbaar vanaf [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Standaard u ADAL-tracering inschakelen met het menu **Extra**, **Opties,** onder **Azure Services**, **Azure Cloud**, **ADAL Output Window Trace Level,** gevolgd door **Uitvoer** in te schakelen in het menu **Weergave.** De traces zijn beschikbaar in het uitvoervenster bij het selecteren van **de Azure Active Directory-optie**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

