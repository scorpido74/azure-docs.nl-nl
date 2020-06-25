---
title: Onverwachte toepassing in de lijst met toepassingen | Microsoft Docs
description: Alle toepassingen in uw Tenant weer geven en begrijpen hoe toepassingen worden weer gegeven in de lijst alle toepassingen onder bedrijfs toepassingen
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97eef1f331d8e6965e378d6a76cfa7a0c50feb9a
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85317462"
---
# <a name="unexpected-application-in-my-applications-list"></a>Onverwachte toepassing in de lijst met toepassingen

Dit artikel helpt u te begrijpen hoe toepassingen worden weer gegeven in de lijst **alle toepassingen** onder **bedrijfs toepassingen**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Alle toepassingen in uw Tenant weer geven

Als u alle toepassingen in uw Tenant wilt zien, moet u het **filter** besturings element gebruiken om **alle toepassingen** in de lijst **alle toepassingen** weer te geven. Volg deze stappen:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

6.  Klik op het **filter** besturings element gebruiken boven aan de **lijst alle toepassingen**.

7.  Stel in het deel venster **filter** de optie voor het **weer geven** van **alle toepassingen in.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Waarom wordt een specifieke toepassing weer gegeven in mijn lijst met alle toepassingen?

Bij het filteren op **alle toepassingen**wordt in de **lijst** **alle toepassingen** elke service-principal-object in uw Tenant weer gegeven. Service-Principal-objecten kunnen op verschillende manieren in deze lijst worden weer gegeven:

1. Wanneer u een toepassing toevoegt vanuit de toepassings galerie, waaronder:

   1. **Azure AD Gallery-toepassingen** : een toepassing die vooraf is geïntegreerd voor eenmalige aanmelding met Azure AD

   2. **Toepassings proxy toepassingen** : een toepassing die in uw on-premises omgeving wordt uitgevoerd en die u wilt voorzien van een beveiligde eenmalige aanmelding op een externe locatie

   3. **Aangepaste, ontwikkelde toepassingen** : een toepassing die uw organisatie wil ontwikkelen op het Azure ad-platform voor toepassings ontwikkeling, maar die mogelijk nog niet bestaat

   4. **Niet-galerie toepassingen** : Breng uw eigen toepassingen. Een wille keurige webkoppeling of een toepassing die een gebruikers naam-en wachtwoord veld weergeeft, ondersteunt SAML-of OpenID Connect Connect-protocollen of ondersteunt SCIM die u wilt integreren voor eenmalige aanmelding met Azure AD.

2. Wanneer u zich aanmeldt voor of zich aanmeldt bij, een toepassing met<sup>een 3-</sup> Party-partij die is geïntegreerd met Azure Active Directory. Een voor beeld is [Smart Sheet](https://app.smartsheet.com/b/home) of [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Als u zich aanmeldt voor of een licentie aan een gebruiker of een groep toevoegt aan een toepassing van de eerste partij, zoals [Microsoft Office 365](https://products.office.com/)

4. Wanneer u een nieuwe toepassings registratie toevoegt door een aangepaste toepassing te maken met behulp van het [toepassings register](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Wanneer u een nieuwe toepassings registratie toevoegt door een aangepaste toepassing te maken met behulp van de [Portal voor toepassings registratie van de v 2.0-toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Wanneer u een toepassing die u ontwikkelt, toevoegt met behulp van de [ASP.NET-verificatie methoden](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) of [verbonden services](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) van Visual Studio

7. Wanneer u een Service-Principal-object maakt met behulp van de [Azure AD Power shell-module](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Wanneer u [een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) als beheerder hebt om gegevens in uw Tenant te gebruiken

9. Wanneer een [gebruiker een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) gebruikt om gegevens in uw Tenant te gebruiken

10. Wanneer u bepaalde services voor het opslaan van gegevens in uw Tenant inschakelt. Een voor beeld is het opnieuw instellen van een wacht woord, dat wordt gemodelleerd als Service-Principal om het beleid voor wachtwoord herstel veilig op te slaan.

Lees [hoe en waarom toepassingen worden toegevoegd aan Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)voor meer informatie over hoe apps worden toegevoegd aan uw Directory.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ik wil de toewijzing van een specifieke gebruiker of groep aan een toepassing verwijderen

Als u een gebruiker of groeps toewijzing wilt verwijderen uit een toepassing, volgt u de stappen in de [toewijzing een gebruiker of groep verwijderen uit een bedrijfs-app in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) -artikel.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ik wil alle toegang tot een toepassing uitschakelen voor elke gebruiker

Als u alle aanmeldingen van gebruikers voor een toepassing wilt uitschakelen, volgt u de stappen in het artikel [Gebruikers aanmeldingen voor een bedrijfs-app uitschakelen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) .

## <a name="i-want-to-delete-an-application-entirely"></a>Ik wil een toepassing volledig verwijderen

Voer de volgende stappen uit om **een toepassing te verwijderen**:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing die u wilt verwijderen.

7. Zodra de toepassing is geladen, klikt u op pictogram **verwijderen** in het **overzichts** venster van de bovenste toepassing.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ik wil alle toekomstige bewerkingen voor gebruikers toestemming voor elke toepassing uitschakelen

Door de toestemming van de gebruiker voor uw hele map uit te scha kelen, voor komt u dat eind gebruikers aan elke toepassing worden doorgestuurd. Beheerders kunnen namens de gebruiker nog steeds toestemming geven. Meer informatie over de toestemming van de toepassing en waarom u dit mogelijk niet wilt doen, kunt u lezen [over de toestemming van gebruikers en beheerders](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Voer de volgende stappen uit om **alle toekomstige bewerkingen voor gebruikers toestemming in uw hele directory uit te scha kelen**:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **gebruikers instellingen**.

6.  Schakel alle toekomstige acties voor gebruikers toestemming uit door de gebruikers in staat te stellen **apps toegang te geven tot hun gegevens** in-of uit te scha **kelen en op** de knop **Opslaan** te klikken.

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
