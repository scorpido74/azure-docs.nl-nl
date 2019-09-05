---
title: Aanmeldings wachtwoord zonder wacht woord inschakelen voor Azure AD (preview)-Azure Active Directory
description: Wacht woordloze beveiligings sleutel aanmelden bij Azure AD inschakelen met behulp van FIDO2-beveiligings sleutels (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316a523a6216354ae5b6166be55e183a4e050766
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305081"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Aanmeldings wachtwoord zonder wacht woord inschakelen voor Azure AD (preview)

## <a name="requirements"></a>Vereisten

* Azure Multi-Factor Authentication
* Gecombineerde registratie Preview met ingeschakelde gebruikers voor SSPR
* Voor beeld van FIDO2-beveiligings sleutel zijn compatibele FIDO2-beveiligings sleutels vereist
* Webauthn vereist micro soft Edge op Windows 10 versie 1809 of hoger
* Voor FIDO2 gebaseerde Windows-aanmelding moet Azure AD zijn toegevoegd aan Windows 10 versie 1809 of hoger

## <a name="prepare-devices-for-preview"></a>Apparaten voorbereiden voor de preview-versie

Op apparaten waarop u wilt testen, moet Windows 10 versie 1809 of hoger worden uitgevoerd. De beste ervaring is met Windows 10 versie 1903 of hoger.

## <a name="enable-security-keys-for-windows-sign-in"></a>Beveiligings sleutels voor Windows-aanmelding inschakelen

Organisaties kunnen ervoor kiezen om een of meer van de volgende methoden te gebruiken om het gebruik van beveiligings sleutels voor Windows-aanmelding in te scha kelen.

### <a name="enable-credential-provider-via-intune"></a>Referentie provider inschakelen via intune

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar **Microsoft intune** > **registratie van Windows** > **Hello voor bedrijven** > -inschrijving > voor het apparaat.
1. Onder **instellingen** instellen **beveiligings sleutels gebruiken voor aanmelden** bij **ingeschakeld**.

Configuratie van beveiligings sleutels voor aanmelden is niet afhankelijk van het configureren van Windows hello voor bedrijven.

#### <a name="enable-targeted-intune-deployment"></a>Beoogde intune-implementatie inschakelen

Als u specifieke apparaatgroepen wilt instellen om de referentie provider in te scha kelen, gebruikt u de volgende aangepaste instellingen via intune. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar **Microsoft intune** > **apparaatconfiguratie** > profielenprofiel > **maken**.
1. Configureer het nieuwe profiel met de volgende instellingen
   1. Naam: Beveiligings sleutels voor Windows-aanmelding
   1. Beschrijving: Hiermee kunnen FIDO-beveiligings sleutels worden gebruikt tijdens Windows-aanmelding
   1. Platform: Windows 10 en hoger
   1. Profiel type: Aanpassen
   1. Aangepaste OMA-URI-instellingen:
      1. Naam: FIDO-beveiligings sleutels inschakelen voor Windows-aanmelding
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Gegevens type: Integer
      1. Waarde: 1 
1. Dit beleid kan worden toegewezen aan specifieke gebruikers, apparaten of groepen. Meer informatie vindt u in het artikel [gebruikers-en apparaatprofielen toewijzen in Microsoft intune](https://docs.microsoft.com/intune/device-profile-assign).

![Beleid voor aangepaste configuratie van intune-apparaten maken](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Referentie provider inschakelen via inrichtings pakket

Voor apparaten die niet worden beheerd door intune, kan een inrichtings pakket worden geïnstalleerd om de functionaliteit in te scha kelen. De app Windows Configuration Designer kan worden geïnstalleerd vanuit de [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Start de ontwerp functie voor Windows-configuratie.
1. Selecteer **bestand** > **Nieuw project**.
1. Geef uw project een naam en noteer het pad waar het project is gemaakt.
1. Selecteer **Volgende**.
1. Verlaat het **inrichtings pakket** dat is geselecteerd als de **geselecteerde project werk stroom** en selecteer **volgende**.
1. Selecteer **alle Windows Desktop-edities** onder **Kies welke instellingen u wilt weer geven en configureren** en selecteer **volgende**.
1. Selecteer **Finish**.
1. Blader in het zojuist gemaakte project naar **runtime-instellingen** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Stel **UseSecurityKeyForSignIn** in op **ingeschakeld**.
1. **Inrichtings pakket** **exporteren** > selecteren
1. Accepteer de standaard waarden in het venster **Build** onder **Beschrijving van het inrichtings pakket** en selecteer **volgende**.
1. Accepteer de standaard waarden in het venster **bouwen** onder **Selecteer beveiligings Details voor het inrichtings pakket** en selecteer **volgende**.
1. Noteer of wijzig het pad in de **Build** -Vensters onder **selecteren waar het inrichtings pakket moet worden opgeslagen** en selecteer **volgende**.
1. Selecteer **samen stellen** op de pagina **het inrichtings pakket bouwen** .
1. Sla de twee gemaakte bestanden (ppkg en Cat) op een locatie op waar u ze op een later tijdstip op de computers kunt Toep assen.
1. Volg de instructies in het artikel [een inrichtings pakket Toep assen](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)om het inrichtings pakket toe te passen dat u hebt gemaakt.

## <a name="obtain-fido2-security-keys"></a>FIDO2-beveiligings sleutels verkrijgen

Zie de sectie FIDO2-beveiligings sleutels in het artikel [Wat is een wacht woord?](concept-authentication-passwordless.md) voor meer informatie over ondersteunde sleutels en fabrikanten.

> [!NOTE]
> Als u voor het kopen en plannen van NFC-beveiligings sleutels wilt gebruiken, hebt u een ondersteunde NFC-lezer nodig.

## <a name="enable-passwordless-authentication-method"></a>Verificatie methode met wacht woord inschakelen

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratie functies voor verificatie methoden met een wacht woord zijn afhankelijk van de gecombineerde registratie preview. Volg de stappen in het artikel [registratie van gecombineerde beveiligings gegevens inschakelen (preview)](howto-registration-mfa-sspr-combined.md)om de gecombineerde registratie voorbeeld in te scha kelen.

### <a name="enable-new-passwordless-authentication-method"></a>Nieuwe verificatie methode met een wacht woord toestaan

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Blader naar **Azure Active Directory** > beleid voor verificatie methode voor**beveiligings** > **verificatie methoden** >  **(preview-versie)**
1. Onder elke **methode**kiest u de volgende opties:
   1. **Inschakelen** -ja of Nee
   1. **Doel** -alle gebruikers of Selecteer gebruikers
1. Elke methode **Opslaan**

> [!WARNING]
> Het FIDO2 ' sleutel restrictie beleid ' werkt nog niet. Deze functionaliteit is beschikbaar voor algemene Beschik baarheid. Wijzig deze beleids regels niet standaard.

> [!NOTE]
> U hoeft niet aan te melden bij beide methoden zonder wacht woord (als u slechts één methode met een wacht woord wilt bekijken, kunt u alleen die methode inschakelen). We raden u aan beide methoden uit te proberen, want ze hebben hun eigen voor delen.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Gebruikers registratie en het beheer van FIDO2-beveiligings sleutels

1. Ga naar [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Meld u aan als dat nog niet is gebeurd
1. Klik op **beveiligings gegevens**
   1. Als de gebruiker al ten minste één Azure Multi-Factor Authentication-methode heeft geregistreerd, kunnen ze onmiddellijk een FIDO2-beveiligings sleutel registreren.
   1. Als er niet ten minste één Azure Multi-Factor Authentication-methode is geregistreerd, moeten deze er een worden toegevoegd.
1. Voeg een FIDO2-beveiligings sleutel toe door te klikken op **methode toevoegen** en **beveiligings sleutel** kiezen
1. **USB-apparaat** of **NFC-apparaat** kiezen
1. Laat uw sleutel gereed en kies **volgende**
1. Er wordt een vak weer gegeven waarin u wordt gevraagd om een pincode voor uw beveiligings sleutel te maken of in te voeren. vervolgens voert u de vereiste penbeweging voor uw sleutel biometrisch of Touch uit.
1. U wordt teruggekeerd naar de gecombineerde registratie-ervaring en u hebt gevraagd om een duidelijke naam op te geven voor uw token, zodat u kunt herkennen wat er allemaal zijn. Klik op **Volgende**.
1. Klik op **gereed** om het proces te volt ooien

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Beveiligings sleutel biometrisch, pincode of opnieuw instellen beveiligings sleutel beheren

* Windows 10 versie 1809
   * Er is een aanvullende software van de leverancier van de beveiligings sleutel vereist
* Windows 10 versie 1903 of hoger
   * Gebruikers kunnen **Windows-instellingen** op de**beveiligings sleutel** van hun apparaat > **accounts** > openen
   * Gebruikers kunnen hun pincode wijzigen, biometrie bijwerken of hun beveiligings sleutel opnieuw instellen

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Gebruikers registratie en het beheer van Microsoft Authenticator-app

Volg de instructies in het artikel [Aanmelden bij uw accounts met behulp van de Microsoft Authenticator-app](../user-help/user-help-auth-app-sign-in.md)om de Microsoft Authenticator-app te configureren voor aanmelden bij de telefoon.

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met een wacht woord zonder referenties

### <a name="sign-in-at-the-lock-screen"></a>Meld u aan op het vergrendelings scherm

In het voor beeld onder een gebruiker Bala Sandhu is de beveiligings sleutel FIDO2 al ingericht. Bala kan de referentie provider voor de beveiligings sleutel kiezen in het vergrendelings scherm van Windows 10 en de beveiligings sleutel voor aanmelding bij Windows invoegen.

![De beveiligings sleutel meldt zich aan bij het vergrendelings scherm van Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Aanmelden op het web

In het voor beeld onder een gebruiker heeft de FIDO2-beveiligings sleutel al ingericht. De gebruiker kan zich aanmelden op het web met de beveiligings sleutel FIDO2 in de micro soft Edge-browser van Windows 10 versie 1809 of hoger.

![Aanmeldings sleutel voor de beveiliging van micro soft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>Bekende problemen

### <a name="security-key-provisioning"></a>Inrichten van beveiligings sleutel

Het inrichten van de beheerder en het ongedaan maken van de inrichting van beveiligings sleutels is niet beschikbaar in de open bare preview.

### <a name="hybrid-azure-ad-join"></a>Hybrid Azure AD Join

Gebruikers die gebruikmaken van WIA SSO die beheerde referenties gebruiken, zoals FIDO2-beveiligings sleutels of aanmelden met een wacht woord waarbij Microsoft Authenticator app moet worden aangemeld bij een hybride verbinding van Windows 10, kunt u profiteren van de voor delen van SSO. Beveiligings sleutels werken echter alleen voor Azure Active Directory gekoppelde machines. We raden u aan om FIDO2-beveiligings sleutels voor het Windows-vergrendelings scherm op zuivere Azure Active Directory gekoppelde computers alleen uit te proberen. Deze beperking is niet van toepassing op het web.

### <a name="upn-changes"></a>UPN-wijzigingen

Er wordt gewerkt aan de ondersteuning van een functie waarmee UPN kan worden gewijzigd op hybride AADJ-en AADJ-apparaten. Als de UPN van een gebruiker wordt gewijzigd, kunt u FIDO2-beveiligings sleutels niet meer wijzigen. De enige benadering is het opnieuw instellen van het apparaat en de gebruiker moet zich opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over apparaatregistratie](../devices/overview.md)

[Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
