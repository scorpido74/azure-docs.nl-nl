---
title: Verificatie op basis van certificaten op iOS-Azure Active Directory
description: Meer informatie over de ondersteunde scenario's en de vereisten voor het configureren van verificatie op basis van certificaten in oplossingen met iOS-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f9e7d71ab660c4df6f65d6bebe1d3854086bdd
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848796"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Op certificaten gebaseerde verificatie op iOS Azure Active Directory

iOS-apparaten kunnen authenticatie op basis van certificaten (dit) gebruiken voor de verificatie van Azure Active Directory met behulp van een client certificaat op hun apparaat bij het maken van verbinding met:

* Mobiele Office-toepassingen, zoals micro soft Outlook en micro soft Word
* Exchange ActiveSync-clients (EAS)

Als u deze functie configureert, hoeft u geen combi natie van gebruikers naam en wacht woord op te geven in bepaalde mail-en Microsoft Office toepassingen op uw mobiele apparaat.

In dit onderwerp vindt u de vereisten en ondersteunde scenario's voor het configureren van dit op een iOS-apparaat (Android) voor gebruikers van tenants in Office 365 Enter prise, Business, education, Amerikaanse overheid, China en Duitsland plannen.

Deze functie is beschikbaar als preview-versie van Office 365 Amerikaanse overheids instellingen en federale plannen.

## <a name="microsoft-mobile-applications-support"></a>Ondersteuning voor micro soft Mobile Applications

| Apps | Ondersteuning |
| --- | --- |
| App Azure Information Protection |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Intune-bedrijfsportal |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Microsoft Teams |![Vinkje voor ondersteuning voor deze toepassing][1] |
| OneNote |![Vinkje voor ondersteuning voor deze toepassing][1] |
| OneDrive |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Outlook |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Power BI |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Skype voor Bedrijven |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Word/Excel/Power Point |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Yammer |![Vinkje voor ondersteuning voor deze toepassing][1] |

## <a name="requirements"></a>Vereisten

De versie van het besturings systeem van het apparaat moet iOS 9 en hoger zijn

Er moet een Federatie server worden geconfigureerd.

Microsoft Authenticator is vereist voor Office-toepassingen op iOS.

Als Azure Active Directory een client certificaat wilt intrekken, moet het ADFS-token de volgende claims hebben:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (het serie nummer van het client certificaat)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (de teken reeks voor de verlener van het client certificaat)

Azure Active Directory voegt deze claims toe aan het vernieuwings token als deze beschikbaar zijn in het ADFS-token (of een ander SAML-token). Wanneer het vernieuwings token moet worden gevalideerd, wordt deze informatie gebruikt om het intrekken te controleren.

Als best practice moet u de ADFS-fout pagina's van uw organisatie bijwerken met de volgende gegevens:

* De vereiste voor het installeren van de Microsoft Authenticator op iOS
* Instructies voor het ophalen van een gebruikers certificaat.

Zie [de AD FS-aanmeldings pagina's aanpassen](https://technet.microsoft.com/library/dn280950.aspx)voor meer informatie.

Sommige Office-apps (waarvoor moderne verificatie is ingeschakeld) verzenden '*prompt = login*' naar Azure AD in de aanvraag. Standaard vertaalt Azure AD '*prompt = login*' in de aanvraag bij ADFS als '*wauth = usernamepassworduri*' (vraagt ADFS om u/P-verificatie te doen) en '*wfresh = 0*' (vraagt ADFS de SSO-status te negeren en een nieuwe verificatie uit te voeren). Als u verificatie op basis van certificaten voor deze apps wilt inschakelen, moet u het standaard gedrag van Azure AD wijzigen. Stel de*PromptLoginBehavior*in uw federatieve domein instellingen in op*uitgeschakeld*.
U kunt de [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) -cmdlet gebruiken om deze taak uit te voeren:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Ondersteuning voor Exchange ActiveSync-clients

Op iOS 9 of hoger wordt de systeem eigen iOS-e-mailclient ondersteund. Neem contact op met de ontwikkelaar van de toepassing voor alle andere Exchange ActiveSync-toepassingen om te bepalen of deze functie wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

Als u verificatie op basis van certificaten in uw omgeving wilt configureren, raadpleegt u aan de [slag met verificatie op basis van certificaten op Android](../authentication/active-directory-certificate-based-authentication-get-started.md) voor instructies.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
