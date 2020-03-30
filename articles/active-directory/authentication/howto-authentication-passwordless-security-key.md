---
title: Aanmelden voor een wachtwoordloze beveiligingssleutel (voorbeeld) - Azure Active Directory
description: Aanmelding met wachtwoordloze beveiligingssleutel bij Azure AD inschakelen met FIDO2-beveiligingssleutels (voorbeeld)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e088d239a91edeff34ecd1a7dc5be7a9f8628da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129145"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Aanmelden voor een wachtwoordloze beveiligingssleutel inschakelen (voorbeeld)

Voor bedrijven die tegenwoordig wachtwoorden gebruiken en een gedeelde pc-omgeving hebben, bieden beveiligingssleutels werknemers een naadloze manier om zich te verifiëren zonder een gebruikersnaam of wachtwoord in te voeren. Beveiligingssleutels zorgen voor een hogere productiviteit voor werknemers en hebben een betere beveiliging.

Dit document richt zich op het inschakelen van verificatie op basis van beveiligingssleutel op basis van wachtwoord. Aan het einde van dit artikel u zich aanmelden bij webgebaseerde toepassingen met uw Azure AD-account met behulp van een FIDO2-beveiligingssleutel.

|     |
| --- |
| FIDO2-beveiligingssleutels zijn een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) meer informatie over voorvertoningen|
|     |

## <a name="requirements"></a>Vereisten

- [Azure-verificatie met meerdere factoren](howto-mfa-getstarted.md)
- [Voorbeeld van registratie van gecombineerde beveiligingsgegevens](concept-registration-mfa-sspr-combined.md)
- Compatibele [FIDO2-beveiligingssleutels](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN vereist Windows 10 versie 1809 of hoger**

Als u beveiligingssleutels wilt gebruiken voor het inloggen op web-apps en -services, moet u een browser hebben die het WebAuthN-protocol ondersteunt. Deze omvatten Microsoft Edge, Chrome, Firefox en Safari.

## <a name="prepare-devices-for-preview"></a>Apparaten voorbereiden op voorbeeld

Azure AD-apparaten waarmee u wordt getest, moeten Windows 10-versie 1809 of hoger uitvoeren. De beste ervaring is op Windows 10 versie 1903 of hoger.

Hybride Azure AD-apparaten moeten Windows 10 Insider Build 18945 of nieuwer uitvoeren.

## <a name="enable-passwordless-authentication-method"></a>Verificatiemethode zonder wachtwoord inschakelen

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratiefuncties voor wachtwoordloze verificatiemethoden zijn afhankelijk van de gecombineerde registratievoorbeeld. Volg de stappen in het artikel [Gecombineerde beveiliginggegevensregistratie inschakelen (voorbeeld)](howto-registration-mfa-sspr-combined.md)om het gecombineerde registratievoorbeeld in te schakelen.

### <a name="enable-fido2-security-key-method"></a>Fido2-beveiligingssleutelmethode inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar het**verificatiemethodebeleid voor verificatiemethoden** > voor Azure Active > **Directory-beveiliging** > **(Voorbeeld)**. **Azure Active Directory**
1. Kies onder de methode **FIDO2 Security Key**de volgende opties:
   1. **Inschakelen** - Ja of Nee
   1. **Doel** - Alle gebruikers of Selectie-gebruikers
1. Sla de configuratie **op.**

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Gebruikersregistratie en -beheer van FIDO2-beveiligingssleutels

1. Blader [https://myprofile.microsoft.com](https://myprofile.microsoft.com)naar .
1. Log in als je dat nog niet hebt gedaan.
1. Klik **op Beveiligingsgegevens**.
   1. Als de gebruiker al ten minste één Azure Multi-Factor Authentication-methode heeft geregistreerd, kan hij of zij onmiddellijk een FIDO2-beveiligingssleutel registreren.
   1. Als ze niet ten minste één Azure Multi-Factor Authentication-methode hebben geregistreerd, moeten ze er een toevoegen.
1. Voeg een FIDO2-beveiligingssleutel toe door op **Methode toevoegen** te klikken en **beveiligingssleutel**te kiezen .
1. Kies **USB-apparaat** of **NFC-apparaat**.
1. Maak uw sleutel klaar en kies **Volgende**.
1. Er verschijnt een vakje en vraagt de gebruiker om een pincode voor uw beveiligingssleutel te maken/invoeren en vervolgens het vereiste gebaar voor de sleutel uit te voeren, biometrisch of aanraken.
1. De gebruiker wordt teruggestuurd naar de gecombineerde registratie-ervaring en gevraagd om een zinvolle naam voor de sleutel te geven, zodat de gebruiker kan identificeren welke als ze meerdere hebben. Klik op **Volgende**.
1. Klik **op Gereed** om het proces te voltooien.

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met wachtwoordloze referenties

In het onderstaande voorbeeld heeft een gebruiker zijn FIDO2-beveiligingssleutel al ingericht. De gebruiker kan ervoor kiezen om in te loggen op het web met hun FIDO2-beveiligingssleutel in een ondersteunde browser op Windows 10 versie 1809 of hoger.

![Aanmeldingscode Microsoft Edge van beveiligingssleutel](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Probleemoplossing en feedback

Als u feedback wilt delen of problemen wilt ondervinden tijdens het bekijken van deze functie, deelt u via de Windows Feedback Hub-app de volgende stappen:

1. Start **de feedbackhub** en zorg ervoor dat je bent aangemeld.
1. Feedback verzenden onder de volgende indeling:
   - Categorie: Beveiliging en privacy
   - Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie om **mijn probleem opnieuw te maken**

## <a name="known-issues"></a>Bekende problemen

### <a name="security-key-provisioning"></a>Beveiliging sleutel provisioning

Het inrichten en de-inrichten van beveiligingssleutels door beheerders is niet beschikbaar in de openbare preview.

### <a name="upn-changes"></a>UPN wijzigingen

We werken aan het ondersteunen van een functie waarmee UPN-wijzigingen op hybride Azure AD kunnen worden samengevoegd en Azure AD-apparaten. Als de UPN van een gebruiker verandert, u fido2-beveiligingssleutels niet langer wijzigen om rekening te houden met de wijziging. De resolutie is om het apparaat te resetten en de gebruiker moet opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen

[FIDO2-beveiligingssleutel Windows 10-aanmelding](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-verificatie inschakelen voor on-premises resources](howto-authentication-passwordless-security-key-on-premises.md)

[Meer informatie over apparaatregistratie](../devices/overview.md)

[Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
