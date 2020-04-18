---
title: Verificatie op basis van certificaten op iOS - Azure Active Directory
description: Meer informatie over de ondersteunde scenario's en de vereisten voor het configureren van verificatie op basis van certificaten voor Azure Active Directory in oplossingen met iOS-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639635"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory-certificaatverificatie op iOS

Om de beveiliging te verbeteren, kunnen iOS-apparaten op basis van certificaten verificatie (CBA) gebruiken om te verifiëren naar Azure Active Directory (Azure AD) met behulp van een clientcertificaat op hun apparaat wanneer ze verbinding maken met de volgende toepassingen of services:

* Mobiele Office-toepassingen zoals Microsoft Outlook en Microsoft Word
* Exchange ActiveSync (EAS) clients

Als u certificaten gebruikt, hoeft u geen combinatie van gebruikersnaam en wachtwoord in bepaalde e-mail- en Microsoft Office-toepassingen op uw mobiele apparaat in te voeren.

In dit artikel worden de vereisten en de ondersteunde scenario's voor het configureren van CBA op een iOS-apparaat beschreven. CBA voor iOS is beschikbaar in Azure public clouds, Microsoft Government Cloud, Microsoft Cloud Germany en Microsoft Azure China 21Vianet.

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

Om CBA met iOS te gebruiken, zijn de volgende vereisten en overwegingen van toepassing:

* De versie van het apparaatbesturingssysteem moet iOS 9 of hoger zijn.
* Microsoft Authenticator is vereist voor Office-toepassingen op iOS.
* Er moet een identiteitsvoorkeur worden gemaakt in de macOS-sleutelhanger die de verificatie-URL van de ADFS-server bevat. Zie [Een identiteitsvoorkeur maken in Sleutelhangertoegang op Mac voor](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)meer informatie.

De volgende ADFS-vereisten en overwegingen (Active Directory Federation Services) zijn van toepassing:

* De ADFS-server moet zijn ingeschakeld voor certificaatverificatie en federated-verificatie gebruiken.
* Het certificaat moet enhanced key usage (EKU) gebruiken en het UPN van de gebruiker bevatten in de *alternatieve naam van het onderwerp (NT Principal Name).*

## <a name="configure-adfs"></a>ADFS configureren

Als Azure AD een clientcertificaat wilt intrekken, moet het ADFS-token de volgende claims hebben. Azure AD voegt deze claims toe aan het vernieuwingstoken als ze beschikbaar zijn in het ADFS-token (of een ander SAML-token). Wanneer het vernieuwingstoken moet worden gevalideerd, wordt deze informatie gebruikt om de intrekking te controleren:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- voeg het serienummer van uw klantencertificaat toe
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- voeg de tekenreeks toe voor de uitgever van uw clientcertificaat

Als aanbevolen praktijk moet u ook de ADFS-foutpagina's van uw organisatie bijwerken met de volgende gegevens:

* De vereiste voor het installeren van de Microsoft Authenticator op iOS.
* Instructies voor het verkrijgen van een gebruikerscertificaat.

Zie [De aanmeldingspagina ad FS aanpassen](https://technet.microsoft.com/library/dn280950.aspx)voor meer informatie .

## <a name="use-modern-authentication-with-office-apps"></a>Moderne verificatie gebruiken met Office-apps

Sommige Office-apps met moderne `prompt=login` verificatie zijn verzonden naar Azure AD in hun aanvraag. Azure AD vertaalt `prompt=login` standaard in de aanvraag `wauth=usernamepassworduri` naar ADFS als (vraagt ADFS `wfresh=0` om U/P Auth te doen) en (vraagt ADFS om de SSO-status te negeren en een nieuwe verificatie uit te brengen). Als u verificatie op basis van certificaten voor deze apps wilt inschakelen, wijzigt u het standaard AD-gedrag van Azure.

Als u het standaardgedrag wilt bijwerken, stelt u het '*PromptLoginBehavior*' in de federatieve domeininstellingen in op *Uitgeschakeld.* U de cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) gebruiken om deze taak uit te voeren, zoals in het volgende voorbeeld wordt weergegeven:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Ondersteuning voor Exchange ActiveSync-clients

Op iOS 9 of hoger wordt de native iOS-e-mailclient ondersteund. Neem contact op met de toepassingsontwikkelaar om te bepalen of deze functie wordt ondersteund voor alle andere Exchange ActiveSync-toepassingen.

## <a name="next-steps"></a>Volgende stappen

Zie [Aan de slag met verificatie op basis van certificaten](active-directory-certificate-based-authentication-get-started.md) voor instructies als u verificatie op basis van certificaten wilt configureren in uw omgeving.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
