---
title: Wat is de pagina Aanvullende verificatie? -Azure AD
description: Ga naar de extra beveiligings verificatie pagina voor twee ledige verificatie.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062554"
---
# <a name="what-is-the-additional-verification-page"></a>Wat is de pagina Aanvullende verificatie?

U hebt een e-mail ontvangen van iemand op de IT-afdeling of van uw baas met de mededeling dat in de organisatie extra beveiligingsverificatie is toegevoegd aan uw account. Wat houdt dit in? Dit houdt in dat in uw organisatie extra stappen worden ondernomen om uw identiteit te controleren bij het aanmelden. Deze extra verificatie, ook wel bekend als twee ledige verificatie, wordt uitgevoerd via een combi natie van uw gebruikers naam, uw wacht woord en een mobiel apparaat of telefoon nummer.

Verificatie met twee factoren is veiliger dan alleen een wacht woord omdat het afhankelijk is van twee vormen van verificatie: iets wat u kent en wat u met u hebt. Iets dat u weet, is uw wachtwoord. Iets dat u bij u hebt, is een telefoon of apparaat dat u meestal met u meeneemt. Verificatie op basis van twee factoren kan helpen voor komen dat kwaadwillende hackers zich voordoen, want zelfs als ze uw wacht woord hebben, zijn conflicteert dat ze niet uw apparaat hebben.

>[!Important]
>Dit artikel is bedoeld voor gebruikers die proberen twee ledige verificatie met een werk-of school account (zoals alain@contoso.com) te gebruiken. Als u een beheerder bent die op zoek bent naar informatie over het inschakelen van twee ledige verificatie voor uw werk nemers of andere gebruikers, raadpleegt u de [documentatie van Azure Active Directory-verificatie](https://docs.microsoft.com/azure/active-directory/authentication/).

## <a name="who-decides-if-you-use-this-feature"></a>Wie besluit of u deze functie gebruikt?

Afhankelijk van uw accounttype kan uw organisatie besluiten dat u gebruik moet maken van verificatie in twee stappen, maar het is ook mogelijk dat u dat zelf kunt bepalen.

- **Werk- of schoolaccount.** Als u een werk- of schoolaccount gebruikt (zoals alain@contoso.com), is het aan uw organisatie of u verificatie in twee stappen moet gebruiken, samen met de specifieke verificatiemethoden. Omdat uw organisatie u heeft besloten om deze functie te gebruiken, is het niet mogelijk om deze afzonderlijk in te scha kelen.

- **Persoonlijk Microsoft-account.** U kunt ervoor kiezen verificatie in twee stappen in te stellen voor uw persoonlijke Microsoft-accounts (zoals alain@outlook.com). Als u problemen ondervindt met twee ledige verificatie en uw persoonlijke Microsoft-account, raadpleegt u [twee ledige verificatie in-of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Omdat u kiest of u deze functie wilt gebruiken, kunt u deze op elk gewenst moment inschakelen en uitschakelen.

    >[!Note]
    >Als u problemen ondervindt met twee ledige verificatie en een van uw persoonlijke micro soft-accounts (bijvoorbeeld danielle@outlook.com), kunt u de suggesties uitproberen over het [gebruik van verificatie in twee stappen met uw Microsoft-account](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="access-the-additional-security-verification-page"></a>Toegang tot de extra beveiligings verificatie pagina

Nadat uw organisatie twee ledige verificatie heeft ingeschakeld en ingesteld, wordt u gevraagd om meer informatie op te geven om uw account veilig te houden.

![Vraag meer info vereist](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>De extra beveiligings verificatie pagina openen

1. Selecteer **volgende** bij de vraag **meer informatie vereist** .

    De pagina **aanvullende beveiligings verificatie** wordt weer gegeven.

2. Op de pagina **aanvullende beveiligings verificatie** moet u bepalen welke verificatie methode met twee factoren u wilt gebruiken om te controleren of u bent wie u zegt nadat u zich hebt aangemeld bij uw werk-of school account. U kunt het volgende selecteren:

    | Contactmethode | Beschrijving |
    | --- | --- |
    | Mobiele app | <ul><li>**Meldingen ontvangen voor verificatie.** Met deze optie wordt een melding naar de verificator-app op uw smartphone of Tablet gepusht. Bekijk de melding en selecteer **verifiëren** in de app als deze geldig is. Uw werk of school vereist mogelijk dat u een pincode invoert voordat u zich verifieert.</li><li>**Verificatie code gebruiken.** In deze modus genereert de verificator-app een verificatie code die elke 30 seconden wordt bijgewerkt. Voer de meest actuele verificatie code in het aanmeldings scherm in.<br>De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594) en [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefoon voor authenticatie | <ul><li>Met **telefoon oproep** wordt een geautomatiseerd telefoon gesprek met het door u geleverde nummer geplaatst. Beantwoord de oproep en druk op het hekje (#) op het telefoon blok om te verifiëren.</li><li>**SMS-bericht** eindigt een tekst bericht met een verificatie code. Na de prompt in de tekst antwoordt u op het tekst bericht of voert u de verificatie code in die u in de aanmeldings interface hebt opgegeven.</li></ul> |
    | Telefoon (werk) | Hiermee wordt een geautomatiseerd telefoon gesprek met het door u verstrekte nummer geplaatst. Beantwoord de oproep en druk op het hekje (#) op het telefoon blok om te verifiëren. |

## <a name="next-steps"></a>Volgende stappen

Nadat u de pagina **aanvullende beveiligings verificatie** hebt geopend, moet u de twee ledige verificatie methode selecteren en instellen:

- [Stel uw mobiele apparaat in als uw verificatie methode](multi-factor-authentication-setup-phone-number.md)

- [Stel uw zakelijke telefoon in als uw verificatie methode](multi-factor-authentication-setup-office-phone.md)

- [Stel de Microsoft Authenticator-app in als uw verificatie methode](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Gerelateerde resources

- [De instellingen voor de verificatie methode met twee factoren beheren](multi-factor-authentication-end-user-manage-settings.md)

- [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)

- [Aanmelden met twee ledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij twee ledige verificatie](multi-factor-authentication-end-user-troubleshoot.md) 
