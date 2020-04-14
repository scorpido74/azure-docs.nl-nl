---
title: Veelvoorkomende problemen met accounttweefactorverificatie - Azure AD
description: Oplossingen voor enkele van de meest voorkomende twee-factor verificatie problemen en uw werk of school account.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 3df79fb1e103ddfaafeb348641e675cd250f5858
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271689"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Veelvoorkomende problemen met tweefactorverificatie en uw werk- of schoolaccount

Wanneer uw Azure Active Directory-organisatie (Azure AD) tweestapsverificatie inschakelt, vereist uw aanmelding voor werk- of schoolaccounts een combinatie van uw gebruikersnaam, uw wachtwoord en een mobiel apparaat of telefoon. Het is veiliger dan alleen een wachtwoord, vertrouwend op twee vormen van authenticatie: iets wat je weet, en iets wat je bij je hebt. Twee-factor verificatie kan helpen om te voorkomen dat kwaadwillende hackers zich voordoen als u, want zelfs als ze uw wachtwoord hebben, de kans groot dat ze niet uw apparaat hebben, ook.

<center>

![Afbeelding van conceptverificatiemethoden](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Er zijn een aantal gemeenschappelijke twee-factor verificatie problemen die lijken te gebeuren vaker dan ieder van ons zou willen. We hebben dit artikel samengesteld om de meest voorkomende problemen en een aantal mogelijke oplossingen aan te pakken.

>[!Important]
>Als u een beheerder bent, vindt u meer informatie over het instellen en beheren van uw Azure AD-omgeving in de [Azure AD-documentatie.](https://docs.microsoft.com/azure/active-directory)
>
>Deze inhoud is ook alleen bedoeld voor gebruik met uw werk- of schoolaccount, het alain@contoso.comaccount dat uw organisatie (bijvoorbeeld) aan u verstrekt. Als u problemen ondervindt met tweestapsverificatie en uw persoonlijke Microsoft-account, een account danielle@outlook.comdat u voor uzelf hebt ingesteld (bijvoorbeeld), raadpleegt u [Tweestapsverificatie in- of uitschakelen voor uw Microsoft-account.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

## <a name="i-dont-have-my-mobile-device-with-me"></a>Ik heb mijn mobiele apparaat niet bij me

Het gebeurt. Je hebt je mobiele apparaat thuis gelaten en nu kun je je telefoon niet gebruiken om te verifiëren wie je bent. Als u eerder een andere methode hebt toegevoegd om u aan te melden bij uw account, zoals uw kantoortelefoon, moet u die methode nu kunnen gebruiken. Als u nooit een extra verificatiemethode hebt toegevoegd, moet u contact opnemen met de helpdesk van uw organisatie en deze laten helpen om weer in uw account te komen.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>U zich aanmelden bij uw werk- of schoolaccount met een andere verificatiemethode

1. Meld u aan bij uw account, maar selecteer de koppeling Aanmelden op **een andere manier** op de pagina **Tweestapsverificatie.**

    ![De verificatiemethode voor aanmelding wijzigen](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Als u de koppeling Aanmelden op **een andere manier** niet ziet, betekent dit dat u geen andere verificatiemethoden hebt ingesteld. Je moet contact opnemen met je beheerder voor hulp bij het aanmelden bij je account.

2. Kies uw alternatieve verificatiemethode en ga verder met het verificatieproces met twee factoren.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Ik verloor mijn mobiele apparaat of het werd gestolen

Als u uw mobiele apparaat hebt verloren of laten stelen, u zich met een andere methode aanmelden of de helpdesk van uw organisatie vragen om uw instellingen te wissen. We raden je ten zeerste aan om de helpdesk van je organisatie te laten weten of je telefoon is verloren of gestolen, zodat de juiste updates kunnen worden uitgevoerd in je account. Nadat uw instellingen zijn gewist, wordt u gevraagd zich te [registreren voor tweestapsverificatie](multi-factor-authentication-end-user-first-time.md) wanneer u zich de volgende keer aanmeldt.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Ik krijg de verificatiecode niet naar mijn mobiele apparaat gestuurd

Het niet verkrijgen van uw verificatiecode is een veel voorkomend probleem en het is meestal gerelateerd aan uw mobiele apparaat en de instellingen ervan. Enkele mogelijke dingen om te proberen:

Probeer dit eens | Richtlijnen
--------- | ------------
Uw mobiele apparaat opnieuw opstarten | Soms moet uw apparaat gewoon worden vernieuwd. Als u uw apparaat opnieuw start, wordt een einde gemaakt aan achtergrondprocessen of services die momenteel worden uitgevoerd en die problemen kunnen veroorzaken, samen met het vernieuwen van de kerncomponenten van uw apparaat, het opnieuw opstarten van uw apparaat in het geval ze op een bepaald moment zijn gecrasht.
Controleren of uw beveiligingsgegevens correct zijn | Zorg ervoor dat de gegevens van uw beveiligingsverificatiemethode juist zijn, vooral uw telefoonnummers. Als u het verkeerde telefoonnummer invoert, gaan al uw waarschuwingen naar dat onjuiste nummer. Gelukkig kan die gebruiker niets met de waarschuwingen doen, maar het helpt je ook niet om je aan te melden bij je account. Zie de instructies in het artikel Instellingen [voor verificatiemethode](multi-factor-authentication-end-user-manage-settings.md) voor twee stappen beheren om ervoor te zorgen dat uw gegevens correct zijn.
Controleer of uw meldingen zijn ingeschakeld | Zorg ervoor dat op uw mobiele apparaat meldingen zijn ingeschakeld en of u een meldingsmethode hebt geselecteerd waarmee telefoongesprekken, uw verificatie-app en uw berichten-app (voor sms-berichten) zichtbare waarschuwingsmeldingen naar uw mobiele apparaat kunnen verzenden.
Zorg ervoor dat u een apparaatsignaal en internetverbinding hebt | Zorg ervoor dat uw telefoongesprekken en sms-berichten worden doorgedrongen tot uw mobiele apparaat. Laat een vriend je bellen en stuur je een sms om er zeker van te zijn dat je beide ontvangt. Als u dit niet doet, controleert u eerst of uw mobiele apparaat is ingeschakeld. Als uw apparaat is ingeschakeld, maar u nog steeds niet wordt gebeld of gebeld, is dit waarschijnlijk een probleem met uw netwerk en moet u met uw provider praten. Als u vaak problemen hebt met signalen, raden we u aan de [Microsoft Authenticator-app](user-help-auth-app-download-install.md) op uw mobiele apparaat te installeren en te gebruiken. De authenticator-app kan willekeurige beveiligingscodes genereren voor aanmelding, zonder dat er een mobiele signaal of internetverbinding nodig is.
Niet storen uitschakelen | Zorg ervoor dat u de functie **Niet storen voor** uw mobiele apparaat niet hebt ingeschakeld. Wanneer deze functie is ingeschakeld, mogen meldingen u niet waarschuwen op uw mobiele apparaat. Raadpleeg de handleiding van uw mobiele apparaat voor instructies over het uitschakelen van deze functie.
Telefoonnummers deblokkeren | In de Verenigde Staten komen spraakoproepen van Microsoft uit de volgende nummers: +1 (866) 539 4191, +1 (855) 330 8653 en +1 (877) 668 6536.
Uw instellingen voor de batterij controleren | Deze lijkt een beetje vreemd op het oppervlak, maar als je hebt ingesteld uw batterij optimalisatie om minder gebruikte apps te stoppen van actief blijven op de achtergrond, uw melding systeem is waarschijnlijk getroffen. Als u wilt proberen dit probleem op te lossen, schakelt u batterijoptimalisatie voor uw verificatie-app en uw berichten-app uit en probeert u zich opnieuw aan te melden bij uw account.
Beveiligingsapps van derden uitschakelen | Als u een app hebt die sms-berichten of telefoongesprekken beschermt om onbekende bellers tot een minimum te beperken, kan deze voorkomen dat de verificatiecode wordt ontvangen. Probeer beveiligingsapps van derden op uw telefoon uit te schakelen en vraag vervolgens om een andere verificatiecode te sturen.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Ik word niet gevraagd om mijn tweede verificatiegegevens

Als u zich hebt aangemeld bij uw werk- of schoolaccount met uw gebruikersnaam en wachtwoord, maar u niet bent gevraagd naar uw aanvullende beveiligingsverificatiegegevens, hebt u uw apparaat misschien nog niet ingesteld. Uw mobiele apparaat moet zijn ingesteld om te werken met uw specifieke aanvullende beveiligingsverificatiemethode. Zie het artikel Instellingen [voor verificatiemethode beheren](multi-factor-authentication-end-user-manage-settings.md) om ervoor te zorgen dat u uw mobiele apparaat hebt ingeschakeld en dat het beschikbaar is om te gebruiken met uw verificatiemethode. Als u weet dat u uw apparaat of uw account niet hebt ingesteld, u dit nu doen door de stappen in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) te volgen.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Ik heb een nieuw telefoonnummer en ik wil het toevoegen

Als u een nieuw telefoonnummer hebt ontvangen, moet u de gegevens van uw beveiligingsverificatiemethode bijwerken, zodat uw verificatieprompts naar de juiste locatie gaan. Als u uw verificatiemethode wilt bijwerken, voert u de stappen in het gedeelte **Uw telefoonnummer toevoegen of wijzigen** van het artikel Instellingen voor verificatie methode voor twee stappen [beheren.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Ik heb een nieuw mobiel apparaat en ik wil het toevoegen

Als u een nieuw mobiel apparaat hebt gekregen, moet u het instellen om te werken met tweestapsverificatie. Dit is een oplossing in meerdere stappen:

1. Stel uw apparaat in om met uw werk- of schoolaccount te werken door de stappen in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) te volgen.

1. Werk uw account- en apparaatgegevens bij op de pagina **Extra beveiligingsverificatie,** waarbij uw oude apparaat wordt verwijderd en uw nieuwe apparaat wordt toegevoegd. Zie het artikel [Instellingen voor verificatiemethode voor twee stappen beheren](multi-factor-authentication-end-user-manage-settings.md) voor meer informatie.

Optionele stappen:

- Download, installeer en stel de Microsoft Authenticator-app in op uw mobiele apparaat door de stappen in het artikel [Microsoft Authenticator-app downloaden en installeren.](user-help-auth-app-download-install.md)

- Schakel tweestapsverificatie in voor uw vertrouwde apparaten door de stappen te volgen in de **tweestapverificatieprompts inschakelen op een vertrouwd apparaatgedeelte** van het artikel Instellingen [voor tweefactorverificatiemethode beheren.](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Ik heb problemen met aanmelden op mijn mobiele apparaat tijdens het reizen

Het is mogelijk dat u een verificatiemethode voor mobiele apparaten, zoals een sms-bericht, gebruikt terwijl u zich op een internationale locatie bevindt. Het is ook mogelijk dat uw mobiele apparaat ervoor kan zorgen dat u roamingkosten maakt. Voor deze situatie raden we u aan de Microsoft Authenticator-app te gebruiken, met de optie om verbinding te maken met een Wi-Fi-hotspot. Zie het artikel over het downloaden, installeren en instellen van de Microsoft Authenticator-app op uw mobiele apparaat voor meer informatie over het downloaden, installeren en instellen van de Microsoft [Authenticator-app.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Ik kan mijn app-wachtwoorden niet laten werken

App-wachtwoorden vervangen uw normale wachtwoord voor oudere bureaubladtoepassingen die geen tweestapsverificatie ondersteunen. Zorg er eerst voor dat u het wachtwoord correct hebt getypt. Als dit niet wordt opgelost, probeert u een nieuw app-wachtwoord voor de app te maken door de stappen te volgen in het **gedeelte App-wachtwoorden maken en verwijderen met behulp van de sectie Mijn apps-portal** van het artikel [App-wachtwoorden beheren voor verificatiein twee stappen.](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

## <a name="i-cant-turn-two-factor-verification-off"></a>Ik kan tweefactorverificatie niet uitschakelen

Als u tweestapsverificatie gebruikt met uw werk- of alain@contoso.comschoolaccount (bijvoorbeeld), betekent dit waarschijnlijk dat uw organisatie heeft besloten dat u deze toegevoegde beveiligingsfunctie moet gebruiken. Omdat uw organisatie heeft besloten dat u deze functie moet gebruiken, is er geen manier voor u om deze afzonderlijk uit te schakelen. Als u echter tweestapsverificatie gebruikt met een persoonlijk alain@outlook.comaccount, zoals, hebt u de mogelijkheid om de functie in- en uit te schakelen. Zie [Tweestapsverificatie in- of uitschakelen voor uw Microsoft-account voor](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)instructies over het beheren van tweestapsverificatie voor uw persoonlijke accounts.

Als u tweestapsverificatie niet uitschakelen, kan dit ook komen door de beveiligingsstandaards die op organisatieniveau zijn toegepast. Zie [Wat zijn beveiligingsdefulta](../fundamentals/concept-fundamentals-security-defaults.md) voor meer informatie over beveiligingsstandaards?

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Ik heb geen antwoord gevonden op mijn probleem

Als u deze stappen hebt geprobeerd, maar nog steeds problemen ondervindt, neemt u contact op met de helpdesk van uw organisatie voor hulp.

## <a name="related-articles"></a>Verwante artikelen:

- [Uw tweestapsverificatiemethode-instellingen beheren](multi-factor-authentication-end-user-manage-settings.md)

- [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md)

- [Veelgestelde vragen over Microsoft Authenticator-app](user-help-auth-app-faq.md)
