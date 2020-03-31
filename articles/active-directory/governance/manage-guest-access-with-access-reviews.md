---
title: Gasttoegang beheren met toegangsbeoordelingen - Azure AD
description: Gastgebruikers beheren als leden van een groep of toegewezen aan een toepassing met Azure Active Directory-toegangsbeoordelingen
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bf2f9503ae704110786a1e73aec3da18c17e4ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932430"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gasttoegang beheren met Azure AD-toegangsbeoordelingen


Met Azure Active Directory (Azure AD) u samenwerking over organisatiegrenzen heen eenvoudig inschakelen met de [Azure AD B2B-functie.](../b2b/what-is-b2b.md) Gastgebruikers van andere huurders kunnen worden [uitgenodigd door beheerders](../b2b/add-users-administrator.md) of andere [gebruikers.](../b2b/what-is-b2b.md) Deze mogelijkheid is ook van toepassing op sociale identiteiten zoals Microsoft-accounts.

U er ook gemakkelijk voor zorgen dat gastgebruikers de juiste toegang hebben. U de gasten zelf of een besluitvormer vragen om deel te nemen aan een toegangscontrole en de toegang van de gasten opnieuw te certificeren (of te bevestigen). De beoordelaars kunnen op basis van suggesties uit Azure AD hun mening geven over de mate waarin een gebruiker per se toegang moet krijgen. Wanneer een toegangsbeoordeling is voltooid, u wijzigingen aanbrengen en de toegang verwijderen voor gasten die deze niet meer nodig hebben.

> [!NOTE]
> Dit document richt zich op het controleren van de toegang van gastgebruikers. Zie [Gebruikerstoegang beheren met toegangsbeoordelingen](manage-user-access-with-access-reviews.md)als u de toegang van alle gebruikers wilt controleren, niet alleen gasten. Zie Een toegangscontrole starten in Azure AD Privileged Identity [Management](../privileged-identity-management/pim-how-to-start-security-review.md)als u het lidmaatschap van gebruikers wilt controleren in beheerdersrollen, zoals globale beheerder.

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2

Voor meer informatie, [Licentievereisten](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Een toegangsbeoordeling voor gasten maken en uitvoeren

Ga eerst als globale beheerder of gebruikersbeheerder naar de [pagina Identiteitsbeheer](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) om ervoor te zorgen dat toegangsbeoordelingen klaar zijn voor uw organisatie.

Azure AD maakt verschillende scenario's mogelijk voor het beoordelen van gastgebruikers.

U het overzicht bekijken:

 - Een groep in Azure AD met een of meer gasten als lid.
 - Een toepassing die is verbonden met Azure AD en waaraan een of meer gastgebruikers zijn toegewezen. 

U vervolgens beslissen of u elke gast vraagt om zijn eigen toegang te bekijken of om een of meer gebruikers te vragen de toegang van elke gast te controleren.

 Deze scenario's worden behandeld in de volgende secties.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Vraag gasten om hun eigen lidmaatschap in een groep te bekijken

U toegangsbeoordelingen gebruiken om ervoor te zorgen dat gebruikers die zijn uitgenodigd en aan een groep zijn toegevoegd, toegang blijven nodig hebben. U gasten gemakkelijk vragen om hun eigen lidmaatschap van die groep te herzien.

1. Als u een toegangsbeoordeling voor de groep wilt maken, selecteert u de beoordeling om alleen leden van gastgebruikers op te nemen en dat leden zichzelf beoordelen. Zie [Een toegangscontrole van groepen of toepassingen maken voor](create-access-review.md)meer informatie.

2. Vraag elke gast om zijn eigen lidmaatschap te herzien. Elke gast die een uitnodiging heeft geaccepteerd, ontvangt standaard een e-mail van Azure AD met een koppeling naar de toegangscontrole. Azure AD heeft instructies voor gasten over het [controleren van de toegang tot groepen of toepassingen.](perform-access-review.md)

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [Een toegangscontrole van groepen of toepassingen voltooien](complete-access-review.md)voor meer informatie.

4. Naast gebruikers die hun eigen behoefte aan voortdurende toegang hebben geweigerd, u ook gebruikers verwijderen die niet hebben gereageerd. Niet-reagerende gebruikers ontvangen mogelijk geen e-mail meer.

5. Als de groep niet wordt gebruikt voor toegangsbeheer, u ook gebruikers verwijderen die niet zijn geselecteerd om deel te nemen aan de beoordeling omdat ze hun uitnodiging niet hebben geaccepteerd. Niet accepteren kan erop wijzen dat het e-mailadres van de uitgenodigde gebruiker een typfout had. Als een groep wordt gebruikt als distributielijst, zijn sommige gastgebruikers mogelijk niet geselecteerd om deel te nemen omdat ze contactpersonen zijn.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Vraag een sponsor om het lidmaatschap van een gast in een groep te bekijken

U een sponsor, zoals de eigenaar van een groep, vragen om de behoefte van een gast aan voortzetting van het lidmaatschap van een groep te beoordelen.

1. Als u een toegangsbeoordeling voor de groep wilt maken, selecteert u de beoordeling om alleen leden van gastgebruikers op te nemen. Geef vervolgens een of meer revisoren op. Zie [Een toegangscontrole van groepen of toepassingen maken voor](create-access-review.md)meer informatie.

2. Vraag de beoordelaars feedback te geven. Standaard ontvangen ze elk een e-mail van Azure AD met een koppeling naar het toegangspaneel, waar ze [de toegang tot groepen of toepassingen controleren.](perform-access-review.md)

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [Een toegangscontrole van groepen of toepassingen voltooien](complete-access-review.md)voor meer informatie.

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Vraag gasten om hun eigen toegang tot een applicatie te bekijken

U toegangsbeoordelingen gebruiken om ervoor te zorgen dat gebruikers die zijn uitgenodigd voor een bepaalde toepassing toegang blijven nodig hebben. U de gasten gemakkelijk zelf vragen om hun eigen behoefte aan toegang te herzien.

1. Als u een toegangscontrole voor de toepassing wilt maken, selecteert u de beoordeling om alleen gasten op te nemen en dat gebruikers hun eigen toegang controleren. Zie [Een toegangscontrole van groepen of toepassingen maken voor](create-access-review.md)meer informatie.

2. Vraag elke gast om hun eigen toegang tot de toepassing te bekijken. Elke gast die een uitnodiging heeft geaccepteerd, ontvangt standaard een e-mail van Azure AD. Die e-mail heeft een link naar de toegangscontrole in het toegangspaneel van uw organisatie. Azure AD heeft instructies voor gasten over het [controleren van de toegang tot groepen of toepassingen.](perform-access-review.md)

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [Een toegangscontrole van groepen of toepassingen voltooien](complete-access-review.md)voor meer informatie.

4. Naast gebruikers die hun eigen behoefte aan voortdurende toegang hebben geweigerd, u ook gastgebruikers verwijderen die niet hebben gereageerd. Niet-reagerende gebruikers ontvangen mogelijk geen e-mail meer. U ook gastgebruikers verwijderen die niet zijn geselecteerd om deel te nemen, vooral als ze niet onlangs zijn uitgenodigd. Deze gebruikers accepteerden hun uitnodiging niet en hadden dus geen toegang tot de toepassing. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Vraag een sponsor om de toegang van een gast tot een aanvraag te bekijken

U een sponsor, zoals de eigenaar van een aanvraag, vragen om de behoefte van de gast aan voortdurende toegang tot de toepassing te beoordelen.

1. Als u een toegangsbeoordeling voor de toepassing wilt maken, selecteert u de beoordeling om alleen gasten op te nemen. Geef vervolgens een of meer gebruikers op als revisoren. Zie [Een toegangscontrole van groepen of toepassingen maken voor](create-access-review.md)meer informatie.

2. Vraag de beoordelaars feedback te geven. Standaard ontvangen ze elk een e-mail van Azure AD met een koppeling naar het toegangspaneel, waar ze [de toegang tot groepen of toepassingen controleren.](perform-access-review.md)

3. Wanneer alle beoordelaars feedback hebben gegeven, kunt u de toegangsbeoordeling stoppen en de wijzigingen toepassen. Zie [Een toegangscontrole van groepen of toepassingen voltooien](complete-access-review.md)voor meer informatie.

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Vraag gasten om hun behoefte aan toegang te herzien, in het algemeen

In sommige organisaties zijn gasten mogelijk niet op de hoogte van hun groepslidmaatschappen.

> [!NOTE]
> Eerdere versies van de Azure-portal hebben geen beheerderstoegang toegestaan voor gebruikers met het UserType van Gast. In sommige gevallen heeft een beheerder in uw directory mogelijk de UserType-waarde van een gast gewijzigd in Lid met powershell. Als deze wijziging eerder in uw map is opgetreden, bevat de vorige query mogelijk niet alle gastgebruikers die in het verleden beheerderstoegangsrechten hadden. In dit geval moet u het UserType van de gast wijzigen of de gast handmatig opnemen in het groepslidmaatschap.

1. Maak een beveiligingsgroep in Azure AD met de gasten als leden, als er nog geen geschikte groep bestaat. U bijvoorbeeld een groep maken met een handmatig onderhouden lidmaatschap van gasten. U ook een dynamische groep maken met een naam zoals 'Gasten van Contoso' voor gebruikers in de Contoso-tenant die de kenmerkwaarde UserType van Gast hebben.  Voor efficiëntie moet u ervoor zorgen dat de groep voornamelijk gasten is - selecteer geen groep met ledengebruikers, omdat ledengebruikers niet hoeven te worden beoordeeld.  Houd er ook rekening mee dat een gastgebruiker die lid is van de groep de andere leden van de groep kan zien.

2. Als u een toegangsbeoordeling voor die groep wilt maken, selecteert u de revisoren die de leden zelf zijn. Zie [Een toegangscontrole van groepen of toepassingen maken voor](create-access-review.md)meer informatie.

3. Vraag elke gast om zijn eigen lidmaatschap te herzien. Elke gast die een uitnodiging heeft geaccepteerd, ontvangt standaard een e-mail van Azure AD met een koppeling naar de toegangscontrole in het toegangspaneel van uw organisatie. Azure AD heeft instructies voor gasten over het [controleren van de toegang tot groepen of toepassingen.](perform-access-review.md)  Gasten die hun uitnodiging niet hebben geaccepteerd, worden in de beoordelingsresultaten weergegeven als 'Niet aangemeld'.

4. Nadat de revisoren invoer hebben opgegeven, stopt u de toegangscontrole. Zie [Een toegangscontrole van groepen of toepassingen voltooien](complete-access-review.md)voor meer informatie.

5. Verwijder gasttoegang voor gasten die zijn geweigerd, de beoordeling niet hebben voltooid of hun uitnodiging niet eerder hebben geaccepteerd. Als sommige van de gasten contactpersonen zijn die zijn geselecteerd om deel te nemen aan de beoordeling of als ze niet eerder een uitnodiging hebben geaccepteerd, u hun accounts uitschakelen met behulp van de Azure-portal of PowerShell. Als de gast geen toegang meer nodig heeft en geen contactpersoon is, u het gebruikersobject uit uw map verwijderen met behulp van de Azure-portal of PowerShell om het gastgebruikersobject te verwijderen.

## <a name="next-steps"></a>Volgende stappen

[Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)







