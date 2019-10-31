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
ms.openlocfilehash: b5758b1fbb9d311219e3dc4dd483691f6c9d80c1
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172168"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Aanmeldings wachtwoord zonder wacht woord inschakelen voor Windows 10-apparaten (preview-versie)

Dit document is gericht op het inschakelen van FIDO2-verificatie op basis van wacht woorden met Windows 10-apparaten. Aan het einde van dit artikel kunt u zich aanmelden bij zowel webtoepassingen als uw aan Azure AD gekoppelde Windows 10-apparaten met uw Azure AD-account met behulp van een FIDO2-beveiligings sleutel.

|     |
| --- |
| FIDO2-beveiligings sleutels zijn een open bare preview-functie van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over Previews|
|     |

## <a name="requirements"></a>Vereisten

| Apparaattype | Toegevoegd aan Azure AD | Hybride Azure AD-join |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Preview van registratie van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md) | X | X |
| Compatibele [FIDO2-beveiligings sleutels](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| Webauthn vereist Windows 10 versie 1809 of hoger | X | X |
| Voor apparaten die lid zijn van [Azure AD](../devices/concept-azure-ad-join.md) is Windows 10 versie 1809 of hoger vereist | X |   |
| Voor [hybride Azure AD gekoppelde apparaten](../devices/concept-azure-ad-join-hybrid.md) is Windows 10 Insider build 18945 of hoger vereist |   | X |
| Volledige patches voor Windows Server 2016/2019-domein controllers. |   | X |
| Upgrade uitvoeren naar de nieuwste versie van [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) |   | X |
| [Microsoft intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (optioneel) | X | X |
| Inrichtings pakket (optioneel) | X | X |
| Groepsbeleid (optioneel) |   | X |

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

- De implementatie van Windows Server Active Directory Domain Services (AD DS) aan een domein (alleen on-premises apparaten) wordt **niet ondersteund**.
- RDP-, VDI-en Citrix-scenario's worden **niet ondersteund** met behulp van de beveiligings sleutel.
- S/MIME wordt **niet ondersteund** met behulp van de beveiligings sleutel.
- ' Uitvoeren als ' wordt **niet ondersteund** met behulp van de beveiligings sleutel.
- Aanmelden bij een server met behulp van een beveiligings sleutel wordt **niet ondersteund**.
- Als u uw beveiligings sleutel niet hebt gebruikt om u online aan te melden bij uw apparaat, kunt u dit niet gebruiken om u aan te melden of offline te ontgrendelen.

## <a name="prepare-devices-for-preview"></a>Apparaten voorbereiden voor de preview-versie

In azure AD gekoppelde apparaten waarop u wilt testen, moet Windows 10 versie 1809 of hoger worden uitgevoerd. De beste ervaring is met Windows 10 versie 1903 of hoger.

Aan hybride Azure AD gekoppelde apparaten waarop u wilt testen, moet Windows 10 Insider build 18945 of hoger worden uitgevoerd.

## <a name="enable-security-keys-for-windows-sign-in"></a>Beveiligings sleutels voor Windows-aanmelding inschakelen

Organisaties kunnen ervoor kiezen om een of meer van de volgende methoden te gebruiken om het gebruik van beveiligings sleutels voor Windows-aanmelding in te scha kelen op basis van de vereisten van de organisatie.

- [Inschakelen met intune](#enable-with-intune)
   - [Doel gerichte intune-implementatie](#targeted-intune-deployment)
- [Inschakelen met een inrichtings pakket](#enable-with-a-provisioning-package)
- [Inschakelen met groepsbeleid (alleen voor hybride Azure AD gekoppelde apparaten)](#enable-with-group-policy)

> [!IMPORTANT]
> Organisaties met **hybride Azure AD gekoppelde apparaten** moeten **ook** de stappen in het artikel uitvoeren, [FIDO2-verificatie inschakelen voor on-premises resources](howto-authentication-passwordless-security-key-on-premises.md) voordat de verificatie van de beveiligings sleutel voor Windows 10 FIDO2 wordt uitgevoerd.
>
> Organisaties met **apparaten die lid zijn van Azure AD** moeten dit doen voordat hun apparaten zich kunnen aanmelden bij on-premises resources met FIDO2-beveiligings sleutels.

### <a name="enable-with-intune"></a>Inschakelen met intune

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Blader naar **Microsoft Intune** > **apparaatregistratie** > **Windows-inschrijving** > **Windows hello voor bedrijven** > - **Eigenschappen**.
1. Onder **instellingen** instellen **beveiligings sleutels gebruiken voor aanmelden** bij **ingeschakeld**.

Configuratie van beveiligings sleutels voor aanmelden is niet afhankelijk van het configureren van Windows hello voor bedrijven.

#### <a name="targeted-intune-deployment"></a>Doel gerichte intune-implementatie

Als u specifieke apparaatgroepen wilt instellen om de referentie provider in te scha kelen, gebruikt u de volgende aangepaste instellingen via intune.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Blader naar **Microsoft Intune** > **apparaatconfiguratie** > **profielen** > **profiel maken**.
1. Configureer het nieuwe profiel met de volgende instellingen
   1. Naam: beveiligings sleutels voor Windows-aanmelding
   1. Beschrijving: Hiermee kunt u FIDO-beveiligings sleutels gebruiken tijdens het aanmelden bij Windows
   1. Platform: Windows 10 en hoger
   1. Profiel type: aangepast
   1. Aangepaste OMA-URI-instellingen:
      1. Naam: Schakel FIDO-beveiligings sleutels voor Windows-aanmelding in
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Gegevens type: geheel getal
      1. Waarde: 1
1. Dit beleid kan worden toegewezen aan specifieke gebruikers, apparaten of groepen. Meer informatie vindt u in het artikel [gebruikers-en apparaatprofielen toewijzen in Microsoft intune](https://docs.microsoft.com/intune/device-profile-assign).

![Beleid voor aangepaste configuratie van intune-apparaten maken](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Inschakelen met een inrichtings pakket

Voor apparaten die niet worden beheerd door intune, kan een inrichtings pakket worden geïnstalleerd om de functionaliteit in te scha kelen. De app Windows Configuration Designer kan worden geïnstalleerd vanuit de [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Start de ontwerp functie voor Windows-configuratie.
1. Selecteer **bestand** > **Nieuw project**.
1. Geef uw project een naam en noteer het pad waar het project is gemaakt.
1. Selecteer **Next**.
1. Verlaat het **inrichtings pakket** dat is geselecteerd als de **geselecteerde project werk stroom** en selecteer **volgende**.
1. Selecteer **alle Windows Desktop-edities** onder **Kies welke instellingen u wilt weer geven en configureren** en selecteer **volgende**.
1. Selecteer **Finish**.
1. Blader in het zojuist gemaakte project naar **runtime-instellingen** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Stel **UseSecurityKeyForSignIn** in op **ingeschakeld**.
1.  > **inrichtings pakket** voor **export** selecteren
1. Accepteer de standaard waarden in het venster **Build** onder **Beschrijving van het inrichtings pakket** en selecteer **volgende**.
1. Accepteer de standaard waarden in het venster **bouwen** onder **Selecteer beveiligings Details voor het inrichtings pakket** en selecteer **volgende**.
1. Noteer of wijzig het pad in de **Build** -Vensters onder **selecteren waar het inrichtings pakket moet worden opgeslagen** en selecteer **volgende**.
1. Selecteer **samen stellen** op de pagina **het inrichtings pakket bouwen** .
1. Sla de twee gemaakte bestanden (ppkg en Cat) op een locatie op waar u ze op een later tijdstip op de computers kunt Toep assen.
1. Volg de instructies in het artikel [een inrichtings pakket Toep assen](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)om het inrichtings pakket toe te passen dat u hebt gemaakt.

> [!NOTE]
> Op apparaten met Windows 10 versie 1809 moet ook de modus gedeelde PC (EnableSharedPCMode) worden ingeschakeld. Informatie over het inschakelen van deze functionaliteit vindt u in het artikel, [een gedeelde of gast pc instellen met Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Inschakelen met groepsbeleid

Voor **Hybrid Azure AD aangesloten apparaten** kunnen organisaties de volgende Groepsbeleid instellen om aanmelden met Fido-beveiligings sleutel in te scha kelen.

U kunt de instelling vinden onder **computer configuratie** > **Beheersjablonen** > **systeem** > **Aanmelden** > aanmelden met **beveiligings sleutel inschakelen**.

- Als u dit beleid instelt op **ingeschakeld** , kunnen gebruikers zich aanmelden met beveiligings sleutels.
- Als u dit beleid instelt op **uitgeschakeld** of **niet geconfigureerd** , kunnen gebruikers zich niet aanmelden met beveiligings sleutels.

Voor deze groepsbeleid instelling is een bijgewerkte versie van de `credentialprovider.admx` groepsbeleid sjabloon vereist. Deze nieuwe sjabloon is beschikbaar in de volgende versie van Windows Server en met Windows 10 20H1. Deze instelling kan worden beheerd met een apparaat waarop een van deze nieuwere versies van Windows of een andere versie wordt uitgevoerd door de richt lijnen in het ondersteunings onderwerp te volgen, [het centrale archief voor Groepsbeleid-Beheersjablonen in Windows te maken en te beheren](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Aanmelden met FIDO2-beveiligings sleutel

In het voor beeld onder een gebruiker Bala Sandhu is al hun FIDO2-beveiligings sleutel ingericht met behulp van de stappen in het vorige artikel, [wacht woordloze beveiligings sleutel aanmelden inschakelen](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala kan de referentie provider voor de beveiligings sleutel kiezen in het vergrendelings scherm van Windows 10 en de beveiligings sleutel voor aanmelding bij Windows invoegen.

![De beveiligings sleutel meldt zich aan bij het vergrendelings scherm van Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Beveiligings sleutel biometrisch, pincode of opnieuw instellen beveiligings sleutel beheren

* Windows 10 versie 1903 of hoger
   * Gebruikers kunnen **Windows-instellingen** op hun apparaat > **accounts** > **beveiligings sleutel** openen
   * Gebruikers kunnen hun pincode wijzigen, biometrie bijwerken of hun beveiligings sleutel opnieuw instellen

## <a name="troubleshooting-and-feedback"></a>Problemen oplossen en feedback

Als u feedback wilt delen of problemen ondervindt tijdens het vooraf bekijken van deze functie, kunt u deze delen via de Windows feedback hub-app.

1. Start de **feedback-hub** en zorg ervoor dat u bent aangemeld.
1. Feedback verzenden in de volgende categorisatie:
   1. Categorie: beveiliging en privacy
   1. Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie: **mijn probleem opnieuw maken**

## <a name="next-steps"></a>Volgende stappen

[Toegang tot on-premises resources inschakelen voor Azure AD en hybride Azure AD gekoppelde apparaten](howto-authentication-passwordless-security-key-on-premises.md)

[Meer informatie over apparaatregistratie](../devices/overview.md)

[Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
