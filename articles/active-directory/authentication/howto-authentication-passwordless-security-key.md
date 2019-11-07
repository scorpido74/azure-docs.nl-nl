---
title: Aanmeldings wachtwoord zonder wacht woord inschakelen voor Azure AD (preview)-Azure Active Directory
description: Aanmeldings wachtwoord zonder wacht woord inschakelen voor Azure AD met behulp van FIDO2-beveiligings sleutels (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50af82e79e7ba8b979ab28a1b3f608ec7e41bfb2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603435"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Aanmelden zonder wacht woord voor beveiligings sleutel inschakelen (preview)

Voor ondernemingen die tegenwoordig wacht woorden gebruiken en een gedeelde PC-omgeving hebben, bieden beveiligings sleutels een naadloze manier voor het verifiëren van werk nemers zonder een gebruikers naam of wacht woord in te voeren. Beveiligings sleutels bieden een verbeterde productiviteit voor werk nemers en hebben betere beveiliging.

Dit document is gericht op het inschakelen van op wacht woord gebaseerde verificatie op basis van een sleutel. Aan het einde van dit artikel kunt u zich met behulp van een FIDO2-beveiligings sleutel aanmelden bij webtoepassingen met uw Azure AD-account.

|     |
| --- |
| FIDO2-beveiligings sleutels zijn een open bare preview-functie van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over Previews|
|     |

## <a name="requirements"></a>Vereisten

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Preview van registratie van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md)
- Compatibele [FIDO2-beveiligings sleutels](concept-authentication-passwordless.md#fido2-security-keys)
- Webauthn vereist Windows 10 versie 1809 of hoger * *

Als u beveiligings sleutels wilt gebruiken om u aan te melden bij Web-apps en-services, moet u een browser hebben die het webauthn-protocol ondersteunt. Dit zijn onder andere micro soft Edge, Chrome, Firefox en Safari.

## <a name="prepare-devices-for-preview"></a>Apparaten voorbereiden voor de preview-versie

Op apparaten waarop u wilt testen, moet Windows 10 versie 1809 of hoger worden uitgevoerd. De beste ervaring is met Windows 10 versie 1903 of hoger.

## <a name="enable-passwordless-authentication-method"></a>Verificatie methode met wacht woord inschakelen

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratie functies voor verificatie methoden met een wacht woord zijn afhankelijk van de gecombineerde registratie preview. Volg de stappen in het artikel [registratie van gecombineerde beveiligings gegevens inschakelen (preview)](howto-registration-mfa-sspr-combined.md)om de gecombineerde registratie voorbeeld in te scha kelen.

### <a name="enable-fido2-security-key-method"></a>FIDO2-beveiligings sleutel methode inschakelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory** > **beveiligings** > **verificatie methoden** > **beleid voor verificatie methode (preview)** .
1. Kies onder de **beveiligings sleutel methode FIDO2**de volgende opties:
   1. **Inschakelen** -ja of Nee
   1. **Doel** -alle gebruikers of Selecteer gebruikers
1. **Sla** de configuratie op.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Gebruikers registratie en het beheer van FIDO2-beveiligings sleutels

1. Blader naar [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Meld u aan als dat nog niet het geval is.
1. Klik op **beveiligings gegevens**.
   1. Als de gebruiker al ten minste één Azure Multi-Factor Authentication-methode heeft geregistreerd, kunnen ze onmiddellijk een FIDO2-beveiligings sleutel registreren.
   1. Als er niet ten minste één Azure Multi-Factor Authentication-methode is geregistreerd, moeten deze er een worden toegevoegd.
1. Voeg een FIDO2-beveiligings sleutel toe door te klikken op **methode toevoegen** en **beveiligings sleutel**te kiezen.
1. Kies een **USB-apparaat** of **NFC-apparaat**.
1. Laat uw sleutel gereed en kies **volgende**.
1. Er wordt een vak weer gegeven waarin de gebruiker wordt gevraagd een pincode voor uw beveiligings sleutel te maken of in te voeren. vervolgens moet u de vereiste penbeweging voor de sleutel, biometrisch of touch, uitvoeren.
1. De gebruiker wordt teruggekeerd naar de gecombineerde registratie-ervaring en er wordt gevraagd om een duidelijke naam op te geven voor de sleutel, zodat de gebruiker kan identificeren dat er meerdere zijn. Klik op **Volgende**.
1. Klik op **gereed** om het proces te volt ooien.

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met een wacht woord zonder referenties

In het voor beeld onder een gebruiker heeft de FIDO2-beveiligings sleutel al ingericht. De gebruiker kan ervoor kiezen om zich aan te melden op het web met de beveiligings sleutel FIDO2 in een ondersteunde browser in Windows 10 versie 1809 of hoger.

![Aanmelden voor beveiligings sleutel micro soft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Problemen oplossen en feedback

Als u feedback wilt delen of problemen ondervindt tijdens het vooraf bekijken van deze functie, kunt u deze delen via de Windows feedback hub-app.

1. Start de **feedback-hub** en zorg ervoor dat u bent aangemeld.
1. Feedback verzenden in de volgende categorisatie:
   1. Categorie: beveiliging en privacy
   1. Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie: **mijn probleem opnieuw maken**

## <a name="known-issues"></a>Bekende problemen

### <a name="security-key-provisioning"></a>Inrichten van beveiligings sleutel

Het inrichten van de beheerder en het ongedaan maken van de inrichting van beveiligings sleutels is niet beschikbaar in de open bare preview.

### <a name="upn-changes"></a>UPN-wijzigingen

Als de UPN van een gebruiker wordt gewijzigd, kunt u FIDO2-beveiligings sleutels niet meer wijzigen om de wijziging aan te brengen. De oplossing is het opnieuw instellen van het apparaat en de gebruiker moet hun FIDO2-beveiligings sleutels opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen

[FIDO2 beveiligings sleutel voor Windows 10 aanmelden](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-verificatie inschakelen voor on-premises resources](howto-authentication-passwordless-security-key-on-premises.md)

[Meer informatie over apparaatregistratie](../devices/overview.md)

[Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
