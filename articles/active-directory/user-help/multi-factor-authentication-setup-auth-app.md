---
title: Een authenticator-app instellen als verificatiemethode voor twee stappen - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het instellen van de Microsoft Authenticator-app als verificatiemethode voor twee stappen.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062571"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Een authenticator-app instellen als verificatiemethode voor twee stappen

U een authenticator-app instellen om een melding naar uw mobiele apparaat te sturen of om u een verificatiecode als beveiligingsverificatiemethode te sturen. U hoeft de Microsoft Authenticator-app niet te gebruiken en u tijdens het instellen een andere app selecteren. In dit artikel wordt echter de Microsoft Authenticator-app gebruikt.

>[!Important]
>Voordat u uw account toevoegen, moet u de Microsoft Authenticator-app downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het [artikel Downloaden en installeren.](user-help-auth-app-download-install.md)

>[!Note]
> Als de optie Mobiele app grijs is, is het mogelijk dat u met uw organisatie geen verificatie-app gebruiken voor verificatie. In dit geval moet u een andere methode selecteren of contact opnemen met uw beheerder voor meer hulp.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>De Microsoft Authenticator-app instellen om meldingen te verzenden

1. Selecteer op de pagina **Extra beveiligingsverificatie** de optie **Mobiele app** in stap **1: Hoe moeten we contact met u** opnemen.

2. Selecteer **Meldingen ontvangen voor verificatie** in het gebied Hoe wilt u het gebied voor mobiele apps **gebruiken** en selecteer Vervolgens **Instellen**.

    ![Extra pagina voor beveiligingsverificatie, met de optie mobiele app en meldingen](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    De pagina **Mobiele app configureren** wordt weergegeven.

    ![Scherm met de QR-code](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en besturingselement** rechtsboven en selecteer **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, ontvangt u mogelijk een prompt met de vraag of u de app toegang wilt geven tot uw camera (iOS) of dat de app foto's kan maken en video (Android) opnemen. U moet **Toestaan** selecteren, zodat de authenticator-app toegang heeft tot uw camera om in de volgende stap een foto van de QR-code te maken. Als u de camera niet toestaat, u de authenticator-app nog steeds instellen, maar u moet de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app voor](user-help-auth-app-add-account-manual.md)informatie over het handmatig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **Mobiele app configureren** op uw computer en kies **Volgende**.

5. Ga terug naar uw computer en de pagina **Extra beveiligingsverificatie,** zorg ervoor dat u het bericht ontvangt waarin staat dat uw configuratie is geslaagd en selecteer **Volgende**.

    ![Extra pagina voor beveiligingsverificatie, met succesbericht](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    De authenticator-app stuurt als test een melding naar je mobiele apparaat.

6. Selecteer **Goedkeuren op**uw mobiele apparaat .

7. Op uw computer voegt u uw telefoonnummer voor mobiele apparaten toe aan stap **3: Als u de toegang tot het mobiele app-gebied verliest** en vervolgens **Volgende**selecteert.

    We raden u ten zeerste aan om uw telefoonnummer voor mobiele apparaten toe te voegen als back-up als u om welke reden dan ook geen toegang hebt tot de mobiele app of deze niet gebruiken.

8. Vanuit stap **4: Blijf uw bestaande toepassingen** gebied gebruiken, kopieer het opgegeven app-wachtwoord en plak het ergens veilig.

    ![Het gedeelte Met app-wachtwoorden van de pagina Extra beveiligingsverificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)voor informatie over het gebruik van het app-wachtwoord met uw oudere apps. U hoeft alleen app-wachtwoorden te gebruiken als u oudere apps blijft gebruiken die geen tweestapsverificatie ondersteunen.

9. Selecteer **Done**.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>De Microsoft Authenticator-app instellen om verificatiecodes te gebruiken

1. Selecteer op de pagina **Extra beveiligingsverificatie** de optie **Mobiele app** in stap **1: Hoe moeten we contact met u** opnemen.

2. Selecteer **Verificatiecode gebruiken** in **het gebied Hoe wilt u het gebied voor mobiele apps gebruiken** en selecteer **Vervolgens Instellen**.

    ![Extra pagina voor beveiligingsverificatie, met de optie mobiele app en meldingen](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    De pagina **Mobiele app configureren** wordt weergegeven.

    ![Scherm met de QR-code](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en besturingselement** rechtsboven en selecteer **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, ontvangt u mogelijk een prompt met de vraag of u de app toegang wilt geven tot uw camera (iOS) of dat de app foto's kan maken en video (Android) opnemen. U moet **Toestaan** selecteren, zodat de authenticator-app toegang heeft tot uw camera om in de volgende stap een foto van de QR-code te maken. Als u de camera niet toestaat, u de authenticator-app nog steeds instellen, maar u moet de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app voor](user-help-auth-app-add-account-manual.md)informatie over het handmatig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **Mobiele app configureren** op uw computer en kies **Volgende**.

5. Ga terug naar uw computer en de pagina **Extra beveiligingsverificatie,** zorg ervoor dat u het bericht ontvangt waarin staat dat uw configuratie is geslaagd en selecteer **Volgende**.

    ![Extra pagina voor beveiligingsverificatie, met succesbericht](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    De authenticator-app vraagt om een verificatiecode als test.

6. Blader vanuit de Microsoft Authenticator-app omlaag naar uw werk- of schoolaccount, kopieer en plak de 6-cijferige code van de app in de **stap 2: Voer de verificatiecode in het** vak mobiele app op uw computer in en selecteer **Verifiëren**.

    ![Pagina met extra beveiligingsverificatie, met verificatiecodetest](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Op uw computer voegt u uw telefoonnummer voor mobiele apparaten toe aan stap **3: Als u de toegang tot het mobiele app-gebied verliest** en vervolgens **Volgende**selecteert.

    We raden u ten zeerste aan om uw telefoonnummer voor mobiele apparaten toe te voegen als back-up als u om welke reden dan ook geen toegang hebt tot de mobiele app of deze niet gebruiken.

8. Vanuit stap **4: Blijf uw bestaande toepassingen** gebied gebruiken, kopieer het opgegeven app-wachtwoord en plak het ergens veilig.

    ![Het gedeelte Met app-wachtwoorden van de pagina Extra beveiligingsverificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)voor informatie over het gebruik van het app-wachtwoord met uw oudere apps. U hoeft alleen app-wachtwoorden te gebruiken als u oudere apps blijft gebruiken die geen tweestapsverificatie ondersteunen.

9. Selecteer **Done**.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw tweestapsverificatiemethode hebt ingesteld, u aanvullende methoden toevoegen, uw instellingen en app-wachtwoorden beheren, zich aanmelden of hulp krijgen bij enkele veelvoorkomende problemen met tweefactorverificatie.

- [Uw tweestapsverificatiemethode-instellingen beheren](multi-factor-authentication-end-user-manage-settings.md)

- [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)

- [Aanmelden met tweestapsverificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij tweestapsverificatie](multi-factor-authentication-end-user-troubleshoot.md)
