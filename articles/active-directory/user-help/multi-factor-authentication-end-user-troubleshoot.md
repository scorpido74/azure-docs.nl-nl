---
title: Veelvoorkomende problemen met account twee ledige verificatie-Azure AD
description: Oplossingen voor een aantal veelvoorkomende problemen met twee ledige verificaties en uw werk-of school account.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 8d2e34bcfd180dfeb814dace2a496f3ac593c5bf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738605"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Veelvoorkomende problemen met twee ledige verificatie en uw werk-of school account

Wanneer uw Azure Active Directory-organisatie (Azure AD) de twee ledige verificatie inschakelt, moet uw werk-of school account een combi natie hebben van uw gebruikers naam, uw wacht woord en een mobiel apparaat of telefoon nummer. Het is veiliger dan alleen een wacht woord, afhankelijk van twee vormen van verificatie: iets wat u kent en wat u met u hebt. Verificatie op basis van twee factoren kan helpen voor komen dat kwaadwillende hackers zich voordoen, want zelfs als ze uw wacht woord hebben, zijn de conflicteert dat ze niet uw apparaat hebben.

<center>

![Afbeelding van de conceptuele verificatie methoden](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Er zijn enkele veelvoorkomende problemen met twee ledige verificatie die vaak vaker voor komen dan wat ons zou willen. We hebben dit artikel gecombineerd om de meest voorkomende problemen op te lossen en enkele mogelijke oplossingen.

>[!Important]
>Als u een beheerder bent, kunt u meer informatie vinden over het instellen en beheren van uw Azure AD-omgeving in de [Azure AD-documentatie](https://docs.microsoft.com/azure/active-directory).
>
>Deze inhoud is ook alleen bedoeld voor gebruik met uw werk-of school account. Dit is het account dat u hebt ontvangen van uw organisatie (bijvoorbeeld alain@contoso.com ). Als u problemen ondervindt met twee ledige verificatie en uw persoonlijke Microsoft-account, wat een account is dat u zelf hebt ingesteld (bijvoorbeeld danielle@outlook.com ), raadpleegt u [twee ledige verificatie inschakelen of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Ik heb mijn mobiele apparaat niet met mij

Dit gebeurt. U hebt uw mobiele apparaat thuis verlaten en nu kunt u uw telefoon niet gebruiken om te controleren wie u bent. Als u eerder een andere methode hebt toegevoegd om u aan te melden bij uw account, zoals uw zakelijke telefoon, kunt u deze methode nu gebruiken. Als u nog nooit een extra verificatie methode hebt toegevoegd, moet u contact opnemen met de Help Desk van uw organisatie en deze gebruiken om terug te gaan naar uw account.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Aanmelden bij uw werk-of school account met een andere verificatie methode

1. Meld u aan bij uw account, maar selecteer de koppeling **aanmelden op een andere manier** op de pagina **twee ledige verificatie** .

    ![Verificatie methode voor aanmelding wijzigen](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Als u de koppeling **aanmelden op een andere manier** niet ziet, betekent dit dat u geen andere verificatie methoden hebt ingesteld. Neem contact op met uw beheerder voor hulp bij het aanmelden bij uw account.

2. Kies uw alternatieve verificatie methode en ga door met het verificatie proces met twee factoren.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Ik ben mijn mobiele apparaat kwijt geraakt of het is gestolen

Als u het mobiele apparaat kwijtraakt of als u het hebt gestolen, kunt u zich aanmelden met een andere methode of u kunt de Help Desk van uw organisatie vragen uw instellingen te wissen. We raden u ten zeerste aan de Help Desk van uw organisatie te laten weten of uw telefoon is kwijt geraakt of gestolen. Daarom kunnen de juiste updates worden aangebracht in uw account. Nadat uw instellingen zijn gewist, wordt u gevraagd om u te [registreren voor twee ledige verificatie](multi-factor-authentication-end-user-first-time.md) bij de volgende keer dat u zich aanmeldt.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Ik krijg de verificatie code niet die naar mijn mobiele apparaat wordt verzonden

Het niet ophalen van uw verificatie code is een veelvoorkomend probleem en is doorgaans gerelateerd aan uw mobiele apparaat en de bijbehorende instellingen. Enkele mogelijke manieren om te proberen:

Probeer dit | Informatie over begeleiding
--------- | ------------
Uw mobiele apparaat opnieuw opstarten | Soms moet uw apparaat gewoon worden vernieuwd. Als uw apparaat opnieuw wordt opgestart, worden alle achtergrond processen of services die momenteel worden uitgevoerd, beëindigd en kunnen er problemen ontstaan, samen met het vernieuwen van de kern onderdelen van uw apparaat, het opnieuw opstarten als ze op een bepaald moment zijn vastgelopen.
Controleer of uw beveiligings gegevens juist zijn | Zorg ervoor dat de gegevens van de beveiligings verificatie methode nauw keurig zijn, met name uw telefoon nummers. Als u het verkeerde telefoon nummer plaatst, wordt al uw waarschuwingen naar een onjuist nummer gegaan. Gelukkig kan deze gebruiker niets doen met de waarschuwingen, maar ook niet om u aan te melden bij uw account. Zie de instructies in het artikel [uw twee ledige verificatie methode-instellingen beheren](multi-factor-authentication-end-user-manage-settings.md) om ervoor te zorgen dat uw gegevens correct zijn.
Controleren of uw meldingen zijn ingeschakeld | Zorg ervoor dat op uw mobiele apparaat meldingen zijn ingeschakeld en dat u een meldings methode hebt geselecteerd waarmee telefoon gesprekken, uw verificatie-app en uw Messa ging-app (voor SMS-berichten) zicht bare waarschuwings meldingen naar uw mobiele apparaat kunnen verzenden.
Zorg ervoor dat u een signaal voor een apparaat en een Internet verbinding hebt | Zorg ervoor dat uw telefoon gesprekken en SMS-berichten op uw mobiele apparaat terechtkomen. Laat een vriend u bellen en u een SMS-bericht sturen om er zeker van te zijn dat u beide ontvangt. Als u dit niet doet, controleert u eerst of het mobiele apparaat is ingeschakeld. Als uw apparaat is ingeschakeld, maar u nog steeds geen gesprek of tekst krijgt, is het waarschijnlijk een probleem met uw netwerk en moet u contact opnemen met uw provider. Als u vaak signaal problemen ondervindt, raden we u aan om de [Microsoft Authenticator-app](user-help-auth-app-download-install.md) op uw mobiele apparaat te installeren en te gebruiken. De verificator-app kan wille keurige beveiligings codes genereren voor aanmelden, zonder dat er een celpictogram of Internet verbinding vereist is.
Niet storen uitschakelen | Zorg ervoor dat u de functie **niet Stores** voor uw mobiele apparaat niet hebt ingeschakeld. Als deze functie is ingeschakeld, kunnen meldingen niet worden gewaarschuwd op uw mobiele apparaat. Raadpleeg de hand leiding van uw mobiele apparaat voor instructies over het uitschakelen van deze functie.
Telefoon nummers blok keren | In de Verenigde Staten zijn telefoon aanroepen van micro soft afkomstig van de volgende nummers: + 1 (866) 539 4191, + 1 (855) 330 8653 en + 1 (877) 668 6536.
Controleer de accu-gerelateerde instellingen | Dit lijkt een beetje oneven op het Opper vlak, maar als u uw batterij optimalisatie hebt ingesteld om te voor komen dat toepassingen die minder worden gebruikt, op de achtergrond actief blijven, heeft het meest waarschijnlijk invloed op uw meldings systeem. Als u wilt proberen dit probleem op te lossen, schakelt u de batterij optimalisatie voor uw verificatie-app en uw Messa ging-app uit en probeert u zich opnieuw aan te melden bij uw account.
Beveiligings-apps van derden uitschakelen | Als u een app hebt die tekst berichten of telefoon gesprekken beveiligt om onbekende bellers te minimaliseren, kan de verificatie code niet worden ontvangen. Probeer alle beveiligings-apps van derden uit te scha kelen op uw telefoon en vraag vervolgens een andere verificatie code te verzenden.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Ik krijg geen melding meer over mijn tweede verificatie gegevens

Als u zich hebt aangemeld bij uw werk-of school account met behulp van uw gebruikers naam en wacht woord, maar u nog geen informatie over uw aanvullende beveiligings verificatie hebt opgegeven, kunt u uw apparaat mogelijk nog niet instellen. Uw mobiele apparaat moet worden ingesteld om te werken met uw specifieke aanvullende beveiligings verificatie methode. Om ervoor te zorgen dat u uw mobiele apparaat hebt ingeschakeld en dat het beschikbaar is voor gebruik met uw verificatie methode, raadpleegt u het artikel [uw instellingen voor twee ledige verificatie methode beheren](multi-factor-authentication-end-user-manage-settings.md) . Als u weet dat u uw apparaat of uw account nog niet hebt ingesteld, kunt u dit nu doen door de stappen in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) te volgen.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Ik heb een nieuw telefoon nummer en ik wil het toevoegen

Als u een nieuw telefoon nummer hebt, moet u de details van de beveiligings verificatie methode bijwerken zodat de verificatie vraagt om naar de juiste locatie. Volg de stappen in het gedeelte **uw telefoon nummer toevoegen of wijzigen** in het artikel uw [instellingen voor twee ledige verificatie methode beheren](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) om uw verificatie methode bij te werken.

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Ik heb een nieuw mobiel apparaat gekregen dat ik wil toevoegen

Als u een nieuw mobiel apparaat hebt, moet u dit instellen op het gebruik van twee ledige verificatie. Dit is een oplossing met meerdere stappen:

1. Stel uw apparaat in op samen werking met uw werk-of school account door de stappen in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) te volgen.

1. Werk uw account en apparaatgegevens bij op de **aanvullende beveiligings verificatie** pagina en verwijder het oude apparaat en voeg uw nieuwe toe. Zie het artikel [uw instellingen voor de verificatie methode van twee factoren beheren](multi-factor-authentication-end-user-manage-settings.md) voor meer informatie.

Optionele stappen:

- U kunt de app Microsoft Authenticator downloaden, installeren en instellen op uw mobiele apparaat door de stappen te volgen in het artikel [Microsoft Authenticator app downloaden en installeren](user-help-auth-app-download-install.md) .

- Schakel twee ledige verificatie in voor uw vertrouwde apparaten door de stappen in de sectie met **twee ledige verificatie vragen op een vertrouwd apparaat** in te scha kelen in het artikel [uw instellingen voor twee ledige verificatie methode beheren](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Ik ondervind problemen bij het aanmelden op mijn mobiele apparaat tijdens het reizen

Mogelijk vindt u het moeilijker om een verificatie methode met een mobiel apparaat te gebruiken, zoals een SMS-bericht, terwijl u zich op een internationale locatie bevindt. Het is ook mogelijk dat uw mobiele apparaat u de kosten voor roaming kan veroorzaken. Voor deze situatie raden we u aan om de Microsoft Authenticator-app te gebruiken, met de optie om verbinding te maken met een Wi-Fi-hotspot. Zie het artikel [Microsoft Authenticator app downloaden en installeren](user-help-auth-app-download-install.md) voor meer informatie over het downloaden, installeren en instellen van de Microsoft Authenticator-app op uw mobiele apparaat.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Ik kan mijn app-wacht woorden niet gebruiken

App-wacht woorden vervangen uw normale wacht woord voor oudere desktop toepassingen die geen ondersteuning bieden voor twee ledige verificatie. Controleer eerst of u het wacht woord juist hebt getypt. Als dat niet het geval is, maakt u een nieuw app-wacht woord voor de app door de stappen in de sectie **app-wacht woorden maken en verwijderen te volgen met behulp van de portal mijn apps** in het artikel [app-wacht woorden beheren voor verificatie in twee stappen](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-two-factor-verification-off"></a>Ik kan niet twee ledige verificatie uitschakelen

Als u twee ledige verificatie met uw werk-of school account gebruikt (bijvoorbeeld alain@contoso.com ), betekent dit waarschijnlijk dat uw organisatie heeft besloten deze extra beveiligings functie te gebruiken. Omdat uw organisatie u heeft besloten om deze functie te gebruiken, is het niet mogelijk om deze afzonderlijk in te scha kelen. Als u echter twee ledige verificatie met een persoonlijk account gebruikt, alain@outlook.com hebt u de mogelijkheid om de functie in of uit te scha kelen. Zie voor meer informatie over het beheren van twee ledige verificatie voor uw persoonlijke accounts, [het inschakelen van twee ledige verificatie in-of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Als u twee ledige verificatie niet kunt uitschakelen, kan dit ook worden veroorzaakt door de standaard beveiligings instellingen die zijn toegepast op het niveau van de organisatie. Zie [Wat zijn de standaard beveiligings instellingen?](../fundamentals/concept-fundamentals-security-defaults.md) voor meer informatie over de standaard instellingen van de beveiliging.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Ik heb geen antwoord op mijn probleem gevonden

Als u deze stappen hebt uitgevoerd, maar nog steeds problemen ondervindt, neemt u contact op met de Help Desk van uw organisatie voor hulp.

## <a name="related-articles"></a>Verwante artikelen:

- [De instellingen voor de verificatie methode met twee factoren beheren](multi-factor-authentication-end-user-manage-settings.md)

- [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md)

- [Veelgestelde vragen over Microsoft Authenticator-app](user-help-auth-app-faq.md)
