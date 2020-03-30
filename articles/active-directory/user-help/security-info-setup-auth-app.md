---
title: De Microsoft Authenticator-app instellen als verificatiemethode - Azure AD
description: Uw pagina Beveiligingsgegevens (voorbeeld) instellen om uw identiteit te verifiëren met de Microsoft Authenticator-app als verificatiemethode.
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
ms.openlocfilehash: 16cb512d3f8f8afcc199cb52c13e09b12107576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062418"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>De Microsoft Authenticator-app instellen als verificatiemethode

U deze stappen volgen om uw methoden voor verificatie en wachtwoord opnieuw instellen toe te voegen. Nadat u dit de eerste keer hebt ingesteld, u terugkeren naar de pagina **Beveiligingsgegevens** om uw beveiligingsgegevens toe te voegen, bij te werken of te verwijderen.

Als u wordt gevraagd dit onmiddellijk in te stellen nadat u zich hebt aangemeld bij uw werk- of schoolaccount, raadpleegt u de gedetailleerde stappen in het artikel Uw beveiligingsgegevens instellen in het artikel [met de aanmeldingspaginaprompt.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Als u de optie authenticator-app niet ziet, is het mogelijk dat uw organisatie u niet toestaat deze optie te gebruiken voor verificatie. In dit geval moet u een andere methode kiezen of contact opnemen met de helpdesk van uw organisatie voor meer hulp.

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

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>De Microsoft Authenticator-app instellen op de pagina Beveiligingsgegevens

Afhankelijk van de instellingen van uw organisatie u mogelijk een verificatie-app gebruiken als een van uw beveiligingsgegevensmethoden. U hoeft de Microsoft Authenticator-app niet te gebruiken en u tijdens het instellen een andere app kiezen. In dit artikel wordt echter de Microsoft Authenticator-app gebruikt.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>De Microsoft Authenticator-app instellen

1. Meld u aan bij uw werk- https://myprofile.microsoft.com/ of schoolaccount en ga vervolgens naar uw pagina.

    ![Mijn profielpagina met gemarkeerde koppelingen naar beveiligingsgegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **Beveiligingsgegevens** in het linkernavigatiedeelvenster of in de koppeling in het blok **Beveiligingsgegevens** en selecteer **vervolgens Methode toevoegen** op de pagina **Beveiligingsgegevens.**

    ![Pagina Beveiligingsgegevens met gemarkeerde optie Methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer op de pagina **Een methode toevoegen** de optie **Authenticator-app** in de vervolgkeuzelijst en selecteer **Vervolgens Toevoegen**.

    ![Methodevak toevoegen, met authenticator-app geselecteerd](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Selecteer op de **pagina Start door de app te downloaden** de optie Nu **downloaden** om de Microsoft Authenticator-app op uw mobiele apparaat te downloaden en te installeren en selecteer **Volgende**.

    Zie voor meer informatie over het downloaden en installeren van de app, [De Microsoft Authenticator-app downloaden en installeren](user-help-auth-app-download-install.md).

    ![Begin met het downloaden van de app-pagina](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Als u een andere verificator-app dan de Microsoft Authenticator-app gebruiken wilt, selecteert u de koppeling **Ik wil een andere verificator-app gebruiken**.
   >
   > Als u van uw organisatie u een andere methode dan de authenticator-app kiezen mag, kunt u de **Ik wil andere methode instellen-koppeling** selecteren.

5. Blijven op de **Uw account instellen**-pagina tijdens het instellen van de Microsoft Authenticator-app op uw mobiele apparaat.

    ![De pagina authenticator-app instellen](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Open de Microsoft Authenticator-app, selecteer dat u meldingen toestaat (als u hierom wordt gevraagd), selecteer **Account toevoegen** uit het pictogram **Aanpassen en controle** in de rechterbovenhoek en selecteer vervolgens **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, ontvangt u mogelijk een prompt met de vraag of u de app toegang wilt geven tot uw camera (iOS) of dat de app foto's kan maken en video (Android) opnemen. U moet **Toestaan** selecteren, zodat de authenticator-app toegang heeft tot uw camera om in de volgende stap een foto van de QR-code te maken. Als u de camera niet toestaat, u de authenticator-app nog steeds instellen, maar u moet de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app voor](user-help-auth-app-add-account-manual.md)informatie over het handmatig toevoegen van de code.

7. Ga terug naar de pagina **Uw account instellen** op uw computer en selecteer vervolgens **Volgende**.

    De pagina **QR-code scannen** wordt weergegeven.

    ![De QR-code scannen met behulp van de Authenticator-app](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Scan de meegeleverde code met de QR-codelezer van de Microsoft Authenticator-app, die op uw mobiele apparaat verscheen nadat u uw werk- of schoolaccount in stap 6 hebt gemaakt.

    De verificator-app moet nu uw werk of schoolaccount toevoegen zonder aanvullende informatie van u. Als de QR-codelezer de code echter niet lezen kan, kunt u de **Koppeling kan QR-code niet scannen** selecteren en handmatig de code en URL in de Microsoft Authenticator-app invoeren. Zie voor meer informatie over het handmatig toevoegen van een code [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md).

9. Selecteer **Volgende** op de pagina **QR-code scannen** op uw computer.

    Een melding wordt verzonden naar de Microsoft Authenticator-app op uw mobiele apparaat voor het testen van uw account.

    ![Uw account met de verificator-app testen](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Keur de melding in de Microsoft Authenticator-app goed en selecteer vervolgens **Volgende**.

     ![Melding van geslaagd, met verbinding van de app en uw account](media/security-info/securityinfo-myprofile-successauthapp.png)

     Uw beveiligingsgegevens zijn bijgewerkt voor het standaard gebruik van de Microsoft Authenticator-app om uw identiteit te verifiëren bij het gebruik van verificatie in twee stappen of opnieuw instellen van het wachtwoord.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Uw authenticator-app verwijderen uit uw beveiligingsgegevensmethoden

Als u uw authenticator-app niet langer als beveiligingsinformatiemethode wilt gebruiken, u deze verwijderen van de pagina **Beveiligingsgegevens.** Dit werkt voor alle authenticator-apps, niet alleen voor de Microsoft Authenticator-app. Nadat je de app hebt verwijderd, moet je naar de authenticator-app op je mobiele apparaat gaan en het account verwijderen.

>[!Important]
>Als u de authenticator-app per ongeluk verwijdert, is er geen manier om deze ongedaan te maken. U moet de authenticator-app opnieuw toevoegen, nadat u de stappen in het gedeelte [Authenticator-app](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) van dit artikel instelt.

### <a name="to-delete-the-authenticator-app"></a>De authenticator-app verwijderen

1. Selecteer op de pagina **Beveiligingsinfo** de koppeling **Verwijderen** naast de authenticator-app.

    ![Koppeling om de authenticator-app uit beveiligingsgegevens te verwijderen](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecteer **Ja** in het bevestigingsvak om de authenticator-app te verwijderen. Nadat de authenticator-app is verwijderd, wordt deze uit uw beveiligingsgegevens verwijderd en verdwijnt deze van de pagina **Beveiligingsgegevens.** Als de authenticator-app de standaardmethode is, wordt de standaardmethode gewijzigd in een andere beschikbare methode.

3. Open de authenticator-app op uw mobiele apparaat, selecteer **Accounts bewerken**en verwijder vervolgens uw werk- of schoolaccount uit de authenticator-app.

    Uw account wordt volledig verwijderd uit de authenticator-app voor aanvragen voor tweestapsverificatie en het opnieuw instellen van wachtwoorden.

## <a name="change-your-default-security-info-method"></a>Uw standaardbeveiligingsgegevensmethode wijzigen

Als u wilt dat de authenticator-app de standaardmethode is die wordt gebruikt wanneer u zich aanmeldt bij uw werk- of schoolaccount met behulp van tweestapsverificatie of voor verzoeken om het opnieuw instellen van wachtwoorden, u deze instellen vanaf de pagina **Beveiligingsgegevens.**

### <a name="to-change-your-default-security-info-method"></a>Uw standaardbeveiligingsgegevensmethode wijzigen

1. Selecteer op de pagina **Beveiligingsinfo** de koppeling **Wijzigen** naast de **standaardaanmeldingsmethodegegevens.**

    ![Koppeling wijzigen voor standaardaanmeldingsmethode](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Kies **Microsoft Authenticator - melding** in de vervolgkeuzelijst met beschikbare methoden. Als u de Microsoft Authenticator-app niet gebruikt, selecteert u de optie **Authenticator-app of hardwaretoken.**

    ![Methode kiezen voor standaardaanmelding](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selecteer **Bevestigen**.

    De standaardmethode die wordt gebruikt voor aanmeldingswijzigingen in de Microsoft Authenticator-app.

## <a name="additional-security-info-methods"></a>Aanvullende beveiligingsinformatiemethoden

U hebt extra opties voor de manier waarop uw organisatie contact met u opneemt om uw identiteit te verifiëren, op basis van wat u probeert te doen. De opties zijn:

- **Sms voor mobiele apparaten.** Voer uw mobiele apparaatnummer in en ontvang een tekst die u gebruikt voor verificatie in twee stappen of wachtwoordopnieuw instellen. Zie [Beveiligingsgegevens instellen om sms'jes te gebruiken](security-info-setup-text-msg.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een sms.

- **Mobiel apparaat of werktelefoongesprek.** Voer uw mobiele apparaatnummer in en ontvang een telefoongesprek voor verificatie in twee stappen of wachtwoordopnieuw instellen. Zie [Beveiligingsgegevens instellen om telefoongesprekken te gebruiken voor](security-info-setup-phone-number.md)stapsgewijze instructies over het verifiëren van uw identiteit met een telefoonnummer.

- **Beveiligingssleutel.** Registreer uw microsoft-compatibele beveiligingssleutel en gebruik deze samen met een pincode voor verificatie in twee stappen of wachtwoordreset. Zie [Beveiligingsgegevens instellen om een beveiligingssleutel te gebruiken](security-info-setup-security-key.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een beveiligingssleutel.

- **E-mailadres.** Voer uw e-mailadres op het werk of de school in om een e-mail te ontvangen voor het opnieuw instellen van het wachtwoord. Deze optie is niet beschikbaar voor verificatie in twee stappen. Zie [Beveiligingsgegevens instellen om e-mail te gebruiken](security-info-setup-email.md)voor stapsgewijze instructies over het instellen van uw e-mail.

- **Veiligheidsvragen.** Beantwoord enkele beveiligingsvragen die door uw beheerder voor uw organisatie zijn gemaakt. Deze optie is alleen beschikbaar voor het opnieuw instellen van wachtwoorden en niet voor verificatie in twee stappen. Zie het artikel [Beveiligingsgegevens instellen om beveiligingsvragen te gebruiken voor](security-info-setup-questions.md) stapsgewijze instructies voor het instellen van uw beveiligingsvragen.

    >[!Note]
    >Als sommige van deze opties ontbreken, is dit waarschijnlijk omdat uw organisatie deze methoden niet toestaat. Als dit het geval is, moet u een beschikbare methode kiezen of contact opnemen met uw beheerder voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

- Meld u aan met de Microsoft Authenticator-app en volgt stappen in het [aanmelden met verificatie of beveiligingsinformatie](security-info-setup-signin.md) in twee stappen.

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
