---
title: Problemen met het configureren van wachtwoord-SSO voor Azure AD Gallery-app
description: Meer informatie over de veelvoorkomende problemen bij het configureren van eenmalige aanmelding met een wacht woord voor toepassingen die al zijn opgenomen in de Azure AD-toepassings galerie
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96e729bee0ac9beac447893ed8dbf1bba633e412
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274183"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Probleem met het configureren van een eenmalige aanmelding met een wacht woord voor een toepassing in de Azure AD-galerie

Dit artikel helpt u inzicht te krijgen in de veelvoorkomende problemen bij het configureren van **eenmalige aanmelding met een wacht woord** met een Azure AD Gallery-toepassing.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Referenties zijn ingevuld, maar de extensie verzendt deze niet

Dit probleem treedt doorgaans op als de leverancier van de toepassing onlangs een veld heeft gewijzigd, een id heeft gewijzigd die wordt gebruikt voor het detecteren van de gebruikers naam en het wacht woord, of het wijzigen van de manier waarop de aanmeldings ervaring werkt voor de toepassing. Gelukkig kan micro soft in veel gevallen samen werken met leveranciers van toepassingen om deze problemen snel op te lossen.

Micro soft heeft technologieën om automatisch te detecteren wanneer integraties zijn verbroken. het is mogelijk dat u de problemen niet meteen kunt vinden of dat de problemen enige tijd duren om op te lossen. Als een van deze integraties niet correct werkt, opent u een ondersteunings aanvraag zodat deze zo snel mogelijk kan worden opgelost.

**Als u contact opneemt met de leverancier van deze toepassing, kunt u** ze onze manier sturen zodat micro soft ermee kan samen werken om hun toepassing systeem eigen te integreren met Azure Active Directory. U kunt de leverancier verzenden naar de [vermelding uw toepassing in de galerie met Azure Active Directory toepassingen](../develop/howto-app-gallery-listing.md) om ze te starten.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Referenties worden ingevuld en verzonden, maar de pagina geeft aan dat de referenties onjuist zijn

Voer de volgende stappen uit om dit probleem op te lossen:

- Laat de gebruiker eerst proberen **zich rechtstreeks aan te melden bij de toepassings website** met de referenties die voor hen zijn opgeslagen.

  * Als de gebruiker zich aanmeldt, klikt u op de knop **referenties bijwerken** op de **tegel toepassing** in de sectie **apps** van het [toegangs venster](https://myapps.microsoft.com/) voor de toepassing om ze bij te werken naar de meest recente werkende gebruikers naam en het nieuwste wacht woord.

  * Als u of een andere beheerder de referenties voor deze gebruiker heeft toegewezen, zoekt u de toepassings toewijzing van de gebruiker of groep door te navigeren naar het tabblad **gebruikers & groepen** van de toepassing, de toewijzing te selecteren en op de knop **referenties bijwerken** te klikken.

- Als de gebruiker zijn of haar eigen referenties heeft toegewezen, moet de gebruiker controleren of het **wacht woord niet is verlopen in de toepassing** . als dat het geval is, werkt u het **verlopen wacht woord** bij door u rechtstreeks aan te melden bij de toepassing.

  * Wanneer het wacht woord in de toepassing is bijgewerkt, vraagt u de gebruiker om te klikken op de knop **referenties bijwerken** op de **tegel toepassing** in de sectie **apps** van het [toegangs venster](https://myapps.microsoft.com/) voor de toepassing om ze bij te werken naar de meest recente werkende gebruikers naam en het nieuwste wacht woord.

  * Als u of een andere beheerder de referenties voor deze gebruiker heeft toegewezen, zoekt u de toepassings toewijzing van de gebruiker of groep door te navigeren naar het tabblad **gebruikers & groepen** van de toepassing, de toewijzing te selecteren en op de knop **referenties bijwerken** te klikken.

- Laat de gebruiker de browser uitbreiding van het toegangs venster bijwerken met behulp van de volgende stappen in de sectie [de browser uitbreiding van het toegangs venster installeren](#how-to-install-the-access-panel-browser-extension) .

- Zorg ervoor dat de browser uitbreiding van het toegangs venster wordt uitgevoerd en is ingeschakeld in de browser van uw gebruiker.

- Zorg ervoor dat uw gebruikers zich niet bij de toepassing aanmelden vanuit het toegangs venster in **incognito, InPrivate of private**. De uitbrei ding van het toegangs paneel wordt niet ondersteund in deze modus.

Als de vorige suggesties niet werken, is het mogelijk dat er een wijziging is opgetreden aan de kant van de toepassing die de integratie van de toepassing met Azure AD tijdelijk heeft verbroken. Dit kan bijvoorbeeld gebeuren wanneer de leverancier van de toepassing een script op de pagina maakt dat anders werkt voor hand matig en geautomatiseerd invoeren, waardoor automatische integratie, zoals onze eigen, kan worden onderbroken. Gelukkig kan micro soft in veel gevallen samen werken met leveranciers van toepassingen om deze problemen snel op te lossen.

Micro soft heeft technologieën om automatisch te detecteren wanneer een toepassings integratie is verbroken. het is mogelijk dat u de problemen niet meteen kunt vinden of dat de problemen enige tijd in beslag nemen. Wanneer een integratie niet correct werkt, kunt u een ondersteunings aanvraag openen om deze zo snel mogelijk vast te krijgen. 

Daarnaast kunt **u, als u in contact komt met de leverancier van deze toepassing,** **hen onze manier sturen** zodat we met hen kunnen samen werken om hun toepassing systeem eigen te integreren met Azure Active Directory. U kunt de leverancier verzenden naar de [vermelding uw toepassing in de galerie met Azure Active Directory toepassingen](../develop/howto-app-gallery-listing.md) om ze te starten.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>De uitbrei ding werkt in Chrome en Firefox, maar niet in Internet Explorer

Er zijn twee hoofd oorzaken voor dit probleem:

- Afhankelijk van de beveiligings instellingen die zijn ingeschakeld in Internet Explorer, wordt het uitvoeren van de uitvoering van het script voor de toepassing soms geblokkeerd als de website geen deel uitmaakt van een **vertrouwde zone**.

  *  Om dit probleem op te lossen, geeft u de gebruiker de **website van de toepassing toe te voegen** aan de lijst met **vertrouwde sites** in de **beveiligings instellingen van Internet Explorer**. U kunt uw gebruikers verzenden naar het artikel [een site toevoegen aan mijn lijst met vertrouwde sites](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) voor gedetailleerde instructies.

- In zeldzame omstandigheden kan de beveiligings validatie van Internet Explorer soms trager worden geladen dan het uitvoeren van het script.

  * Helaas kan deze situatie variëren, afhankelijk van de browser versie, de computer snelheid of de bezochte site. In dit geval raden we u aan om contact op te nemen met de ondersteuning, zodat we de integratie voor deze specifieke toepassing kunnen oplossen.

Daarnaast kunt **u, als u in contact komt met de leverancier van deze toepassing,** **hen onze manier sturen** zodat we met hen kunnen samen werken om hun toepassing systeem eigen te integreren met Azure Active Directory. U kunt de leverancier verzenden naar de [vermelding uw toepassing in de galerie met Azure Active Directory toepassingen](../develop/howto-app-gallery-listing.md) om ze te starten.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Controleren of de aanmeldings pagina van de toepassing onlangs is gewijzigd of een extra veld vereist

Als de aanmeldings pagina van de toepassing drastisch is gewijzigd, is het mogelijk dat onze integraties worden verbroken. Een voor beeld hiervan is wanneer een toepassings leverancier een aanmeld veld, een CAPTCHA of multi-factor Authentication toevoegt aan hun ervaringen. Gelukkig kan micro soft in veel gevallen samen werken met leveranciers van toepassingen om deze problemen snel op te lossen.

Micro soft heeft technologieën om automatisch te detecteren wanneer een toepassings integratie is verbroken. het is mogelijk dat u de problemen niet meteen kunt vinden of dat de problemen enige tijd in beslag nemen. Wanneer een integratie niet correct werkt, kunt u een ondersteunings aanvraag openen om deze zo snel mogelijk vast te krijgen. 

Daarnaast kunt **u, als u in contact komt met de leverancier van deze toepassing,** **hen onze manier sturen** zodat we met hen kunnen samen werken om hun toepassing systeem eigen te integreren met Azure Active Directory. U kunt de leverancier verzenden naar de [vermelding uw toepassing in de galerie met Azure Active Directory toepassingen](../develop/howto-app-gallery-listing.md) om ze te starten.

## <a name="how-to-install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Volg de onderstaande stappen om de browser uitbreiding van het toegangs venster te installeren:

1.  Open het [toegangs venster](https://myapps.microsoft.com) in een van de ondersteunde browsers en meld u aan als een **gebruiker** in uw Azure AD.

2.  Klik op een **wacht woord-SSO-toepassing** in het toegangs venster.

3.  Selecteer **nu installeren**bij de vraag of u de software wilt installeren.

4.  Op basis van uw browser wordt u omgeleid naar de download koppeling. **Voeg** de uitbrei ding toe aan uw browser.

5.  Als uw browser vraagt, selecteert u de extensie **inschakelen** of **toestaan** .

6.  Nadat de installatie is voltooid, start u de browser sessie **opnieuw** .

7.  Meld u aan bij het toegangs venster en controleer of u uw wacht woord-SSO-toepassingen kunt **starten** .

U kunt de uitbrei ding voor Chrome en Firefox ook downloaden via de onderstaande rechtstreekse koppelingen:

-   [Uitbrei ding Chrome toegangs paneel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensie voor het toegangs paneel van Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Volgende stappen
[Geef single sign-on bij uw apps met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

