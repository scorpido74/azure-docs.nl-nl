---
title: Onverwachte aanvraag in mijn lijst met aanvragen | Microsoft Documenten
description: Alle toepassingen in uw tenant bekijken en begrijpen hoe toepassingen worden weergegeven in uw lijst met alle toepassingen onder Enterprise-toepassingen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cb4eeb52d0680695bda266ad1a563b2ef5ee02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781098"
---
# <a name="unexpected-application-in-my-applications-list"></a>Onverwachte toepassing in mijn lijst met toepassingen

In dit artikel u begrijpen hoe toepassingen worden weergegeven in uw lijst **Met alle toepassingen** onder **Ondernemingstoepassingen**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Hoe u alle toepassingen in uw tenant bekijken

Als u alle toepassingen in uw tenant wilt bekijken, moet u het **besturingselement Filter** gebruiken om **Alle toepassingen** weer te geven onder de lijst **Alle toepassingen.** Volg deze stappen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

6.  klik op het besturingselement **Filter gebruiken** boven aan de lijst **Alle toepassingen**.

7.  Stel in het deelvenster **Filter** de optie **Weergeven** in **op Alle toepassingen.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Waarom wordt een specifieke toepassing weergegeven in mijn lijst met alle toepassingen?

Wanneer deze wordt gefilterd naar **Alle toepassingen,** wordt in de **lijst** **Alle toepassingen** elk serviceprincipal-object in uw tenant weergegeven. Serviceprincipal-objecten kunnen op verschillende manieren in deze lijst worden weergegeven:

1. Wanneer u een toepassing toevoegt vanuit de toepassingsgalerie, waaronder:

   1. **Azure AD-galerietoepassingen** – een toepassing die vooraf is geïntegreerd voor eenmalige aanmelding met Azure AD

   2. **Application Proxy Applications** – Een toepassing die wordt uitgevoerd in uw on-premises omgeving waarop u externe toepassingen veilig wilt aanmelden

   3. **Op maat ontwikkelde toepassingen** – Een toepassing die uw organisatie wil ontwikkelen op het Azure AD Application Development Platform, maar die mogelijk nog niet bestaat

   4. **Niet-Gallery Toepassingen** – Breng uw eigen toepassingen! Elke gewenste weblink of elke toepassing die een gebruikersnaam en wachtwoordveld weergeeft, SAML- of OpenID Connect-protocollen ondersteunt of SCIM ondersteunt die u wilt integreren voor eenmalige aanmelding met Azure AD.

2. Wanneer u zich aanmeldt voor of zich aanmeldt bij een toepassing<sup>van derden</sup> die is geïntegreerd met Azure Active Directory. Een voorbeeld is [Smartsheet](https://app.smartsheet.com/b/home) of [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Wanneer u zich aanmeldt voor of een licentie toevoegt aan een gebruiker of een groep aan een first party-toepassing, zoals [Microsoft Office 365](https://products.office.com/)

4. Wanneer u een nieuwe toepassingsregistratie toevoegt door een op maat ontwikkelde toepassing te maken met behulp van het [toepassingsregister](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Wanneer u een nieuwe toepassingsregistratie toevoegt door een op maat ontwikkelde toepassing te maken met behulp van de [V2.0 Application Registration portal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Wanneer u een toepassing toevoegt die u ontwikkelt met behulp van de [ASP.net-verificatiemethoden](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) of [verbonden services](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) van Visual Studio

7. Wanneer u een hoofdobject voor service maakt met de [Azure AD PowerShell-module](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Wanneer u [instemt met een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) als beheerder om gegevens in uw tenant te gebruiken

9. Wanneer een [gebruiker instemt met een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) om gegevens in uw tenant te gebruiken

10. Wanneer u bepaalde services inschakelt die gegevens opslaan in uw tenant. Een voorbeeld is Password Reset, dat is gemodelleerd als een serviceprincipal om uw wachtwoordresetbeleid veilig op te slaan.

Lees Hoe en waarom toepassingen worden toegevoegd [aan Azure AD voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)meer informatie over de manier waarop apps aan uw map worden toegevoegd.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ik wil de toewijzing van een specifieke gebruiker of groep naar een toepassing verwijderen

Als u een gebruiker of groepstoewijzing wilt verwijderen naar een toepassing, voert u de stappen uit die worden vermeld in de [taak van een gebruiker of groep verwijderen uit een bedrijfsapp in het](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) Azure Active Directory-artikel.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ik wil alle toegang tot een toepassing voor elke gebruiker uitschakelen

Als u alle aanmeldingen van gebruikers bij een toepassing wilt uitschakelen, voert u de stappen uit die worden vermeld in de [aanmeldingen van gebruikers voor een bedrijfsapp uitschakelen in het](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) Azure Active Directory-artikel.

## <a name="i-want-to-delete-an-application-entirely"></a>Ik wil een toepassing volledig verwijderen

Voer de volgende stappen uit om een toepassing te **verwijderen:**

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt verwijderen.

7. Zodra de toepassing is geladen, klikt u op Pictogram **Verwijderen** in het **deelvenster Overzicht** van de bovenste toepassing.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ik wil alle toekomstige bewerkingen voor gebruikerstoestemming voor elke toepassing uitschakelen

Het uitschakelen van de toestemming van de gebruiker voor uw hele map voorkomt dat eindgebruikers toestemming geven voor een toepassing. Beheerders kunnen nog steeds toestemming geven namens de gebruiker. Lees Toestemming voor gebruiker en beheerder begrijpen voor meer informatie over toestemming voor toepassingen en waarom u al dan niet toestemming wilt [geven.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

Voer de volgende stappen uit om **alle toekomstige bewerkingen voor toestemming van gebruikers in uw hele map uit**te schakelen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Gebruikersinstellingen**.

6.  Schakel alle toekomstige bewerkingen voor toestemming van gebruikers uit door de instellingen in te stellen **dat gebruikers toegang kunnen geven tot hun gegevensschakeling** op **Nee** en op de knop **Opslaan** klikken.

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
