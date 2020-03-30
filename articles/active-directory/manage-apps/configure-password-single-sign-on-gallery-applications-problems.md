---
title: Problemen met het configureren van wachtwoord SSO voor Azure AD Gallery-app
description: Inzicht krijgen in de veelvoorkomende problemen waarmee mensen worden geconfronteerd bij het configureren van wachtwoordenkelvoudige aanmelding voor toepassingen die al zijn vermeld in de Azure AD Application Gallery
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
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159043"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Probleem met het configureren van eenmalige aanmelding voor een Azure AD Gallery-toepassing

In dit artikel u inzicht krijgen in de veelvoorkomende problemen waarmee mensen worden geconfronteerd bij het configureren van **een malige aanmelding spassword** met een Azure AD Gallery-toepassing.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Referenties worden ingevuld, maar de extensie verzendt deze niet

Dit probleem treedt meestal op als de toepassingsleverancier onlangs zijn aanmeldingspagina heeft gewijzigd om een veld toe te voegen, een id heeft gewijzigd die wordt gebruikt voor het detecteren van de gebruikersnaam- en wachtwoordvelden of als de aanmeldingservaring voor hun toepassing werkt. Gelukkig kan Microsoft in veel gevallen samenwerken met toepassingsleveranciers om deze problemen snel op te lossen.

Hoewel Microsoft technologieën heeft om automatisch te detecteren wanneer integraties worden afgebroken, is het mogelijk niet mogelijk om de problemen meteen te vinden of nemen de problemen enige tijd in beslag om op te lossen. Wanneer een van deze integraties niet goed werkt, opent u een ondersteuningsaanvraag zodat deze zo snel mogelijk kan worden opgelost.

**Als u contact hebt met de leverancier van deze toepassing,** stuurt u ze op onze manier zodat Microsoft met hen kan samenwerken om hun toepassing native te integreren met Azure Active Directory. U de leverancier naar de vermelding van uw toepassing sturen [in de azure Active Directory-toepassingsgalerie](../azuread-dev/howto-app-gallery-listing.md) om deze aan de slag te krijgen.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Referenties worden ingevuld en ingediend, maar de pagina geeft aan dat de referenties onjuist zijn

Als u dit probleem wilt oplossen, probeert u eerst de volgende dingen:

- Laat de gebruiker eerst **proberen om zich rechtstreeks aan te melden bij de website** van de toepassing met de referenties die voor hen zijn opgeslagen.

  * Als aanmelding werkt, klikt de gebruiker op de knop **Referenties bijwerken** op de **toepassingstegel** in het gedeelte **Apps** van het [toepassingstoegangspaneel](https://myapps.microsoft.com/) om deze bij te werken naar de laatst bekende gebruikersnaam en wachtwoord.

  * Als u of een andere beheerder de referenties voor deze gebruiker hebt toegewezen, zoekt u de toepassingstoewijzing van de gebruiker of groep door te navigeren naar het tabblad **Gebruikers & groepen** van de toepassing, selecteert u de toewijzing en klikt u op de knop Referenties **bijwerken.**

- Als de gebruiker zijn eigen referenties heeft toegewezen, laat de gebruiker **dan controleren of zijn wachtwoord niet is verlopen in de toepassing** en zo ja, werk dan zijn verlopen **wachtwoord** bij door zich rechtstreeks bij de toepassing aan te melden.

  * Nadat het wachtwoord in de toepassing is bijgewerkt, vraagt u de gebruiker om op de knop **Referenties bijwerken** op de **toepassingstegel** in het gedeelte **Apps** van het [toepassingstoegangspaneel](https://myapps.microsoft.com/) te klikken om deze bij te werken naar de laatst bekende gebruikersnaam en wachtwoord.

  * Als u of een andere beheerder de referenties voor deze gebruiker hebt toegewezen, zoekt u de toepassingstoewijzing van de gebruiker of groep door te navigeren naar het tabblad **Gebruikers & groepen** van de toepassing, selecteert u de toewijzing en klikt u op de knop Referenties **bijwerken.**

- Laat de browserextensie van het toegangspaneel de browserextensie toegangspaneel bijwerken door de onderstaande stappen in de extensie [van de toegangspaneelbrowser te installeren.](#how-to-install-the-access-panel-browser-extension)

- Controleer of de browserextensie van het toegangspaneel wordt uitgevoerd en ingeschakeld in de browser van uw gebruiker.

- Controleer of uw gebruikers zich niet proberen aan te melden bij de toepassing in het toegangspaneel terwijl ze in de **incognito-, inPrivate- of privémodus staan.** De extensie van het toegangspaneel wordt niet ondersteund in deze modi.

Als de vorige suggesties niet werken, kan het zijn dat er een wijziging is opgetreden aan de toepassingszijde die de integratie van de toepassing met Azure AD tijdelijk heeft verbroken. Dit kan bijvoorbeeld gebeuren wanneer de leverancier van de toepassing een script op zijn pagina introduceert dat zich anders gedraagt voor handmatige versus geautomatiseerde invoer, waardoor geautomatiseerde integratie, zoals de onze, wordt afgebroken. Gelukkig kan Microsoft in veel gevallen samenwerken met toepassingsleveranciers om deze problemen snel op te lossen.

Hoewel Microsoft technologieën heeft om automatisch te detecteren wanneer toepassingsintegraties worden afgebroken, is het mogelijk niet mogelijk om de problemen meteen te vinden of kan het enige tijd duren voordat de problemen zijn opgelost. Wanneer een integratie niet goed werkt, u een ondersteuningsaanvraag openen om deze zo snel mogelijk te laten repareren. 

Als **u contact hebt met de leverancier van deze toepassing,** stuurt u deze bovendien op onze **manier** zodat we met hen kunnen samenwerken om hun toepassing native te integreren met Azure Active Directory. U de leverancier naar de vermelding van uw toepassing sturen [in de azure Active Directory-toepassingsgalerie](../azuread-dev/howto-app-gallery-listing.md) om deze aan de slag te krijgen.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>De extensie werkt in Chrome en Firefox, maar niet in Internet Explorer

Er zijn twee belangrijke oorzaken aan dit probleem:

- Afhankelijk van de beveiligingsinstellingen die zijn ingeschakeld in Internet Explorer, als de website geen deel uitmaakt van een **vertrouwde zone,** wordt ons script soms geblokkeerd voor de uitvoering van de toepassing.

  *  Als u dit wilt oplossen, instrueert u de gebruiker om **de website van de toepassing toe** te voegen aan de lijst Vertrouwde **sites** in de beveiligingsinstellingen van **Internet Explorer.** U uw gebruikers naar het artikel Hoe u [een site toevoegt aan het artikel](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) van de lijst met vertrouwde sites voor gedetailleerde instructies.

- In zeldzame omstandigheden kan de beveiligingsvalidatie van Internet Explorer er soms toe leiden dat de pagina langzamer wordt geladen dan de uitvoering van ons script.

  * Helaas kan deze situatie variëren afhankelijk van de browserversie, computersnelheid of bezochte site. In dit geval raden we u aan contact op te nemen met ondersteuning, zodat we de integratie voor deze specifieke toepassing kunnen herstellen.

Als **u contact hebt met de leverancier van deze toepassing,** stuurt u deze bovendien op onze **manier** zodat we met hen kunnen samenwerken om hun toepassing native te integreren met Azure Active Directory. U de leverancier naar de vermelding van uw toepassing sturen [in de azure Active Directory-toepassingsgalerie](../azuread-dev/howto-app-gallery-listing.md) om deze aan de slag te krijgen.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Controleren of de aanmeldingspagina van de toepassing onlangs is gewijzigd of een extra veld vereist

Als de inlogpagina van de toepassing drastisch is gewijzigd, zorgt dit er soms voor dat onze integraties worden afgebroken. Een voorbeeld hiervan is wanneer een toepassingsleverancier een aanmeldingsveld, een captcha of multifactorauthenticatie toevoegt aan hun ervaringen. Gelukkig kan Microsoft in veel gevallen samenwerken met toepassingsleveranciers om deze problemen snel op te lossen.

Hoewel Microsoft technologieën heeft om automatisch te detecteren wanneer toepassingsintegraties worden afgebroken, is het mogelijk niet mogelijk om de problemen meteen te vinden of kan het enige tijd duren voordat de problemen zijn opgelost. Wanneer een integratie niet goed werkt, u een ondersteuningsaanvraag openen om deze zo snel mogelijk te laten repareren. 

Als **u contact hebt met de leverancier van deze toepassing,** stuurt u deze bovendien op onze **manier** zodat we met hen kunnen samenwerken om hun toepassing native te integreren met Azure Active Directory. U de leverancier naar de vermelding van uw toepassing sturen [in de azure Active Directory-toepassingsgalerie](../azuread-dev/howto-app-gallery-listing.md) om deze aan de slag te krijgen.

## <a name="how-to-install-the-access-panel-browser-extension"></a>De extensie Access Panel Browser installeren

Voer de onderstaande stappen uit om de extensie Access Panel Browser te installeren:

1.  Open het [Access-paneel](https://myapps.microsoft.com) in een van de ondersteunde browsers en meld u aan als **gebruiker** in uw Azure AD.

2.  klik op een **Wachtwoord-SSO-toepassing** in het Access-paneel.

3.  Selecteer **Nu installeren**in de prompt waarin u vraagt de software te installeren .

4.  Op basis van uw browser wordt u doorverwezen naar de downloadlink. **Voeg** de extensie toe aan uw browser.

5.  Als uw browser daarom vraagt, selecteert u de extensie **inschakelen** of **toestaan.**

6.  Zodra u deze **browserhebt geïnstalleerd, start u** de browsersessie opnieuw.

7.  Meld u aan bij het Toegangspaneel en kijk of u uw wachtwoord-SSO-toepassingen **starten**

U ook downloaden van de extensie voor Chrome en Firefox van de directe links hieronder:

-   [Chrome Access Panel Extension](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel Extension](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Volgende stappen
[Eén aanmelding bij uw apps bieden met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

