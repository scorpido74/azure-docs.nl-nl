---
title: Uw telefoonnummer instellen als verificatiemethode - Azure AD
description: De pagina Beveiligingsgegevens (voorbeeld) instellen om uw identiteit te verifiëren met uw telefoonnummer en mobiele apparaat als verificatiemethode.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: e85be9140aabe5b66e63d3ccdd4a3ea907a1d6fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062401"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Een telefoonnummer instellen als verificatiemethode

U deze stappen volgen om uw methoden voor verificatie en wachtwoord opnieuw instellen toe te voegen. Nadat u dit de eerste keer hebt ingesteld, u terugkeren naar de pagina **Beveiligingsgegevens** om uw beveiligingsgegevens toe te voegen, bij te werken of te verwijderen.

Als u wordt gevraagd dit onmiddellijk in te stellen nadat u zich hebt aangemeld bij uw werk- of schoolaccount, raadpleegt u de gedetailleerde stappen in het artikel Uw beveiligingsgegevens instellen in het artikel [met de aanmeldingspaginaprompt.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Beveiligingsgegevens ondersteunen geen telefoonextensies. Zelfs als u het juiste formaat toevoegt, +1 4255551234X12345, worden de extensies verwijderd voordat de oproep wordt geplaatst.
>
> Als u geen telefoonoptie ziet, is het mogelijk dat uw organisatie u niet toestaat deze optie te gebruiken voor verificatie. In dit geval moet u een andere methode kiezen of contact opnemen met de helpdesk van uw organisatie voor meer hulp.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificatie van beveiliging versus verificatie voor het opnieuw instellen van wachtwoorden

Beveiligingsinformatiemethoden worden gebruikt voor zowel tweestapsbeveiligingsverificatie als voor het opnieuw instellen van wachtwoorden. Niet elke methode kan echter voor beide worden gebruikt.

| Methode | Gebruikt voor |
| ------ | -------- |
| Authenticator-app | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Sms-berichten | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Telefoonoproep | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Beveiligingssleutel | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| E-mailaccount | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |
| Beveiligingsvragen | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Telefoongesprekken instellen vanaf de pagina Beveiligingsgegevens

Afhankelijk van de instellingen van uw organisatie u telefoongesprekken mogelijk gebruiken als een van uw beveiligingsgegevensmethoden.

>[!Note]
>Als u een sms-bericht wilt ontvangen in plaats van een telefoongesprek, voert u de stappen in het [beveiligingsgegevens instellen om sms-artikelen te gebruiken.](security-info-setup-text-msg.md)

### <a name="to-set-up-phone-calls"></a>Telefoongesprekken instellen

1. Meld u aan bij uw werk- https://myprofile.microsoft.com/ of schoolaccount en ga vervolgens naar uw pagina.

    ![Mijn profielpagina met gemarkeerde koppelingen naar beveiligingsgegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **Beveiligingsgegevens** in het linkernavigatiedeelvenster of in de koppeling in het blok **Beveiligingsgegevens** en selecteer **vervolgens Methode toevoegen** op de pagina **Beveiligingsgegevens.**

    ![Pagina Beveiligingsgegevens met gemarkeerde optie Methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer **op** de pagina Een methode toevoegen de optie **Telefoon** in de vervolgkeuzelijst en selecteer **Vervolgens Toevoegen**.

    ![Het vakmethode toevoegen, met Telefoon geselecteerd](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Typ **op** de pagina Telefoon het telefoonnummer voor uw mobiele apparaat, kies **Bel mij**en selecteer **Volgende**.

    ![Telefoonnummer toevoegen en telefoongesprekken kiezen](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Beantwoord het verificatietelefoontje, stuur naar het telefoonnummer dat u hebt ingevoerd en volg de instructies.

    De pagina wordt gewijzigd om uw succes weer te geven.

    ![Melding van succes, het verbinden van het telefoonnummer, de keuze om telefoongesprekken te ontvangen en uw account](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Uw beveiligingsgegevens worden bijgewerkt en u telefoongesprekken gebruiken om uw identiteit te verifiëren wanneer u verificatie in twee stappen of wachtwoordreset gebruikt. Zie de sectie [Uw standaardbeveiligingsmethode wijzigen](#change-your-default-security-info-method) van dit artikel als u telefoongesprekken wilt voeren met uw standaardmethode.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Telefoongesprekken verwijderen uit uw beveiligingsgegevensmethoden

Als u telefoongesprekken niet langer wilt gebruiken als beveiligingsinformatiemethode, u deze verwijderen van de pagina **Beveiligingsgegevens.**

>[!Important]
>Als u telefoongesprekken per ongeluk verwijdert, is er geen manier om het ongedaan te maken. U moet de methode opnieuw toevoegen, volgens de stappen in het gedeelte [Telefoongesprekken instellen](#set-up-phone-calls-from-the-security-info-page) van dit artikel.

### <a name="to-delete-phone-calls"></a>Telefoongesprekken verwijderen

1. Selecteer op de pagina **Beveiligingsgegevens** de koppeling **Verwijderen** naast de optie **Telefoon.**

    ![Koppeling om de telefoonmethode uit beveiligingsgegevens te verwijderen](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecteer **Ja** in het bevestigingsvak om het **telefoonnummer te** verwijderen. Nadat uw telefoonnummer is verwijderd, wordt het verwijderd uit uw beveiligingsgegevens en verdwijnt het van de pagina **Beveiligingsgegevens.** Als **Telefoon** uw standaardmethode is, wordt de standaardinstelling gewijzigd in een andere beschikbare methode.

## <a name="change-your-default-security-info-method"></a>Uw standaardbeveiligingsgegevensmethode wijzigen

Als u wilt dat telefoongesprekken de standaardmethode zijn die wordt gebruikt wanneer u zich aanmeldt bij uw werk- of schoolaccount met behulp van tweestapsverificatie of voor verzoeken om het opnieuw instellen van wachtwoorden, u deze instellen op de pagina **Beveiligingsgegevens.**

### <a name="to-change-your-default-security-info-method"></a>Uw standaardbeveiligingsgegevensmethode wijzigen

1. Selecteer op de pagina **Beveiligingsinfo** de koppeling **Wijzigen** naast de **standaardaanmeldingsmethodegegevens.**

    ![Koppeling wijzigen voor standaardaanmeldingsmethode](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecteer **Telefoon -*_your_phone_number_* your_phone_number** bellen in de vervolgkeuzelijst met beschikbare methoden en selecteer **Vervolgens Bevestigen**.

    ![Methode kiezen voor standaardaanmelding](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    De standaardmethode die wordt gebruikt voor aanmeldingswijzigingen in **Telefoon - bellen (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Aanvullende beveiligingsinformatiemethoden

U hebt extra opties voor de manier waarop uw organisatie contact met u opneemt om uw identiteit te verifiëren, op basis van wat u probeert te doen. De opties zijn:

- **Authenticator app.** Download en gebruik een authenticator-app om een goedkeuringsmelding of een willekeurig gegenereerde goedkeuringscode te krijgen voor verificatie in twee stappen of wachtwoordreset. Zie [Beveiligingsgegevens instellen om een authenticator-app te gebruiken voor](security-info-setup-auth-app.md)stapsgewijze instructies over het instellen en gebruiken van de Microsoft Authenticator-app.

- **Sms voor mobiele apparaten.** Voer uw mobiele apparaatnummer in en ontvang een tekst die u gebruikt voor verificatie in twee stappen of wachtwoordopnieuw instellen. Zie [Beveiligingsgegevens instellen om sms'jes te gebruiken](security-info-setup-text-msg.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een sms.

- **Beveiligingssleutel.** Registreer uw microsoft-compatibele beveiligingssleutel en gebruik deze samen met een pincode voor verificatie in twee stappen of wachtwoordreset. Zie [Beveiligingsgegevens instellen om een beveiligingssleutel te gebruiken](security-info-setup-security-key.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een beveiligingssleutel.

- **E-mailadres.** Voer uw e-mailadres op het werk of de school in om een e-mail te ontvangen voor het opnieuw instellen van het wachtwoord. Deze optie is niet beschikbaar voor verificatie in twee stappen. Zie [Beveiligingsgegevens instellen om e-mail te gebruiken](security-info-setup-email.md)voor stapsgewijze instructies over het instellen van uw e-mail.

- **Veiligheidsvragen.** Beantwoord enkele beveiligingsvragen die door uw beheerder voor uw organisatie zijn gemaakt. Deze optie is alleen beschikbaar voor het opnieuw instellen van wachtwoorden en niet voor verificatie in twee stappen. Zie het artikel [Beveiligingsgegevens instellen om beveiligingsvragen te gebruiken voor](security-info-setup-questions.md) stapsgewijze instructies voor het instellen van uw beveiligingsvragen.

    >[!Note]
    >Als sommige van deze opties ontbreken, is dit waarschijnlijk omdat uw organisatie deze methoden niet toestaat. Als dit het geval is, moet u een beschikbare methode kiezen of contact opnemen met uw beheerder voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
