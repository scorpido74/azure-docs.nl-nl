---
title: Een e-mailadres instellen als verificatiemethode - Azure AD
description: De pagina Beveiligingsgegevens (voorbeeld) instellen om uw identiteit te verifiëren met behulp van een e-mailadres als verificatiemethode.
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
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063999"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Een e-mailadres instellen als verificatiemethode

U deze stappen volgen om uw methode voor het opnieuw instellen van wachtwoorden toe te voegen. Nadat u dit de eerste keer hebt ingesteld, u terugkeren naar de pagina **Beveiligingsgegevens** om uw beveiligingsgegevens toe te voegen, bij te werken of te verwijderen.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Als u geen e-mailoptie ziet, is het mogelijk dat uw organisatie u niet toestaat deze optie te gebruiken voor uw methode voor het opnieuw instellen van wachtwoorden. In dit geval moet u een andere methode kiezen of contact opnemen met de helpdesk van uw organisatie voor meer hulp.

## <a name="security-vs-password-reset-verification"></a>Verificatie beveiliging versus wachtwoordreset

Beveiligingsinformatiemethoden worden gebruikt voor zowel tweestapsbeveiligingsverificatie als voor het opnieuw instellen van wachtwoorden. Niet elke methode kan echter voor beide worden gebruikt.

| Methode | Gebruikt voor |
| ------ | -------- |
| Authenticator-app | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Sms-berichten | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Telefoonoproep | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Beveiligingssleutel | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| E-mailaccount | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |
| Beveiligingsvragen | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Uw e-mailadres instellen op de pagina Beveiligingsgegevens

Afhankelijk van de instellingen van uw organisatie u uw e-mailadres mogelijk gebruiken als een van uw beveiligingsgegevensmethoden.

>[!Note]
>We raden u aan een e-mailadres te gebruiken waarvoor uw netwerkwachtwoord niet toegankelijk is. Als u de e-mailoptie niet ziet, is het mogelijk dat uw organisatie u niet toestaat een e-mail te gebruiken voor verificatie. Als dit het geval is, moet u een andere methode kiezen of contact opnemen met uw beheerder voor meer hulp.

### <a name="to-set-up-your-email-address"></a>Uw e-mailadres instellen

1. Meld u aan bij uw werk- https://myprofile.microsoft.com/ of schoolaccount en ga vervolgens naar uw pagina.

    ![Mijn profielpagina met gemarkeerde koppelingen naar beveiligingsgegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **Beveiligingsgegevens** in het linkernavigatiedeelvenster of in de koppeling in het blok **Beveiligingsgegevens** en selecteer **vervolgens Methode toevoegen** op de pagina **Beveiligingsgegevens.**

    ![Pagina Beveiligingsgegevens met gemarkeerde optie Methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer **e-mail** toevoegen op de pagina **Een methode** toevoegen in de vervolgkeuzelijst en selecteer **Vervolgens Toevoegen**.

    ![Het vakmethode toevoegen, met e-mail geselecteerd](media/security-info/securityinfo-myprofile-addemail.png)

4. Typ op de pagina **E-mail** uw alain@gmail.come-mailadres (bijvoorbeeld) en selecteer **Volgende**.

    ![Telefoonnummer toevoegen en telefoongesprekken kiezen](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Dit e-mailadres kan niet uw werk of school e-mail.

5. Typ de code die naar het opgegeven e-mailadres wordt verzonden en selecteer **Volgende**.

    ![Telefoonnummer toevoegen en tekstberichten kiezen](media/security-info/securityinfo-myprofile-emailcode.png)

    Uw beveiligingsgegevens worden bijgewerkt en u uw e-mailadres gebruiken om uw identiteit te verifiëren wanneer u het opnieuw instellen van het wachtwoord gebruikt.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Uw e-mailadres verwijderen uit uw beveiligingsgegevensmethoden

Als u uw e-mailadres niet langer als beveiligingsinformatiemethode wilt gebruiken, u het verwijderen van de pagina **Beveiligingsgegevens.**

>[!Important]
>Als u uw e-mailadres per ongeluk verwijdert, u het niet ongedaan maken. U moet de methode opnieuw toevoegen, volgens de stappen in het gedeelte [Uw e-mailadres](#set-up-your-email-address-from-the-security-info-page) instellen van dit artikel.

### <a name="to-delete-your-email-address"></a>Uw e-mailadres verwijderen

1. Selecteer op de pagina **Beveiligingsgegevens** de koppeling **Verwijderen** naast de optie **E-mail.**

    ![Koppeling om de telefoonmethode uit beveiligingsgegevens te verwijderen](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Selecteer **Ja** in het bevestigingsvak om het **e-mailaccount** te verwijderen. Nadat het e-mailaccount is verwijderd, wordt het uit uw beveiligingsgegevens verwijderd en verdwijnt het van de pagina **Beveiligingsgegevens.**

## <a name="additional-security-info-methods"></a>Aanvullende beveiligingsinformatiemethoden

U hebt extra opties voor de manier waarop uw organisatie contact met u opneemt om uw identiteit te verifiëren, op basis van wat u probeert te doen. De opties zijn:

- **Authenticator app.** Download en gebruik een authenticator-app om een goedkeuringsmelding of een willekeurig gegenereerde goedkeuringscode te krijgen voor verificatie in twee stappen of wachtwoordreset. Zie [Beveiligingsgegevens instellen om een authenticator-app te gebruiken voor](security-info-setup-auth-app.md)stapsgewijze instructies over het instellen en gebruiken van de Microsoft Authenticator-app.

- **Sms voor mobiele apparaten.** Voer uw mobiele apparaatnummer in en ontvang een tekst die u gebruikt voor verificatie in twee stappen of wachtwoordopnieuw instellen. Zie [Beveiligingsgegevens instellen om sms'jes te gebruiken](security-info-setup-text-msg.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een sms.

- **Mobiel apparaat of werktelefoongesprek.** Voer uw mobiele apparaatnummer in en ontvang een telefoongesprek voor verificatie in twee stappen of wachtwoordopnieuw instellen. Zie [Beveiligingsgegevens instellen om telefoongesprekken te gebruiken voor](security-info-setup-phone-number.md)stapsgewijze instructies over het verifiëren van uw identiteit met een telefoonnummer.

- **Beveiligingssleutel.** Registreer uw microsoft-compatibele beveiligingssleutel en gebruik deze samen met een pincode voor verificatie in twee stappen of wachtwoordreset. Zie [Beveiligingsgegevens instellen om een beveiligingssleutel te gebruiken](security-info-setup-security-key.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een beveiligingssleutel.

- **Veiligheidsvragen.** Beantwoord enkele beveiligingsvragen die door uw beheerder voor uw organisatie zijn gemaakt. Deze optie is alleen beschikbaar voor het opnieuw instellen van wachtwoorden en niet voor verificatie in twee stappen. Zie het artikel [Beveiligingsgegevens instellen om beveiligingsvragen te gebruiken voor](security-info-setup-questions.md) stapsgewijze instructies voor het instellen van uw beveiligingsvragen.

    >[!Note]
    >Als sommige van deze opties ontbreken, is dit waarschijnlijk omdat uw organisatie deze methoden niet toestaat. Als dit het geval is, moet u een beschikbare methode kiezen of contact opnemen met uw beheerder voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
