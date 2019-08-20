---
title: Uw overzicht van twee ledige verificatie methoden instellen-Azure Active Directory | Microsoft Docs
description: Overzicht van het instellen van uw twee ledige verificatie methoden voor twee ledige verificatie.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616176"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>Het overzicht van twee ledige verificatie methoden instellen

Uw organisatie heeft twee ledige verificatie ingeschakeld, wat betekent dat het aanmelden van uw werk-of school account nu een combi natie van uw gebruikers naam, uw wacht woord en een mobiel apparaat of telefoon vereist. Uw organisatie heeft deze extra verificatie ingeschakeld omdat het veiliger is dan alleen een wacht woord, afhankelijk van twee vormen van verificatie: iets wat u kent en wat u met u hebt. Twee ledige verificatie kan helpen voor komen dat kwaadwillende hackers zich voordoen, want zelfs als ze uw wacht woord hebben, zijn conflicteert dat ze niet uw apparaat hebben.

>[!Important]
>Deze inhoud is bedoeld voor gebruikers. Als u een beheerder bent, kunt u meer informatie vinden over hoe u uw Azure Active Directory-omgeving (Azure AD) instelt en beheert in de [documentatie voor Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="who-decides-if-you-use-this-feature"></a>Wie besluit of u deze functie gebruikt?

Afhankelijk van uw accounttype kan uw organisatie besluiten dat u gebruik moet maken van verificatie in twee stappen, maar het is ook mogelijk dat u dat zelf kunt bepalen.

- **Werk- of schoolaccount.** Als u een werk- of schoolaccount gebruikt (zoals alain@contoso.com), is het aan uw organisatie of u verificatie in twee stappen moet gebruiken, samen met de specifieke verificatiemethoden. Omdat uw organisatie u heeft besloten om deze functie te gebruiken, is het niet mogelijk om deze afzonderlijk in te scha kelen.

- **Persoonlijk Microsoft-account.** U kunt ervoor kiezen verificatie in twee stappen in te stellen voor uw persoonlijke Microsoft-accounts (zoals alain@outlook.com). Als u problemen ondervindt met twee ledige verificatie en uw persoonlijke Microsoft-account, raadpleegt u [twee ledige verificatie in-of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Omdat u kiest of u deze functie wilt gebruiken, kunt u deze op elk gewenst moment inschakelen en uitschakelen.

## <a name="access-the-additional-security-verification-page"></a>Toegang tot de extra beveiligings verificatie pagina

Nadat uw organisatie twee ledige verificatie heeft ingeschakeld en ingesteld, wordt u gevraagd om meer informatie op te geven om uw account veilig te houden.

![Vraag meer info vereist](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>De extra beveiligings verificatie pagina openen

1. Selecteer **volgende** bij de vraag **meer informatie vereist** .

    De pagina **aanvullende beveiligings verificatie** wordt weer gegeven.

2. Op de pagina **aanvullende beveiligings verificatie** moet u bepalen welke verificatie methode met twee factoren u wilt gebruiken om te controleren of u bent wie u zegt nadat u zich hebt aangemeld bij uw werk-of school account. U kunt het volgende selecteren:

    | Contactmethode | Description |
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
