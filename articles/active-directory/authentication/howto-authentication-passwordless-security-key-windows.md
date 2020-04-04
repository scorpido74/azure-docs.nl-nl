---
title: Aanmeldingscode van wachtwoordloze beveiligingssleutel Windows - Azure Active Directory
description: Meer informatie over het inschakelen van aanmelding met een wachtwoordloze beveiligingssleutel bij Azure Active Directory met FIDO2-beveiligingssleutels (voorbeeld)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8f5d6aaa96c24eb37eb78d237a489f1d25293c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653988"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Aanmelding met wachtwoordloze beveiligingssleutel inschakelen bij Windows 10-apparaten met Azure Active Directory (voorbeeld)

Dit document richt zich op het inschakelen van FIDO2-beveiligingssleutelgebaseerde wachtwoordloze verificatie met Windows 10-apparaten. Aan het einde van dit artikel u zich aanmelden bij zowel uw Azure AD als hybride Azure AD-apparaten met uw Azure AD-account met uw Azure AD-account met behulp van een FIDO2-beveiligingssleutel.

|     |
| --- |
| FIDO2-beveiligingssleutels zijn een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) meer informatie over voorvertoningen|
|     |

## <a name="requirements"></a>Vereisten

| Apparaattype | Neemt deel aan Azure AD | lid is van hybride Azure Active Directory |
| --- | --- | --- |
| [Azure-verificatie met meerdere factoren](howto-mfa-getstarted.md) | X | X |
| [Voorbeeld van registratie van gecombineerde beveiligingsgegevens](concept-registration-mfa-sspr-combined.md) | X | X |
| Compatibele [FIDO2-beveiligingssleutels](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN vereist Windows 10 versie 1809 of hoger | X | X |
| [Azure AD-apparaten vereisen](../devices/concept-azure-ad-join.md) Windows 10-versie 1903 of hoger | X |   |
| [Hybride Azure AD-apparaten vereisen](../devices/concept-azure-ad-join-hybrid.md) Windows 10 Insider Build 18945 of hoger |   | X |
| Volledig gepatchte Windows Server 2016/2019-domeincontrollers. |   | X |
| [Azure AD Connect-versie](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 1.4.32.0 of hoger |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (optioneel) | X | X |
| Inrichtingspakket (optioneel) | X | X |
| Groepsbeleid (optioneel) |   | X |

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden niet ondersteund:

- Windows Server Active Directory Domain Services (AD DS) domein-verbonden (on-premises only apparaten) implementatie.
- RDP-, VDI- en Citrix-scenario's met behulp van een beveiligingssleutel.
- S/MIME met behulp van een beveiligingssleutel.
- "Run as" met behulp van een beveiligingssleutel.
- Log in bij een server met een beveiligingssleutel.
- Als u uw beveiligingssleutel niet hebt gebruikt om u online aan te melden bij uw apparaat, u deze niet gebruiken om u aan te melden of offline te ontgrendelen.
- Een Windows 10-apparaat aanmelden of ontgrendelen met een beveiligingssleutel die meerdere Azure AD-accounts bevat. Dit scenario maakt gebruik van het laatste account dat aan de beveiligingssleutel is toegevoegd. WebAuthN stelt gebruikers in staat om het account te kiezen dat ze willen gebruiken.
- Ontgrendel een apparaat met Windows 10 versie 1809. Gebruik Windows 10 versie 1903 of hoger voor de beste ervaring.

## <a name="prepare-devices-for-preview"></a>Apparaten voorbereiden op voorbeeld

Azure AD-apparaten waarmee u tijdens de functievoorbeeld wordt getest, moeten Windows 10-versie 1809 of hoger uitvoeren. De beste ervaring is op Windows 10 versie 1903 of hoger.

Hybride Azure AD-apparaten moeten Windows 10 Insider Build 18945 of nieuwer uitvoeren.

## <a name="enable-security-keys-for-windows-sign-in"></a>Beveiligingssleutels voor Windows-aanmelding inschakelen

Organisaties kunnen ervoor kiezen om een of meer van de volgende methoden te gebruiken om het gebruik van beveiligingssleutels voor Windows-aanmelding mogelijk te maken op basis van de vereisten van hun organisatie:

- [Inschakelen met Intune](#enable-with-intune)
- [Gerichte intune-implementatie](#targeted-intune-deployment)
- [Inschakelen met een inrichtingspakket](#enable-with-a-provisioning-package)
- [Inschakelen met groepsbeleid (alleen gekoppelde apparaten voor hybride Azure-AD)](#enable-with-group-policy)

> [!IMPORTANT]
> Organisaties met **hybride Azure AD-samengevoegde apparaten** moeten **ook** de stappen in het artikel uitvoeren, [FIDO2-verificatie inschakelen voor on-premises resources](howto-authentication-passwordless-security-key-on-premises.md) voordat Windows 10 FIDO2-verificatie met beveiligingssleutels werkt.
>
> Organisaties met **Azure AD-samengevoegde apparaten** moeten dit doen voordat hun apparaten kunnen verifiëren aan on-premises resources met FIDO2-beveiligingssleutels.

### <a name="enable-with-intune"></a>Inschakelen met Intune

Voer de volgende stappen uit om het gebruik van beveiligingssleutels met Intune in te schakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar **Microsoft Intune** > **Device-inschrijving** > **Windows-inschrijving** > Windows Hello voor**Bedrijven** > **-eigenschappen**.
1. Stel **onder Instellingen** **beveiligingssleutels gebruiken in voor aanmelden** bij **Ingeschakeld**.

De configuratie van beveiligingssleutels voor aanmelding is niet afhankelijk van het configureren van Windows Hello voor Bedrijven.

### <a name="targeted-intune-deployment"></a>Gerichte intune-implementatie

Als u specifieke apparaatgroepen wilt targeten om de referentieprovider in te schakelen, gebruikt u de volgende aangepaste instellingen via Intune:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar **Microsoft Intune-apparaatconfiguratieprofielen** > **Device configuration** > **Profiles** > **Profiel maken.**
1. Configureer het nieuwe profiel met de volgende instellingen:
   - Naam: Beveiligingssleutels voor Windows-aanmelding
   - Beschrijving: Hiermee kunnen FIDO-beveiligingssleutels worden gebruikt tijdens Windows-aanmelding
   - Platform: Windows 10 en hoger
   - Profieltype: Aangepast
   - Aangepaste OMA-URI-instellingen:
      - Naam: FIDO-beveiligingssleutels inschakelen voor Windows-aanmelding
      - OMA-URI: ./Apparaat/Leverancier/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Gegevenstype: geheel getal
      - Waarde: 1
1. Dit beleid kan worden toegewezen aan specifieke gebruikers, apparaten of groepen. Zie [Gebruikers- en apparaatprofielen toewijzen in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign)voor meer informatie.

![Het maken van aangepast apparaatconfiguratiebeleid intunen](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Inschakelen met een inrichtingspakket

Voor apparaten die niet door Intune worden beheerd, kan een inrichtingspakket worden geïnstalleerd om de functionaliteit in te schakelen. De Windows Configuration Designer-app kan worden geïnstalleerd vanuit de [Microsoft Store.](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22) Voer de volgende stappen uit om een inrichtingspakket te maken:

1. Start de Windows Configuration Designer.
1. Selecteer **Bestand** > **Nieuw project**.
1. Geef uw project een naam en noteer het pad waar uw project wordt gemaakt en selecteer **Volgende**.
1. Het *inrichtingspakket* geselecteerd laten als **de geselecteerde projectwerkstroom** en selecteer **Volgende**.
1. Selecteer *Alle Bureaubladuitgaven van Windows* onder Kiezen welke instellingen u wilt **weergeven en configureren**en selecteer **Volgende**.
1. Selecteer **Finish**.
1. Blader in uw nieuw gemaakte project naar **Runtime-instellingen** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. **Stel UseSecurityKeyForSignIn in** op *ingeschakeld*.
1. Pakket **Exportinrichting** > **selecteren**
1. Laat de standaardinstellingen in het venster **Bouwen** onder **Het inrichtingspakket beschrijven**en selecteer **Volgende**.
1. Laat de standaardinstellingen in het venster **Bouwen** onder **Beveiligingsgegevens selecteren voor het inrichtingspakket** en selecteer **Volgende**.
1. Let op of wijzig het pad in de **vensteren Bouwen** onder **Selecteren waar u het inrichtingspakket wilt opslaan** en selecteer **Volgende**.
1. Selecteer **Bouwen** op de pagina **Het inrichtingspakket bouwen.**
1. Sla de twee gemaakte bestanden *(ppkg* en *cat)* op een locatie waar u ze later op machines toepassen.
1. Zie [Een inrichtingspakket toepassen](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)als u het inrichtingspakket wilt toepassen dat u hebt gemaakt.

> [!NOTE]
> Apparaten met Windows 10 Versie 1809 moeten ook de gedeelde pc-modus inschakelen *(EnableSharedPCMode).* Zie [Een gedeelde of gast-pc instellen met Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)voor meer informatie over het inschakelen van deze functionaliteit.

### <a name="enable-with-group-policy"></a>Inschakelen met groepsbeleid

Voor **hybride Azure AD-apparaten**kunnen organisaties de volgende groepsbeleidsinstelling configureren om de aanmelding van FIDO-beveiligingssleutels in te schakelen. De instelling is te vinden onder **Computer Configuration** > **Administrative Templates** > **System** > **Logon** > **Turn on security key sign-in:**

- Als u dit beleid instelt **op Ingeschakeld,** kunnen gebruikers zich aanmelden met beveiligingssleutels.
- Als u dit beleid instelt **op Uitgeschakeld** of **niet geconfigureerd,** kunnen gebruikers zich niet aanmelden met beveiligingssleutels.

Voor deze groepsbeleidsinstelling is `credentialprovider.admx` een bijgewerkte versie van de sjabloon Groepsbeleid vereist. Deze nieuwe sjabloon is beschikbaar met de volgende versie van Windows Server en met Windows 10 20H1. Deze instelling kan worden beheerd met een apparaat waarop een van deze nieuwere versies van Windows wordt uitgevoerd of centraal door de richtlijnen in het ondersteuningsonderwerp, [Hoe de centrale opslag voor groepsbeleidsbeheerbeheersjablonen in Windows te maken en te beheren.](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)

## <a name="sign-in-with-fido2-security-key"></a>Inloggen met FIDO2-beveiligingssleutel

In het onderstaande voorbeeld heeft een gebruiker met de naam Bala Sandhu zijn FIDO2-beveiligingssleutel al ingericht met behulp van de stappen in het vorige artikel, [Wachtwoordloos aanmelden voor beveiligingssleutels inschakelen.](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys) Voor hybride Azure AD-apparaten moet u ervoor zorgen dat u ook [aanmelding met wachtwoordloze beveiligingssleutel hebt ingeschakeld bij on-premises resources.](howto-authentication-passwordless-security-key-on-premises.md) Bala kan de leverancier van beveiligingssleutels kiezen op het vergrendelingsscherm van Windows 10 en de beveiligingssleutel invoegen om u bij Windows aan te melden.

![Aanmelden voor beveiligingssleutels op het vergrendelingsscherm van Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Beveiligingssleutel biometrische gegevenscode, pincode of resetbeveiligingssleutel beheren

* Windows 10 versie 1903 of hoger
   * Gebruikers kunnen **Windows-instellingen openen** op hun apparaat >**beveiligingssleutel** **voor accounts** > 
   * Gebruikers kunnen hun pincode wijzigen, biometrie bijwerken of hun beveiligingssleutel resetten

## <a name="troubleshooting-and-feedback"></a>Probleemoplossing en feedback

Als u feedback wilt delen of problemen wilt ondervinden tijdens het bekijken van deze functie, deelt u via de Windows Feedback Hub-app de volgende stappen:

1. Start **de feedbackhub** en zorg ervoor dat je bent aangemeld.
1. Feedback verzenden onder de volgende indeling:
   - Categorie: Beveiliging en privacy
   - Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie om **mijn probleem opnieuw te maken**

## <a name="next-steps"></a>Volgende stappen

[Toegang tot on-premises bronnen voor Azure AD en hybride Azure AD-apparaten inschakelen](howto-authentication-passwordless-security-key-on-premises.md)

[Meer informatie over apparaatregistratie](../devices/overview.md)

[Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
