---
title: Uw verificatiemethode en -instellingen met twee stappen wijzigen - Azure Active Directory
description: Meer informatie over het wijzigen van de beveiligingsverificatiemethode en -instellingen voor uw werk- of schoolaccount, op de pagina Aanvullende beveiligingsverificatie.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253245"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Uw verificatiemethode en -instellingen met twee stappen wijzigen

Nadat u uw beveiligingsverificatiemethoden hebt ingesteld voor uw werk- of schoolaccount, u een van de gerelateerde gegevens bijwerken, waaronder:

- Standaardbeveiligingsverificatiemethode

- Details van de beveiligingsverificatiemethode, zoals uw telefoonnummer

- Het instellen of verwijderen van een apparaat door een authenticator-app

## <a name="using-the-additional-security-verification-page"></a>De pagina Extra beveiligingsverificatie gebruiken

Als uw organisatie u specifieke stappen heeft gegeven over het inschakelen en beheren van uw tweestapsverificatie, moet u deze instructies eerst opvolgen. Anders u via de pagina [Extra beveiligingsverificatie](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time) naar de instellingen van uw beveiligingsverificatiemethode gaan.

>[!Note]
>Als wat u op uw scherm ziet niet overeenkomt met wat in dit artikel wordt behandeld, betekent dit dat uw beheerder de ervaring **Beveiligingsgegevens (preview)** heeft ingeschakeld of dat uw organisatie uw eigen aangepaste portal heeft geleverd. Zie [Overzicht van beveiligingsgegevens (voorbeeld).](user-help-security-info-overview.md) Voor meer informatie over de aangepaste portal van uw organisatie moet u contact opnemen met de helpdesk van uw organisatie.

### <a name="to-get-to-the-additional-security-verification-page"></a>Naar de pagina Extra beveiligingsverificatie gaan

U deze link volgen naar de [pagina Aanvullende beveiligingsverificatie.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

![Aanvullende pagina met beveiligingsverificatie, met de gegevens van de beschikbare beveiligingsverificatiemethode](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

U ook naar de pagina **Aanvullende beveiligingsverificatie** gaan door de volgende stappen te volgen:

1. Log hier [https://myapps.microsoft.com](https://myapps.microsoft.com)in

1. Selecteer rechtsboven uw accountnaam en selecteer **profiel**.

1. Selecteer **Extra beveiligingsverificatie**.  

    ![Koppeling naar de koppeling Mijn apps naar de pagina Aanvullende beveiligingsverificatie](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Zie [App-wachtwoorden beheren voor verificatie door twee stappen](multi-factor-authentication-end-user-app-passwords.md)te beheren voor informatie over het gebruik van de sectie **App-wachtwoorden** van de pagina **Extra beveiliging.** App-wachtwoorden mogen alleen worden gebruikt voor apps die geen tweestapsverificatie ondersteunen.

## <a name="change-your-default-security-verification-method"></a>Uw standaardbeveiligingsverificatiemethode wijzigen

Nadat u zich met uw gebruikersnaam en wachtwoord hebt aangemeld bij uw werk- of schoolaccount, krijgt u automatisch de door u gekozen beveiligingsverificatiemethode te zien. Afhankelijk van de vereisten van uw organisatie kan dit een meldings- of verificatiecode zijn via een authenticator-app, een sms of een telefoongesprek.

Als u besluit dat u de standaardbeveiligingsverificatiemethode die u gebruikt wilt wijzigen, u dit vanaf hier doen.

### <a name="to-change-your-default-security-verification-method"></a>Uw standaardbeveiligingsverificatiemethode wijzigen

1. Selecteer op de pagina **Extra beveiligingsverificatie** de methode die u wilt gebruiken in de **optielijst Wat is uw voorkeur.** U ziet alle opties, maar u alleen de opties selecteren die door uw organisatie aan u beschikbaar zijn gesteld.

    - **Laat het me weten via de app:** Je krijgt via je authenticator-app een melding dat je een verificatieprompt hebt.

    - **Bel mijn verificatietelefoon:** u krijgt een telefoontje op uw mobiele apparaat en vraagt u om uw gegevens te verifiëren.

    - **Tekstcode naar mijn verificatietelefoon**: U krijgt een verificatiecode als onderdeel van een sms-bericht op uw mobiele apparaat. U moet deze code invoeren in de verificatieprompt voor uw werk- of schoolaccount.

    - **Bel mijn kantoor telefoon**: Je krijgt een telefoontje op uw kantoor telefoon, vraagt u om uw gegevens te verifiëren.

    - **Verificatiecode van de app gebruiken:** u gebruikt uw authenticator-app om een verificatiecode te krijgen die u in de prompt typt vanuit uw werk- of schoolaccount.

2. Selecteer **Opslaan**.

## <a name="add-or-change-your-phone-number"></a>Uw telefoonnummer toevoegen of wijzigen

U nieuwe telefoonnummers toevoegen of bestaande nummers bijwerken via de pagina **Extra beveiligingsverificatie.**

>[!Important]
>We raden u ten zeerste aan een secundair telefoonnummer toe te voegen om te voorkomen dat uw account wordt buitengesloten als uw primaire telefoon verloren of gestolen is, of als u een nieuwe telefoon krijgt en niet langer uw oorspronkelijke, primaire telefoonnummer hebt.

### <a name="to-change-your-phone-numbers"></a>Uw telefoonnummers wijzigen

1. In de **sectie Hoe wilt u reageren?** Op de pagina Aanvullende **beveiligingsverificatie** u de telefoonnummergegevens van uw **verificatietelefoon** (uw primaire mobiele apparaat) en uw **Office-telefoon bijwerken.**

1. Schakel het vakje naast de optie **Alternatieve verificatietelefoon** in en typ een secundair telefoonnummer in waar u sms-berichten of telefoongesprekken ontvangen als u geen toegang hebt tot uw primaire apparaat.

1. Selecteer **Opslaan**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Een nieuw account toevoegen aan de Microsoft authenticator-app

U uw werk- of schoolaccount instellen in de Microsoft Authenticator-app voor [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) of [iOS.](https://apps.apple.com/app/microsoft-authenticator/id983156458)

Als u uw werk- of schoolaccount al hebt ingesteld in de Microsoft Authenticator-app, hoeft u dit niet opnieuw te doen.

1. Selecteer **Authenticator-app instellen**in **Additional security verification** de **sectie Hoe wilt u reageren?**

    ![Uw werk- of schoolaccount instellen in de Microsoft Authenticator-app](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Volg de instructies op het scherm, inclusief het gebruik van uw mobiele apparaat om de QR-code te scannen en selecteer **Volgende**.

    U wordt gevraagd een melding goed te keuren via de Microsoft Authenticator-app om uw gegevens te verifiëren.

1. Selecteer **Opslaan**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Uw account of apparaat verwijderen uit de Microsoft Authenticator-app

U uw account verwijderen uit de Microsoft Authenticator-app en uw apparaat verwijderen uit uw werk- of schoolaccount. Meestal verwijdert u uw apparaat om een verloren, gestolen of oud apparaat permanent uit uw account te verwijderen en verwijdert u uw account om te proberen bepaalde verbindingsproblemen op te lossen of een accountwijziging aan te pakken, zoals een nieuwe gebruikersnaam.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Uw apparaat verwijderen uit uw werk- of schoolaccount

1. Selecteer op de pagina Hoe wilt **Additional security verification** **u reageren?** **Set up Authenticator app**

1. Selecteer **Opslaan**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Uw account verwijderen uit de Microsoft Authenticator-app

Selecteer in de Microsoft Authenticator-app de knop **Verwijderen** naast het apparaat dat u wilt verwijderen.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Tweestapsverificatieprompts inschakelen op een vertrouwd apparaat

Afhankelijk van uw organisatie-instellingen ziet u mogelijk een selectievakje met de tekst **Niet opnieuw vragen voor X-dagen** wanneer u tweestapsverificatie uitvoert in uw browser. Als u deze optie hebt geselecteerd om tweestapsverificatieprompts te stoppen en u uw apparaat verliest of uw apparaat mogelijk is gecompromitteerd, moet u tweestapsverificatieprompts weer inschakelen om uw account te beschermen. U moet de prompts tegelijk inschakelen voor al uw apparaten. Helaas u de prompts niet weer inschakelen voor alleen een specifiek apparaat.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Tweefactorverificatieprompts weer inschakelen voor uw apparaten

Selecteer op de pagina [ **Extra beveiligingsverificatie** ](#to-get-to-the-additional-security-verification-page)de optie **Meervoudige verificatie herstellen op eerder vertrouwde apparaten**. De volgende keer dat u zich aanmeldt op een apparaat, wordt u gevraagd om tweestapsverificatie uit te voeren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw tweestapsverificatie-instellingen hebt toegevoegd of bijgewerkt, u uw app-wachtwoorden beheren, u aanmelden of hulp krijgen bij enkele veelvoorkomende problemen met tweefactorverificatie.

- [Beheer app-wachtwoorden voor tweestapsverificatie](multi-factor-authentication-end-user-app-passwords.md) voor apps die geen tweestapsverificatie ondersteunen.

- [Aanmelden met tweestapsverificatie](multi-factor-authentication-end-user-signin.md)

- [Veelvoorkomende problemen oplossen met tweefactorverificatie](multi-factor-authentication-end-user-troubleshoot.md)
