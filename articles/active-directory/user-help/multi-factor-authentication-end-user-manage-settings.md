---
title: De twee ledige verificatie methode en-instellingen wijzigen-Azure AD
description: Meer informatie over het wijzigen van de beveiligings verificatie methode en instellingen voor uw werk-of school account, van de pagina aanvullende beveiligings verificatie.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: curtand
ms.openlocfilehash: 457608af9a5ffc06a778e900363bc8138fcb4e99
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062588"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>De twee ledige verificatie methode en-instellingen wijzigen

Nadat u de beveiligings verificatie methoden voor uw werk-of school account hebt ingesteld, kunt u de gerelateerde gegevens bijwerken, waaronder:

- De standaard methode voor beveiligings verificatie te kiezen.

- U kunt de gegevens van de beveiligings verificatie methode toevoegen of bijwerken, zoals uw telefoon nummer.

- Het instellen van een nieuwe verificator-app of het verwijderen van een apparaat uit de verificator-app.

## <a name="using-the-additional-security-verification-page"></a>De extra beveiligings verificatie pagina gebruiken

Als uw organisatie u heeft voorzien van specifieke stappen voor het inschakelen en beheren van uw twee ledige verificatie, moet u deze instructies volgen. Als dat niet het geval is, kunt u de instellingen voor de beveiligings verificatie methode van de [extra beveiligings verificatie](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) pagina.

>[!Note]
>Als datgene wat u op uw scherm ziet, niet overeenkomt met wat er in dit artikel wordt behandeld, betekent dit dat de beheerder de beveiligings gegevens (preview-versie) heeft ingeschakeld of dat uw organisatie een eigen aangepaste portal heeft. Zie voor meer informatie over de beveiligings informatie, [overzicht van beveiligings gegevens (preview)](user-help-security-info-overview.md). Voor meer informatie over de aangepaste portal van uw organisatie moet u contact opnemen met uw Help Desk.

### <a name="to-get-to-the-additional-security-verification-page"></a>De extra beveiligings verificatie pagina weer geven

- Ga naar de pagina [aanvullende beveiligings verificatie](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) .

    ![Aanvullende beveiligings verificatie pagina, met de beschik bare beveiligings verificatie methode Details](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    Als u op de koppeling klikt, kunt u ook naar de pagina **aanvullende beveiligings verificatie** gaan door de volgende stappen uit te voeren:

    1. Meld u aan bij [https://myapps.microsoft.com](https://myapps.microsoft.com).

    2. Selecteer uw account naam in de rechter bovenhoek en selecteer vervolgens **profiel**.

    3. Selecteer **aanvullende beveiligings verificatie**.  

        ![Mijn apps koppeling naar de pagina aanvullende beveiligings verificatie](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Zie [app-wacht woorden beheren voor twee ledige verificatie](multi-factor-authentication-end-user-app-passwords.md)voor meer informatie over het gebruik van de sectie **app-wacht woorden** van een **aanvullende beveiligings verificatie** pagina. App-wacht woorden mogen alleen worden gebruikt voor apps die nog geen ondersteuning bieden voor twee ledige verificatie.

## <a name="change-your-default-security-verification-method"></a>Uw standaard methode voor beveiligings verificatie wijzigen

Nadat u zich hebt aangemeld bij uw werk-of school account met uw gebruikers naam en wacht woord, wordt u automatisch weer gegeven met de gekozen methode voor beveiligings verificatie. Afhankelijk van de vereisten van uw organisatie, kan dit een melding of een verificatie code via een verificator-app, een tekst bericht of een telefoon gesprek zijn.

Als u besluit dat u de standaard beveiligings verificatie methode die u gebruikt, wilt wijzigen, kunt u dat hier doen.

### <a name="to-change-your-default-security-verification-method"></a>De standaard methode voor beveiligings verificatie wijzigen

1. Selecteer op de pagina **aanvullende beveiligings verificatie** de methode die u wilt gebruiken in de vervolg keuzelijst **Wat is uw voorkeurs optie** . U ziet alle opties, maar u kunt alleen de instellingen kiezen die voor u beschikbaar zijn in uw organisatie.

    - **Stuur mij een melding via de app.** U wordt gewaarschuwd via uw verificator-app.

    - **Bel mijn verificatie telefoon.** U ontvangt een telefoon gesprek op uw mobiele apparaat, waarin u wordt gevraagd om uw gegevens te verifiëren.

    - **Tekst code naar mijn verificatie telefoon.** U ontvangt een verificatie code als onderdeel van een SMS-bericht op uw mobiele apparaat. U moet deze code invoeren in de verificatie prompt voor uw werk-of school account.

    - **Bel mijn zakelijke telefoon.** U krijgt een telefoon gesprek op uw zakelijke telefoon, waarin u wordt gevraagd om uw gegevens te verifiëren.

    - **Gebruik de verificatie code uit de app.** U gebruikt uw verificator-app om een verificatie code op te halen die u typt in de prompt van uw werk-of school account.

2. Selecteer **Opslaan**.

## <a name="add-or-change-your-phone-number"></a>Uw telefoon nummer toevoegen of wijzigen

U kunt nieuwe telefoon nummers toevoegen of bestaande nummers bijwerken op de pagina **aanvullende beveiligings verificatie** .

>[!Important]
>We raden u ten zeerste aan om een tweede telefoon nummer toe te voegen om te voor komen dat uw account wordt vergrendeld als uw primaire telefoon is kwijt geraakt of gestolen, of als u een nieuwe telefoon hebt en u niet langer het oorspronkelijke, primaire telefoon nummer hebt.

### <a name="to-change-your-phone-numbers"></a>Telefoon nummers wijzigen

1. Werk in de sectie **Hoe wilt u reageren?** van de pagina **aanvullende beveiligings verificatie** de telefoon nummer gegevens voor uw **telefoon voor authenticatie** (uw primaire mobiele apparaat) en uw **zakelijke telefoon**.

2. Schakel het selectie vakje naast de optie **alternatieve telefoon voor authenticatie** in en typ in een secundair telefoon nummer waar u tekst berichten of telefoon gesprekken kunt ontvangen als u geen toegang hebt tot uw primaire apparaat.

3. Selecteer **Opslaan**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Een nieuw account toevoegen aan de micro soft Authenticator-app

U kunt uw werk-of school account instellen op de Microsoft Authenticator-app voor [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) of [IOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Als u uw werk-of school account in de app Microsoft Authenticator hebt ingesteld, hoeft u dit niet opnieuw te doen.

1. Selecteer de knop **verificator-app instellen** in het gedeelte **Hoe wilt u reageren?** van de pagina **aanvullende beveiligings verificatie** .

    ![Uw werk-of school account instellen in de Microsoft Authenticator-app](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. Volg de instructies op het scherm, inclusief het gebruik van uw mobiele apparaat om de QR-code te scannen en selecteer vervolgens **volgende**.

    U wordt gevraagd een melding via de app Microsoft Authenticator goed te keuren om uw gegevens te verifiëren.

3. Selecteer **Opslaan**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Uw account of apparaat uit de Microsoft Authenticator-app verwijderen

U kunt uw account uit de app Microsoft Authenticator verwijderen en u kunt uw apparaat verwijderen uit uw werk-of school account. Normaal gesp roken verwijdert u uw apparaat om een verloren, gestolen of oud apparaat permanent uit uw account te verwijderen en verwijdert u uw account om te proberen enkele verbindings problemen op te lossen of om een account wijziging aan te pakken, zoals een nieuwe gebruikers naam.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Uw apparaat verwijderen uit uw werk-of school account

1. Selecteer de knop **verificator-app instellen** in het gedeelte **Hoe wilt u reageren?** van de pagina **aanvullende beveiligings verificatie** .

2. Selecteer **Opslaan**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Uw account verwijderen uit de Microsoft Authenticator-app

- Selecteer in de Microsoft Authenticator-app de knop **verwijderen** naast het apparaat dat u wilt verwijderen.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Twee ledige verificatie prompts inschakelen op een vertrouwd apparaat

Afhankelijk van de instellingen van uw organisatie ziet u mogelijk een selectie vakje waarin wordt aangegeven dat u de **X dagen niet opnieuw moet vragen** wanneer u twee ledige verificatie uitvoert voor uw browser. Als u dit selectie vakje hebt ingeschakeld om twee ledige verificatie vragen te stoppen en u het apparaat kwijtraakt of het apparaat mogelijk is aangetast, moet u de twee ledige verificatie prompts weer inschakelen om uw account te beveiligen. Helaas kunt u de prompts niet weer inschakelen voor één apparaat. U moet de prompts inschakelen voor al uw apparaten tegelijk.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Om twee ledige verificatie vragen weer in te scha kelen voor uw apparaten

- Selecteer op de pagina **aanvullende beveiligings verificatie** de optie **multi-factor Authentication herstellen op eerder vertrouwde apparaten**.

    De volgende keer dat u zich aanmeldt op een apparaat, wordt u gevraagd om twee ledige verificatie uit te voeren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw twee ledige verificatie-instellingen hebt toegevoegd of bijgewerkt, kunt u uw app-wacht woorden beheren, aanmelden of hulp krijgen bij enkele veelvoorkomende problemen met twee ledige verificatie.

- [App-wacht woorden beheren voor twee ledige verificatie](multi-factor-authentication-end-user-app-passwords.md) voor alle apps die geen ondersteuning bieden voor twee ledige verificatie.

- [Aanmelden met twee ledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij twee ledige verificatie](multi-factor-authentication-end-user-troubleshoot.md)
