---
title: Niet-Microsoft-accounts toevoegen aan de Microsoft Authenticator-app - Azure AD
description: Voeg niet-Microsoft-accounts, zoals voor Google of Facebook, toe aan de Microsoft Authenticator-app om uw identiteit te verifiëren terwijl u tweestapsverificatie gebruikt.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 8650d0170e8ff910140e2b432dd1c998d19e72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063948"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Niet-Microsoft-accounts toevoegen aan de Microsoft Authenticator-app

Voeg uw niet-Microsoft-accounts, zoals voor Google, Facebook of GitHub, toe aan de Microsoft Authenticator-app voor tweestapsverificatie. De app Microsoft Authenticator werkt met elke app die verificatie in twee stappen gebruikt en met elk account dat de TOTP-normen (Time-Based One-Time Passwords) ondersteunt.

>[!Important]
>Voordat u uw account toevoegen, moet u de Microsoft Authenticator-app downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het [artikel Downloaden en installeren.](user-help-auth-app-download-install.md)

## <a name="add-personal-accounts"></a>Persoonlijke accounts toevoegen

Over het algemeen moet u voor al uw persoonlijke accounts:

1. Meld u aan bij uw account en schakel tweestapsverificatie in met uw apparaat of uw computer.

2. Voeg het account toe aan de Microsoft Authenticator-app. U wordt mogelijk gevraagd om een QR-code te scannen als onderdeel van dit proces.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, ontvangt u mogelijk een prompt met de vraag of u de app toegang wilt geven tot uw camera (iOS) of dat de app foto's kan maken en video (Android) opnemen. U moet **Toestaan** selecteren, zodat de authenticator-app toegang heeft tot uw camera om in de volgende stap een foto van de QR-code te maken. Als u de camera niet toestaat, u de authenticator-app nog steeds instellen, maar u moet de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app voor](user-help-auth-app-add-account-manual.md)informatie over het handmatig toevoegen van de code.

We bieden het proces hier voor je Facebook-, Google-, GitHub- en Amazon-accounts, maar dit proces is hetzelfde voor elke andere app, zoals Instagram, Netflix of Adobe.

## <a name="add-your-google-account"></a>Uw Google-account toevoegen

Voeg uw Google-account toe door verificatie met twee stappen in te schakelen en vervolgens het account aan de app toe te voegen.

### <a name="turn-on-two-factor-verification"></a>Tweestapsverificatie inschakelen

1. Ga op uw https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcomecomputer naar , selecteer **Aan de slag**en controleer vervolgens uw identiteit.

2. Volg de stappen op de pagina om verificatie in twee stappen in te schakelen voor uw persoonlijke Google-account.

### <a name="add-your-google-account-to-the-app"></a>Uw Google-account toevoegen aan de app

1. Ga op de Google-pagina op uw computer naar de sectie **Alternatieve tweede stap instellen** en kies **Instellen** in de sectie **Authenticator-app.**

2. Selecteer op de pagina Codes ophalen op de pagina **Authenticator-app** de optie **Android** of **iPhone** op basis van uw telefoontype en selecteer **Volgende**.

    U krijgt een QR-code die u gebruiken om uw account automatisch te koppelen aan de Microsoft Authenticator-app. Sluit dit raam niet.

3. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en besturingselement** rechtsboven en selecteer **Vervolgens Ander account (Google, Facebook, enz.)**.

4. Gebruik de camera van uw apparaat om de QR-code te scannen op de pagina **Authenticator instellen** op uw computer.

    >[!Note]
    >Als uw camera niet goed werkt, u de QR-code en URL handmatig invoeren.

5. Bekijk de **pagina Accounts** van de Microsoft Authenticator-app op uw apparaat, om te controleren of uw accountgegevens juist zijn en of er een bijbehorende verificatiecode van zes cijfers is.

    Voor extra beveiliging wordt de verificatiecode elke 30 seconden gewijzigd waardoor iemand een code meerdere keren kan gebruiken.

6. Selecteer **Volgende** op de pagina **Authenticator instellen** op uw computer, typ de zescijferige verificatiecode die in de app voor uw Google-account wordt opgegeven en selecteer Vervolgens **Verifiëren**.

7. Uw account is geverifieerd en u **Gereed** selecteren om de pagina **Authenticator instellen** te sluiten.

    >[!NOTE]
    >Zie Verificatie in twee stappen inschakelen en meer informatie over verificatie in twee stappen voor meer informatie over [tweestapsverificatie](https://www.google.com/landing/2step/help.html)en uw [Google-account.](https://support.google.com/accounts/answer/185839)

## <a name="add-your-facebook-account"></a>Je Facebook-account toevoegen

Voeg je Facebook-account toe door tweestapsverificatie in te schakelen en vervolgens het account aan de app toe te voegen.

### <a name="turn-on-two-factor-verification"></a>Tweestapsverificatie inschakelen

1. Open Facebook op je computer, selecteer het vervolgkeuzemenu in de rechterbovenhoek en ga naar **Instellingenbeveiliging** > **en Aanmelding.**

    De pagina **Beveiliging en aanmelding** wordt weergegeven.

2. Ga naar de optie **Tweestapsverificatie gebruiken** in de sectie **Tweestapsverificatie** en selecteer **Bewerken**.

    De pagina **Tweefactorauthenticatie** wordt weergegeven.

3. Selecteer **Inschakelen**.

### <a name="add-your-facebook-account-to-the-app"></a>Je Facebook-account toevoegen aan de app

1. Ga op de Facebook-pagina op je computer naar de sectie **Een back-up toevoegen** en kies **Instellen** in het gebied van de **verificatie-app.**

    U krijgt een QR-code die u gebruiken om uw account automatisch te koppelen aan de Microsoft Authenticator-app. Sluit dit raam niet.

2. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en besturingselement** rechtsboven en selecteer **Vervolgens Ander account (Google, Facebook, enz.)**.

3. Gebruik de camera van uw apparaat om de QR-code te scannen op de **tweestapsverificatiepagina** op uw computer.

    >[!Note]
    >Als uw camera niet goed werkt, u de QR-code en URL handmatig invoeren.

4. Bekijk de **pagina Accounts** van de Microsoft Authenticator-app op uw apparaat, om te controleren of uw accountgegevens juist zijn en of er een bijbehorende verificatiecode van zes cijfers is.

    Voor extra beveiliging wordt de verificatiecode elke 30 seconden gewijzigd waardoor iemand een code meerdere keren kan gebruiken.

5. Selecteer **Volgende** op de pagina **Tweestapsverificatie** op uw computer en typ vervolgens de zescijferige verificatiecode die in de app wordt opgegeven voor uw Facebook-account.

    Je account is geverifieerd en je de app nu gebruiken om je account te verifiëren.

    >[!NOTE]
    >Zie [Wat is tweestapsverificatie en hoe werkt het?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Uw GitHub-account toevoegen

Voeg uw GitHub-account toe door verificatie met twee stappen in te schakelen en vervolgens het account aan de app toe te voegen.

### <a name="turn-on-two-factor-verification"></a>Tweestapsverificatie inschakelen

1. Open GitHub op uw computer, selecteer uw afbeelding in de rechterbovenhoek en selecteer **Instellingen**.

    De pagina **Tweestapsverificatie** wordt weergegeven.

2. Selecteer **Beveiliging** in de zijbalk **Persoonlijke instellingen** en selecteer **tweestapsverificatie inschakelen** in het gebied **tweestapsverificatie.**

### <a name="add-your-github-account-to-the-app"></a>Uw GitHub-account toevoegen aan de app

1. Selecteer op de pagina **Tweestapsverificatie** op uw computer de optie **Instellen met een app**.

2. Sla uw herstelcodes op zodat u weer toegang krijgen tot uw account als u de toegang verliest en selecteer **Volgende**. 

    U uw codes opslaan door ze naar uw apparaat te downloaden, door een papieren kopie af te drukken of door ze te kopiëren naar een hulpprogramma voor wachtwoordbeheer.

3. Selecteer op de pagina **Tweestapsverificatie** de optie **Instellen met een app**.

    De pagina wordt gewijzigd om u een QR-code te laten zien. Sluit deze pagina niet.

4. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en besturingselement** rechtsboven, selecteer Ander account **(Google, Facebook, enz.)** en selecteer **deze tekstcode** in de tekst boven aan de pagina.

    De Microsoft Authenticator-app kan de QR-code niet scannen, dus u moet de code handmatig invoeren.

5. Voer een **accountnaam** in (bijvoorbeeld GitHub) en typ de **geheime sleutel** van stap 4 en selecteer **Voltooien.**

6. Typ op de pagina **Tweestapsauthenticator** op uw computer de zescijferige verificatiecode die in de app voor uw GitHub-account is opgegeven en selecteer **Inschakelen**.

    Op de pagina **Accounts** van de app ziet u uw accountnaam en een zescijferige verificatiecode. Voor extra beveiliging wordt de verificatiecode elke 30 seconden gewijzigd waardoor iemand een code meerdere keren kan gebruiken.

    >[!NOTE]
    >Zie [Over tweestapsverificatie](https://help.github.com/articles/about-two-factor-authentication/)voor meer informatie over tweestapsverificatie en uw GitHub-account.

## <a name="add-your-amazon-account"></a>Je Amazon-account toevoegen

Voeg je Amazon-account toe door tweestapsverificatie in te schakelen en vervolgens het account aan de app toe te voegen.

### <a name="turn-on-two-factor-verification"></a>Tweestapsverificatie inschakelen

1. Open Amazon op uw computer, selecteer het vervolgkeuzemenu **Account & lijsten** en selecteer uw **account**.

2. Selecteer **Aanmelden & beveiliging,** meld u aan bij uw Amazon-account en selecteer **Bewerken** in het gebied **Geavanceerde beveiligingsinstellingen.**

    De pagina **Geavanceerde beveiligingsinstellingen** wordt weergegeven.

3. Selecteer **Aan de slag**.

4. Selecteer **Authenticator-app** op de pagina **Kiezen hoe u codes ontvangt.**

    De pagina wordt gewijzigd om u een QR-code te laten zien. Sluit deze pagina niet.

5. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en besturingselement** rechtsboven en selecteer **Vervolgens Ander account (Google, Facebook, enz.)**.

6. Gebruik de camera van uw apparaat om de QR-code te scannen op de pagina Kiezen hoe u codes op uw computer **ontvangt.**

    >[!Note]
    >Als uw camera niet goed werkt, u de QR-code en URL handmatig invoeren.

7. Bekijk de **pagina Accounts** van de Microsoft Authenticator-app op uw apparaat, om te controleren of uw accountgegevens juist zijn en of er een bijbehorende verificatiecode van zes cijfers is.

    Voor extra beveiliging wordt de verificatiecode elke 30 seconden gewijzigd waardoor iemand een code meerdere keren kan gebruiken.

8. Typ op de **pagina Codes kiezen** op uw computer de zescijferige verificatiecode die in de app voor uw Amazon-account wordt opgegeven en selecteer Code verifiëren en **doorgaan.**

9. Voltooi de rest van het aanmeldingsproces, inclusief het toevoegen van een back-upverificatiemethode, zoals een sms-bericht, en selecteer **Code verzenden**.

10. Typ op de pagina **Een back-upverificatiemethode** toevoegen op uw computer de verificatiecode van zes cijfers die door uw verificatiemethode voor back-up voor uw Amazon-account wordt verstrekt en selecteer **Vervolgens Code verifiëren en doorgaan.**

11. Bepaal op de pagina **Bijna klaar** of u van uw computer een vertrouwd apparaat wilt maken en selecteer Vervolgens **Got it. Verificatie in twee stappen inschakelen**.

    De pagina **Geavanceerde beveiligingsinstellingen** wordt weergegeven met uw bijgewerkte verificatiegegevens van twee stappen.

    >[!NOTE]
    >Zie Verificatie in twee stappen en aanmelden met verificatie in twee stappen voor meer informatie over [tweestapsverificatie](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)en uw [Amazon-account.](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330)

## <a name="next-steps"></a>Volgende stappen

- Nadat u uw accounts aan de app hebt toegevoegd, u zich aanmelden met de Authenticator-app op uw apparaat. Zie [Aanmelden met de app](user-help-auth-app-sign-in.md)voor meer informatie.

- Voor apparaten met iOS u ook een back-up maken van uw accountreferenties en gerelateerde app-instellingen, zoals de volgorde van uw accounts, naar de cloud. Zie [Back-upmaken en herstellen met de Microsoft Authenticator-app voor](user-help-auth-app-backup-recovery.md)meer informatie.
