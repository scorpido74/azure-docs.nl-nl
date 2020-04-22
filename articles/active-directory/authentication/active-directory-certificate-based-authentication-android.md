---
title: Verificatie op Basis van Android-certificaten - Azure Active Directory
description: Meer informatie over de ondersteunde scenario's en de vereisten voor het configureren van verificatie op basis van certificaten in oplossingen met Android-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9760624afec111a271ae5aa0ebbe5533d6ba8d6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680213"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory-certificaatverificatie op Android

Android-apparaten kunnen op certificaten gebaseerde verificatie (CBA) gebruiken om te verifiëren naar Azure Active Directory met behulp van een clientcertificaat op hun apparaat wanneer ze verbinding maken met:

* Mobiele Office-toepassingen zoals Microsoft Outlook en Microsoft Word
* Exchange ActiveSync (EAS) clients

Als u deze functie configureert, hoeft u geen combinatie van gebruikersnaam en wachtwoord in bepaalde e-mail- en Microsoft Office-toepassingen op uw mobiele apparaat in te voeren.

In dit onderwerp vindt u de vereisten en de ondersteunde scenario's voor het configureren van CBA op een Android-apparaat voor gebruikers van tenants in Office 365 Enterprise, Business, Education, us Government, China en Duitsland.

Deze functie is beschikbaar in preview in Office 365 Us Government Defense and Federal plans.

## <a name="microsoft-mobile-applications-support"></a>Ondersteuning voor mobiele toepassingen van Microsoft

| Apps | Ondersteuning |
| --- | --- |
| Azure-app voor informatiebeveiliging |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| Intune Bedrijfsportal |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| Microsoft Teams |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| OneNote |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| OneDrive |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| Outlook |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| Power BI |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| Skype voor Bedrijven |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| Word / Excel / PowerPoint |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |
| Yammer |![Vinkje dat ondersteuning voor deze toepassing betekent][1] |

### <a name="implementation-requirements"></a>Uitvoeringseisen

De versie van het apparaat-besturingssysteem moet Android 5.0 (Lollipop) en hoger zijn.

Er moet een federatieserver zijn geconfigureerd.

Als Azure Active Directory een clientcertificaat wilt intrekken, moet het ADFS-token de volgende claims hebben:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Het serienummer van het clientcertificaat)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(De tekenreeks voor de uitgever van het clientcertificaat)

Azure Active Directory voegt deze claims toe aan het vernieuwingstoken als ze beschikbaar zijn in het ADFS-token (of een ander SAML-token). Wanneer het vernieuwingstoken moet worden gevalideerd, wordt deze informatie gebruikt om de intrekking te controleren.

Als aanbevolen praktijk moet u de ADFS-foutpagina's van uw organisatie bijwerken met de volgende gegevens:

* De vereiste voor het installeren van de Microsoft Authenticator op Android.
* Instructies voor het verkrijgen van een gebruikerscertificaat.

Zie [De aanmeldingspagina's van AD FS aanpassen](https://technet.microsoft.com/library/dn280950.aspx)voor meer informatie .

Sommige Office-apps (met moderne verificatie ingeschakeld) verzenden *'prompt=login'* naar Azure AD in hun verzoek. Azure AD vertaalt standaard '*prompt=login*' in het verzoek naar ADFS als '*wauth=usernamepassworduri*' (vraagt ADFS om U/P Auth te doen) en '*wfresh=0*' (vraagt ADFS om de SSO-status te negeren en een nieuwe verificatie te doen). Als u verificatie op basis van certificaten voor deze apps wilt inschakelen, moet u het standaard AD-gedrag van Azure wijzigen. Stel de '*PromptLoginBehavior*' in uw federatieve domeininstellingen in op '*Uitgeschakeld'.*
U de cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) gebruiken om deze taak uit te voeren:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Ondersteuning voor Exchange ActiveSync-clients

Bepaalde Exchange ActiveSync-toepassingen op Android 5.0 (Lollipop) of hoger worden ondersteund. Neem contact op met de ontwikkelaar van de toepassing om te bepalen of uw e-mailtoepassing deze functie ondersteunt.

## <a name="next-steps"></a>Volgende stappen

Zie Aan [de slag met verificatie op](active-directory-certificate-based-authentication-get-started.md) basis van certificaten op Android voor instructies als u verificatie op basis van certificaten in uw omgeving wilt configureren.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
