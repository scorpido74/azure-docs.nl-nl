---
title: Beveiligingsvragen instellen als verificatiemethode - Azure AD
description: Uw pagina Beveiligingsgegevens (voorbeeld) instellen om uw identiteit te verifiëren met vooraf gedefinieerde beveiligingsvragen als verificatiemethode.
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
ms.openlocfilehash: 236f1e4e41488b926d9fc2e5e717e68090a0ed7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063965"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Beveiligingsvragen instellen als verificatiemethode

U deze stappen volgen om uw methode voor het opnieuw instellen van wachtwoorden toe te voegen. Nadat u dit de eerste keer hebt ingesteld, u terugkeren naar de pagina **Beveiligingsgegevens** om uw beveiligingsgegevens toe te voegen, bij te werken of te verwijderen.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Als u de optie beveiligingsvragen niet ziet, is het mogelijk dat uw organisatie u niet toestaat deze optie te gebruiken voor uw methode voor het opnieuw instellen van wachtwoorden. In dit geval moet u een andere methode kiezen of contact opnemen met de helpdesk van uw organisatie voor meer hulp.

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

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Uw beveiligingsvragen instellen op de pagina Beveiligingsgegevens

Afhankelijk van de instellingen van uw organisatie u mogelijk een aantal beveiligingsvragen kiezen en beantwoorden als een van uw beveiligingsgegevensmethoden. Uw beheerder stelt het aantal beveiligingsvragen in dat u moet kiezen en beantwoorden.

Als u beveiligingsvragen gebruikt, raden we u aan deze te gebruiken in combinatie met een andere methode. Beveiligingsvragen kunnen minder veilig zijn dan andere methoden, omdat sommige mensen de antwoorden op uw vragen kennen.

> [!Note]
> Beveiligingsvragen worden privé en veilig opgeslagen op een gebruikersobject in de directory en kunnen alleen door u worden beantwoord tijdens de registratie. Uw beheerder kan uw vragen of antwoorden niet lezen of wijzigen.
>
> Als u de optie beveiligingsvragen niet ziet, is het mogelijk dat u met uw organisatie geen beveiligingsvragen gebruiken voor verificatie. Als dit het geval is, moet u een andere methode kiezen of contact opnemen met uw beheerder voor meer hulp.
>
> Beheerdersaccounts mogen beveiligingsvragen niet gebruiken als methode voor het opnieuw instellen van wachtwoorden. Als u bent ingelogd als een account op beheerdersniveau, ziet u deze opties niet.

### <a name="to-set-up-your-security-questions"></a>Uw beveiligingsvragen instellen

1. Meld u aan bij uw werk- https://myprofile.microsoft.com/ of schoolaccount en ga vervolgens naar uw pagina.

    ![Mijn profielpagina met gemarkeerde koppelingen naar beveiligingsgegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **Beveiligingsgegevens** in het linkernavigatiedeelvenster of in de koppeling in het blok **Beveiligingsgegevens** en selecteer **vervolgens Methode toevoegen** op de pagina **Beveiligingsgegevens.**

    ![Pagina Beveiligingsgegevens met gemarkeerde optie Methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer **op** de pagina Een methode toevoegen de optie **Beveiligingsvragen** in de vervolgkeuzelijst en selecteer **Vervolgens Toevoegen**.

    ![Het vakmethode toevoegen, met beveiligingsvragen geselecteerd](media/security-info/securityinfo-myprofile-addquestions.png)

4. Kies en beantwoord uw beveiligingsvragen op de pagina **Beveiligingsvragen** en selecteer **Opslaan**.

    ![Telefoonnummer toevoegen en telefoongesprekken kiezen](media/security-info/securityinfo-myprofile-securityquestions.png)

    Uw beveiligingsgegevens worden bijgewerkt en u uw beveiligingsvragen gebruiken om uw identiteit te verifiëren bij het opnieuw instellen van het wachtwoord.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Beveiligingsvragen verwijderen uit uw beveiligingsgegevensmethoden

Als u uw beveiligingsvragen niet langer als beveiligingsinformatiemethode wilt gebruiken, u deze verwijderen van de pagina **Beveiligingsgegevens.**

>[!Important]
>Als u uw beveiligingsvragen per ongeluk verwijdert, is er geen manier om deze ongedaan te maken. U moet de methode opnieuw toevoegen, volgens de stappen in het gedeelte [Uw beveiligingsvragen instellen](#set-up-your-security-questions-from-the-security-info-page) van dit artikel.

### <a name="to-delete-your-security-questions"></a>Uw beveiligingsvragen verwijderen

1. Selecteer op de pagina **Beveiligingsinfo** de koppeling **Verwijderen** naast de optie **Beveiligingsvragen.**

    ![Koppeling om de telefoonmethode uit beveiligingsgegevens te verwijderen](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selecteer **Ja** in het bevestigingsvak om uw **beveiligingsvragen**te verwijderen. Nadat uw beveiligingsvragen zijn verwijderd, wordt de methode uit uw beveiligingsgegevens verwijderd en verdwijnt deze van de pagina **Beveiligingsgegevens.**

## <a name="additional-security-info-methods"></a>Aanvullende beveiligingsinformatiemethoden

U hebt extra opties voor de manier waarop uw organisatie contact met u opneemt om uw identiteit te verifiëren, op basis van wat u probeert te doen. De opties zijn:

- **Authenticator app.** Download en gebruik een authenticator-app om een goedkeuringsmelding of een willekeurig gegenereerde goedkeuringscode te krijgen voor verificatie in twee stappen of wachtwoordreset. Zie [Beveiligingsgegevens instellen om een authenticator-app te gebruiken voor](security-info-setup-auth-app.md)stapsgewijze instructies over het instellen en gebruiken van de Microsoft Authenticator-app.

- **Sms voor mobiele apparaten.** Voer uw mobiele apparaatnummer in en ontvang een tekst die u gebruikt voor verificatie in twee stappen of wachtwoordopnieuw instellen. Zie [Beveiligingsgegevens instellen om sms'jes te gebruiken](security-info-setup-text-msg.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een sms.

- **Mobiel apparaat of werktelefoongesprek.** Voer uw mobiele apparaatnummer in en ontvang een telefoongesprek voor verificatie in twee stappen of wachtwoordopnieuw instellen. Zie [Beveiligingsgegevens instellen om telefoongesprekken te gebruiken voor](security-info-setup-phone-number.md)stapsgewijze instructies over het verifiëren van uw identiteit met een telefoonnummer.

- **Beveiligingssleutel.** Registreer uw microsoft-compatibele beveiligingssleutel en gebruik deze samen met een pincode voor verificatie in twee stappen of wachtwoordreset. Zie [Beveiligingsgegevens instellen om een beveiligingssleutel te gebruiken](security-info-setup-security-key.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een beveiligingssleutel.

- **E-mailadres.** Voer uw e-mailadres op het werk of de school in om een e-mail te ontvangen voor het opnieuw instellen van het wachtwoord. Deze optie is niet beschikbaar voor verificatie in twee stappen. Zie [Beveiligingsgegevens instellen om e-mail te gebruiken](security-info-setup-email.md)voor stapsgewijze instructies over het instellen van uw e-mail.

    >[!Note]
    >Als sommige van deze opties ontbreken, is dit waarschijnlijk omdat uw organisatie deze methoden niet toestaat. Als dit het geval is, moet u een beschikbare methode kiezen of contact opnemen met uw beheerder voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
