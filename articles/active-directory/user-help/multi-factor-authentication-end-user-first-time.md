---
title: Wat is de pagina Aanvullende verificatie? - Azure AD
description: Hoe u naar de pagina Aanvullende beveiligingsverificatie gaan voor tweestapsverificatie.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 5a7f0e10b23bf1a541fe83c3112962c38f7e1331
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062554"
---
# <a name="what-is-the-additional-verification-page"></a>Wat is de pagina Aanvullende verificatie?

U hebt een e-mail ontvangen van iemand op de IT-afdeling of van uw baas met de mededeling dat in de organisatie extra beveiligingsverificatie is toegevoegd aan uw account. Wat houdt dit in? Dit houdt in dat in uw organisatie extra stappen worden ondernomen om uw identiteit te controleren bij het aanmelden. Deze extra verificatie, ook wel twee-factor verificatie genoemd, wordt uitgevoerd door een combinatie van uw gebruikersnaam, uw wachtwoord en een mobiel apparaat of telefoon.

Twee-factor verificatie is veiliger dan alleen een wachtwoord, omdat het is gebaseerd op twee vormen van authenticatie: iets wat je weet, en iets wat je hebt met je mee. Iets dat u weet, is uw wachtwoord. Iets dat u bij u hebt, is een telefoon of apparaat dat u meestal met u meeneemt. Twee-factor verificatie kan helpen om te voorkomen dat kwaadwillende hackers zich voordoen als u, want zelfs als ze uw wachtwoord hebben, is de kans groot dat ze uw apparaat niet hebben, ook.

>[!Important]
>Dit artikel is bedoeld voor gebruikers die tweefactorverificatie proberen te gebruiken alain@contoso.commet een werk- of schoolaccount (zoals). Als u een beheerder bent die op zoek is naar informatie over het inschakelen van tweestapsverificatie voor uw werknemers of andere gebruikers, raadpleegt u de [documentatie azure Active Directory Authentication](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Wie besluit of u deze functie gebruikt?

Afhankelijk van uw accounttype kan uw organisatie besluiten dat u gebruik moet maken van verificatie in twee stappen, maar het is ook mogelijk dat u dat zelf kunt bepalen.

- **Werk- of schoolaccount.** Als u een werk- of schoolaccount gebruikt (zoals alain@contoso.com), is het aan uw organisatie of u verificatie in twee stappen moet gebruiken, samen met de specifieke verificatiemethoden. Omdat uw organisatie heeft besloten dat u deze functie moet gebruiken, is er geen manier voor u om deze afzonderlijk uit te schakelen.

- **Persoonlijk Microsoft-account.** U kunt ervoor kiezen verificatie in twee stappen in te stellen voor uw persoonlijke Microsoft-accounts (zoals alain@outlook.com). Zie [Tweestapsverificatie in- of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)als u problemen ondervindt met tweestapsverificatie en uw persoonlijke Microsoft-account. Omdat u ervoor kiest om deze functie te gebruiken, u deze in- en uitschakelen wanneer u maar wilt.

    >[!Note]
    >Als u problemen ondervindt met tweestapsverificatie en een van danielle@outlook.comuw persoonlijke Microsoft-accounts (bijvoorbeeld), u de suggesties voor de verificatie in [twee stappen proberen met uw Microsoft-account.](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)

## <a name="access-the-additional-security-verification-page"></a>Toegang tot de pagina Extra beveiligingsverificatie

Nadat uw organisatie is ingeschakeld en tweestapsverificatie heeft ingesteld, ontvangt u een prompt waarin u wordt verteld dat u meer informatie moet verstrekken om uw account veilig te houden.

![Meer info vereist prompt](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Toegang tot de pagina Extra beveiligingsverificatie

1. Selecteer **Volgende** in de vereiste prompt **meer informatie.**

    De pagina **Extra beveiligingsverificatie** wordt weergegeven.

2. Op de pagina **Aanvullende beveiligingsverificatie** moet u bepalen welke tweestapsverificatiemethode u wilt gebruiken om te controleren wie u bent nadat u zich hebt aanmeldt bij uw werk- of schoolaccount. U kunt de volgende selecties maken:

    | Contactmethode | Beschrijving |
    | --- | --- |
    | Mobiele app | <ul><li>**Ontvang meldingen ter verificatie.** Met deze optie wordt een melding naar de authenticator-app op uw smartphone of tablet gepusht. Bekijk de melding en selecteer, als deze legitiem is, **Authenticeren** in de app. Uw werk of school kan vereisen dat u een pincode invoert voordat u zich verifieert.</li><li>**Gebruik verificatiecode.** In deze modus genereert de authenticator-app een verificatiecode die elke 30 seconden wordt bijgewerkt. Voer de meest recente verificatiecode in het aanmeldingsscherm in.<br>De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594) en [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Verificatietelefoon | <ul><li>**Bij een telefoongesprek** wordt een geautomatiseerd telefoongesprek naar het door u opteerde telefoonnummer gestuurd. Beantwoord de oproep en druk op de pound-toets (#) op het telefoontoetsenbord om te verifiëren.</li><li>**Sms eindigt** een sms-bericht met een verificatiecode. Als u de prompt in de tekst volgt, beantwoordt u het sms-bericht of voert u de verificatiecode in die in de aanmeldingsinterface wordt verstrekt.</li></ul> |
    | Zakelijke telefoon | Hiermee plaatst u een geautomatiseerd telefoongesprek naar het telefoonnummer dat u opgeeft. Beantwoord de oproep en druk op de pound-toets (#) op het telefoontoetsenbord om te verifiëren. |

## <a name="next-steps"></a>Volgende stappen

Nadat u de pagina **Extra beveiligingsverificatie** hebt geopend, moet u de methode voor tweestapsverificatie selecteren en instellen:

- [Uw mobiele apparaat instellen als verificatiemethode](multi-factor-authentication-setup-phone-number.md)

- [Uw kantoortelefoon instellen als verificatiemethode](multi-factor-authentication-setup-office-phone.md)

- [De Microsoft Authenticator-app instellen als verificatiemethode](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Gerelateerde resources

- [Uw tweestapsverificatiemethode-instellingen beheren](multi-factor-authentication-end-user-manage-settings.md)

- [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)

- [Aanmelden met tweestapsverificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij tweestapsverificatie](multi-factor-authentication-end-user-troubleshoot.md) 
