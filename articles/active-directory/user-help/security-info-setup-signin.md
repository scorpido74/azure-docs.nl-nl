---
title: Beveiligings gegevens (preview-versie) instellen bij een aanmeldings prompt-Azure AD
description: Informatie over het instellen van beveiligingsgegevens voor uw werk- of schoolaccount, als hierom wordt gevraagd door de aanmeldingspagina van uw organisatie.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 08/05/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ae1856c7934c658bd244445306b4bb78c666e38
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820289"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Uw beveiligingsgegevens (preview) instellen vanaf de opdrachtprompt voor de aanmeldingspagina

U kunt deze stappen volgen als u wordt gevraagd uw beveiligingsgegevens in te stellen, onmiddellijk nadat u zich aanmeldt bij uw werk- of schoolaccount.

U ziet deze prompt alleen als u de beveiligingsgegevens niet hebt ingesteld die zijn vereist door uw organisatie. Als u eerder de beveiligingsgegevens hebt ingesteld, maar u wijzigingen wilt aanbrengen, kunt u de stappen volgen in de verschillende artikelen met uitleg per methode. Zie [Uw overzicht met beveiligingsinformatie toevoegen of bijwerken](security-info-add-update-methods-overview.md) voor meer informatie.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Meld u aan bij uw werk- of schoolaccount

Nadat u zich bij uw account voor werk of school aanmeldt, ziet u een prompt waarin u meer informatie moet opgeven voordat u toegang tot uw account wordt verleend.

![Vraag om meer informatie](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Instellen van uw beveiligingsgegevens met de wizard

Volg deze stappen voor het instellen van uw beveiligingsgegevens voor uw werk- of schoolaccount vanaf de opdrachtprompt.

>[!Important]
>Dit is slechts een voorbeeld van het proces. Afhankelijk van de vereisten van uw organisatie, kan de beheerder andere verificatiemethoden hebben ingesteld die u moet gebruiken tijdens dit proces. In dit voorbeeld hebben we twee methoden verplicht gesteld, de Microsoft Authenticator-app en een mobiel telefoonnummer voor verificatie oproepen of sms-berichten.

1. Nadat u **Volgende** hebt geselecteerd in de opdrachtprompt, wordt de wizard **Houd uw account veilig** weergegeven, de eerste methode die u van uw beheerder en de organisatie moet instellen. Voor dit voorbeeld is dit de Microsoft Authenticator-app.

   > [!Note]
   > Als u een andere verificator-app dan de Microsoft Authenticator-app gebruiken wilt, selecteert u de koppeling **Ik wil een andere verificator-app gebruiken**.
   >
   > Als u van uw organisatie u een andere methode dan de authenticator-app kiezen mag, kunt u de **Ik wil andere methode instellen-koppeling** selecteren.

    ![Wizard Houd uw account veilig met de downloadpagina van de verificator](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecteer **Nu downloaden** om de Microsoft Authenticator-app op uw mobiele apparaat te downloaden en installeren en selecteer vervolgens **Volgende**. Zie voor meer informatie over het downloaden en installeren van de app, [De Microsoft Authenticator-app downloaden en installeren](user-help-auth-app-download-install.md).

    ![Wizard Houd uw account veilig met de pagina Uw account instellen van de verificator](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Blijven op de **Uw account instellen**-pagina tijdens het instellen van de Microsoft Authenticator-app op uw mobiele apparaat.

4. Open de Microsoft Authenticator-app, selecteer dat u meldingen toestaat (als u hierom wordt gevraagd), selecteer **Account toevoegen** uit het pictogram **Aanpassen en controle** in de rechterbovenhoek en selecteer vervolgens **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of dat de app afbeeldingen en record video (Android) mag maken. U moet **toestaan** inschakelen zodat de verificator-app toegang heeft tot uw camera om een foto van de QR-code in de volgende stap te maken. Als u de camera niet toestaat, kunt u nog steeds de verificator-app instellen, maar u moet de code gegevens hand matig toevoegen. Zie [hand matig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md)voor meer informatie over het hand matig toevoegen van de code.

5. Ga terug naar de pagina **Uw account instellen** op uw computer en selecteer vervolgens **Volgende**.

    De pagina **QR-code scannen** wordt weergegeven.

    ![De QR-code scannen met behulp van de Authenticator-app](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Scan de weergegeven code met de QR-codelezer van de Microsoft Authenticator-app die op uw mobiele apparaat wordt weergegeven nadat u uw werk of school-account hebt gemaakt in stap 5.

    De verificator-app moet nu uw werk of schoolaccount toevoegen zonder aanvullende informatie van u. Als de QR-code lezer de code echter niet kan lezen, kunt u de **QR-afbeelding niet scannen** en de code en URL hand matig invoeren in de app Microsoft Authenticator. Zie voor meer informatie over het handmatig toevoegen van een code [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md).

7. Selecteer **Volgende** op de pagina **QR-code scannen** op uw computer.

    Een melding wordt verzonden naar de Microsoft Authenticator-app op uw mobiele apparaat voor het testen van uw account.

    ![Uw account met de verificator-app testen](media/security-info/securityinfo-prompt-test-app.png)

8. Keur de melding in de Microsoft Authenticator-app goed en selecteer vervolgens **Volgende**.

    ![Melding van geslaagd, met verbinding van de app en uw account](media/security-info/securityinfo-prompt-auth-app-success.png)

    Uw beveiligingsgegevens zijn bijgewerkt voor het standaard gebruik van de Microsoft Authenticator-app om uw identiteit te verifiëren bij het gebruik van verificatie in twee stappen of opnieuw instellen van het wachtwoord.

9. Op de pagina **Telefoon** instellen, kiest u of u een sms-bericht of een telefonische oproep wilt ontvangen, en selecteert vervolgens **Volgende**. Voor de doeleinden van dit voorbeeld gebruiken we sms-berichten, zodat u een telefoonnummer moet gebruiken voor een apparaat dat sms-berichten kan accepteren.

    ![Begin met het instellen van uw telefoonnummer voor de sms-berichten verzenden](media/security-info/securityinfo-prompt-text-msg.png)

    Een sms-bericht is verzonden naar uw telefoonnummer. Als liever een telefoongesprek krijgt, is het proces hetzelfde. U zult echter een telefonische oproep met instructies in plaats van een sms-bericht ontvangen.

10. Voer de code die is geleverd door het sms-bericht aan uw mobiele apparaat en selecteer vervolgens **Volgende**.

    ![Test uw account met het sms-bericht](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Bekijk de Geslaagd-melding en selecteer vervolgens **Gereed**.

    ![Geslaagd-melding](media/security-info/securityinfo-prompt-call-answered-success.png)

    Uw beveiligingsgegevens zijn bijgewerkt voor het gebruik van een sms-bericht als back-up om uw identiteit te verifiëren bij het gebruik van verificatie in twee stappen of opnieuw instellen van het wachtwoord.

12. Bekijk de pagina **Succes** om te controleren dat u zowel de Microsoft Authenticator-app als een telefoonmethode (sms-bericht of telefonische oproep) heeft ingesteld voor uw beveiligingsgegevens en selecteer vervolgens **Gereed**.

    ![Pagina Wizard is voltooid](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Als uw organisatie vereist dat u app-wacht woorden gebruikt, ziet u mogelijk een extra sectie in deze wizard, waar u deze kunt instellen. Als er een derde sectie met de naam **app-wacht woorden**wordt weer geven, moet u deze invullen voordat u de wizard kunt volt ooien. Zie de sectie [uw app-wacht woorden beheren](#manage-your-app-passwords) in dit artikel voor instructies over het toevoegen van een app-wacht woord.

### <a name="manage-your-app-passwords"></a>Uw app-wacht woorden beheren

Bepaalde apps, zoals Outlook 2010, bieden geen ondersteuning voor verificatie in twee stappen. Dit gebrek aan ondersteuning betekent dat als u verificatie in twee stappen in uw organisatie gebruikt, de app niet werkt. Om dit probleem op te lossen, kunt u een automatisch gegenereerd wacht woord maken voor gebruik met elke niet-browser-app, gescheiden van uw normale wacht woord.

>[!Note]
>Als deze optie niet wordt weer geven in de wizard, betekent dit dat uw beheerder deze niet heeft ingesteld. Als dit niet is ingesteld, maar u weet dat u app-wacht woorden moet gebruiken, kunt u de stappen volgen in de [pagina app-wacht woorden instellen op het tabblad beveiligings gegevens (preview)](security-info-app-passwords.md).

Wanneer u app-wacht woorden gebruikt, is het belang rijk om het volgende te onthouden:

- App-wacht woorden worden automatisch gegenereerd en eenmaal per app ingevoerd.

- Er is een limiet van 40 wacht woorden per gebruiker. Als u later een van deze limieten probeert te maken, wordt u gevraagd een bestaand wacht woord te verwijderen voordat u de nieuwe kunt maken.

- Gebruik één app-wacht woord per apparaat, niet per app. U kunt bijvoorbeeld één wacht woord maken voor alle apps op uw laptop en vervolgens een ander wacht woord voor alle apps op uw bureau blad.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>App-wacht woorden toevoegen in de wizard Aanmelden

1. Nadat u de vorige secties van de wizard hebt voltooid, selecteert u **volgende** en voltooit u de sectie **app-wacht woord** .

2. Typ de naam van de app waarvoor het wacht woord is vereist, bijvoorbeeld `Outlook 2010`, en selecteer vervolgens **volgende**.

    ![De naam van het app-wacht woord toevoegen in de wizard](media/security-info/app-password-app-password.png)

3. Kopieer de wachtwoord code uit het scherm voor het **app-wacht woord** en plak deze in het **wachtwoord** gebied van de app (in dit voor beeld Outlook 2010).

    ![De pagina app-wacht woord, met een wacht woord voor kopiëren](media/security-info/app-password-copy-password.png)

4. Nadat u het wacht woord hebt gekopieerd en in de app hebt geplakt, keert u terug naar deze wizard om ervoor te zorgen dat alle gegevens van uw aanmeldings methode nauw keurig zijn en selecteert u **gereed**.

    ![Pagina app-wacht woord met voltooiings kennisgeving](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Volgende stappen

- Als u standaard-info beveiligingsmethoden wilt wijzigen, wissen of bijwerken, zie:

    - [Beveiligingsgegevens instellen voor een Authenticator-app](security-info-setup-auth-app.md).

    - [Beveiligingsgegevens instellen voor sms-berichten](security-info-setup-text-msg.md).

    - [Beveiligingsgegevens instellen om telefonische boodschappen te gebruiken](security-info-setup-phone-number.md).

    - [Beveiligingsgegevens instellen om e-mail te gebruiken](security-info-setup-email.md).

    - [Beveiligingsgegevens instellen om vooraf gedefinieerde beveiligingsvragen te gebruiken](security-info-setup-questions.md).

- Zie voor meer informatie over hoe u zich aanmelden met de opgegeven methode [Hoe u zich aanmeldt](user-help-sign-in.md).

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
