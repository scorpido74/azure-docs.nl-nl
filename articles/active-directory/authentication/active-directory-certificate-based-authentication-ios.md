---
title: Verificatie op basis van certificaten op iOS - Azure Active Directory
description: Meer informatie over de ondersteunde scenario's en de vereisten voor het configureren van verificatie op basis van certificaten in oplossingen met iOS-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a042897ecbe35935c1832a53f523eb0597ebafc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654246"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory-certificaatverificatie op iOS

iOS-apparaten kunnen op certificaten gebaseerde verificatie (CBA) gebruiken om te verifiëren naar Azure Active Directory met behulp van een clientcertificaat op hun apparaat wanneer ze verbinding maken met:

* Mobiele Office-toepassingen zoals Microsoft Outlook en Microsoft Word
* Exchange ActiveSync (EAS) clients

Als u deze functie configureert, hoeft u geen combinatie van gebruikersnaam en wachtwoord in bepaalde e-mail- en Microsoft Office-toepassingen op uw mobiele apparaat in te voeren.

In dit onderwerp vindt u de vereisten en de ondersteunde scenario's voor het configureren van CBA op een iOS-apparaat (Android) voor gebruikers van tenants in Office 365 Enterprise, Business, Education, de Amerikaanse overheid, China en Duitsland.

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

## <a name="requirements"></a>Vereisten

De versie van het apparaatbesturingssysteem moet iOS 9 en hoger zijn

Er moet een federatieserver zijn geconfigureerd.

Microsoft Authenticator is vereist voor Office-toepassingen op iOS.

Als Azure Active Directory een clientcertificaat wilt intrekken, moet het ADFS-token de volgende claims hebben:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Het serienummer van het clientcertificaat)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(De tekenreeks voor de uitgever van het clientcertificaat)

Azure Active Directory voegt deze claims toe aan het vernieuwingstoken als ze beschikbaar zijn in het ADFS-token (of een ander SAML-token). Wanneer het vernieuwingstoken moet worden gevalideerd, wordt deze informatie gebruikt om de intrekking te controleren.

Als aanbevolen praktijk moet u de ADFS-foutpagina's van uw organisatie bijwerken met de volgende gegevens:

* De vereiste voor het installeren van de Microsoft Authenticator op iOS
* Instructies voor het verkrijgen van een gebruikerscertificaat.

Zie [De aanmeldingspagina's van AD FS aanpassen](https://technet.microsoft.com/library/dn280950.aspx)voor meer informatie .

Sommige Office-apps (met moderne verificatie ingeschakeld) verzenden *'prompt=login'* naar Azure AD in hun verzoek. Azure AD vertaalt standaard '*prompt=login*' in het verzoek naar ADFS als '*wauth=usernamepassworduri*' (vraagt ADFS om U/P Auth te doen) en '*wfresh=0*' (vraagt ADFS om de SSO-status te negeren en een nieuwe verificatie te doen). Als u verificatie op basis van certificaten voor deze apps wilt inschakelen, moet u het standaard AD-gedrag van Azure wijzigen. Stel gewoon de *'PromptLoginBehavior'* in uw federatieve domeininstellingen in op '*Uitgeschakeld'.*
U de cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) gebruiken om deze taak uit te voeren:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Ondersteuning voor Exchange ActiveSync-clients

Op iOS 9 of hoger wordt de native iOS-e-mailclient ondersteund. Neem voor alle andere Exchange ActiveSync-toepassingen contact op met de ontwikkelaar van de toepassing om te bepalen of deze functie wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

Zie Aan [de slag met verificatie op](../authentication/active-directory-certificate-based-authentication-get-started.md) basis van certificaten op Android voor instructies als u verificatie op basis van certificaten in uw omgeving wilt configureren.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
