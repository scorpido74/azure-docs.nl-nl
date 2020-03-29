---
title: Azure Analysis Services-verificatie en gebruikersmachtigingen| Microsoft Documenten
description: In dit artikel wordt beschreven hoe Azure Analysis Services Azure Active Directory (Azure AD) gebruikt voor identiteitsbeheer en gebruikersverificatie.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a054c3c042e18f1679acd75e5ba5ad74f66edff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572755"
---
# <a name="authentication-and-user-permissions"></a>Verificatie en gebruikersmachtigingen

Azure Analysis Services gebruikt Azure Active Directory (Azure AD) voor identiteitsbeheer en gebruikersverificatie. Elke gebruiker die een Azure Analysis Services-server maakt, beheert of verbinding maakt, moet een geldige gebruikersidentiteit hebben in een [Azure AD-tenant](../active-directory/fundamentals/active-directory-administer.md) in hetzelfde abonnement.

Azure Analysis Services ondersteunt [Azure AD B2B-samenwerking.](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) Met B2B kunnen gebruikers van buiten een organisatie worden uitgenodigd als gastgebruikers in een Azure AD-map. Gasten kunnen afkomstig zijn uit een andere Azure AD-tenantmap of een geldig e-mailadres. Zodra de gebruiker is uitgenodigd en de uitnodiging per e-mail vanuit Azure accepteert, wordt de gebruikersidentiteit toegevoegd aan de tenantmap. Deze identiteiten kunnen worden toegevoegd aan beveiligingsgroepen of als leden van een serverbeheerder of databaserol.

![Verificatiearchitectuur azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication

Alle clienttoepassingen en -hulpprogramma's gebruiken een of meer van de [Analysis Services-clientbibliotheken](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) om verbinding te maken met een server. 

Alle drie de clientbibliotheken ondersteunen zowel de interactieve stroom van Azure AD als niet-interactieve verificatiemethoden. De twee niet-interactieve methoden, Active Directory Password en Active Directory Integrated Authentication methoden kunnen worden gebruikt in toepassingen die gebruik maken van AMOMD en MSOLAP. Deze twee methoden resulteren nooit in pop-updialoogvensters.

Clienttoepassingen zoals Excel en Power BI Desktop en hulpprogramma's zoals SSMS en Analysis Services-projecten extensie voor Visual Studio installeren de nieuwste versies van de bibliotheken wanneer bijgewerkt naar de nieuwste release. De uitbreiding van de projecten Power BI Desktop, SSMS en Analysis Services wordt maandelijks bijgewerkt. Excel wordt [bijgewerkt met Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Office 365-updates komen minder vaak voor en sommige organisaties gebruiken het uitgestelde kanaal, wat betekent dat updates tot drie maanden worden uitgesteld.

Afhankelijk van de clienttoepassing of -tool die u gebruikt, kan het type verificatie en de manier waarop u zich aanmeldt, verschillen. Elke toepassing biedt mogelijk ondersteuning voor verschillende functies voor het maken van verbinding met cloudservices zoals Azure Analysis Services.

Power BI Desktop, Visual Studio en SSMS ondersteunen Active Directory Universal Authentication, een interactieve methode die ook Azure Multi-Factor Authentication (MFA) ondersteunt. Azure MFA helpt de toegang tot gegevens en toepassingen te waarborgen en biedt tegelijkertijd een eenvoudig aanmeldingsproces. Het levert sterke authenticatie met verschillende verificatieopties (telefoongesprek, sms, smartcards met pin of mobiele app-melding). Interactieve MFA met Azure AD kan resulteren in een pop-updialoogvenster voor validatie. **Universele verificatie wordt aanbevolen**.

Als u zich aanmeldt bij Azure met een Windows-account en Universele verificatie niet is geselecteerd of beschikbaar is (Excel), is [Active Directory Federation Services (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) vereist. Met Federation worden Azure AD- en Office 365-gebruikers geverifieerd met behulp van on-premises referenties en hebben ze toegang tot Azure-bronnen.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services-servers ondersteunen verbindingen vanaf [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en hoger met Windows Authentication, Active Directory Password Authentication en Active Directory Universal Authentication. In het algemeen wordt aanbevolen active directory universele verificatie te gebruiken, omdat:

*  Ondersteunt interactieve en niet-interactieve verificatiemethoden.

*  Ondersteunt Azure B2B-gastgebruikers die zijn uitgenodigd in de Azure AS-tenant. Wanneer u verbinding maakt met een server, moeten gastgebruikers Active Directory Universal Authentication selecteren wanneer ze verbinding maken met de server.

*  Ondersteunt multi-factor authenticatie (MFA). Azure MFA helpt de toegang tot gegevens en toepassingen te waarborgen met een reeks verificatieopties: telefoongesprek, sms, smartcards met pin of melding van mobiele apps. Interactieve MFA met Azure AD kan resulteren in een pop-updialoogvenster voor validatie.

### <a name="visual-studio"></a>Visual Studio

Visual Studio maakt verbinding met Azure Analysis Services met behulp van Active Directory Universal Authentication met MFA-ondersteuning. Gebruikers wordt gevraagd zich bij de eerste implementatie aan te melden bij Azure. Gebruikers moeten zich aanmelden bij Azure met een account met serverbeheerdersmachtigingen op de server waarop ze worden geïmplementeerd. Wanneer u zich de eerste keer aanmeldt bij Azure, wordt een token toegewezen. Het token wordt in de cache opgeslagen in het geheugen voor toekomstige opnieuw verbinden.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop maakt verbinding met Azure Analysis Services met behulp van Active Directory Universal Authentication met MFA-ondersteuning. Gebruikers wordt gevraagd zich bij azure aan te melden bij de eerste verbinding. Gebruikers moeten zich aanmelden bij Azure met een account dat is opgenomen in een serverbeheerder of databaserol.

### <a name="excel"></a>Excel

Excel-gebruikers kunnen verbinding maken met een server met een Windows-account, een organisatie-id (e-mailadres) of een extern e-mailadres. Externe e-mailidentiteiten moeten als gastgebruiker in het Azure AD bestaan.

## <a name="user-permissions"></a>Gebruikersmachtigingen

**Serverbeheerders** zijn specifiek voor een Azure Analysis Services-serverinstantie. Ze maken verbinding met hulpprogramma's zoals Azure portal, SSMS en Visual Studio om taken uit te voeren, zoals het toevoegen van databases en het beheren van gebruikersrollen. Standaard wordt de gebruiker die de server maakt automatisch toegevoegd als serverbeheerder van Analysis Services. Andere beheerders kunnen worden toegevoegd met behulp van Azure portal of SSMS. Serverbeheerders moeten een account hebben in de Azure AD-tenant in hetzelfde abonnement. Zie [Serverbeheerders beheren](analysis-services-server-admins.md)voor meer informatie. 

**Databasegebruikers** maken verbinding met modeldatabases met behulp van clienttoepassingen zoals Excel of Power BI. Gebruikers moeten worden toegevoegd aan databaserollen. Databaserollen definiëren beheerders-, proces- of leesmachtigingen voor een database. Het is belangrijk om te begrijpen database gebruikers in een rol met beheerdersmachtigingen is anders dan serverbeheerders. Serverbeheerders zijn echter ook databasebeheerders. Zie [Databaserollen en gebruikers beheren](analysis-services-database-users.md)voor meer informatie.

**Azure-broneigenaren**. Broneigenaren beheren resources voor een Azure-abonnement. Broneigenaren kunnen Azure AD-gebruikersidentiteiten toevoegen aan eigenaar- of bijdragen in een abonnement met behulp van **Access-besturingselement** in Azure-portal of met Azure Resource Manager-sjablonen. 

![Toegangsbeheer in Azure-portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Rollen op dit niveau zijn van toepassing op gebruikers of accounts die taken moeten uitvoeren die in de portal kunnen worden voltooid of met Azure Resource Manager-sjablonen. Zie [Toegangsbeheer op basis van rollen](../role-based-access-control/overview.md)voor meer informatie. 

## <a name="database-roles"></a>Databaserollen

 Rollen die zijn gedefinieerd voor een tabelmodel zijn databaserollen. Dat wil zeggen dat de rollen leden bevatten die bestaan uit Azure AD-gebruikers en beveiligingsgroepen die specifieke machtigingen hebben die de actie definiëren die deze leden kunnen uitvoeren op een modeldatabase. Een databaserol wordt gemaakt als een afzonderlijk object in de database en is alleen van toepassing op de database waarin die rol is gemaakt.   
  
 Wanneer u een nieuw tabelmodelproject maakt, heeft het modelproject standaard geen rollen. Rollen kunnen worden gedefinieerd met het dialoogvenster Rolbeheer in Visual Studio. Wanneer rollen worden gedefinieerd tijdens het ontwerp van het modelproject, worden ze alleen toegepast op de database met modelwerkruimten. Wanneer het model wordt geïmplementeerd, worden dezelfde rollen toegepast op het geïmplementeerde model. Nadat een model is geïmplementeerd, kunnen server- en databasebeheerders rollen en leden beheren met Behulp van SSMS. Zie [Databaserollen en gebruikers beheren](analysis-services-database-users.md)voor meer informatie.
  
## <a name="next-steps"></a>Volgende stappen

[Toegang tot bronnen beheren met Azure Active Directory-groepen](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Databaserollen en -gebruikers beheren](analysis-services-database-users.md)  
[Serverbeheerders beheren](analysis-services-server-admins.md)  
[Op rollen gebaseerd toegangscontrole](../role-based-access-control/overview.md)  