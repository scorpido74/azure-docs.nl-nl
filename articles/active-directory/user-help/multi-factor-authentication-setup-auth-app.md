---
title: Stel een verificator-app in als uw twee ledige verificatie methode-Azure Active Directory | Microsoft Docs
description: Meer informatie over het instellen van de Microsoft Authenticator-app als uw verificatie methode met twee factoren.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 6ab1f7c97173021cc112a5f117469abd74ac954d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77062571"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Een verificator-app instellen als uw verificatie methode met twee factoren

U kunt een verificator-app instellen voor het verzenden van een melding naar uw mobiele apparaat of om u een verificatie code te sturen als uw methode voor beveiligings verificatie. U hoeft de Microsoft Authenticator-app niet te gebruiken en u kunt een andere app selecteren tijdens het instellen van het proces. In dit artikel wordt echter gebruikgemaakt van de app Microsoft Authenticator.

>[!Important]
>Voordat u uw account kunt toevoegen, moet u de app Microsoft Authenticator downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het artikel [app downloaden en installeren](user-help-auth-app-download-install.md) .

>[!Note]
> Als de optie voor de mobiele app grijs wordt weer gegeven, is het mogelijk dat uw organisatie geen verificatie-app mag gebruiken voor verificatie. In dit geval moet u een andere methode selecteren of contact opnemen met de beheerder voor meer hulp.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>De app Microsoft Authenticator instellen voor het verzenden van meldingen

1. Selecteer op de pagina **aanvullende beveiligings verificatie** de optie **mobiele app** in de sectie **stap 1: hoe moet er contact met u worden opgenomen** .

2. Selecteer **meldingen ontvangen voor verificatie** vanuit het gebied voor de **mobiele app** en selecteer vervolgens **instellen**.

    ![Extra beveiligings verificatie pagina, met de optie mobiele app en meldingen](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    De pagina **mobiele app configureren** wordt weer gegeven.

    ![Scherm met de QR-code](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **account toevoegen** uit het pictogram **aanpassen en beheer** in de rechter bovenhoek en selecteer vervolgens **werk-of school account**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of dat de app afbeeldingen en record video (Android) mag maken. U moet **toestaan** inschakelen zodat de verificator-app toegang heeft tot uw camera om een foto van de QR-code in de volgende stap te maken. Als u de camera niet toestaat, kunt u nog steeds de verificator-app instellen, maar u moet de code gegevens hand matig toevoegen. Zie [hand matig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md)voor meer informatie over het hand matig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **mobiele app configureren** op uw computer en kies vervolgens **volgende**.

5. Ga terug naar de computer en de **aanvullende beveiligings verificatie** pagina, zorg ervoor dat u het bericht krijgt dat uw configuratie is geslaagd en selecteer **volgende**.

    ![Extra beveiligings verificatie pagina, met succes bericht](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    De verificator-app stuurt als test een melding naar uw mobiele apparaat.

6. Selecteer **goed keuren**op uw mobiele apparaat.

7. Voeg op uw computer het telefoon nummer van uw mobiele apparaat toe aan **stap 3: als u geen toegang meer hebt tot het gebied voor mobiele apps** en vervolgens **volgende**selecteert.

    We raden u ten zeerste aan om het telefoon nummer van uw mobiele apparaat toe te voegen als back-up als u de mobiele app om welke reden dan ook niet kunt gebruiken.

8. In de **stap 4: Blijf uw bestaande toepassingen gebruiken** , kopieer het opgegeven app-wacht woord en plak het ergens veilig.

    ![Het gedeelte app-wacht woorden van de pagina aanvullende beveiligings verificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [app-wacht woorden beheren](multi-factor-authentication-end-user-app-passwords.md)voor meer informatie over het gebruik van het app-wacht woord met uw oudere apps. U hoeft alleen app-wacht woorden te gebruiken als u oudere apps gaat gebruiken die geen ondersteuning bieden voor twee ledige verificatie.

9. Selecteer **Done**.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Stel de Microsoft Authenticator-app in voor het gebruik van verificatie codes

1. Selecteer op de pagina **aanvullende beveiligings verificatie** de optie **mobiele app** in de sectie **stap 1: hoe moet er contact met u worden opgenomen** .

2. Selecteer **verificatie code gebruiken** in het gedeelte **Hoe wilt u het gebied voor mobiele apps gebruiken** en selecteer vervolgens **instellen**.

    ![Extra beveiligings verificatie pagina, met de optie mobiele app en meldingen](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    De pagina **mobiele app configureren** wordt weer gegeven.

    ![Scherm met de QR-code](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **account toevoegen** uit het pictogram **aanpassen en beheer** in de rechter bovenhoek en selecteer vervolgens **werk-of school account**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of dat de app afbeeldingen en record video (Android) mag maken. U moet **toestaan** inschakelen zodat de verificator-app toegang heeft tot uw camera om een foto van de QR-code in de volgende stap te maken. Als u de camera niet toestaat, kunt u nog steeds de verificator-app instellen, maar u moet de code gegevens hand matig toevoegen. Zie [hand matig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md)voor meer informatie over het hand matig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **mobiele app configureren** op uw computer en kies vervolgens **volgende**.

5. Ga terug naar de computer en de **aanvullende beveiligings verificatie** pagina, zorg ervoor dat u het bericht krijgt dat uw configuratie is geslaagd en selecteer **volgende**.

    ![Extra beveiligings verificatie pagina, met succes bericht](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    De verificator-app vraagt om een verificatie code als test.

6. Ga vanuit de Microsoft Authenticator-app omlaag naar uw werk-of school account, kopieer en plak de 6-cijferige code van de app in de **stap 2: Voer de verificatie code uit het vak mobiele app** op uw computer in en selecteer vervolgens **controleren**.

    ![Aanvullende beveiligings verificatie pagina, met verificatie code testen](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Voeg op uw computer het telefoon nummer van uw mobiele apparaat toe aan **stap 3: als u geen toegang meer hebt tot het gebied voor mobiele apps** en vervolgens **volgende**selecteert.

    We raden u ten zeerste aan om het telefoon nummer van uw mobiele apparaat toe te voegen als back-up als u de mobiele app om welke reden dan ook niet kunt gebruiken.

8. In de **stap 4: Blijf uw bestaande toepassingen gebruiken** , kopieer het opgegeven app-wacht woord en plak het ergens veilig.

    ![Het gedeelte app-wacht woorden van de pagina aanvullende beveiligings verificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [app-wacht woorden beheren](multi-factor-authentication-end-user-app-passwords.md)voor meer informatie over het gebruik van het app-wacht woord met uw oudere apps. U hoeft alleen app-wacht woorden te gebruiken als u oudere apps gaat gebruiken die geen ondersteuning bieden voor twee ledige verificatie.

9. Selecteer **Done**.

## <a name="next-steps"></a>Volgende stappen

Nadat u de twee ledige verificatie methode hebt ingesteld, kunt u extra methoden toevoegen, uw instellingen en app-wacht woorden beheren, aanmelden of hulp krijgen bij enkele veelvoorkomende problemen met twee ledige verificatie.

- [De instellingen voor de verificatie methode met twee factoren beheren](multi-factor-authentication-end-user-manage-settings.md)

- [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)

- [Aanmelden met twee ledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij twee ledige verificatie](multi-factor-authentication-end-user-troubleshoot.md)
