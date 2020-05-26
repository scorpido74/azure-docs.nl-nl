---
title: Wat is de pagina Aanvullende verificatie? - Azure AD
description: Hoe kom ik bij de pagina Aanvullende beveiligingsverificatie pagina voor tweeledige verificatie?
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 47220bc721092005c7e4d65a00eb933cd5c49a8c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746772"
---
# <a name="what-is-the-additional-verification-page"></a>Wat is de pagina Aanvullende verificatie?

U hebt een e-mail ontvangen van iemand op de IT-afdeling of van uw baas met de mededeling dat in de organisatie extra beveiligingsverificatie is toegevoegd aan uw account. Wat houdt dit in? Dit houdt in dat in uw organisatie extra stappen worden ondernomen om uw identiteit te controleren bij het aanmelden. Deze extra verificatie, ook wel tweeledige verificatie genoemd, is een combinatie van uw gebruikersnaam, uw wachtwoord en een mobiel apparaat of mobiele telefoon.

Tweeledige verificatie is veiliger dat alleen een wachtwoord omdat het is gebaseerd op twee soorten verificatie: iets dat u weet, en iets dat u bij u hebt. Iets dat u weet, is uw wachtwoord. Iets dat u bij u hebt, is een telefoon of apparaat dat u meestal met u meeneemt. Tweeledige verificatie kan helpen voorkomen dat hackers zich voordoen als u, omdat ze - zelfs als ze uw wachtwoord hebben achterhaald - waarschijnlijk niet ook nog beschikken over uw apparaat.

>[!Important]
>Dit artikel is bedoeld voor gebruikers die tweeledige verificatie gebruiken voor een werk- of schoolaccount (zoals alain@contoso.com). Als u een beheerder bent die op zoek is naar informatie over het inschakelen van tweeledige verificatie voor uw werknemers of andere gebruikers, raadpleegt u de [documentatie voor Azure Active Directory-verificatie](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Wie besluit of u deze functie gebruikt?

Afhankelijk van uw accounttype kan uw organisatie besluiten dat u gebruik moet maken van verificatie in twee stappen, maar het is ook mogelijk dat u dat zelf kunt bepalen.

- **Werk- of schoolaccount.** Als u een werk- of schoolaccount gebruikt (zoals alain@contoso.com), is het aan uw organisatie of u verificatie in twee stappen moet gebruiken, samen met de specifieke verificatiemethoden. Omdat uw organisatie u heeft besloten dat u deze functie moet gebruiken, is het niet mogelijk deze afzonderlijk uit te schakelen.

- **Persoonlijk Microsoft-account.** U kunt ervoor kiezen verificatie in twee stappen in te stellen voor uw persoonlijke Microsoft-accounts (zoals alain@outlook.com). Als u problemen ondervindt met tweeledige verificatie en uw persoonlijke Microsoft-account, raadpleegt u [Tweeledige verificatie in- of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Omdat u zelf kiest of u deze functie wilt gebruiken, kunt u deze op elk gewenst moment in- of uitschakelen.

    >[!Note]
    >Als u problemen ondervindt met tweeledige verificatie en een van uw persoonlijke Microsoft-accounts (bijvoorbeeld danielle@outlook.com), kunt u gebruikmaken van de suggesties in [Tweeledige verificatie gebruiken voor uw Microsoft-account](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="access-the-additional-security-verification-page"></a>Toegang tot de pagina Aanvullende beveiligingsverificatie

Nadat uw organisatie tweeledige verificatie heeft ingeschakeld en ingesteld, wordt u gevraagd om meer gegevens te verstrekken om uw account veilig te houden.

![Vraag om meer info te verstrekken](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Toegang krijgen tot de pagina Aanvullende beveiligingsverificatie

1. Selecteer **Volgende** in de prompt **Meer informatie vereist**.

    De pagina **Aanvullende beveiligingsverificatie** wordt weergegeven.

2. Op de pagina **Aanvullende beveiligingsverificatie** moet u besluiten welke tweeledige verificatiemethode u wilt gebruiken om te controleren of u bent wie u zegt nadat u zich hebt aangemeld bij uw werk- of schoolaccount. U kunt het volgende selecteren:

    | Contactmethode | Beschrijving |
    | --- | --- |
    | Mobiele app | <ul><li>**Meldingen ontvangen voor verificatie.** Met deze optie wordt een melding naar de verificator-app op uw smartphone of tablet gepusht. Bekijk de melding en, indien deze in orde is, selecteer **Verifiëren** in de app. Uw werk of onderwijsinstelling kan vereisen dat u een pincode invoert voordat u zich verifieert.</li><li>**Verificatiecode gebruiken.** In deze modus genereert de verificator-app een verificatiecode die elke dertig seconden wordt bijgewerkt. Voer de meest actuele verificatiecode in het aanmeldingsscherm in.<br>De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594) en [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefoonnummer voor verificatie | <ul><li>**Telefonische oproep** voert een automatische spraakoproep uit naar het door u verstrekte nummer. Beantwoord de oproep en druk op het hekje (#) op het toetsenblok om u te verifiëren.</li><li>**Sms-bericht** verzendt een sms-bericht met een verificatiecode. Na de prompt in de tekst antwoordt u op het sms-bericht of voert u de opgegeven verificatiecode in de aanmeldingsinterface in.</li></ul> |
    | Bureautelefoon | Voert een automatische spraakoproep uit naar het door u verstrekte nummer. Beantwoord de oproep en druk op het hekje (#) op het toetsenblok om u te verifiëren. |

## <a name="next-steps"></a>Volgende stappen

Nadat u de pagina **Aanvullende beveiligingsverificatie** hebt geopend, moet u de tweeledige verificatiemethode selecteren en instellen:

- [Uw mobiele apparaat instellen als uw verificatiemethode](multi-factor-authentication-setup-phone-number.md)

- [Uw bureautelefoon instellen als uw verificatiemethode](multi-factor-authentication-setup-office-phone.md)

- [De app Microsoft Authenticator instellen als uw verificatiemethode](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Gerelateerde resources

- [Instellingen voor tweeledige verificatiemethode beheren](multi-factor-authentication-end-user-manage-settings.md)

- [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)

- [Aanmelden met behulp van tweeledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij tweeledige verificatie](multi-factor-authentication-end-user-troubleshoot.md) 
