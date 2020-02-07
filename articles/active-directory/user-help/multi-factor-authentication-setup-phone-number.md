---
title: Stel een mobiel apparaat in als uw twee ledige verificatie methode-Azure Active Directory | Microsoft Docs
description: Meer informatie over het instellen van een mobiel apparaat als uw verificatie methode met twee factoren.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: b0bfaa87c77ba9fff9f6605c1989e48ffbc3fb35
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062503"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Een mobiel apparaat instellen als uw verificatie methode met twee factoren

U kunt uw mobiele apparaat instellen om te fungeren als uw verificatie methode met twee factoren. Uw mobiele telefoon kan een SMS-bericht ontvangen met een verificatie code of telefoon gesprek.

>[!Note]
> Als de optie telefoon voor verificatie grijs wordt weer gegeven, is het mogelijk dat uw organisatie geen telefoon nummer of SMS-bericht mag gebruiken voor verificatie. In dit geval moet u een andere methode selecteren of contact opnemen met de beheerder voor meer hulp.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Stel uw mobiele apparaat in voor het gebruik van een SMS-bericht als verificatie methode

1. Selecteer op de pagina **aanvullende beveiligings verificatie** de optie telefoon nummer voor **authenticatie** in **stap 1: hoe moeten we contact met u opnemen?** Selecteer uw land of regio in de vervolg keuzelijst en typ vervolgens uw mobiele apparaat.

2. Selecteer **Ik wil een code per SMS-bericht** in het gedeelte **methode** verzenden en selecteer **volgende**.

    ![Aanvullende beveiligings verificatie pagina met verificatie telefoon en SMS-bericht](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Typ de verificatie code uit het SMS-bericht dat van micro soft wordt verzonden naar de **stap 2: we hebben een SMS-bericht naar uw telefoon gebied verzonden** en selecteer vervolgens **verifiëren**.

    ![Aanvullende beveiligings verificatie pagina met verificatie telefoon en SMS-bericht](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. In **stap 3: het gebied bestaande toepassingen blijven gebruiken** , kopieert u het opgegeven app-wacht woord en plakt u het op een veilige plek.

    ![Het gedeelte app-wacht woorden van de pagina aanvullende beveiligings verificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [app-wacht woorden beheren](multi-factor-authentication-end-user-app-passwords.md)voor meer informatie over het gebruik van het app-wacht woord met uw oudere apps. U hoeft alleen app-wacht woorden te gebruiken als u oudere apps gaat gebruiken die geen ondersteuning bieden voor twee ledige verificatie.

5. Selecteer **Done**.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Uw mobiele apparaat instellen om een telefoon gesprek te ontvangen

1. Selecteer op de pagina **aanvullende beveiligings verificatie** de optie telefoon nummer voor **authenticatie** in **stap 1: hoe moeten we contact met u opnemen?** Selecteer uw land of regio in de vervolg keuzelijst en typ vervolgens uw mobiele apparaat.

2. Selecteer **mij bellen** in het gedeelte **methode** en selecteer vervolgens **volgende**.

    ![Aanvullende beveiligings verificatie pagina, met verificatie telefoon en telefoon gesprek](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. U ontvangt een telefonische oproep van micro soft. u wordt gevraagd op het hekje (#) op uw mobiele apparaat te drukken om uw identiteit te verifiëren.

    ![Het opgegeven telefoon nummer testen](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. In **stap 3: het gebied bestaande toepassingen blijven gebruiken** , kopieert u het opgegeven app-wacht woord en plakt u het op een veilige plek.

    ![Het gedeelte app-wacht woorden van de pagina aanvullende beveiligings verificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [app-wacht woorden beheren](multi-factor-authentication-end-user-app-passwords.md)voor meer informatie over het gebruik van het app-wacht woord met uw oudere apps. U hoeft alleen app-wacht woorden te gebruiken als u oudere apps gaat gebruiken die geen ondersteuning bieden voor twee ledige verificatie.

5. Selecteer **Done**.

## <a name="next-steps"></a>Volgende stappen

Nadat u de twee ledige verificatie methode hebt ingesteld, kunt u extra methoden toevoegen, uw instellingen en app-wacht woorden beheren, aanmelden of hulp krijgen bij enkele veelvoorkomende problemen met twee ledige verificatie.

- [De instellingen voor de verificatie methode met twee factoren beheren](multi-factor-authentication-end-user-manage-settings.md)

- [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)

- [Aanmelden met twee ledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij twee ledige verificatie](multi-factor-authentication-end-user-troubleshoot.md)
